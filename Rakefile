# coding: utf-8
#!/usr/bin/ruby

task :default => [:xcode, :zshell, :mac_os, :brew, :cask, :computer_name, :vim_config, :git_config, :ssh_config, :nvm_install, :cask_configs]
task :continue => [:cask_configs, :computer_name]

def curl what
  sh "curl -O #{what}"
end

def brew what
  sh "brew install #{what}"
end

def cask what
  sh "brew cask install #{what}"
end

def in_dir dir
  pwd = Dir.pwd
  begin
    Dir.chdir dir
    yield if block_given?
  ensure
    Dir.chdir pwd
  end
end

def git_config setting, what
  sh "git config --global #{setting} #{what}"
end

def ask_for what
  print what
  STDIN.gets.strip
end

#### Download steps ####

desc "Installs xcode. Waits for input while installer is running."
task :xcode do
  begin
    sh "xcode-select --install"
  rescue
    puts "Looks like xcode failed... was it already installed?"
  ensure
    puts "Wait until xcode is installed... When done, press [ENTER] to continue."
    STDIN.gets.strip
  end

  begin
    puts "Now, let's accept the XCode licenses from command line!"
    sh "sudo xcodebuild -license accept"
  rescue
  end
end

desc "Installs Oh-my zshell"
task :zshell do
  sh "curl -L http://install.ohmyz.sh | sh" unless Dir.exists? "#{ENV['HOME']}/.oh-my-zsh"
  sh "chsh -s /bin/zsh"
end

def install_homebrew
  sh %{/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"} unless \
    Dir.exists? '/usr/local/Homebrew'
  sh "touch ~/.homebrew_analytics_user_uuid && chmod 000 ~/.homebrew_analytics_user_uuid"
end

task :install_homebrew do
  install_homebrew
end

def install_profiles
  sh "curl -L http://raw.github.com/caiogondim/bullet-train-oh-my-zsh-theme/master/bullet-train.zsh-theme -o ~/.oh-my-zsh/custom/themes/bullet-train.zsh-theme"
  sh "cp bullet-train.zsh-theme ~/.oh-my-zsh/custom/themes/"
  sh "cp .zshrc ~"
end

task :install_profiles do
  install_profiles
end

desc "Sets some macOS preferred settings"
task :mac_os do
  unless Dir.pwd.include?("macos") then
    sh "git clone https://github.com/haf/macos.git"
    in_dir "macos" do
      install_homebrew
      install_profiles
    end
  end
end

desc "Updates, upgrades and installs homebrew packages"
task :brew do
  sh "brew update"
  sh "brew upgrade"
  sh "brew cleanup"
  sh "brew tap homebrew/cask"
  sh "brew tap homebrew/cask-fonts"
  sh "brew tap homebrew/cask-versions"
  packages = %w|
    autoconf
    automake
    colordiff
    ctags
    direnv
    editorconfig
    fzf
    git
    git-lfs
    git-flow-avh
    gnupg
    gnutls
    go
    hasura-cli
    jq
    kubectl
    helm
    kustomize
    libtool
    libuv
    ngrep
    nmap
    nvm
    pinentry-mac
    popt
    pygments
    pyenv
    pipenv
    rbenv
    readline
    rsync
    skaffold
    stern
    thefuck
    travis
    tree
    ucspi-tcp
    watch
    zlib
  |.join(' ')
  brew packages

  sh "/usr/local/opt/fzf/install --no-bash --completion --key-bindings"
end

desc "Installs common casks"
task :cask do
  packages = %w|
    iterm2
    caffeine
    cleanmymac
    brave-browser
    discord
    docker
    dotnet-sdk
    eclipse-java
    firefox
    font-awesome-terminal-fonts
    font-monoid
    font-monoid-nerd-font
    font-roboto
    font-roboto-mono
    font-roboto-mono-for-powerline
    google-cloud-sdk
    gitkraken
    google-chrome
    jdownloader
    jetbrains-toolbox
    macvim
    microsoft-auto-update
    microsoft-office
    nordvpn
    omnigraffle
    postman
    qgis
    slack
    rectangle
    shottr
    spotify
    steam
    vagrant
    vagrant-manager
    vagrant-completion
    vagrant-vmware-utility
    virtualbox
    visual-studio-code
  |.join(' ')
  cask packages
end

desc "Configure the installed casks"
task :cask_configs do
  sh "mkdir -p ~/.iterm && cp com.googlecode.iterm2.plist ~/.iterm"

  sh %{mkdir -p "#{ENV['HOME']}/Library/Application Support/Code/User"}
  sh %{cp vscode.json "#{ENV['HOME']}/Library/Application Support/Code/User/settings.json"}
end

desc "Sets computer name. Asks for input"
task :computer_name do
  # Set computer name (as done via System Preferences → Sharing)
  computer_name = ask_for "Computer name: "
  sh "sudo scutil --set ComputerName '#{computer_name}'"
  sh "sudo scutil --set HostName '#{computer_name}'"
  sh "sudo scutil --set LocalHostName '#{computer_name}'"
  sh "sudo defaults write /Library/Preferences/SystemConfiguration/com.apple.smb.server NetBIOSName -string '#{computer_name.upcase}'"
  sh "sudo launchctl load -w /System/Library/LaunchDaemons/com.apple.locate.plist"
end

desc 'Configure vim'
task :vim_config do
  #in_dir ENV['HOME'] do
  #  system 'git clone https://github.com/amix/vimrc.git ~/.vim_runtime'
  #  in_dir '.vim_runtime' do
  #    sh 'chmod +x install_awesome_vimrc.sh && ./install_awesome_vimrc.sh'
  #  end
  #end
  sh "cp .vimrc ~/.vimrc"
end

desc "Sets minimum git config. Asks for input"
task :git_config do
  sh "cp .gitconfig ~/.gitconfig"
  git_config "core.editor", "/usr/bin/vim"
  git_config "push.default", "simple"

  user = ask_for "Git user name: "
  git_config "user.name", "'#{user}'"
  email = ask_for "Git user email: "
  git_config "user.email", "'#{email}'"
end

desc 'Configures SSH and its agent'
task :ssh_config do
  sh "mkdir -p ~/.ssh"
  begin
    puts "Please ensure you have 1Password synchronised and are ready to input ssh-key passphrases"
  rescue
    puts "Cancelled."
  ensure
    puts "Done."
  end
end

task :nvm_install do
  sh "./nvm-install-lts.sh"
end

task :macos_config do
  sh "./config-macos.sh"
end
