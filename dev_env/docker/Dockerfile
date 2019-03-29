FROM ubuntu

MAINTAINER John Louie jlouie707@gmail.com

# Set up important dev layers
RUN apt-get update
RUN apt-get install -qy curl git python tmux vim
RUN git config --global user.email "jlouie707@gmail.com"
RUN git config --global user.name "John Louie"

WORKDIR /home

# Clone git repo
RUN git clone https://github.com/jwlouie/jwlouie.github.io.git

# Set up Jekyll environment
RUN apt-get install -qy ruby-full build-essential zlib1g-dev
RUN echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc && \
    echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc && \
    echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
RUN gem install jekyll bundler

# Set up Jekyll theme
ENV LC_ALL C.UTF-8
RUN apt-get install -qy nodejs npm
RUN git clone https://github.com/nielsenramon/chalk.git
RUN cp -r /home/chalk/* /home/jwlouie.github.io
WORKDIR /home/jwlouie.github.io
RUN bundle install && npm run setup
# RUN npm run local

# WORKDIR /home/jwlouie.github.io

ENTRYPOINT ["/bin/bash"]
