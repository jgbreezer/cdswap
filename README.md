# cdswap
Switch current path for one where given directory/subdirectories match, keeping as much of existing path as possible

# Usage
Usage: cdswap new_dir

Hunt for directory new_dir in all parent directories and change to the directory as though the current cwd had been swapped for one where the specified directory is used instead of the current one at that level, or the specified directory if the full subtree below it does not exist (as far down as possible to match the current level below it).

The easiest use is by defining a shell function:                                                                                                                                             
        function cdswap () { cd $($(which cdswap) $1); }                                                                                                                                     

For instance, given the directory tree with the following paths (and no more):
  /home/user/trunk/path/to/directory1/
  /home/user/trunk/path/to/directory1/subdir
  /home/user/branch/path/to/directory1/subdir
  /home/user/branch2/path/to

If your current directory (given by pwd) is /home/user/trunk/path/to/directory1/, using `cdswap branch` will find the 'branch' parent directory and change directory to /home/user/branch/path/to/directory1, keeping you in the equivalent "directory1" directory.
If you then `cdswap branch2`, it will find the branch2 directory but only keep the path/to subdirectory as 'directory1' does not exist (it keeps as much as possible from the original path, calling it a match for your directory parameter so no error); you'll be left in /home/user/branch2/path/to .
If you then `cdswap path/to/directory1` from within 'branch2', it will fail to find a valid directory as there is no other 'path' directory further back and the existing 'path' directory you are in does not contain the 'to/directory1' directory. You will remain in the 'branch2/path/to' directory.
If you then use `cdswap trunk/path/to/directory1` you'll be back in the starting directory, '/home/user/trunk/path/to/directory1/'.

I hope that helps explain its operation.
