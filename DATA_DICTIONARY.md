# Data dictionary

## NPZ

signals: float64, shape (2880, 512), normalized amplitudes; row number is array_row.
train, validation, test, reserved: ordered one-dimensional integer arrays of
zero-based signals row indices. They retain the order stored in the experiment.

## Waveform CSV files

sample_id: stable textual identifier SYN followed by the six-digit zero-based
original signals row number. Treat it as text.
sample_000 ... sample_511: normalized dimensionless amplitudes; time for
sample_NNN is NNN/500 seconds.

## metadata/samples.csv

| Field | Meaning |
| --- | --- |
| sample_id | Stable textual waveform identifier |
| array_row | Zero-based row in the NPZ signals matrix |
| split | train, validation, test, or reserved |
| split_position | Zero-based position in the corresponding ordered split array and CSV |
| source_group_id | Public group label SG001...SG240 |
| source_file | Original SZ_ simulation workbook basename; no local directory path |
| source_column_index | Zero-based waveform column in that source workbook |
| reference_onset_index | Zero-based, inclusive reference onset sample |
| reference_termination_index | Zero-based, inclusive reference termination sample |
| reference_onset_s | reference_onset_index / 500 |
| reference_termination_s | reference_termination_index / 500 |
| reference_duration_s | (termination_index - onset_index + 1) / 500 |
| waveform_sha256_float64_le | SHA-256 of 512 float64 amplitudes encoded as little-endian contiguous bytes |
| deduplication_fingerprint | SHA-256 after polarity canonicalization and rounding to seven decimal places, encoded as little-endian float64 |

Polarity canonicalization multiplies the normalized waveform by the sign of
its first maximum-absolute-amplitude sample. The deduplication fingerprint
describes the archived preprocessing rule, not the CSV file's hash.

## Other metadata

split_order.csv: split, split_position, sample_id, array_row; rows are grouped
by split and retain the stored within-split order.

time_axis.csv: zero-based sample_index and time_s, common to all waveforms.

split_summary.csv: waveform count, source-group count, and whether the split
was used in the experiment. Reserved source groups also occur in the test split.

source_groups.csv: public group label, main experiment partition, number of
source files, total/used/reserved waveforms, and original group label.

dataset_description.json: scope, acquisition grid, normalization, split policy,
reference-window rule, licensing, and limitations.

validation_report.json: counts and integrity checks performed on this release;
it reports data integrity, not denoising performance.
