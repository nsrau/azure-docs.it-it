---
title: Integrazione di Elenco dei servizi con System Center Operations Manager | Microsoft Docs
description: "Elenco dei servizi è una soluzione di Operations Management Suite che individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux e mappa la comunicazione fra i servizi. Questo articolo illustra l&quot;uso di Elenco dei servizi per creare automaticamente diagrammi applicazioni distribuite in Operations Manager."
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
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 0b710c338be3a2c2fde6bba43173f7c5f480e357
ms.contentlocale: it-it
ms.lasthandoff: 05/03/2017


---

# <a name="service-map-integration-with-system-center-operations-manager"></a>Integrazione di Elenco dei servizi con System Center Operations Manager
  > [!NOTE]
  > Poiché è disponibile in anteprima privata, questa funzionalità non deve essere usata nei sistemi di produzione.
  > 
  
Elenco dei servizi di Operations Management Suite individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux ed esegue il mapping della comunicazione fra i servizi. Elenco dei servizi consente di visualizzare i server nel modo in cui si pensa a essi, ovvero come sistemi interconnessi che forniscono servizi critici. Elenco dei servizi mostra le connessioni fra i server, i processi e le porte di tutte le architetture connesse via TCP senza bisogno di alcuna configurazione a parte l'installazione di un agente. Per altre informazioni, vedere la [documentazione su Elenco dei servizi](operations-management-suite-service-map.md).

Con questa integrazione tra Elenco dei servizi e System Center Operations Manager è possibile creare automaticamente diagrammi applicazioni distribuite in Operations Manager basati sulle mappe delle dipendenze dinamiche in Elenco dei servizi.

