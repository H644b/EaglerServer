# Prerequisites:
- some form of Linux server with terminal access
  - Note that raspberry pi os 64 bit does not have java 8 or 17, please use ubuntu or a 32 bit version
- some way to access this terminal (ssh, in person, serial cable)

# Step Zero: Installing Dependencies

You do need some programs installed to host a minecraft server (shocker). This is just a simple list of applications. you can install with `apt` on any debian/ubuntu based server

- `tmux` or an alternative
- java 17 or 8 depending on what version of minecraft your hosting (use `apt search openjdk` to find the install candidate)
- `nano` or any other text editor available

# Step One: File Structure

First you'll wanna create some for of structure for your server so it isn't a complete mess. This is done with the commands `cd` and `mkdir`

- `cd` is used to change directory (folder)
  - there are some special folder references you can make 
    - `~/` for your home directory
    - `.` for your current directory
    - `..` for the previous directory
- `mkdir` is used to make a directory

Some example syntax would be `mkdir eagler` to make a folder called eagler and then `cd eagler` to go into you new eagler folder. If you wanted to return home you could do `cd ~/`

Back to making the server, you'll probably want to make a folder structure of 

~/
├─ eagler/
│  ├─ server/
│  ├─ proxy/

# Step Two: Downloading Server Files

## Part One: Downloading the Server

