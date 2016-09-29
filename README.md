Welcome to the gremau.github.io wiki!

I am in the process of migrating my old wiki to here.

Steps for this are:

1. Use dokuwiki2git to export a git repository of the pages

2. Copy pages to new directory and change permissions of those pages

3. Convert dokuwiki to markdown using the pandoc mediawiki converter

    find . -name \*.txt -type f -exec pandoc -f mediawiki -t markdown -o {}.md {} \;

4. do some custom editing of the new markdown pages

    for file in *.txt.md; do git mv "$file" "${file//txt./}"; done

5. replace nonbreaking spaces in vim

    :%s/<NBSP>/ /g

where entering <NBSP> is accomplished with <CTRL-k>+space+space

Or - remove in all files with sed:

    find . -type f -exec sed -i 's/\xC2\xA0/ /g' {} +

6. Replace other stuff with sed:

    find . -type f -exec sed -i 's/ "wikilink"//g' {} +
    find . -type f -exec sed -i 's/<\/code>/~~~/g' {} +
    find . -type f -exec sed -i 's/<code bash>/~~~/g' {} + 
    find . -type f -exec sed -i 's/-   -   / **/g' {} +


The first page to be complete is [[start]]

[[pandoc]]
