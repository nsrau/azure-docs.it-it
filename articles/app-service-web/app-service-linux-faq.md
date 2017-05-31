---
title: Domande frequenti su App Web del Servizio app di Azure su Linux |Microsoft Docs
description: Domande frequenti su App Web del Servizio app di Azure su Linux.
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
ms.date: 05/04/2017
ms.author: aelnably;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: dcce8d855f8c37d40fe8f09ef0a97e46b342e3cf
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---

# <a name="azure-app-service-web-app-on-linux-faq"></a>Domande frequenti su App Web del Servizio app di Azure su Linux

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


Con il lancio di App Web su Linux, sono previsti miglioramenti e nuove funzionalità per la piattaforma. Di seguito vengono riportate alcune domande frequenti (FAQ) poste dai clienti negli ultimi mesi.
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

**R:** Sì, è possibile farlo tramite il sito SCM, consultare l'articolo seguente per altre informazioni sul [supporto SSH per App Web in Linux](./app-service-linux-ssh-support.md)

## <a name="continuous-integrationdeployment"></a>Integrazione/distribuzione continua

**D:** L'app Web usa ancora un'immagine obsoleta del contenitore Docker dopo l'aggiornamento dell'immagine su DockerHub. È supportata l'integrazione/distribuzione continua di contenitori personalizzati?

**R:** Per configurare l'integrazione o la distribuzione continua per le immagini di DockerHub consultare l'articolo seguente [Distribuzione continua dell'hub Docker con App Web in Linux](./app-service-linux-ci-cd.md). Per registri privati, è possibile aggiornare il contenitore arrestando e riavviando l'app Web. Oppure è possibile modificare o aggiungere un'impostazione dell'applicazione fittizia per forzare l'aggiornamento del contenitore.

**D:** Gli ambienti di gestione temporanea sono supportati?

**R:** Sì.

## <a name="language-support"></a>Supporto per le lingue

**D:** È presente il supporto per le app .NET Core non compilate?

**R:** Sì.

**R:** È previsto il supporto per lo strumento Composer come gestore delle dipendenze per le app PHP?

**R:** No. Le app PHP dovranno essere distribuite con tutte le dipendenze. Per il futuro, è prevista l'implementazione di un'esperienza di distribuzione completa.

## <a name="custom-containers"></a>Contenitori personalizzati

**D:** Uso un contenitore personalizzato. L'app si trova nella directory `\home\`, ma riesco a trovare i file quando sfoglio i contenuti dal [sito SCM](https://github.com/projectkudu/kudu) o da un client FTP. Dove si trovano i file?

**R:** Viene montata una condivisione SMB nella directory `\home\`. In questo modo viene sostituito qualsiasi contenuto presente.

**D:** Qual è il formato dell'URL del server del Registro di sistema privato?

**R:** È necessario immettere l'URL completo del registro, incluso `http://` o `https://`.

**D:** Qual è il formato per il nome dell'immagine nell'opzione del Registro di sistema privato?

**R:** È necessario aggiungere il nome completo dell'immagine, incluso l'URL del Registro di sistema privato (es. myacr.azurecr.io/dotnet:latest)

**D:** Si desidera esporre più di una porta sull'immagine del contenitore personalizzato. È possibile?

**R:** Attualmente questa operazione non è supportata.

**D:** È possibile usare la propria archiviazione?

**R:** Attualmente questa operazione non è supportata.

**D:** Non è possibile accedere al file system del contenitore personalizzato o ai processi in esecuzione dal sito SCM. Perché?

**R:** Il sito SCM viene eseguito in un contenitore separato. Non è possibile controllare il file system o i processi in esecuzione del contenitore dell'app.

**D:** Il contenitore personalizzato è in attesa su una porta diversa dalla porta 80. Come si può configurare l'app in modo da indirizzare le richieste a questa porta?

**R:** È presente la rilevazione automatica della porta, ma è anche possibile specificare un'impostazione dell'applicazione denominata **PORTA** e assegnare a tale impostazione il valore del numero di porta previsto.

**D:** È necessario implementare HTTPS nel contenitore personalizzato?

**R:** No, la piattaforma gestisce l'interruzione HTTPS a livello dei server front-end condivisi.

## <a name="pricing-and-sla"></a>Prezzi e contratto di servizio

**D:** Qual è il prezzo quando si usa l'anteprima pubblica?

**R:** Verrà addebitata le metà del numero di ore di esecuzione dell'app, con il normale prezzo del Servizio app di Azure. Ciò significa che si ottiene uno sconto del 50% sul normale prezzo del Servizio app di Azure.

## <a name="other"></a>Altri

**D:** Quali sono i caratteri supportati nei nomi delle impostazioni dell'applicazione?

**R:** È possibile usare solo i caratteri A-Z, a-z, 0-9 e il carattere di sottolineatura per le impostazioni dell'applicazione.

**D:** Dove è possibile richiedere nuove funzionalità?

**R:** È possibile inviare l'idea al [forum dei commenti App Web](https://aka.ms/webapps-uservoice). Aggiungere "[Linux]" al titolo dell'idea.

## <a name="next-steps"></a>Passaggi successivi
* [Definizione di App Web di Azure in Linux](app-service-linux-intro.md)
* [Creazione di app Web in App Web di Azure in Linux](app-service-linux-how-to-create-web-app.md)
* [SSH support for Azure Web App on Linux](./app-service-linux-ssh-support.md) (Supporto SSH per App Web di Azure in Linux)
* [Configurare gli ambienti di gestione temporanea nel Servizio app di Azure](./web-sites-staged-publishing.md)
* [Docker Hub Continuous Deployment with Azure Web App on Linux](./app-service-linux-ci-cd.md) (Distribuzione continua dell'hub Docker con App Web di Azure in Linux)

