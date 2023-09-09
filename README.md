## CTF Challenges for GRCon23
The GRC files used to create and solve the challenges are included in this repo.

### Numbers Mixup
The flags are all in voice tracks, read using the phonetic alphabet. Flags 1 and 2 were combined into the L and R channels of a wav file. This can also be thought of as I/Q data. The left (I) channel is `Flag1+Flag2` and the right (Q) channel is `Flag1-Flag2`.

The intended solution for these flags was to add/subtract the two channels. It turned out that this also worked out as audio center and stereo wide right, so a set of stereo headphones or speakers was sufficient.

Flags 3 and 4 are a little harder. Two additional mono channels representing `Flag3+(Flag1+Flag2)` and `Flag4+(Flag1-Flag2)`were frequency shifted. The intended solution was to shift them back to center and subtract off the interfering sounds.

See ctf_mix.grc and ctf_unmix.grc.

### Digital and Voice
I thought it would be cool to "scramble" a voice signal using a valid digital signal. In the process, the digital signal might as well have its own flag, so these were two different, almost unrelated, challenges in one.

The digital signal was created using fldigi in MFSK64 mode, then shifted down 12kHz. [This](https://www.sigidwiki.com/wiki/Multi_Frequency_Shift_Keying_(MFSK)) page shows various MFSK modes. The spectrogram for MFSK64 should look similar and the audio should sound similar. The source audio file is C2_Flag1_MFSK64.wav.

I used fldigi to recover the text from the MFSK64 signal after shifting it back to 0Hz and outputting a wav file. The most elegant way to use fldigi with various audio sources is to set up Pulseaudio routing. That can be a little difficult, but fortunately, there is File/Audio/Playback, which will play directly from a wav file.

The MFSK64 signal was then converted into a complex, one-sided signal using a Hilbert transform. This makes it into a frequency "key" used to "scramble" Flag2, which is first turned into a double-sided complex signal. Multiplying the two signals together frequency modulates Flag2. The result is shifted up 12kHz.

The intended solution was to shift both signals back to zero, multiply the signal by the conjugate of the "key", and then listen to the result. There are a bunch of valid ways to do this. Even if the resulting signal did not end up at 0Hz, it could be loaded into a program such as Gqrx and demodulated at an offset. Here, I am just adding the two sides of the signal together to recover the audio. Demod as single sideband (LSB or USB) also would have worked.
