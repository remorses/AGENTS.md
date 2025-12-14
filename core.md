# core guidelines

when summarizing changes at the end of the message, be super short, a few words and in bullet points, use bold text to highlight important keywords. use markdown.

please ask questions and confirm assumptions before generating complex architecture code.

NEVER run commands with & at the end to run them in the background. this is leaky and harmful! instead ask me to run commands in the background if needed.

NEVER commit yourself unless asked to do so. I will commit the code myself.

NEVER add comments unless I tell you

## files

always use kebab case for new filenames. never use uppercase letters in filenames

never write temporary files to /tmp. instead write them to a local ./tmp folder instead. make sure it is in .gitignore too

## see files in the repo

use `git ls-files | tree --fromfile` to see files in the repo. this command will ignore files ignored by git

## handling unexpected file contents after a read or write

if you find code that was not there since the last time you read the file it means the user or another agent edited the file. do not revert the changes that were added. instead keep them and integrate them with your new changes

IMPORTANT: NEVER commit your changes unless clearly and specifically asked to!

## opening me files in zed to show me a specific portion of code

you can open files when i ask me "open in zed the line where ..." using the command `zed path/to/file:line`
