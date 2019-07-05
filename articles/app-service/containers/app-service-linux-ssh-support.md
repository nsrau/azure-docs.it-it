---
title: Supporto SSH per il Servizio app in Linux - Azure | Microsoft Docs
description: Informazioni sull'uso di SSH con il Servizio app di Azure in Linux.
keywords: Servizio app di Azure, app Web, Linux, OSS
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: b54d5003f67a1bd79e1e52eef87df858bc68ade1
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551919"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Supporto SSH per il Servizio app di Azure in Linux

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) viene usato comunemente per eseguire comandi amministrativi in remoto da un terminale della riga di comando. Il Servizio app in Linux offre il supporto SSH nel contenitore di app con ognuna delle immagini Docker predefinite usate per lo stack di runtime di nuove app Web. 

![Stack di runtime](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Per le immagini Docker personalizzate tramite la configurazione del server SSH in un'immagine personalizzata.

È anche possibile connettersi al contenitore direttamente dal computer di sviluppo locale tramite SSH e SFTP.

## <a name="open-ssh-session-in-browser"></a>Aprire una sessione SSH nel browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>Usare il supporto SSH con le immagini Docker personalizzate

Visualizzare [configurare SSH in un contenitore personalizzato](configure-custom-container.md#enable-ssh).

## <a name="open-ssh-session-from-remote-shell"></a>Aprire una sessione SSH dalla shell remota

> [!NOTE]
> Questa funzionalità è attualmente disponibile in anteprima.
>

Con il tunneling TCP è possibile creare una connessione di rete tra il computer di sviluppo e l'app Web per contenitori mediante una connessione WebSocket autenticata. Consente di aprire una sessione SSH con il contenitore in esecuzione nel servizio app dal client scelto.

Per iniziare, è necessario installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Per esaminare il funzionamento senza installare l'interfaccia della riga di comando di Azure, aprire [Azure Cloud Shell](../../cloud-shell/overview.md). 

Aprire una connessione remota all'app usando il comando [az webapp remote-connection create](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create). Specificare  _\<id-sottoscrizione >_ ,  _\<gruppo-name >_ e \_ \<app-name > _ per l'app.

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
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
