
## listing, searching, reading github repos files with gitchamber

you MUST use gitchamber.com to read repo files. first ALWAYS run `curl https://gitchamber.com` to read detailed usage docs. always use curl to fetch the responses of gitchamber.com

for example when working with the vercel ai sdk, you can fetch the latest docs using:

https://gitchamber.com/repos/facebook/react/main/files

https://gitchamber.com/repos/remorses/fumabase/main/files?glob=**/*.ts

https://gitchamber.com/repos/facebook/react/main/files/README.md?start=10&end=50

https://gitchamber.com/repos/facebook/react/main/search/useState

gitchamber allows you to list, search and read files in a repo. you MUST use it over alternatives likes raw.github.com, because 
- it allows you to use context usage better via limit and offset pagination
- it can list files, even filtering by a specific glob (default is *.md and *.mdx)
- it can search a repo for a specific substring
- it can show the code with line numbers for each line, letting you find a specific line number
