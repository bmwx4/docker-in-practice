
### 在mac上安装docker
```bash
$ brew update                           # Fetch latest version of homebrew and formula.
$ brew tap caskroom/cask                # Tap the Caskroom/Cask repository from Github using HTTPS.
$ brew search docker                    # Searches all known Casks for a partial or exact match.
$ brew cask info docker                 # Displays information about the given Cask
$ brew cask install docker              # Install the given cask.
$ brew cleanup                          # Remove any older versions from the cellar.
```

### shell 补齐,你懂得...
```bash
brew install bash-completion
brew install docker-completion
brew install docker-compose-completion
brew install docker-machine-completion
```

### 参考
https://docs.docker.com/docker-for-mac/install/
