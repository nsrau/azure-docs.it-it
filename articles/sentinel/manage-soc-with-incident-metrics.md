---
title: Gestire meglio il SOC con le metriche relative agli eventi imprevisti in Sentinel di Azure | Microsoft Docs
description: Usare le informazioni della schermata e della cartella di lavoro della metrica per gli eventi imprevisti di Sentinel di Azure per semplificare la gestione di Operations Center (SOC).
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2020
ms.author: yelevin
ms.openlocfilehash: 408913fed864ee5f966b96c81afbfee4b2dc8678
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660730"
---
# <a name="manage-your-soc-better-with-incident-metrics"></a>Gestire meglio il Centro operazioni di sicurezza con le metriche degli eventi imprevisti

> [!IMPORTANT]
> Le funzionalità relative alle metriche per gli eventi imprevisti sono attualmente disponibili in anteprima pubblica.
> Queste funzionalità vengono fornite senza un contratto di servizio e non sono consigliate per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In qualità di responsabile della sicurezza Operations Center (SOC), è necessario avere a portata di mano metriche e misure di efficienza complessiva per misurare le prestazioni del team. È possibile visualizzare le operazioni relative agli eventi imprevisti nel tempo in base a diversi criteri, ad esempio gravità, tattica Mitra, tempo medio di valutazione, tempo medio per la risoluzione e altro ancora. Azure Sentinel rende ora disponibili questi dati con la nuova tabella e schema di **SecurityIncident** in log Analytics e la cartella di lavoro delle **operazioni di sicurezza** associata. Potrai visualizzare le prestazioni del tuo team nel tempo e usare queste informazioni per migliorare l'efficienza. È inoltre possibile scrivere e utilizzare le proprie query KQL sulla tabella degli eventi imprevisti per creare cartelle di lavoro personalizzate che soddisfano le esigenze di controllo specifiche e gli indicatori KPI.

## <a name="use-the-security-incidents-table"></a>Usare la tabella eventi imprevisti della sicurezza

La tabella **SecurityIncident** è incorporata in Sentinel di Azure. Si troverà con le altre tabelle nella raccolta **SecurityInsights** in **logs**. È possibile eseguire query in modo analogo a qualsiasi altra tabella in Log Analytics.

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incident-table.png" alt-text="Tabella eventi imprevisti della sicurezza":::

Ogni volta che si crea o si aggiorna un evento imprevisto, viene aggiunta una nuova voce di log alla tabella. In questo modo è possibile tenere traccia delle modifiche apportate agli eventi imprevisti e consentire metriche SOC ancora più potenti, ma è necessario tenere presente questo problema durante la creazione di query per questa tabella, in quanto potrebbe essere necessario rimuovere le voci duplicate per un evento imprevisto (a seconda della query esatta in esecuzione). 

Se, ad esempio, si desidera restituire un elenco di tutti gli eventi imprevisti ordinati in base al numero di evento imprevisto, ma si desidera solo restituire il log più recente per evento imprevisto, è possibile eseguire questa operazione utilizzando l' [operatore di riepilogo](/azure/data-explorer/kusto/query/summarizeoperator) KQL con la `arg_max()` [funzione di aggregazione](/azure/data-explorer/kusto/query/arg-max-aggfunction):


```Kusto
SecurityIncident
| summarize arg_max(LastModifiedTime, *) by IncidentNumber
```
### <a name="more-sample-queries"></a>Altre query di esempio

Tempo medio per la chiusura:
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToClosure =  (ClosedTime - CreatedTime)/1h
| summarize 5th_Percentile=percentile(TimeToClosure, 5),50th_Percentile=percentile(TimeToClosure, 50), 
  90th_Percentile=percentile(TimeToClosure, 90),99th_Percentile=percentile(TimeToClosure, 99)
```

Tempo medio per la valutazione:
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToTriage =  (FirstModifiedTime - CreatedTime)/1h
| summarize 5th_Percentile=max_of(percentile(TimeToTriage, 5),0),50th_Percentile=percentile(TimeToTriage, 50), 
  90th_Percentile=percentile(TimeToTriage, 90),99th_Percentile=percentile(TimeToTriage, 99) 
```

## <a name="security-operations-efficiency-workbook"></a>Cartella di lavoro dell'efficienza delle operazioni di sicurezza

Per completare la tabella **SecurityIncidents** , è disponibile un modello di cartella di lavoro di **sicurezza per le operazioni di sicurezza** predefinite che è possibile usare per monitorare le operazioni Soc. La cartella di lavoro contiene le metriche seguenti: 
- Evento imprevisto creato nel tempo 
- Eventi imprevisti creati da classificazione, gravità, proprietario e stato di chiusura 
- Tempo medio per la valutazione 
- Tempo medio per la chiusura 
- Eventi imprevisti creati da gravità, proprietario, stato, prodotto e tattiche nel tempo 
- Tempo per la valutazione percentile 
- Tempo di chiusura percentile 
- Tempo medio per la valutazione per proprietario 
- Attività recenti 
- Classificazioni di chiusura recenti  

È possibile trovare questo nuovo modello di cartella di lavoro scegliendo **cartelle di lavoro** dal menu di navigazione di Azure Sentinel e selezionando la scheda **modelli** . Scegliere **sicurezza operazioni di sicurezza** dalla raccolta e fare clic su uno dei pulsanti **Visualizza cartella di lavoro salvata** e **Visualizza modello** .

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incidents-workbooks-gallery.png" alt-text="Raccolta cartelle di lavoro eventi imprevisti di sicurezza":::

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-operations-workbook-1.png" alt-text="Cartella di lavoro eventi imprevisti di sicurezza completata":::

È possibile usare il modello per creare cartelle di lavoro personalizzate in base alle proprie esigenze specifiche.

## <a name="securityincidents-schema"></a>Schema SecurityIncidents

[!INCLUDE [SecurityIncidents schema](../../includes/sentinel-schema-security-incident.md)]

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a usare Azure Sentinel, è necessario avere una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/free/).
- Informazioni su come [caricare i dati in Azure Sentinel](quickstart-onboard.md) e [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).