## <a name="prerequisites"></a>Prerequisiti
* Gruppo di gestione di Operations Manager che gestisce un set di server.
* Area di lavoro di Operations Manager con la soluzione Elenco dei servizi abilitata.
* Set di server (almeno uno) che vengono gestiti da Operations Manager e inviano dati a Elenco dei servizi. Sono supportati server Windows e Linux.
* Un'entità servizio con accesso alla sottoscrizione di Azure associata all'area di lavoro di Operations Management Suite. Per altre informazioni, vedere [Creare un'entità servizio](#creating-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Installare il management pack di Elenco dei servizi
L'integrazione tra Operations Manager ed Elenco dei servizi viene abilitata importando il bundle di management pack Microsoft.SystemCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb). Il bundle contiene i management pack seguenti:
* Microsoft Service Map Application Views
* Microsoft System Center Service Map Internal
* Microsoft System Center Service Map Overrides
* Microsoft System Center Service Map

## <a name="configure-the-service-map-integration"></a>Configurare l'integrazione di Elenco dei servizi
Dopo avere installato il management pack di Elenco dei servizi, nel riquadro **Amministrazione** di **Operations Management Suite** sarà presente il nuovo nodo **Elenco dei servizi**. 

Per configurare l'integrazione di Elenco dei servizi, eseguire le operazioni seguenti:

1. Per aprire la configurazione guidata, fare clic su **Add workspace** (Aggiungi area di lavoro) nel riquadro **Service Map Overview** (Panoramica di Elenco dei servizi) .  

    ![Riquadro Service Map Overview (Panoramica di Elenco dei servizi)](media/oms-service-map/scom-configuration.png)

2. Nella finestra **Configurazione di connessione** immettere l'ID o il nome del tenant, l'ID applicazione (noto anche come nome utente o ClientID) e la password dell'entità servizio, quindi fare clic su **Avanti**. Per altre informazioni, vedere [Creare un'entità servizio](#creating-a-service-principal).

    ![Finestra di configurazione della connessione](media/oms-service-map/scom-config-spn.png)

3. Nella finestra **Subscription Selection** (Selezione della sottoscrizione), selezionare la sottoscrizione di Azure, il gruppo di risorse di Azure contenente l'area di lavoro di Operations Management Suite e infine l'area di lavoro stessa, quindi fare clic su **Avanti**.

    ![Area di lavoro di configurazione di Operations Manager](media/oms-service-map/scom-config-workspace.png)

4. Nella finestra **Selezione server** è possibile configurare il gruppo di server di Elenco dei servizi con i server che si desidera sincronizzare tra Operations Manager ed Elenco dei servizi. Fare clic su **Aggiungi/Rimuovi server**.   
    
    Perché l'integrazione crei un diagramma applicazioni distribuite per un server, quest'ultimo deve essere:

    * Gestito da Operations Manager.
    * Gestito da Elenco dei servizi.
    * Elencato nel gruppo di server di Elenco dei servizi.

    ![Gruppo di configurazione di Operations Manager](media/oms-service-map/scom-config-group.png)

5. Facoltativo: selezionare il pool di risorse server di gestione per comunicare con Operations Management Suite, quindi fare clic su **Aggiungi area di lavoro**.

    ![Pool di risorse di configurazione di Operations Manager](media/oms-service-map/scom-config-pool.png)

    Per configurare e registrare l'area di lavoro di Operations Management Suite potrebbero essere necessari alcuni minuti. Dopo averlo configurato, Operations Manager avvia la prima sincronizzazione di Elenco dei servizi da Operations Management Suite.

    ![Pool di risorse di configurazione di Operations Manager](media/oms-service-map/scom-config-success.png)

    >[!NOTE]
    >L'intervallo di sincronizzazione predefinito è impostato su 60 minuti. È possibile configurare gli override per modificare l'intervallo di sincronizzazione. È anche possibile aggiungere manualmente server al gruppo di server di Elenco dei servizi tramite il riquadro **Tecnologie**. A tale scopo, selezionare **Gruppi**, quindi cercare **Service Map Servers Group** (Gruppo di server di Elenco dei servizi). Le mappe di tali server verranno sincronizzate con la sincronizzazione successiva, in base all'intervallo di sincronizzazione configurato.

## <a name="monitor-service-map"></a>Monitorare le metriche del servizio
Dopo aver connesso l'area di lavoro di Operations Management Suite, nel riquadro **Monitoraggio** della console di Operations Manager comparirà una nuova cartella denominata Elenco dei servizi.

![Riquadro Monitoraggio di Operations Manager](media/oms-service-map/scom-monitoring.png)

La cartella Elenco dei servizi ha tre nodi:
* **Avvisi attivi**: elenca tutti gli avvisi attivi per le comunicazioni tra Operations Manager e la soluzione Elenco dei servizi in Operations Management Suite.

    >[!NOTE]
    >Tali avvisi non corrispondono agli avvisi di Operations Management Suite rilevati in Operations Manager.

* **Server**: contiene l'elenco dei server monitorati configurati per la sincronizzazione da Elenco dei servizi.

    ![Riquadro Monitoraggio server di Operations Manager](media/oms-service-map/scom-monitoring-servers.png)

* **Server Dependency Views** (Visualizzazioni dipendenze server): elenca tutti i server sincronizzati da Elenco dei servizi. È possibile fare clic su un server per visualizzarne il diagramma applicazioni distribuite.

    ![Diagramma applicazioni distribuite di Operations Manager](media/oms-service-map/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Modificare o eliminare l'area di lavoro
È possibile modificare o eliminare l'area di lavoro configurata tramite il riquadro **Service Map Overview** (Panoramica di Elenco dei servizi): riquadro **Amministrazione** --> **Operations Management Suite** > **Elenco dei servizi**. Attualmente è possibile configurare una sola area di lavoro di Operations Management Suite.

![Riquadro Modifica area di lavoro di Operations Manager](media/oms-service-map/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Configurare regole e override
Viene creata una regola _Microsoft.SystemCenter.ServiceMapImport.Rule_ per recuperare periodicamente le informazioni da Elenco dei servizi. Per modificare gli intervalli di sincronizzazione è possibile configurare gli override della regola (riquadro **Tecnologie** > **Regole** > **Microsoft.SystemCenter.ServiceMapImport.Rule**).

![Finestra delle proprietà di override di Operations Manager](media/oms-service-map/scom-overrides.png)

* **Enabled**: abilita/disabilita gli aggiornamenti automatici. 
* **IntervalMinutes**: reimposta l'intervallo tra gli aggiornamenti. L'intervallo predefinito è un'ora. Se si desidera sincronizzare le mappe dei server più di frequente, è possibile modificare il valore.
* **TimeoutSeconds**: reimposta l'intervallo di tempo prima che la richiesta raggiunga il timeout. 
* **TimeWindowMinutes**: reimposta l'intervallo di tempo per eseguire query sui dati. Il valore predefinito è una finestra temporale di 60 minuti. Il valore massimo consentito da Elenco dei servizi è 60 minuti.

## <a name="known-issues-and-limitations"></a>Problemi noti e limitazioni

La progettazione attuale presenta i problemi e le limitazioni seguenti:
* Anche se è possibile aggiungere manualmente server al gruppo di server di Elenco dei servizi tramite il riquadro **Tecnologie**, le mappe di tali server verranno sincronizzate da Elenco dei servizi solo durante il ciclo di sincronizzazione successivo. L'impostazione predefinita è 60 minuti, ma è possibile modificare l'intervallo di tempo. 
* È possibile connettersi a una sola area di lavoro Operations Management Suite.

## <a name="create-a-service-principal"></a>Creare un’entità servizio
Per la documentazione ufficiale di Azure sulla creazione di un'entità servizio, vedere:
* [Create a service principal by using PowerShell](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal) (Creare un'entità servizio usando PowerShell)
* [Create a service principal by using Azure CLI](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli) (Creare un'entità servizio usando Azure CLI)
* [Create a service principal by using the Azure portal](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal) (Creare un'entità servizio usando il portale di Azure)

### <a name="feedback"></a>Commenti e suggerimenti
Per inviare commenti sull'elenco dei servizi e sulla relativa documentazione, Visitare la [pagina per i suggerimenti degli utenti](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), in cui è possibile suggerire funzionalità o votare i suggerimenti esistenti.

