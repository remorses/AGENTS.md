# fly

fly is a deployment platform. some packages use it to deploy the website. you can find the deployment scripts searching for **/deployment.ts

usually there are 2 fly apps for each package, one staging environment and one production. these are 2 different apps at 2 different urls, you can target the right app usually by using `pnpm fly:preview` or `pnpm fly:prod`. sometimes there is only `pnpm fly` and you can use that instead. These scripts will append the right --app argument to work on the right fly app.

Never deploy with fly yourself. ask the user to do it

## reading logs

you can read fly apps logs using `pnpm fly:preview logs --no-tail | tail -n 100`

if content is too long write pipe to a .log file and grep that file for what you are searching (like Error for searching for failures).
