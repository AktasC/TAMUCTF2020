# [Too Many Credits](http://toomanycredits.tamuctf.com/)

This exercice is about a simple website made of a counter and a submit button resulting in an increment of that counter.  
The website stores this value in the cookies, which we can read and write with the browser's dev tools.

Untill the value reaches a certain point, the website shows a message like this :  
`You have X credits. You haven't won yet...`

## Initial Thoughts
"All we have to do" is to find a way to get that counter to a big enough number so we can get the flag.  
That was the idea and it seemed logical for a challenge giving less than 300 points.  

If you refresh the page or click, you'll get an increment.  
So I let an auto-refresher plugin do his work while I was trying to decode the hash.  

Here's a list of different hashes corresponding to different `counter` (integer) values.

| Int  | Hash                                                                                                         |
| ---- | ------------------------------------------------------------------------------------------------------------ |
| 1    | H4sIAAAAAAAAAFvzloG1uIhBNzk/Vy+5KDUls6QYg87NT0nN0XMG85zzS/NKjDhvC4lwqrgzMTB6MbCWJeaUplYUMEAAIwCwY0JiUgAAAA== |
| 5    | H4sIAAAAAAAAAFvzloG1uIhBNzk/Vy+5KDUls6QYg87NT0nN0XMG85zzS/NKjDhvC4lwqrgzMTB6MbCWJeaUplYUMEAAKwCppy9lUgAAAA== |
| 10   | H4sIAAAAAAAAAFvzloG1uIhBNzk/Vy+5KDUls6QYg87NT0nN0XMG85zzS/NKjDhvC4lwqrgzMTB6MbCWJeaUplYUMEAAFwA4upD1UgAAAA== |
| 11   | H4sIAAAAAAAAAFvzloG1uIhBNzk/Vy+5KDUls6QYg87NT0nN0XMG85zzS/NKjDhvC4lwqrgzMTB6MbCWJeaUplYUMEAANwCuipeCUgAAAA== |
| ---- | ------------------------------------------------------------------------------------------------------------ |
| 200  | H4sIAAAAAAAAAFvzloG1uIhBNzk/Vy+5KDUls6QYg87NT0nN0XMG85zzS/NKjDhvC4lwqrgzMTB6MbCWJeaUplYUMEDACQCkGfqAUgAAAA== |
| 500  | H4sIAAAAAAAAAFvzloG1uIhBNzk/Vy+5KDUls6QYg87NT0nN0XMG85zzS/NKjDhvC4lwqrgzMTB6MbCWJeaUplYUMIAB4xcAYlSOtlIAAAA= |
| 650  | H4sIAAAAAAAAAFvzloG1uIhBNzk/Vy+5KDUls6QYg87NT0nN0XMG85zzS/NKjDhvC4lwqrgzMTB6MbCWJeaUplYUMIABUxcAmlseKlIAAAA= |
| ---- | ------------------------------------------------------------------------------------------------------------ |
| 800  | H4sIAAAAAAAAAFvzloG1uIhBNzk/Vy+5KDUls6QYg87NT0nN0XMG85zzS/NKjDhvC4lwqrgzMTB6MbCWJeaUplYUMIABswIALSAGBVIAAAA= |
| 805  | H4sIAAAAAAAAAFvzloG1uIhBNzk/Vy+5KDUls6QYg87NT0nN0XMG85zzS/NKjDhvC4lwqrgzMTB6MbCWJeaUplYUMIABsyoAotRsdVIAAAA= |
| 810  | H4sIAAAAAAAAAFvzloG1uIhBNzk/Vy+5KDUls6QYg87NT0nN0XMG85zzS/NKjDhvC4lwqrgzMTB6MbCWJeaUplYUMIABsxYAM8nT5VIAAAA= |
| ---- | ------------------------------------------------------------------------------------------------------------ |
| 900  | H4sIAAAAAAAAAFvzloG1uIhBNzk/Vy+5KDUls6QYg87NT0nN0XMG85zzS/NKjDhvC4lwqrgzMTB6MbCWJeaUplYUMIABcwsA3Ee91FIAAAA= |
| 1000 | H4sIAAAAAAAAAFvzloG1uIhBNzk/Vy+5KDUls6QYg87NT0nN0XMG85zzS/NKjDhvC4lwqrgzMTB6MbCWJeaUplYUMIAB8wsAr2q5kFIAAAA= |
| 4000 | H4sIAAAAAAAAAFvzloG1uIhBNzk/Vy+5KDUls6QYg87NT0nN0XMG85zzS/NKjDhvC4lwqrgzMTB6MbCWJeaUplYUMIAB/wIAAewLRFIAAAA= |
| 4100 | H4sIAAAAAAAAAFvzloG1uIhBNzk/Vy+5KDUls6QYg87NT0nN0XMG85zzS/NKjDhvC4lwqrgzMTB6MbCWJeaUplYUMICBAAsAboXqWFIAAAA= |
| 4200 | H4sIAAAAAAAAAFvzloG1uIhBNzk/Vy+5KDUls6QYg87NT0nN0XMG85zzS/NKjDhvC4lwqrgzMTB6MbCWJeaUplYUMICBQAYAHajuHFIAAAA= |
| 5420 | H4sIAAAAAAAAAFvzloG1uIhBNzk/Vy+5KDUls6QYg87NT0nN0XMG85zzS/NKjDhvC4lwqrgzMTB6MbCWJeaUplYUMICBqA4A0dkoEFIAAAA= |

As you can see, there's a long prefix and a short suffix also acting as padding. Again, this is clearly some sort of Base64.

There's a part changing in a weird way which caught our attention : the `YUM` part preceding the "usual" changing chars.  
So let's take the hash of `11` and change the `YUMEAA` part into `YUMFAA` just to see what happens.

| Int | Prefix                                                                                 | Changes | Suffix           |
| --- | -------------------------------------------------------------------------------------- | ------- | ---------------- |
| 11  | H4sIAAAAAAAAAFvzloG1uIhBNzk/Vy+5KDUls6QYg87NT0nN0XMG85zzS/NKjDhvC4lwqrgzMTB6MbCWJeaUpl | YUMEAA  | NwCuipeCUgAAAA== |
| ??? | H4sIAAAAAAAAAFvzloG1uIhBNzk/Vy+5KDUls6QYg87NT0nN0XMG85zzS/NKjDhvC4lwqrgzMTB6MbCWJeaUpl | YUMFAA  | NwCuipeCUgAAAA== |

This actually happens to be the hash for `31525678435270769` which is enough credits to give us the flag :)

> Thanks for the gig ! `gigem{l0rdy_th15_1s_mAny_cr3d1ts}`
