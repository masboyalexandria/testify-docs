# internal/difflib

Package difflib is a partial port of Python difflib module.

It provides tools to compare sequences of strings and generate textual diffs.

The following class and functions have been ported:

\- SequenceMatcher

\- unified\_diff

Getting unified diffs was the main goal of the port. Keep in mind this code is mostly suitable to output text differences in a human friendly way, there are no guarantees generated diffs are consumable by patch(1).

This package was adopted from [github.com/pmezard/go-difflib](https://github.com/pmezard/go-difflib) which is no longer maintained.

## GetUnifiedDiffString

```go
func GetUnifiedDiffString(diff UnifiedDiff) (string, error)
```

Like WriteUnifiedDiff but returns the diff a string.

## NewMatcher

```go
func NewMatcher(a, b []string) *SequenceMatcher
```

## SplitLines

```go
func SplitLines(s string) []string
```

Split a string on "\\n" while preserving them. The output can be used as input for UnifiedDiff and ContextDiff structures.

## WriteUnifiedDiff

```go
func WriteUnifiedDiff(writer io.Writer, diff UnifiedDiff) error
```

Compare two sequences of lines; generate the delta as a unified diff.

Unified diffs are a compact way of showing line changes and a few lines of context.  The number of context lines is set by 'n' which defaults to three.

By default, the diff control lines (those with ---, +++, or @@) are created with a trailing newline.  This is helpful so that inputs created from file.readlines() result in diffs that are suitable for file.writelines() since both the inputs and outputs have trailing newlines.

For inputs that do not have trailing newlines, set the lineterm argument to "" so that the output will be uniformly newline free.

The unidiff format normally has a header for filenames and modification times.  Any or all of these may be specified using strings for 'fromfile', 'tofile', 'fromfiledate', and 'tofiledate'. The modification times are normally expressed in the ISO 8601 format.

## Match

```go
type Match struct {
	A    int
	B    int
	Size int
}
```

- `A: int`: 
- `B: int`: 
- `Size: int`: 

## OpCode

```go
type OpCode struct {
	Tag byte
	I1  int
	I2  int
	J1  int
	J2  int
}
```

- `Tag: byte`: 
- `I1: int`: 
- `I2: int`: 
- `J1: int`: 
- `J2: int`: 

## SequenceMatcher

```go
type SequenceMatcher struct {
	IsJunk func(string) bool
	// contains filtered or unexported fields
}
```

SequenceMatcher compares sequence of strings. The basic algorithm predates, and is a little fancier than, an algorithm published in the late 1980's by Ratcliff and Obershelp under the hyperbolic name "gestalt pattern matching".  The basic idea is to find the longest contiguous matching subsequence that contains no "junk" elements (R-O doesn't address junk).  The same idea is then applied recursively to the pieces of the sequences to the left and to the right of the matching subsequence.  This does not yield minimal edit sequences, but does tend to yield matches that "look right" to people.

SequenceMatcher tries to compute a "human-friendly diff" between two sequences.  Unlike e.g. UNIX(tm) diff, the fundamental notion is the longest \*contiguous\* & junk-free matching subsequence.  That's what catches peoples' eyes.  The Windows(tm) windiff has another interesting notion, pairing up elements that appear uniquely in each sequence. That, and the method here, appear to yield more intuitive difference reports than does diff.  This method appears to be the least vulnerable to synching up on blocks of "junk lines", though (like blank lines in ordinary text files, or maybe "\<P>" lines in HTML files).  That may be because this is the only method of the 3 that has a \*concept\* of "junk" \<wink>.

Timing:  Basic R-O is cubic time worst case and quadratic time expected case.  SequenceMatcher is quadratic time for the worst case and has expected-case behavior dependent in a complicated way on how many elements the sequences have in common; best case time is linear.

- `IsJunk: func(string) bool`: 

## GetGroupedOpCodes

```go
func (m *SequenceMatcher) GetGroupedOpCodes(n int) [][]OpCode
```

Isolate change clusters by eliminating ranges with no changes.

Return a generator of groups with up to n lines of context. Each group is in the same format as returned by GetOpCodes().

## GetMatchingBlocks

```go
func (m *SequenceMatcher) GetMatchingBlocks() []Match
```

Return list of triples describing matching subsequences.

Each triple is of the form (i, j, n), and means that a\[i:i+n] == b\[j:j+n].  The triples are monotonically increasing in i and in j. It's also guaranteed that if (i, j, n) and (i', j', n') are adjacent triples in the list, and the second is not the last triple in the list, then i+n != i' or j+n != j'. IOW, adjacent triples never describe adjacent equal blocks.

The last triple is a dummy, (len(a), len(b), 0), and is the only triple with n==0.

## GetOpCodes

```go
func (m *SequenceMatcher) GetOpCodes() []OpCode
```

Return list of 5-tuples describing how to turn a into b.

Each tuple is of the form (tag, i1, i2, j1, j2).  The first tuple has i1 == j1 == 0, and remaining tuples have i1 == the i2 from the tuple preceding it, and likewise for j1 == the previous j2.

The tags are characters, with these meanings:

'r' (replace):  a\[i1:i2] should be replaced by b\[j1:j2]

'd' (delete):   a\[i1:i2] should be deleted, j1==j2 in this case.

'i' (insert):   b\[j1:j2] should be inserted at a\[i1:i1], i1==i2 in this case.

'e' (equal):    a\[i1:i2] == b\[j1:j2]

## SetSeq1

```go
func (m *SequenceMatcher) SetSeq1(a []string)
```

Set the first sequence to be compared. The second sequence to be compared is not changed.

SequenceMatcher computes and caches detailed information about the second sequence, so if you want to compare one sequence S against many sequences, use .SetSeq2(s) once and call .SetSeq1(x) repeatedly for each of the other sequences.

See also SetSeqs() and SetSeq2().

## SetSeq2

```go
func (m *SequenceMatcher) SetSeq2(b []string)
```

Set the second sequence to be compared. The first sequence to be compared is not changed.

## SetSeqs

```go
func (m *SequenceMatcher) SetSeqs(a, b []string)
```

Set two sequences to be compared.

## UnifiedDiff

```go
type UnifiedDiff struct {
	A        []string // First sequence lines
	FromFile string   // First file name
	FromDate string   // First file time
	B        []string // Second sequence lines
	ToFile   string   // Second file name
	ToDate   string   // Second file time
	Eol      string   // Headers end of line, defaults to LF
	Context  int      // Number of context lines
}
```

Unified diff parameters

- `A: []string`: First sequence lines
- `FromFile: string`: First file name
- `FromDate: string`: First file time
- `B: []string`: Second sequence lines
- `ToFile: string`: Second file name
- `ToDate: string`: Second file time
- `Eol: string`: Headers end of line, defaults to LF
- `Context: int`: Number of context lines
