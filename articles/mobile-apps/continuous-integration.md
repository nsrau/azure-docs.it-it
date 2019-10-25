---
title: Automatizza il ciclo di vita delle tue app con Visual Studio App Center e i servizi di Azure
description: Informazioni sui servizi, ad esempio App Center che consentono di configurare la compilazione continua e l'integrazione per le applicazioni per dispositivi mobili.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 07e8e0edc4638c6c0bf9acc205175aa731c0f8bc
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795549"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatizza il ciclo di vita delle tue app con compilazione e integrazione continue

Gli sviluppatori possono scrivere il codice e archiviarlo nel repository del codice, ma i commit archiviati nel repository potrebbero non essere sempre coerenti. Con più sviluppatori che lavorano sullo stesso progetto, i problemi si verificano con l'integrazione e il team si trova in situazioni in cui le cose non funzionano, i bug continuano ad accumularsi e lo sviluppo del progetto viene ritardato. Gli sviluppatori devono attendere che l'intero codice software venga compilato e testato per verificare la presenza di errori, rendendo il processo lento e meno iterativo. 

Con la **compilazione e l'integrazione continue**, gli sviluppatori possono semplificare la compilazione e il test del codice non solo eseguendo il commit delle modifiche nel repository del codice sorgente, ma anche inserendo test e verifiche nell'ambiente di compilazione in modo che siano sempre in esecuzione esegue il test del codice. Tutte le modifiche apportate al codice sorgente vengono compilate continuamente ogni volta che viene eseguito un commit nel repository. Con ogni archiviazione, il server CI convalida ed esegue tutti i test creati dallo sviluppatore. Se i test non vengono superati, il codice viene restituito per ulteriori modifiche. Ciò consente allo sviluppatore di non suddividere le compilazioni create, oltre a non eseguire tutti i test in locale nel computer, aumentando a sua volta la produttività degli sviluppatori. 

## <a name="key-benefits"></a>Vantaggi principali
- Pipeline **automatizzata** di compilazione, test e distribuzione.
- **Rileva i bug e risolvi** tempestivamente i problemi per garantire velocità di rilascio più veloci.
- Esegui il **commit del codice con** maggiore frequenza e crea rapidamente le applicazioni.
- **Flessibilità** di modificare rapidamente il codice senza problemi.
- Un **time-to-market più veloce** garantisce che solo il codice di qualità sia così lungo.
- **Piccole modifiche al codice** che consentono di integrare piccole parti di codice alla volta.
- **Aumenta la trasparenza e la responsabilità del team** ti permette di ottenere **feedback continuo** non solo dai tuoi clienti, ma anche dal tuo team.

Usare i servizi seguenti per abilitare la pipeline di integrazione continua nelle app per dispositivi mobili.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center](/appcenter/build/) servizio di compilazione consente di compilare applicazioni native e multipiattaforma su cui il team sta lavorando, usando un'infrastruttura cloud sicura. È possibile connettere facilmente il repository in App Center e iniziare a creare l'app nel cloud a ogni commit senza doversi preoccupare della configurazione dei server di compilazione localmente, delle configurazioni complesse e del codice che si basa sul computer di un collega, ma non su quello che si sta creando.

Grazie alla potenza aggiuntiva dei servizi di App Center, è possibile automatizzare ulteriormente il flusso di lavoro. Rilascia automaticamente le compilazioni ai tester e agli App Store pubblici con App Center distribuire o eseguire test automatizzati dell'interfaccia utente su migliaia di configurazioni del dispositivo e del sistema operativo reali nel cloud con App Center test.

**Funzionalità principali**
- **Configura l'integrazione continua** in pochi minuti e crea applicazioni più spesso e più velocemente.
- Eseguire l'integrazione con **GitHub, Bitbucket, Azure DevOps e GitLab**.
- **Build veloci e sicure** su computer gestiti e ospitati sul cloud.
- **Abilitare le compilazioni per l'avvio del test** e verificare se l'app viene compilata in dispositivi iOS e Android reali.
- **Supporto nativo e multipiattaforma** : iOS, Android, MacOS, Windows, Novell, React native.
- **Personalizzare le compilazioni** aggiungendo script post-clone, pre-compilazione e post-compilazione.

**Riferimenti**
- [Iscriversi con App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introduzione a App Center Build](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/), un servizio in Azure DevOps, è un servizio di integrazione continua e recapito continuo (CD) con funzionalità complete che funziona con il provider git preferito e che può essere distribuito nella maggior parte dei principali servizi cloud, tra cui Azure. È possibile iniziare con il codice in GitHub, GitHub Enterprise Server, GitLab, Bitbucket cloud o Azure Repos. È quindi possibile automatizzare la compilazione, il testing e la distribuzione del codice a Microsoft Azure, Google Cloud Platform o Amazon Web Services.

**Funzionalità principali**
- Esperienza semplificata basata su attività per la configurazione di un server CI per le applicazioni per dispositivi mobili native (Android, iOS e Windows) e multipiattaforma (Novell, Cordova e React native), oltre a server basati su Microsoft e non Microsoft (node. js, Java) tecnologie.
- Tutte le applicazioni per **linguaggi, piattaforme e cloud** -compila, Esegui test e Distribuisci node. js, Python, Java, php, Ruby, goC++, C#C/,, Android e iOS. Esegui i processi in parallelo in Linux, macOS e Windows. Distribuisci a provider di servizi cloud come Azure, AWS e GCP. Distribuisci le applicazioni per dispositivi mobili tramite canali Beta e App Store.
- **Supporto per contenitori nativi** : è possibile creare nuovi contenitori con facilità ed eseguirne il push in qualsiasi registro. Distribuire i contenitori in host indipendenti o Kubernetes.
- **Flussi di lavoro avanzati** : semplice concatenamento della compilazione e compilazioni in più fasi. Supporto per YAML, integrazione di test, cancelli di rilascio, creazione di report e altro ancora.
- **Estendibile** : usa una gamma di attività di compilazione, test e distribuzione compilate dalla community: centinaia di estensioni da Slack a SonarCloud. È anche possibile eseguire la distribuzione da altri sistemi CI, ad esempio Jenkins, e usare webhook e API REST per semplificare l'integrazione
- **Build gratuite ospitate nel cloud** per repository pubblici e privati.
- **Supporta la distribuzione in altri fornitori di cloud** come Amazon Web Services, Google Cloud Platform e così via.

**Riferimenti**
- [Guida introduttiva a Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Introduzione ad Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Guide introduttive](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Per la scelta del servizio appropriato per le compilazioni dell'applicazione, seguire l'articolo che confronta [App Center Build e Azure Pipelines](/appcenter/build/choose-between-services).
