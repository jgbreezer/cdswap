# cdswap
Switch current path for one where given directory/subdirectories match, keeping as much of existing path as possible

# Usage
Usage: cdswap [-p] new_dir

Hunt for directory new_dir in all parent directories and change to the directory as though the current cwd had been swapped for one where the specified directory is used instead of the current one at that level, or the specified directory if the full subtree below it does not exist (as far down as possible to match the current level below it).
Option: -p = use pushd instead of cd

The easiest use is by defining a shell function (an explicit path would do instead of using 'command cdswap' too, if its not in a directory in your PATH):

        cdswap () {
	    [ "$1" == -p ] && {
		cmd=pushd;
		shift
	    } || cmd=cd;
	    builtin $cmd $(command cdswap $1 || echo -n . )
	}

Although I prefer to define an alias cdsw=cdswap for a little less typing.

For instance, given the directory tree with the following paths (and no more):
  /home/user/trunk/path/to/directory1/
  /home/user/trunk/path/to/directory1/subdir
  /home/user/branch/path/to/directory1/subdir
  /home/user/branch2/path/to
  /home/user/branch3/extra/subdir/path/to

If your current directory (given by pwd) is /home/user/trunk/path/to/directory1/, using `cdswap branch` will find the 'branch' directory at the same level as the 'trunk' parent, and change directory to /home/user/branch/path/to/directory1, keeping you in the equivalent "directory1" directory.
If you then `cdswap branch2`, it will find the branch2 directory but only keep the path/to subdirectory as 'directory1' does not exist (it keeps as much as possible from the original path, calling it a match for your directory parameter so no error); you'll be left in /home/user/branch2/path/to .
If you then `cdswap path/to/directory1` from within 'branch2', it will fail to find a valid directory as there is no other 'path' directory further back and the existing 'path' directory you are in does not contain the 'to/directory1' directory. You will remain in the 'branch2/path/to' directory.
If you then use `cdswap trunk/path/to/directory1` you'll be back in the starting directory, '/home/user/trunk/path/to/directory1/'.

If you're in /home/user/branch2/path/to and do `cdswap branch3/extra/subdir` you should end up in /home/user/branch3/extra/subdir/path/to, as it will also check if a match exists for multiple directories to replace the same directory the new parameter directory starts in (because branch3 matches at the same level as branch2, and replacing just "branch2" by the new path given, keeping the /path/to on the end, will also match - though this is lower priority match than a 1-dir-component-to-1-dir-component match)

I recommend you *don't* replace your normal cd command with this, it could be dangerously abused in scripts where directory path changes are allowed by 3rd parties but supposed to be carefully limited [due to ability to switch to whole other directory trees or much further towards root of filesystem]. Note also: it only looks at its first command-line argument (2nd when -p used).

It can be made more cautious; it currently overrides default False parameter for match_incomplete_subtree() in the script in the '\_\_main\_\_' code, change that back to False/remove the override to match more tightly.

I hope that helps explain its operation. I also hope this readme is still correct, because I've tweaked it and added use-cases since I first wrote it.
