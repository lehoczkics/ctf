# Crypto: PhaseStream2

## Info
The aliens have learned of a new concept called "security by obscurity". Fortunately for us they think it is a great idea and not a description of a common mistake. We've intercepted some alien comms and think they are XORing flags with a single-byte key and hiding the result inside 9999 lines of random data, Can you find the flag?

## Output pattern

```
$ head output.txt
3cc60a255dd328130e4203bb42f3be22d2935dbe5d9ebf498ce2
44e4088c49ce3aea69832d3c0a6cd43443ab1865daab8eab0fdc
bc0e3b0b7a600d5ff319ba661f6a077b058f1bd73c2c8f646c78
594a7cdfe5fe79edf5060c0ccd26304fd7bb9175f0ff6e6bc935
f807d7abd0cf8f82f56c22b59f1d22fcf1732163dcc4062a3f18
0d7fc2a812c0be988ef197bd7685876c8ff332f77dd5c8fb4ceb
5a04f0ecfa3b681930c29858f7e4f6f44f34c87f88533dd3ac17
93828080662b73d05deaf98e7a574b997f7e7c242619a541cb26
4b716313567479d19e64d0aa6794af8eac7d2e0c6f0475b7c0e6
947483e68b992c56db9bb7a9c89b1cee148539ed9745e9788512
```

## Code time

It is known the flag should start with "CHTB"\
It is also known that inverse operation of XOR is itself\
Using that info we will find the key and decode the flag.

```python
#!/usr/bin/env python3

# Flag text starts with "CHTB"
# Their ASCII codes are 67, 72, 84,66 respectively

# The key was 1 byte long, there is only 256 possible keys, let's collect them:
keys = []
for i in range(0,256):
    keys.append(i)

# Let us get the first 4 hex code from every line and XOR them with one of the keys.
# When the result matches "CHTB" then we will know which is the correct key and which line contains the flag.
with open("output.txt") as f:
    for line in f:
        myc = int(line[0:2], 16)
        myh = int(line[2:4], 16)
        myt = int(line[4:6], 16)
        myb = int(line[6:8], 16)
        # print(myc, myh, myt, myb)
        
        for mykey in keys:
            if myc ^ mykey == 67 and myh ^ mykey == 72 and myt ^ mykey == 84 and myb ^ mykey == 66 :
                goodkey = mykey
                goodline = line
                break
    print("Key: ", goodkey, "Line: ", goodline)

# All that remains is doing the XOR on all hexcodes of the line we found.
flag=[]

for j in range(0,26):
    myhexstr = goodline[2*j:2*j+2] # extract two characters from the good line
    mybyte = int(myhexstr, 16) # convert it to a decimal number
    myascii = mybyte ^ goodkey # XOR it wit the key
    flag.append(chr(myascii))

print("FLAG: ", ''.join(flag))
```

## Run it

```
$ python3 flag.py      
Key:  69 Line:  060d11073e2b76762129761a742b1a711a2d713c363171262e38

FLAG:  CHTB{n33dl3_1n_4_h4yst4ck}
```
