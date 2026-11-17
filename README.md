# AudioSpectrogram 1.3.3

Lister plugin (WLX) for Total Commander.
Shows music files as a spectrogram with technical data and measurements;
compares up to four files. The file is only read, never changed.
Manual: notes\manual\AudioSpectrogram.html
Case collection with images (separate package): notes\cases

The plugin measures and displays. It does not rate.

<img width="1920" height="1041" alt="2026-11-17_132037" src="https://github.com/user-attachments/assets/618c6fcb-2505-4a25-8dde-46d5be0cc075" />



## Display
- Spectrogram 
  - X = time, Y = frequency (kHz), colour = level (dB)
- Average spectrum 
  - X = frequency (kHz), Y = level (dB)
- Third-octave bands
  - the average spectrum summed in 1/3-octave bands (IEC 61260, 25 Hz to 20 kHz at 44.1 kHz), one bar per band; in comparison one bar per file and band. A full-scale sine reads 0 dB in its band, wherever it lies between two FFT bins. Calculated with a larger FFT (16384 at 44.1/48 kHz); until it is ready the bands from 200 Hz upward come from FFT 2048. An empty slot means the band is below -100 dB. Copy measurements (Ctrl+C) adds the band values.
- Measuring cursor
  - move the mouse over the chart; values at top right
- Dashed line
  - measured cutoff frequency
- Zoom (spectrogram)
  - mouse wheel enlarges the time axis at the mouse position (down to half a second), Ctrl + mouse wheel the frequency axis (down to 1/100). Drag with the left button to move, in both directions. Double click or "Reset zoom" shows everything again. Works in comparison too.
  - The visible part is analysed again (status line: "Refining"); until then the enlarged image of the whole file is shown. For a narrow frequency range the FFT size grows automatically (4096 to 16384); the status line shows the size in use. A larger FFT separates frequencies more finely but blurs in time: a mathematical limit, not a defect.
- Right click 
  - view, channels, frequency axis, measurements on/off
- Save image (PNG)... 
  - right click; saves the current view as shown, without the mouse cursor lines, in the size of the window
- Play
  - click in the spectrogram plays the file from that position; a line shows where it is. Space = pause / continue, Esc = stop (Esc again closes Lister). In comparison the clicked file is played. Can be switched off in the settings.
- Playback bar
  - below the time axis: play / pause, stop, loop (start again at the end), position and length (m:ss.mmm), volume. Space starts playback from the left edge of the visible range. Play starts at the left edge of the visible range. Volume: drag, click or mouse wheel over the bar; 100 % = original level, it only gets quieter. Only the sound changes, never a measurement. In comparison a coloured mark shows which file plays. Not in the saved PNG.
- Settings...
  - right click; click to play, language, colour scheme, cache (limit, folder, clear). Values are stored in AudioSpectrogram.ini
- Language
  - AudioSpectrogram.lng holds the texts. The plugin follows the language of Total Commander; a fixed language can be set in the settings. Missing texts stay English. How to add a language is described in the file.
- Light / dark mode
  - the window follows Total Commander; the chart area is dark in both modes, so colours mean the same
- The measurement table is hidden automatically in small windows.

## Comparison 
- Right click, "Compare" turns comparison on. Every file loaded afterwards in Lister with N or P is added (4 at most; the fifth pushes out the oldest). Select several files, F3, turn on "Compare", step through with N.
- Spectrogram
  - spectrograms stacked, same time and frequency axis, shared measuring cursor  
- Average spectrum
  - average curves overlaid, one colour per file; short mark at the bottom = cutoff frequency
- Measurement table
  - one column per file; with "Mark differences" values that differ are shown in bold; longer tables scroll with the mouse wheel
- The axes follow the longest file and the highest sample rate. In comparison the channels are shown mixed. The list belongs to its Lister window. There is no ranking.

## Log and clipboard
- Right click, "Copy measurements" (or Ctrl+C in Lister) copies the measurements of the displayed file as text; in comparison the whole table.
- Right click, "Save folder log..." writes a log for the folder of the displayed file: one line per audio file, columns separated by tabs (readable in an editor, a table in a spreadsheet). Files already measured come from the cache, the others are measured in the background; the status line shows the progress. Units are in the header row, an empty cell means not applicable. 
- The log contains measurements only, no rating and no ranking.

## Colour scale
The colour in the spectrogram stands for the level in dB, not for the frequency. The bar right of the spectrogram shows the scale from -120 to 0 dB. Colour schemes (settings dialog or INI): Standard, Aurora, Viridis, Magma, Fire, Green, Grey. In every scheme the brightness rises evenly with the level, so a quieter place never looks brighter than a louder one. Viridis is also readable with red-green colour blindness, Grey is meant for printing. The images in the manual and the case collection use Standard.  

