---
title: Raccogliere log attività di Azure con le impostazioni di diagnostica (anteprima)-monitoraggio di Azure | Microsoft Docs
description: Usare le impostazioni di diagnostica per inviare i log attività di Azure ai log di monitoraggio di Azure, archiviazione di Azure o hub eventi di Azure.
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 12/20/2019
ms.openlocfilehash: e3a8048d8bcb8bb737c511a8b8bb6b57f9e81403
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750984"
---
# <a name="collect-azure-activity-log-with-legacy-settings"></a>Raccolta del log attività di Azure con le impostazioni legacy
Il [log attività di Azure](platform-logs-overview.md) è un [log di piattaforma](platform-logs-overview.md) che fornisce informazioni approfondite sugli eventi a livello di sottoscrizione che si sono verificati in Azure. Fino a poco tempo fa, è stato creato un profilo di log per inviare le voci del log attività a [un hub eventi o](activity-log-export.md) a un account di archiviazione e viene usato un connettore per raccoglierle in un' [area di lavoro log Analytics](activity-log-collect.md). Questo articolo descrive la differenza tra i metodi, come usare le impostazioni legacy esistenti e come cancellare le impostazioni legacy in preparazione per le impostazioni di diagnostica.


## <a name="differences-between-methods"></a>Differenze tra i metodi

### <a name="advantages"></a>Vantaggi
L'uso delle impostazioni di diagnostica presenta i vantaggi seguenti rispetto ai metodi correnti:

- Metodo coerente per la raccolta di tutti i log di piattaforma.
- Raccogliere il log attività tra più sottoscrizioni e tenant.
- Filtra raccolta per raccogliere solo i log per determinate categorie.
- Raccoglie tutte le categorie del log attività. Alcune categorie non vengono raccolte utilizzando il metodo legacy.
- Latenza più veloce per l'inserimento dei log. Il metodo precedente presenta una latenza di circa 15 minuti mentre le impostazioni di diagnostica aggiungono solo circa 1 minuto.
  
### <a name="considerations"></a>Considerazioni
Prima di abilitare questa funzionalità, prendere in considerazione i dettagli seguenti della raccolta di log attività usando le impostazioni di diagnostica.

- L'impostazione di conservazione per la raccolta del log attività in archiviazione di Azure è stata rimossa, a indicare che i dati verranno archiviati per un periodo illimitato fino a quando non vengono rimossi.
- Attualmente, è possibile creare solo un'impostazione di diagnostica a livello di sottoscrizione usando il portale di Azure. Per usare altri metodi, ad esempio PowerShell o l'interfaccia della riga di comando, è possibile creare un modello di Gestione risorse.


### <a name="differences-in-data"></a>Differenze nei dati
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

## <a name="work-with-legacy-settings"></a>Usare le impostazioni legacy
Le impostazioni legacy per la raccolta del log attività continueranno a funzionare se non si sceglie di sostituire con un'impostazione di diagnostica. Usare il metodo seguente per gestire il profilo di log per una sottoscrizione.

1. Dal menu **monitoraggio di Azure** nella portale di Azure selezionare **log attività**.
3. Fare clic su **Impostazioni di diagnostica**.
   
   ![Impostazioni di diagnostica](media/diagnostic-settings-subscription/diagnostic-settings.png)
   
4. Fare clic sul banner viola per l'esperienza legacy. 

    ![Esperienza legacy](media/diagnostic-settings-subscription/legacy-experience.png)


Per informazioni dettagliate sull'uso dei metodi di raccolta legacy, vedere gli articoli seguenti.

- [Raccogliere e analizzare i log attività di Azure nell'area di lavoro Log Analytics in monitoraggio di Azure](activity-log-collect.md)
- [Raccogliere i log attività di Azure in monitoraggio di Azure tra Azure Active Directory tenant](activity-log-collect-tenants.md)
- [Esportare il log attività di Azure nell'archiviazione o in hub eventi di Azure](activity-log-export.md)

## <a name="disable-existing-settings"></a>Disabilitare le impostazioni esistenti
È necessario disabilitare la raccolta esistente dell'attività prima di abilitarla usando le impostazioni di diagnostica. L'abilitazione di entrambe le funzionalità può generare dati duplicati.

### <a name="disable-collection-into-log-analytics-workspace"></a>Disabilitare la raccolta nell'area di lavoro Log Analytics

1. Aprire il menu **log Analytics aree di lavoro** nel portale di Azure e selezionare l'area di lavoro per raccogliere il log attività.
2. Nella sezione **origini dati dell'area di lavoro** del menu dell'area di lavoro selezionare **log attività di Azure**.
3. Fare clic sulla sottoscrizione che si desidera disconnettere.
4. Fare clic su **Disconnetti** e quindi su **Sì** quando viene richiesto di confermare la scelta.

### <a name="disable-log-profile"></a>Disabilitare il profilo di log

1. Usare la procedura descritta in [lavorare con le impostazioni legacy](#work-with-legacy-settings) per aprire le impostazioni legacy.
2. Disabilitare qualsiasi raccolta corrente nell'archivio o hub eventi. 



## <a name="activity-log-monitoring-solution"></a>Soluzione monitoraggio log attività
La soluzione di monitoraggio di Azure Log Analytics include più query e visualizzazioni di log per analizzare i record del log attività nell'area di lavoro Log Analytics. Questa soluzione USA i dati di log raccolti in un'area di lavoro di Log Analytics e continuerà a funzionare senza modifiche se si raccoglie il log attività usando le impostazioni di diagnostica. Per informazioni dettagliate su questa soluzione, vedere [analisi dei log attività soluzione di monitoraggio](activity-log-collect.md#activity-logs-analytics-monitoring-solution) .

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sul log attività](../../azure-resource-manager/resource-group-audit.md)
* [Altre informazioni sulle impostazioni di diagnostica](diagnostic-settings.md)
