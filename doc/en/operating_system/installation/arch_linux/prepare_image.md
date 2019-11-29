# Get iso
Before you are going to read my guide, I want you to know that any downloable Linux distributios are fully secure.

## Arch Linux
* `# pacman -S gnupg dirmngr sha1sum awk xclip` Install [GnuPG](https://en.wikipedia.org/wiki/GNU_Privacy_Guard), dirmngr, sha1sum, awk and xclip.
* Generate [entropy](https://en.wikipedia.org/wiki/Entropy_(computing)).
* Go to Arch Linux [download](https://www.archlinux.org/download/) page.
    + Download the *.iso* from a HTTP mirror from a [free country](https://en.wikipedia.org/wiki/Internet_censorship_and_surveillance_by_country).
    + Checksums
        + Download the official *PGP signature*.
        + Copy the *SHA1* text.
        + `# xclip -selection c -o > sha1sum.txt` Paste the text into a file named *sha1sum.txt*.
* `# pacman-key -v archlinux-20??.??.??-x86_64.iso.sig` Verify the PGP signature.
* `# sha1sum archlinux-20??.??.??-x86_64.iso | awk '{print $1}' > diso_sha1sum.txt`
* `# diff -s sha1sum.txt diso_sha1sum.txt` Compare if the keys are the same

## GNU Debian based Linux distrubution
* `# apt-get install gnupg dirmngr sha1sum awk xclip` Install [GnuPG](https://en.wikipedia.org/wiki/GNU_Privacy_Guard), dirmngr, sha1sum awk and xclip.
* Generate [entropy](https://en.wikipedia.org/wiki/Entropy_(computing)).
* Go to Arch Linux [download](https://www.archlinux.org/download/) page.
    + Download the *.iso* from a HTTP mirror from a [free country](https://en.wikipedia.org/wiki/Internet_censorship_and_surveillance_by_country).
    + Checksums
        + Download the official *PGP signature*.
        + Copy the *SHA1* text.
        + `# xclip -selection c -o > sha1sum.txt` Paste the text into a file named *sha1sum.txt*.
* Import the *Owner's Signing Key* from [here](https://www.archlinux.org/master-keys/)
    + `# gpg --receive-keys 9741E8AC` Example to import [Pierre Schmitz](https://www.archlinux.org/people/developers/#pierre) key (2019-02-11)
* `# gpg --keyserver pgp.mit.edu --keyserver-options auto-key-retrieve --verify archlinux-20??.??.??-x86_64.iso.sig` Verify the PGP signature.
* `# sha1sum archlinux-20??.??.??-x86_64.iso | awk '{print $1}' > diso_sha1sum.txt`
* `# diff -s sha1sum.txt diso_sha1sum.txt` Compare the keys, they must be identical.

# BIOS and UEFI bootable USB
## All based Linux distrubution
* `dd bs=4M if=$(ls archlinux-20??.??.??-x86_64.iso) of=/dev/sdx status=progress oflag=sync` *sdx* is the path of the USB.

