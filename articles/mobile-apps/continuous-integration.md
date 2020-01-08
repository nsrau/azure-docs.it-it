---
title: Automatizza il ciclo di vita delle tue app con Visual Studio App Center e i servizi di Azure
description: Informazioni sui servizi, ad esempio App Center che consentono di configurare la compilazione continua e l'integrazione per le applicazioni per dispositivi mobili.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 9b0a9e10a88836ce83e636db20180c3692ab4429
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453182"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatizza il ciclo di vita delle tue app con compilazione e integrazione continue

Gli sviluppatori possono scrivere codice e archiviarlo nel repository di codice, ma i commit archiviati nel repository potrebbero non essere sempre coerenti. Quando più sviluppatori lavorano sullo stesso progetto, è possibile che si verifichino problemi di integrazione. I team potrebbero essere in situazioni in cui le cose non funzionano, i bug si accumulano e lo sviluppo del progetto viene ritardato. Gli sviluppatori devono attendere che l'intero codice software venga compilato e testato per verificare la presenza di errori, rendendo il processo lento e meno iterativo. 

Con la compilazione e l'integrazione continue, gli sviluppatori possono semplificare le compilazioni e testare il codice eseguendo il commit delle modifiche nel repository del codice sorgente e inserendo test e verifiche nell'ambiente di compilazione. In questo modo, eseguono sempre test sul codice. Tutte le modifiche apportate al codice sorgente vengono compilate continuamente ogni volta che viene eseguito un commit nel repository. Con ogni archiviazione, il server di integrazione continua (CI) convalida ed esegue tutti i test creati dallo sviluppatore. Se i test non vengono superati, il codice viene restituito per ulteriori modifiche. In questo modo, gli sviluppatori non interrompono le compilazioni create. Non è inoltre necessario eseguire tutti i test localmente nei propri computer, aumentando così la produttività degli sviluppatori. 

## <a name="key-benefits"></a>Vantaggi principali
- Automatizzare le compilazioni, i test e le distribuzioni per le pipeline.
- Rileva i bug e risolvi tempestivamente i problemi per garantire velocità di rilascio più veloci.
- Esegui il commit del codice con maggiore frequenza e crea rapidamente le applicazioni.
- Ottieni flessibilità per modificare rapidamente il codice senza problemi.
- Ottenere tempi di immissione sul mercato più rapidi, in modo che solo un codice di qualità valido lo faccia fino a questo punto.
- Apportare modifiche minime al codice in modo più efficiente, in quanto le piccole parti di codice sono integrate in una sola volta.
- Aumenta la trasparenza e la responsabilità del team in modo da ottenere feedback continuo dai tuoi clienti e dal tuo team.

Usare i servizi seguenti per abilitare una pipeline di integrazione continua nelle app per dispositivi mobili.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center compilazione](/appcenter/build/) consente di compilare applicazioni native e multipiattaforma su cui il team sta lavorando usando un'infrastruttura cloud sicura. È possibile connettere facilmente il repository in Visual Studio App Center e iniziare a compilare l'app nel cloud a ogni commit. Non è necessario preoccuparsi di configurare i server di compilazione localmente, le configurazioni complesse e il codice che si basa su un computer di un collaboratore, ma non sul proprio.

Grazie alla potenza aggiuntiva dei servizi di Visual Studio App Center, è possibile automatizzare ulteriormente il flusso di lavoro. È possibile rilasciare automaticamente le compilazioni ai tester e agli App Store pubblici con App Center distribuire. È anche possibile eseguire test automatizzati dell'interfaccia utente su migliaia di configurazioni del dispositivo e del sistema operativo reali nel cloud con App Center test.

**Caratteristiche principali**
- Configura l'integrazione continua in pochi minuti e compila le applicazioni con maggiore frequenza e più velocità.
- Eseguire l'integrazione con GitHub, BitBucket, Azure DevOps e GitLab.
- Crea Build veloci e sicure su computer gestiti e ospitati sul cloud.
- Abilitare le compilazioni per avviare il test e verificare se l'app viene compilata in dispositivi iOS e Android reali.
- Ottieni supporto nativo e multipiattaforma per iOS, Android, macOS, Windows, Novell e React native.
- Personalizzare le compilazioni aggiungendo script post-clone, pre-compilazione e post-compilazione.

**Riferimenti**
- [Iscriversi con Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introduzione a App Center Build](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/), un servizio in Azure DevOps, è un servizio di integrazione continua e recapito continuo (CD) con funzionalità complete che funziona con il provider git preferito. Può essere distribuito nella maggior parte dei principali servizi cloud, incluso Azure. È possibile iniziare con il codice in GitHub, GitHub Enterprise Server, GitLab, Bitbucket cloud o Azure Repos. Quindi, è possibile automatizzare la compilazione, il test e la distribuzione del codice per Microsoft Azure, Google Cloud Platform o Amazon Web Services (AWS).

**Caratteristiche principali**
- **Esperienza semplificata basata sulle attività per la configurazione di un server ci:** Configurare un server CI per le applicazioni per dispositivi mobili native (Android, iOS e Windows) e multipiattaforma (Novell, Cordova e React native), oltre alle tecnologie server basate su Microsoft e non Microsoft (node. js, Java).
- **Qualsiasi linguaggio, piattaforma e cloud:** Compila, testa e Distribuisci applicazioni node. js, Python, Java, php, Ruby, go, CC++/ C#,, Android e iOS. Esegui i processi in parallelo in Linux, macOS e Windows. Distribuisci a provider di servizi cloud come Azure, AWS e Google Cloud Platform. Distribuisci le applicazioni per dispositivi mobili tramite canali Beta e App Store.
- **Supporto del contenitore nativo:** È possibile creare nuovi contenitori con facilità ed eseguirne il push in qualsiasi registro. Distribuire i contenitori in host indipendenti o Kubernetes.
- **Flussi di lavoro avanzati:** Crea con facilità le catene di compilazione e le compilazioni in più fasi. Ottieni supporto per YAML, integrazione di test, cancelli di rilascio, Reporting e altro ancora.
- **Estendibile:** Usa una gamma di attività di compilazione, test e distribuzione compilate dalla community, che include centinaia di estensioni da Slack a SonarCloud. È anche possibile eseguire la distribuzione da altri sistemi CI, ad esempio Jenkins. Gli hook Web e le API REST possono essere utili per l'integrazione di.
- **Build gratuite ospitate nel cloud:** Queste build sono disponibili per i repository pubblici e privati.
- **Supporto per la distribuzione in altri fornitori di cloud:** I fornitori includono AWS e Google Cloud Platform.

**Riferimenti**
- [Guida introduttiva a Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Introduzione ad Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Guide introduttive](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Per facilitare la scelta del servizio appropriato per le compilazioni dell'applicazione, vedere l'articolo relativo al confronto tra [App Center Build e Azure Pipelines](/appcenter/build/choose-between-services).
