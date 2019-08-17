---
title: Domande frequenti sul Servizio app in Linux |Microsoft Docs
description: Domande frequenti sul Servizio app di Azure in Linux.
keywords: Servizio app di Azure, app Web, domande frequenti, linux, oss, app Web per contenitori, multi-contenitore, più contenitori
services: app-service
documentationCenter: ''
author: msangapu-msft
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 10452590c9415291cb2a5913aeef5c8a00cdfe12
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562987"
---
# <a name="azure-app-service-on-linux-faq"></a>Domande frequenti sul Servizio app di Azure in Linux

Con il lancio del Servizio app in Linux, sono previsti miglioramenti e nuove funzionalità per la piattaforma. Questo articolo fornisce le risposte alle domande che i clienti ci hanno posto di recente.

In caso di domande, inviare commenti su questo articolo.

## <a name="built-in-images"></a>Immagini predefinite

**Si desidera biforcare i contenitori Docker predefiniti forniti dalla piattaforma. Dove si trovano questi file?**

È possibile trovare tutti i file Docker su [GitHub](https://github.com/azure-app-service). È possibile trovare tutti i contenitori Docker nell'[hub Docker](https://hub.docker.com/u/appsvc/).

<a id="#startup-file"></a>

**Quali sono i valori previsti per la sezione relativa al file di avvio quando si configura lo stack di runtime?**

| Stack           | Valore previsto                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | comando per avviare l'app JAR (ad esempio, `java -jar my-app.jar --server.port=80`) |
| Tomcat, Wildfly | percorso di uno script per eseguire tutte le configurazioni necessarie (ad esempio, `/home/site/deployments/tools/startup_script.sh`)          |
| Node.js         | il file di configurazione PM2 o il file di script                                |
| .Net Core       | nome della DLL compilata come`dotnet <myapp>.dll`                                 |
| Ruby            | script Ruby con cui si vuole inizializzare l'app                     |

Questi comandi o script vengono eseguiti dopo l'avvio del contenitore Docker incorporato, ma prima dell'avvio del codice dell'applicazione.

## <a name="management"></a>Gestione

**Cosa accade quando viene premuto il pulsante di riavvio nel portale di Azure?**

Questa azione equivale a un riavvio di Docker.

**È possibile usare Secure Shell (SSH) per connettersi alla macchina virtuale (VM) del contenitore dell'app?**

Sì, è possibile farlo tramite il sito di gestione controllo del codice sorgente.

> [!NOTE]
> È anche possibile connettersi al contenitore di app direttamente dal computer di sviluppo locale tramite SSH, SFTP o Visual Studio Code (per eseguire il debug attivo di app Node.js). Per altre informazioni, vedere [Remote debugging and SSH in App Service on Linux](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html) (Debug remoto e SHH nel servizio app in Linux).
>

**Come è possibile creare un piano di servizio app Linux tramite un SDK o un modello di Azure Resource Manager?**

È necessario impostare il campo **reserved** del servizio app su *true*.

## <a name="continuous-integration-and-deployment"></a>Integrazione continua e distribuzione

**L'app Web usa ancora un'immagine obsoleta del contenitore Docker dopo l'aggiornamento dell'immagine su DockerHub. È supportata l'integrazione e distribuzione continua di contenitori personalizzati?**

Sì, per configurare l'integrazione o la distribuzione continua per Registro Azure Container o le immagini di DockerHub, vedere [Distribuzione continua con l'app Web per contenitori](./app-service-linux-ci-cd.md). Per registri privati, è possibile aggiornare il contenitore arrestando e riavviando l'app Web. Oppure è possibile modificare o aggiungere un'impostazione dell'applicazione fittizia per forzare l'aggiornamento del contenitore.

**Gli ambienti di gestione temporanea sono supportati?**

Sì.

**È possibile usare *WebDeploy/MSDeploy* per distribuire l'app Web?**

Sì, è necessario impostare `WEBSITE_WEBDEPLOY_USE_SCM` nell'app su *false*.

**La distribuzione in GIT dell'applicazione non riesce quando si usa l'app Web di Linux. Come è possibile risolvere il problema?**

Se la distribuzione in GIT non riesce per l'app Web di Linux, scegliere una delle opzioni seguenti per distribuire il codice dell'applicazione:

- Usare la funzionalità Recapito continuo (anteprima): Per usare il recapito continuo di Azure, è possibile archiviare il codice sorgente dell'app in un repository git DevOps di Azure o in un repository GitHub. Per altre informazioni, vedere [How to configure Continuous Delivery for Linux web app](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/) (Come configurare Recapito continuo per app Web in Linux).

- Usare la [l'API per la distribuzione ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): per usare questa API, attivare una connessione [SSH nell'app Web](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support) e passare alla cartella in cui si vuole distribuire il codice. Eseguire il codice seguente:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Se si verifica un errore che indica l'impossibilità di trovare il comando `curl`, assicurarsi di installare curl tramite `apt-get install curl` prima di eseguire il comando `curl` precedente.

## <a name="language-support"></a>Supporto per la lingua

**Esistono impostazioni speciali o configurazioni specifiche da impostare se si vogliono usare Web Socket nell'applicazione Node.js?**

Sì, disabilitare `perMessageDeflate` nel codice Node.js sul lato server. Ad esempio, per socket.io usare il codice seguente:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**È presente il supporto per le app .NET Core non compilate?**

Sì.

**È previsto il supporto per lo strumento Composer come gestore delle dipendenze per le app PHP?**

