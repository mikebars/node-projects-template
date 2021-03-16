###########################################################################
# `node-projects`                                                         #
# `Container`                                                             #
# `Dockerfile`                                                            #
###########################################################################

# [image]

ARG DEVCONTAINER_IMAGE_REPOSITORY

ARG DEVCONTAINER_IMAGE_TAG_VARIANT

ARG DEVCONTAINER_IMAGE_TAG_VERSION

FROM ${DEVCONTAINER_IMAGE_REPOSITORY}:${DEVCONTAINER_IMAGE_TAG_VERSION}-${DEVCONTAINER_IMAGE_TAG_VARIANT} AS image

# [root]

FROM image AS root

ARG DEVCONTAINER_PACKAGE_APT_PACKAGE_LIST

ARG DEVCONTAINER_PACKAGE_OH_MY_ZSH_PLUGINS

ARG DEVCONTAINER_PACKAGE_OH_MY_ZSH_THEME

ARG DEVCONTAINER_USER_GID

ARG DEVCONTAINER_USER_GROUP

ARG DEVCONTAINER_USER_NAME

ARG DEVCONTAINER_USER_UID

RUN \
    # Set the UID and GID for the `DEVCONTAINER_USER_NAME` user
    groupmod --gid ${DEVCONTAINER_USER_GID} ${DEVCONTAINER_USER_NAME} \
    && usermod --gid ${DEVCONTAINER_USER_GID} --uid ${DEVCONTAINER_USER_UID} ${DEVCONTAINER_USER_NAME} \
    && chown --recursive ${DEVCONTAINER_USER_UID}:${DEVCONTAINER_USER_GID} /home/${DEVCONTAINER_USER_NAME} \
    # Download git-lfs
    && curl --silent https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | bash \
    # Install packages
    && apt-get update \
    && export DEBIAN_FRONTEND=noninteractive \
    && apt-get --assume-yes install --no-install-recommends git-lfs sudo zsh ${DEVCONTAINER_PACKAGE_APT_PACKAGE_LIST} \
    # Add sudo support for the `DEVCONTAINER_USER_NAME` user
    && echo ${DEVCONTAINER_USER_NAME} ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/${DEVCONTAINER_USER_NAME} \
    && chmod a+rwx,u-wx,g-wx,o-rwx,ug-s,-t /etc/sudoers.d/${DEVCONTAINER_USER_NAME} \
    # Install git-lfs
    && git lfs install \
    # Upgrade npm
    && npm install --force --global --unsafe-perm npm@latest

# [user]

FROM root AS user

ARG DEVCONTAINER_PACKAGE_APT_PACKAGE_LIST

ARG DEVCONTAINER_PACKAGE_OH_MY_ZSH_PLUGINS

ARG DEVCONTAINER_PACKAGE_OH_MY_ZSH_THEME

ARG DEVCONTAINER_USER_GID

ARG DEVCONTAINER_USER_GROUP

ARG DEVCONTAINER_USER_NAME

ARG DEVCONTAINER_USER_UID

USER ${DEVCONTAINER_USER_NAME}

RUN \
    # Install oh-my-zsh
    git clone https://github.com/ohmyzsh/ohmyzsh.git ~/.oh-my-zsh \
    && cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc \
    && sed --in-place "s/plugins=(git)/plugins=${DEVCONTAINER_PACKAGE_OH_MY_ZSH_PLUGINS}/g" ~/.zshrc \
    && sed --in-place "s/ZSH_THEME=\"robbyrussell\"/ZSH_THEME=\"${DEVCONTAINER_PACKAGE_OH_MY_ZSH_THEME}\"/g" ~/.zshrc
