---
title: Integrazione di Elenco dei servizi con System Center Operations Manager | Microsoft Docs
description: "L&quot;elenco dei servizi è una soluzione di Operations Management Suite (OMS) che individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux e mappa la comunicazione fra i servizi.  Questo articolo fornisce informazioni dettagliate sull&quot;uso di Elenco dei servizi per creare automaticamente diagrammi applicazioni distribuite in SCOM."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: bwren;dairwin
translationtype: Human Translation
ms.sourcegitcommit: 834e805e05696175bfc5a1d71f2223a8f7cf45e8
ms.openlocfilehash: 2c4b07a7bf541c0ad1e979df9cb2937bfa66c216
ms.lasthandoff: 02/24/2017


---

# <a name="service-map-integration-with-system-center-operations-manager-integration"></a>Integrazione di Elenco dei servizi con System Center Operations Manager
Elenco dei servizi di Operations Management Suite (OMS) individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux ed esegue il mapping della comunicazione fra i servizi. Consente di visualizzare i server nel modo in cui si pensa a essi, ovvero come sistemi interconnessi che forniscono servizi critici. L'elenco dei servizi mostra le connessioni fra i server, i processi e le porte di tutte le architetture connesse via TCP senza il bisogno di alcuna configurazione a parte l'installazione di un agente.  Per altre informazioni, vedere la [documentazione su Elenco dei servizi](operations-management-suite-service-map.md).

Con questa anteprima dell'integrazione tra Elenco dei servizi e System Center Operations Manager (SCOM), è possibile creare automaticamente diagrammi applicazioni distribuite in SCOM basati sulle mappe delle dipendenze dinamiche in Elenco dei servizi.

# <a name="prerequisites"></a>Prerequisiti
1.    Un gruppo di gestione SCOM che gestisce un set di server.
2.    Un'area di lavoro di OMS con la soluzione Elenco dei servizi abilitata.
3.    Un set di server (almeno uno) che vengono gestiti da SCOM e inviano dati all'elenco dei servizi.  Sono supportati server Windows e Linux.
4.    Un'entità servizio con accesso alla sottoscrizione di Azure associata all'area di lavoro di OMS.  Per altre informazioni sulle entità servizio, vedere l'appendice.

# <a name="installing-service-map-management-pack"></a>Installazione del Service Pack di Elenco dei servizi
L'integrazione tra SCOM ed Elenco dei servizi viene abilitata importando il bundle di Management Pack Microsoft.SystemCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb).  Il bundle contiene i Management Pack seguenti:
* Microsoft ServiceMap Application Views
* Microsoft System Center ServiceMap Internal
* Microsoft System Center ServiceMap Overrides
* Microsoft System Center ServiceMap

# <a name="configuring-the-service-map-integration"></a>Configurazione dell'integrazione di Elenco dei servizi
1. Dopo l'installazione del Management Pack ServiceMap, nel riquadro Amministrazione di Operations Management Suite sarà presente il nuovo nodo Elenco dei servizi.
2. Fare clic su "Add workspace" (Aggiungi area di lavoro) nel riquadro Service Map Overview (Panoramica di Elenco dei servizi) per aprire la configurazione guidata.

    ![Configurazione guidata SCOM](media/oms-service-map/scom-configuration.png)

