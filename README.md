# Synthetic Mine Microseismic Waveform Dataset

Processed synthetic reference waveforms associated with the manuscript
**The wavelet transform-based CAMS-UNet denoising method for microseismic signals**.

Version 1.0.0; prepared 2026-10-18.

## Dataset contents

The release contains **2,880 clean synthetic waveforms**, each with 512 samples
at 500 Hz. Of these, **2,877 were used in the recorded experiment**:

| Split | Waveforms | Source groups | Experimental use |
| --- | ---: | ---: | --- |
| Training | 2,028 | 169 | Used for training |
| Validation | 288 | 24 | Used for validation |
| Test | 561 | 47 | Used for testing |
| Reserved | 3 | 3 | Not used |

There are 240 source groups in total. The three reserved groups also occur in
the test split, so source-group counts across all four rows must not be added
together. Reserved waveforms must not be moved into training or validation.

## Provenance and preprocessing

The source is the project's SZ_ numerical forward-simulation waveform archive.
Only simulation records were retained. Each trace was truncated to its first
512 samples, demeaned, and normalized by its maximum absolute amplitude.
Polarity-invariant normalized waveforms rounded to seven decimal places were
deduplicated before source-group splitting, with split seed 20260929.
Files connected through duplicate traces were kept in the same source group.

The original processed values, full-array row identities, split membership,
and within-split order are preserved. The NPZ file is a byte-for-byte copy of
the archived clean-signal split. It stores float64 values; the recorded neural
network experiment cast these values to float32 during computation.

Amplitudes are dimensionless and are not voltage, displacement, or another
calibrated physical quantity. The nominal record duration is 512/500 = 1.024 s;
with time starting at zero, the final sample is at 511/500 = 1.022 s.

## Files and reading conventions

- data/synthetic_microseismic_500Hz_512.npz contains the signals matrix and four
  ordered split-index arrays.
- data/train_waveforms.csv, validation_waveforms.csv, test_waveforms.csv, and
  reserved_waveforms.csv provide the same values in a text format.
- metadata/samples.csv maps each sample identifier to its source, split,
  original array row, and reference window.
- metadata/split_order.csv gives the exact experimental ordering within each split.
- metadata/source_groups.csv documents the source-group allocation.
- metadata/time_axis.csv supplies the shared 512-point time axis.
- metadata/dataset_description.json and DATA_DICTIONARY.md describe all fields.
- metadata/validation_report.json records package integrity checks.
- checksums.sha256 supplies file hashes.

For NumPy users, the archive has numeric keys signals, train, validation, test,
and reserved and does not require pickle. The signals matrix has shape
(2880, 512), with one waveform per row. The split arrays contain zero-based row
indices, **not sample identifiers**. Preserve their stored order when aligning
with experimental outputs.

For CSV users, the first column is sample_id. The next 512 columns,
sample_000 through sample_511, are amplitudes in chronological order. These
CSV files follow the original within-split order, which is not ascending array
row order. Numeric values are written with 17 significant digits to preserve
float64 values when read with a round-trip-capable numeric parser.

## Reference time windows

Annotations were generated automatically from the clean waveform using a fixed
smoothed absolute-amplitude envelope rule; they are not manual event labels.
The envelope uses a nine-point moving average and a threshold of 4% of its peak.
Active runs shorter than three samples are removed, and gaps of at most 75
samples are merged. The interval containing the envelope peak is selected;
if none contains it, the interval with the greatest envelope sum is used.
Eight samples are added before the selected interval and twelve after it,
with boundaries clipped to the record.

All boundary indices are zero-based and inclusive. Boundary time is index/500.
Window duration is (termination_index - onset_index + 1)/500.
Annotations are unchanged across methods and noise levels in the experiment.

## Scope and limitations

This release contains synthetic clean/reference signals only. It contains no
research source code, model weights, field recordings, recorded background
noise, noisy mixtures, or benchmark outputs. It therefore does not, by itself,
recreate the paper's noisy test inputs or full denoising comparison.

The split is a source-group holdout within the existing project corpus.
This corpus was used during earlier exploratory development. Neither a trace
nor a source group should automatically be treated as an independent seismic
event, and this split is not external cross-mine validation.

## License and attribution

The data and documentation in this repository are licensed under
[Creative Commons Attribution 4.0 International](https://creativecommons.org/licenses/by/4.0/).
See LICENSE.md.

For attribution, cite the dataset title, version, repository or release URL,
and creators identified in the repository's release metadata. Identify any
changes made to the data. Cite the associated manuscript when its publication
details become available. No article DOI or repository DOI is assigned by this
local preparation.
