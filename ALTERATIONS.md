# Dusklight alteration to UAM 1.1.0

Upstream: https://github.com/devkitPro/uam.git
Base commit: `5a5afc2bae8b55409ab36ba45be63fcb73f68993`.
Original fincs/zlib and Mesa licenses remain in LICENSE and the source headers.
This is an altered version, not a claim that upstream provides this method.

Adds `DekoCompiler::OutputDkshToMemory(std::vector<uint8_t>&) const` for the
experimental Dawn runtime wrapper. It produces caller-owned bytes without
writing a temporary file, rejects an uncompiled object/oversized output, and
leaves caller bytes unchanged on failure. The original `OutputDksh` file writer
is unchanged. Actual host tests compare the complete byte streams, including
padding and embedded constants, and test graphics and compute serialization.
The real Dawn wrapper's VS/FS output also matches the original pinned CLI.

Validation is PARTIAL. The altered interface compiles for AArch64, but no whole
native UAM/Dawn runtime link or GPU execution is established. Extended runtime
LeakSanitizer checks fail; a matched control compiling the exact original class
and using only the file API reports the same 23,985 bytes / 142 allocations.
The allocator lifetime issue is preserved, not hidden by suppressions or by
disabling leak detection. Only the narrow immediate-helper sanitizer suite
passes; it must not be used as evidence that all of UAM is sanitizer-clean.

Complete receipts, dedicated source patches and independent review disposition
are kept in the isolated game's `docs/switch/SET_IMMEDIATES.md` and
`docs/switch/evidence/set-immediates-result.json`. This dependency change is not
published and does not modify the known-good Mesa/NVK or Performance Monitor.
