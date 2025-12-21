# nostr-multimedia

Fetch anything from Nostr - JSON RPC, plaintext, Markdown, HTML and images

New: Host a static website on Nostr using experimental Kind 30080 notes!

Note: Publishing posts is not supported, use [nostr-publisher-cli](https://github.com/ed253/nostr-publisher-cli) instead

## Installation

`npm install ed253/nostr-multimedia`
`npm start`

Runs on localhost:8080 by default.

If you want to expose it to the internet:
- Setup PM2 [(guide)](https://www.digitalocean.com/community/tutorials/how-to-use-pm2-to-setup-a-node-js-production-environment-on-an-ubuntu-vps)
- Setup a Nginx reverse proxy [(guide)](https://www.digitalocean.com/community/tutorials/how-to-configure-nginx-as-a-reverse-proxy-on-ubuntu-22-04)
- Enable SSL [(guide)](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-20-04)
- Add a ratelimit [(guide)](https://www.digitalocean.com/community/tutorials/how-to-rate-limit-a-node-js-app-with-nginx-on-ubuntu-20-04)

Or use the public instance at nostr-multimedia.netlify.com

## JSON RPC

### Format

`http://127.0.0.1:8080/json/{TYPE}/{RELAY}/{ID}/`

Or

`http://127.0.0.1:8080/json/{TYPE}/{RELAY}/{ID}/limit-{LIMIT}/since-{SINCE}/until-{UNTIL}`

- Type: note, comments, profile, author or search
- Relay: URL without `wss://`
- ID: bech32, hex or search term

### Fetch a note by ID

https://nostr-multimedia.netlify.com/json/note/relay.nostr.band/note1kme87khrvjawl0d7k0mwwdqqrx3ya4kenuajzynft4uk75ya53ms2qeeqw/

Returns:

`[["EVENT","fetchNote",{"id":"b6f27f5ae364baefbdbeb3f6e7340019a24ed6d99f3b2112695d796f509da477","pubkey":"9279276bffb83cee33946e564c3600a32840269a8206d01ddf40c6432baa0bcb","content":"Do you prefer reading physical books or digital ones?\n\nby cryotosensei in ~BooksAndArticles\n212 sats and 7 comments so far\n\nhttps://stacker.news/items/1288861","sig":"59fe34ded736e10fcad6993d8ff7acabd063fc4fd23ff1772299be763fd813d2db1b2204ed1d45d8793fcc023557db828e2c322c655d8f2a78e2c70ff60fe6a4","kind":1,"created_at":1763899205,"tags":[["client","stacker.news"]]}]]`

### Fetch comments by note

https://nostr-multimedia.netlify.com/json/comments/relay.nostr.band/note1kme87khrvjawl0d7k0mwwdqqrx3ya4kenuajzynft4uk75ya53ms2qeeqw/

### Fetch profile by npub

https://nostr-multimedia.netlify.com/json/profile/relay.nostr.band/npub1jfujw6llhq7wuvu5detycdsq5v5yqf56sgrdq8wlgrryx2a2p09svwm0gx/

### Fetch notes by author

https://nostr-multimedia.netlify.com/json/author/relay.nostr.band/npub1jfujw6llhq7wuvu5detycdsq5v5yqf56sgrdq8wlgrryx2a2p09svwm0gx/

### Fetch notes by search term

https://nostr-multimedia.netlify.com/json/search/relay.nostr.band/bitcoin/

Or

https://nostr-multimedia.netlify.com/json/search/relay.nostr.band/bitcoin/limit-10/since-100000000/

Note: Not all relays support search

### Advantages

- Supports all kinds
- Call via browser, curl, Node, Python, PHP, Rust...
- No Websocket needed
- Synchronous
- Supports CORS

## Plain text

### Format

`http://127.0.0.1:8080/text/{RELAY}/{ID}/`

### Display a note

https://nostr-multimedia.netlify.com/text/nos.lol/naddr1qqfxy6t5vdhkjm3dwa5xjar9wpshqetjqgsp6ppesc0wf3q3c3qh7g74yzytc297xdn6f66lqgv2mv03exlmcwsrqsqqqa28dd8gcl/

Returns:

A plain text file

### Advantages

- Lightweight
- No JavaScript
- Supports CORS

## Markdown

### Format

`http://127.0.0.1:8080/markdown/{RELAY}/{ID}/`

### Display a note

https://nostr-multimedia.netlify.com/markdown/nos.lol/naddr1qqfxy6t5vdhkjm3dwa5xjar9wpshqetjqgsp6ppesc0wf3q3c3qh7g74yzytc297xdn6f66lqgv2mv03exlmcwsrqsqqqa28dd8gcl/

Returns:

A HTML page

### Advantages

- Display a Nostr post as a simple webpage with Markdown -> HTML conversion
- Supports any kind, most useful for Kind 1 notes and Kind 30023 long form posts
- No JavaScript, lightweight and fast

## Static websites

### Format

`http://127.0.0.1:8080/web/{RELAY}/{ID}/`

Or

`http://127.0.0.1:8080/web/{RELAY}/{NIP-05}/{D-TAG}`

### Display a web page or file

https://nostr-multimedia.netlify.com/web/nos.lol/naddr1qqyxcmm8duh8qmn8qgsq6y6ncwl540trzeqxkurd4366gt40kn0qus9kwukr78wnwdgzq5grqsqqqavqa726jp/

Or

https://nostr-multimedia.netlify.com/web/nos.lol/ed253@nostrcheck.me/test.html

Or

https://nostr-multimedia.netlify.com/web/nos.lol/ed253@nostrcheck.me/logo.png

Returns:

A HTML page or file

### Kind 30080

Kind 30080 is an experimental kind for static websites

It looks like

`["EVENT",{"id":"xxxx","pubkey":"xxxx","created_at":100000000,"kind":30080,"tags":[["d","test.html"],["published_at","100000000"]],"content":"data:text/html;base64,PGh0bWw+CiAgPGhlYWQ+[...]PC9odG1sPg==","sig":"xxxx"}]`

- Replaceable addressable event, only the newest file is saved
- `content` must be the Data URI of the HTML/image/CSS/JS file (must be base64-encoded and include the mime type)
- `content` may be any length, but relays generally limit content to 64 KB
- The `d tag` must be the file name
- Other tags are optional, e.g. `tags` to describe the content

You can publish Kind 30080 using [nostr-publisher-cli](https://github.com/ed253/nostr-publisher-cli)

Not all relays accept arbitrary kinds, but nos.lol works for now

### Advantages

- Host a static website on Nostr
- Upload HTML pages, images, CSS/JavaScript
- Support for NIP-05 usernames
- Set the file name as a d tag
- Uses base64 encoding by default

With a shorter URL and default relay, Nostr webpages could look something like: https://nostr.site/bob@nostr.me/projects.html

### Disadvantages

- Not all relays accept the experimental Kind 30080
- There is currently no NIP for Kind 30080
- Does not support backend code like Node, Python or PHP
- The content field usually has a max size of 64 KB

## Tips

If you like nostr-multimedia, tips are appreciated:

Bitcoin: ed253@coinos.io

Monero: 87vvXn5XgwHGP8YFxGDNA9LVZbAYESXpRaqNn3LR6dYgHzqJhEemN5eLXoNciR8BKUAZ32Ygc8z9UPxqmbQLZxmU4mTa81V

## License

MIT
