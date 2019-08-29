---
title: Distribuire un'immagine personalizzata, multi-contenitore o predefinita - Servizio app di Azure | Microsoft Docs
description: Come scegliere tra una distribuzione di contenitori Docker personalizzata e un framework applicazioni multi-contenitore e predefinito per il servizio app in Linux
keywords: Servizio app di Azure, app Web, Linux, OSS
services: app-service
documentationCenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: ae28b185aa44ca22d59204826036435a10c64e91
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066783"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Immagine personalizzata, multi-contenitore o immagine della piattaforma predefinita?

Il [servizio app in Linux](app-service-linux-intro.md) offre tre percorsi diversi per pubblicare un'applicazione sul Web:

- **Distribuzione di un'immagine personalizzata**: integrare l'app in un'immagine Docker che contiene tutti i file e le dipendenze in un pacchetto pronto per l'esecuzione.
- **Distribuzione multi-contenitore**: "Contenitore Docker" l'app tra più contenitori usando un file di configurazione Docker Compose.
- **Distribuzione di un'app con un'immagine di piattaforma predefinita**: le immagini di piattaforma predefinite contengono runtime e dipendenze di app Web comuni, come Node e PHP. Usare uno qualsiasi dei [metodi di distribuzione di Servizio app di Azure](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) per distribuire l'app nello spazio di archiviazione dell'app Web e quindi usare un'immagine della piattaforma predefinita per eseguirla.

## <a name="which-method-is-right-for-your-app"></a>Qual è il metodo più appropriato per l'app? 

Ecco i principali fattori da prendere in considerazione:

- **Disponibilità di Docker nel flusso di lavoro di sviluppo**: lo sviluppo di immagini personalizzate richiede una conoscenza di base del flusso di lavoro di sviluppo Docker. La distribuzione di un'immagine personalizzata in un'app Web richiede la pubblicazione dell'immagine personalizzata in un host repository come l'hub Docker. Se si ha familiarità con Docker e si è in grado di aggiungere attività Docker al flusso di lavoro di compilazione oppure si sta già pubblicando l'app come immagine Docker, un'immagine personalizzata è molto probabilmente la scelta migliore.
- **Architettura a più livelli**: distribuire più contenitori, ad esempio un livello di applicazione Web e un livello API per separare le funzionalità tramite più contenitori. 
- **Prestazioni delle applicazioni**: aumentare le prestazioni dell'app multi-contenitore usando un livello di cache, ad esempio Redis. A tale scopo, selezionare l'opzione relativa al multi-contenitore.
- **Requisiti di runtime univoci**: le immagini di piattaforma predefinite sono progettate per soddisfare le esigenze della maggior parte delle app Web, ma sono limitate per quanto riguarda la personalizzazione. L'app può avere dipendenze univoche o altri requisiti di runtime che superano le capacità delle immagini predefinite.
- **Requisiti di compilazione**: con una [distribuzione continua](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) è possibile rendere operativa l'app in Azure direttamente dal codice sorgente. Non è necessario un processo di compilazione o pubblicazione esterno. Tuttavia, la personalizzazione e la disponibilità degli strumenti di compilazione nel motore di compilazione [Kudu](https://github.com/projectkudu/kudu/wiki) sono limitate. L'app potrebbe superare le capacità di Kudu con l'aumentare delle sue dipendenze o dei requisiti per la logica di compilazione personalizzata.
- **Requisiti di lettura/scrittura sui dischi**: a tutte le app Web viene allocato un volume di archiviazione per il contenuto Web. Questo volume, supportato da Archiviazione di Azure, è montato su `/home` nel file system dell'app. Diversamente dai file nel file system del contenitore, i file presenti nel volume del contenuto sono accessibili tra tutte le istanze scalabili di un'app e le modifiche vengono mantenute tra i riavvii dell'app. Tuttavia, la latenza del disco del volume del contenuto è maggiore e più variabile rispetto alla latenza del file system del contenitore locale e aggiornamenti della piattaforma, tempi di interruzione imprevisti e problemi di connettività di rete possono influire sull'accesso. Le app che richiedono un accesso elevato in sola lettura a file di contenuto possono trarre vantaggio dalla distribuzione di immagini personalizzate, che inserisce i file nel file system dell'immagine invece che nel volume del contenuto.
- **Utilizzo delle risorse di compilazione**: quando un'app viene distribuita dal codice sorgente, gli script di distribuzione eseguiti da Kudu usano le stesse risorse di calcolo e archiviazione del piano di servizio dell'app in esecuzione. Distribuzioni di app di grandi dimensioni possono utilizzare più risorse o richiedere più tempo rispetto a quanto desiderato. In particolare, molti flussi di lavoro di distribuzione generano attività dei dischi elevate nel volume del contenuto dell'app, che non è ottimizzato per questo tipo di attività. Un'immagine personalizzata fornisce tutti i file e le dipendenze dell'app ad Azure in un unico pacchetto, senza che siano necessari altri trasferimenti di file o azioni di distribuzione.
- **Necessità di interazione rapida**: l'uso di un'immagine Docker per un'app richiede altri passaggi di compilazione. Perché le modifiche abbiano effetto, è necessario pubblicare la nuova immagine in un repository con ogni aggiornamento. Di questi aggiornamenti viene quindi eseguito il pull nell'ambiente Azure. Se uno dei contenitori predefiniti soddisfa le esigenze dell'app, la distribuzione dal codice sorgente può offrire un flusso di lavoro di sviluppo più rapido.

## <a name="next-steps"></a>Passaggi successivi

Contenitore personalizzato:
* [Esegui contenitore personalizzato](quickstart-docker-go.md)

Più contenitori:
* [Creare un'app a più contenitori](quickstart-multi-container.md)

Gli articoli seguenti consentono di iniziare a usare il servizio app in Linux con un'immagine della piattaforma incorporata:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](quickstart-php.md)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)