---
title: Recapito continuo per i Servizi cloud con Visual Studio Online | Documentazione Microsoft
description: Informazioni su come configurare il recapito continuo per app cloud di Azure senza salvare la chiave di archiviazione di diagnostica nei file di configurazione del servizio
services: cloud-services
documentationcenter: 
author: cawa
manager: paulyuk
editor: 
ms.assetid: 148b2959-c5db-4e4a-a7e9-fccb252e7e8a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/02/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: 165ab7363efaf90eaab41098f71e2f1b846c346e
ms.openlocfilehash: 7e70f92d4d1333ca6cbac5876e5ccbc763bd915c

---
# <a name="securely-save-cloud-services-diagnostics-storage-key-and-setup-continuous-integration-and-deployment-to-azure-using-visual-studio-online"></a>Salvare in modo sicuro la chiave di archiviazione di diagnostica dei Servizi cloud e configurare l'integrazione e la distribuzione continue in Azure usando Visual Studio Online
 L'uso di progetti open source è attualmente molto diffuso. Il salvataggio dei segreti dell'applicazione nei file di configurazione non è più considerato sicuro, perché comporta l'esposizione di vulnerabilità della sicurezza a causa della diffusione dei segreti dai controlli di origine pubblica. L'archiviazione del segreto come testo normale in un file in una pipeline di integrazione continua non risulta sicura, perché i server di compilazione potrebbero essere risorse condivise nell'ambiente cloud. Questo articolo illustra il modo in cui Visual Studio e Visual Studio Online consentono di ridurre i problemi di sicurezza durante il processo di sviluppo e di integrazione continua.

## <a name="remove-diagnostics-storage-key-secret-in-project-configuration-file"></a>Rimuovere il segreto della chiave di archiviazione di diagnostica dal file di configurazione del progetto
L'estensione di diagnostica dei Servizi cloud richiede l'Archiviazione di Azure per il salvataggio dei risultati di diagnostica. La stringa di connessione di archiviazione veniva in precedenza specificata nei file di configurazione dei Servizi cloud (con estensione cscfg) e poteva essere archiviata nel controllo del codice sorgente. Nella versione più recente di Azure SDK il comportamento è stato modificato, in modo che venga archiviata solo una stringa di connessione parziale e che la chiave venga sostituita da un token. La procedura seguente illustra il funzionamento dei nuovi strumenti dei Servizi cloud:

### <a name="1-open-the-role-designer"></a>1. Aprire la finestra di progettazione dei ruoli
* Fare doppio clic o fare clic con il pulsante destro del mouse su un ruolo dei Servizi cloud per aprire la finestra di progettazione dei ruoli

![Aprire la finestra di progettazione dei ruoli][0]

### <a name="2-under-diagnostics-section-a-new-check-box-dont-remove-storage-key-secret-from-project-is-added"></a>2. Nella sezione relativa alla diagnostica è stata aggiunta una nuova casella di controllo "Non rimuovere il segreto della chiave di archiviazione dal file di configurazione del progetto (.cscfg)"
* Se si usa l'emulatore di archiviazione locale, questa casella di controllo è disabilitata perché non sono presenti segreti da gestire per la stringa di connessione locale, ovvero UseDevelopmentStorage=true.

