---
title: Monitoraggio di Azure per le macchine virtuali gli avvisi sull'integrità Guest (anteprima)
description: Vengono descritti gli avvisi creati da Monitoraggio di Azure per le macchine virtuali integrità Guest, incluso il modo in cui abilitarli e configurare le notifiche.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: 9bbb0be321e78317c52586454de3d49cb2f878c1
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686888"
---
# <a name="azure-monitor-for-vms-guest-health-alerts-preview"></a>Monitoraggio di Azure per le macchine virtuali gli avvisi sull'integrità Guest (anteprima)
Monitoraggio di Azure per le macchine virtuali integrità Guest consente di visualizzare l'integrità di una macchina virtuale in base a quanto definito da un set di misurazioni delle prestazioni campionate a intervalli regolari. È possibile creare un avviso quando una macchina virtuale o un monitoraggio viene modificato in uno stato non integro. È possibile visualizzare e gestire questi avvisi con [quelli creati dalle regole di avviso in monitoraggio di Azure](../platform/alerts-overview.md) e scegliere di ricevere notifiche in modo proattivo quando viene creato un nuovo avviso.

## <a name="configure-alerts"></a>Configurare gli avvisi
Non è possibile creare una regola di avviso esplicita per Monitoraggio di Azure per le macchine virtuali integrità Guest mentre questa funzionalità è in anteprima. Per impostazione predefinita, gli avvisi verranno creati per ogni macchina virtuale, ma non per ogni monitoraggio.  Ciò significa che se un monitoraggio viene modificato in uno stato che non influisce sullo stato corrente della macchina virtuale, non viene creato alcun avviso perché lo stato della macchina virtuale non è stato modificato. 

È possibile disabilitare gli avvisi per una determinata macchina virtuale o per un particolare monitoraggio in una macchina virtuale dall'impostazione **dello stato degli avvisi** nella configurazione della macchina virtuale nella portale di Azure. Per informazioni dettagliate sulla configurazione dei monitoraggi nel portale di Azure, vedere [configurare il monitoraggio in monitoraggio di Azure per le macchine virtuali Health Guest (anteprima)](vminsights-health-configure.md) . Per informazioni dettagliate sulla configurazione dei monitoraggi in un set di macchine virtuali, vedere [configurare il monitoraggio in monitoraggio di Azure per le macchine virtuali integrità Guest mediante regole di raccolta dati (anteprima)](vminsights-health-configure-dcr.md) .

## <a name="alert-severity"></a>Gravità dell'avviso
Il livello di gravità dell'avviso creato dall'integrità Guest viene mappato direttamente alla gravità della macchina virtuale o al monitoraggio che ha attivato l'avviso.

| Stato monitoraggio | Gravità dell'avviso |
|:---|:---|
| Critico | Sev1 |
| Avviso  | Sev2 |
| Healthy  | Sev4 |

## <a name="alert-lifecycle"></a>Ciclo di vita degli avvisi
Viene creato un [avviso di Azure](../platform/alerts-overview.md) per ogni macchina virtuale in qualsiasi momento in cui viene modificato in uno stato di **avviso** o **criticità** . Visualizzare l'avviso dagli **avvisi** dal menu **monitoraggio di Azure** o dal menu della macchina virtuale nel portale di Azure.

Se un avviso si trova già nello stato **attivato** quando viene modificato lo stato della macchina virtuale, non verrà creato un secondo avviso, ma la gravità dello stesso avviso verrà modificata in base allo stato della macchina virtuale. Se, ad esempio, la macchina virtuale diventa uno stato **critico** quando **un avviso** era già nello stato **attivato** , la gravità dell'avviso verrà modificata in **Sev1**. Se la macchina virtuale passa a uno stato di **avviso** quando un avviso **Sev1** è già stato **attivato** , il livello di gravità dell'avviso verrà modificato in **Sev2**. Se la macchina virtuale torna a uno stato **integro** , l'avviso verrà risolto con la gravità modificata in **Sev4**.

