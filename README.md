# CSV delimiter converter based on macOS Automator
*Easily convert CSV delimiters from comma to semicolon and vice versa with macOS
Automator*

As the name suggests,
[CSV](https://en.wikipedia.org/wiki/Comma-separated_values#Standardization) (or
comma-separated-value) files contain a *comma* `,`  to separate the different
values. However, many European countries use the comma as a decimal separator
and therefore by default revert to a semicolon `;` to separate values in a CSV
(or rather, a semicolon-separated-file) file.

This can lead to frustration because a, say, German-language Microsoft Excel
installation will *save* CSVs with a semicolon and is at the same time unable to
properly *view* an (actual) CSV file. Unfortunately, there is also [no
straightforward way](https://superuser.com/a/988762) to set the desired
delimiter inside the app itself without changing system settings which might
mess with other parts of your system.

There is a work-around, but you will have to go through the ordeal of opening
the files, then selecting the first column, hitting "Text to Columns", selecting
"Delimiter" and then "Comma" and so on before being able to FINALLY view them
correctly...

This handy and privacy-friendly tool based on [macOS
Automator](https://support.apple.com/guide/automator/welcome/mac) is here to
help! In the blink of an eye, you can convert any proper CSV (you know,
COMMA-separated) file to a semicolon-separated file which will work like a charm
in a :de: Excel installation. If you want to revert this operation - or are
maybe faced with the exact opposite problem - there's also a opposite Action.

## Demo
![](delim-converter-demo.gif)

## Prerequisites
Besides a Mac with Automator, you will need a running `gnu-sed` (or `gsed`)
installation as the Action relies on pure `sed`-based text replacement. If you
do not have `gsed` installed, you can get it via
[Homebrew](https://formulae.brew.sh/formula/gnu-sed) with `brew install
gnu-sed`.

## Installation
In this GitHub repository, click on the green "Code" button and select "Download
ZIP". Unzip the downloaded file and navigate to the respective folder. Then
double-click the `csv_comma_to_semicolon.workflow` and / or
`csv_semicolon_to_comma.workflow` file. This will install the Action in your
macOS Automator library. Alternatively, you can also copy the files to
`~/Library/Services` manually. The Action will be available in the "Quick
Actions" menu in the Finder or (depending on your view settings) below the file
information in the sidebar.

## Behind the scenes
The tool is built with a two-step macOS Automator Action which
1. takes the selected Finder objects (`$@`) and
2. runs the following shell script:

```bash
if [[ $(uname -m) == 'arm64' ]]; then
	# Apple Silicon
    GSED_PATH="/opt/homebrew/bin/gsed"
else
	# Intel
    GSED_PATH="/usr/local/bin/gsed"
fi

for f in "$@"
do
	"$GSED_PATH" -i 's=;=,=g' "$f"
done
```

As the location of the `gsed` binary installed via Homebrew can vary depending
on whether you are using an Intel or Apple Silicon Mac, the script checks for
the architecture and sets the path accordingly.

The script then simply replaces all occurrences of `;` with `,` in each selected file. Note
that this is done *in-place* (`-i`) but can be reverted with the other Action
which does the exact opposite.
