# scantag - Scan to Tagged OCRed PDFs

This is a wrapper script around [`scanimage`][scanimage], [`noteshrunk`][noteshrunk], [`ocrmypdf`][ocrmypdf] and [`tmsu`][TMSU].

It is designed for use with an Automatic Document Feeder (ADF).
It scans a whole stack of pages with `scanimage`, optimizes, shrinks and converts them to PDF with `noteshrunk`, does a character recognition with `ocrmypdf` and tags the resulting file with `tsmu`.

The intermediate images and pdfs are all stored in a temporary folder in the current working directory (so you can choose between disk and RAM [e.g. by running from `/tmp`]).


## Requirements

- [scanimage][scanimage] (`sane-backends`)
- [noteshrunk][noteshrunk]
- [OCRmyPDF][ocrmypdf]
- [TMSU][TMSU]

## Configuration

Set `SCANNER` and `OCR_LANG` in the config section of the script.

```
#################### CONFIG ####################

# See `scanimage --list-devices`
SCANNER = 'fujitsu:fi-6110dj:1206658'

# See `tesseract --list-langs`
OCR_LANG = 'deu+eng'

################################################
```

### Auto-Completion

For [argument completion](https://pypi.org/project/argcomplete/) use (BASH)
```bash
eval "$(register-python-argcomplete scan)"
```

or e.g.

```fish
register-python-argcomplete --shell fish scan | source
```

for other shells (see [here](https://github.com/kislyuk/argcomplete/tree/develop/contrib)).

## Usage

```
usage: scantag [-h] [-m {single,duplex}] [-c {lineart,gray,color}] [-r {50..600}]
            [-t TAGS] [-s {a4,a5}] [--text] [-k] [--nsparams NSPARAMS] [--skip-ocr] [-v] [-y]
            [output_file]

Scan a document and save it as an optimized, OCRed and tagged PDF.

positional arguments:
  output_file           Output File Name for the Scanned Document (default: output.pdf)

options:
  -h, --help            show this help message and exit
  -m {single,duplex}, --mode {single,duplex}
                        Single sided / duplex scanning. The side facing away from the user is the <single> page / the first page of the <duplex> mode. (default: duplex)
  -c {lineart,gray,color}, --color {lineart,gray,color}
                        Color Mode: Lineart / Gray / Color (default: color)
  -p {text}, --preset {text}
                        Presets: "text": A preset for text that results in black-and-white scans - corresponds to '-c lineart -d 400 --nsparams=""' (default: )
  -r {50..600}, --resolution {50..600}
                        Scanning Resolution (default: 300)
  -t TAGS, --tags TAGS  Comma-separated list of tags to be used with tmsu. E.g. "tag1, tag2, tag3" (default: None)
  -s {a4,a5}, --paper-size {a4,a5}
                        Paper Size for Scanning (default: a4)
  --text                Short for --preset="text" - A preset for text that results in black-and-white scans - corresponds to '-c lineart -d 400 --nsparams ""' (default:
                        False)
  --scan                Just scan, skip the rest. (default: False)
  -k, --keep_intermediate
                        Do not delete intermediate scans afterwards. (default: False)
  --nsparams NSPARAMS   Arguments to be used for noteshrunk (see noteshrunk -h). (default: -w -s --unsharp_mask)
  --skip-ocr            Skip OCR on the scanned document. (default: False)
  -v, --verbose         Increase verbosity. Use twice to activate flushing of subprocess stdout to terminal. (default: 0)
  -y, --overwrite       Answer all questions with Yes. Overwrite existing files / Create TMSU database without asking. (default: False)
  --version             Show program version and exit
```

## Example

```fish
> scantag -y --text -m single -t 'invoice, car, YEAR=2024' cinquecento.pdf
> tmsu tags
YEAR  car  invoice
> tmsu files 'YEAR > 2020'
./cinquecento.pdf
```

[scanimage]: http://www.sane-project.org/man/scanimage.1.html
[noteshrunk]: https://github.com/suuuehgi/noteshrunk
[ocrmypdf]: https://github.com/ocrmypdf/OCRmyPDF
[TMSU]: https://github.com/oniony/TMSU
