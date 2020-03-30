---
title: Automatizzare la distribuzione e il rilascio delle applicazioni mobili con Visual Studio App Center e i servizi di AzureAutomate the deployment and release of your mobile applications with Visual Studio App Center and Azure services
description: Informazioni sui servizi, ad esempio App Center, che consentono di configurare la pipeline di distribuzione continua per le applicazioni mobili.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ccfd0fc0450a6fbd34192dce7e375fe2de7f47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235345"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatizza la distribuzione e il rilascio delle tue applicazioni mobili con servizi di distribuzione continua

Gli sviluppatori scrivono il codice e lo archiviano nel repository di codice, ma i commit archiviati nel repository potrebbero non essere sempre coerenti. Quando più sviluppatori lavorano sullo stesso progetto, possono essere risolti problemi di integrazione. I team potrebbero incorrere in situazioni in cui le cose non funzionano, i bug si accumulano e lo sviluppo del progetto viene ritardato. Gli sviluppatori devono attendere fino a quando l'intero codice software viene costruito e testato per verificare la presenza di errori, il che rende il processo lento e meno iterativo.

Con la distribuzione continua, si automatizza la distribuzione e il rilascio delle applicazioni mobili. Non importa se stai distribuendo l'applicazione a un gruppo di tester o dipendenti dell'azienda (per il beta testing) o a un app store (per la produzione). La distribuzione continua rende le distribuzioni meno rischiose e incoraggia le iterazioni veloci. Puoi anche rilasciare nuove modifiche ai tuoi clienti in modo continuo.

## <a name="distribute-application-binaries-to-beta-testers"></a>Distribuire i file binari dell'applicazione ai beta testerDistribute application binaries to beta testers
Il beta testing dell'applicazione mobile è uno dei passaggi critici durante il processo di sviluppo dell'applicazione. Aiuta a trovare bug e problemi nell'applicazione nelle prime fasi. Il feedback migliora la qualità dell'applicazione quando la si prepara per l'uso in produzione.

Usare i servizi seguenti per abilitare una pipeline di distribuzione continua nelle app per dispositivi mobili.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Distribute](/appcenter/distribution/) è uno strumento che consente agli sviluppatori di rilasciare rapidamente le build ai dispositivi. Con un'esperienza completa del portale di installazione, App Center Distribute è una soluzione potente per la distribuzione di beta tester di app. È anche una comoda alternativa alla distribuzione attraverso app store pubblici. Gli sviluppatori possono automatizzare ulteriormente il flusso di lavoro di distribuzione con App Center Build e le integrazioni degli archivi applicazioni pubblici.

**Caratteristiche principali**
- Distribuisci la tua app a beta tester e utenti e assicurati che tutti i tester siano sulla versione più recente dell'applicazione.
- Informare i tester delle nuove versioni senza che i tester eserciteranno nuovamente il flusso di download.
- Gestire i gruppi di distribuzione per le diverse versioni dell'applicazione.
- Distribuisci ai negozi: 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- Ottieni il supporto della piattaforma per iOS, Android, macOS, tvOS, Xamarin, React Native, Unity e Cordova.
- Registra automaticamente i dispositivi iOS nel tuo profilo di provisioning.

