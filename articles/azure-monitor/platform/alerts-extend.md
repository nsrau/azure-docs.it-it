---
title: Estendere (copiare) gli avvisi di Log Analytics nel cloud di Azure per enti pubblici
description: Panoramica del processo per la copia di avvisi da Log Analytics nel portale OMS in Avvisi di Azure e informazioni sulle principali preoccupazioni dei clienti.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: efd2fc9d164564126b7b641ef35bbb10c4834f49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996405"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>Estendere gli avvisi di Log Analytics in Avvisi di Azure

> [!NOTE]
> Microsoft ha già completato la procedura descritta in questo articolo per le versioni pubbliche di Azure. Tuttavia, si applica ancora alle versioni per enti pubblici degli Stati Uniti.  

Azure Log Analytics includeva fino a poco tempo fa una propria funzionalità di avviso, in grado di informare l'utente in modo proattivo relativamente ad alcune condizioni in base ai dati di Log Analytics. Le regole degli avvisi venivano gestite nel portale Microsoft Operations Management Suite (OMS). La nuova esperienza Avvisi è ora integrata nei diversi servizi e componenti di Microsoft Azure. È disponibile come **Avvisi** in Monitoraggio di Azure nel portale di Azure e supporta avvisi dei log attività, avvisi delle metriche e avvisi relativi ai log per Log Analytics e Application Insights.

I clienti del cloud di Azure per enti pubblici che usano il portale di OMS possono [scegliere di estendere le regole di avviso ad Azure](alerts-extend-tool.md) dall'**1 febbraio 2019**. A partire dall'**1 marzo 2019** Microsoft estenderà automaticamente in modo sistematico tutte le regole di avviso esistenti nel portale di OMS per Azure per enti pubblici senza tempi di inattività o interruzioni nel monitoraggio. Le nuove aree di lavoro create a partire dall'**1 marzo 2019** nel portale di OMS per Azure per enti pubblici verranno automaticamente estese ad Azure.

## <a name="benefits-of-extending-your-alerts"></a>Vantaggi offerti dall'estensione degli avvisi
La creazione e la gestione degli avvisi nel portale di Azure offre diversi vantaggi, ad esempio:

- A differenza del portale di OMS, dove possono essere creati e visualizzati solo 250 avvisi, in Avvisi di Azure questa limitazione non è presente.
- Avvisi di Azure consente di gestire, enumerare e visualizzare tutti i tipi di avviso. In precedenza, era possibile eseguire queste operazioni solo per gli avvisi di Log Analytics.
- È possibile controllare l'accesso degli utenti solo per le attività di monitoraggio e invio di avvisi tramite il [ruolo Monitoraggio di Azure](../../azure-monitor/platform/roles-permissions-security.md).
- In Avvisi di Azure, è possibile usare [gruppi di azioni](../../azure-monitor/platform/action-groups.md). Ciò consente di eseguire più di un'azione per ogni avviso. È possibile inviare SMS, chiamate vocali, richiamare un runbook di automazione di Azure, richiamare un webhook e configurare un connettore di Gestione dei servizi IT (ITSM). 

## <a name="process-of-extending-your-alerts"></a>Processo di estensione degli avvisi
Il processo di estensione degli avvisi da Log Analytics in Azure non comporta la modifica della definizione, della query o della configurazione degli avvisi. L'unica modifica necessaria è che in Azure tutte le azioni vengono eseguite usando un gruppo di azioni. Se i gruppi di azioni sono già associati con l'avviso, questi vengono inclusi durante l'estensione in Azure.

Quando vengono pianificati per l'estensione ad Azure, gli avvisi presenti in un'area di lavoro Log Analytics continuano a funzionare, senza compromettere in alcun modo la configurazione. Se pianificati, gli avvisi possono risultare temporaneamente non disponibili per la modifica, ma in questo intervallo è possibile continuare a creare nuovi avvisi in Azure. Se si tenta di modificare o creare avvisi dal portale di OMS, è possibile continuare la loro creazione dall'area di lavoro Log Analytics. È anche possibile crearli da Avvisi di Azure nel portale di Azure.

 ![Schermata dell'opzione per creare avvisi da Log Analytics o da Avvisi di Azure](media/alerts-extend/ScheduledDirection.png)

> [!NOTE]
> L'estensione degli avvisi da Log Analytics in Azure non implica alcun addebito sull'account. Per l'utilizzo di Avvisi di Azure per la generazione di avvisi di Log Analytics basati su query non verranno addebitati costi, purché siano rispettati i limiti e le condizioni indicati in [Prezzi di Monitoraggio di Azure ](https://azure.microsoft.com/pricing/details/monitor/).  


### <a name="how-to-extend-your-alerts-voluntarily"></a>Come estendere gli avvisi volontariamente
Per estendere gli avvisi in Avvisi di Azure, è possibile usare una procedura guidata disponibile nel portale di OMS, oppure agire a livello di codice tramite un'API. Per altre informazioni, vedere [Estensione degli avvisi ad Azure tramite il portale di OMS e l'API](alerts-extend-tool.md).

## <a name="experience-after-extending-your-alerts"></a>Esperienza dopo l'estensione degli avvisi
Dopo aver esteso gli avvisi in Avvisi di Azure, questi continuano a essere disponibili per la gestione nel portale di OMS, come prima.

![Schermata del portale di OMS, con elenchi di avvisi](media/alerts-extend/PostExtendList.png)

Quando si tenta di modificare un avviso esistente o di creare un nuovo avviso nel portale di OMS, si verrà reindirizzati automaticamente ad Avvisi di Azure.  

> [!NOTE]
> Assicurarsi che le autorizzazioni assegnate ai singoli utenti che hanno necessità di aggiungere o modificare gli avvisi siano assegnate correttamente in Azure. Per comprendere quali autorizzazioni è necessario concedere vedere le [autorizzazioni per l'uso di Monitoraggio di Azure e Avvisi di Azure](../../azure-monitor/platform/roles-permissions-security.md).  
> 

È possibile continuare a creare avvisi dall'[API di Log Analytics](../../azure-monitor/platform/api-alerts.md) e dal [modello di risorsa di Log Analytics](../../azure-monitor/insights/solutions-resources-searches-alerts.md). Quando si esegue questa operazione, è necessario includere i gruppi di azioni.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sugli strumenti per [avviare l'estensione degli avvisi da Log Analytics ad Azure](alerts-extend-tool.md).
* Altre informazioni sull'[esperienza di Avvisi di Azure](../../azure-monitor/platform/alerts-overview.md).
* Informazioni su come creare [avvisi del log in Avvisi di Azure](alerts-unified-log.md).

