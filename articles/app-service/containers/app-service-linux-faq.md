---
title: Domande frequenti sulle app Web per contenitori del servizio app di Azure | Microsoft Docs
description: Domande frequenti sulle app Web per contenitori del servizio app di Azure.
keywords: Servizio app di Azure, app Web, domande frequenti, Linux, OSS
services: app-service
documentationCenter: 
author: ahmedelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: aelnably;wesmc
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 146c598f5cb62612327ce679a6bfaadc8312b149
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="azure-app-service-web-app-for-containers-faq"></a>Domande frequenti sulle app Web per contenitori del servizio app di Azure

Con il lancio di App Web per contenitori, sono previsti miglioramenti e nuove funzionalità per la piattaforma. Di seguito vengono riportate alcune domande frequenti (FAQ) poste dai clienti negli ultimi mesi.
In caso di domande, scrivere un commento all'articolo; si riceverà una risposta il prima possibile.

## <a name="built-in-images"></a>Immagini predefinite

**D:** Si desidera biforcare i contenitori Docker predefiniti forniti dalla piattaforma. Dove si trovano questi file?

**R:** È possibile trovare tutti i file Docker su [GitHub](https://github.com/azure-app-service). È possibile trovare tutti i contenitori Docker nell'[hub Docker](https://hub.docker.com/u/appsvc/).

**D:** Quali sono i valori previsti per la sezione relativa al file di avvio quando si configura lo stack di runtime?

**R:** Per Node.Js specificare il file di configurazione PM2 o il file script. Per .NET Core specificare il nome del file DLL compilato. Per Ruby è possibile specificare uno script Ruby da usare per l'inizializzazione dell'app.

## <a name="management"></a>gestione

**D:** Cosa accade quando viene premuto il pulsante di riavvio nel portale di Azure?

**R:** È l'equivalente del riavvio di Docker.

**D:** È possibile usare Secure Shell (SSH) per connettersi alla macchina virtuale (VM) del contenitore dell'app?

**R:** Sì, è possibile farlo tramite il sito di Gestione controllo servizi.

**D:** Come si crea un piano di servizio app Linux tramite SDK o un modello ARM?

**R:** È necessario impostare il campo `reserved` del servizio app su `true`.

## <a name="continuous-integrationdeployment"></a>Integrazione/distribuzione continua

**D:** L'app Web usa ancora un'immagine obsoleta del contenitore Docker dopo l'aggiornamento dell'immagine su DockerHub. È supportata l'integrazione/distribuzione continua di contenitori personalizzati?

**R:** Per configurare l'integrazione o la distribuzione continua per Registro contenitori di Azure o le immagini di DockerHub, consultare l'articolo [Distribuzione continua con App Web di Azure per contenitori](./app-service-linux-ci-cd.md). Per registri privati, è possibile aggiornare il contenitore arrestando e riavviando l'app Web. Oppure è possibile modificare o aggiungere un'impostazione dell'applicazione fittizia per forzare l'aggiornamento del contenitore.

**D:** Gli ambienti di gestione temporanea sono supportati?

**R:** Sì.

**D:** È possibile usare **Distribuzione Web** per distribuire l'app Web?

**R:** Sì, è necessario impostare `WEBSITE_WEBDEPLOY_USE_SCM` nell'app su `false`.

## <a name="language-support"></a>Supporto per le lingue

**D:** È presente il supporto per le app .NET Core non compilate?

**R:** Sì.

**R:** È previsto il supporto per lo strumento Composer come gestore delle dipendenze per le app PHP?

**R:** Sì. Durante una distribuzione Git, Kudu rileverà che si sta distribuendo un'applicazione PHP (grazie alla presenza di un file composer.lock) e attiverà automaticamente l'installazione di Composer.

## <a name="custom-containers"></a>Contenitori personalizzati

**D:** Uso un contenitore personalizzato. L'app si trova nella directory `/home/`, ma riesco a trovare i file quando sfoglio i contenuti dal [sito SCM](https://github.com/projectkudu/kudu) o da un client FTP. Dove si trovano i file?

**R:** Viene montata una condivisione SMB nella directory `/home/`. In questo modo viene sostituito qualsiasi contenuto presente.

**D:** Uso un contenitore personalizzato. Desidero che la piattaforma monti una condivisione SMB nella directory `/home/`.

**R:** È possibile farlo impostando `WEBSITES_ENABLE_APP_SERVICE_STORAGE` dell'app su `true` o rimuovendo l'app completamente l'impostazione. Si tenga presente che questo causerà il riavvio del contenitore quando lo spazio di archiviazione della piattaforma subisce una modifica. 

Si noti che se WEBSITES_ENABLE_APP_SERVICE_STORAGE è "false", la directory /home/ non verrà più condivisa tra istanze scalabili e i file scritti non verranno mantenuti tra i riavvii.

**D:** L'avvio del contenitore personalizzato richiede molto tempo e la piattaforma riavvia il contenitore prima del completamento di questa operazione. Come si risolve il problema?

**R:** È possibile configurare il tempo di attesa della piattaforma prima del riavvio del contenitore. A questo scopo specificare l'impostazione di app `WEBSITES_CONTAINER_START_TIME_LIMIT` sul valore desiderato in secondi. Il valore predefinito è 230 secondi e il valore massimo è 600 secondi.

**D:** Qual è il formato dell'URL del server del Registro di sistema privato?

**R:** È necessario immettere l'URL completo del registro, incluso `http://` o `https://`.

**D:** Qual è il formato per il nome dell'immagine nell'opzione del Registro di sistema privato?

**R:** È necessario aggiungere il nome completo dell'immagine, incluso l'URL del Registro di sistema privato (es. myacr.azurecr.io/dotnet:latest). I nomi di immagine che usano una porta personalizzata [non possono essere inseriti tramite il portale](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Usare lo [`az` strumento dalla riga di comando](https://docs.microsoft.com/en-us/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set) per impostare `docker-custom-image-name`.

**D:** Si desidera esporre più di una porta sull'immagine del contenitore personalizzato. È possibile?

**R:** Attualmente questa operazione non è supportata.

**D:** È possibile usare la propria archiviazione?

**R:** Attualmente questa operazione non è supportata.

**D:** Non è possibile accedere al file system del contenitore personalizzato o ai processi in esecuzione dal sito SCM. Perché?

**R:** Il sito SCM viene eseguito in un contenitore separato. Non è possibile controllare il file system o i processi in esecuzione del contenitore dell'app.

**D:** Il contenitore personalizzato è in attesa su una porta diversa dalla porta 80. Come si può configurare l'app in modo da indirizzare le richieste a questa porta?

**R:** È presente la rilevazione automatica della porta, ma è anche possibile specificare un'impostazione dell'applicazione denominata **WEBSITES_PORT** e assegnare a tale impostazione il valore del numero di porta previsto. In precedenza, la piattaforma usava l'impostazione dell'app `PORT`. È previsto che l'uso di questa impostazione dell'app venga deprecato per passare all'uso esclusivo di `WEBSITES_PORT`.

**D:** È necessario implementare HTTPS nel contenitore personalizzato?

**R:** No, la piattaforma gestisce l'interruzione HTTPS a livello dei server front-end condivisi.

## <a name="pricing-and-sla"></a>Prezzi e contratto di servizio

**Q:** Qual è il piano tariffario ora che il servizio è generalmente disponibile?

**R:** Verrà addebitato il numero di ore di esecuzione dell'app, con il normale prezzo del Servizio app di Azure.

## <a name="other"></a>Altri

**D:** Quali sono i caratteri supportati nei nomi delle impostazioni dell'applicazione?

**R:** È possibile usare solo i caratteri A-Z, a-z, 0-9 e il carattere di sottolineatura per le impostazioni dell'applicazione.

**D:** Dove è possibile richiedere nuove funzionalità?

**R:** È possibile inviare l'idea al [forum dei commenti App Web](https://aka.ms/webapps-uservoice). Aggiungere "[Linux]" al titolo dell'idea.

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è App Web di Azure per contenitori?](app-service-linux-intro.md)
* [Configurare gli ambienti di gestione temporanea nel Servizio app di Azure](../../app-service-web/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Distribuzione continua con App Web di Azure per contenitori](./app-service-linux-ci-cd.md)

