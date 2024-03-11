# files

one-liners (mostly) to manage files

## merge files and add filename to text

```bash
find . -maxdepth 1 -type f -name "*en.srt" -exec sh -c 'echo "=== {} ==="; cat "{}"' \; > combined_files.txt

```

## find and delete
```bash
find . -type f -path '*4.3*/*' -name '*.yml' -exec rm {} -i \;
```
(-i interactive) 

## find files (.pl.) and count words
```bash
find ./ -type f -name "*.pl.*" -exec wc -w {} +
```


## find the newest file recursively


```bash
find $1 -type f -exec stat --format '%Y :%y %n' "{}" \; | sort -nr | cut -d: -f2- | head
```

(supports filenames with spaces).
Performance improved with `xargs`:

```bash
find $1 -type f -print0 | xargs -0 stat --format '%Y :%y %n' | sort -nr | cut -d: -f2- | head
```


## find image files in directory

```bash
find . -type f -exec file --mime-type {} + | grep "image/"
```
or simply by extension (-iname for case insensitive seach)
```bash
find . -type f -iname "*.{png,gif,jpg,jpeg}"
```

windows gitbash version:
```bash
find . -type f | grep -iE '\.png$|\.gif$|\.jpg$|\.jpeg$'
```

or just all contents of images/ dirs
```bash
find . -type d -name "images" -exec find {} -type f \;
```



## find images in html files:

```bash
grep -r . -e '<img[^>]*src="[^"]*"' --include=*.html > images_in_htmls.txt
```

find images in markdown files:
```bash
grep -r . -e '\!\[' --include=*.md > images_in_mds.txt
```

## count files in subdirectories

mindepth and maxdepth to only check immediate descendants. add -name e.g "*.md" etc to only count certain filetype 

```bash
find . -mindepth 1 -maxdepth 1 -type d -exec sh -c 'echo -n "{}: "; find "{}" -type f | wc -l' \;
```

## batch rename files

```bash
$ for file in *; do
    extension="${file##*.}"
    filename="${file%.*}"
    new_filename="${filename}_pl.${extension}"
    mv "$file" "$new_filename"
done
```