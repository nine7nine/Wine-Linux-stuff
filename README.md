# Wine-Linux-stuff

this repo contains a few tools that I use for managing Wine on Linux.

# Wine-Node:

This tool is for creating psuedo-prefixes in Wine. The idea is to have a single 'master-prefix' or master-node that is your real wine-prefix. It's children or 'wine-nodes' are essentially minimal copies of the master-node, the rest is symlinked. This allows you to have muliplte 'throw away' prefixes; where you don't need to re-install ms runtimes and your apps, as they use that from the master-node, while copying other needed files in order to keep them unique/separate. Some other advanges are; 

* multiple prefixes that share some core data
* you can run multiple 'singleton apps'. ie: apps that try and hold a mutex or disallow running multiple instances
* you can manage installations from a single prefix
* the wine-nodes can be 'throw away' or a testing ground, in some cases. 
* you can tie different nodes to different wine-builds (like a build using different patchsets), winelib dlls and/or loader.

these scripts will not work out-of-the-box, as they are setup for my own wine-container (a folder that contains the master-node and it's wine-nodes). So some folders that it creates or symlinks to wouldn't exist in a typical wine-prefix. however, it does show how it is done, the script is commented and is easy to edit. This scripts are setup for managing my Native Instruments installations and other proaudio apps, across wine-nodes...

* wine-node-create - configures a single wine-node in a given wine-container, using the master-node. The script MUST be read and modified to your own needs before using. It accounts for both x86 and x64 paths and typically reproduces only the necessay folders required in each wine-node. however, the [SPECIAL CASE] chunk of the script is meant for fine-tuning how some important app-specifc data may need to be handled. [read the script for more info]

* wine-node-config - this script is to actually execute wine-node-create.  you specify how many nodes to create in the script, like so;

env NODE=1 "$CONTAINER"/bin/wine-node-create

env NODE=2 "$CONTAINER"/bin/wine-node-create

env NODE=3 "$CONTAINER"/bin/wine-node-create

env NODE=4 "$CONTAINER"/bin/wine-node-create

This would create 4 wine-nodes, which would use about 100mb per node [on average].

* gr5updb - A migration tool that I use to manage GuitarRig5 Presets. When wine isn't running, I can use this script to push all new gr5 presets from wine-nodes back to the master-node. Then, I run gr5 frm the master-node to pick up any changes in it's database. This way, when I update my master-ndoe and re-run the 'wine-node-config' script, I can keep all of those changes and migrate them to each node.

* rapid-mutex.exe - mutex test for windows/wine.

* five-mutex.exe - The 'Dining/hungry Philosopher Problem' test for windows/wine.... 

ref: https://en.wikipedia.org/wiki/Dining_philosophers_problem
