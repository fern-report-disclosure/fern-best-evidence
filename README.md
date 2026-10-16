# fern.best evidence: cloud saves upload proxied-site cookies

Redacted browser captures (HAR) from a controlled test of the "cloud saves" feature of
[fern.best](https://fern.best), a web proxy ("unblocker") used by K-12 students.

**Finding:** while a user is signed into a Fern account, Fern uploads the proxy's cookie jar and local
storage to `fern.best/api/saves` every 2–3 minutes. These hold the cookies and stored data of every
site opened through Fern, which is what keeps people logged in. Fern's server returns them to any
browser that signs into the same account.

Full analysis: https://rentry.co/6n3t22dr
Earlier capture of the same site: https://archive.org/details/fern-best-har-2026-10-16

## Method

- Captured 16 October 2026, 21:30–21:46 UTC, against build `app-shared-C9iDx0wb-c57aa2b0.js`.
- Fresh virtual machine and browser on a mobile connection, with a throwaway Fern account.
- A harmless test cookie (`fernaudit` = `fernck-3n9s1h`) was set through Fern's proxy using
  httpbin.org. A test local storage value (`fernaudit_ls` = `fernls-4k9x1p`) was set on fern.best.
- No real accounts or personal data were used.

## Files

| File | What it shows |
| --- | --- |
| `fern-cloudsave-planted.redacted.har` | Sign-up and planting the canaries. `PUT /api/saves` at 21:34:54 UTC, 15 seconds after the cookie was set, uploads the proxy's cookie jar (IndexedDB `__ebgiyqeiogq_hebgiyq`) containing the test cookie. Uploads repeat every 2–3 minutes. |
| `fern-cloudsave-restored.redacted.har` | A separate incognito window, which had never visited httpbin.org, signs into the same account. `GET /api/saves` returns the test cookie and the local storage value. Its next upload includes a proxied site's local storage key (`www.google.com@…`) and ad-network identifiers (Criteo, Lotame, Yahoo ConnectID, ID5 and others). |
| `SHA256SUMS.txt` | Hashes of both files. |

To find the evidence, open a HAR in your browser's DevTools (Network tab, import), or search it as text
for `api/saves`, `fernck-3n9s1h` and `fernls-4k9x1p`.

## Redaction

The following were replaced with `REDACTED`, with key names kept:
- the browser's user agent, client hints and language
- cookies and authorization headers
- the Fern sign-in token (including in a websocket URL), device ID, user ID, username and password
- the fingerprint data sent at sign-up
- ad-network fingerprint fields
- every cloud-save value except the two test canaries.

The unredacted originals are retained unaltered:
- planted: `4971dcd689bafbe3a6bda66efa907a8a562ba3cf1fd22238c4211c8fc10807cd`
- restored: `7ad7767acf74e95df5fe768f27956c25bf5b4d8a762bbef00d269faea46168f2`

## Safety

These files are plain JSON text. They contain copies of Fern's scripts, so don't rename them to `.js`
or open their contents as a web page.

## License

CC0-1.0
