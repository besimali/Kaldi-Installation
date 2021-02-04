# Kaldi-Installation

1. Install Kaldi
	- In desired installation folder (e.g. /opt) 
        ```bash
        git clone https://github.com/kaldi-asr/kaldi.git kaldi --origin upstream
	    cd kaldi
	    ```
    - follow install instructions in INSTALL:
        1. go to tools/ and follow INSTALL instructions there.
            ```bash
            extras/check_dependencies.sh
            make
            ```
        2. go to src/ and follow INSTALL instructions there.
            ```bash
            ./configure --shared
            make depend -j 4
            make -j 4
            ```
            (if this fails (out of memory) run without "-j 4" option or with "-j 2" instead, or give the
            vm more memory)
2. Install some dependencies
    ```bash
    sudo apt-get install build-essential pkg-config python3-pip python3-dev python3-setuptools python3-wheel ffmpeg sox libatlas-base-dev libatlas3-base portaudio19-dev libasound-dev
    ```
3. Fix Library Paths
    - ***pkg-config***
        - add a file called kaldi-asr.pc to /usr/local/lib/pkgconfig
        with content :
            ```bash
            kaldi_root=/opt/kaldi
            Name: kaldi-asr
            Description: kaldi-asr speech recognition toolkit
            Version: 5.5
            Requires: lapack-atlas blas-atlas
            Libs: -L${kaldi_root}/tools/openfst/lib -L${kaldi_root}/src/lib -lkaldi-decoder -lkaldi-lat -lkaldi-fstext -lkaldi-hmm -lkaldi-feat -lkaldi-transform -lkaldi-gmm -lkaldi-tree -lkaldi-util -lkaldi-matrix -lkaldi-base -lkaldi-nnet3 -lkaldi-online2 -lkaldi-cudamatrix -lkaldi-ivector -lfst
            Cflags: -I${kaldi_root}/src -I${kaldi_root}/tools/openfst/include
            ```
            (change kaldi_root to wherever it is installed in step1)
    - ***ld***
        - add a file to /etc/ld.so.conf.d called kaldi.conf with a line containing the folders where
            kaldi tools and kaldi src libraries are stored, eg:
            /opt/kaldi/src/lib
            /opt/kaldi/tools/openfst/lib
        - if directory doesn't exist, instead add these line to /etc/ld.so.conf
        - then run
            ```bash
            sudo ldconfig
            ```
4. Create a symbolic link because one of the pip packages expect atlas in this location: 
ln -s /usr/include/x86_64-linux-gnu/atlas /usr/include/atlas

5. Install python dependencies (best under a python3 virtual env):
```bash
pip install wheel numpy nltk cython
pip install py-kaldi-asr py-nltools
```
6. If you encounter an error about the atlas package, do this:

	- create a file named atlas.pc under /usr/lib/pkgconfig
	-write following data inside(paths may differ on your system - check if the path exists):
    ```bash
		Name: atlas
		Version: 3.10.3
		Description: Debug information for package atlas
		Cflags: -I/usr/include/x86_64-linux-gnu/atlas
		Libs: -L/usr/include/x86_64-linux-gnu/atlas
	```


# Running examples:

1. Decode microphone input in real time:
```bash
python3 kaldi_decode_live.py -m PATH_TO_MODEL
```

2. Decode a wav file:
```bash
python3 kaldi_decode_wav.py -m PATH_TO_MODEL input.wav
```
Change input.wav to whichever file you want to decode. If the file is not in the current folder, provide a full path to the file. THe wav file must be single chanel 16 bit.


# Further training

TO adapt the model or train another one refer to Zamia-speech project.https://github.com/gooofy/zamia-speech
It is much easier than using plain Kaldi but it is still not beginner friendly. 
