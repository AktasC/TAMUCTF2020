# [Too Many Credits 2](http://toomanycredits.tamuctf.com/)

We're still on the same website, with our first flag still printed.  
The second flag's about successfully "popping a shell" says the description.

All we know is that this is a java application and I have 0 experience with Java whatsoever.  
Nevertheless, I had to try and get a maximum of useful informations so my peers could keep digging wherever I stopped.

## Initial findings

The hash we saw has a gzip header starting with `H4sIA`.  
Hash = Base64Decode(Hash).GzipDeflate(Hash) is basically the idea I had at first.

Trying that gives the following string :

```
¬í..sr.-com.credits.credits.credits.model.CreditCount2	Û..	$G...J..valuexp....... 
```

[Decoding process example](<https://gchq.github.io/CyberChef/#recipe=From_Base64('A-Za-z0-9%2B/%3D',true)Gunzip()&input=SDRzSUFBQUFBQUFBQUZ2emxvRzF1SWhCTnprL1Z5KzVLRFVsczZRWWc4N05UMG5OMFhNRzg1enpTL05LakRodkM0bHdxcmd6TVRCNk1iQ1dKZWFVcGxZVU1JQUJzellBcGZuVWtsSUFBQUE9>)
