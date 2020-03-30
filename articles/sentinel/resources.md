---
title: Risorse utili per l'utilizzo di Azure Sentinel Documenti Microsoft
description: Questo documento fornisce un elenco di risorse utili quando si lavora con Azure Sentinel.This document provides you with a list of useful resources when working with Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: yelevin
ms.openlocfilehash: 2110d3319cebf693ef06deec26a29fa655e35035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585272"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Risorse utili per l'uso di Azure Sentinel



Questo articolo elenca le risorse che consentono di ottenere altre informazioni sull'uso di Azure Sentinel.This article lists resources that can help you get more information about working with Azure Sentinel.

Connettori delle app per la logica di Azure:Azure Logic Apps connectors:<https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Controllo e creazione di report
I log di controllo di Azure Sentinel vengono gestiti nei log attività di Azure.Audit logs of Azure Sentinel are maintained in [Azure Activity Logs](../azure-monitor/platform/platform-logs-overview.md).

È possibile controllare le seguenti operazioni supportate.

|Nome operazione|    Tipo di risorsa|
|----|----|
|Creare o aggiornare una cartella di lavoro  |Microsoft.Insights/cartelle di lavoro|
|Elimina cartella di lavoro    |Microsoft.Insights/cartelle di lavoro|
|Imposta flusso di lavoro   |Microsoft.Logic/workflows|
|Elimina flusso di lavoro    |Microsoft.Logic/workflows|
|Crea ricerca salvata    |Microsoft.OperationalInsights/workspaces/savedSearches (Informazioni in preceden0 giorni|
|Elimina ricerca salvata    |Microsoft.OperationalInsights/workspaces/savedSearches (Informazioni in preceden0 giorni|
|Aggiorna regole di avviso |Microsoft.SecurityInsights/alertRules|
|Eliminare le regole di avvisoDelete Alert Rules |Microsoft.SecurityInsights/alertRules|
|Azioni di risposta della regola di avviso di aggiornamento |Microsoft.SecurityInsights/alertRules/actions|
|Eliminare le azioni di risposta della regola di avvisoDelete Alert Rule Response Actions |Microsoft.SecurityInsights/alertRules/actions|
|Aggiorna segnalibri   |Microsoft.SecurityInsights/segnalibri|
|Elimina segnalibri   |Microsoft.SecurityInsights/segnalibri|
|Aggiorna casi   |Microsoft.SecurityInsights/Casi|
|Aggiorna indagine caso  |Microsoft.SecurityInsights/Casi/indagini|
|Creazione di commenti del caso   |Microsoft.SecurityInsights/Casi/commenti|
|Aggiornare i connettori dati |Microsoft.SecurityInsights/dataConnectors|
|Eliminazione di connettori dati |Microsoft.SecurityInsights/dataConnectors|
|Aggiorna impostazioni    |Microsoft.SecurityInsights/impostazioni|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Visualizzare i dati di controllo e creazione di report in Azure SentinelView audit and reporting data in Azure Sentinel

È possibile visualizzare questi dati trasmettendoli dal log attività di Azure in Azure Sentinel, dove è possibile eseguire ricerche e analisi su di esso.

1. Connettere l'origine dati [Attività di Azure.Connect](connect-azure-activity.md) the Azure Activity data source. Dopo aver eseguito questa operazione, gli eventi di controllo vengono trasmessi in una nuova tabella nella schermata Log denominata AzureActivity.After doing this, audit events are streamed into a new table in the **Logs** screen called AzureActivity.
2. Quindi, eseguire una query sui dati utilizzando KQL, come si farebbe con qualsiasi altra tabella.



## <a name="vendor-documentation"></a>Documentazione del fornitore

| **Fornitore**  | **Usare l'evento imprevisto in Azure SentinelUse incident in Azure Sentinel** | **Link**|
|----|----|----|
| GitHub| Utilizzato per accedere alla pagina Community| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| Configurare CEF| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| Pluralsight | Corso sul linguaggio di query Kusto| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Blog e forum

Pubblicare le domande nello spazio TechCommunity per Azure Sentinel.Post your questions on the [TechCommunity space](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) for Azure Sentinel.

Visualizzare i post di blog di Azure Sentinel da [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) e [Microsoft Azure.](https://azure.microsoft.com/blog/tag/azure-sentinel/)


## <a name="next-steps"></a>Passaggi successivi
In questo documento è disponibile un elenco di risorse utili quando si lavora con Azure Sentinel.In this document, you have a list of resources that are useful when you're working with Azure Sentinel. Ulteriori informazioni sulla sicurezza e la conformità di Azure sono disponibili nel blog sulla sicurezza e la conformità di [Microsoft Azure](https://blogs.msdn.com/b/azuresecurity/).
