# praqma
A PRAGMATIC WORKFLOW

This is a test repo to try out http://www.praqma.com/stories/a-pragmatic-workflow/

Which uses git, ghi and waffle and Jenkins to integrate changes and move status

What you will need 

- assuming a mac and homebrew

`brew install rbenv ruby-build`

- Add rbenv to bash so that it loads every time you open a terminal
```bash
echo 'if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi' >> ~/.bash_profile
source ~/.bash_profile

# Install Ruby
rbenv install 2.3.1
rbenv global 2.3.1
ruby -v
```

https://gorails.com/setup/osx/10.11-el-capitan

- Fix ruby gems for our neurotic firewall
```bash
gem sources -r https://rubygems.org/
gem sources -a http://rubygems.org/
```
http://stackoverflow.com/questions/10246023/bundle-install-fails-with-ssl-certificate-verification-error

- config
- 
```bash
ghi config --auth YOUR_GITHUB_USER
```


