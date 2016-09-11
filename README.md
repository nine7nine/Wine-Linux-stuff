# Wine-Linux-stuff

this repo contains a few tools that I use for managing Wine on Linux.

# Wine-Node:

This tool is for creating psuedo-prefixes in Wine. The idea is to have a single 'master-prefix' or master-node that is your real wine-prefix. It's children or 'wine-nodes' are essentially minimal copies of the master-node, the rest is symlinked. This allows you to have muliplte templated prefixes; where you don't need to re-install ms runtimes and your apps, as they use that data from the master-node, while copying only needed files in order to keep them unique/separate/sandboxed. It can be thought of as a psuedo/multi-user environment or wine-prefix templating/sharing... Some advanges are; 

* multiple prefixes that share some core data (reducing prefix sizes, significantly).
* helpful for when you have massive sound libraries, app data or preset databases. (no need to copy/install to a new prefix).
* you can run multiple 'singleton apps'. ie: apps that try and hold a mutex/lock or disallow running multiple instances
* you can manage installations from a single prefix
* the wine-nodes can be 'throw away', templates or a testing ground, in some cases. 
* you can tie different nodes to different wine-builds (like a build using different patchset/features) and/or loader.
* you can (and even should!) avoid using master-node/main wine-prefix, except for software installations and/or making 
  global changes && app changes that you want each node to be affected by. (which will require deleting the nodes and re-running the 'wine-nodes-create' script).  

IMPORTANT NOTE: These scripts will not work out-of-the-box, as they are setup for my own wine-container (a folder that contains the master-node and it's wine-nodes).  Some of the folders that it creates or symlinks wouldn't exist in a typical wine-prefix, or your system. However, it does show how it is done; the script is commented and is easy to edit. These scripts are setup for managing my Native Instruments installations and other proaudio apps, across wine-nodes...

That being said, for minimal setup and to get an idea of how it works;

1. create a directory in your home directory called 'winebox'; /home/username/winebox
2. create /home/username/winebox/bin and fill it with the scripts from this repo.
3. add /home/username/winebox/bin to your PATH in .bashrc (ie: export PATH=/home/username/winebox/bin:$PATH)
4. from a terminal, execute: $ source ~/.bashrc
5. change some of the environment variable PATHs in mastertricks && wine-node-config/create shell scripts:

CONTAINER="/home/username/winebox"

USER="username"

Next,

1. Create a wine-prefix in your wine-container, name it master-node; /home/username/winebox/master-node
2. from a terminal, execute: $ mastertricks (installs runtimes/dlls in your master-node)
3. After double-checking the scripts to make sure that you have all env/PATHs correct,
   execute; wine-node-config... You shoud now have a master-node and 4 nodes;

/home/username/winebox/master-node

/home/username/winebox/node1

/home/username/winebox/node2

/home/username/winebox/node3

/home/username/winebox/node4

(to make easy use of the nodes, I've included example wineloader wrappers; node*/master-node scripts. The PATHs will
also need to be adjusted, but you would use these instead of executing 'wine foo'. NOTE: My examples, have the wine-rt/staging variables enabled... you can just comment them out, if they aren't desired).

Each node will not share registry files, nor will they share apps from the master-node by default. To share, you 
must edit the wine-node-create script, under the [SPECIAL CASE] section. It's really just a matter of adding a variable with the relevant folder name. An example from my system;

PFILES5="REAPER (x64)"

ln -s "$CONTAINER"/"$MASTER_NODE"/"$X64"/"$PFILES5" /"$CONTAINER"/"$WINE_NODE"/"$X64"/"$PFILES5"

translates to: '/home/ninez/winebox/master-node/drive_c/Program Files/REAPER (x64)'... This creates symlinks in each node, linking back to the master-node application folder.The environment naming scheme may make you wonder; "why not just put the full paths?". Well, it is possible to have more than one container, to rename the nodes/master-node, etc. Additionally, I found this to be more flexible and easier to read; Especially, in text editors that display shell scripts in color!

# Wine-Node Scripts

* wine-node-create - configures a single wine-node in a given wine-container, using the master-node. The script MUST be read
  and modified to your own needs before using. It accounts for both x86 and x64 paths and typically reproduces only the
  necessay folders required in each wine-node. however, the [SPECIAL CASE] chunk of the script is meant for fine-tuning how
  some important app-specifc data may need to be handled. [read the script for more info]

* wine-node-config - this script is to actually execute wine-node-create.  you specify how many nodes to create, like so;

env NODE=1 "$CONTAINER"/bin/wine-node-create

env NODE=2 "$CONTAINER"/bin/wine-node-create

env NODE=3 "$CONTAINER"/bin/wine-node-create

env NODE=4 "$CONTAINER"/bin/wine-node-create

This would create 4 wine-nodes, which would use about 100mb per node [on average].

# Mastertricks 

A winetricks wrapper for Wine-Node's master-node, automating the installation of commonly needed dlls and runtimes 
for proaudio apps and other programs. 

# gr5updb 

A migration tool that I use to manage GuitarRig5 Presets. When wine isn't running, I can use this script to push
all new gr5 presets from wine-nodes back to the master-node. Then, I run gr5 frm the master-node to pick up any changes in
it's database. This way, when I update my master-node and re-run the 'wine-node-config' script, I can keep all of those
changes and migrate them to each node.

# Mutex Tests

2 windows executables for testing mutex performance and synchronisation in Windows/Wine.

* rapid-mutex.exe - mutex test for windows/wine.

* five-mutex.exe - The 'Dining/hungry Philosopher Problem' test for windows/wine.... 

ref: https://en.wikipedia.org/wiki/Dining_philosophers_problem
