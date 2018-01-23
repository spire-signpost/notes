### Node.js - Update and Upgrade Node.js and NPM

A brief guide to updating and upgrading Node.js and NPM.

*n.b.* this guide has been tested with OS X.

#### upgrade steps
Use the following steps at a terminal to update and upgrade a current install of Node.js and NPM.

Use the *n* version manager,

```
npm install -g n
npm cache clean -f
```

Then, upgrade Node.js itself

```
sudo n stable
sudo npm update -g
```

Then, we can simply check the installed versions of Node.js and NPM,

```
node -v
npm -v
```
