---
title: Automatizzare il ciclo di vita delle app con Visual Studio App Center e i servizi di AzureAutomate the lifecycle of your apps with Visual Studio App Center and Azure services
description: Informazioni sui servizi, ad esempio App Center, che consentono di configurare la compilazione e l'integrazione continue per le applicazioni mobili.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 0560f47b832ec2965d9b567e1aeff78baa9c247c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240931"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatizza il ciclo di vita delle tue app con la compilazione e l'integrazione continue

Gli sviluppatori scrivono il codice e lo archiviano nel repository di codice, ma i commit archiviati nel repository potrebbero non essere sempre coerenti. Quando più sviluppatori lavorano sullo stesso progetto, possono essere risolti problemi di integrazione. I team potrebbero incorrere in situazioni in cui le cose non funzionano, i bug si accumulano e lo sviluppo del progetto viene ritardato. Gli sviluppatori devono attendere fino a quando l'intero codice software viene costruito e testato per verificare la presenza di errori, il che rende il processo lento e meno iterativo. 

Con la compilazione e l'integrazione continue, gli sviluppatori possono semplificare le compilazioni e testare il codice eseguendo il commit delle modifiche nel repository del codice sorgente e inserendo test e verifiche nell'ambiente di compilazione. In questo modo, vengono sempre eseguiti test in base al codice. Tutte le modifiche apportate al codice sorgente vengono compilate continuamente ogni volta che viene eseguito un commit nel repository. Ad ogni archiviazione, il server di integrazione continua (CI) convalida ed esegue qualsiasi test creato dallo sviluppatore. Se i test non vengono superati, il codice viene inviato indietro per ulteriori modifiche. In questo modo, gli sviluppatori non rompono le compilazioni create. Inoltre, non è necessario eseguire tutti i test in locale nei propri computer, aumentando la produttività degli sviluppatori. 

## <a name="key-benefits"></a>Vantaggi principali
- Automatizza le compilazioni, i test e le distribuzioni per le pipeline.
- Rileva i bug e risolvi i problemi in anticipo per garantire velocità di rilascio più veloci.
- Eseguire il commit del codice con maggiore frequenza e compilare rapidamente le applicazioni.
- Ottieni flessibilità per modificare rapidamente il codice senza problemi.
- Ottieni tempi di time-to-market più rapidi in modo che solo un buon codice di qualità lo faccia fino in fondo.
- Apportare piccole modifiche al codice in modo più efficiente perché piccole parti di codice sono integrate contemporaneamente.
- Aumenta la trasparenza e la responsabilità del team in modo da ottenere feedback continui dai tuoi clienti e dal tuo team.

Usare i servizi seguenti per abilitare una pipeline di integrazione continua nelle app per dispositivi mobili.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Build](/appcenter/build/) ti aiuta a creare applicazioni native e multipiattaforma su cui il tuo team sta lavorando usando un'infrastruttura cloud sicura. È possibile connettere facilmente il repository in Visual Studio App Center e iniziare a compilare l'app nel cloud a ogni commit. Non devi preoccuparti di configurare i server di compilazione in locale, le configurazioni complicate e il codice che si basa sulla macchina di un collega, ma non sul tuo.

Con la potenza aggiunta dei servizi di Visual Studio App Center, è possibile automatizzare ulteriormente il flusso di lavoro. Puoi rilasciare automaticamente le build ai tester e agli app store pubblici con App Center Distribute. È anche possibile eseguire test automatizzati dell'interfaccia utente su migliaia di configurazioni di dispositivi e sistemi di controllo sistema reali nel cloud con App Center Test.You also can run automated UI tests on thousands of real device and OS configurations in the cloud with App Center Test.

**Caratteristiche principali**
- Configura l'integrazione continua in pochi minuti e crea le applicazioni con maggiore frequenza e velocità.
- Integrazione con GitHub, BitBucket, Azure DevOps e GitLab.
- Crea build veloci e sicure su computer gestiti ospitati nel cloud.
- Abilita le compilazioni per avviare il test e verifica se l'app viene compilata in dispositivi iOS e Android reali.
- Ottieni supporto nativo e multipiattaforma per iOS, Android, macOS, Windows, Xamarin e React Native.
- Personalizza le tue compilazioni aggiungendo script post-clonazione, pre-compilazione e post-compilazione.

**Riferimenti**
- [Iscriversi a Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introduzione alla compilazione di App Center](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/), un servizio in Azure DevOps, è un servizio di integrazione continua e distribuzione continua (CD) completo che funziona con il provider Git preferito. Può essere distribuito alla maggior parte dei principali servizi cloud, che include Azure.It can deploy to most major cloud services, which includes Azure. È possibile iniziare con il codice in GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud o Azure Repos.You can start with your code on GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud, or Azure Repos. È quindi possibile automatizzare la compilazione, il test e la distribuzione del codice in Microsoft Azure, Google Cloud Platform o Amazon Web Services (AWS).

**Caratteristiche principali**
- **Esperienza semplificata basata sulle attività per la configurazione** di un server CI: Configurare un server CI per le applicazioni mobili native (Android, iOS e Windows) e multipiattaforma (Xamarin, Cordova e React Native), oltre alle tecnologie server basate su Microsoft e non Microsoft (Node.js, Java).
- **Qualsiasi lingua, piattaforma e cloud:** Compilare, testare e distribuire le applicazioni Node.js, Python, Java, PHP, Ruby, Go, C/C, C, Android e iOS. Esegui in parallelo su Linux, macOS e Windows. Distribuisci in provider cloud come Azure, AWS e Google Cloud Platform. Distribuisci le applicazioni mobili tramite canali beta e app store.
- **Supporto dei contenitori nativi:Native container support:** Crea nuovi contenitori con facilità e eseguiil push a qualsiasi registro. Distribuire contenitori ad host indipendenti o Kubernetes.
- **Flussi di lavoro avanzati:** Crea facilmente catene di build e build multifase. Ottieni supporto per YAML, integrazione di test, gate di rilascio, creazione di report e altro ancora.
- **Estensibile:** Usa una vasta gamma di attività di compilazione, test e distribuzione create dalla community, che include centinaia di estensioni da Slack a SonarCloud. È anche possibile eseguire la distribuzione da altri sistemi CI, ad esempio Jenkins.You can even deploy from other CI systems, like Jenkins. Gli hook Web e le API REST possono aiutarti a integrarti.
- **Compilazioni ospitate nel cloud gratuite:** Queste build sono disponibili per archivi pubblici e privati.
- **Supporto per la distribuzione ad altri fornitori di cloud:** I fornitori includono AWS e Google Cloud Platform.

**Riferimenti**
- [Guida introduttiva alle pipeline di AzureGet started with Azure Pipelines guide](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Introduzione a Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Avvi rapidi](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Per scegliere il servizio corretto per le compilazioni dell'applicazione, vedere l'articolo che confronta [la compilazione](/appcenter/build/choose-between-services)di App Center con le pipeline di Azure.
