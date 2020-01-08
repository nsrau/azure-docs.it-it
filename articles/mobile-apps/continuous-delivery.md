---
title: Automatizzare la distribuzione e il rilascio delle applicazioni per dispositivi mobili con Visual Studio App Center e i servizi di Azure
description: Informazioni sui servizi, ad esempio App Center che consentono di configurare la pipeline di recapito continuo per le applicazioni per dispositivi mobili.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 6a8c8d9fc535d973c70eb2e477051dbd1dd1f6fb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454466"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatizzare la distribuzione e il rilascio delle applicazioni per dispositivi mobili con servizi di recapito continuo

Gli sviluppatori possono scrivere codice e archiviarlo nel repository di codice, ma i commit archiviati nel repository potrebbero non essere sempre coerenti. Quando più sviluppatori lavorano sullo stesso progetto, è possibile che si verifichino problemi di integrazione. I team potrebbero essere in situazioni in cui le cose non funzionano, i bug si accumulano e lo sviluppo del progetto viene ritardato. Gli sviluppatori devono attendere che l'intero codice software venga compilato e testato per verificare la presenza di errori, rendendo il processo lento e meno iterativo.

Con il recapito continuo, è possibile automatizzare la distribuzione e il rilascio delle applicazioni per dispositivi mobili. Non è importante se si sta distribuendo l'applicazione a un gruppo di tester o dipendenti della società (per il test beta) o a un App Store (per la produzione). Il recapito continuo rende le distribuzioni meno rischiose e incoraggia le iterazioni rapide. È anche possibile rilasciare nuove modifiche ai clienti in modo continuo.

## <a name="distribute-application-binaries-to-beta-testers"></a>Distribuire i file binari dell'applicazione a beta tester
Beta testing l'applicazione per dispositivi mobili è uno dei passaggi fondamentali durante il processo di sviluppo dell'applicazione. Consente di individuare tempestivamente i bug e i problemi nell'applicazione. Il feedback migliora la qualità dell'applicazione quando viene preparata per l'uso in produzione.

Usare i servizi seguenti per abilitare una pipeline di recapito continuo nelle app per dispositivi mobili.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center distribuire](/appcenter/distribution/) è uno strumento che consente agli sviluppatori di rilasciare rapidamente le compilazioni nei dispositivi. Con un'esperienza di installazione del portale completa, App Center distribuire è una soluzione potente per la distribuzione di beta app tester. Si tratta anche di una comoda alternativa alla distribuzione tramite App Store pubbliche. Gli sviluppatori possono automatizzare ulteriormente il flusso di lavoro di distribuzione con App Center Build e le integrazioni di archivio applicazioni pubbliche.

**Caratteristiche principali**
- Distribuisci la tua app a beta tester e utenti e assicurati che tutti i tester si trovino nella versione più recente dell'applicazione.
- Inviare una notifica ai tester di nuove versioni senza tester che passano di nuovo al flusso di download.
- Gestire i gruppi di distribuzione per versioni diverse dell'applicazione.
- Distribuisci negli archivi: 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- Ottieni supporto della piattaforma per iOS, Android, macOS, tvOS, Novell, React native, Unity e Cordova.
- Registrare automaticamente i dispositivi iOS nel profilo di provisioning.

**Riferimenti**
- [Iscriversi con Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introduzione alla distribuzione App Center](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) è un servizio di integrazione continua e recapito continuo (CD) con funzionalità complete che interagisce con il provider git preferito. Azure Pipelines possibile eseguire la distribuzione nella maggior parte dei servizi cloud principali, ad esempio i servizi di Azure. È possibile iniziare con il codice in GitHub, GitHub Enterprise Server, GitLab, Bitbucket cloud o Azure Repos. Quindi, è possibile automatizzare la compilazione, il test e la distribuzione del codice per Microsoft Azure, Google Cloud Platform o Amazon Web Services (AWS).