## <a name="viewing-alerts"></a>Visualizzazione degli avvisi
Consente di visualizzare gli avvisi creati da Monitoraggio di Azure per le macchine virtuali integrità Guest con altri [avvisi nel portale di Azure](../platform/alerts-overview.md#alerts-experience). È possibile selezionare **avvisi** dal menu **monitoraggio di Azure** per visualizzare gli avvisi per tutte le risorse monitorate oppure selezionare **avvisi** dal menu di una macchina virtuale per visualizzare gli avvisi solo per tale macchina virtuale.

## <a name="alert-properties"></a>Proprietà avviso

### <a name="properties-in-the-azure-portal"></a>Proprietà nell'portale di Azure
Nella tabella seguente vengono descritte le proprietà dell'avviso quando lo si visualizza nella portale di Azure.

| Proprietà | Descrizione |
|:---|:---|
| Stato del monitoraggio prima della creazione dell'avviso | Stato del monitoraggio o della macchina virtuale prima che questo avviso venisse generato la prima volta. |
| Monitorare lo stato quando è stato creato l'avviso | Stato del monitoraggio o della macchina virtuale quando l'avviso è stato generato per la prima volta. Si tratta dello stato che ha provocato l'attivazione dell'avviso. |
| Per ulteriori informazioni sulla transizione di stato quando è stato creato l'avviso | Collegamento alla pagina di integrità della macchina virtuale in cui è possibile visualizzare la transizione di stato esatta. Questa transizione di stato rappresenta l'istanza quando il monitoraggio passa prima dallo stato **integro** a uno stato non integro. |

Ad esempio, un monitoraggio passa da **integro** a **critico** al momento T0 e viene generato un nuovo avviso con **Sev1**. Passa quindi da **critico** ad **avviso** alla data T1 e la gravità dell'avviso viene aggiornata a **Sev2**. Diventa **integro** all'ora T2 e l'avviso viene risolto.

Le proprietà degli avvisi avranno questi valori durante l'intera sequenza.

- Stato del monitoraggio prima della creazione dell'avviso: integro
- Monitorare lo stato quando è stato creato l'avviso: critico
- Per altre informazioni sulla transizione di stato quando è stato creato l'avviso: il collegamento di navigazione alla transizione di stato si è verificato al momento t0.


### <a name="properties-in-notifications"></a>Proprietà nelle notifiche
Nella tabella seguente sono descritte le proprietà dell'avviso incluso nelle notifiche.

| Proprietà | Descrizione |
|:---|:---|
| Stato monitoraggio precedente | Stato del monitoraggio o della macchina virtuale prima della modifica dello stato. Se l'aggiornamento della gravità dell'avviso attiva questa notifica, questa proprietà rappresenta lo stato appena prima dell'aggiornamento della gravità. |
| Stato monitoraggio corrente | Stato del monitoraggio o della macchina virtuale quando è stato modificato lo stato. Se l'aggiornamento della gravità dell'avviso attiva questa notifica, questa proprietà rappresenta il nuovo stato. |
| Per ulteriori informazioni su questa transizione di stato | Collegamento alla pagina di integrità della macchina virtuale in cui è possibile visualizzare la transizione di stato esatta. Questa transizione di stato rappresenta l'istanza quando il monitoraggio ha modificato lo stato che ha attivato la notifica. |

Utilizzando l'esempio precedente, le notifiche avrebbero le proprietà seguenti per ogni volta.

Notifica ricevuta al momento t0
- Stato di monitoraggio precedente: integro
- Stato monitoraggio corrente: critico
- Per altre informazioni su questa transizione di stato: il collegamento di navigazione alla transizione di stato si è verificato al momento t0.

Notifica ricevuta alla data T1
- Stato monitoraggio precedente: critico
- Stato monitoraggio corrente: avviso
- Per ulteriori informazioni su questa transizione di stato: il collegamento di navigazione alla transizione di stato si è verificato all'ora T1.

Notifica ricevuta all'ora T2
- Stato monitoraggio precedente: avviso
- Stato monitoraggio corrente: integro
- Per ulteriori informazioni su questa transizione di stato: il collegamento di navigazione alla transizione di stato si è verificato all'ora T2.

## <a name="configure-notifications"></a>Configurare le notifiche
Per ricevere notifiche proattive di un avviso attivato da integrità Guest, creare un gruppo di [azioni](../platform/action-groups.md) per definire le diverse azioni da eseguire, ad esempio l'invio di un messaggio SMS o l'avvio di un'app per la logica. Creare quindi una [regola di azione](../platform/alerts-action-rules.md) che specifichi l'ambito dei monitoraggi e delle macchine virtuali e usi tale gruppo di azione.

Scegliere **avvisi** dal menu **monitoraggio** della portale di Azure.  Selezionare **Gestisci azioni** e quindi **regole di azione (anteprima)**. 

![Nuova regola azione](media/vminsights-health-alerts/action-rule-new.png)

Fare clic su **nuova regola azione** per creare una nuova regola. Fare clic su **Seleziona** accanto a ambito e selezionare una sottoscrizione, un gruppo di risorse o una o più macchine virtuali specifiche. La notifica verrà attivata solo per le macchine virtuali che rientrano nell'ambito.

![Ambito della regola azione](media/vminsights-health-alerts/action-rule-scope.png)

Fare clic su **Aggiungi** accanto a **filtro**. Creare un filtro in cui il **servizio di monitoraggio sia uguale a VM Insights-Health**. Aggiungere altri filtri per specificare gli avvisi specifici che devono attivare la notifica. Ad esempio, è possibile usare la **gravità** per trovare la corrispondenza con gli avvisi di tutti i monitoraggi che corrispondono a una particolare gravità.

![Filtro regole azione](media/vminsights-health-alerts/action-rule-filter.png)

In **Definisci in questo ambito** selezionare **gruppo di azioni** e quindi selezionare il gruppo di azioni da associare al monitoraggio. Assegnare un nome alla regola e selezionare il gruppo di risorse in cui deve essere salvato. Fare clic su **Crea** per creare la regola.

![Regola azione](media/vminsights-health-alerts/action-rule.png)


## <a name="next-steps"></a>Passaggi successivi

- [Abilitare l'integrità Guest in Monitoraggio di Azure per le macchine virtuali e caricare gli agenti.](vminsights-health-enable.md)
- [Configurare i monitoraggi utilizzando il portale di Azure.](vminsights-health-configure.md)
- [Configurare i monitoraggi con le regole di raccolta dati.](vminsights-health-configure-dcr.md)