# tailwind v4

this project uses tailwind v4. this new tailwind version does not use tailwind.config.js. instead it does all configuration in css files.

read https://tailwindcss.com/docs/upgrade-guide to understand the updates landed in tailwind v4 if you do not have tailwind v4 in your training context. ignore the parts that talk about running the upgrade cli. this project already uses tailwind v4 so no need to upgrade anything.

## spacing should use multiples of 4

for margin, padding, gaps, widths and heights it is preferable to use multiples of 4 of the tailwind spacing scale. for example p-4 or gap-4

4 is equal to 16px which is the default font size of the page. this way every spacing is a multiple of the height and width of a default letter.

user interfaces are mostly text so using the letter width and height as a base unit makes it easier to reason about the layout and sizes.

use grow instead of flex-1.
