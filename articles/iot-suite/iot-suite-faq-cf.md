---
title: Domande frequenti sulla soluzione di fabbrica connessa - Azure | Microsoft Docs
description: Domande frequenti sulla soluzione di connected factory di IoT Suite
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: ab72152fc937e3c4552147fce29c95ea0efcadf4
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="frequently-asked-questions-for-iot-suite-connected-factory-preconfigured-solution"></a>Domande frequenti sulla soluzione preconfigurata di connected factory di IoT Suite

Vedere anche le [domande frequenti](iot-suite-faq.md) generali su IoT Suite.

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solution"></a>Dove è possibile visualizzare il codice sorgente per la soluzione preconfigurata?

Il codice sorgente è archiviato nel repository di GitHub seguente:

* [Connected factory preconfigured solution](https://github.com/Azure/azure-iot-connected-factory) (Soluzione preconfigurata di connected factory)

### <a name="what-is-opc-ua"></a>Che cos'è OPC UA?

OPC Unified Architecture (UA), rilasciato nel 2008, è uno standard di interoperabilità indipendente dalla piattaforma e orientato ai servizi. OPC UA viene anche usato da svariati sistemi e dispositivi di settore, ad esempio PC, PLC e sensori. OPC UA integra la funzionalità delle specifiche OPC Classic in un unico framework estendibile con sicurezza predefinita. È uno standard definito da OPC Foundation. [OPC Foundation](http://opcfoundation.org/) è un'organizzazione no profit che conta più di 440 membri. L'obiettivo dell'organizzazione è di usare le specifiche OPC per realizzare un'interoperabilità sicura e affidabile multifornitore e multipiattaforma tramite:

* Infrastruttura
* Specifiche
* Tecnologia
* Processi

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-preconfigured-solution"></a>Perché Microsoft ha scelto OPC UA per la soluzione preconfigurata di connected factory?

Microsoft ha scelto OPC UA perché è uno standard aperto, non proprietario, indipendente dalla piattaforma, riconosciuto nel settore e collaudato. È un requisito per le soluzioni basate sull'architettura di riferimento Industrie 4.0 (RAMI4.0) che assicurano l'interoperabilità tra un'ampia serie di processi e attrezzature di produzione. Microsoft assiste alla crescente richiesta da parte dei clienti di creare soluzioni conformi al piano Industria 4.0. Il supporto per OPC UA consente di agevolare i clienti nel raggiungimento degli obiettivi e offre immediatamente valore aggiunto.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Come è possibile aggiungere un indirizzo IP pubblico alla macchina virtuale di simulazione?

Sono disponibili due opzioni per aggiungere l'indirizzo IP:

* Usare lo script di PowerShell `Simulation/Factory/Add-SimulationPublicIp.ps1` nel [repository](https://github.com/Azure/azure-iot-connected-factory). Passare il nome della distribuzione come parametro. Per una distribuzione locale, usare `<your username>ConnFactoryLocal`. Lo visualizza l'indirizzo IP della VM.

* Nel portale di Azure individuare il gruppo di risorse della distribuzione. Fatta eccezione per una distribuzione locale, il gruppo di risorse ha il nome specificato come nome della soluzione o della distribuzione. Per una distribuzione locale che usa lo script di compilazione, il nome del gruppo di risorse è `<your username>ConnFactoryLocal`. Aggiungere ora una nuova risorsa **Indirizzo IP pubblico** al gruppo di risorse.

> [!NOTE]
> In entrambi i casi, assicurarsi di installare le patch più recenti seguendo le istruzioni del [sito Web Ubuntu](https://wiki.ubuntu.com/Security/Upgrades). Mantenere l'installazione aggiornata finché la VM è accessibile tramite un indirizzo IP pubblico.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Come è possibile rimuovere l'indirizzo IP pubblico alla macchina virtuale di simulazione?

Sono disponibili due opzioni per rimuovere l'indirizzo IP:

* Usare lo script di PowerShell Simulation/Factory/Remove-SimulationPublicIp.ps1 del [repository](https://github.com/Azure/azure-iot-connected-factory). Passare il nome della distribuzione come parametro. Per una distribuzione locale, usare `<your username>ConnFactoryLocal`. Lo visualizza l'indirizzo IP della VM.

* Nel portale di Azure individuare il gruppo di risorse della distribuzione. Fatta eccezione per una distribuzione locale, il gruppo di risorse ha il nome specificato come nome della soluzione o della distribuzione. Per una distribuzione locale che usa lo script di compilazione, il nome del gruppo di risorse è `<your username>ConnFactoryLocal`. Rimuovere ora la risorsa **Indirizzo IP pubblico** dal gruppo di risorse.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Come è possibile accedere alla VM di simulazione?

L'accesso alla VM di simulazione è supportato solo se la soluzione è stata distribuita usando lo script di PowerShell `build.ps1` nel [repository](https://github.com/Azure/azure-iot-connected-factory).

Se la soluzione è stata distribuita da www.azureiotsuite.com, non è possibile accedere alla VM perché la password viene generata in modo casuale e non è possibile reimpostarla.

1. Aggiungere un indirizzo IP pubblico alla VM. Vedere [Come è possibile aggiungere un indirizzo IP pubblico alla macchina virtuale di simulazione?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Creare una sessione SSH per la macchina virtuale usando l'indirizzo IP della macchina virtuale.
1. Il nome utente da usare è: `docker`.
1. La password da usare dipende dalla versione usata per la distribuzione:
    * Per le soluzioni distribuite usando lo script build.ps1 prima dell'1 giugno 2017, la password è: `Passw0rd`.
    * Per le soluzioni distribuite usando lo script build.ps1 dopo l'1 giugno 2017, è possibile trovare la password nel file `<name of your deployment>.config.user`. La password viene archiviata nell'impostazione **VmAdminPassword**. La password viene generata in modo casuale in fase di distribuzione a meno che non la si specifichi usando il parametro `-VmAdminPassword` dello script `build.ps1`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Come è possibile arrestare e avviare tutti i processi docker nella VM di simulazione?

1. Accedere alla VM di simulazione. Vedere [Come è possibile accedere alla VM di simulazione?](#how-do-i-sign-in-to-the-simulation-vm)
1. Per controllare quali sono i contenitori attivi, eseguire: `docker ps`.
1. Per arrestare tutti i contenitori della simulazione, eseguire: `./stopsimulation`.
1. Per avviare tutti i contenitori della simulazione:
    * Esportare una variabile della shell con il nome **IOTHUB_CONNECTIONSTRING**. Usare il valore dell'impostazione **IotHubOwnerConnectionString** nel file `<name of your deployment>.config.user`. Ad esempio: 

        ```
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Eseguire `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Come è possibile aggiornare la simulazione nella VM?

Se sono state apportate modifiche alla simulazione, è possibile usare lo script di PowerShell `build.ps1` nel [repository](https://github.com/Azure/azure-iot-connected-factory) eseguendo il comando `updatedimulation`. Questo script compila tutti i componenti della simulazione, arresta la simulazione nella VM, li carica, li installa e li avvia.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Come è possibile trovare la stringa di connessione dell'hub IoT usato dalla soluzione?

Se la soluzione è stata distribuita con lo script `build.ps1` nel [repository](https://github.com/Azure/azure-iot-connected-factory), la stringa di connessione è il valore di **IotHubOwnerConnectionString** nel file `<name of your deployment>.config.user`.

Per trovare la stringa di connessione, è anche possibile usare il portale di Azure. Nella risorsa Hub IoT del gruppo di risorse della distribuzione individuare le impostazioni della stringa di connessione.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Quali dispositivi dell'hub IoT usa la simulazione di connected factory?

La simulazione registra automaticamente i dispositivi seguenti:

* proxy.beijing.corp.contoso
* proxy.capetown.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.munich0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Con lo strumento [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) o con [l'estensione IoT dell'interfaccia della riga di comando di Azure 2.0](https://github.com/Azure/azure-iot-cli-extension) è possibile controllare quali dispositivi sono registrati nell'hub IoT usato dalla soluzione. Per usare Device Explorer, è necessaria la stringa di connessione per l'hub IoT della distribuzione. Per usare l'estensione IoT dell'interfaccia della riga di comando di Azure 2.0, è necessario il nome dell'hub IoT.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Come è possibile ottenere i dati dei log dai componenti della simulazione?

Tutti i componenti della simulazione registrano informazioni nei file di log, disponibili nella cartella `home/docker/Logs` della VM. Per recuperare i log, è possibile usare lo script di PowerShell `Simulation/Factory/Get-SimulationLogs.ps1` nel [repository](https://github.com/Azure/azure-iot-connected-factory).

È necessario che questo script acceda alla VM. Potrebbe essere necessario specificare le credenziali per l'accesso. Vedere [Come è possibile accedere alla VM di simulazione?](#how-do-i-sign-in-to-the-simulation-vm) per trovare le credenziali.

Lo script aggiunge/rimuove un indirizzo IP pubblico nella VM, se non ne è già presente uno che viene rimosso. Lo script inserisce tutti i file di log in un archivio che scarica nella workstation di sviluppo.

In alternativa, accedere alla VM tramite SSH ed esaminare i file di log in fase di esecuzione.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Come è possibile controllare se la simulazione sta inviando dati al cloud?

Con lo strumento [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) o [iothub-explorer](https://github.com/azure/iothub-explorer), è possibile controllare i dati inviati all'hub IoT da determinati dispositivi. Per usare questi strumenti, è necessario conoscere la stringa di connessione per l'hub IoT della distribuzione. Vedere [Come è possibile trovare la stringa di connessione dell'hub IoT usato dalla soluzione?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Esaminare i dati inviati da uno dei dispositivi di pubblicazione:

* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Se non vengono visualizzati dati inviati all'hub IoT, si è verificato un problema con la simulazione. Come primo passaggio è consigliabile analizzare i file di log dei componenti della simulazione. Vedere [Come è possibile ottenere i dati dei log dai componenti della simulazione?](#how-can-i-get-log-data-from-the-simulation-components) Provare quindi ad arrestare e avviare la simulazione e, se non sono ancora presenti dati inviati, aggiornare la simulazione completamente. Vedere [Come è possibile aggiornare la simulazione nella VM?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Come è possibile abilitare una mappa interattiva nella soluzione di fabbrica connessa?

Per abilitare una mappa interattiva nella soluzione di fabbrica connessa, è necessario avere un piano API di Bing Maps per aziende esistente.

Durante la distribuzione da [www.azureiotsuite.com](http://www.azureiotsuite.com), il processo verifica che la sottoscrizione includa un piano API di Bing Maps per aziende abilitato e distribuisce automaticamente una mappa interattiva nella soluzione di fabbrica connessa. In caso contrario, è possibile comunque abilitare una mappa interattiva nella distribuzione seguendo questa procedura:

Quando si esegue la distribuzione usando lo script `build.ps1` disponibile nel repository GitHub della soluzione di fabbrica connessa e si dispone di un piano API di Bing Maps per aziende, impostare la variabile di ambiente `$env:MapApiQueryKey` nella finestra di compilazione sulla chiave di query del piano. La mappa interattiva viene quindi abilitata automaticamente.

Se non si dispone di un piano API di Bing Maps per aziende, distribuire la soluzione di fabbrica connessa da [www.azureiotsuite.com](http://www.azureiotsuite.com) o usando lo script `build.ps1`. Aggiungere quindi un piano API di Bing Maps per aziende alla sottoscrizione, come illustrato nella sezione [Come è possibile creare un account API di Bing Maps per aziende?](#how-do-i-create-a-bing-maps-api-for-enterprise-account). Cercare la chiave di query di questo account come illustrato nella sezione [Come è possibile ottenere la chiave di query dell'API di Bing Maps per aziende?](#how-to-obtain-your-bing-maps-api-for-enterprise-querykey) e salvarla. Passare al portale di Azure e accedere alla risorsa Servizio app nella distribuzione della soluzione di fabbrica connessa. Passare a **Impostazioni dell'applicazione**, in cui si trova una sezione **Impostazioni app**. Impostare **MapApiQueryKey** sulla chiave di query ottenuta. Salvare le impostazioni e quindi passare a **Panoramica** e riavviare il servizio app.

### <a name="how-do-i-create-a-bing-maps-api-for-enterprise-account"></a>Come è possibile creare un account API di Bing Maps per aziende?

È possibile ottenere una piano *API di Bing Maps per aziende - Transazioni interne Livello 1* gratuito. È tuttavia possibile aggiungere solo due piani di questo tipo in una sottoscrizione di Azure. Se non si dispone di un account API di Bing Maps per aziende, crearne uno nel portale di Azure facendo clic su **+ Crea una risorsa**. Cercare quindi **API di Bing Maps per aziende** e seguire le istruzioni per creare l'account.

![Chiave di Bing](media/iot-suite-faq-cf/bing.png)

### <a name="how-to-obtain-your-bing-maps-api-for-enterprise-querykey"></a>Come è possibile ottenere la chiave di query di API di Bing Maps per aziende?

Dopo aver creato il piano API di Bing Maps per aziende, aggiungere una risorsa API di Bing Maps per aziende al gruppo di risorse della soluzione di fabbrica connessa nel portale di Azure.

1. Nel portale di Azure passare al gruppo di risorse contenente il piano API di Bing Maps per aziende.

1. Fare clic su **All Settings** (Tutte le impostazioni) e quindi su **Key Management** (Gestione chiavi).

1. Sono disponibili due chiavi: **MasterKey** e **QueryKey**. Copiare il valore **QueryKey**.

1. Per consentire allo script `build.ps1` di prelevare la chiave, impostare la variabile di ambiente `$env:MapApiQueryKey` dell'ambiente PowerShell sul valore **QueryKey** del piano. Lo script di compilazione aggiunge quindi automaticamente il valore alle impostazioni del servizio app.

1. Eseguire una distribuzione locale o cloud usando lo script `build.ps1`.

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Come è possibile abilitare la mappa interattiva durante il debug in locale?

Per abilitare la mappa interattiva durante il debug in locale, impostare il valore di `MapApiQueryKey` nei file `local.user.config` e `<yourdeploymentname>.user.config` della radice della distribuzione sul valore di **QueryKey** copiato in precedenza.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Come è possibile impostare un'immagine diversa nella home page del dashboard?

Per cambiare l'immagine statica visualizzata nella home page del dashboard, sostituire l'immagine `WebApp\Content\img\world.jpg` e quindi ricompilare e ridistribuire l'app Web.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Come è possibile usare dispositivi non OPC UA con la soluzione di fabbrica connessa?

Per inviare dati di telemetria da dispositivi non OPC UA alla soluzione di fabbrica connessa:

1. [Configurare una nuova stazione nella topologia di fabbrica connessa](iot-suite-connected-factory-configure.md) nel file `ContosoTopologyDescription.json`.

1. Inserire i dati di telemetria in formato JSON compatibile con la soluzione di fabbrica connessa:

    ```json
    [
      {
        "ApplicationUri": "<the_value_of_OpcUri_of_your_station",
        "DisplayName": "<name_of_the_datapoint>",
        "NodeId": "value_of_NodeId_of_your_datapoint_in_the_station",
        "Value": {
          "Value": <datapoint_value>,
          "SourceTimestamp": "<timestamp>"
        }
      }
    ]
    ```

1. Il formato di `<timestamp>` è: `2017-12-08T19:24:51.886753Z`

1. Riavviare il servizio app della soluzione di fabbrica connessa.

### <a name="next-steps"></a>Passaggi successivi

È anche possibile esplorare alcune altre funzionalità delle soluzioni preconfigurate di IoT Suite:

* [Panoramica della soluzione preconfigurata di manutenzione predittiva](iot-suite-predictive-overview.md)
* [Panoramica della soluzione preconfigurata di connected factory](iot-suite-connected-factory-overview.md)
* [Sicurezza IoT sin dall'inizio](securing-iot-ground-up.md)