**Caratteristiche principali**
- **Esperienza semplificata basata sulle attività per la configurazione di un server ci:** Configurare un server CI per le applicazioni per dispositivi mobili native (Android, iOS e Windows) e multipiattaforma (Novell, Cordova e React native).
- **Qualsiasi linguaggio, piattaforma e cloud:** Compila, testa e Distribuisci app node. js, Python, Java, php, Ruby, go, CC++/ C#,, Android e iOS. Esegui i processi in parallelo in Linux, macOS e Windows. Distribuisci a provider di servizi cloud come Azure, AWS e Google Cloud Platform. Distribuisci le applicazioni per dispositivi mobili tramite canali Beta e App Store.
- **Supporto del contenitore nativo:** È possibile creare nuovi contenitori con facilità ed eseguirne il push in qualsiasi registro. Distribuire i contenitori in host indipendenti o Kubernetes.
- **Flussi di lavoro e funzionalità avanzati:** Crea con facilità le catene di compilazione e le compilazioni in più fasi. Ottieni supporto per YAML, integrazione di test, cancelli di rilascio, Reporting e altro ancora.
- **Estendibile:** Usa una gamma di attività di compilazione, test e distribuzione compilate dalla community, che include centinaia di estensioni da Slack a SonarCloud. È anche possibile eseguire la distribuzione da altri sistemi CI, ad esempio Jenkins. Gli hook Web e le API REST possono essere utili per l'integrazione di.
- **Build gratuite ospitate nel cloud:** Queste build sono disponibili per i repository pubblici e privati.
- **Supporto per la distribuzione in altri fornitori di cloud:** I fornitori includono AWS e Google Cloud Platform.

**Riferimenti**
- [Guida introduttiva a Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Introduzione ad Azure DevOps](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Distribuisci l'applicazione direttamente negli App Store
Quando l'applicazione è pronta per l'uso in ambiente di produzione e si vuole che venga usata pubblicamente, è necessario inviarla agli App Store dove può essere scaricata dai clienti. Esistono diversi modi per distribuire l'applicazione direttamente negli App Store. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
Con [App Center distribuire](/appcenter/distribution/stores/), è possibile pubblicare le applicazioni per dispositivi mobili direttamente negli App Store. Quando l'applicazione è pronta per essere scaricata dagli utenti, è possibile pubblicare i file binari dell'applicazione direttamente dal portale di Visual Studio App Center. 

È possibile distribuire direttamente a:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Store](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
Nell'App Store sviluppata e gestita da Apple, gli utenti possono esplorare e scaricare le applicazioni sviluppate per i dispositivi iOS, MacOS, Watchos e tvOS. Gli sviluppatori devono inviare le proprie app iOS all'App Store di Apple per l'uso pubblico.

### <a name="google-play"></a>Google Play

Google Play è l'App Store ufficiale per Android OS, in cui gli utenti possono esplorare e scaricare le applicazioni sviluppate per i dispositivi Android pubblicate tramite Google.

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management) è un servizio basato sul cloud nello spazio di gestione della mobilità aziendale che consente di garantire la produttività della forza lavoro mantenendo al tempo stesso la protezione dei dati aziendali. Con Intune, è possibile:
- Gestire i dispositivi mobili e i PC usati dalla forza lavoro per accedere ai dati aziendali.
- Gestire le applicazioni per dispositivi mobili utilizzate dalla forza lavoro.
- Proteggi le informazioni aziendali controllando la modalità di accesso e condivisione da parte della forza lavoro.
- Assicurarsi che i dispositivi e le applicazioni siano conformi ai requisiti di sicurezza aziendali.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Distribuisci gli aggiornamenti direttamente nei dispositivi degli utenti

### <a name="codepush"></a>CodePush
Con [codepush](/appcenter/distribution/codepush/) in App Center, gli sviluppatori nativi di Apache Cordova e React possono distribuire gli aggiornamenti delle applicazioni per dispositivi mobili direttamente nei dispositivi degli utenti. Funge da repository centrale a cui gli sviluppatori possono pubblicare determinati aggiornamenti, ad esempio le modifiche a JavaScript, HTML, CSS e immagini. Le applicazioni possono quindi eseguire query per gli aggiornamenti dal repository usando gli SDK client forniti. In questo modo, è possibile avere un modello di engagement più deterministico e diretto con gli utenti mentre si risolvono i bug o si aggiungono funzionalità di piccole dimensioni. Non è necessario ricompilare un file binario o ridistribuirlo tramite tutti gli App Store pubblici.

**Caratteristiche principali**
- Cordova e React gli sviluppatori nativi possono distribuire gli aggiornamenti delle applicazioni per dispositivi mobili direttamente nei dispositivi degli utenti senza rilasciare in uno Store.
- Utile per la correzione di bug o per l'aggiunta e la rimozione di funzionalità di piccole dimensioni che non richiedono la ricompilazione di file binari e la ridistribuzione tramite i rispettivi archivi.

**Riferimenti**
- [Iscriversi con Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introduzione a codepush in App Center](/appcenter/distribution/codepush/)
- [INTERFACCIA della riga di comando codepush](/appcenter/distribution/codepush/cli)