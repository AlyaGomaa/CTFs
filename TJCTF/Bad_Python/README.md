# Description
My friend wrote a cool program to encode text data! His code is sometimes hard to understand, and only he knows how it works. I ran the program twice, but forgot the input I used for the first time. I didn't save the key I used either, but I know it was 15 characters long. Can you figure out what text I encoded the first time?
```py
Output 1 =  b'\x02\x19\x01\x16Q\r\x07\nS\x02)\x1a1=EE2\x0e=G/D\nRY)\nV\x1bJ'
Input 2 =  "Lorem ipsum dolor sit amet, consectetur adipiscing elit"
Output 2 = b':\x1c\x10\x07ZV\x1a\x12\x11B\x1bS\x06\r[\x19\x01B\x11^\x02S\x03\x0fR\x02_B\x01X\x18\x00\x07\x01C\x13\x07\x17\x10\x17\x17\x17\x0b\x12^\x05\x10\x0b\x0cPV\x16\x0e\x0bC'
```
here's the program:
```py
import random
A=bool
N=ord
y=sorted
Y=list
h=bin
T=int
e=range
F=len
L=open
O=repr
R=random.shuffle
import itertools
import functools
D=functools.reduce
S=A
P=lambda a,b:(N(a)^N(b)).to_bytes(1,'big')
B='[redacted - 15 chars]'
E=((0,3),(1,4),(0,1),(3,4),(2,3),(1,2))
B=y(Y(B))
x=lambda a,b:h((N(a)-N(b))^(T('1'*10,2)))[0]!='-'
def u(li):
 Q=[li[i::3]for i in e(3)]
 for i in Q:
  while not W(i):
   pass
 return Q
def W(i):
 R(i)
 a=[S(T(h(N('e'))[2:][-1]))]
 return[n(a,x(i[E[j][0]],i[E[j][1]]))for j in e(F(E))][-1]
def n(g,k):
 g[0]=g[0]and k
 return g[0]
f=u(B)
a=L('input.txt','r').read()
m='output.txt'
L(m,'w').write(O(b''.join([D(P,[(((N(a[i])&(~N(f[j][i%5])))|((~N(a[i]))&(N(f[j][i%5])))).to_bytes(1,"big"))for j in e(F(f))])for i in e(F(a))])))

#   if c%100000==0:
#     print(i)
#     print(c)

```
---
## Solution

so i tried to make it more readable and removed the unused/dead code, here's the clean version of the challenge:
```py
import functools
def u(arr):
	Q=[arr[i::3]for i in range(3)]
	return Q 
	#returns [[key[0], key[3], key[6], key[9], key[12]], [key[1], key[4], key[7], key[10], key[13]], [key[2], key[5], key[8], key[11], key[14]]]
xor_two_bytes=lambda a,b:(ord(a)^ord(b)).to_bytes(1,'big')
key='[redacted - 15 chars]'
key = u(key)
flag=open('input.txt','r').read()
enc_flag = [functools.reduce(xor_two_bytes,[(((ord(flag[i])&(~ord(key[j][i%5])))|((~ord(flag[i]))&(ord(key[j][i%5])))).to_bytes(1,"big")) for j in range(len(key))] ) for i in range(len(flag))]

enc_flag=repr(b''.join(enc_flag))
output_file='output.txt'
open(output_file,'w').write(enc_flag)
	
```
so here's a nice reference to understand how functools' reduce function works[and how the encoder works in general?]: https://www.python-course.eu/python3_lambda.php

now it's clear that we need to use Input2 and Output2 to get the key and then use the key to get the flag
so i made this script to get the key
```py
from z3 import * 
import functools

Input2 = "Lorem ipsum dolor sit amet, consectetur adipiscing elit"
output2= b':\x1c\x10\x07ZV\x1a\x12\x11B\x1bS\x06\r[\x19\x01B\x11^\x02S\x03\x0fR\x02_B\x01X\x18\x00\x07\x01C\x13\x07\x17\x10\x17\x17\x17\x0b\x12^\x05\x10\x0b\x0cPV\x16\x0e\x0bC'
xor_two_bytes=lambda a,b:(a^b)
s=Solver()
key = [BitVec("key{}".format(i),8) for i in range(15)]
key2=[key[i::3] for i in range(3)]

constraints= [functools.reduce(xor_two_bytes,[((ord(Input2[i])&(~key2[j][i%5]))|((~ord(Input2[i]))&(key2[j][i%5]))) for j in range(len(key2))] ) for i in range(len(Input2))]

for i in range(len(constraints)):
	s.add(constraints[i]==output2[i])

if(s.check()==sat):
	key_=[]
	solution = s.model()
	for c in key:
		key_.append(s.model()[c].as_long())
# key_ = [27, 253, 144, 60, 191, 240, 60, 191, 225, 10, 31, 119, 58, 191, 178]

```
now that we have the key here's another z3 script to get the flag
```py
from z3 import * 
import functools

enc_flag = b'\x02\x19\x01\x16Q\r\x07\nS\x02)\x1a1=EE2\x0e=G/D\nRY)\nV\x1bJ'
key = [27, 253, 144, 60, 191, 240, 60, 191, 225, 10, 31, 119, 58, 191, 178]

xor_two_bytes=lambda a,b:(a^b)

s=Solver()
key = [key[i::3] for i in range(3)]
flag = [BitVec("flag{}".format(i),8) for i in range(30)] #because the enc flag is 30 bytes long

constraints = [functools.reduce(xor_two_bytes,[(((flag[i])&(~key[j][i%5]))|((~(flag[i]))&(key[j][i%5]))) for j in range(len(key))] ) for i in range(len(flag))]
for i in range(len(enc_flag)):
	s.add( constraints[i] == enc_flag[i])
if (s.check()==sat):
	f=""
	solution = s.model()
	for e in flag:
		f+= chr(solution[e].as_long())
	print(f)

```
# Flag

```tjctf{th15_iS_r3Al_pY7h0n_y4y}```
