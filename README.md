# nostr-multimedia

Fetch anything from Nostr - JSON RPC, plaintext, Markdown, HTML and images

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

Or use the public instance at nostr-multimedia.eu-4.evennode.com

## JSON RPC

### Format

`http://127.0.0.1:8080/json/{TYPE}/{RELAY}/{ID}/`

Or

`http://127.0.0.1:8080/json/{TYPE}/{RELAY}/{ID}/limit-{LIMIT}/since-{SINCE}/`

- Type: note, comments, profile, author or search
- Relay: URL without `wss://`
- ID: bech32, hex or search term

### Fetch a note by ID

https://nostr-multimedia.eu-4.evennode.com/json/note/relay.nostr.band/note1kme87khrvjawl0d7k0mwwdqqrx3ya4kenuajzynft4uk75ya53ms2qeeqw/

Returns:

`[["EVENT","fetchNote",{"id":"b6f27f5ae364baefbdbeb3f6e7340019a24ed6d99f3b2112695d796f509da477","pubkey":"9279276bffb83cee33946e564c3600a32840269a8206d01ddf40c6432baa0bcb","content":"Do you prefer reading physical books or digital ones?\n\nby cryotosensei in ~BooksAndArticles\n212 sats and 7 comments so far\n\nhttps://stacker.news/items/1288861","sig":"59fe34ded736e10fcad6993d8ff7acabd063fc4fd23ff1772299be763fd813d2db1b2204ed1d45d8793fcc023557db828e2c322c655d8f2a78e2c70ff60fe6a4","kind":1,"created_at":1763899205,"tags":[["client","stacker.news"]]}]]`

### Fetch comments by note

https://nostr-multimedia.eu-4.evennode.com/json/comments/relay.nostr.band/note1kme87khrvjawl0d7k0mwwdqqrx3ya4kenuajzynft4uk75ya53ms2qeeqw/

### Fetch profile by npub

https://nostr-multimedia.eu-4.evennode.com/json/profile/relay.nostr.band/npub1jfujw6llhq7wuvu5detycdsq5v5yqf56sgrdq8wlgrryx2a2p09svwm0gx/

### Fetch notes by author

https://nostr-multimedia.eu-4.evennode.com/json/author/relay.nostr.band/npub1jfujw6llhq7wuvu5detycdsq5v5yqf56sgrdq8wlgrryx2a2p09svwm0gx/

### Fetch notes by search term

https://nostr-multimedia.eu-4.evennode.com/json/search/relay.nostr.band/bitcoin/

Or

https://nostr-multimedia.eu-4.evennode.com/json/search/relay.nostr.band/bitcoin/limit-10/since-100000000/

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

https://nostr-multimedia.eu-4.evennode.com/text/nos.lol/naddr1qqfxy6t5vdhkjm3dwa5xjar9wpshqetjqgsp6ppesc0wf3q3c3qh7g74yzytc297xdn6f66lqgv2mv03exlmcwsrqsqqqa28dd8gcl/

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

https://nostr-multimedia.eu-4.evennode.com/markdown/nos.lol/naddr1qqfxy6t5vdhkjm3dwa5xjar9wpshqetjqgsp6ppesc0wf3q3c3qh7g74yzytc297xdn6f66lqgv2mv03exlmcwsrqsqqqa28dd8gcl/

Returns:

A HTML page

### Advantages

- Display a Nostr post as a simple webpage with Markdown -> HTML conversion
- Supports any kind, most useful for Kind 1 notes and Kind 30023 long form posts
- No JavaScript, lightweight and fast

## HTML

### Format

`http://127.0.0.1:8080/html/{RELAY}/{ID}/`

### Display a note

https://nostr-multimedia.eu-4.evennode.com/html/nos.lol/naddr1qq9xummnw3ez6urpvajsygqaqsucv8hycsgugstly02jpz9u9zlrxeayad0syx9dk8cun0au8gpsgqqqw4rsz30n4m/

Returns:

A HTML page

### Advantages

- Host a static website on Nostr
- Supports any kind (because there isn't a HTML kind yet)
- Upload HTML pages as Kind 30023 (for now) using nostr-publisher-cli
- Supports inline CSS, JavaScript and base64 images

### Disadvantages

- No specific HTML page kind yet
- Using Kind 1 could spam social media clients, Kind 30023 could spam blogging clients
- Does not support backend code like Node, Python or PHP

## Base64 images and files

### Format

`http://127.0.0.1:8080/file/{RELAY}/{ID}/`

Or

https://nostr-multimedia.eu-4.evennode.com/file/{RELAY}/{ID}/{FILENAME}.{EXTENSION}

### Display an image

https://nostr-multimedia.eu-4.evennode.com/file/nos.lol/note1wqvk97jzp5nqxt79vy3rj3ts8pxp7z4sqhznh6sjj0txjjcdj28qrlp7m7/image.png

Returns:

An image

### Advantages

- Host images and other small files on Nostr
- Supports any kind (because there isn't a base64 file kind yet)
- Upload images and other small files as Kind 1 (for now) using nostr-publisher-cli

### Disadvantages

- No specific base64 file kind yet
- Using Kind 1 could spam social media clients, Kind 30023 could spam blogging clients
- Excessive images and files could spam the relay's storage
- Max content size is usually 64 KB

## Support

If you have a NIP for HTML pages or small base64 files, let me know and I'll add your note kind and parameters.

## Tips

If you like nostr-multimedia, tips are appreciated:

Bitcoin: ed253@coinos.io

Monero: 87vvXn5XgwHGP8YFxGDNA9LVZbAYESXpRaqNn3LR6dYgHzqJhEemN5eLXoNciR8BKUAZ32Ygc8z9UPxqmbQLZxmU4mTa81V

## License

MIT
