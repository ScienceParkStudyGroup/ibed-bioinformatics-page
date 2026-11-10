# TODO

- [ ] [bowtie.qmd](source/core_tools/bowtie.qmd): a "Combining Bowtie2 with samtools" subsection now shows a piped `bowtie2 | samtools sort` one-liner that goes straight to a sorted BAM. Check whether this fully resolves the original ask (e.g. for MetaBAT2's `jgi_summarize_bam_contig_depths`), or whether the main/basic example further up the page should also be changed to the piped form by default.
- [x] [bowtie.qmd](source/core_tools/bowtie.qmd): add a note/section about BWA as an alternative read mapper. Done — [bwa.qmd](source/core_tools/bwa.qmd) created and cross-linked from bowtie.qmd, star.qmd, minimap2.qmd, samtools.qmd.
- [ ] [featurecounts.qmd](source/core_tools/featurecounts.qmd): currently listed as not installed on Crunchomics — install it there and update the page once verified.
- [ ] [bwa.qmd](source/core_tools/bwa.qmd): look into adding bwa-mem2 (faster reimplementation of bwa mem) as an alternative/addition to classic BWA. Note: the lh3/bwa repo itself lists [minibwa](https://github.com/lh3/minibwa) as the likely successor to bwa mem (per user, 2026-08-17) — check its maturity/status before deciding whether to document minibwa instead of or alongside bwa-mem2.
- [ ] For the Bioinfo toolbox, consider a "Sanity checks in sequencing workflows" page. The goal is to have on page that teaches students about looking at what do I expect from my data, how do I look at outputs, compare results and localize issues. Examples I could use from my course work: retention of reads after filtering, alignments versus reads, taxa versus experimental design, do I retain all samples in my workflow, ...
  

For-loops page:
It suggests $(ls) as a list source, which is a known anti-pattern.
-op $outdir is unquoted, right after the quoting advice.
"read by default recognizes tab" is incomplete. Default IFS splits on spaces, tabs, and newlines, so a sample name containing a space breaks. IFS=$'\t' read -r is the explicit form.
The curly-quotes sentence is logically inverted. It currently says straight quotes may not be recognized.

Bash Validation page:
~~It uses for ID in $(cat samples) throughout, which the for-loops page warns against.~~ Done 2026-11-10: kept the `for` loops on purpose (one-column list; `while read` skips a last line without newline and lets tools in the loop eat the list, tested in WSL). Added a note after the first loop that links the `while` loop and the DOS line-ending section.
~~The exit 1 example pasted into an interactive terminal closes the shell. On Crunchomics that logs students out of their SSH session. A warning is more useful than "press enter to restart."~~ Done 2026-11-09: the page now says to save the loop as a script and run it with `bash`.


Done 2026-11-10 (draft, not yet reviewed): pipefail "rightmost" fix, `set -e` hidden rules section, rule of thumb and pipe sentence on the validation page, new page [bash-skip-finished-samples.qmd](source/core_tools/bash-skip-finished-samples.qmd) ("Skip finished samples safely", uses `for` loops instead of `while read`), added to the navigation. Rendered 2026-11-10 (the three bash pages only; other pages get the sidebar link at the next full render). MEGAHIT tested on Crunchomics 2026-11-10 (v1.2.9): `done` file, `--continue` resume and its limits are now on the new page under "Tools that can resume a crashed run".

Done 2026-11-10 (review round with student eyes, rendered the three bash pages only):

- Skip page, point 5: the glob is not expanded in a variable or inside `[[ ]]`, so removing the quotes doesn't help; `[ -s $VAR ]` works for one match only ("binary operator expected" with two). Tested in WSL.
- Skip page: `--array=1-4` bullet (match the range to `wc -l samples`), one sentence that a SLURM job array is not a bash array.
- Pipefail page: "Hidden rules of `set -e`" now has two rules; the pipefail item is its own section "`pipefail` without `set -e`" (anchor `#pipefail-without-set--e`). Added that the failed-samples `if` block works without `set -e`.
- Skip page intro cut to two paragraphs plus a jump list; the "two related pages" list is a collapsed callout; the GenAI paragraph moved to the GenAI note at the end.
- Moved the "without `|| exit 1`" demo from "A marker file" (validation page) to a new section "Why the marker file works" on the skip page, rewritten for the template and its `data/` files, run in WSL. The loop example `marker_test.sh` stays on the validation page (the skip page links to it for the `continue` version). Links on the pipefail page updated.
- All `ls -l` outputs on the validation and skip pages replaced with Crunchomics output (2026-11-10). Sizes were the same as before. Point 4 (`-s` on a folder): empty folder is size 2 on Crunchomics, 4096 under WSL; added `ls -ld` vs `ls -l`.

- [ ] Optional: em dashes on [bash-pipefail.qmd](source/core_tools/bash-pipefail.qmd) (lines ~88, ~169-170) and in the summary of [bash-input-validation.qmd](source/core_tools/bash-input-validation.qmd).
- [ ] Review the new page [bash-skip-finished-samples.qmd](source/core_tools/bash-skip-finished-samples.qmd).
- [ ] Check on Crunchomics whether `hmmsearch` writes `--tblout` during the run (`ls -l` on the file while a job with several HMMs runs). If yes, name it as the example in "When a tool makes its own final file" and link the HMMER page.
- [ ] Optional: one real SLURM run of `clean_job.sh` from the new page, to replace the made-up job ID 123456 in "Which samples are not done yet?" and confirm that the `cat:` error lands in the `.out` file.
- [ ] Optional: [bash-input-validation.qmd](source/core_tools/bash-input-validation.qmd) "Looping across all samples": the real output has a leading space (` s1.cleaned.fastq exists...`) and some early examples print an empty first line that the page leaves out.

- [ ] Consider a MEGAHIT tool page. Tested on Crunchomics 2026-11-10 (v1.2.9), already on the skip page: writes an empty `done` file as last step; stops if the `-o` folder exists; `--continue` resumes a crashed run (same contigs as an uncrashed run), ignores all options except `-o`, and fails on an already finished run. From the source only (master branch, 2026-11-09, not tested): `-f` skips the folder check but keeps old files; a first run with `--continue` works like a normal run. A tool page could link to "Tools that can resume a crashed run" on the skip page.

Make a new page making it easier for students to write their own file checks. Prompt (replace `<user>` and `<student>` in the reference path with the real folder names when you paste it, and don't save the real names in this file, the repo is public):

```
# Task: improve the bash pages on the IBED bioinformatics website

## Context
- This repo builds the website https://scienceparkstudygroup.github.io/ibed-bioinformatics-page/ (Quarto).
- The readers are biology students. For many of them the command line is new, and many use GenAI tools to write scripts. Write in plain language and explain every technical term the first time it appears. Prefer explicit code that is easy to copy over short, clever code.
- The relevant pages are `source/core_tools/bash-input-validation.qmd` and `source/core_tools/bash-pipefail.qmd`. Read both pages in full, plus `bash-for-loops.qmd` and `bash-arrays.qmd`, before you propose changes. Match their style: short sections, a code block, then its output, and a "note on GenAI" where it fits.
- `bash-input-validation.qmd` was extended on 2026-11-09 and already covers part of this task. Don't repeat that content, link to it instead:
  - `exit 1` vs. no `exit`, and the exit code table ("Which exit code to use")
  - `-s` on `.gz` files and on cut-off files, tested with broken dummy files ("Test your check with broken files")
  - checking output after the job with `zcat | wc -l` and `gzip -t`
  - the marker pattern `|| exit 1` + `touch "${ID}.done"` + `[[ -f ]]`, with and without `|| exit 1` ("A marker file")
  - loop vs. job array, and why the check depends on the file type ("Before you copy a check")
- Division of work: `bash-input-validation.qmd` explains why checks fail and how to test them. The new page (step 3) is the short template to copy, plus the traps that page does not cover yet.

## Reference material (read-only, outside this repo)
I give you permission to READ these files in a script review I wrote for a student:
`C:\Users\<user>\OneDrive - UvA\Projects\03_students\<student>\2026-11-09-script_review\`
- `CLAUDE.md`: the patterns found, the agreed fixes and the verified tool facts. Start here.
- `comments_nd.qmd`: the review itself.
- `scripts/`: the student's scripts. Read them only if you need context.

Rules for that folder:
- Read only. Never edit, create, move or delete anything there.
- Do not read any other file there (for example a `mails.md`).
- The website is public. Never copy names, user names, email addresses, personal paths, folder names or script names from that folder into the website. Use generic examples instead (`sample1`, `results/`, `<user>`). Rewrite every example from scratch. Do not paste real code from the student.

## What to do
1. **Fix a factual error in `bash-pipefail.qmd`.** The page says that with pipefail the pipeline "returns the exit code of the first failing command". The bash manual says: "the last (rightmost) command to exit with a non-zero status, or zero if all commands exit successfully". (Checked in `man bash` on Ubuntu 24.04.)

2. **Small additions to the two existing pages.** Propose them, don't apply them yet:
   - `bash-input-validation.qmd`: a rule of thumb, `-f` for marker files and `-s` for data files, but `-s` only catches files of 0 bytes (link to "Test your check with broken files"). The page already shows that `-s` on an empty file made with `touch` is never true, so don't add that again.
   - `bash-input-validation.qmd`, section "A marker file": it says that only `|| exit 1` keeps a failed tool from reaching `touch`. This is not true for a pipe such as `tool | gzip > out || exit 1` without pipefail, because only `gzip` is checked. Add one sentence with a link to the pipefail page.
   - `bash-pipefail.qmd`: two hidden `set -e` rules. First: in an `&&` chain, only the last command stops the script. Second: code that should run after a failure (for example writing a "failed samples" list) is never reached under `set -e`. Also: `set -o pipefail` plus `|| exit 1` after a pipe stops the script without `set -e`.
   - The loops use `for ID in $(cat samples)` (8 times, including the new marker scripts). The review recommends `while read -r ID` and points out Windows line endings (`\r`) in CSV files. Point out the mismatch and suggest a fix. Don't rewrite the loops without asking. If you suggest `while read`, use the form `while read -r ID; do ... done < samples`. Never use `cat samples | while read -r ID`: there the loop runs in a subshell, so `exit 1` only leaves the loop and the script goes on. Run both forms in `_tests/` and show the difference. Also check whether a tool inside the loop that reads standard input can use up the sample list. If the loops change, rerun the examples and replace their outputs.

3. **Draft a new page: "Making a job script restartable"** (or a better title). Goal: one pattern that students copy into every job script, instead of writing a new variant per script. Content:
   - The agreed pattern (see "Agreed fixes" in the review's `CLAUDE.md`): `|| exit 1` after each tool command, then `touch "${ID}.done"` after the last data step, and a skip check with `[[ -f "$DONE" ]]`. Give it as a short template to copy. The explanation of why it works is already in "A marker file" on `bash-input-validation.qmd`, so link there.
   - After an array job: a `while read` loop over the sample list that prints samples without a `.done` file, then `wc -l`.
   - When a tool writes its own final file (or its own `done` file, like MEGAHIT), that file can be the check instead.
   - A short section "Checks that look right but aren't", with generic examples. The first two are already shown on `bash-input-validation.qmd`: give them one line each with a link, no new demo:
     - `-s` on a file that a tool writes during the run (a half-finished file passes)
     - `-s` on a `.gz` file (even an empty one has a few bytes)
     - checking only R1 of a read pair
     - `-s` on a folder
     - a glob inside a quoted check, `COMPLETE="${ID}_b0*.tsv"` with `[[ -s "$COMPLETE" ]]` (never true, the `*` stays literal)
     - a glob that matches nothing stays as literal text (so `cp bin.*.fa ...` with no matches fails or creates odd names)
     - `>>` that adds the same lines again on every rerun
     - `rm -rf "$DIR"` on a rerun (deletes finished work, and is dangerous if the variable is empty)
     - array tasks that all write the same file at the same time
     - deleting temporary files inside the job, before anyone has checked them
   - Close with a "note on GenAI": a script that runs is not the same as a script that gave correct results. Look at intermediate files after each step (`head`, `wc -l`, `grep` for one known entry).
   - Link it from the two existing pages and add it to the site navigation, next to the other bash pages.

## How to work
- Start with a numbered plan and wait for my approval.
- Run every code example before you show its output on the page. Put test files and scripts in `_tests/` in this repo (it exists and is in `.gitignore`). Paste only output you actually got. If you could not run an example, mark it as not run.
- Flag every claim you have not checked yourself. Examples: the exact size of an empty `.gz` file, or tool behaviour.
- Don't render the whole site or commit without asking. Never push.
```


Done 2026-11-10 (restructure after the assessment, rendered the three bash pages only):

- Pipefail page, "When it helps and when it causes problems": the recommendations now match the template. Use `set -o pipefail` + `|| exit 1`, no `set -e`. For GenAI scripts: replace `set -euo pipefail`, always together with `|| exit 1` after every tool command.
- Moved from the validation page to the skip page: "Test your check with broken files" (now "Why not just `-s`?"), "Skipping is not the same as checking the output" (now "Checking the outputs"), "A marker file" (now "In a loop instead of a job array", rewritten for the template's `data/` files, run in Git Bash in `_tests/skip_loop_test.sh` and `_tests/skip_loop_test2.sh`). Deleted "Before you copy a check" (its loop vs array text is in the new loop section). The validation page has a short section "Skipping samples that already finished" that links to the skip page.
- All `#anchor` links on the five bash pages checked against the rendered HTML: all resolve.
- [ ] Optional: run `clean_loop.sh` from the new loop section on Crunchomics (only run in Git Bash so far; output shows plain `ls`, no `ls -l`).
- [ ] Next steps from the assessment: merge the GenAI notes, split index.qmd into "read in order" and "reference", move the MEGAHIT resume section to a tool page, fix the for-loops page.

Done 2026-11-10 (step 3, GenAI notes, rendered the five bash pages only):

- One shared section "Checking GenAI-generated scripts" on the skip page, before the Summary: patterns to look for (with links) and how to check a script. Linked from the jump list at the top of the skip page.
- The for-loops, validation, arrays and pipefail pages keep a short note about their own topic plus a link to that section. Removed from the for-loops note: the advice to prefer `-s` over `-f` (went against the marker file).
- All anchor links checked against the rendered HTML: all resolve.
- [ ] for-loops page: Quarto warns "Div at line 3 column 1 unclosed". The `<div style="text-align: justify">` has no closing `</div>`. Was already like this before step 3. Fix with the for-loops step.

Done 2026-11-10 (step 4, index and order, rendered index.qmd only):

- index.qmd: "Writing bash scripts" split into "read the first three in this order" (for-loops, checking files, skip finished samples) and "read these when you need them" (pipefail, arrays). Descriptions updated.
- _quarto.yml sidebar in the same order: for-loops, input-validation, skip-finished-samples, pipefail, arrays.
- Page headings now match the index: "Checking files in bash scripts" (was "File verifications in bash"), "Bash arrays" (was "Arrays in bash"). Link texts on the skip page updated.
- [ ] Full site render (sidebar order and the new headings only show on the other pages after it).

Done 2026-11-10 (MEGAHIT page, rendered megahit.qmd, the skip page, index.qmd and order_a-z.qmd only):

- New page [megahit.qmd](source/metagenomics/megahit.qmd). Installed on Crunchomics without a conda env (v1.2.9). Citation li2015 checked against the journal page. `final.contigs.fa`, `log` and `done` confirmed from a real run listing. bioconda `megahit=1.2.9` checked.
- The `--continue` results moved from the skip page to the MEGAHIT page ("Restarting a crashed run"). The skip page keeps the four general checks and links to it.
- Added to _quarto.yml ((Meta)genomics, after medaka), index.qmd and order_a-z.qmd.
- [x] Removed by the maintainer 2026-11-10. order_a-z.qmd: no page or config links to it, and all its tool links are broken (they resolve to `source/source/...`, Quarto warns for every link). Was like this before. Decide: fix the links or remove the page (index.qmd has the same A-Z list).
- [ ] Not tested, left out of the MEGAHIT page: `-f` (skips the folder check but keeps old files, from the source only).

Done 2026-11-10 (repeated `.gz` warning, rendered the validation and skip pages only):

- Validation page: the `.gz` paragraph after `-s` cut to two sentences plus a link.
- Skip page: points 1 and 2 of "Checks that look right but aren't" merged, points renumbered 1-9, "see point 8" changed to "see point 7". The other `.gz` mentions are demo outputs or one-line links and stay.

Done 2026-11-10 (for-loops page, rendered bash-for-loops.qmd only):

- Fixed: `$(ls)` replaced by a glob; curly braces explanation (braces mark the end of the variable name, the double quotes protect from word splitting, tested `$GENOME_ID_R1` gives empty text); `read` splits at spaces and tabs (tested); `-op "$outdir"` quoted; curly quotes sentence; "Spaces" item (indentation is ignored by bash); "Missing or empty input files" item now says stop or skip with a clear message; typo "imput"; two em dashes; unclosed `<div>`; "Using Loops with Files" and "While loops" now `##`.
- DOS line endings: now shows how to check (`file`, `cat -A`) and fixes with `sed 's/\r$//'` into a new file, with the same checks as the AWK/sed tutorial, and links to it. The old `tr` loop that overwrote every `.txt` file is gone. Outputs from Git Bash (`_tests/forloops_sed_check.sh`).
- [x] Tested on a Mac with the default sed, 2026-11-10: `sed 's/\r$//'` works, `cat -A` does not, use `cat -e`. macOS version unknown. Sentence on `cat -e` added to the for-loops page. The AWK/sed tutorial (other repo) also uses `cat -A` and needs the same sentence.