3. Il primo passaggio della procedura guidata è Connection Configuration (Configurazione di connessione) in cui si configura un nome di entità servizio. Immettere l'ID tenant, l'ID applicazione (oppure il nome utente o l'ID client) e la password dell'entità servizio.  Per creare un'entità servizio, vedere la sezione dell'appendice.

    ![SPN di configurazione SCOM](media/oms-service-map/scom-config-spn.png)

4. Nel passaggio successivo si selezionano la sottoscrizione di Azure, il gruppo di risorse di Azure (quello contenente l'area di lavoro di OMS) e l'area di lavoro di OMS.

    ![Area di lavoro di configurazione SCOM](media/oms-service-map/scom-config-workspace.png)

5. Nel passaggio successivo si configura il gruppo di server di Elenco dei servizi con i server che si vuole sincronizzare tra SCOM ed Elenco dei servizi.  Fare clic sul pulsante Add/Remove Servers (Aggiungi/Rimuovi server) . Tenere presente che per consentire all'integrazione di creare un diagramma applicazioni distribuite per un server, il server deve: 1) essere gestito da SCOM, 2) essere gestito da Elenco dei servizi ed 3) essere elencato nel gruppo di server di Elenco dei servizi.

    ![Gruppo di configurazione SCOM](media/oms-service-map/scom-config-group.png)

6. Facoltativo: selezionare il pool di risorse server di gestione per comunicare con OMS e fare clic su "Add Workspace" (Aggiungi area di lavoro).

    ![Pool di risorse di configurazione SCOM](media/oms-service-map/scom-config-pool.png)

7. Si noti che la configurazione e la registrazione dell'area di lavoro OMS richiederanno un minuto. Dopo la configurazione, SCOM avvierà la prima sincronizzazione di Elenco dei servizi da OMS.

    ![Pool di risorse di configurazione SCOM](media/oms-service-map/scom-config-success.png)

**Nota:** l'intervallo di sincronizzazione predefinito è impostato su 60 minuti. Gli utenti possono configurare gli override per modificare l'intervallo di sincronizzazione. Gli utenti possono anche aggiungere server al gruppo di server di Elenco dei servizi manualmente tramite il riquadro Creazione e modifica: riquadro Creazione e modifica --> Gruppi, quindi cercare "Service Map Servers Group" (Gruppo di server di Elenco dei servizi). Le mappe di tali server verranno sincronizzate con la sincronizzazione successiva (in base all'intervallo di sincronizzazione configurato).

# <a name="monitoring-service-map"></a>Monitoraggio di Elenco dei servizi
Dopo che l'area di lavoro OMS è stata connessa, nel riquadro Monitoraggio della console SCOM verrà visualizzata una nuova cartella Elenco dei servizi.
![Monitoraggio SCOM](media/oms-service-map/scom-monitoring.png)

La cartella Elenco dei servizi ha tre nodi:
## <a name="all-alerts"></a>Tutti gli avvisi:
Visualizza tutti gli avvisi sulla comunicazione tra SCOM e la soluzione Elenco dei servizi in OMS.

**Nota:** non si tratta di avvisi OMS presentati in SCOM.
## <a name="servers"></a>Server:
Contiene l'elenco dei server monitorati configurati per la sincronizzazione da Elenco dei servizi.

![Monitoraggio dei server in SCOM](media/oms-service-map/scom-monitoring-servers.png)

## <a name="server-dependency-views"></a>Server Dependency Views (Visualizzazioni dipendenze server):
Questa visualizzazione includerà l'elenco di tutti i server sincronizzati da Elenco dei servizi. Gli utenti possono fare clic su un server per visualizzarne il diagramma applicazioni distribuite.

![Diagramma applicazioni distribuite SCOM](media/oms-service-map/scom-dad.png)

# <a name="editdelete-workspace"></a>Modificare/Eliminare l'area di lavoro
Gli utenti possono modificare o eliminare l'area di lavoro configurata tramite il riquadro Service Map Overview (Panoramica di Elenco dei servizi): riquadro Amministrazione --> Operations Management Suite --> Elenco dei servizi.  Tenere presente che per il momento è possibile configurare una sola area di lavoro OMS.

![Modificare l'area di lavoro in SCOM](media/oms-service-map/scom-edit-workspace.png)

# <a name="configuring-rules-and-overrides"></a>Configurazione di regole e override
Viene creata una regola **_Microsoft.SystemCenter.ServiceMap.Import.Rule**_ per recuperare periodicamente le informazioni da Elenco dei servizi.  Gli utenti possono configurare override di questa regola per modificare le ore di sincronizzazione.
Riquadro Creazione e modifica --> Regole --> Microsoft.SystemCenter.ServiceMapImport.Rule

![Override SCOM](media/oms-service-map/scom-overrides.png)
* **Enabled**: abilita/disabilita gli aggiornamenti automatici 
* **IntervalSeconds**: intervallo tra gli aggiornamenti.  Il valore predefinito è 1 ora. Gli utenti possono modificare il valore se vogliono sincronizzare le mappe server con maggiore frequenza.
* **TimeoutSeconds**: indica quanto tempo prima si verifica il timeout della richiesta 
* **TimeWindowMinutes**: ampiezza della query dei dati.  Il valore predefinito è una finestra temporale di 60 minuti. Il valore massimo consentito da Elenco dei servizi è di 1 ora.

# <a name="known-issueslimitations"></a>Problemi e limitazioni noti
Nella progettazione corrente:
1. Anche se gli utenti possono aggiungere server a "Service Map Servers Group" (Gruppo di server di Elenco dei servizi) manualmente tramite il riquadro di creazione e modifica, le mappe di tali server verranno sincronizzate da Elenco dei servizi solo durante il ciclo di sincronizzazione successivo. L'impostazione predefinita è di&60; minuti. Gli utenti possono eseguire l'override dell'ora di sincronizzazione. 
2. Gli utenti possono connettersi a una singola area di lavoro OMS.

# <a name="appendix"></a>Appendice
## <a name="creating-a-service-principal"></a>Creazione di un'entità servizio
I collegamenti seguenti aprono le pagine della documentazione ufficiale di Azure su tre diversi modi di creare un'entità servizio.
* [Creare un'entità servizio con PowerShell](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Creare un'entità servizio con l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Creare un'entità servizio con il portale di Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal)

## <a name="feedback"></a>Commenti e suggerimenti
Per inviare commenti sull'elenco dei servizi e sulla relativa documentazione,  visitare la [pagina per i suggerimenti degli utenti](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), in cui è possibile suggerire funzionalità o votare i suggerimenti esistenti.