**Riferimenti**
- [Iscriversi a Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introduzione all'app Center Distribute](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Pipeline di Azure](https://azure.microsoft.com/services/devops/pipelines/) è un servizio di integrazione continua (CI) e distribuzione continua (CD) completo che funziona con il provider Git preferito. Le pipeline di Azure possono essere distribuite nella maggior parte dei servizi cloud principali, ad esempio i servizi di Azure.Azure Pipelines can deploy to most major cloud services, such as Azure services. È possibile iniziare con il codice in GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud o Azure Repos.You can start with your code on GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud, or Azure Repos. È quindi possibile automatizzare la compilazione, il test e la distribuzione del codice in Microsoft Azure, Google Cloud Platform o Amazon Web Services (AWS).

**Caratteristiche principali**
- **Esperienza semplificata basata sulle attività per la configurazione** di un server CI: Configurare un server CI per applicazioni mobili native (Android, iOS e Windows) e multipiattaforma (Xamarin, Cordova e React Native).
- **Qualsiasi lingua, piattaforma e cloud:** Compilare, testare e distribuire le app Node.js, Python, Java, PHP, Ruby, Go, C/C, C' E iOS. Esegui in parallelo su Linux, macOS e Windows. Distribuisci in provider cloud come Azure, AWS e Google Cloud Platform. Distribuisci le applicazioni mobili tramite canali beta e app store.
- **Supporto dei contenitori nativi:Native container support:** Crea nuovi contenitori con facilità e eseguiil push a qualsiasi registro. Distribuire contenitori ad host indipendenti o Kubernetes.
- **Flussi di lavoro e funzionalità avanzate:** Crea facilmente catene di build e build multifase. Ottieni supporto per YAML, integrazione di test, gate di rilascio, creazione di report e altro ancora.
- **Estensibile:** Usa una vasta gamma di attività di compilazione, test e distribuzione create dalla community, che include centinaia di estensioni da Slack a SonarCloud. È anche possibile eseguire la distribuzione da altri sistemi CI, ad esempio Jenkins.You can even deploy from other CI systems, like Jenkins. Gli hook Web e le API REST possono aiutarti a integrarti.
- **Compilazioni ospitate nel cloud gratuite:** Queste build sono disponibili per archivi pubblici e privati.
- **Supporto per la distribuzione ad altri fornitori di cloud:** I fornitori includono AWS e Google Cloud Platform.

**Riferimenti**
- [Guida introduttiva alle pipeline di AzureGet started with Azure Pipelines guide](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Introduzione a Azure DevOps](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Distribuire l'applicazione direttamente ad App Store
Dopo che l'applicazione è pronta per l'uso in produzione e si desidera che venga usata pubblicamente, deve essere inviata agli app store in cui può essere scaricata dai clienti. Esistono diversi modi per distribuire l'applicazione direttamente agli app store. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
Con [App Center Distribute,](/appcenter/distribution/stores/)puoi pubblicare le tue applicazioni per dispositivi mobili direttamente negli app store. Quando l'applicazione è pronta per essere scaricata dagli utenti, è possibile pubblicare i file binari dell'applicazione direttamente dal portale di Visual Studio App Center. 

È possibile distribuire direttamente a:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Store](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
Nell'app store sviluppato e gestito da Apple, gli utenti possono sfogliare e scaricare le applicazioni sviluppate per dispositivi iOS, MacOS, WatchOS e tvOS. Gli sviluppatori devono inviare le proprie app iOS all'App Store di Apple per un uso pubblico.

### <a name="google-play"></a>Google Play

Google Play è l'app store ufficiale per il sistema operativo Android, dove gli utenti possono sfogliare e scaricare le applicazioni sviluppate per i dispositivi Android che vengono pubblicate tramite Google.

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management) è un servizio basato su cloud nello spazio di gestione della mobilità aziendale che consente di consentire alla forza lavoro di essere produttiva mantenendo al contempo protetti i dati aziendali. Con Intune, è possibile:
- Gestire i dispositivi mobili e i PC usati dalla forza lavoro per accedere ai dati aziendali.
- Gestisci le applicazioni mobili utilizzate dalla tua forza lavoro.
- Proteggi le informazioni della tua azienda controllando il modo in cui la tua forza lavoro accede e le condivide.
- Assicurarsi che i dispositivi e le applicazioni siano conformi ai requisiti di sicurezza aziendali.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Distribuire gli aggiornamenti direttamente ai dispositivi degli utentiDeploy updates directly to users' devices

### <a name="codepush"></a>CodePush
Con [CodePush](/appcenter/distribution/codepush/) in App Center, gli sviluppatori Apache Cordova e React Native possono distribuire gli aggiornamenti delle applicazioni mobili direttamente nei dispositivi degli utenti. Funge da archivio centrale a cui gli sviluppatori possono pubblicare determinati aggiornamenti, ad esempio JavaScript, HTML, CSS e modifiche alle immagini. Le applicazioni possono quindi eseguire query per gli aggiornamenti dal repository utilizzando gli SDK client forniti. In questo modo, è possibile avere un modello di engagement più deterministico e diretto con gli utenti mentre si affrontano i bug o si aggiungono piccole funzionalità. Non è necessario ricompilare un file binario o ridistribuirlo tramite archivi app pubblici.

**Caratteristiche principali**
- Gli sviluppatori Cordova e React Native possono distribuire gli aggiornamenti delle applicazioni mobili direttamente nei dispositivi degli utenti senza rilasciarli in un archivio.
- Utile per correggere bug o aggiungere e rimuovere piccole funzionalità che non richiedono la ricostruzione di file binarie e ridistribuirlo nei rispettivi archivi.

**Riferimenti**
- [Iscriversi a Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introduzione a CodePush in App Center](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)