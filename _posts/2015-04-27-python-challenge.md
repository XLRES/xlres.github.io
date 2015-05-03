---
date: 19:02 2015-04-27
title: Python Challenge
layout: post
tag:
  - python
  - python challenge
---

之前开始学Python的时候，偶然查到了这个神奇的网站
[Python Challenge](http://www.pythonchallenge.com)  
应该说是解谜网站？每一关（level）都是让访问者根据界面上的提示
得到下一关的URL。  
挺有意思。

Click here to get challenged.  

---

应该说不愧是程序员吗计数果然从0开始。  
<img src="http://www.pythonchallenge.com/pc/def/calc.jpg"  width = 70%/>  
给出的提示是试着改掉URL。
第一眼以为是238，然而输入238则会提示
>No... the 38 is a little bit above the 2...

顺带一提如果没看懂图片而直接改URL，1的页面提示的是
>2**38 is much much larger.

答案是2\*\*38，274877906944，在python命令行中可以直接计算出结果。   
以此显示Python对于数字支持的很好，2\*\*38这么大的可以直接处理。  
当然这一次只是warming up.

---

在这儿URL其实已经自动跳转到map.html，映射。  
<img src="http://www.pythonchallenge.com/pc/def/map.jpg" width = 70% />  
>everybody thinks twice before solving this.

其实我并没有看懂提示……但是图很清晰了。  
K->M, O->Q, E->G, 三个映射都只是往后移两位而已。  
下面还有一段意义不明的字符串，那就先做一次处理看看结果。  

```{python}
msg = "g fmnc wms bgblr rpylqjyrc gr zw fylb. rfyrq ufyr amknsrcpq ypc dmp. bmgle gr gl zw fylb gq glcddgagclr ylb rfyr'q ufw rfgq rcvr gq qm jmle. sqgle qrpgle.kyicrpylq() gq pcamkkclbcb. lmu ynnjw ml rfc spj"
for c in msg:
	if (ord(c) <= 96 or ord(c) > 122):
		print c,
	else:
		print chr((ord(c) - 96 + 2) % 26 + 96),
```

结果是
>i  hope  you  didnt  translate  it  by  hand .  thats  what  computers  are  for .  doing  it  in  by  hand  is  inefficient  and  that 's  why  this  text  is  so  long .  using  string .maketrans ( )  is  recommended .  now  apply  on  the  url

好嘛...原来还有string.maketrans()方法可以用，我都手动了。  
对url操作，map->ocr

---

<img src="http://www.pythonchallenge.com/pc/def/ocr.jpg" width = 70%/>  
ocr可是个麻烦活儿。  
然而图片很模糊，估计也不是让跑ocr，hint上写的则是  
>recognize the characters. maybe they are in the book, 
>but MAYBE they are in the page source.

查看网页源代码，原来在html注释里藏着一大段字符串，
一共1200多行……  
注释里也有提示：
> find rare charcters in the mess below.
 
跑一下程序：

```{python}
f = open("chars2.txt", "r")
count = [0]*128
content = f.readline()
while (content != ''):
	for c in content:
		count[ord(c)] += 1
                #if (96 < ord(c) and ord(c) < 123):
			#print (c),
	content = f.readline()

for i in range(128):
	if (count[i] > 0 ) :
		print "\b"+chr (i),count[i]
f.close()
```

各种符号每个6000多次左右，字母只有a,e,i,l,q,t,u,y每个出现一次。  
早知道这个结果，直接检测小写字母就完了……  
结果是equality，放进url。  

---

网页标题是re，regular expression， 正则表达式。
Python 的正则表达式继承自Perl，应该说相当强大。
<img src = "http://www.pythonchallenge.com/pc/def/bodyguard.jpg" width = 70%/>  

>One small letter, 
surrounded by EXACTLY three big bodyguards on each of its sides.

蜡烛真是奇怪呢……难道说是六个大写字母中间夹一个小写字母？   
试试看。

```{python}
import re
def isExactly(i):
    for c in i:
        if c.isupper(): 
            if not (c in 'EXACTLY'):
                return False
    return True

f = open("chars.txt", "r")
buf = f.read()
s = re.findall (r'[A-Z]{3}[a-z][A-Z]{3}',buf)
for i in s:
    #if i[0] == i[4]:
        print '\b'+i[3],

f.close()
```

结果：
>jfeiauzroivgzbmpszazlutnwsdofbiwqdjbzshfrblqgsbydajygcbj
wggtdfjeobcmdlzxajvitecgpkcfwqbvkoietpiijanvqjjgtcpadjkgc
luaidgumcdskunujfcjfmbzkzsasdxsqqqlaeisjefjfdaoljaywxjthq
jknednxnsahxqedoeqsdcmltcsnwakjxtytaalhgabekfmyimwrkffydg
hiunlriwgkuzqljjbsxguytfsatejmdwkfbzifdknpcqimvehxujszbuy
utsompijjojspbwlroefiwmrsjstdjhfwxhnthsoosmoqtufoxvpvpjkg
iaqgfrhufxxdnjiwtfqusbkeakunjgknpibklgjounivhgxsnekxgrrbs
lpuaouvhzbilbirmqqxtktgcnkdljoasnexwtgvwjegurnksokjtrovpm
ykzgeolwysfidemflmkwmj

呵呵……  
注意到左右的大蜡烛似乎有相似，难道第一个字符和第五个字符应该是一样的？  
无果。  
说不定秘密在hints中大写的EXACTLY？
暂时没想出来，以后如果有结果了再说。

