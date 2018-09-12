---
title: Supporto SSH per il Servizio app di Azure in Linux | Microsoft Docs
description: Informazioni sull'uso di SSH con il Servizio app di Azure in Linux.
keywords: Servizio app di Azure, app Web, Linux, OSS
services: app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.openlocfilehash: 049ae78efee0b5ebdf6bfbe207f8c85d94e9238f
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44050069"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Supporto SSH per il Servizio app di Azure in Linux

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) viene usato comunemente per eseguire comandi amministrativi in remoto da un terminale della riga di comando. Il Servizio app in Linux offre il supporto SSH nel contenitore di app con ognuna delle immagini Docker predefinite usate per lo stack di runtime di nuove app Web. 

![Stack di runtime](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Per le immagini Docker personalizzate tramite la configurazione del server SSH in un'immagine personalizzata.

È anche possibile connettersi al contenitore direttamente dal computer di sviluppo locale tramite SSH e SFTP.

## <a name="open-ssh-session-in-browser"></a>Aprire una sessione SSH nel browser

Per stabilire una connessione client SSH con il contenitore, l'app deve essere in esecuzione.

Incollare l'URL seguente nel browser e sostituire \<nome_app > con il nome dell'app:

```
https://<app_name>.scm.azurewebsites.net/webssh/host
```

Se non lo si è già fatto, è necessario eseguire l'autenticazione con la sottoscrizione di Azure per la connessione. Al termine dell'autenticazione viene visualizzata una shell nel browser, in cui è possibile eseguire i comandi all'interno del contenitore.

![Connessione SSH](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="use-ssh-support-with-custom-docker-images"></a>Usare il supporto SSH con le immagini Docker personalizzate

Affinché un'immagine Docker personalizzata supporti la comunicazione SSH tra il contenitore e il client nel portale di Azure, eseguire la procedura seguente per l'immagine Docker.

Questa procedura viene mostrata nel repository del Servizio app di Azure come [esempio](https://github.com/Azure-App-Service/node/blob/master/6.9.3/).

1. Includere l'installazione `openssh-server` nell'[istruzione `RUN`](https://docs.docker.com/engine/reference/builder/#run) in Dockerfile per l'immagine e impostare la password per l'account radice su `"Docker!"`.

    > [!NOTE]
    > Questa configurazione non consente connessioni esterne al contenitore. SSH è accessibile solo tramite il sito Kudu/sito, che viene autenticato tramite le credenziali di pubblicazione.

    ```docker
    # ------------------------
    # SSH Server support
    # ------------------------
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

1. Aggiungere un'[istruzione `COPY`](https://docs.docker.com/engine/reference/builder/#copy) a Dockerfile per copiare un file [sshd_config](http://man.openbsd.org/sshd_config) nella directory */etc/ssh/*. Il file di configurazione deve essere basato sul file sshd_config disponibile in [questa pagina](https://github.com/Azure-App-Service/node/blob/master/8.2.1/sshd_config) del repository GitHub di Azure-App-Service.

    > [!NOTE]
    > Per fare in modo che la connessione abbia esito positivo, il file *sshd_config* deve includere quanto segue: 
    > * `Ciphers` deve includere almeno uno dei seguenti: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` deve includere almeno uno dei seguenti: `hmac-sha1,hmac-sha1-96`.

    ```docker
    COPY sshd_config /etc/ssh/
    ```

1. Includere la porta 2222 nell'[istruzione `EXPOSE`](https://docs.docker.com/engine/reference/builder/#expose) per Dockerfile. Anche se la password radice è nota, la porta 2222 non è accessibile da Internet. È una porta interna accessibile solo dai contenitori all'interno della rete bridge di una rete virtuale privata.

    ```docker
    EXPOSE 2222 80
    ```

1. Avviare il servizio SSH usando uno script della shell (vedere l'esempio in [init_container.sh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh)).

    ```bash
    #!/bin/bash
    service ssh start
    ```

Dockerfile usa l'[istruzione `ENTRYPOINT`](https://docs.docker.com/engine/reference/builder/#entrypoint) per eseguire lo script.

    ```docker
    COPY init_container.sh /opt/startup
    ...
    RUN chmod 755 /opt/startup/init_container.sh
    ...
    ENTRYPOINT ["/opt/startup/init_container.sh"]
    ```

## <a name="open-ssh-session-from-remote-shell"></a>Aprire una sessione SSH dalla shell remota

> [!NOTE]
> Questa funzionalità è attualmente disponibile in anteprima.
>

Con il tunneling TCP è possibile creare una connessione di rete tra il computer di sviluppo e l'app Web per contenitori mediante una connessione WebSocket autenticata. Consente di aprire una sessione SSH con il contenitore in esecuzione nel servizio app dal client scelto.

Per iniziare, è necessario installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Per esaminare il funzionamento senza installare l'interfaccia della riga di comando di Azure, aprire [Azure Cloud Shell](../../cloud-shell/overview.md). 

Aggiungere l'estensione del servizio app più recente eseguendo [az extension add](/cli/azure/extension?view=azure-cli-latest#az-extension-add):

```azurecli-interactive
az extension add --name webapp
```

Se è già stato eseguito il comando `az extension add` in precedenza, eseguire invece [az extension update](/cli/azure/extension?view=azure-cli-latest#az-extension-update):

```azurecli-interactive
az extension update --name webapp
```

Aprire una connessione remota all'app usando il comando [az webapp remote-connection create](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create). Specificare ID _\<sottoscrizione\_>_, _\<nome\_gruppo>_ e \_<nome\_app>_ per l'app e sostituire \<porta> con un numero di porta locale.

```azurecli-interactive
az webapp remote-connection create --subscription <subscription_id> --resource-group <group_name> -n <app_name> -p <port> &
```

> [!TIP]
> Il carattere `&` alla fine del comando serve solo per comodità, se si usa Cloud Shell. Esegue il processo in background in modo che sia possibile eseguire il comando successivo nella stessa shell.

L'output del comando include le informazioni necessarie per aprire una sessione SSH.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Aprire una sessione SSH con il contenitore tramite il client scelto usando la porta locale. Nell'esempio seguente si usa il comando [ssh](https://ss64.com/bash/ssh.html) predefinito:

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

Quando viene richiesto, digitare `yes` per continuare con la connessione. Verrà richiesto di specificare la password. Usare il valore `Docker!` che è stato illustrato in precedenza.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

Al termine dell'autenticazione verrà visualizzata la schermata iniziale della sessione.

```
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  >
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
```

A questo punto si è connessi al connettore. 

Provare a eseguire il comando [top](https://ss64.com/bash/top.html). Dovrebbe essere visibile il processo dell'app nell'elenco di processi. Nell'esempio seguente è quello con `PID 263`.

```
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
```

## <a name="next-steps"></a>Passaggi successivi

È possibile pubblicare eventuali domande e dubbi nel [forum di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

Per altre informazioni sull'app Web per contenitori, vedere:

* [Introducing remote debugging of Node.js apps on Azure App Service from VS Code](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0) (Introduzione al debug remoto delle app Node.js nel Servizio app di Azure da Visual Studio Code)
* [Come usare un'immagine Docker personalizzata per l'app Web per contenitori](quickstart-docker-go.md)
* [Uso di .NET Core nel Servizio app di Azure in Linux](quickstart-dotnetcore.md)
* [Uso di Ruby in Servizio app di Azure in Linux](quickstart-ruby.md)
* [Azure App Service Web App for Containers FAQ (Domande frequenti sulle app Web per contenitori del servizio app di Azure)](app-service-linux-faq.md)
