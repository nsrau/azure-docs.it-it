---
title: Domande frequenti sulle app Web del Servizio app di Azure su Linux |Microsoft Docs
description: Domande frequenti sulle app Web del Servizio app di Azure su Linux.
keywords: Servizio app di Azure, app Web, domande frequenti, Linux, OSS
services: app-service
documentationCenter: 
authors: aelnably
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
ms.sourcegitcommit: 831ef097027721146531e8d699fe3f67417a57ea
ms.openlocfilehash: b88aa3d0ae89aec81c2b9144fb5de3210a0b8d1e
ms.lasthandoff: 02/18/2017


---

# <a name="azure-app-service-web-apps-on-linux-faq"></a>Domande frequenti sulle app Web del Servizio app di Azure su Linux #

Con il lancio del Servizio app di Azure su Linux (attualmente in anteprima), sono previsti nuovi miglioramenti e funzionalità alla piattaforma. Di seguito vengono riportate alcune domande frequenti poste dai clienti negli ultimi mesi.
In caso di domande, scrivere un commento all'articolo; si riceverà una risposta il prima possibile.

## <a name="built-in-images"></a>Immagini predefinite ##

**D:** Si desidera biforcare i contenitori Docker predefiniti forniti dalla piattaforma. Dove si trovano questi file?

**R:** Tutti i file di Docker sono disponibili qui: https://github.com/azure-app-service. Tutti i contenitori Docker sono disponibili qui: https://hub.docker.com/u/appsvc/.

## <a name="management"></a>gestione ##

**D:** Viene premuto il pulsante di riavvio nel portale, ma l'app Web non si riavvia. Come mai?

**R:** Stiamo lavorando per implementare il pulsante di riavvio nel prossimo futuro. Al momento sono disponibili due opzioni.
1. Aggiungere o modificare un'impostazione fittizia dell'applicazione per forzare il riavvio dell'app Web. 
2. Arrestare e avviare l'app Web.

**D:** È possibile eseguire SSH nella macchina virtuale?

**R:** No, in una versione futura verrà fornito un modo per eseguire SSH nel contenitore dell'app.

## <a name="continuous-integration--deployment"></a>Integrazione/Distribuzione continua ##

**Q:** L'app Web usa ancora un'immagine obsoleta del contenitore Docker dopo l'aggiornamento dell'immagine su DockerHub? È supportata l'integrazione/distribuzione continua di contenitori personalizzati?

**R:** È possibile aggiornare il contenitore arrestando e avviando l'app Web o modificando/aggiungendo un'impostazione fittizia dell'applicazione per forzare l'aggiornamento del contenitore. È prevista in una versione futura una funzionalità di integrazione e distribuzione continua per i contenitori personalizzati.

## <a name="language-support"></a>Supporto per le lingue ##

**Q:** È presente il supporto per le app .NET Core non compilate?

**R:** No, è necessario distribuire l'app .NET Core compilata con tutte le dipendenze. In una versione futura verrà fornita un'esperienza di distribuzione e compilazione completa.

## <a name="built-in-images"></a>Immagini predefinite ##

**D:** Quali sono i valori previsti per la sezione relativa al file di avvio quando si configura lo stack di runtime?

**R:** Per Node.Js è possibile specificare il file di configurazione PM2 o il file di script. Per .NET Core è necessario specificare il nome del file dll compilato. Per Ruby è possibile specificare uno script Ruby da usare per l'inizializzazione dell'app.

## <a name="custom-containers"></a>Contenitori personalizzati ##

**D:** Si sta utilizzando un contenitore personalizzato. L'app si trova nella directory \home\, ma non è possibile trovare i file quando si sfogliano i contenuti dal sito SCM o da un client ftp. Dove si trovano i file?

**R:** Viene montata una condivisione SMB nel percorso \home\directory. La condivisione sovrascrive i contenuti lì presenti.

**D:** Si desidera esporre più di una porta sull'immagine del contenitore personalizzato. È possibile?

**R:** Questa funzionalità non è supportata al momento.

**D:** È possibile usare la propria archiviazione?

**R:** Questa funzionalità non è supportata al momento.

**D:** Non è possibile accedere al file system del contenitore personalizzato o ai processi in esecuzione dal sito SCM. Perché?

**R:** Il sito SCM viene eseguito in un contenitore separato. Non è possibile controllare il file system o i processi in esecuzione del contenitore dell'app.

**D:** Il contenitore personalizzato è in attesa su una porta diversa dalla porta 80. Come si può configurare l'app in modo da indirizzare le richieste a questa porta?

**R:** È possibile specificare un'impostazione dell'applicazione denominata **PORTA** e assegnare a tale impostazione il valore del numero di porta previsto.

## <a name="pricing-and-sla"></a>Prezzi e contratto di servizio ##

**D:** Quali sono i prezzi dell'anteprima pubblica?

**R:** L'addebito è pari alla metà delle ore di esecuzione dell'app, con i normali prezzi del Servizio app di Azure; si tratta in sostanza di uno sconto del 50% sui normali prezzi del Servizio app di Azure.

## <a name="other"></a>Altri ##

**D:** Quali sono i caratteri supportati nei nomi delle impostazioni dell'applicazione?

**R:** È possibile utilizzare solo i caratteri A-Z, a-z, 0-9 e di sottolineatura per le impostazioni dell'applicazione.

**D:** Dove è possibile richiedere nuove funzionalità?

**R:** È possibile inviare qui le proprie idee: https://aka.ms/webapps-uservoice. Aggiungere [Linux] al titolo dell'idea.

## <a name="next-steps"></a>Passaggi successivi
* [What is App Service on Linux?](app-service-linux-intro.md) (Che cos'è Servizio app in Linux?)
* [Creating Apps in an App Service on Linux](app-service-linux-how-to-create-a-web-app.md) (Creazione di app Web nel servizio app in Linux)

