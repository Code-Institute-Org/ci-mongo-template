FROM ubuntu:20.04

ENV PYTHON_VERSION 3.8.11
ENV DEBIAN_FRONTEND=noninteractive
#Install pyenv and mongo shell dependencies
RUN apt-get update && apt-get install -y gnupg2
RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 20691eec35216c63caf66ce1656408e390cfb1f5 \
    && sh -c 'echo "deb http://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse" | tee /etc/apt/sources.list.d/mongodb-org-4.4.list' \
    && apt-get update -y \ 
    && apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget ca-certificates curl llvm libncurses5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev mecab-ipadic-utf8 git mongodb-org-shell links

RUN curl https://cli-assets.heroku.com/install.sh | sh

ARG USERNAME=codeany
RUN useradd -ms /bin/bash $USERNAME
USER $USERNAME
WORKDIR /home/$USERNAME

# Set-up necessary Env vars for PyEnv
ENV PYENV_ROOT /home/$USERNAME/.pyenv
ENV PATH $PYENV_ROOT/shims:$PYENV_ROOT/bin:$PATH

# Install pyenv
RUN set -ex \
    && curl https://pyenv.run | bash \
    && pyenv update \
    && pyenv install $PYTHON_VERSION \
    && pyenv global $PYTHON_VERSION \
    && pyenv rehash \
    && python3 -m pip install --no-cache-dir --upgrade pip \
    && python3 -m pip install --no-cache-dir --upgrade setuptools wheel virtualenv pipenv pylint rope flake8  mypy autopep8 pep8 pylama pydocstyle bandit notebook twine

RUN echo 'alias python=python3' >> ~/.bashrc && \
    echo 'export PIP_USER=yes' >> ~/.bashrc && \
    echo 'alias pip=pip3' >> ~/.bashrc \
    echo 'alias mongosh=mongo' >> ~/.bashrc  

COPY ./build-assets/heroku_config.sh /home/$USERNAME/.theia/heroku_config.sh
RUN echo 'alias heroku_config=". $HOME/.theia/heroku_config.sh"' >> ~/.bashrc

COPY ./build-assets/make_url.py /home/$USERNAME/.theia/make_url.py
RUN echo 'alias make_url="python3 $HOME/.theia/make_url.py "' >> ~/.bashrc

USER root
RUN chown -R $USERNAME:$USERNAME /home/$USERNAME/.theia

CMD ["tail", "-f", "/dev/null"]