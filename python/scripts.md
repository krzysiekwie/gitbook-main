# Quick automation scripts

I'm working on a sizeable (around 1 mil words) collection of texts in a number of github repos. Dealing with them manually would be a chore so I wrote a couple automation scripts.


## Remove BOM
MemoQ has a habit of exporting utf-8 plaintext/markdown  with BOM, and this is not caught by our course building script and messes up the final html. 

<details>
<summary>Click to see code...</summary>

```python
import os


def has_bom(filepath):
    with open(filepath, "rb") as file:
        bom_check = file.read(3)
        return bom_check == b"\xEF\xBB\xBF"


def convert_files(directory):
    for root, dirs, files in os.walk(directory):
        for filename in files:
            filepath = os.path.join(root, filename)
            if has_bom(filepath):
                print(filename)
                drop_BOM(filepath)


def drop_bom(filepath):
    with open(filepath, "r", encoding="utf-8-sig") as file:
        content = file.read()
        print(f"saved {filepath} without BOM")
    with open(filepath, "w", encoding="utf-8") as file:
        file.write(content)


# directory containing the files to convert
directory = "../.."

convert_files(directory)
```

</details>