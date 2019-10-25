---
title: Automatizzare la distribuzione e il rilascio delle applicazioni per dispositivi mobili con Visual Studio App Center e i servizi di Azure
description: Informazioni sui servizi, ad esempio App Center che consentono di configurare la pipeline di recapito continuo per le applicazioni per dispositivi mobili.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 3b8305d02345abe98b674a8ed61626eaf6b5528d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795601"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatizzare la distribuzione e il rilascio delle applicazioni per dispositivi mobili con servizi di recapito continuo

Gli sviluppatori possono scrivere il codice e archiviarlo nel repository del codice, ma i commit archiviati nel repository potrebbero non essere sempre coerenti. Con più sviluppatori che lavorano sullo stesso progetto, i problemi si verificano con l'integrazione e il team si trova in situazioni in cui le cose non funzionano, i bug continuano ad accumularsi e lo sviluppo del progetto viene ritardato. Gli sviluppatori devono attendere che l'intero codice software venga compilato e testato per verificare la presenza di errori, rendendo il processo lento e meno iterativo.

Con il **recapito continuo**, è possibile automatizzare la distribuzione e il rilascio delle applicazioni per dispositivi mobili, indipendentemente dal fatto che l'applicazione venga distribuita a un gruppo di tester o dipendenti della società (per il test beta) o all'App Store (per la produzione). Rende le distribuzioni meno rischiose, incoraggia le iterazioni rapide e consente di rilasciare le nuove modifiche ai clienti in modo continuo.

## <a name="distribute-application-binaries-to-beta-testers"></a>Distribuire i file binari dell'applicazione a beta tester
Beta testing l'applicazione per dispositivi mobili è uno dei passaggi fondamentali durante il processo di sviluppo dell'applicazione. Consente di individuare tempestivamente i bug e i problemi dell'applicazione e il feedback che migliora la qualità dell'applicazione per prepararsi all'uso in produzione.

Usare i servizi seguenti per abilitare la pipeline di recapito continuo nelle app per dispositivi mobili.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center distribuire](/appcenter/distribution/) è uno strumento che consente agli sviluppatori di rilasciare rapidamente le compilazioni ai dispositivi degli utenti finali. Con un'esperienza di installazione completa del portale, Distribute non è solo una soluzione potente per la distribuzione di beta app tester, ma anche una comoda alternativa alla distribuzione tramite gli App Store pubblici. Gli sviluppatori possono automatizzare ulteriormente il flusso di lavoro di distribuzione con App Center Build e le integrazioni di archivio applicazioni pubbliche.

**Funzionalità principali**
- **Distribuisci la tua app a beta tester e utenti** e assicurati che tutti i tester si trovino nella versione più recente dell'applicazione.
- **Inviare una notifica ai tester di nuove versioni** senza tester che passano di nuovo al flusso di download.
- **Gestire i gruppi di distribuzione** per versioni diverse dell'applicazione.
- **Distribuzione negli archivi** 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- **Supporto della piattaforma** : iOS, Android, MacOS, TvOS, Novell, React native, Unity, Cordova.
- Registrare automaticamente i dispositivi iOS nel profilo di provisioning.

**Riferimenti**
- [Iscriversi con App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introduzione alla distribuzione App Center](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) è un servizio di integrazione continua e recapito continuo (CD) con funzionalità complete che funziona con il provider git preferito e può essere distribuito nella maggior parte dei principali servizi cloud, inclusi i servizi di Azure. È possibile iniziare con il codice in GitHub, GitHub Enterprise Server, GitLab, Bitbucket cloud o Azure Repos. È quindi possibile automatizzare la compilazione, il testing e la distribuzione del codice a Microsoft Azure, Google Cloud Platform o Amazon Web Services.

