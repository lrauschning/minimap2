## Minimap2 Python Binding

[Minimap2][minimap2] is a fast and accurate pairwise aligner for genomic and
transcribed nucleotide sequences. This module wraps minimap2 and provides a
convenient interface to calling minimap2 in Python.

### Installation

The minimap2 model can be installed directly with:
```sh
git clone https://github.com/lh3/minimap2
cd minimap2
python setup.py install
```
or with [pip][pip]:
```sh
pip install --user minimap2
```

### Usage

The following Python program shows the key functionality of this module:
```python
import minimap2 as mm
a = mm.Aligner("test/MT-human.fa")
for hit in a.map("GGTTAAATACAGACCAAGAGCCTTCAAAGCCCTCAGTAAGTTGCAATACTTAATTTCTGT"):
	print("{}\t{}\t{}\t{}".format(hit.ctg, hit.r_st, hit.r_en, hit.cigar_str))
```
It builds an index from the specified sequence file (or loads an index if a
pre-built index is supplied), aligns a sequence against it, traverses each hit
and prints them out.

### APIs

#### Class minimap2.Aligner

```python
Aligner(fn_idx_in, preset=None, ...)
```
Arguments:

* `fn_idx_in`: index or sequence file name. Minimap2 automatically tests the
  file type. If a sequence file is provided, minimap2 builds an index. The
  sequence file can be optionally gzip'd.

* `preset`: minimap2 preset. Currently, minimap2 supports the following
  presets: `sr` for single-end short reads; `map-pb` for PacBio
  read-to-reference mapping; `map-ont` for Oxford Nanopore read mapping;
  `splice` for long-read spliced alignment; `asm5` for assembly-to-assembly
  alignment; `asm10` for full genome alignment of closely related species. Note
  that the Python module does not support all-vs-all read overlapping.

* `k`: k-mer length, no larger than 28

* `w`: minimizer window size, no larger than 255

* `min_cnt`: mininum number of minimizers on a chain

* `min_chain_score`: minimum chaing score

* `bw`: chaining and alignment band width

* `best_n`: max number of alignments to return

* `n_threads`: number of indexing threads; 3 by default

* `fn_idx_out`: name of file to which the index is written

```python
map(query_seq)
```
This methods maps `query_seq` against the index. It *yields* a generator,
generating a series of `Alignment` objects.

#### Class minimap2.Alignment

This class has the following properties:

* `ctg`: name of the reference sequence the query is mapped to

* `ctg_len`: total length of the reference sequence

* `r_st` and `r_en`: start and end positions on the reference

* `q_st` and `q_en`: start and end positions on the query

* `strand`: +1 if on the forward strand; -1 if on the reverse strand

* `mapq`: mapping quality

* `NM`: number of mismatches and gaps in the alignment

* `blen`: length of the alignment, including both alignment matches and gaps

* `trans_strand`: transcript strand. +1 if on the forward strand; -1 if on the
  reverse strand; 0 if unknown

* `is_primary`: if the alignment is primary (typically the best and the first
  to generate)

* `cigar_str`: CIGAR string

* `cigar`: CIGAR returned as an array of shape `(n_cigar,2)`. The two numbers
  give the length and the operator of each CIGAR operation.

An Alignment object can be converted to a string in the following format:
```
q_st  q_en  strand  ctg  ctg_len  r_st  r_en  blen-NM  blen  mapq  cg:Z:cigar_str
```



[minimap2]: https://github.com/lh3/minimap2
[pip]: https://pypi.python.org/pypi/pip