Files Needed:
- a [1.8.8 compatible server preferably spigot based](https://papermc.io/downloads)
  - If you need 1.8.8 itself you should buy [flamepaper](https://builtbybit.com/resources/flamepaper-fix-performance-security.19660/) as it is regularly updated
  - If you want to use newer versions of minecraft you can use the [via* suite](https://ci.viaversion.com/view/ViaRewind/) of plugins for backwards compatibility

For this you'll want to use something like `wget` or `curl` to download your files onto the server. We will use wget for this example because of its simple syntax. 

Once you are in the `server` directory run the command `wget <link to server file>`. This will download the server file into the `server` directory. Next to run the server you'll need a startup script. I recommend using https://flags.sh because it has optimizations for servers. We however will just be making a simple startup script. 

Use `nano` or any other text editor to create a file called `start.sh` (`nano start.sh`)

In this file put the line:
`java -Xmx<RAM IN MB>M -jar <Server file>`
Then if you're using nano press ctrl + x to exit the file, make sure to save when prompted when exiting. 

## Part Two: plugins

For plugins like the [via* suite](https://ci.viaversion.com/view/ViaRewind/), [authme](https://ci.codemc.io/job/AuthMe/job/AuthMeReloaded/), [essentialsx](https://essentialsx.net/downloads.html) you can add them at this point. To do this make a folder called `plugins` and put the plugin files inside (its that easy!).

At this point your file structure should look like this (different names for files)
~/
├─ eagler/
│  ├─ server/
│  │  ├─ plugins/
│  │  │  ├─ plugin.jar
│  │  ├─ server.jar
│  │  ├─ start.sh
│  ├─ proxy/

# Step Three: Downloading the Bungee

## Part One: Waterfall

[Waterfall is a fork of bungee made by the PaperMC team](https://papermc.io/downloads) with many performance changes. I personally recommend using it over normal bungeecord. Once you've found your download link go to the `proxy` directory you made in part one and use wget to download the file. 

`wget <bungee link>`

Next create your start script like with the server. Use this for the startup script:

`java -Xmx512M -jar <proxy file>` (512mb is plenty for waterfall)

## Part Two: Download Eaglerxbungee

Now you probably want eaglerxbungee so you can (shocker) connect to your server. This is done by first making a plugins folder in the `proxy` directory. You should know how to do this with `mkdir`. Now you just download the [eaglerxbungee](https://sus.shhnowisnottheti.me/static/EaglerXBungee-1.0.9.jar) file from ayunami's website.

At this point your file structure should look like this:
~/
├─ eagler/
│  ├─ server/
│  │  ├─ plugins/
│  │  │  ├─ plugin.jar
│  │  ├─ server.jar
│  │  ├─ start.sh
│  ├─ proxy/
│  │  ├─ plugins/
│  │  │  ├─ eaglerxbungee.jar
│  │  ├─ waterfall.jar
│  │  ├─ start.sh

# Step Four: Config files

## Part One: Generating the Files

So by now you have made downloaded all of the files you need. Now you need to make and edit the config files. To do this you simply run the server and proxy once and it does all the magic for you (kinda). Starting with the server go to `eagler/server` and run `./start.sh` until the server stops doing stuff. Then run the `stop` command to close the server, you will see a ton of new files in the folder (use `ls` to check). Do the same in for bungee (run `./start.sh` in `eagler/proxy` and use the `end` command to close it once it stops doing stuff)

## Part Two: Editing the Files

Now that you have all the files you want to configure you need to make a couple of changes. Starting with your server you'll need to edit `server.properties` and change the server port to something other than 25565 (I use 25550-25559 for servers personally). Next you'll need to edit `spigot.yml` and set `bungeecord`  to true. on the bungee side you'll need to edit `config.yml` and change `- query_port:` to 25565, `host:` to 25565 and `address: localhost:25565` to `address: localhost:<serverport>`.

Examples: 
- config.yml (eagler/proxy/)
`server_connect_timeout: 5000
remote_ping_cache: -1
forge_support: false
player_limit: -1
permissions:
  default:
  - bungeecord.command.server
  - bungeecord.command.list
  admin:
  - bungeecord.command.alert
  - bungeecord.command.end
  - bungeecord.command.ip
  - bungeecord.command.reload
timeout: 30000
log_commands: false
network_compression_threshold: 256
*online_mode: true*
disabled_commands:
- disabledcommandhere
servers:
  lobby:
    motd: '&1Just another BungeeCord - Forced Host'
***    address: localhost:25550***
    restricted: false
listeners:
***- query_port: 25565***
  motd: '&1Another Bungee server'
  tab_list: GLOBAL_PING
  query_enabled: false
  proxy_protocol: false
  forced_hosts:
    pvp.md-5.net: pvp
  ping_passthrough: false
  priorities:
  - lobby
  bind_local_address: true
***  host: 0.0.0.0:25565***
  max_players: 1
  tab_size: 60
  force_default_server: false
ip_forward: true
remote_ping_timeout: 5000
prevent_proxy_connections: false
groups:
  md_5:
  - admin
connection_throttle: 4000
stats: ***...
connection_throttle_limit: 3
log_pings: true`

- server.properties (eagler/server/)
`#Minecraft server properties
#(File modification date and time)
enable-jmx-monitoring=false
rcon.port=25575
level-seed=
gamemode=survival
enable-command-block=false
enable-query=false
generator-settings={}
***enforce-secure-profile=false***
level-name=world
motd=A Minecraft Server
***query.port=25550***
pvp=true
generate-structures=true
max-chained-neighbor-updates=1000000
difficulty=easy
network-compression-threshold=256
max-tick-time=60000
require-resource-pack=false
use-native-transport=true
max-players=20
*online-mode=true*
enable-status=true
allow-flight=false
initial-disabled-packs=
broadcast-rcon-to-ops=true
view-distance=10
server-ip=
resource-pack-prompt=
allow-nether=true
***server-port=25550***
enable-rcon=false
sync-chunk-writes=true
op-permission-level=4
prevent-proxy-connections=false
hide-online-players=false
resource-pack=
entity-broadcast-range-percentage=100
simulation-distance=10
rcon.password=
player-idle-timeout=0
force-gamemode=false
rate-limit=0
hardcore=false
white-list=false
broadcast-console-to-ops=true
spawn-npcs=true
spawn-animals=true
log-ips=true
function-permission-level=2
initial-enabled-packs=vanilla
level-type=minecraft\:normal
text-filtering-config=
spawn-monsters=true
enforce-whitelist=false
spawn-protection=16
resource-pack-sha1=
max-world-size=29999984`

- spigot.yml (eagler/server/)
`config-version: 11
settings:
  debug: false
  save-user-cache-on-stop-only: false
  sample-count: 12
  moved-too-quickly-multiplier: 10.0
  filter-creative-items: true
  moved-wrongly-threshold: 0.0625
  user-cache-size: 1000
  int-cache-limit: 1024
  late-bind: false
***  bungeecord: true***
  play...`
# Step Five: Running the Server

 Now that the server is ready you need to run both at the same time. I recommend tmux for this. You can do this with these steps:

- run the `tmux` command
- press ctrl + b then % to split tmux in half
- navigate to the `~eagler/server/` folder and run `./start.sh`
- use ctrl + b then the arrow key to go to the other pane
- navigate to the `~eagler/proxy/` folder and run `./start.sh`

Once both are running connect at `<IP>:8081` MAKE SURE TO PORT FORWARD (TCP).

# Troubleshooting:

## I get "you are not registered on this server" when joining on eager:
- You have online mode on, either turn it off in `server.properties` and `config.yml`

## It says permission denied when I try to `./start.sh`
- You have to `chmod +x ./start.sh`
