# Description
I zipped the file a bit too many times it seems... and I may have added passwords to some of the zip files... eh, they should be pretty common passwords right?
Hint! A script will help you with this, please don't try do this manually...
Hint! All passwords should be in the SecLists/Passwords/xato-net-10-million-passwords-100.txt file, which you can get here : https://github.com/danielmiessler/SecLists/blob/master/Passwords/xato-net-10-million-passwords-100.txt
or alternatively, use "git clone https://github.com/danielmiessler/SecLists"

---

```python
import os
import zipfile
from sh import tar,file ,unzip
import subprocess
from natsort import natsorted
pw_list = ['123456', 'password', '12345678', 'qwerty', '123456789', '12345', '1234', '111111', '1234567', 'dragon', '123123', 'baseball', 'abc123', 'football', 'monkey', 'letmein', '696969', 'shadow', 'master', '666666', 'qwertyuiop', '123321', 'mustang', '1234567890', 'michael', '654321', 'pussy', 'superman', '1qaz2wsx', '7777777', 'fuckyou', '121212', '000000', 'qazwsx', '123qwe', 'killer', 'trustno1', 'jordan', 'jennifer', 'zxcvbnm', 'asdfgh', 'hunter', '', 'buster', 'soccer', 'harley', 'batman', 'andrew', 'tigger', 'sunshine', 'iloveyou', 'fuckme', '2000', 'charlie', 'robert', 'thomas', 'hockey', 'ranger', 'daniel', 'starwars', 'klaster', '112233', 'george', 'asshole', 'computer', 'michelle', 'jessica', 'pepper', '1111', 'zxcvbn', '555555', '11111111', '131313', 'freedom', '777777', 'pass', 'fuck', 'maggie', '159753', 'aaaaaa', 'ginger', 'princess', 'joshua', 'cheese', 'amanda', 'summer', 'love', 'ashley', '6969', 'nicole', 'chelsea', 'biteme', 'matthew', 'access', 'yankees', '987654321', 'dallas', 'austin', 'thunder', 'taylor']
def unzip_with_pw(file):
	for pw in pw_list:
		try: 
			with zipfile.ZipFile(file) as zf:
				zf.extractall(pwd=bytes(pw,'utf-8'))
				zf.close()
				return
		except: 
			continue;


def unzip_without_pw(file):
	unzip(file)
	return

def untar(file):
	tar("-xf", file)
	return

def ungz(file):
	subprocess.call("7z e "+file,shell=True)
	return


def file_type(f1l3):
	extension = str(file(f1l3)).lower()
	if "was" in extension: 
		extension = str(file(f1l3)).lower()[33:]

	if "zip" in extension :
		extension = ".zip"
		
	elif "gz" in extension : 
		extension = ".gz"
		
	elif "tar" in extension : 
		extension = ".tar"

	elif "text" in extension:
		extension = ".txt"
	return extension

def rename_file(current_name,fileType):
	if "." not in current_name : new_name = current_name+filetype #extensionless
	else: new_name = current_name
	os.rename(current_name,new_name) 
	
def flag_exists():
	if any(file.endswith('.txt') for file in lst):
		for file in lst:
			if(file.endswith('.txt')): 
				f = open(file)
				print(f.read())
				f.close()
				return True
	else: return False

while(1):
	lst = natsorted(os.listdir())
	if flag_exists(): break;
	current_file = lst[0]
	filetype = file_type(current_file)
	rename_file(current_file,filetype)
	
	print("Unpacking : " +  current_file)

	if current_file.endswith(".zip"):
		try: 
			unzip_without_pw(current_file)
		except: 
			unzip_with_pw(current_file)

	elif current_file.endswith(".gz"):
		ungz(current_file)

	elif current_file.endswith(".tar"): 
		untar(current_file)

```



# Flag

`rtcp{z1pPeD_4_c0uPl3_t00_M4Ny_t1m3s_a1b8c687}`
