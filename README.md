# Flexible-Jekyll is a simple and clean theme for Jekyll

![](https://github.com/artemsheludko/flexible-jekyll/blob/master/assets/img/promo-img.jpg?raw=true)

## Demo

Check the theme in action [Demo](https://artemsheludko.github.io/flexible-jekyll/)

The main page would look like this:

![Main page preview](https://github.com/artemsheludko/flexible-jekyll/blob/master/assets/img/home-page.jpg?raw=true)

The post page would look like this:

![Post page preview](https://github.com/artemsheludko/flexible-jekyll/blob/master/assets/img/post-example.jpg?raw=true)

## Features

- [Google Fonts](https://fonts.google.com/)
- [Font Awesome](http://fontawesome.io/)
- [Disqus](https://disqus.com/)
- [Analytics](https://analytics.google.com/analytics/web/)
- Support Emoji

## Installation:

Fork the ``master`` branch and follow the [Jekyll Installation Documentation](https://jekyllrb.com/docs/installation/).

## License

GNU General Public License v3.0

## Donate

<p>If you like the themes that I create you can become my sponsor on <a href="https://www.patreon.com/artemsheludko" target="_blank">Patreon</a>.
<p align="center"><b>Thank you for your support ❤️</b></p>


## Installation Detail

1. Install Homebrew   
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

2. Install rbenv
```
brew install rbenv ruby-build
# rbenv 는 ruby의 버전을 독립적으로 관리해주는 패키지.
```
3. rbenv versions check and download ruby
```
# 설치 가능 버전 확인
rbenv install -l
# 2.6.3 설치
rbenv install 2.6.3
```

4. check ruby version and change global config
```
rbenv versions

# 나 같은 경우 버전 확인 시, 2.6.3을 system이 사용중이었으므로 변경 필요.
# *  system
#    2.6.3 (set by /Users/kds/.rbenv/version)

rbenv global 2.6.3
```

5. Set global config 
```
vi ~/.zshrc

export PATH={$Home}/.rbenv/bin:$PATH && \
eval "$(rbenv init -)"
```

6. Install jekyll / bundler by gem on ruby
```
gem install jekyll bundler
```

7. Fork master branch to my repository


8. Setting your local computer
git clone URL(your master branch on this theme)

9. bundle install in clone directory
```
bundle install
```

10. Start server
```
bundle exec jekyll serve
```
