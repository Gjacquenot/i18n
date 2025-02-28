# Commandes Supportées

> Paramètres de ligne de commande pris en charge par Electron.

Vous pouvez utiliser [app.commandLine.appendSwitch](app.md#appcommandlineappendswitchswitch-value) pour l'ajouter au script principal de votre application avant que l'événement [ready](app.md#event-ready) du module [app](app.md) soit émis :

```javascript
const { app } = require('electron')
app.commandLine.appendSwitch('remote-debugging-port', '8315')
app.commandLine.appendSwitch('host-rules', 'MAP * 127.0.0.1')
app.on('ready', () => {
  // Votre code ici
})
```

## --ignore-connections-limit=`domains`

Ignore la limite de connexion pour la liste de `domains` séparés par `,`.

## --disable-http-cache

Désactive le cache disque pour les requêtes HTTP.

## --disable-http2

Désactive les protocoles HTTP/2 et SPDY/3.1.

## --lang

Permet de mettre une langue personnalisée.

## --inspect=`port` et --inspect-brk=`port`

Indicateurs relatifs au débogage, reportez-vous au guide [Déboguer le processus principal](../tutorial/debugging-main-process.md) pour plus de détails.

## --remote-debugging-port=`port`

Active le débogage distant via HTTP sur le `port` spécifié.

## --disk-cache-size=`size`

Force l'espace disque maximum à utiliser par le cache disque, en octets.

## --js-flags=`flags`

Specifies the flags passed to the Node.js engine. It has to be passed when starting Electron if you want to enable the `flags` in the main process.

```sh
$ electron --js-flags="--harmony_proxies --harmony_collections" votre-app
```

Consultez la documentation [Node.js](https://nodejs.org/api/cli.html) ou exécutez `node --help` dans votre terminal pour une liste des drapeaux disponibles. De plus, exécutez `node --v8-options` pour voir une liste de drapeaux qui se réfèrent spécifiquement au moteur JavaScript V8 de Node.js.

## --proxy-server=`address:port`

Utilise le serveur proxy spécifié, qui remplace le paramètre système. Cet indicateur n'affecte que les requêtes avec le protocole HTTP, y compris les requêtes HTTPS et WebSocket. Il est également intéressant de noter que tous les serveurs proxy ne supportent pas les requêtes HTTPS et WebSocket. L'url du proxy ne supporte pas l'authentification par nom d'utilisateur et mot de passe [selon un bug Chronium](https://bugs.chromium.org/p/chromium/issues/detail?id=615947).

## --proxy-bypass-list=`hosts`

Instructs Electron to bypass the proxy server for the given semi-colon-separated list of hosts. This flag has an effect only if used in tandem with `--proxy-server`.

Par exemple :

```javascript
const { app } = require('electron')
app.commandLine.appendSwitch('proxy-bypass-list', '<local>;*.google.com;*foo.com;1.2.3.4:5678')
```

Utilise le serveur proxy pour tous les hôtes sauf les adresses locales (`localhost`, `127.0.0.1`, etc.), les sous-domaines `google.com`, les hôtes qui contiennent le suffixe `foo. com` et tout ce qui est à `1.2.3.4:5678`.

## --proxy-pac-url=`url`

Utilise le script PAC à l'`url` spécifiée.

## --no-proxy-server

Don't use a proxy server and always make direct connections. Overrides any other proxy server flags that are passed.

## --host-rules=`rules`

Une liste séparée par des virgules de `rules` qui contrôle comment les noms d'hôtes sont mappés.

Par exemple :

* `MAP * 127.0.0.1` Force tous les noms d'hôtes à être mappés à 127.0.0.1
* `MAP *.google.com proxy` Force tous les sous-domaines google.com à être résolus en "proxy".
* `MAP test.com [::1]:77` Forces "test.com" to resolve to IPv6 loopback. Will also force the port of the resulting socket address to be 77.
* `MAP * baz, EXCLUDE www.google.com` Remappe tout à "baz", sauf pour "www.google.com".

Ces mappages s'appliquent à l'hôte ciblé dans une requête réseau (le résolveur de connexion et d'hôte TCP dans une connexion directe, et l'hôte `CONNECT` dans une connexion avec proxy HTTP, et l'hôte du point terminal dans une connexion proxy `SOCKS`).

## --host-resolver-rules=`rules`

Comme `--host-rules` mais ces `rules` ne s'appliquent qu'au résolveur hôte.

## --auth-server-whitelist=`url`

Une liste de serveurs séparés par des virgules pour lesquels l'authentification intégrée est activée.

Par exemple :

```sh
--auth-server-whitelist='*example.com, *foobar.com, *baz'
```

puis toute `url` finissant par `example.com`, `foobar.com`, `baz` se verra appliquer une authentification intégrée. Sans le préfixe `*` l'URL doit correspondre exactement.

## --auth-negotiate-delegate-whitelist=`url`

A comma-separated list of servers for which delegation of user credentials is required. Sans le préfixe `*` l'URL doit correspondre exactement.

## --ignore-certificate-errors

Ignore les erreurs relatives au certificat.

## --ppapi-flash-path=`path`

Définit le chemin (`path`) du plugin pepper flash.

## --ppapi-flash-version=`version`

Définit la `version` du plugin pepper flash.

## --log-net-log=`path`

Permet que les événements réseau net log soient sauvés et les écrit dans `path`.

## --disable-renderer-backgrounding

Empêche Chromium d'abaisser la priorité des processus de rendu des pages invisibles.

Ce commutateur est global à tous les processus de rendu, si vous voulez seulement désactiver l'ajustement d'une fenêtre, vous pouvez passer par la modification de [playing silent audio](https://github.com/atom/atom/pull/9485/files).

## --enable-logging

Envoie les traces de Chromium à la console.

Ce commutateur ne peut pas être utilisé dans `app.commandLine.appendSwitch` car il est pris en compte avant que l'app utilisateur soit chargée, mais vous pouvez activer la variable d'environnement `ELECTRON_ENABLE_LOGGING` pour obtenir le même résultat.

## --v=`log_level`

Gives the default maximal active V-logging level; 0 is the default. Normally positive values are used for V-logging levels.

Ce commutateur ne fonctionne que si `--enable-logging` est également fourni.

## --vmodule=`pattern`

Permet que les niveaux maximum par module de V-logging puisse dépasser la valeur donnée par `--v`. Par exemple `my_module=2,foo*=3` changera le niveau de logging pour tout le code source des fichiers `my_module.*` à 2 et `foo*.*` à 3.

Tout pattern contenant un slash ou un anti-slash sera testé pour tout le chemin et pas seulement le module. Par exemple `*/foo/bar/*=2` changera le niveau de log pour tout le code source des fichiers du dossier `foo/bar`.

Ce commutateur ne fonctionne que si `--enable-logging` est également fourni.

## --enable-api-filtering-logging

Enables caller stack logging for the following APIs (filtering events):
- `desktopCapturer.getSources()` / `desktop-capturer-get-sources`
- `remote.require()` / `remote-require`
- `remote.getGlobal()` / `remote-get-builtin`
- `remote.getBuiltin()` / `remote-get-global`
- `remote.getCurrentWindow()` / `remote-get-current-window`
- `remote.getCurrentWebContents()` / `remote-get-current-web-contents`
- `remote.getGuestWebContents()` / `remote-get-guest-web-contents`

## --no-sandbox

Disables Chromium sandbox, which is now enabled by default. Should only be used for testing.
