# test-codespace

FROM python:3.10.4-slim-buster

## SO Dependencies 
RUN apt update && apt upgrade

RUN apt-get install --yes --no-install-recommends \ 
    curl git zsh sudo wget build-essential manpages-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*


## Create non root user
ARG USER_NAME="sebas"

ENV USER_NAME $USER_NAME

ARG USER_UID=1000

ARG USER_GID=$USER_UID

ARG USER_HOME=/home/$USER_NAME

## Create user group

RUN groupadd --gid $USER_GID $USER_NAME

## Create user

RUN adduser --quiet --disabled-password \
  --uid $USER_UID --gid $USER_GID \
  --shell /bin/zsh \
  --gecos "ZSH user" $USER_NAME

RUN echo $USER_NAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USER_NAME \
    && chmod 0440 /etc/sudoers.d/$USER_NAME

USER $USER_NAME

WORKDIR $USER_HOME

## Install oh-my-zsh

RUN wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh || true

ARG ZSH_CUSTOM=$USER_HOME/.oh-my-zsh/custom

# install oh-my-zsh plugins and theme
RUN \
  ZSH_PLUGINS=$ZSH_CUSTOM/plugins \
  && ZSH_THEMES=$ZSH_CUSTOM/themes \
  && git clone --single-branch --branch '0.7.1' --depth 1 https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_PLUGINS/zsh-syntax-highlighting \
  && git clone --single-branch --branch 'v0.6.4' --depth 1 https://github.com/zsh-users/zsh-autosuggestions.git $ZSH_PLUGINS/zsh-autosuggestions

WORKDIR /home/$USER_NAME

ENV PYTHONPATH=${PYTHONPATH}:${PWD}

ENV PATH "/home/${USER_NAME}/.local/bin:$PATH"

ENV PATH "/usr/local/bin:$PATH"

## Git config 
RUN git config --global user.name "Sebastian Parra Quintero"

RUN git config --global user.email "sebastian.parra96@gmail.com"

RUN pip3 install poetry jupyter

RUN poetry config virtualenvs.in-project true

RUN jupyter notebook --generate-config

RUN sed -i 's/plugins/#plugins/g' $USER_HOME/.zshrc

RUN sed -i 's/ZSH_THEME=/#ZSH_THEME=/g' $USER_HOME/.zshrc

RUN sed -i 's/ZSH_HIGHLIGHT_HIGHLIGHTERS/#ZSH_HIGHLIGHT_HIGHLIGHTERS/g' $USER_HOME/.zshrc

RUN sed -i 's/ZSH_THEME=/#ZSH_THEME=/g' $USER_HOME/.zshrc

RUN echo "ZSH_THEME=\"eastwood\"" >> $USER_HOME/.zshrc

RUN echo "ZSH_HIGHLIGHT_HIGHLIGHTERS=(main brackets pattern cursor)" >> $USER_HOME/.zshrc

RUN echo "plugins=( colored-man-pages colorize command-not-found cp copypath copyfile dirhistory extract globalias z zsh-autosuggestions zsh-interactive-cd zsh-navigation-tools zsh-syntax-highlighting )" >> $USER_HOME/.zshrc

RUN echo "source ${USER_HOME}/.oh-my-zsh/oh-my-zsh.sh" >> $USER_HOME/.zshrc

ENTRYPOINT ["tail", "-f", "/dev/null"]

CMD ["zsh"]