![La stringa di connessione dell'emulatore di archiviazione locale non è segreta][1]

* Se si crea un nuovo progetto, questa casella di controllo è deselezionata per impostazione predefinita. La sezione relativa alla chiave di archiviazione della stringa di connessione selezionata viene quindi sostituita da un token. Il valore del token sarà disponibile nella cartella AppData\Roaming dell'utente corrente, ad esempio: C:\Users\contosouser\AppData\Roaming\Microsoft\CloudService

> Si noti che l'accesso alla cartella user\AppData è controllato tramite gli accessi utente e la cartella viene considerata una posizione sicura per l'archiviazione dei segreti di sviluppo.
> 
> 

![La chiave di archiviazione viene salvata nella cartella del profilo utente][2]

### <a name="3-select-a-diagnostics-storage-account"></a>3. Selezionare un account di archiviazione di diagnostica
* Selezionare un account di archiviazione dalla finestra di dialogo visualizzata quando si fa clic sui puntini di sospensione "…" . Si noti che la stringa di connessione di archiviazione generata non avrà la chiave dell'account di archiviazione.
* Ad esempio: DefaultEndpointsProtocol=https;AccountName=contosostorage;AccountKey=$(*clouddiagstrg.key*)

### <a name="4----debugging-the-project"></a>4.    Debug del progetto
* Premere F5 per avviare il debug in Visual Studio. Non sono state apportate modifiche rispetto al comportamento precedente.
  ![Avviare il debug localmente][3]

### <a name="5-publish-project-from-visual-studio"></a>5. Pubblicare il progetto da Visual Studio
* Avviare la finestra di dialogo di pubblicazione e seguire le istruzioni di accesso per pubblicare l'applicazione in Azure.

### <a name="6-additional-information"></a>6. Informazioni aggiuntive
> Nota: il pannello Impostazioni nella finestra di progettazione dei ruoli non viene modificato, per il momento. Se si vuole usare la funzionalità di gestione dei segreti per la diagnostica, passare alla scheda Configurazioni.
> 
> 

![Aggiungere le impostazioni][4]

> Nota: se abilitata, la chiave di Application Insights verrà archiviata come testo normale. La chiave viene usata solo per caricare contenuti, quindi non si correrà alcun rischio di compromissione di dati sensibili.
> 
> 

## <a name="build-and-publish-a-cloud-services-project-using-visual-studio-online-task-templates"></a>Compilare e pubblicare un progetto di Servizi cloud usando i modelli di attività di Visual Studio Online
* La procedura seguente illustra come configurare l'integrazione continua per un progetto di Servizi cloud usando le attività di Visual Studio Online:
  ### <a name="1----obtain-a-vso-account"></a>1.    Ottenere un account VSO
* [Creare un account Visual Studio Online][Creare un account Visual Studio Online], se non è già disponibile
* [Creare un progetto team][Creare un progetto team] nell'account Visual Studio Online

### <a name="2----setup-source-control-in-visual-studio"></a>2.    Configurare il controllo del codice sorgente in Visual Studio
* Connettersi a un progetto team

![Connettersi a un progetto team][5]

![Selezionare il progetto team a cui connettersi][6]

* Aggiungere il progetto al controllo del codice sorgente

![Aggiungere il progetto al controllo del codice sorgente][7]

![Eseguire il mapping del progetto a una cartella del controllo del codice sorgente][8]

* Archiviare il progetto da Team Explorer

![Archiviare il progetto nel controllo del codice sorgente][9]

### <a name="3----configure-build-process"></a>3.    Configurare il processo di configurazione
* Passare al progetto team e aggiungere un nuovo processo di compilazione ai modelli

![Aggiungere una nuova compilazione][10]

* Selezionare un'attività di compilazione

![Aggiungere un'attività di compilazione][11]

![Selezionare il modello di attività di compilazione di Visual Studio][12]

* Modificare l'input dell'attività di compilazione. Personalizzare i parametri di compilazione in base alle esigenze specifiche

![Configurare l'attività di compilazione][13]

`/t:Publish /p:TargetProfile=$(targetProfile) /p:DebugType=None /p:SkipInvalidConfigurations=true /p:OutputPath=bin\ /p:PublishDir="$(build.artifactstagingdirectory)\\"`

* Configurare le variabili di compilazione

![Configurare le variabili di compilazione][14]

* Aggiungere un'attività per caricare la destinazione finale della compilazione

![Scegliere l'attività di pubblicazione della destinazione finale della compilazione][15]

![Configurare l'attività di pubblicazione della destinazione finale della compilazione][16]

* Eseguire la compilazione

![Accodare la nuova compilazione][17]

![Visualizzare il riepilogo della compilazione][18]

* se la compilazione viene completata correttamente, verrà visualizzato un risultato simile a questo

![Risultato della compilazione][19]

### <a name="4----configure-release-process"></a>4.    Configurare il processo di rilascio
* Creare una nuova versione

![Creare una nuova versione][20]

* Selezionare l'attività di distribuzione dei Servizi cloud di Azure

![Selezionare l'attività di distribuzione dei Servizi cloud di Azure][21]

* Poiché la chiave dell'account di archiviazione non viene archiviata nel controllo del codice sorgente,è necessario specificare la chiave privata per la configurazione delle estensioni di diagnostica. Espandere la sezione **Opzioni avanzate per la creazione del nuovo servizio** e modificare l'input del parametro **Chiavi dell'account di archiviazione di diagnostica**. L'input accetta più righe della coppia chiave-valore con formato **[NomeRuolo]:$(ChiaveAccountArchiviazione)**

> Nota: se l'account di archiviazione di diagnostica si trova nella stessa sottoscrizione in cui verrà pubblicata l'applicazione di Servizi cloud, non è necessario immettere la chiave nell'input dell'attività di distribuzione. La distribuzione otterrà le informazioni di archiviazione a livello di codice dalla sottoscrizione.
> 
> 

![Configurare l'attività di distribuzione dei Servizi cloud di Azure][22]

* Usare le variabili di compilazione del segreto per salvare le chiavi di archiviazione. Per mascherare una variabile come segreto, fare clic sull'icona a forma di lucchetto a destra dell'input Variabili

![Salvare le chiavi di archiviazione nelle variabili di compilazione del segreto][23]

* Creare una versione e distribuire il progetto in Azure

![Creare una nuova versione][24]

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla configurazione delle estensioni di diagnostica per i Servizi cloud di Azure, vedere [Abilitare la diagnostica nei servizi Cloud di Azure tramite PowerShell][Abilitare la diagnostica nei servizi Cloud di Azure tramite PowerShell]

[Creare un account Visual Studio Online]:https://www.visualstudio.com/team-services/
[Creare un progetto team]: https://www.visualstudio.com/it-it/docs/setup-admin/team-services/connect-to-visual-studio-team-services
[Abilitare la diagnostica nei servizi Cloud di Azure tramite PowerShell]:https://azure.microsoft.com/en-us/documentation/articles/cloud-services-diagnostics-powershell/

[0]: ./media/cloud-services-vs-ci/vs-01.png
[1]: ./media/cloud-services-vs-ci/vs-02.png
[2]: ./media/cloud-services-vs-ci/file-01.png
[3]: ./media/cloud-services-vs-ci/vs-03.png
[4]: ./media/cloud-services-vs-ci/vs-04.png
[5]: ./media/cloud-services-vs-ci/vs-05.png
[6]: ./media/cloud-services-vs-ci/vs-06.png
[7]: ./media/cloud-services-vs-ci/vs-07.png
[8]: ./media/cloud-services-vs-ci/vs-08.png
[9]: ./media/cloud-services-vs-ci/vs-09.png
[10]: ./media/cloud-services-vs-ci/vso-01.png
[11]: ./media/cloud-services-vs-ci/vso-02.png
[12]: ./media/cloud-services-vs-ci/vso-03.png
[13]: ./media/cloud-services-vs-ci/vso-04.png
[14]: ./media/cloud-services-vs-ci/vso-05.png
[15]: ./media/cloud-services-vs-ci/vso-06.png
[16]: ./media/cloud-services-vs-ci/vso-07.png
[17]: ./media/cloud-services-vs-ci/vso-08.png
[18]: ./media/cloud-services-vs-ci/vso-09.png
[19]: ./media/cloud-services-vs-ci/vso-10.png
[20]: ./media/cloud-services-vs-ci/vso-11.png
[21]: ./media/cloud-services-vs-ci/vso-12.png
[22]: ./media/cloud-services-vs-ci/vso-13.png
[23]: ./media/cloud-services-vs-ci/vso-14.png
[24]: ./media/cloud-services-vs-ci/vso-15.png



<!--HONumber=Nov16_HO3-->


