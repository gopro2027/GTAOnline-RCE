# GTAOnline-RCE
#### Array Overflow Read/Write within Rage Script On PS3
On PS3, In 2019 I discovered this exploit within GTA V's multiplayer scripting code and wanted to document how it worked and provide the source code for it. This document will explain the process of how it is set up and executed with code snippets. The full code can be found within the source release of [Paradise SPRX](https://github.com/gopro2027/)

#### Overview
The exploit is not a full RCE, it provides read and write access to the normal memory area of the game. This may be a prereq to a full code execution in powerpc, but it can be used to inject code into the scripts of GTA V and that is what I used it for.
The way it functions is by modifying the array size of shared array structures used by [NETWORK::NETWORK_REGISTER_HOST_BROADCAST_VARIABLES](http://dev-c.com/nativedb/func/info/3e9b2f01c50df595). Arrays in Rage Script are of size `1 + array_size` in ints where the first index is the size of the array. An oversight with `NETWORK_REGISTER_HOST_BROADCAST_VARIABLES` will sync the array size of synced arrays with the clients, allowing you to set the size of certain arrays to INT_MAX on your system and other client's systems. Combining this with carefully treated [SCRIPT::TRIGGER_SCRIPT_EVENT](http://dev-c.com/nativedb/func/info/5ae99c571d5bbe5d) functions, you can access out of bounds areas on a remote client's system with the now large array size.

#### Steps
1. Request and gain host control of the `freemode` script so you are script host
2. Modify the size of the exploitable array to `INT_MAX` so the clients array is now modified in size
3. Pass host control back to the client you want to RCE on. This is important because writing of `NETWORK_REGISTER_HOST_BROADCAST_VARIABLES` data is only done while host
4. Use a trigger script event function to read a chunk of data out of bounds on the client's system using the exploited array
5. Locally search for that chunk of data on your own system to calculate the address of the exploited array on the client's system
6. **You now have read-write using exploited functions**, but the code is likely unstable
7. Write your own Rage Script code into their game to make it faster and more stable

I have included the rce.h file which includes nearly all of the code relating to this topic. It is a direct copy of the one from the [Paradise SPRX](https://github.com/gopro2027/) source code. All of the rest of the code you may need can be found there.

Thank you!

RIP GTA Online PS3
2013 - 2021

