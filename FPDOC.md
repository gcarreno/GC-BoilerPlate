# FPDOC scripts

Script: `updateXML`

```bash
#!/bin/bash

MAKESKEL=~/FreePascal/fpc/bin/x86_64-linux/makeskel
FLAGS="--disable-private --disable-protected --update"

PACKAGE=PROJECT

XML=~/Programming/PROJECT/docs/xml
SRC=~/Programming/PROJECT/src
OUT=~/Programming/PROJECT

FILES=(
    "File1"
    "File2"
)

for file in "${FILES[@]}"; do
  echo "Doing: ${SRC}/${file}.pas"
  $MAKESKEL $FLAGS --package=${PACKAGE} \
                   --descr=${XML}/${file,,}.xml \
                   --input=${SRC}/${file}.pas \
                   --output=${OUT}/${file,,}.xml
done
```

Script `buildDocs`

```bash
#!/bin/bash

FPDOC=~/FreePascal/fpc/bin/x86_64-linux/fpdoc
PACKAGE=PROJECT

OUT=~/Programming/PROJECT/docs/html
XML=~/Programming/PROJECT/docs/xml
SRC=~/Programming/PROJECT/src

${FPDOC} \
    --package=${PACKAGE} \
    --output=${OUT} \
    --format=html \
    --descr=${XML}/file1.xml \
    --descr=${XML}/file2.xml \
    --input=${SRC}/File1.pas \
    --input=${SRC}/File2.pas
```
