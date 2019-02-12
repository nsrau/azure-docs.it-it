---
title: Domande frequenti sul Servizio app in Linux |Microsoft Docs
description: Domande frequenti sul Servizio app di Azure in Linux.
keywords: Servizio app di Azure, app Web, domande frequenti, linux, oss, app Web per contenitori, multi-contenitore, più contenitori
services: app-service
documentationCenter: ''
author: yili
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: yili
ms.custom: seodec18
ms.openlocfilehash: a12d3708cdb547cc036b249bebf901d2ec5121c3
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55729320"
---
# <a name="azure-app-service-on-linux-faq"></a>Domande frequenti sul Servizio app di Azure in Linux

Con il lancio del Servizio app in Linux, sono previsti miglioramenti e nuove funzionalità per la piattaforma. Questo articolo fornisce le risposte alle domande che i clienti ci hanno posto di recente.

In caso di domande, inviare commenti su questo articolo.

## <a name="built-in-images"></a>Immagini predefinite

**Si desidera biforcare i contenitori Docker predefiniti forniti dalla piattaforma. Dove si trovano questi file?**

È possibile trovare tutti i file Docker su [GitHub](https://github.com/azure-app-service). È possibile trovare tutti i contenitori Docker nell'[hub Docker](https://hub.docker.com/u/appsvc/).

**Quali sono i valori previsti per la sezione relativa al file di avvio quando si configura lo stack di runtime?**

Per Node.js specificare il file di configurazione PM2 o il file script. Per .NET Core specificare il nome del file DLL compilato come `dotnet <myapp>.dll`. Per Ruby è possibile specificare uno script Ruby da usare per l'inizializzazione dell'app.

## <a name="management"></a>Gestione

**Cosa accade quando viene premuto il pulsante di riavvio nel portale di Azure?**

Questa azione equivale a un riavvio di Docker.

**È possibile usare Secure Shell (SSH) per connettersi alla macchina virtuale (VM) del contenitore dell'app?**

Sì, è possibile farlo tramite il sito di gestione controllo del codice sorgente.

> [!NOTE]
> È anche possibile connettersi al contenitore di app direttamente dal computer di sviluppo locale tramite SSH, SFTP o Visual Studio Code (per eseguire il debug attivo di app Node.js). Per altre informazioni, vedere [Remote debugging and SSH in App Service on Linux](https://aka.ms/linux-debug) (Debug remoto e SHH nel servizio app in Linux).
>

**Come è possibile creare un piano di servizio app Linux tramite un SDK o un modello di Azure Resource Manager?**

È necessario impostare il campo **reserved** del servizio app su *true*.

## <a name="continuous-integration-and-deployment"></a>Integrazione e distribuzione continue

**L'app Web usa ancora un'immagine obsoleta del contenitore Docker dopo l'aggiornamento dell'immagine su DockerHub. È supportata l'integrazione e distribuzione continua di contenitori personalizzati?**

Sì, per configurare l'integrazione o la distribuzione continua per Registro Azure Container o le immagini di DockerHub, vedere [Distribuzione continua con l'app Web per contenitori](./app-service-linux-ci-cd.md). Per registri privati, è possibile aggiornare il contenitore arrestando e riavviando l'app Web. Oppure è possibile modificare o aggiungere un'impostazione dell'applicazione fittizia per forzare l'aggiornamento del contenitore.

**Gli ambienti di gestione temporanea sono supportati?**

Sì.

**È possibile usare *WebDeploy/MSDeploy* per distribuire l'app Web?**

Sì, è necessario impostare `WEBSITE_WEBDEPLOY_USE_SCM` nell'app su *false*.

**La distribuzione in GIT dell'applicazione non riesce quando si usa l'app Web di Linux. Come è possibile risolvere il problema?**

Se la distribuzione in GIT non riesce per l'app Web di Linux, scegliere una delle opzioni seguenti per distribuire il codice dell'applicazione:

- Usare la funzionalità Recapito continuo (anteprima): è possibile archiviare il codice sorgente dell'app in un repository Git di Azure DevOps o un repository di GitHub per usare Recapito continuo di Azure. Per altre informazioni, vedere [How to configure Continuous Delivery for Linux web app](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/) (Come configurare Recapito continuo per app Web in Linux).

- Usare la [l'API per la distribuzione ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): per usare questa API, attivare una connessione [SSH nell'app Web](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support) e passare alla cartella in cui si vuole distribuire il codice. Eseguire il codice seguente:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Se si verifica un errore che indica l'impossibilità di trovare il comando `curl`, assicurarsi di installare curl tramite `apt-get install curl` prima di eseguire il comando `curl` precedente.

## <a name="language-support"></a>Supporto per le lingue

**Esistono impostazioni speciali o configurazioni specifiche da impostare se si vogliono usare Web Socket nell'applicazione Node.js?**

Sì, disabilitare `perMessageDeflate` nel codice Node.js sul lato server. Ad esempio, per socket.io usare il codice seguente:

```nodejs
var io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**È presente il supporto per le app .NET Core non compilate?**

Sì.

**È previsto il supporto per lo strumento Composer come gestore delle dipendenze per le app PHP?**

Sì, durante una distribuzione Git, Kudu rileverà che si sta distribuendo un'applicazione PHP (grazie alla presenza di un file composer.lock) e Kudu attiverà l'installazione di Composer.

## <a name="custom-containers"></a>Contenitori personalizzati

**Uso un contenitore personalizzato. Desidero che la piattaforma monti una condivisione SMB nella directory `/home/`.**

È possibile specificare l'impostazione di app `WEBSITES_ENABLE_APP_SERVICE_STORAGE` su *true*. Si tenga presente che questo causerà il riavvio del contenitore quando lo spazio di archiviazione della piattaforma subisce una modifica.

>[!NOTE]
>Se l'impostazione `WEBSITES_ENABLE_APP_SERVICE_STORAGE` non è specificata o è *false*, la directory `/home/` non verrà condivisa tra istanze scalabili e i file scritti non verranno mantenuti tra i riavvii.

**L'avvio del contenitore personalizzato richiede molto tempo e la piattaforma riavvia il contenitore prima del completamento di questa operazione. Come si risolve il problema?**

È possibile configurare il tempo di attesa della piattaforma prima del riavvio del contenitore. A tale scopo impostare `WEBSITES_CONTAINER_START_TIME_LIMIT` nell'app sul valore desiderato. Il valore predefinito è 230 secondi e il valore massimo è 1800 secondi.

**Qual è il formato dell'URL del server del Registro di sistema privato?**

È necessario immettere l'URL completo del registro, incluso `http://` o `https://`.

**Qual è il formato per il nome dell'immagine nell'opzione del Registro di sistema privato?**

Aggiungere il nome dell'immagine completa, incluso l'URL del registro privato (ad esempio myacr.azurecr.io/dotnet:latest). I nomi di immagine che usano una porta personalizzata [non possono essere inseriti tramite il portale](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Per impostare `docker-custom-image-name` usare lo [`az`strumento dalla riga di comando](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set).

**È possibile esporre più di una porta sull'immagine del contenitore personalizzato?**

Attualmente non è disponibile il supporto per l'esposizione di più porte.

**È possibile usare la propria archiviazione?**

Sì, l'opzione che consente di [usare la propria archiviazione](https://docs.microsoft.com/azure/app-service/containers/how-to-serve-content-from-azure-storage) è disponibile in anteprima.

**Perché non è possibile accedere al file system del contenitore personalizzato o ai processi in esecuzione dal sito SCM?**

Il sito SCM viene eseguito in un contenitore separato. Non è possibile controllare il file system o i processi in esecuzione del contenitore dell'app.

**Il contenitore personalizzato è in attesa su una porta diversa dalla porta 80. Come si può configurare l'app in modo da indirizzare le richieste a questa porta?**

È disponibile il rilevamento delle porte automatico. È anche possibile specificare un'impostazione dell'app denominata *WEBSITES_PORT* e assegnare a tale impostazione il valore del numero di porta previsto. In precedenza la piattaforma utilizzava l'impostazione di app *PORT*. Stiamo pianificazione di deprecare questa impostazione di app e usare esclusivamente *WEBSITES_PORT*.

**È necessario implementare HTTPS nel contenitore personalizzato?**

No, la piattaforma gestisce l'interruzione HTTPS a livello dei server front-end condivisi.

## <a name="multi-container-with-docker-compose-and-kubernetes"></a>Più contenitori con Docker Compose e Kubernetes

**Come si configura Registro Azure Container da usare con più contenitori?**

Per usare Registro Azure Container con più contenitori, **tutte le immagini del contenitore** devono essere ospitate nello stesso server di Registro Azure Container. Quando sono nello stesso server del registro contenitori, è necessario creare le impostazioni dell'applicazione e quindi aggiornare il file di configurazione di Kubernetes o Docker Compose per includere il nome dell'immagine di Registro Azure Container.

Definire le impostazioni dell'applicazione seguenti:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (URL completo, ad esempio: https://<nome-server>.azurecr.io)
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
- [Configurare gli ambienti di gestione temporanea nel Servizio app di Azure](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Distribuzione continua con l'app Web per contenitori](./app-service-linux-ci-cd.md)
