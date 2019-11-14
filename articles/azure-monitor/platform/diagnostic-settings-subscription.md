---
title: Raccogliere log attività di Azure con le impostazioni di diagnostica (anteprima)-monitoraggio di Azure | Microsoft Docs
description: Usare le impostazioni di diagnostica per inviare i log attività di Azure ai log di monitoraggio di Azure, archiviazione di Azure o hub eventi di Azure.
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 10/31/2019
ms.openlocfilehash: 6104a8b01cc9fca5ff8de973e7fc2af77cda8515
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048197"
---
# <a name="collect-azure-activity-log-with-diagnostic-settings-preview"></a>Raccolta del log attività di Azure con le impostazioni di diagnostica (anteprima)
Il [log attività di Azure](activity-logs-overview.md) è un [log di piattaforma](platform-logs-overview.md) che fornisce informazioni approfondite sugli eventi a livello di sottoscrizione che si sono verificati in Azure. Fino a questo momento, è stato creato un profilo di log per inviare le voci del log attività a [un hub eventi o](activity-log-export.md) a un account di archiviazione e si è usato un connettore per raccoglierle in un' [area di lavoro log Analytics](activity-log-collect.md).

È ora possibile configurare la raccolta del log attività di Azure usando le stesse [impostazioni di diagnostica](diagnostic-settings.md) usate per raccogliere i [log delle risorse](resource-logs-overview.md). L'uso delle impostazioni di diagnostica presenta i vantaggi seguenti rispetto ai metodi correnti:

- Metodo coerente per la raccolta di tutti i log di piattaforma.
- Raccogliere il log attività tra più sottoscrizioni e tenant.
- Filtra raccolta per raccogliere solo i log per determinate categorie.
- Raccoglie tutte le categorie del log attività. Alcune categorie non vengono raccolte utilizzando il metodo precedente.
- Latenza più veloce per l'inserimento dei log. Il metodo precedente presenta una latenza di circa 15 minuti mentre le impostazioni di diagnostica aggiungono solo circa 1 minuto.

## <a name="considerations"></a>Considerazioni
Prima di abilitare questa funzionalità, prendere in considerazione i dettagli seguenti della raccolta di log attività usando le impostazioni di diagnostica.

- È necessario disabilitare la raccolta esistente dell'attività prima di abilitarla usando le impostazioni di diagnostica. L'abilitazione di entrambe le funzionalità può generare dati duplicati.
- L'impostazione di conservazione per la raccolta del log attività in archiviazione di Azure è stata rimossa, a indicare che i dati verranno archiviati per un periodo illimitato fino a quando non vengono rimossi.
- È possibile inviare il log attività da più sottoscrizioni a una singola area di lavoro Log Analytics. È possibile inviare il log attività da una singola sottoscrizione a un massimo di cinque aree di lavoro Log Analytics.

## <a name="configure-diagnostic-settings"></a>Configurare le impostazioni di diagnostica
Usare la procedura seguente per creare un'impostazione di diagnostica nel portale di Azure per raccogliere il log attività di Azure. Attualmente non è possibile creare un'impostazione di diagnostica a livello di sottoscrizione con altri metodi.

1. Disabilitare la raccolta di aree di lavoro Log Analytics esistente per il log attività:
   1. Aprire il menu **log Analytics aree di lavoro** nel portale di Azure e selezionare l'area di lavoro per raccogliere il log attività.
   2. Nella sezione **origini dati dell'area di lavoro** del menu dell'area di lavoro selezionare **log attività di Azure**.
   3. Fare clic sulla sottoscrizione che si desidera disconnettere.
   4. Fare clic su **Disconnetti** e quindi su **Sì** quando viene richiesto di confermare la scelta.
2. Dal menu **monitoraggio di Azure** nella portale di Azure selezionare **log attività**.
3. Fare clic su **Impostazioni di diagnostica**.
   
   ![Impostazioni di diagnostica](media/diagnostic-settings-subscription/diagnostic-settings.png)
   
4. Fare clic sul banner viola per l'esperienza legacy e disabilitare qualsiasi raccolta corrente nell'archivio o hub eventi. 

    ![Esperienza legacy](media/diagnostic-settings-subscription/legacy-experience.png)

5. Per creare un'impostazione di diagnostica, seguire le procedure riportate in [creare impostazioni di diagnostica in portale di Azure](diagnostic-settings.md#create-diagnostic-settings-in-azure-portal) . Per una spiegazione delle categorie che è possibile usare per filtrare gli eventi dal log attività, vedere [le categorie nel log attività](activity-logs-overview.md#categories-in-the-activity-log) . 


## <a name="differences-in-data"></a>Differenze nei dati
Le impostazioni di diagnostica raccolgono gli stessi dati dei metodi precedenti usati per raccogliere il log attività con le differenze correnti seguenti:

Sono state rimosse le proprietà seguenti:

- ActivityStatus
- ActivitySubstatus
- OperationName
- ResourceProvider 

Sono state aggiunte le proprietà seguenti:

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-monitoring-solution"></a>Soluzione monitoraggio log attività
La soluzione di monitoraggio di Azure Log Analytics include più query e visualizzazioni di log per analizzare i record del log attività nell'area di lavoro Log Analytics. Questa soluzione USA i dati di log raccolti in un'area di lavoro di Log Analytics e continuerà a funzionare senza modifiche se si raccoglie il log attività usando le impostazioni di diagnostica. Per informazioni dettagliate su questa soluzione, vedere [analisi dei log attività soluzione di monitoraggio](activity-log-collect.md#activity-logs-analytics-monitoring-solution) .

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sul log attività](../../azure-resource-manager/resource-group-audit.md)
* [Altre informazioni sulle impostazioni di diagnostica](diagnostic-settings.md)
