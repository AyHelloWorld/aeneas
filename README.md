# aeneas 

**aeneas** is a Python library and a set of tools to automagically synchronize audio and text (aka forced alignment).

* Version: 1.4.1
* Date: 2016-02-13
* Developed by: [ReadBeyond](http://www.readbeyond.it/)
* Lead Developer: [Alberto Pettarin](http://www.albertopettarin.it/)
* License: the GNU Affero General Public License Version 3 (AGPL v3)
* Contact: [aeneas@readbeyond.it](mailto:aeneas@readbeyond.it)
* Quick Links: [Home](http://www.readbeyond.it/aeneas/) - [GitHub](https://github.com/readbeyond/aeneas/) - [PyPI](https://pypi.python.org/pypi/aeneas/) - [API Docs](http://www.readbeyond.it/aeneas/docs/) - [Web App](http://aeneasweb.org)
 

1. [Goal](#goal)
2. [System Requirements, Supported Platforms and Installation](#system-requirements-supported-platforms-and-installation)
    1. [System Requirements](#system-requirements)
    2. [Supported Platforms](#supported-platforms)
    3. [Installation](#installation)
        1. [Using pip (OS Independent)](#using-pip-os-independent)
        2. [Linux](#linux)
        3. [Windows](#windows)
        4. [Mac OS X](#mac-os-x)
3. [Usage](#usage)
4. [Documentation](#documentation)
5. [Supported Features](#supported-features)
6. [Limitations and Missing Features](#limitations-and-missing-features)
7. [TODO List](#todo-list)
8. [How Does This Thing Work?](#how-does-this-thing-work)
9. [License](#license)
10. [Supporting and Contributing](#supporting-cand-contributing)
    1. [Sponsors](#sponsors)
    2. [Supporting](#supporting)
    3. [Contributing](#contributing)
11. [Development History](#development-history)
12. [Acknowledgments](#acknowledgments)


## Goal

**aeneas** automatically generates a **synchronization map**
between a list of text fragments
and an audio file containing the narration of the text.
In computer science this task is known as
(automatically computing a) **forced alignment**.

For example, given [this text file](https://raw.githubusercontent.com/readbeyond/aeneas/master/aeneas/tests/res/container/job/assets/p001.xhtml)
and [this audio file](https://raw.githubusercontent.com/readbeyond/aeneas/master/aeneas/tests/res/container/job/assets/p001.mp3),
**aeneas** determines, for each fragment, the corresponding time interval in the audio file:

```
1                                                     => [00:00:00.000, 00:00:02.680]
From fairest creatures we desire increase,            => [00:00:02.680, 00:00:05.480]
That thereby beauty's rose might never die,           => [00:00:05.480, 00:00:08.640]
But as the riper should by time decease,              => [00:00:08.640, 00:00:11.960]
His tender heir might bear his memory:                => [00:00:11.960, 00:00:15.280]
But thou contracted to thine own bright eyes,         => [00:00:15.280, 00:00:18.520]
Feed'st thy light's flame with self-substantial fuel, => [00:00:18.520, 00:00:22.760]
Making a famine where abundance lies,                 => [00:00:22.760, 00:00:25.720]
Thy self thy foe, to thy sweet self too cruel:        => [00:00:25.720, 00:00:31.240]
Thou that art now the world's fresh ornament,         => [00:00:31.240, 00:00:34.280]
And only herald to the gaudy spring,                  => [00:00:34.280, 00:00:36.960]
Within thine own bud buriest thy content,             => [00:00:36.960, 00:00:40.640]
And tender churl mak'st waste in niggarding:          => [00:00:40.640, 00:00:43.600]
Pity the world, or else this glutton be,              => [00:00:43.600, 00:00:48.000]
To eat the world's due, by the grave and thee.        => [00:00:48.000, 00:00:53.280]
```

This synchronization map can be output to file in several formats:
SMIL for EPUB 3, SBV/SRT/SUB/TTML/VTT for closed captioning,
JSON/RBSE for Web usage,
or raw CSV/SSV/TSV/TXT/XML for further processing.


## System Requirements, Supported Platforms and Installation

### System Requirements

1. a reasonably recent machine (recommended 4 GB RAM, 2 GHz 64bit CPU)
2. `ffmpeg` and `ffprobe` executables available in your `$PATH`
3. `espeak` executable available in your `$PATH`
4. Python 2.7 (Linux, OS X, Windows) or 3.4 or later (Linux, OS X)
5. Python modules `BeautifulSoup4`, `lxml`, and `numpy`
6. (Optional, strongly recommended) Python C headers to compile the Python C extensions
7. (Optional, strongly recommended if you plan to use the CLI tools) A shell supporting UTF-8
8. (Optional, only required if you plan to download audio from YouTube) Python module `pafy`

Depending on the format(s) of audio files you work with,
you might need to install additional audio codecs for `ffmpeg`.
Similarly, you might need to install additional voices
for `espeak`, depending on the language(s) you work on.
(Installing _all_ the codecs and _all_ the voices available
might be a good idea.)

### Supported Platforms

**aeneas** has been developed and tested on **Debian 64bit**,
which is the **only supported OS** at the moment.

(Do you need official support for another OS?
Consider [sponsoring](#supporting) this project!)

However, **aeneas** has been confirmed to work on the following systems:

| OS             | 32/64 bit | Python 2.7 | Python 3.4/3.5  |
|----------------|-----------|------------|-----------------|
| Debian         | 64        | Yes        | Yes             |
| Debian         | 32        | Yes        | Yes             |
| Ubuntu         | 64        | Yes        | Yes             |
| Gentoo         | 64        | Yes        | Unknown         |
| Slackware      | 64        | Yes        | Unknown         |
| Mac OS X 10.9  | 64        | Yes (1)    | Unknown (1)     |
| Mac OS X 10.10 | 64        | Yes (1)    | Unknown (1)     |
| Mac OS X 10.11 | 64        | Yes (1)    | Unknown (1)     |
| Windows Vista  | 32        | Yes (1)    | Yes (1, 2)      |
| Windows 7      | 64        | Yes (1)    | Yes (1, 2)      |
| Windows 8.1    | 64        | Yes (1)    | Unknown (1, 2)  |
| Windows 10     | 64        | Yes (1)    | Yes (1, 2)      |

**Notes**
(1) The ``cew`` Python C extension to speed up text synthesis
is available only on Linux at the moment.
(2) On Windows and Python 3.4/3.5, compiling the Python C extensions
is quite complex; however, running **aeneas** in pure Python mode
has been confirmed to work.

Anyway, **aeneas** should work on any OS, at least in pure Python mode,
provided that:

1. the required Python modules `BeautifulSoup4`, `lxml`, and `numpy` are installed, and
2.  `ffmpeg`, `ffprobe` (which is part of `ffmpeg` distribution), and `espeak`
are installed and callable by the `subprocess` Python module.
A way to ensure the latter consists
in adding these three executables to your `PATH` environment variable.

All strings and text files read by **aeneas** are expected to be UTF-8 encoded,
and all text files written by **aeneas** are UTF-8 encoded.
Therefore, it is strongly recommended to run the **aeneas** CLI tools
on a shell with UTF-8 encoding and to convert any input text file to UTF-8.

If installing **aeneas** natively on your OS proves difficult,
you are strongly encouraged to use
[aeneas-vagrant](https://github.com/readbeyond/aeneas-vagrant),
which provides **aeneas** inside a virtualized Debian image
running under [VirtualBox](https://www.virtualbox.org/)
and [Vagrant](http://www.vagrantup.com/), which can be installed
on any modern OS (Linux, Mac OS X, Windows).

### Installation

#### Using pip (OS Independent)

1. Make sure you have
    `ffmpeg`, `ffprobe` (usually provided by the `ffmpeg` package),
    and `espeak` installed and available on your command line.
    You also need Python and its "developer" package
    containing the C headers (`python-dev` or similar).

2. Install `aeneas` system-wise with `pip`:
    
    ```bash
    $ sudo pip install numpy
    $ sudo pip install aeneas
    (Optional: $ sudo pip install pafy)
    ```

    **Note**: you must install `numpy` before `aeneas`,
    otherwise the setup process will fail.

    **Note**: you can install `aeneas` via `pip`
    in a virtual environment (e.g. created by `virtualenv`).

#### Linux

1. If you are a user of a `deb`-based Linux distribution
(e.g., Debian or Ubuntu),
you can install all the dependencies by downloading and running
[the provided install_dependencies.sh script](https://raw.githubusercontent.com/readbeyond/aeneas/master/install_dependencies.sh)

    ```bash
    $ wget https://raw.githubusercontent.com/readbeyond/aeneas/master/install_dependencies.sh
    $ sudo bash install_dependencies.sh
    ```

    If you have another Linux distribution,
    just make sure you have
    `ffmpeg`, `ffprobe` (usually provided by the `ffmpeg` package),
    and `espeak` installed and available on your command line.
    You also need Python and its "developer" package
    containing the C headers (`python-dev` or similar).

2. Clone the `aeneas` repo, install Python dependencies, and compile C extensions:

    ```bash
    $ git clone https://github.com/ReadBeyond/aeneas.git
    $ cd aeneas
    $ sudo pip install -r requirements.txt
    (Optional: $ sudo pip install pafy)
    $ python setup.py build_ext --inplace
    $ python aeneas_check_setup.py
    ```

    If the last command prints a success message,
    you have all the required dependencies installed
    and you can confidently run **aeneas** in production.

3. In alternative to the previous point, you can install `aeneas` system-wise with `pip`:
    
    ```bash
    $ sudo pip install numpy
    $ sudo pip install aeneas
    (Optional: $ sudo pip install pafy)
    ```

#### Windows

Please follow the installation instructions
contained in the
["Using aeneas for Audio-Text Synchronization" PDF](http://software.sil.org/scriptureappbuilder/resources/),
based on
[these directions](https://groups.google.com/d/msg/aeneas-forced-alignment/p9cb1FA0X0I/8phzUgIqBAAJ),
written by Richard Margetts.

Please note that on Windows it is recommended to run **aeneas**
with Python 2.7, since compiling the C extensions on Python 3.4 or 3.5
requires [a complex setup process](http://stackoverflow.com/questions/29909330/microsoft-visual-c-compiler-for-python-3-4).

#### Mac OS X

Feel free to jump to step 9 if you already have
`python`, `ffmpeg`/`ffprobe` and `espeak` installed.

1. Install the Xcode command line tools:

    ```bash
    $ xcode-select --install
    ```

    Follow the instructions appearing on screen.

2. Install the `brew` packet manager:

    ```bash
    $ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    ```

3. Update `brew`:

    ```bash
    $ brew update
    ```

4. Install `espeak` and `ffmpeg` (which also provides `ffprobe`)  via `brew`:

    ```bash
    $ brew install espeak
    $ brew install ffmpeg
    ```

5. Install Python:

    ```bash
    $ brew install python
    ```

6. Replace the default (Apple's) Python distribution with the Python installed by `brew`,
   by adding the following line at the end of your `~/.bash_profile`:

    ```bash
    export PATH=/usr/local/bin:/usr/local/sbin:~/bin:$PATH
    ```

7. Open a new terminal window. (This step is IMPORTANT!
   If you do not, you will still use Apple's Python,
   and everything in the Universe will go wrong!)

8. Check that you are running the new `python`:

    ```bash
    $ which python
    /usr/local/bin/python
    
    $ python --version
    Python 2.7.10 (or later)
    ```

9. Clone the `aeneas` repo, install Python dependencies, and compile C extensions:

    ```bash
    $ git clone https://github.com/ReadBeyond/aeneas.git
    $ cd aeneas
    $ sudo pip install -r requirements.txt
    (Optional: $ sudo pip install pafy)
    $ python setup.py build_ext --inplace
    $ python aeneas_check_setup.py
    ```

    If the last command prints a success message,
    you have all the required dependencies installed
    and you can confidently run **aeneas** in production.

10. In alternative to the previous point, you can install `aeneas` system-wise with `pip`:
    
    ```bash
    $ sudo pip install numpy
    $ sudo pip install aeneas
    (Optional: $ sudo pip install pafy)
    ```

## Usage

1. Install `aeneas` as described above. (Only the first time!)

2. Open a command prompt/shell/terminal and go to the root directory
   of the aeneas repository, that is, the one containing the `README.md` and `VERSION` files.
   (This step is not needed if you installed `aeneas` with `pip`,
   since you will have the `aeneas` module available system-wise.)

3. To compute a synchronization map `map.json` for a pair
   (`audio.mp3`, `text.txt` in `plain` text format), you can run:

    ```bash
    $ python -m aeneas.tools.execute_task audio.mp3 text.txt "task_language=en|os_task_file_format=json|is_text_type=plain" map.json
    ```

   To compute a synchronization map `map.smil` for a pair
   (`audio.mp3`, `page.xhtml` containing fragments marked by `id` attributes like `f001`),
   you can run:

    ```bash
    $ python -m aeneas.tools.execute_task audio.mp3 page.xhtml "task_language=en|os_task_file_format=smil|os_task_file_smil_audio_ref=audio.mp3|os_task_file_smil_page_ref=page.xhtml|is_text_type=unparsed|is_text_unparsed_id_regex=f[0-9]+|is_text_unparsed_id_sort=numeric" map.smil
    ```

   The third parameter (the _configuration string_) can specify several other parameters/options.
   See the [documentation](http://www.readbeyond.it/aeneas/docs/)
   or use the `-h` switch for details.

4. If you have several tasks to run,
   you can create a job container and a configuration file,
   and run them all at once:

    ```bash
    $ python -m aeneas.tools.execute_job job.zip /tmp/
    ```
    
   File `job.zip` should contain a `config.txt` or `config.xml`
   configuration file, providing **aeneas**
   with all the information needed to parse the input assets
   and format the output sync map files.
   See the [documentation](http://www.readbeyond.it/aeneas/docs/)
   or use the `-h` switch for details.

5. You might want to run `execute_task` or `execute_job`
   with `-h` to get an usage message and some examples:

    ```bash
    $ python -m aeneas.tools.execute_task -h
    $ python -m aeneas.tools.execute_job -h
    ```

   See the [documentation](http://www.readbeyond.it/aeneas/docs/)
   for an introduction to the concepts of `task` and  `job`,
   and for the list of all the available options.


## Documentation

Online: [http://www.readbeyond.it/aeneas/docs/](http://www.readbeyond.it/aeneas/docs/)

Generated from the source files (it requires `sphinx`):

```bash
$ git clone https://github.com/readbeyond/aeneas.git
$ cd aeneas/docs
$ make html
```

Tutorial: [A Practical Introduction To The aeneas Package](http://www.albertopettarin.it/blog/2015/05/21/a-practical-introduction-to-the-aeneas-package.html)

Mailing list: [https://groups.google.com/d/forum/aeneas-forced-alignment](https://groups.google.com/d/forum/aeneas-forced-alignment)

Changelog: [http://www.readbeyond.it/aeneas/docs/changelog.html](http://www.readbeyond.it/aeneas/docs/changelog.html)


## Supported Features

* Input text files in plain, parsed, subtitles, or unparsed format
* Text extraction from XML (e.g., XHTML) files using `id` and `class` attributes
* Arbitrary text fragment granularity (single word, subphrase, phrase, paragraph, etc.)
* Input audio file formats: all those supported by `ffmpeg`
* Possibility of downloading the audio file from a YouTube video
* Batch processing
* Output sync map formats: CSV, JSON, RBSE, SMIL, SSV, TSV, TTML, TXT, VTT, XML
* Tested languages: BG, CA, CY, CS, DA, DE, EL, EN, EO, ES, ET, FA, FI, FR, GA, GRC, HR, HU, IS, IT, LA, LT, LV, NL, NO, RO, RU, PL, PT, SK, SR, SV, SW, TR, UK
* Robust against misspelled/mispronounced words, local rearrangements of words, background noise/sporadic spikes
* Code suitable for a Web app deployment (e.g., on-demand AWS instances)
* Adjustable splitting times, including a max character/second constraint for CC applications
* Automated detection of audio head/tail
* MFCC and DTW computed via Python C extensions to reduce the processing time
* On Linux, `espeak` called via a Python C extension for faster audio synthesis
* Output an HTML file (from `finetuneas` project) for fine tuning the sync map manually


## Limitations and Missing Features 

* Audio should match the text: large portions of spurious text or audio might produce a wrong sync map
* Audio is assumed to be spoken: not suitable/YMMV for song captioning
* No protection against memory trashing if you feed extremely long audio files
* On Mac OS X and Windows, audio synthesis might be slow if you have thousands of text fragments


## TODO List

* Improving robustness against music in background
* Isolating non-speech intervals (music, prolonged silence)
* Automated text fragmentation based on audio analysis
* Auto-tuning DTW parameters
* Reporting the alignment score
* Improving (removing?) dependency from `espeak`, `ffmpeg`, `ffprobe` executables
* Multilevel sync map granularity (e.g., multilevel SMIL output)
* Better documentation
* Testing other approaches, like GMM/HMM/NN (e.g., using HTK or Kaldi)
* Publishing the package on Debian repo

Would you like to see one of the above points done?
Consider [sponsoring](#supporting) this project!


## How Does This Thing Work?

### One Word Explanation

Math.

### One Sentence Explanation (Layman Edition)

A good deal of math and computer science,
a handful of software engineering and
some optimization tricks.

### One Sentence Explanation (Pro Edition)

Using the Sakoe-Chiba Band Dynamic Time Warping (DTW) algorithm
to align the Mel-frequency cepstral coefficients (MFCCs)
representation of the given (real) audio wave and
the audio wave obtained by synthesizing the text fragments
with a TTS engine, eventually mapping
the computed alignment back onto the (real) time domain.

### Extended Explanation

To be written. Eventually. Some day.


## License

**aeneas** is released under the terms of the
GNU Affero General Public License Version 3.
See the [LICENSE](https://raw.githubusercontent.com/readbeyond/aeneas/master/LICENSE) file for details.

The pure Python code for computing the MFCCs
`aeneas/mfcc.py`
is a modified version from the
[CMU Sphinx3 project](http://cmusphinx.sourceforge.net/).
See [licenses/sphinx3.txt](https://raw.githubusercontent.com/readbeyond/aeneas/master/licenses/sphinx3.txt) for details.

The pure Python code for reading and writing WAVE files 
`aeneas/wavfile.py`
is a verbatim copy from the
[scipy project](https://github.com/scipy/scipy/),
included here to avoid installing the whole `scipy` package.
See [licenses/scipy.txt](https://raw.githubusercontent.com/readbeyond/aeneas/master/licenses/scipy.txt) for details.

The C header `speak_lib.h` for the Python C extension `cew`
is a verbatim copy from the
[espeak project](http://espeak.sourceforge.net/).
See [licenses/eSpeak.txt](https://raw.githubusercontent.com/readbeyond/aeneas/master/licenses/eSpeak.txt) for details.

The HTML file `aeneas/res/finetuneas.html`
is a verbatim copy from the
[finetuneas project](https://github.com/ozdefir/finetuneas),
courtesy of Firat Ozdemir.
See [licenses/finetuneas.txt](https://raw.githubusercontent.com/readbeyond/aeneas/master/licenses/finetuneas.txt) for details.

Audio files contained in the unit tests `aeneas/tests/res/` directory
are adapted from recordings produced by
the [LibriVox Project](http://www.librivox.org)
and they are in the public domain.
See [licenses/LibriVox.txt](https://raw.githubusercontent.com/readbeyond/aeneas/master/licenses/LibriVox.txt) for details.

Text files contained in the unit tests `aeneas/tests/res/` directory
are adapted from files produced by
the [Project Gutenberg](http://www.gutenberg.org)
and they are in the public domain.
See [licenses/ProjectGutenberg.txt](https://raw.githubusercontent.com/readbeyond/aeneas/master/licenses/ProjectGutenberg.txt) for details.

No copy rights were harmed in the making of this project.


## Supporting and Contributing

### Sponsors 

* **July 2015**: [Michele Gianella](https://plus.google.com/+michelegianella/about) generously supported the development of the boundary adjustment code (v1.0.4)

* **August 2015**: [Michele Gianella](https://plus.google.com/+michelegianella/about) partially sponsored the port of the MFCC/DTW code to C (v1.1.0)

* **September 2015**: friends in West Africa partially sponsored the development of the head/tail detection code (v1.2.0)

* **October 2015**: an anonymous donation sponsored the development of the "YouTube downloader" option (v1.3.0)

### Supporting

Would you like supporting the development of **aeneas**?

I accept sponsorships to

* fix bugs,
* add new features,
* improve the quality and the performance of the code,
* port the code to other languages/platforms,
* support of third party installations, and
* improve the documentation.

In case, feel free to [get in touch](mailto:aeneas@readbeyond.it).

### Contributing

If you think you found a bug,
please use the
[GitHub issue tracker](https://github.com/readbeyond/aeneas/issues)
to file a bug report.

If you are able to contribute code directly, that is awesome!
I will be glad to merge it!

Just a few rules, to make life easier for both you and me:

1. Please do not work on the `master` branch.
   Instead, create a new branch on your GitHub repo
   by cheking out the `devel` branch.
   Open a pull request from your branch on your repo
   to the `devel` branch on this GitHub repo.

2. Please make your code consistent with
   the existing code base style
   (see the
   [Google Python Style Guide](https://google-styleguide.googlecode.com/svn/trunk/pyguide.html)
   ), and test your contributed code
   against the unit tests
   before opening the pull request.

3. Ideally, add some unit tests for the code you are submitting,
   either adding them to the existing unit tests or creating a new file
   in `aeneas/tests/`.

4. **Please note that, by opening a pull request,
   you automatically agree to apply
   the AGPL v3 license
   to the code you contribute.**

## Development History

**Early 2012**: Nicola Montecchio and Alberto Pettarin
co-developed an initial experimental package
to align audio and text, intended to be run locally
to compute Media Overlay (SMIL) files for
EPUB 3 Audio-eBooks

**Late 2012-June 2013**: Alberto Pettarin
continued engineering and tuning the alignment tool,
making it faster and memory efficient,
writing the I/O functions for batch processing
of multiple audio/text pairs,
and started producing the first EPUB 3 Audio-eBooks
with Media Overlays (SMIL files) computed automatically
by this package

**July 2013**: incorporation of ReadBeyond Srl

**July 2013-March 2014**: development of ReadBeyond Sync,
a SaaS version of this package,
exposing the alignment function via APIs
and a Web application

**March 2014**: launch of ReadBeyond Sync beta

**April 2015**: ReadBeyond Sync beta ended

**May 2015**: release of this package on GitHub

**August 2015**: release of v1.1.0, including Python C extensions
to speed the computation of audio/text alignment up

**September 2015**: release of v1.2.0,
including code to automatically detect the audio head/tail

**October 2015**: release of v1.3.0,
including calling espeak via its C API (on Linux)
for faster audio synthesis, and the possibility
of downloading audio from YouTube

**November 2015**: release of v1.3.2,
for the first time available
also on [PyPI](https://pypi.python.org/pypi/aeneas/)

**January 2016**: release of v1.4.0,
supporting both Python 2.7 and 3.4 or later

## Acknowledgments

Many thanks to **Nicola Montecchio**,
who suggested using MFCCs and DTW,
and co-developed the first experimental code
for aligning audio and text.

**Paolo Bertasi**, who developed the
APIs and Web application for ReadBeyond Sync,
helped shaping the structure of this package
for its asynchronous usage.

**Chris Hubbard** prepared the files for
packaging aeneas as a Debian/Ubuntu `.deb`.

All the mighty [GitHub contributors](https://github.com/readbeyond/aeneas/graphs/contributors),
and the members of the [Google Group](https://groups.google.com/d/forum/aeneas-forced-alignment).

