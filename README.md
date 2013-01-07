The sample code to reproduce a bug (?) in Derby. 

This creates a database called derbytest6.

## The bug

I'm running my app in two modes: normal way, listening a port and accepting requests from browsers --  and in a "daemon" mode that runs a only on the server side, doesn't listen to any port, but uses cron instead to run some functions periodically. The daemon version I start with a command `node daemon.js -d`.

In the lib/server/index.js I have a function which creates a model by store.createModel(). I use that model to update the data in the database. 

Now, the problem is that, while the database gets changed, the changes won't get propagated "live" to the clients (browsers). Each browser updates only when doing a hard refresh. So, this happens when I run the function in the daemon mode.

If I run the same function through an Express route and use a browser to run it, then everything works as expected -- database gets updated and changes are propagated to the clients. The store.createModel() works in this case. But it doesn't fully work when run in the daemon mode.

## How to reproduce

- Start the app: run `node server.js` and wait for a while.
- Browse to http://localhost:3000
- Reload a couple of times
- Start the daemon: `node daemon.js -d`
- The output should be something like 
	```
	********* incrementing *********
	{ visits: 5 }
	```
- Press Ctrl-C and maybe run the daemon again to increment again. See that "visits" really increments.
- See that the browser page *has not updated*. That is, visits has not incremented on the browser.
- Do a hard refresh in the browser, visits should update to the correct number.

## Derby version used

As seen in package.json, I am actually using https://github.com/reneclaus/derby and other Derby modules from there.