**Funzionalità principali**
- Esperienza semplificata basata sulle attività per la configurazione di un server CI per **le applicazioni per dispositivi mobili native (Android, iOS e Windows) e multipiattaforma (Novell, Cordova e React native)** .
- **Qualsiasi linguaggio, piattaforma e app per** la compilazione, il test e la distribuzione di node. js, Python, Java, php, Ruby, go,C++C C#/,, Android e iOS. Esegui i processi in parallelo in Linux, macOS e Windows. Distribuisci a provider di servizi cloud come Azure, AWS e GCP. Distribuisci le applicazioni per dispositivi mobili tramite canali Beta e App Store.
- **Supporto per contenitori nativi** : è possibile creare nuovi contenitori con facilità ed eseguirne il push in qualsiasi registro. Distribuire i contenitori in host indipendenti o Kubernetes.
- **Flussi di lavoro e funzionalità avanzati** : semplice concatenamento della compilazione e compilazioni in più fasi. Supporto per YAML, integrazione di test, cancelli di rilascio, creazione di report e altro ancora.
- **Estendibile** : usa una gamma di attività di compilazione, test e distribuzione compilate dalla community: centinaia di estensioni da Slack a SonarCloud. È anche possibile eseguire la distribuzione da altri sistemi CI, ad esempio Jenkins. Webhook e API REST consentono di integrare
- **Build gratuite ospitate nel cloud** per repository pubblici e privati.
- **Supporta la distribuzione in altri fornitori di cloud** come AWS, GCP e così via.

**Riferimenti**
- [Guida introduttiva a Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Introduzione ad Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Distribuisci l'applicazione direttamente negli App Store
Quando l'applicazione è pronta per l'uso in ambiente di produzione e si vuole che venga usata pubblicamente, è necessario inviarla agli App Store dove può essere scaricata dai clienti. Esistono diversi modi per distribuire l'applicazione direttamente negli App Store. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center distribuire](/appcenter/distribution/stores/) il servizio consente di pubblicare le applicazioni per dispositivi mobili direttamente negli App Store. Quando l'applicazione è pronta per essere scaricata dagli utenti finali, è possibile pubblicare i file binari dell'applicazione direttamente dal portale di App Center.  

È possibile distribuire direttamente a:
- [App Store di Apple](/appcenter/distribution/stores/apple)
- [Google Play Store](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>App Store di Apple
L'App Store sviluppata e gestita da Apple consente agli utenti di esplorare e scaricare le applicazioni sviluppate per i dispositivi iOS, MacOS, Watchos e tvOS. Gli sviluppatori devono inviare le proprie app iOS ad Apple Store per l'uso pubblico.

### <a name="google-play"></a>Google Play

Google Play è l'App Store ufficiale per Android OS, consentendo agli utenti di esplorare e scaricare le applicazioni sviluppate per i dispositivi Android e pubblicate tramite Google.

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management) è un servizio basato sul cloud nello spazio di gestione della mobilità aziendale (EMM) che consente di garantire la produttività della forza lavoro mantenendo al tempo stesso la protezione dei dati aziendali. Con Intune è possibile 
- Gestire i dispositivi mobili e i PC usati dalla forza lavoro per accedere ai dati aziendali.
- Gestire le applicazioni per dispositivi mobili utilizzate dalla forza lavoro.
- Proteggi le informazioni aziendali controllando la modalità di accesso e condivisione da parte della forza lavoro.
- Verificare che i dispositivi e le applicazioni siano conformi ai requisiti di sicurezza aziendali.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Distribuisci gli aggiornamenti direttamente nei dispositivi degli utenti

### <a name="codepush"></a>CodePush
Il servizio [codepush](/appcenter/distribution/codepush/) in App Center consente agli sviluppatori nativi di Apache Cordova e React di distribuire gli aggiornamenti delle applicazioni per dispositivi mobili direttamente nei dispositivi degli utenti. Funziona agendo come un repository centrale a cui gli sviluppatori possono pubblicare determinati aggiornamenti (ad esempio, JS, HTML, CSS e modifiche alle immagini). Le applicazioni possono quindi eseguire query per gli aggiornamenti dal repository usando gli SDK client forniti. In questo modo è possibile avere un modello di engagement più deterministico e diretto con gli utenti finali, durante l'indirizzamento dei bug o l'aggiunta di funzionalità di piccole dimensioni senza richiedere la ricompilazione di un file binario o la ridistribuzione tramite tutti gli App Store pubblici.

**Funzionalità principali**
- Consente a Cordova e a reagire agli sviluppatori nativi di distribuire gli aggiornamenti delle applicazioni per dispositivi mobili direttamente nei dispositivi degli utenti senza rilasciare in uno Store.
- Utile per la correzione di bug o per l'aggiunta o la rimozione di piccole funzionalità che non richiedono la ricompilazione di file binari e la ridistribuzione tramite i rispettivi negozi

**Riferimenti**
- [Iscriversi con App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introduzione al push del codice in App Center](/appcenter/distribution/codepush/)
- [INTERFACCIA della riga di comando codepush](/appcenter/distribution/codepush/cli)