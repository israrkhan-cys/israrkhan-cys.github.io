---
title: "Automating fresh linux setup"
date: 2025-03-10
draft: false
tags: ["linux", "bash", "automation",]
summary: "Automating installation of all Applications and libraries on fresh linux "
coverImage: "cover.png"
---


# 🐧 Ubuntu Fresh Setup

Actually the main purpose of the script was made the setup after fresh ubuntu or other linux installation made easy like we have to go to check the cammand for installation of the browser ,spotify , vs code , python other multiple apps and libraries just to make the linux usable Now i wrote this bash script to automate all of that stuff  

## What this script does

After running, your system will have:

 Development Tools

- GCC, G++, Make (build-essential)
- GDB (debugger)
- Python 3 + pip + venv
- Visual Studio Code

### A pre-created virtual environment: ~/myenv
Inside it:
- Jupyter Lab
- Jupyter Notebook
- NumPy, Pandas, Matplotlib

 Productivity & Daily Apps
- Google Chrome
- Spotify
- VLC
- Curl, Wget, Htop, Tree
- Vim, Nano
- Zip / Unzip
- Net-tools

 How to use
1. Clone the repository
```
#first install git 
sudo apt install git

git clone https://github.com/israrkhan-cys/ubuntu_fresh_setup.git
cd ubuntu_fresh_setup

3. Make the script executable
sudo chmod +x setup.sh

4. Run it
sudo ./setup.sh
```

## ⚠️ Notes

Run only on fresh or clean systems also nothing whould happens if you run it on 
i have already Tested inside VirtualBox linux 

Pull requests are welcome.
Feel free to add tools, themes, or improvements.



