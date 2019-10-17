# Clipboard
# Install
`# pacman -S xclip` Install *xclip* software  

## Usage
`# cat <filename> | xclip` Copy the file content to clipboard  
`# xclip -o` Paste the text  
`# #cat <filename> | xclip` Make the first command a comment and copy it to clipboard  
`# cat <filename> | xclip -selection clipboard` Copy the output somewhere else other than a 'X' application  

## Tips
`# erho 'alias "c=xclip"' >> ~/.bashrc` Set alias for copy  
`# erho 'alias "v=xclip -o"' >> ~/.bashrc` Set alias for paste  
`# erho 'alias "cs=xclip -selection clipboard"' >> ~/.bashrc` Set alias for copy into somewhere else other than a 'X' application  
`# erho 'alias "vs=xclip -o -selection clipboard"' >> ~/.bashrc` Set alias for paste into somewhere else other than a 'X' application  

## Examples
* I want to open my current path from my current virtual console (tty1) in a new virtual console (tty2)
    + `# pwd | c` Copy the current directory path from tty1
    + `cd $(v)` Change directory into the copied path in tty2. The *$(v)* can be replaced with *\`v\`*

More info about the xclip buffer [here](https://unix.stackexchange.com/questions/139191/whats-the-difference-between-primary-selection-and-clipboard-buffer)