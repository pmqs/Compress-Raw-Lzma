[![Linux build](https://github.com/pmqs/Compress-Raw-Lzma/actions/workflows/linux.yml/badge.svg)](https://github.com/pmqs/Compress-Raw-Lzma/actions/workflows/linux.yml)
[![MacOS build](https://github.com/pmqs/Compress-Raw-Lzma/actions/workflows/macos.yml/badge.svg)](https://github.com/pmqs/Compress-Raw-Lzma/actions/workflows/macos.yml)
[![Windows build](https://github.com/pmqs/Compress-Raw-Lzma/actions/workflows/windows.yml/badge.svg)](https://github.com/pmqs/Compress-Raw-Lzma/actions/workflows/windows.yml)

[![Linux + upstream github xz](https://github.com/pmqs/Compress-Raw-Lzma/actions/workflows/linux-upstream-gh-xz.yml/badge.svg)](https://github.com/pmqs/Compress-Raw-Lzma/actions/workflows/linux-upstream-gh-xz.yml)
[![MacOS + upstream GitHub xz](https://github.com/pmqs/Compress-Raw-Lzma/actions/workflows/macos-upstream-gh-xz.yml/badge.svg)](https://github.com/pmqs/Compress-Raw-Lzma/actions/workflows/macos-upstream-gh-xz.yml)

# NAME

Compress::Raw::Lzma - Low-Level Interface to lzma compression library

# SYNOPSIS

    use Compress::Raw::Lzma ;

    # Encoders
    my ($lz, $status) = new Compress::Raw::Lzma::EasyEncoder [OPTS]
        or die "Cannot create lzma object: $status\n";

    my ($lz, $status) = new Compress::Raw::Lzma::AloneEncoder [OPTS]
        or die "Cannot create lzma object: $status\n";

    my ($lz, $status) = new Compress::Raw::Lzma::StreamEncoder [OPTS]
        or die "Cannot create lzma object: $status\n";

    my ($lz, $status) = new Compress::Raw::Lzma::RawEncoder [OPTS]
        or die "Cannot create lzma object: $status\n";

    $status = $lz->code($input, $output);
    $status = $lz->flush($output);

    # Decoders
    my ($lz, $status) = new Compress::Raw::Lzma::AloneDecoder [OPTS]
        or die "Cannot create lzma object: $status\n";

    my ($lz, $status) = new Compress::Raw::Lzma::AutoDecoder [OPTS]
        or die "Cannot create lzma object: $status\n";

    my ($lz, $status) = new Compress::Raw::Lzma::StreamDecoder [OPTS]
        or die "Cannot create lzma object: $status\n";

    my ($lz, $status) = new Compress::Raw::Lzma::RawDecoder [OPTS]
        or die "Cannot create lzma object: $status\n";

    $status = $lz->code($input, $output);

    my $version = Compress::Raw::Lzma::lzma_version_number();
    my $version = Compress::Raw::Lzma::lzma_version_string();

# DESCRIPTION

`Compress::Raw::Lzma` provides an interface to the in-memory
compression/uncompression functions from the lzma compression library.

Although the primary purpose for the existence of `Compress::Raw::Lzma` is
for use by the  `IO::Compress::Lzma`, `IO::Uncompress::UnLzma`,
`IO::Compress::Xz` and `IO::Uncompress::UnXz` modules, it can be used on
its own for simple compression/uncompression tasks.

There are two functions, called `code` and `flush`, used in all the
compression and uncompression interfaces defined in this module. By default
both of these functions overwrites any data stored in its output buffer
parameter. If you want to compress/uncompress to a single buffer, and have
`code` and `flush` append to that buffer, enable the `AppendOutput`
option when you create the compression/decompression object.

# Compression

There are four compression interfaces available in this module.

- Compress::Raw::Lzma::EasyEncoder
- Compress::Raw::Lzma::AloneEncoder
- Compress::Raw::Lzma::StreamEncoder
- Compress::Raw::Lzma::RawEncoder

## ($z, $status) = new Compress::Raw::Lzma::EasyEncoder \[OPTS\];

Creates a new _xz_ compression object.

If successful, it will return the initialised compression object, `$z`
and a `$status` of `LZMA_OK` in a list context. In scalar context it
returns the deflation object, `$z`, only.

If not successful, the returned compression object, `$z`, will be
_undef_ and `$status` will hold the an _lzma_ error code.

Below is a list of the valid options:

- **Preset => $preset**

    Used to choose the compression preset.

    Valid values are 0-9 and `LZMA_PRESET_DEFAULT`.

    0 is the fastest compression with the lowest memory usage and the lowest
    compression.

    9 is the slowest compression with the highest memory usage but with the best
    compression.

    Defaults to `LZMA_PRESET_DEFAULT`.

- **Extreme => 0|1**

    Makes the compression a lot slower, but a small compression gain.

    Defaults to 0.

- **Check => $check**

    Used to specify the integrity check used in the xz data stream.
    Valid values are `LZMA_CHECK_NONE`, `LZMA_CHECK_CRC32`,
    `LZMA_CHECK_CRC64`, `LZMA_CHECK_SHA256`.

    Defaults to `LZMA_CHECK_CRC32`.

- **AppendOutput => 0|1**

    Controls whether the compressed data is appended to the output buffer in
    the `code` and `flush` methods.

    Defaults to 0.
    (Note in versions of this module prior to 2.072 the default value was
    incorrectly documented as 1).

- **BufSize => $number**

    Sets the initial size for the output buffer used by the `$d->code`
    method. If the buffer has to be reallocated to increase the size, it will
    grow in increments of `Bufsize`.

    Defaults to 16k.

## ($z, $status) = new Compress::Raw::Lzma::AloneEncoder \[OPTS\];

Creates a legacy _lzma_ compression object. This format is also know as
lzma\_alone.

If successful, it will return the initialised compression object, `$z`
and a `$status` of `LZMA_OK` in a list context. In scalar context it
returns the deflation object, `$z`, only.

If not successful, the returned compression object, `$z`, will be
_undef_ and `$status` will hold the an _lzma_ error code.

Below is a list of the valid options:

- **Filter => $filter**

    The ` $filter ` option must be an object of type `Lzma::Filter::Lzma1`.
    See ["Lzma::Filter::Lzma" in Compress::Raw::Lzma](https://metacpan.org/pod/Compress%3A%3ARaw%3A%3ALzma#Lzma::Filter::Lzma) for a definition
    of `Lzma::Filter::Lzma1`.

    If this option is not present an `Lzma::Filter::Lzma1` object with default
    values will be used.

- **AppendOutput => 0|1**

    Controls whether the compressed data is appended to the output buffer in
    the `code` and `flush` methods.

    Defaults to 0.
    (Note in versions of this module prior to 2.072 the default value was
    incorrectly documented as 1).

- **BufSize => $number**

    Sets the initial size for the output buffer used by the `$d->code`
    method. If the buffer has to be reallocated to increase the size, it will
    grow in increments of `Bufsize`.

    Defaults to 16k.

## ($z, $status) = new Compress::Raw::Lzma::StreamEncoder \[OPTS\];

Creates a _xz_ compression object.

If successful, it will return the initialised compression object, `$z`
and a `$status` of `LZMA_OK` in a list context. In scalar context it
returns the deflation object, `$z`, only.

If not successful, the returned compression object, `$z`, will be
_undef_ and `$status` will hold the an _lzma_ error code.

Below is a list of the valid options:

- **Filter => $filter**
- **Filter => \[$filter1, $filter2,...\]**

    This option is used to change the bahaviour of the StreamEncoder by
    applying between one and `LZMA_FILTERS_MAX` filters to the data stream
    during compression. See ["Filters"](#filters) for more details on the available
    filters.

    If this option is present it must either contain a single
    `Lzma::Filter::Lzma` filter object or an array reference containing between
    one and `LZMA_FILTERS_MAX` filter objects.

    If this option is not present an `Lzma::Filter::Lzma2` object with default
    values will be used.

- **Check => $check**

    Used to specify the integrity check used in the xz data stream.
    Valid values are `LZMA_CHECK_NONE`, `LZMA_CHECK_CRC32`,
    `LZMA_CHECK_CRC64`, `LZMA_CHECK_SHA256`.

    Defaults to `LZMA_CHECK_CRC32`.

- **AppendOutput => 0|1**

    Controls whether the compressed data is appended to the output buffer in
    the `code` and `flush` methods.

    Defaults to 0.
    (Note in versions of this module prior to 2.072 the default value was
    incorrectly documented as 1).

- **BufSize => $number**

    Sets the initial size for the output buffer used by the `$d->code`
    method. If the buffer has to be reallocated to increase the size, it will
    grow in increments of `Bufsize`.

    Defaults to 16k.

## ($z, $status) = new Compress::Raw::Lzma::RawEncoder \[OPTS\];

Low level access to lzma.

If successful, it will return the initialised compression object, `$z`
and a `$status` of `LZMA_OK` in a list context. In scalar context it
returns the deflation object, `$z`, only.

If not successful, the returned compression object, `$z`, will be
_undef_ and `$status` will hold the an _lzma_ error code.

Below is a list of the valid options:

- **Filter => $filter**
- **Filter => \[$filter1, $filter2,...\]**

    This option is used to change the bahaviour of the RawEncoder by
    applying between one and `LZMA_FILTERS_MAX` filters to the data stream
    during compression. See ["Filters"](#filters) for more details on the available
    filters.

    If this option is present it must either contain a single
    `Lzma::Filter::Lzma` filter object or an array reference containing between
    one and `LZMA_FILTERS_MAX` filter objects.

    If this option is not present an `Lzma::Filter::Lzma2` object with default
    values will be used.

- **AppendOutput => 0|1**

    Controls whether the compressed data is appended to the output buffer in
    the `code` and `flush` methods.

    Defaults to 0.
    (Note in versions of this module prior to 2.072 the default value was
    incorrectly documented as 1).

- **BufSize => $number**

    Sets the initial size for the output buffer used by the `$d->code`
    method. If the buffer has to be reallocated to increase the size, it will
    grow in increments of `Bufsize`.

    Defaults to 16k.

- **ForZip => 1/0**

    This boolean option is used to enable prefixing the compressed data stream
    with an encoded copy of the filter properties.

    Defaults to 0.

## $status = $lz->code($input, $output)

Reads the contents of `$input`, compresses it and writes the compressed
data to `$output`.

Returns `LZMA_OK` on success and an `lzma` error code on failure.

If `appendOutput` is enabled in the constructor for the lzma object, the
compressed data will be appended to `$output`. If not enabled, `$output`
will be truncated before the compressed data is written to it.

## $status = $lz->flush($output, LZMA\_FINISH);

Flushes any pending compressed data to `$output`. By default it terminates
the compressed data stream.

Returns `LZMA_OK` on success and an `lzma` error code on failure.

## Example

TODO

# Uncompression

There are four uncompression interfaces available in this module.

- Compress::Raw::Lzma::AutoDecoder
=item Compress::Raw::Lzma::AloneDecoder
=item Compress::Raw::Lzma::StreamDecoder
=item Compress::Raw::Lzma::RawDecoder

## ($z, $status) = new Compress::Raw::Lzma::AutoDecoder \[OPTS\] ;

Create an object that can uncompress any of the compressed data streams
that can be created by this module.

If successful, it will return the initialised uncompression object, `$z`
and a `$status` of `LZMA_OK` in a list context. In scalar context it
returns the deflation object, `$z`, only.

If not successful, the returned uncompression object, `$z`, will be
_undef_ and `$status` will hold the an _lzma_ error code.

Below is a list of the valid options:

- **-MemLimit**

    The number of bytes to use when uncompressing.

    Default is unlimited.

- **-Bufsize**

    Sets the initial size for the output buffer used by the `$i->code`
    method. If the output buffer in this method has to be reallocated to
    increase the size, it will grow in increments of `Bufsize`.

    Default is 16k.

- **-AppendOutput**

    This option controls how data is written to the output buffer by the
    `$i->code` method.

    If the option is set to false, the output buffer in the `$i->code`
    method will be truncated before uncompressed data is written to it.

    If the option is set to true, uncompressed data will be appended to the
    output buffer by the `$i->code` method.

    This option defaults to false.

- **-ConsumeInput**

    If set to true, this option will remove compressed data from the input
    buffer of the `$i->code` method as the uncompression progresses.

    This option can be useful when you are processing compressed data that is
    embedded in another file/buffer. In this case the data that immediately
    follows the compressed stream will be left in the input buffer.

    This option defaults to true.

- **-LimitOutput**

    The `LimitOutput` option changes the behavior of the `$i->code`
    method so that the amount of memory used by the output buffer can be
    limited.

    When `LimitOutput` is used the size of the output buffer used will either
    be the value of the `Bufsize` option or the amount of memory already
    allocated to `$output`, whichever is larger. Predicting the output size
    available is tricky, so don't rely on getting an exact output buffer size.

    When `LimitOutout` is not specified `$i->code` will use as much
    memory as it takes to write all the uncompressed data it creates by
    uncompressing the input buffer.

    If `LimitOutput` is enabled, the `ConsumeInput` option will also be
    enabled.

    This option defaults to false.

    See ["The LimitOutput option"](#the-limitoutput-option) for a discussion on why `LimitOutput` is
    needed and how to use it.

## ($z, $status) = new Compress::Raw::Lzma::AloneDecoder \[OPTS\] ;

Create an object that can uncompress an lzma\_alone data stream.

If successful, it will return the initialised uncompression object, `$z`
and a `$status` of `LZMA_OK` in a list context. In scalar context it
returns the deflation object, `$z`, only.

If not successful, the returned uncompression object, `$z`, will be
_undef_ and `$status` will hold the an _lzma_ error code.

Below is a list of the valid options:

- **-MemLimit**

    The number of bytes to use when uncompressing.

    Default is unlimited.

- **-Bufsize**

    Sets the initial size for the output buffer used by the `$i->code`
    method. If the output buffer in this method has to be reallocated to
    increase the size, it will grow in increments of `Bufsize`.

    Default is 16k.

- **-AppendOutput**

    This option controls how data is written to the output buffer by the
    `$i->code` method.

    If the option is set to false, the output buffer in the `$i->code`
    method will be truncated before uncompressed data is written to it.

    If the option is set to true, uncompressed data will be appended to the
    output buffer by the `$i->code` method.

    This option defaults to false.

- **-ConsumeInput**

    If set to true, this option will remove compressed data from the input
    buffer of the `$i->code` method as the uncompression progresses.

    This option can be useful when you are processing compressed data that is
    embedded in another file/buffer. In this case the data that immediately
    follows the compressed stream will be left in the input buffer.

    This option defaults to true.

- **-LimitOutput**

    The `LimitOutput` option changes the behavior of the `$i->code`
    method so that the amount of memory used by the output buffer can be
    limited.

    When `LimitOutput` is used the size of the output buffer used will either
    be the value of the `Bufsize` option or the amount of memory already
    allocated to `$output`, whichever is larger. Predicting the output size
    available is tricky, so don't rely on getting an exact output buffer size.

    When `LimitOutout` is not specified `$i->code` will use as much
    memory as it takes to write all the uncompressed data it creates by
    uncompressing the input buffer.

    If `LimitOutput` is enabled, the `ConsumeInput` option will also be
    enabled.

    This option defaults to false.

    See ["The LimitOutput option"](#the-limitoutput-option) for a discussion on why `LimitOutput` is
    needed and how to use it.

## $status = $z->code($input, $output);

Uncompresses `$input` and writes the uncompressed data to `$output`.

Returns `LZMA_OK` if the uncompression was successful, but the end of the
compressed data stream has not been reached. Returns `LZMA_STREAM_END` on
successful uncompression and the end of the compression stream has been
reached.

If `consumeInput` is enabled in the constructor for the lzma object,
`$input` will have all compressed data removed from it after
uncompression. On `LZMA_OK` return this will mean that `$input` will be an
empty string; when `LZMA_STREAM_END` `$input` will either be an empty
string or will contain whatever data immediately followed the compressed
data stream.

If `appendOutput` is enabled in the constructor for the lzma object,
the uncompressed data will be appended to `$output`. If not enabled,
`$output` will be truncated before the uncompressed data is written to it.

# Filters

TODO - more here

A number of the Lzma compression interfaces (namely
`Compress::Raw::Lzma::StreamEncoder` &
`Compress::Raw::Lzma::AloneEncoder`) and the raw lzma uncompression interface
make use of filters. These filters are used to change the behaviour of
compression (and raw uncompression).

All Lzma Filters are sub-classed from the `Lzma::Filter` base-class.

## Lzma::Filter::Lzma

The `Lzma::Filter::Lzma` class is used to... TODO - more here

There are two subclasses of `Lzma::Filter::Lzma`, namely
`Lzma::Filter::Lzma1` and `Lzma::Filter::Lzma2`.

The former is typically used with `Compress::Raw::Lzma::AloneEncoder`.
The latter with `Compress::Raw::Lzma::StreamEncoder`.

When using Lzma filters an `Lzma::Filter::Lzma` _must_ be included and it
_must_ be the last filter in the chain. There can only be one
`Lzma::Filter::Lzma` filter in any filter chain.

The `Lzma::Filter::Lzma` construction takes the following options.

- DictSize => $value

    Dictionary size in bytes. This controls
    how many bytes of the recently processed
    uncompressed data is kept in memory. The size of the dictionary must be at
    least `LZMA_DICT_SIZE_MIN`.

    Defaults to `LZMA_DICT_SIZE_DEFAULT`.

- PresetDict => $dict

    Provide an initial dictionary. This value is used to initialize the LZ77 history window.

    This feature only works correctly with raw encoding and decoding.
    You may not be able to decode other formats that have been encoded with a preset dictionary.

    `$dict` should contain typical strings that occur in the files being compressed,
    with the most probably strings near the end fo the preset dictionary.

    If `$dict` is larger than `DictSize`, only the last `DictSize` bytes are processed.

- Lc => $value

    Number of literal context bits.

    How many of the highest bits of the previous uncompressed
    eight-bit byte (also known as \`literal') are taken into
    account when predicting the bits of the next literal.

    `$value` must be a number between `LZMA_LCLP_MIN` and
    `LZMA_LCLP_MAX`.

    Note the sum of the `Lc` and `Lp` options cannot exceed 4.

    Defaults to `LZMA_LC_DEFAULT`.

- Lp => $value

    Number of literal position bits.

    How many of the lowest bits of the current position (number
    of bytes from the beginning of the uncompressed data) in the
    uncompressed data is taken into account when predicting the
    bits of the next literal (a single eight-bit byte).

    Defaults to `LZMA_LP_DEFAULT`.

- Pb => $value

    Number of position bits

    How many of the lowest bits of the current position in the
    uncompressed data is taken into account when estimating
    probabilities of matches. A match is a sequence of bytes for
    which a matching sequence is found from the dictionary and
    thus can be stored as distance-length pair.

    `$value` must be a number between `LZMA_PB_MIN` and
    `LZMA_PB_MAX`.

    Defaults to `LZMA_PB_DEFAULT`.

- Mode => $value

    The Compression Mode. Valid values are `LZMA_MODE_FAST` and
    `LZMA_MODE_NORMAL`.

    Defaults to `LZMA_MODE_NORMAL`.

- Nice => $value

    Nice length of a match

    Defaults to 64.

- Mf => $value

    Defines which Match Finder to use. Valid values are `LZMA_MF_HC3`
    `LZMA_MF_HC4`, `LZMA_MF_BT2` `LZMA_MF_BT3` and `LZMA_MF_BT4`.

    Defaults to `LZMA_MF_BT4`.

- Depth => $value

    Maximum search depth in the match finder.

    Defaults to 0.

## Lzma::Filter::BCJ

The sub-classes of `Lzma::Filter::BCJ` are the
Branch/Call/Jump conversion filters. These filters are used to rewrite
executable binary code for a number of processor architectures.
None of these classes take any options.

- Lzma::Filter::X86

    Filter for x86 binaries.

- Lzma::Filter::PowerPC

    Filter for Big endian PowerPC binaries.

- Lzma::Filter::IA64

    Filter for IA64 (Itanium) binaries.

- Lzma::Filter::ARM

    Filter for ARM binaries.

- Lzma::Filter::ARMThumb

    Filter for ARMThumb binaries.

- Lzma::Filter::Sparc

    Filter for Sparc binaries.

## Lzma::Filter::Delta

Usage is

    Lzma::Filter::Delta [OPTS]

- Type => $type

    Defines the type of Delta calculation. The only available type (and
    therefore the default) is
    `LZMA_DELTA_TYPE_BYTE`,

- Distance => $value

    Defines the Delta Distance. `$value` must be a number between
    `LZMA_DELTA_DIST_MIN` and `LZMA_DELTA_DIST_MAX`.

    Default is `LZMA_DELTA_DIST_MIN`.

# Misc

## my $version = Compress::Raw::Lzma::lzma\_version\_number();

Returns the version of the underlying lzma library this module is using at
run-time as a number.

## my $version = Compress::Raw::Lzma::lzma\_version\_string();

Returns the version of the underlying lzma library this module is using at
run-time as a string.

## my $version = Compress::Raw::Lzma::LZMA\_VERSION();

Returns the version of the underlying lzma library this module was using at
compile-time as a number.

## my $version = Compress::Raw::Lzma::LZMA\_VERSION\_STRING();

Returns the version of the underlying lzma library this module was using at
compile-time as a string.

# Constants

The following lzma constants are exported by this module

TODO - more here

# SUPPORT

General feedback/questions/bug reports should be sent to
[https://github.com/pmqs/Compress-Raw-Lzma/issues](https://github.com/pmqs/Compress-Raw-Lzma/issues) (preferred) or
[https://rt.cpan.org/Public/Dist/Display.html?Name=Compress-Raw-Lzma](https://rt.cpan.org/Public/Dist/Display.html?Name=Compress-Raw-Lzma).

# SEE ALSO

[Compress::Zlib](https://metacpan.org/pod/Compress%3A%3AZlib), [IO::Compress::Gzip](https://metacpan.org/pod/IO%3A%3ACompress%3A%3AGzip), [IO::Uncompress::Gunzip](https://metacpan.org/pod/IO%3A%3AUncompress%3A%3AGunzip), [IO::Compress::Deflate](https://metacpan.org/pod/IO%3A%3ACompress%3A%3ADeflate), [IO::Uncompress::Inflate](https://metacpan.org/pod/IO%3A%3AUncompress%3A%3AInflate), [IO::Compress::RawDeflate](https://metacpan.org/pod/IO%3A%3ACompress%3A%3ARawDeflate), [IO::Uncompress::RawInflate](https://metacpan.org/pod/IO%3A%3AUncompress%3A%3ARawInflate), [IO::Compress::Bzip2](https://metacpan.org/pod/IO%3A%3ACompress%3A%3ABzip2), [IO::Uncompress::Bunzip2](https://metacpan.org/pod/IO%3A%3AUncompress%3A%3ABunzip2), [IO::Compress::Lzma](https://metacpan.org/pod/IO%3A%3ACompress%3A%3ALzma), [IO::Uncompress::UnLzma](https://metacpan.org/pod/IO%3A%3AUncompress%3A%3AUnLzma), [IO::Compress::Xz](https://metacpan.org/pod/IO%3A%3ACompress%3A%3AXz), [IO::Uncompress::UnXz](https://metacpan.org/pod/IO%3A%3AUncompress%3A%3AUnXz), [IO::Compress::Lzip](https://metacpan.org/pod/IO%3A%3ACompress%3A%3ALzip), [IO::Uncompress::UnLzip](https://metacpan.org/pod/IO%3A%3AUncompress%3A%3AUnLzip), [IO::Compress::Lzop](https://metacpan.org/pod/IO%3A%3ACompress%3A%3ALzop), [IO::Uncompress::UnLzop](https://metacpan.org/pod/IO%3A%3AUncompress%3A%3AUnLzop), [IO::Compress::Lzf](https://metacpan.org/pod/IO%3A%3ACompress%3A%3ALzf), [IO::Uncompress::UnLzf](https://metacpan.org/pod/IO%3A%3AUncompress%3A%3AUnLzf), [IO::Compress::Zstd](https://metacpan.org/pod/IO%3A%3ACompress%3A%3AZstd), [IO::Uncompress::UnZstd](https://metacpan.org/pod/IO%3A%3AUncompress%3A%3AUnZstd), [IO::Uncompress::AnyInflate](https://metacpan.org/pod/IO%3A%3AUncompress%3A%3AAnyInflate), [IO::Uncompress::AnyUncompress](https://metacpan.org/pod/IO%3A%3AUncompress%3A%3AAnyUncompress)

[IO::Compress::FAQ](https://metacpan.org/pod/IO%3A%3ACompress%3A%3AFAQ)

[File::GlobMapper](https://metacpan.org/pod/File%3A%3AGlobMapper), [Archive::Zip](https://metacpan.org/pod/Archive%3A%3AZip),
[Archive::Tar](https://metacpan.org/pod/Archive%3A%3ATar),
[IO::Zlib](https://metacpan.org/pod/IO%3A%3AZlib)

# AUTHOR

This module was written by Paul Marquess, `pmqs@cpan.org`.

# MODIFICATION HISTORY

See the Changes file.

# COPYRIGHT AND LICENSE

Copyright (c) 2005-2023 Paul Marquess. All rights reserved.

This program is free software; you can redistribute it and/or
modify it under the same terms as Perl itself.
