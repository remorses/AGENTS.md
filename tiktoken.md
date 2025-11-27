## count tokens of a file

use uvx with tiktoken to count tokens:

```bash
uvx --from tiktoken python -c "import tiktoken; enc = tiktoken.get_encoding('cl100k_base'); print(len(enc.encode(open('path/to/file').read())))"
```
