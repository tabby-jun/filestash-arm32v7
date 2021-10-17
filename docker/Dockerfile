FROM debian:stable-slim

ARG TAG

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update > /dev/null && \
    #################
    # Install
    apt-get install -y libglib2.0-0 curl > /dev/null && \
    cd /tmp/ && \
    curl -H "Accept: application/vnd.github.v3+json" -s https://api.github.com/repos/tabby-jun/filestash-arm32v7/releases/tags/$TAG \
    | grep "browser_download_url.*tar.gz" \
    | cut -d : -f 2,3 \
    | xargs -n1 curl -sL \
    | tar zxf - && \
    mv filestash /app/ && \
    apt-get purge -y --auto-remove && \
    #################
    # Optional dependencies
    apt-get install -y curl tor emacs-nox ffmpeg zip poppler-utils xz > /dev/null && \
    # org-mode: html export
    curl https://raw.githubusercontent.com/mickael-kerjean/filestash/master/server/.assets/emacs/htmlize.el > /usr/share/emacs/site-lisp/htmlize.el && \
    # org-mode: markdown export
    curl https://raw.githubusercontent.com/mickael-kerjean/filestash/master/server/.assets/emacs/ox-gfm.el > /usr/share/emacs/site-lisp/ox-gfm.el && \
    # org-mode: pdf export (with a light latex distribution)
    cd && apt-get install -y wget perl > /dev/null && \
    export CTAN_REPO="http://mirror.las.iastate.edu/tex-archive/systems/texlive/tlnet" && \
    curl -sL "https://yihui.org/gh/tinytex/tools/install-unx.sh" | sh && \
    mv ~/.TinyTeX /usr/share/tinytex && \
    /usr/share/tinytex/bin/armhf-linux/tlmgr install wasy && \
    /usr/share/tinytex/bin/armhf-linux/tlmgr install ulem && \
    /usr/share/tinytex/bin/armhf-linux/tlmgr install marvosym && \
    /usr/share/tinytex/bin/armhf-linux/tlmgr install wasysym && \
    /usr/share/tinytex/bin/armhf-linux/tlmgr install xcolor && \
    /usr/share/tinytex/bin/armhf-linux/tlmgr install listings && \
    /usr/share/tinytex/bin/armhf-linux/tlmgr install parskip && \
    /usr/share/tinytex/bin/armhf-linux/tlmgr install float && \
    /usr/share/tinytex/bin/armhf-linux/tlmgr install wrapfig && \
    /usr/share/tinytex/bin/armhf-linux/tlmgr install sectsty && \
    /usr/share/tinytex/bin/armhf-linux/tlmgr install capt-of && \
    /usr/share/tinytex/bin/armhf-linux/tlmgr install epstopdf-pkg && \
    /usr/share/tinytex/bin/armhf-linux/tlmgr install cm-super && \
    ln -s /usr/share/tinytex/bin/armhf-linux/pdflatex /usr/local/bin/pdflatex && \
    apt-get purge -y --auto-remove perl wget && \
    # Cleanup
    find /usr/share/ -name 'doc' | xargs rm -rf && \
    find /usr/share/emacs -name '*.pbm' | xargs rm -f && \
    find /usr/share/emacs -name '*.png' | xargs rm -f && \
    find /usr/share/emacs -name '*.xpm' | xargs rm -f && \
    #################
    # Finalise the image
    useradd filestash && \
    chown -R filestash:filestash /app/ && \
    rm -rf /var/lib/apt/lists/* && \
    rm -rf /tmp/*

USER filestash
RUN timeout 1 /app/filestash | grep -q start

EXPOSE 8334
VOLUME ["/app/data/state/"]
WORKDIR "/app"
CMD ["/app/filestash"]
