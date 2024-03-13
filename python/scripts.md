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

## update yml (but like it's text)

Why update .yml files but read them as plaintext? Turns out that in my case they don't follow consistent rules when it comes to quoting. So instead of trying to figure out the reasons and/or try to enforce one style I decided to treat them like text files. This also works well because the changes I'm doing are minimal. Usually one line (or two) in the file contains some PL text. I need to add an extra line with the EN key and value based on the PL text.


<details>
<summary>Click to see code...</summary>

```python
import csv
import os


def get_glossary(csv_file):
    # Read the CSV file with the glossary
    csv_mappings = []
    with open(csv_file, encoding="utf-8") as csv_file:
        csv_reader = csv.reader(csv_file)
        for row in csv_reader:
            if len(row) == 2:
                old_name, new_name = row
                csv_mappings.append((old_name, new_name))
    terms_desc = sorted(csv_mappings, key=lambda x: len(x[0]), reverse=True)
    return terms_desc


def write_settings(ROOT_DIR, terms_desc):
    grading_criteria = set()
    translations_pl = set()
    for root, dirs, files in os.walk(ROOT_DIR):
        for filename in files:
            if filename == "settings.yml": # Find the files to edit
                file_path = os.path.join(root, filename)
                with open(file_path, encoding="utf-8") as f:
                    content = f.readlines()
                new_content = []
                for line in content:
                    if "pl:" in line: # only touch lines with the pl key
                        line_pl = line
                        for pl_term, en_term in terms_desc:
                            if pl_term in line_pl:
                                line_en = line_pl.replace("pl:", "en:")
                                line_en = line_en.replace(pl_term, en_term)
                                line = f"{line_pl.rstrip()}\n{line_en}"
                                break
                    new_content.append(line.rstrip())
                with open(file_path, "w", encoding="utf-8") as f:
                    # save the sa
                    f.write("\n".join(new_content))
                    f.write("\n")


GLOSSARY = "../path/to/glossary.file"
ROOT_DIR = "../path/to/search"
terms_desc = get_glossary(GLOSSARY)
write_settings(ROOT_DIR, terms_desc)

```
</details>