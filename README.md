# <img src="https://rpnutzemutbrumczwvue.supabase.co/storage/v1/object/public/assets/pipeline_resized.png" alt="Pipeline Illustration" style="width:96px; height:96px; vertical-align:middle;"> The Pipe

The pipe is a tool for feeding complex real-world data into large language models. The pipe is built on top of dozens of carefully-crafted heuristics to create sensible representations from a wide variety of sources, including code projects, scientific papers, web pages, github repos, data files, databases, and more.

## 🛠️ How it works 

The pipe is accessible from the command line or from python. The input source is either a file path, a URL, or a directory path. The pipe will then extract information from the source and process it for downstream use with LLMs. The output is an opinionated, sensible text-based representation of the retrieved data (along with image data if any), carefully crafted to work well with LLMs such as GPT or Claude. 

## 📂 Supported input sources

- 📁 **Project directories** (any directory)
- 🗂️ **Zip / Tarballs** (`.zip`, `.tar`, `.gz`)
- 📜 **Code files** (`.py`, `.cpp`, `.ts`, `.css`, `.h`, etc.)
- 📚 **PDFs** (`.pdf`) (`.pdf` or any input containing `arxiv`, extract images/tables/math with `--mathpix`)
- 📓 **IPython notebooks** (`.ipynb`)
- 📝 **Word documents** (`.docx`, `.doc`)
- 🖼️ **Images** (`.png`, `.jpg`, `.jpeg`, `.gif`, defaults to base64, alternative `--text`)
- 🔗 **URLs** (any input containing `http` or `www`, or `.url` shortcut file)
- 🐙 **GitHub Repositories** (any input containing `github.com`)
- 📈 **Spreadsheets / Data** (`.csv`, `.xlsx`)
- 🗃️ **Business Database** (any input containing `supabase`)
- 📃 **Plaintext files** (`.txt`, `.md`, `.json`, `.yaml`, `.xaml`, `.ini`)
- 📊 **Powerpoint presentations** (`.pptx`)

## 🚀 Getting Started

To use The Pipe, simply clone this repository and run

```bash
python thepipe.py --source /path/to/directory --output prompt.txt
```

This command will process all supported files within the specified directory, compressing the information over the token limit if necessary, and outputting the result to `output.txt`.

Arguments are:
- `--source` (required): The input source, can be a file path, a URL, or a directory path.
- `--output` (required): The output file path.
- `--limit` (optional): The token limit for the output, defaults to 64K.
- `--mathpix` (optional): Extract images, tables, and math from PDFs using Mathpix.
- `--text` (optional): Output text scraped from images instead of base64 encoded images.

To use The Pipe from Python:

```python
import openai
import thepipe
openai_client = openai.OpenAI()
response = openai_client.chat.completions.create(
    model="gpt-4-vision-preview",
    messages = thepipe.extract("https://github.com/emcf/engshell"),
)
response_content = response.choices[0].message.content
print(response_content)
```

## Heuristics

To optimize the output for downstream tasks, the pipe makes a variety of assumptions, and uses many heuristics to extract the most important information from the input data. Here are some of the most important ones:
- **Optional Mathpix PDF extraction**: Optional, extracts images, tables, and math from PDFs.
- **Ctags token compression**: When the output prompt is too large, automatically extracts essential code structure (functions, classes, variables, types) and throws away the rest.
- **LLMLingua token compression**: When the output prompt is too large, automatically extracts essential tokens, can improve downstream performance by removing noise.
- **Image resizing, base64 encoding**: Maximum image dimensions are clipped to 512 pixels and encoded in base64 for easy downstream use with vision language models. Can alternatively output a text description of all images with `--text`, or text scraped from all images with `--scrape`.
- **Ignore Rules**: Sensible out-of-the-box ignore rules for common directories and files that are not useful for downstream tasks, such as `node_modules`, `__pycache__`, `.gitignore`, etc. Feel free to customize these for your own use case by modifying `FILES_TO_IGNORE` in `config.py`.

## License 📜

Distributed under the MIT License. See `LICENSE` for more information.

---

Made with ❤️ and Python.