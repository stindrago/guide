# Custom Keymap Configuration

* `# mkdir -p /usr/local/share/kbd/keymaps` Create a local keymap directory
* `# cp /usr/share/kbd/keymaps/i386/dvorak/dvorak-programmer.map.gz /usr/local/share/kbd/keymaps` Copy a default layout to start with
* `# gunzip /usr/local/share/kbd/keymaps/dvorak-programmer.map.gz` Extract the file
* `# showkey` See the keys code
* `# emacs /usr/local/share/kbd/keymaps/dvorak-programmer.map` Edit the file as you want
    + or `# wget <URL:csd_keymap.map> -O /usr/local/share/kbd/keymaps/csd_keymap.map` Import my keymap directly
* `# loadkeys /usr/local/share/kbd/keymaps/csd_keymap.map` Load the keys for the current session
* `# echo "KEYMAP=/usr/local/share/kbd/keymaps/csd_keymap.map" > /etc/vconsole.conf` Load the keys permanently