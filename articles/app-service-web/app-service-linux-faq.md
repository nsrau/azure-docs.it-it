---
title: Domande frequenti sulle app Web del Servizio app di Azure su Linux |Microsoft Docs
description: Domande frequenti sulle app Web del Servizio app di Azure su Linux.
keywords: Servizio app di Azure, app Web, domande frequenti, Linux, OSS
services: app-service
documentationCenter: 
authors: ahmedelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 842eb16768fd5064bd57a0c3672e7ae57885720e
ms.openlocfilehash: 6ed0f07268715102be197bb8a6654fb05b52b0c4
ms.lasthandoff: 03/01/2017


---

# <a name="azure-app-service-web-apps-on-linux-faq"></a>Domande frequenti sulle app Web del Servizio app di Azure su Linux

Con il lancio del Servizio app di Azure su Linux (attualmente in anteprima), sono previsti nuovi miglioramenti e funzionalità alla piattaforma. Di seguito vengono riportate alcune domande frequenti (FAQ) poste dai clienti negli ultimi mesi.
In caso di domande, scrivere un commento all'articolo; si riceverà una risposta il prima possibile.

## <a name="built-in-images"></a>Immagini predefinite

**D:** Si desidera biforcare i contenitori Docker predefiniti forniti dalla piattaforma. Dove si trovano questi file?

**R:** È possibile trovare tutti i file Docker su [GitHub](https://github.com/azure-app-service). È possibile trovare tutti i contenitori Docker nell'[hub Docker](https://hub.docker.com/u/appsvc/).

**D:** Quali sono i valori previsti per la sezione relativa al file di avvio quando si configura lo stack di runtime?

**R:** Per Node.Js specificare il file di configurazione PM2 o il file script. Per .NET Core specificare il nome del file DLL compilato. Per Ruby è possibile specificare uno script Ruby da usare per l'inizializzazione dell'app.

## <a name="management"></a>gestione

**D:** Ho premuto il pulsante di riavvio nel Portale di Azure, ma l'app Web non si riavvia. Come mai?

**R:** Il pulsante di riavvio sarà attivo a breve. In questo momento sono disponibili due opzioni:
- Aggiungere o modificare un'impostazione per l'applicazione fittizia. Questa operazione forzerà il riavvio dell'app Web.
- Arrestare e riavviare l'app Web.

**D:** È possibile usare Secure Shell (SSH) per connettersi alla macchina virtuale (VM) del contenitore dell'app?

**R:** No. In una versione futura verrà fornito un modo per usare SSH per connettersi al contenitore dell'app.

## <a name="continuous-integrationdeployment"></a>Integrazione/distribuzione continua

**D:** L'app Web usa ancora un'immagine obsoleta del contenitore Docker dopo l'aggiornamento dell'immagine su DockerHub. È supportata l'integrazione/distribuzione continua di contenitori personalizzati?

**R:** È possibile aggiornare il contenitore, arrestando e riavviando l'app Web. Oppure è possibile modificare o aggiungere un'impostazione dell'applicazione fittizia per forzare l'aggiornamento del contenitore. Vogliamo produrre una funzione di integrazione e distribuzione continua per i contenitori personalizzati in una versione futura.

## <a name="language-support"></a>Supporto per le lingue

**D:** È presente il supporto per le app .NET Core non compilate?

**R:** No. È necessario distribuire le app .NET Core compilate con tutte le dipendenze. Desideriamo sviluppare una distribuzione completa e compilare l'esperienza in una versione futura.

## <a name="custom-containers"></a>Contenitori personalizzati

**D:** Uso un contenitore personalizzato. L'app si trova nella directory \home\, ma riesco a trovare i file quando sfoglio i contenuti dal [sito SCM](https://github.com/projectkudu/kudu) o da un client FTP. Dove si trovano i file?

**R:** Montiamo una condivisione SMB nella directory \home\. In questo modo si esegue l'override di qualsiasi contenuto presente.

**D:** Si desidera esporre più di una porta sull'immagine del contenitore personalizzato. È possibile?

**R:** Attualmente questa operazione non è supportata.

**D:** È possibile usare la propria archiviazione?

**R:** Attualmente questa operazione non è supportata.

**D:** Non è possibile accedere al file system del contenitore personalizzato o ai processi in esecuzione dal sito SCM. Perché?

**R:** Il sito SCM viene eseguito in un contenitore separato. Non è possibile controllare il file system o i processi in esecuzione del contenitore dell'app.

**D:** Il contenitore personalizzato è in attesa su una porta diversa dalla porta 80. Come si può configurare l'app in modo da indirizzare le richieste a questa porta?

**R:** È possibile specificare un'impostazione dell'applicazione denominata **PORTA** e assegnare a tale impostazione il valore del numero di porta previsto.

## <a name="pricing-and-sla"></a>Prezzi e contratto di servizio

**D:** Qual è il prezzo quando si usa l'anteprima pubblica?

**R:** Verrà addebitata le metà del numero di ore di esecuzione dell'app, con il normale prezzo del Servizio app di Azure. Ciò significa che si ottiene uno sconto del 50% sul normale prezzo del Servizio app di Azure.

## <a name="other"></a>Altri

**D:** Quali sono i caratteri supportati nei nomi delle impostazioni dell'applicazione?

**R:** È possibile usare solo i caratteri A-Z, a-z, 0-9 e il carattere di sottolineatura per le impostazioni dell'applicazione.

**D:** Dove è possibile richiedere nuove funzionalità?

**R:** È possibile inviare l'idea al [forum dei commenti App Web](https://aka.ms/webapps-uservoice). Aggiungere "[Linux]" al titolo dell'idea.

## <a name="next-steps"></a>Passaggi successivi
* [What is App Service on Linux?](app-service-linux-intro.md) (Che cos'è Servizio app in Linux?)
* [Creazione di app Web nel servizio app in Linux](app-service-linux-how-to-create-a-web-app.md)

