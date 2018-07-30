# Enumivo Watcher Plugin based on EOS Watcher Plugin by EOS Authority
The watcher plugin is useful to watch for specific actions on the chain and then send them to an HTTP url. 
The HTTP POST is called as soon as the action is seen on the chain!


# Watcher plugin features
## Filter and get notifications for actions on any account
You can either get notifications for all actions on a specific contract account, or any specific actions. You get notifications in a form of http POST call to the url you specify. 

## Age limit for actions
Usually you don't want to receive notifications for actions which happened months ago, just because your enunode is resyncing. To prevent this you can specify age limit for blocks, which are filtered for actions you want to receive. This is by default set to 1 minute, but is configurable.

## Flexible configuration
Features just mentioned are configurable through options to enunode (built with watcher_plugin) or in your config.ini. Use `enunode --help` to see help for options.

## Asynchronous http calls
Notifications are sent asynchronously, which means it does not interfere with normal operation of enunode, even in case of unreliable http connection to the notification receiver.

## Retry on failed calls
To make sure no action is missed, even when a connection to the receiver is lost, on failed first attempt to send, plugin retries once more before giving up.

# Installation instructions

## Requirements
- Works on any Enumivo node that runs v1.1.0 and up. Lately tested on v1.1.1

## Building the plugin [Install on your enunode server]
1. Copy watcher_plugin folder to `<enumivo-source-dir>/plugins/` You should now have `<enumivo-source-dir>/plugins/watcher_plugin`
2. Add the following line to `<enumivo-source-dir>/plugins/CMakeLists.txt` with other `add_subdirectory` items
  ```
  add_subdirectory(watcher_plugin)
  ```

3. Add the following line to the bottom of `<enumivo-source-dir>/programs/enunode/CMakeLists.txt`
  ```
  target_link_libraries( enunode PRIVATE -Wl,${whole_archive_flag} watcher_plugin -Wl,${no_whole_archive_flag})
  ```
4. Build and install enunode as usual. You could even just `cd <enumivo-source-dir>/build` and then `sudo make install`

# How to setup on your enunode

Enable this plugin using `--plugin` option to enunode or in your config.ini. Use `enunode --help` to see options used by this plugin.

## Edit your enunode config.ini (probably easier)
```
#Enable plugin
plugin = enumivo::watcher_plugin
#Set account:action so eosauthority:spaceinvader or just eosauthority: for all actions on eosauthority
watch = eosauthority:
#watch multiple if required 
watch = b1:
#watch multiple if required 
watch = eosabc:forum
#http endpoint for each action seen on the chain. JSON array if you there are multiple actions in one block.
#see sample json example. All "watch" above will be sent to this URL and the URL can handle processing as required
watch-receiver-url = http://127.0.0.1:8082/blockchain_action
#Age limit in seconds for blocks to send notifications. No age limit if set to negative.
#Used to prevent old actions from trigger HTTP request while on replay (seconds)
watch-age-limit = 5
 ```
## Check if the plugin has loaded
- You should see an entry for watcher_plugin in the logs when you restart enunode. 
- Your HTTP endpoint should receive POST requests as in [our sample JSON](sample-post.json)
- Thats it, you should be all set to get realtime actions on the chain.

# Feedback & development
- This is an adapted plugin go work with Enumivo mainnet.
- The plugin is used on production with space invaders. https://eosauthority.com/space/
- You also use this plugin to setup realtime alerts similar to https://eosauthority.com/alerts 
- Any suggestions and pull requests are welcome :)
