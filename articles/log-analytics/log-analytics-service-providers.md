---
title: "Funzionalità di Log Analytics per i provider di servizi | Documentazione Microsoft"
description: Log Analytics aiuta i provider dei servizi gestiti (MSP), le aziende di grandi dimensioni, i fornitori di software indipendenti (ISV) e i provider di servizi di hosting a gestire e monitorare i server nell'infrastruttura cloud o locale del cliente.
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: richrund
ms.openlocfilehash: 3910038e788352df45ab00c0f697d9a5426b3498
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="log-analytics-features-for-service-providers"></a>Funzionalità di Log Analytics per i provider di servizi
Log Analytics aiuta i provider dei servizi gestiti (MSP), le aziende di grandi dimensioni, i fornitori di software indipendenti (ISV) e i provider di servizi di hosting a gestire e monitorare i server nell'infrastruttura cloud o locale del cliente. 

Le aziende di grandi dimensioni hanno molti punti in comune con i provider di servizi, soprattutto quando c'è un team IT centralizzato che si occupa della gestione dell'IT per molte business unit diverse tra loro. Per semplicità, in questo documento si usa il termine *provider di servizi* ma la stessa funzionalità è disponibile anche per le aziende e gli altri clienti.

## <a name="cloud-solution-provider"></a>Provider di soluzioni cloud
Per i partner e i provider di servizi che fanno parte del programma [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview), Log Analytics è uno dei servizi di Azure disponibili in una [sottoscrizione di Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

Per Log Analytics, le seguenti funzionalità sono abilitate nelle sottoscrizioni *Provider di soluzioni cloud*.

In qualità di *Provider di soluzioni cloud* è possibile:

* Creare le aree di lavoro di Log Analytics in una sottoscrizione di un tenant (cliente).
* Accedere alle aree di lavoro create dai tenant. 
* Aggiungere e rimuovere l'accesso utente all'area di lavoro usando la gestione utente di Azure. Quando in un'area di lavoro del tenant nel portale di OMS la pagina di gestione degli utenti in Impostazioni non è disponibile
  * Log Analytics non supporta ancora l'accesso basato sui ruoli, concedendo a un utente `reader` nel portale di Azure l'autorizzazione ad apportare modifiche alla configurazione nel portale di OMS

Per accedere alla sottoscrizione del tenant è necessario specificare l'identificatore del tenant. L'identificatore del tenant corrisponde spesso all'ultima parte dell'indirizzo di posta elettronica con cui si esegue l'accesso.

* Nel portale di OMS, aggiungere `?tenant=contoso.com` nell'URL per il portale. Ad esempio: `mms.microsoft.com/?tenant=contoso.com`
* In PowerShell, usare il parametro `-Tenant contoso.com` quando si usa il cmdlet `Add-AzureRmAccount`
* L'identificatore del tenant viene aggiunto automaticamente quando si usa il collegamento `OMS portal` dal portale di Azure per aprire e accedere al portale di OMS per l'area di lavoro selezionata

In qualità di *cliente* di un provider di soluzioni cloud è possibile:

* Creare le aree di lavoro di Log Analytics in una sottoscrizione CSP
* Accedere alle aree di lavoro create dal CSP
  * Usare il collegamento `OMS portal` dal portale di Azure per aprire e accedere al portale di OMS per l'area di lavoro selezionata
* Visualizzare e usare la pagina di gestione degli utenti in Impostazioni nel portale di OMS

> [!NOTE]
> Le soluzioni di backup e Site Recovery incluse per Log Analytics non possono connettersi a un insieme di credenziali di Servizi di ripristino e non possono essere configurate in una sottoscrizione CSP. 
> 
> 

## <a name="managing-multiple-customers-using-log-analytics"></a>Gestione di più clienti che usano Log Analytics
È consigliabile creare un'area di lavoro Log Analytics per ogni cliente gestito. Un'area di lavoro di Log Analytics offre:

* Una posizione geografica per archiviare i dati. 
* Granularità per la fatturazione 
* Isolamento dei dati 
* Configurazione univoca

Tramite la creazione di un'area di lavoro per cliente, è possibile mantenere separati i dati di ciascun cliente e di tenere traccia dell'uso di ogni cliente.

Per altre informazioni su quando e perché creare più aree di lavoro, vedere [Gestire l'accesso a Log Analytics](log-analytics-manage-access.md#determine-the-number-of-workspaces-you-need).

Le operazioni di creazione e configurazione delle aree di lavoro del cliente possono essere automatizzate tramite [PowerShell](log-analytics-powershell-workspace-configuration.md), i [Modelli di Resource Manager](log-analytics-template-workspace-configuration.md) o tramite l'[API REST](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights/).

L'uso dei modelli di Resource Manager per la configurazione dell'area di lavoro consente di usare una configurazione principale per creare e configurare le aree di lavoro. È possibile essere certi che le aree di lavoro create per i clienti vengono configurate automaticamente in base ai requisiti. Quando si aggiornano i requisiti, il modello viene aggiornato a sua volta e riapplicato alle aree di lavoro esistenti. Questo processo assicura che le aree di lavoro esistenti siano conformi ai nuovi standard.    

Quando si gestiscono più aree di lavoro di Log Analytics, si consiglia di integrare ogni area di lavoro con il sistema basato su ticket esistente o con la console operatore mediante la funzionalità [Avvisi](log-analytics-alerts.md). Grazie all'integrazione con i sistemi esistenti, il personale di supporto può continuare a seguire i processi abituali. Log Analytics controlla regolarmente ogni area di lavoro in base ai criteri di avviso specificati e genera un avviso quando si richiede di eseguire un'azione.

Per ottenere visualizzazioni personalizzate dei dati, usare la funzionalità [dashboard](../azure-portal/azure-portal-dashboards.md) nel portale di Azure.  

Per i report a livello esecutivo che riepilogano i dati nelle aree di lavoro è possibile usare l'integrazione tra Log Analytics e [PowerBI](log-analytics-powerbi.md). Se si richiede l'integrazione con un altro sistema di reporting, è possibile usare l'API di ricerca (tramite PowerShell o [REST](log-analytics-log-search-api.md)) per eseguire le query ed esportare i risultati della ricerca.

## <a name="next-steps"></a>Fasi successive
* Automatizzare la creazione e la configurazione delle aree di lavoro usando i [modelli di Resource Manager](log-analytics-template-workspace-configuration.md)
* Automatizzare la creazione delle aree di lavoro usando [PowerShell](log-analytics-powershell-workspace-configuration.md) 
* Usare la funzione [Avvisi](log-analytics-alerts.md) per l'integrazione con i sistemi esistenti
* Generare report di riepilogo usando [Power BI](log-analytics-powerbi.md)