## Measurements and their definitions
- File / Format
  - Data from the file header. Bitrate (header) = as stated in the file, Bitrate (file) = file size / duration. Encoder (header) is the entry in the Xing/Info header of an MP3, with lowpass for LAME. Encoder (stream) is the signature inside the music frames themselves. Example: header "Lavc59.38", stream "LAME3.100" = encoded with ffmpeg through the LAME library. The stereo mode is taken from the music frames, not from the frame carrying the header.
- Cutoff
  - Highest frequency at which the mean level in the kilohertz below is at least 20 dB above the highest level above it. The position of the steepest drop is shown. "none" = no such edge.
- Slope
  - Steepness of that drop in dB per kHz.
- Above 24 kHz
  - Level of the band from 24 kHz to half the sample rate, relative to a full-scale sine. Only for sample rates above 48 kHz. For material upsampled from 44.1 or 48 kHz nothing remains in this band.
- Bits used
  - Bit depth actually used: bit depth minus the number of low bits that are zero in all samples. Only for WAV and FLAC up to 24 bit. 
- Sample peak
  - Largest sample magnitude in dBFS.
- True peak
  - Peak after 4x oversampling (2x from 88.2 kHz) in dBTP. Other meters may differ by about 0.2 dB.
- RMS level
  - Root mean square of all samples of all channels in dBFS. A full-scale sine reads -3.01 dBFS. (The DR method counts differently: there a full-scale sine has RMS 0 dB.)
- Crest factor
  - Sample peak minus RMS level, in dB. A sine has 3.01 dB. Crest factor and DR are related but not the same: DR uses only the loudest 20 % of the 3-second blocks, the second highest block peak and an RMS that reads 3.01 dB higher. On steady signals DR is about 3 dB below the crest factor.
- Loudness, Loudness range
  - Integrated loudness per ITU-R BS.1770 / EBU R128 and loudness range per EBU Tech 3342. Channel weights per ITU-R BS.1770: side surround channels 1.41, LFE not counted (5, 6 and 8 channels in the usual order; stereo and mono are not affected).
- DR
  - Per channel: blocks of about 3 seconds (132480 samples at 44.1 kHz; the last, shorter block counts too). RMS and peak of each block are put on a 0.01 dB grid. The second highest block peak is set against the RMS of the loudest 20 % of the blocks. The value of the file is the mean of the channels, rounded. The folder log also lists the unrounded value per channel.  
    A re-implementation of the well-known DR method, not the original software.
- Clipping
  - Number of places with at least 3 consecutive samples at 99.99 % of full scale or above.
- DC offset
  - Largest DC component of any channel in percent of full scale.
- Balance L/R, Correlation, L = R
  - Level left minus level right, correlation of the channels (-1 to +1), and whether both channels are identical sample by sample. 2 channels only.
    Balance and correlation stay empty when one channel is silent.

For MP3 and AAC, level values refer to the decoder output.  
"-" means: not applicable to this file. For a silent file the level values and DR are empty.

## Measurement accuracy
No conformance to a standard is claimed. Listed is what each value was checked against, the number of cases and the largest deviation observed. State: version 1.3.3 (measurement code unchanged since 1.0.0).

| Value                 | Checked against                    | Cases | Largest dev. |
|-----------------------|------------------------------------:|------:|-------------:|
| DR per channel        | foobar2000 DR Meter 1.0.8          |    50 | 0.00 dB      |
| DR per file (rounded) | foobar2000 DR Meter 1.0.8          |    41 | 0            |
| Sample peak           | foobar2000 DR Meter 1.0.8          |    41 | 0.00 dB      |
| Sample peak           | Exact Audio Copy 1.8 (percent)     |    15 | <0.1 %       |
| Balance L/R           | foobar2000 (RMS left - RMS right)  |    15 | 0.01 dB      |
| Loudness              | test signals, EBU Tech 3341 (1-5)   |     5 | 0.02 LU      |
| Loudness, 5.1         | test signal, EBU Tech 3341 (6)      |     1 | 0.02 LU      |
| Loudness range        | test signals, EBU Tech 3342 (1-4)   |     4 | 0.00 LU      |
| True peak             | sines with known true peak          |     5 | 0.17 dB low  |
| K-weighting filter    | ITU-R BS.1770-4, tables 1 and 2     |     8 | equal to 14  |
|                       | (coefficients at 48 kHz)            |       | decimals     |
| Vorbis, Opus decoder  | ffmpeg decoding the same file       |     2 | values equal |
| Third-octave bands    | FFT of the whole file, exact band edges (sines 50, 1000, 1010.7 Hz; white and pink noise) | 5 | 0.01 dB (sines); 0.33 dB (noise, 25-40 Hz), below 0.1 dB from 80 Hz |

Test material for the first four lines: CD rip, 15 tracks, 16 bit, 44.1 kHz, stereo, confirmed by AccurateRip (confidence 24 to 25). The balance deviation is the rounding of the two RMS values shown by foobar2000.

| Crest factor          | expected values of test files      |    26 | <0.05 dB     |

