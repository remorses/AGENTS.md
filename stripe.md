# stripe

stripe is used to manage subscriptions and payments.

the Stripe billing portal is used to

- send user to payment to create a sub via `stripe.checkout.sessions.create`
- let user change plan, cancel or change payment method ("manage subscription") via `stripe.billingPortal.sessions.create`

## customerId

every time you are about to do a call to `checkout.sessions.create` make sure that we create the Stripe customer first. So that we do not get duplicate Stripe customers for different subscriptions:

```ts

let customerId = org.stripeCustomerId

if (!customerId) {
    const customer = await stripe.customers.create({
        email: org.email || undefined,
        name: org.name || undefined,
    })

    customerId = customer.id

    await prisma.org.update({
        where: { id: orgId },
        data: { stripeCustomerId: customerId },
    })
}

 await stripe.checkout.sessions.create({ customer: customerId, ... })
```

## subscriptions

a subscription is active if state is in

- trialing
- active

```ts
await prisma.subscription.findFirst({
  where: {
    orgId: orgId,
    status: {
      in: ["active", "trialing"],
    },
    // ...
  },
});
```

a subscription can be reactivated if state is NOT in

- canceled
- incomplete_expired
- unpaid

> If sub is in any of these states the user will not be able to use the billing portal to reactivate it. Meaning we should treat a subscription in these states as completely missing. Forcing the user to create a new one instead of shoging the "manage subscription" button that redirects the user to the billing portal. BUT customer id must be preserved, reusing previous sub customerId in `stripe.billingPortal.sessions.create({ customer: prevCustomerId })`
