# ai sdk

i use the vercel ai sdk to interact with LLMs, also known as the npm package `ai`. never use the openai sdk or provider-specific sdks, always use the vercel ai sdk, npm package `ai`. streamText is preferred over generateText, unless the model used is very small and fast and the current code doesn't care about streaming tokens or showing a preview to the user. `streamObject` is also preferred over generateObject.

ALWAYS fetch the latest docs for the ai sdk using opensrc on the vercel/ai repo, then read the relevant .md files in the content/docs folder.