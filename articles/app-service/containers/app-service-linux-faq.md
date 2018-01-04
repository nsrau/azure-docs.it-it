---
title: Domande frequenti sul Servizio app di Azure in Linux |Microsoft Docs
description: Domande frequenti sul Servizio app di Azure in Linux.
keywords: Servizio app di Azure, app Web, domande frequenti, Linux, OSS
services: app-service
documentationCenter: 
author: ahmedelnably
manager: cfowler
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: aelnably;wesmc
ms.openlocfilehash: b22d5f3497c388192764aa6b4ee8c95fec568bd8
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="azure-app-service-on-linux-faq"></a>Domande frequenti sul Servizio app di Azure in Linux

Con il lancio del Servizio app in Linux, sono previsti miglioramenti e nuove funzionalità per la piattaforma. Questo articolo fornisce le risposte alle domande che i clienti ci hanno posto di recente.

In caso di domande, scrivere un commento all'articolo; si riceverà una risposta il prima possibile.

## <a name="built-in-images"></a>Immagini predefinite

**Si desidera biforcare i contenitori Docker predefiniti forniti dalla piattaforma. Dove si trovano questi file?**

È possibile trovare tutti i file Docker su [GitHub](https://github.com/azure-app-service). È possibile trovare tutti i contenitori Docker nell'[hub Docker](https://hub.docker.com/u/appsvc/).

**Quali sono i valori previsti per la sezione relativa al file di avvio quando si configura lo stack di runtime?**

Per Node.js specificare il file di configurazione PM2 o il file script. Per .NET Core specificare il nome del file DLL compilato. Per Ruby è possibile specificare uno script Ruby da usare per l'inizializzazione dell'app.

## <a name="management"></a>Gestione

**Cosa accade quando viene premuto il pulsante di riavvio nel portale di Azure?**

Questa azione equivale a un riavvio di Docker.

**È possibile usare Secure Shell (SSH) per connettersi alla macchina virtuale (VM) del contenitore dell'app?**

Sì, è possibile farlo tramite il sito di gestione controllo del codice sorgente.

**Come è possibile creare un piano di servizio app Linux tramite un SDK o un modello di Azure Resource Manager?**

È necessario impostare il campo **reserved** del servizio app su *true*.

## <a name="continuous-integration-and-deployment"></a>Integrazione e distribuzione continue

**L'app Web usa ancora un'immagine obsoleta del contenitore Docker dopo l'aggiornamento dell'immagine su DockerHub. È supportata l'integrazione e distribuzione continua di contenitori personalizzati?**

Per configurare l'integrazione o la distribuzione continua per il Registro contenitori di Azure o le immagini di DockerHub, consultare l'articolo [Distribuzione continua con l'app Web per contenitori](./app-service-linux-ci-cd.md). Per registri privati, è possibile aggiornare il contenitore arrestando e riavviando l'app Web. Oppure è possibile modificare o aggiungere un'impostazione dell'applicazione fittizia per forzare l'aggiornamento del contenitore.

**Gli ambienti di gestione temporanea sono supportati?**

Sì.

**È possibile usare *Distribuzione Web* per distribuire l'app Web?**

Sì, è necessario impostare `WEBSITE_WEBDEPLOY_USE_SCM` nell'app su *false*.

**La distribuzione in GIT dell'applicazione non riesce quando si usa l'app Web di Linux. Come è possibile risolvere il problema?**

Se la distribuzione in GIT non riesce per l'app Web di Linux, è possibile scegliere le opzioni alternative seguenti per distribuire il codice dell'applicazione:

- Usare la funzionalità Recapito continuo (anteprima): è possibile archiviare il codice sorgente dell'app in un repository GIT di Team Services o un repository di GitHub per usare Recapito continuo di Azure. Per altri dettagli, vedere [How to configure Continuous Delivery for Linux web app](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/) (Come configurare Recapito continuo per app Web in Linux).

- Usare l'[API per la distribuzione di ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): per usare questa API, attivare una connessione [SSH nell'app Web](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support#making-a-client-connection) e passare alla cartella in cui si vuole distribuire il codice. Eseguire questo comando:

   ```
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Se si verifica un errore che indica l'impossibilità di trovare il comando `curl`, assicurarsi di installare curl tramite `apt-get install curl` prima di eseguire il comando `curl` precedente.

## <a name="language-support"></a>Supporto per le lingue

**Esistono impostazioni speciali o configurazioni specifiche da impostare se si desidera usare i WebSocket nell'applicazione Node.js?**

Sì, disabilitare `perMessageDeflate` nel codice Node.js sul lato server. Ad esempio, se si usa socket.io, eseguire le operazioni seguenti:
```
var io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**È presente il supporto per le app .NET Core non compilate?**

Sì.

**È previsto il supporto per lo strumento Composer come gestore delle dipendenze per le app PHP?**

Sì. Durante una distribuzione Git, Kudu rileverà che si sta distribuendo un'applicazione PHP (grazie alla presenza di un file composer.lock) e Kudu attiverà automaticamente l'installazione di Composer.

## <a name="custom-containers"></a>Contenitori personalizzati

**Uso un contenitore personalizzato. Desidero che la piattaforma monti una condivisione SMB nella directory `/home/`.**

È possibile farlo impostando `WEBSITES_ENABLE_APP_SERVICE_STORAGE` dell'app su *true* o rimuovendo l'app completamente l'impostazione. Si tenga presente che questo causerà il riavvio del contenitore quando lo spazio di archiviazione della piattaforma subisce una modifica. 

>[!NOTE]
>Se l'impostazione `WEBSITES_ENABLE_APP_SERVICE_STORAGE` è *false*, la directory `/home/` non verrà condivisa tra istanze scalabili e i file che vi sono scritti non verranno mantenuti tra i riavvii.

**L'avvio del contenitore personalizzato richiede molto tempo e la piattaforma riavvia il contenitore prima del completamento di questa operazione. Come si risolve il problema?**

È possibile configurare il tempo di attesa della piattaforma prima del riavvio del contenitore. A tale scopo impostare `WEBSITES_CONTAINER_START_TIME_LIMIT` nell'app sul valore desiderato. Il valore predefinito è 230 secondi e il valore massimo è 600 secondi.

**Qual è il formato dell'URL del server del Registro di sistema privato?**

È necessario immettere l'URL completo del registro, incluso `http://` o `https://`.

**Qual è il formato per il nome dell'immagine nell'opzione del Registro di sistema privato?**

Aggiungere il nome dell'immagine completa, incluso l'URL del registro privato (ad esempio myacr.azurecr.io/dotnet:latest). I nomi di immagine che usano una porta personalizzata [non possono essere inseriti tramite il portale](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Per impostare `docker-custom-image-name` usare lo [`az`strumento dalla riga di comando](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set).

**È possibile esporre più di una porta sull'immagine del contenitore personalizzato?**

Attualmente non è disponibile il supporto per l'esposizione di più porte.

**È possibile usare la propria archiviazione?**

Attualmente la possibilità di portare le proprie risorse di archiviazione non è supportata.

**Perché non è possibile accedere al file system del contenitore personalizzato o ai processi in esecuzione dal sito SCM?**

Il sito SCM viene eseguito in un contenitore separato. Non è possibile controllare il file system o i processi in esecuzione del contenitore dell'app.

**Il contenitore personalizzato è in attesa su una porta diversa dalla porta 80. Come si può configurare l'app in modo da indirizzare le richieste a questa porta?**

È disponibile il rilevamento delle porte automatico. È anche possibile specificare un'impostazione dell'app denominata *WEBSITES_PORT* e assegnare a tale impostazione il valore del numero di porta previsto. In precedenza la piattaforma utilizzava l'impostazione di app *PORT*. Stiamo pianificazione di deprecare questa impostazione di app e usare esclusivamente *WEBSITES_PORT*.

**È necessario implementare HTTPS nel contenitore personalizzato?**

No, la piattaforma gestisce l'interruzione HTTPS a livello dei server front-end condivisi.

## <a name="pricing-and-sla"></a>Prezzi e contratto di servizio

**Qual è il piano tariffario ora che il servizio è generalmente disponibile?**

Verrà addebitato il numero di ore di esecuzione dell'app con il normale prezzo del Servizio app di Azure.

## <a name="other-questions"></a>Altre domande

**Quali sono i caratteri supportati nei nomi delle impostazioni dell'applicazione?**

Per le impostazioni dell'applicazione è possibile usare solo lettere (A-Z, a-z), numeri (0-9) e il carattere di sottolineatura (_).

**Dove è possibile richiedere nuove funzionalità?**

È possibile inviare l'idea al [forum dei commenti App Web](https://aka.ms/webapps-uservoice). Aggiungere "[Linux]" al titolo dell'idea.

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è il Servizio app di Azure in Linux?](app-service-linux-intro.md)
* [Configurare gli ambienti di gestione temporanea nel Servizio app di Azure](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Distribuzione continua con l'app Web per contenitori](./app-service-linux-ci-cd.md)