DR per file and sample peak: additionally 26 artificial test files (sine, noise, compressed and overdriven signals, silent channel, mono, 48 and 96 kHz, 24 bit, 5.1, a file of 2 seconds).

The RMS value of foobar2000 follows the DR convention and is not directly comparable with "RMS level".  
The EBU test signals were generated from the description in the EBU documents; they are not the original EBU files.

Cutoff: an edge only counts if there is signal below it (mean level above -120 dB); the fading noise floor of high-resolution files is not reported as a cutoff.  

Not checked yet: loudness with real programme material against a second meter in larger numbers; multichannel files other than 5.1.

## Cutoff frequencies observed
The cutoff is measured on the average spectrum of the whole file. An encoder that changes its lowpass during the piece shows the average. Measured with one piece (track from a CD confirmed by AccurateRip, 44.1 kHz), encoder LAME:

| Setting         | Lowpass in header | Cutoff measured |
|-----------------|------------------:|----------------:|
| CBR 128 kbit/s  | 17.0 kHz          | 16.63 kHz       | 
| VBR -V5         | 17.0 kHz          | 16.63 kHz       |
| CBR 320 kbit/s  | 20.5 kHz          | 20.12 kHz       |
| VBR -V0         | 22.1 kHz          | none            |

These are observations, not limits. Other encoders, versions and pieces give other values; a cutoff can also come from the recording itself.

## Measurement basis of the spectrum
FFT 2048 points, Hann window; all measurements use the analysis of the whole file with this size. Zoomed images may use a larger FFT (see Zoom). Levels in dBFS: a full-scale sine on a bin frequency reads 0.0 dB. A tone between two bins reads up to 1.4 dB lower. "Mixed" is the mean power of L and R.

## Cache
Stored are format data, measurements and the average spectrum, about 13 KB per file. The spectrogram is recalculated every time. Location: `%LOCALAPPDATA%\AudioSpectrogram\Cache` (can be changed in the INI).
If the audio file changes (size or date), it is measured again.
When the limit is reached, the entries unused for the longest time are removed. "Clear cache" in the settings dialog empties the cache.
The status line shows "values from cache" when the values come from it.

## Formats in this build
- WAV, FLAC 
  - built-in decoder (any Windows from 7)
- MP3, AAC (M4A)
  - Media Foundation 
- ALAC (M4A)
  - Media Foundation, Windows 10 or later
- Ogg Vorbis
  - built-in decoder (stb_vorbis), all Windows versions
- Opus (.opus, Ogg)  
  - built-in decoder (libopus 1.5.2), all Windows versions;
    Opus always decodes at 48 kHz

## Installation
Open the ZIP in Total Commander and confirm the installation.

## When the plugin loads (detect string)
Total Commander decides with the detect string of the plugin whether it is used for a file. It is stored in wincmd.ini, section [ListerPlugins], and can be edited there. Default:

    MULTIMEDIA & (EXT="MP3" | EXT="FLAC" | EXT="WAV" | EXT="M4A" | 
    EXT="AAC" | EXT="OGG" | EXT="OGA" | EXT="OPUS")

With this string the plugin opens on F3 / Ctrl+Q for these files.  
To load it on request only (Lister menu Options, Image/Multimedia, key 4), put FORCE in front:

    FORCE & MULTIMEDIA & (EXT="MP3" | ...)

To leave a file type to another viewer, remove its EXT="..." entry.

## Settings (AudioSpectrogram.ini in the plugin folder)
- Section [General]: ClickToPlay, Language.
- Section [Player]: Volume, Loop.
- Section [View] stores the choices made in the right-click menu and the colour scheme (ColourScheme).
- Section [Cache]: Enabled, MaxSizeMB, Path.

## System requirements
Windows 7 or later, Total Commander 32 or 64 bit. ALAC needs Windows 10.
The plugin calls only Windows functions that exist since Windows 7.  
Which C runtime it needs depends on the compiler used for the build:
- MSVCRT
  - part of every Windows since Windows 7
- UCRT  
  - part of Windows 10 and later; on Windows 7 only with the "Universal C Runtime" update (KB2999226)

build.bat prints the runtime of each file it has built.

## License
MIT, see LICENSE.txt

## Included decoders and colour scales
- dr_flac, stb_vorbis (public domain / MIT)
- libopus (BSD licence)
- viridis, magma colour scales from matplotlib (CC0)
  
See THIRD_PARTY.txt.

## Building from source
After changing the compiler, delete the folder "build" first, so that the Opus library is rebuilt with the new compiler.

build.bat builds the Opus library once (build\libopus64.a and build\libopus32.a, about a minute) and reuses it afterwards. Delete the two files to rebuild it. The Opus sources in src\third_party\opus are the unchanged files of the official release 1.5.2 (https://opus-codec.org, https://github.com/xiph/opus, tag v1.5.2); config.h, sources.rsp and objects.rsp in that folder were added for this build.
