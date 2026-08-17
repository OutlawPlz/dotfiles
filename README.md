# dotfiles

Configurazione personale per workstation Ubuntu e WSL Ubuntu.

Il repository contiene configurazioni riproducibili e una lista esplicita delle
dipendenze di sistema. Credenziali, segreti e stato generato dagli strumenti non
devono essere versionati.

## Installazione

Clonare il repository e avviare il bootstrap:

```bash
git clone <url-del-repository> ~/dev/dotfiles
cd ~/dev/dotfiles
./install.sh
```

Lo script:

1. installa `mise` per l'utente, se non è già disponibile;
2. collega la configurazione globale di `mise` in `~/.config/mise/config.toml`;
3. aggiunge l'attivazione di `mise` a `~/.bashrc`, se non è già presente;
4. installa o aggiorna gli strumenti dichiarati in `mise/config.toml`.

La configurazione `mise` esistente viene salvata con suffisso
`.before-dotfiles` prima di essere sostituita. Lo script può essere eseguito più
volte.

Dopo l'installazione, aprire una nuova shell oppure eseguire:

```bash
source ~/.bashrc
```

## Pacchetti Ubuntu

I pacchetti di sistema non sono gestiti dallo script. Su una nuova installazione
Ubuntu o WSL Ubuntu si possono installare manualmente con:

```bash
sudo apt update
sudo apt install bash-completion build-essential caddy curl git unzip
```

## Strumenti gestiti da mise

Gli strumenti globali sono dichiarati in [`mise/config.toml`](mise/config.toml).
Attualmente sono gestiti:

- `fd`;
- `fzf`;
- `gh`;
- `glab`;
- `intelephense`;
- `jq`;
- `lua-language-server`;
- `neovim`;
- `node`;
- `rg`;
- `stylua`;
- `typescript-language-server`;
- `tree-sitter`.

Per installarli o aggiornarli:

```bash
mise install
mise upgrade
```

Per aggiungere uno strumento globale e aggiornare il repository:

```bash
mise use --global <strumento>@latest
```

Dopo il bootstrap, la configurazione globale è un collegamento al file nel
repository: il comando modifica direttamente `mise/config.toml`.

## git-mr

`git-mr` è uno script personale che crea merge request GitLab tramite `glab` e
può essere invocato come sottocomando Git:

```bash
git mr
```

Lo script è versionato in [`bin/git-mr`](bin/git-mr) e il bootstrap lo collega
a `~/.local/bin/git-mr`. Richiede `git` e `glab`. La configurazione può essere
impostata nel singolo repository:

```bash
git config --local mr.assignee <username>
git config --local mr.target <branch>
git config --local mr.squash true
git config --local mr.removeSourceBranch true
```

Per vedere tutte le opzioni disponibili:

```bash
git mr --help
```

## Caddy

La configurazione di Caddy è specifica della macchina e non viene versionata.
Per esporre servizi locali tramite reverse proxy, si possono aggiungere blocchi
come questi a `/etc/caddy/Caddyfile`:

```caddyfile
welfare.localhost:80 {
    reverse_proxy 127.0.0.1:8000
}

minio.localhost:80 {
    reverse_proxy 127.0.0.1:9001
}
```

Dopo una modifica, validare la configurazione e ricaricare il servizio:

```bash
sudo caddy validate --config /etc/caddy/Caddyfile
sudo systemctl reload caddy
```

## Dati esclusi

- token e credenziali;
- `~/.config/minio/env`;
- configurazioni proxy specifiche della macchina;
- cache e binari installati da `mise`;
- configurazione Git personale o aziendale.