Sì, durante una distribuzione Git, Kudu rileverà che si sta distribuendo un'applicazione PHP (grazie alla presenza di un file composer.lock) e Kudu attiverà l'installazione di Composer.

## <a name="custom-containers"></a>Contenitori personalizzati

**Uso un contenitore personalizzato. Desidero che la piattaforma monti una condivisione SMB nella directory `/home/`.**

Se `WEBSITES_ENABLE_APP_SERVICE_STORAGE` l'impostazione non è **specificata** o èimpostata su true `/home/` , la directory **verrà condivisa** tra le istanze della scala e i file scritti **verranno mantenuti** tra i riavvii. Se si imposta `WEBSITES_ENABLE_APP_SERVICE_STORAGE` in modo esplicito su *false* , il montaggio viene disabilitato.

**L'avvio del contenitore personalizzato richiede molto tempo e la piattaforma riavvia il contenitore prima del completamento di questa operazione. Come si risolve il problema?**

È possibile configurare il tempo di attesa della piattaforma prima del riavvio del contenitore. A tale scopo impostare `WEBSITES_CONTAINER_START_TIME_LIMIT` nell'app sul valore desiderato. Il valore predefinito è 230 secondi e il valore massimo è 1800 secondi.

**Qual è il formato dell'URL del server del Registro di sistema privato?**

È necessario immettere l'URL completo del registro, incluso `http://` o `https://`.

**Qual è il formato per il nome dell'immagine nell'opzione del Registro di sistema privato?**

Aggiungere il nome dell'immagine completa, incluso l'URL del registro privato (ad esempio myacr.azurecr.io/dotnet:latest). I nomi di immagine che usano una porta personalizzata [non possono essere inseriti tramite il portale](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Per impostare `docker-custom-image-name` usare lo [`az`strumento dalla riga di comando](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set).

**È possibile esporre più di una porta sull'immagine del contenitore personalizzato?**

Non è supportata l'esposizione di più di una porta.

**È possibile usare la propria archiviazione?**

Sì, l'opzione che consente di [usare la propria archiviazione](https://docs.microsoft.com/azure/app-service/containers/how-to-serve-content-from-azure-storage) è disponibile in anteprima.

**Perché non è possibile accedere al file system del contenitore personalizzato o ai processi in esecuzione dal sito SCM?**

Il sito SCM viene eseguito in un contenitore separato. Non è possibile controllare il file system o i processi in esecuzione del contenitore dell'app.

**Il contenitore personalizzato è in attesa su una porta diversa dalla porta 80. Come si può configurare l'app in modo da indirizzare le richieste a questa porta?**

È disponibile il rilevamento delle porte automatico. È anche possibile specificare un'impostazione dell'app denominata *WEBSITES_PORT* e assegnare a tale impostazione il valore del numero di porta previsto. In precedenza la piattaforma utilizzava l'impostazione di app *PORT*. Stiamo pianificazione di deprecare questa impostazione di app e usare esclusivamente *WEBSITES_PORT*.

**È necessario implementare HTTPS nel contenitore personalizzato?**

No, la piattaforma gestisce l'interruzione HTTPS a livello dei server front-end condivisi.

## <a name="multi-container-with-docker-compose"></a>Più contenitori con Docker Compose

**Come si configura Registro Azure Container da usare con più contenitori?**

Per usare Registro Azure Container con più contenitori, **tutte le immagini del contenitore** devono essere ospitate nello stesso server di Registro Azure Container. Una volta che si trovano nello stesso server del registro di sistema, sarà necessario creare le impostazioni dell'applicazione e quindi aggiornare il file di configurazione Docker Compose per includere il nome dell'immagine ACR.

Definire le impostazioni dell'applicazione seguenti:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (URL completo, ad esempio `https://<server-name>.azurecr.io`:)
- DOCKER_REGISTRY_SERVER_PASSWORD (abilitare l'accesso di amministratore nelle impostazioni di Registro Azure Container)

Nel file di configurazione fare riferimento all'immagine di Registro Azure Container come nell'esempio seguente:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Come è possibile sapere quale il contenitore è accessibile tramite Internet?**

- Solo un contenitore può essere aperto per l'accesso
- Solo le porte 80 e 8080 sono accessibili (porte esposte)

Di seguito vengono indicate le regole per determinare quale contenitore è accessibile (in ordine di precedenza):

- Impostazione dell'applicazione `WEBSITES_WEB_CONTAINER_NAME` definita sul nome del contenitore
- Il primo contenitore definito dalla porta 80 o 8080
- Se nessuna delle condizioni precedenti è soddisfatta, il primo contenitore definito nel file sarà accessibile (esposto)

## <a name="pricing-and-sla"></a>Prezzi e contratto di servizio

**Qual è il piano tariffario ora che il servizio è generalmente disponibile?**

Verrà addebitato il numero di ore di esecuzione dell'app con il normale prezzo del Servizio app di Azure.

## <a name="other-questions"></a>Altre domande

**Quali sono i caratteri supportati nei nomi delle impostazioni dell'applicazione?**

Per le impostazioni dell'applicazione è possibile usare solo lettere (A-Z, a-z), numeri (0-9) e il carattere di sottolineatura (_).

**Dove è possibile richiedere nuove funzionalità?**

È possibile inviare l'idea al [forum dei commenti App Web](https://aka.ms/webapps-uservoice). Aggiungere "[Linux]" al titolo dell'idea.

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è il Servizio app di Azure in Linux?](app-service-linux-intro.md)
- [Configurare ambienti di servizio App di Azure di staging](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Distribuzione continua con l'app Web per contenitori](./app-service-linux-ci-cd.md)
