This was malware that attempted to expose some private info via compromising publishing of `Nx` packages on NPM, and used Claude Code (or whatever available CLI tool) in its hijacked postinstall script.

The source for `Nx` was not compromised, but someone published `Nx` NPM packages on their behalf. If you used the `Nx` Console VSCode extension during a certain period of time on August 26th, you may have been affected even if your repository wasn't using `Nx` or you were on an older version.

To extract the output from your compromised repo that this malware publishes to your GitHub (although GitHub has disabled access to these, so you may need to ask for yours), do the following two steps:

1.

`cat results.b64 | base64 -d | base64 -d > envdecode.json`

The JSON output is double base64 encoded.

Inside this output, `inventory` is a json array of base64 encoded strings that contains the files it found when searching with your AI CLI tool.

2.

```
jq -r '.inventory[]' envdecode.json | while read b64; do
  echo "$b64" | base64 --decode
  echo "\n=============================NEXT INVENTORY FILE==================================\n"      # add newline after each item, optional
done >! inventorydecode.txt
```

Feel free to look at the `envdecode.json` and `inventorydecode.txt` outputs to see what credentials might have been impacted, if any. I've provided a sample output in this repository for your viewing.

Read more about it here:

[Nx Security Bulletin](https://github.com/nrwl/nx/security/advisories/GHSA-cxm3-wv7p-598c)

[Wiz postmortem](https://www.wiz.io/blog/s1ngularity-supply-chain-attack)

[Aikido postmortem with actual payload](https://www.aikido.dev/blog/popular-nx-packages-compromised-on-npm#the-malicious-payload)
