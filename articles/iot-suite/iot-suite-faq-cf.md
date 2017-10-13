---
title: Domande frequenti sulla soluzione di connected factory di Azure IoT Suite | Microsoft Docs
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
ms.date: 09/15/2017
ms.author: corywink
ms.openlocfilehash: babd39d5e2f290d9e3f7fc3a232937c6ccfc85c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
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

Microsoft ha scelto OPC UA perché è uno standard aperto, non proprietario, indipendente dalla piattaforma, riconosciuto nel settore e collaudato. È un requisito per le soluzioni basate sull'architettura di riferimento Industrie 4.0 (RAMI4.0) che assicurano l'interoperabilità tra un'ampia serie di processi e attrezzature di produzione. Microsoft è consapevole della richiesta dei clienti di creare soluzioni Industrie 4.0. Il supporto per OPC UA consente di agevolare i clienti nel raggiungimento degli obiettivi e offre immediatamente valore aggiunto.

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
    * Esportare una variabile della shell con il nome **IOTHUB_CONNECTIONSTRING**. Usare il valore dell'impostazione **IotHubOwnerConnectionString** nel file `<name of your deployment>.config.user`. ad esempio:

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

Con lo strumento [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) o [iothub-explorer](https://github.com/azure/iothub-explorer), è possibile controllare quali dispositivi sono registrati con l'hub IoT usato dalla soluzione. Per usare questi strumenti, è necessaria la stringa di connessione per l'hub IoT della distribuzione.

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

### <a name="next-steps"></a>Passaggi successivi

È anche possibile esplorare alcune altre funzionalità delle soluzioni preconfigurate di IoT Suite:

* [Panoramica della soluzione preconfigurata di manutenzione predittiva](iot-suite-predictive-overview.md)
* [Panoramica della soluzione preconfigurata di connected factory](iot-suite-connected-factory-overview.md)
* [Sicurezza IoT sin dall'inizio](securing-iot-ground-up.md)