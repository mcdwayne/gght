# gght - GitGuardian Honeytoken CLI

With the release of the Honeytokens API, I made a CLI to learn the ins and out of it. 

This was that tinkering around project. 

Enjoy!  


## install

This is meant to live as a one file, easy to maintain, install anywhere kind of thing. By that, I mean there is a little bit of modification needed from your end to configure this, though for most people this will simply mean setting a ENV variable. 

So to install and use:

1. Download this file and put it in your ~/bin folder
2. Open a terminal to that folder and run `chmod 755 gght`
3. Set a env variable called `TOKENGG` set to your [GitGuardian Personal Access Token](https://docs.gitguardian.com/api-docs/personal-access-tokens). See [this article for help with that](https://unix.stackexchange.com/questions/117467/how-to-permanently-set-environmental-variables) on your system
4. From the terminal run `gght health`. If you get a `"Valid API key."` back, then you are good to go. 
5. Read the API documentation, open the hood of the file, and make it your own! 
6. Have fun!

Note: It does not matter what order you put the options in for `list` and `events list` commands.  It does for the other commands.  


## Example use
While this does not do everything the API can do, i used the 80/20 rule. 
Here are some fun commands I liked.

### Show me the most recent triggered Honeytokens
`gght list -o -triggered_at -p 5 --status triggered`
returns the 5 most recently triggered Honeytokens.


### What Honeytokens include my name in them?
`gght -l search dwayne`
searches for honeytokens with a name that contains my name, case insensitive

### Just show me the tokens already!
`gght -l -p 5 -o -created_at --show_token true -s active | grep 'secret_key\|access_token_id' `
which will return a list of:
the last 5, most recently creared honeytokens,
 which have a status of active
  and the results will display the actual credentials contained within each honenytoken.  
  Then grep will show only the  "access_token_id" and "secret_key" from the results.  
    It looks like this

```
"access_token_id": "xxxxxxxxxxxxxxxxxxxxxxxx",
"secret_key": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```

### What are the IPs that keep showing up?
`gght events list -p 100 -o -triggered_at | grep -w "ip_address" | sort | uniq -c | sort -r`
Show me a list of 
  just the ip addresses 
    that caused the last 100 
      most recent triggered events
    sort it into a list with the most recent on top
  count each group of unique ips from the list
and show me the count before the IP 

Output looks like this:

```
gght ~ $gght events list -p 100 -o -triggered_at | grep -w "ip_address" | sort -r | uniq -c | sort -r      
  50     "ip_address": "xx.xxx.xx.xxx",
  24     "ip_address": "xx.xxx.xx.xxx",
   8     "ip_address": "xx.xxx.xx.xxx",
   5     "ip_address": "xx.xxx.xx.xxx",
   3     "ip_address": "xx.xxx.xx.xxx",
   3     "ip_address": "xx.xxx.xx.xxx",
   2     "ip_address": "xx.xxx.xx.xxx",
   1     "ip_address": "xx.xxx.xx.xxx",
   1     "ip_address": "xx.xxx.xx.xxx",
   1     "ip_address": "xx.xxx.xx.xxx",
   1     "ip_address": "xx.xxx.xx.xxx",

```
Sure most of that command is just built in Bash, but that is the joy(?) and power of the command line! 

##### Do you JUST want the IP addressed with no other text?
Bash has you!
just pipe that last command to `cut -d'"' -f 4`

```
gght events list -p 100 -o -triggered_at | grep -w "ip_address" | sort -r | uniq -c | cut -d'"' -f 4 
xx.xxx.xx.xxx
xx.xxx.xx.xxx
xx.xxx.xx.xxx
xx.xxx.xx.xxx
```
### FAQ

1. Who asked these questions?
Dwayne make this all up

2. Why Bash?
It is universal and a glue that makes all open source play "well" together. If you can dream it, you can script it. 

3. Why not Python?
Python is great and all, but the simplicity of working in Bash just appeals to me. Sure, some of the JSON handling might be much easier to pull off in Python, and if I was building production scripts that relied on queried responses for high throughput transactions I might consider it, but I would still likely want to use Bash. 

This is intended to be for people who live in the command line and see all apps via their APIs and CLIs.  


```
## Help menu
 gght - the GitGuardian Honeytoken CLI nobody asked for!

  usage: gght command <arg1> <arg2> <"message in double quotes">
  example: gght note delete 114 421 

  Commands:
    health - check the API endpoint with your personal access token
    list -  list all the honeytokens of your workspace 
          -p|--per_page 
          -s|--status
          -o|--ordering
          -st|--show_token true
          search            
    retrieve | get - retrieve an existing honeytoken - requires: <honeytoken-id>
    create - create a honeytoken of a type - requires: <name>, (optional): <"description">
    reset - resets a triggered honeytoken - requires: <honeytoken-id>
    revoke - revokes an active or triggered honeytoken. All the associated events will be closed - requires: <honeytoken-id>
    test - trigger a honeytoken using aws s3 ls - requires: <honeytoken-id>
    edit -
       name - edit a honeytoken name - requires: <honeytoken-id> <"new name">
       description - edit a honeytoken description - requires: <honeytoken-id> <"new description">
       both - edit both a honeytoken name and description- requires: <honeytoken-id> <"new name"> <"new description">
       Example use: gght edit both 114 "New Name" "New Desciption"
    
    events - list events related to all honeytokens of the workspace

    note - 
      list - List notes left on a honeytoken in chronological order - requires: <honeytoken-id>
      create - add a note on a honeytoken - requires: <honeytoken-id> <"comment">
      update - update a note on a honeytoken - requires: <honeytoken-id> <note-id> <"comment">
      delete - deletes a note from a honeytoken - requires: <honeytoken-id> <note-id>
      Eample use: gght note update 114 421 "New comment goes here"

```

Contact: dwayne.mcdaniel@gitguardian.com if you have questions
    
