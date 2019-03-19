---
title: Log attività di Azure Active Directory in Monitoraggio di Azure (anteprima) | Microsoft Docs
description: Introduzione ai log attività di Azure Active Directory in Monitoraggio di Azure (anteprima)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d6da0ac5ab204bbfe70ea15e80650dd9b9e6364
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442872"
---
# <a name="azure-ad-activity-logs-in-azure-monitor-preview"></a>Log attività di Azure AD in Monitoraggio di Azure (anteprima)

È ora possibile instradare i log attività di Azure Active Directory (Azure AD) a diversi endpoint per la conservazione a lungo termine e per ottenere informazioni dettagliate sui dati. L'anteprima pubblica dei log di Azure AD in Monitoraggio di Azure consente di:

* Archiviare i log attività di Azure AD in un account di archiviazione di Azure, per conservare i dati per un lungo periodo di tempo.
* Trasmettere i log attività di Azure AD in un hub eventi di Azure per l'analisi usando i più diffusi strumenti di informazioni di sicurezza e gestione degli eventi (SIEM), come Splunk e QRadar.
* Integrare i log attività di Azure AD con soluzioni per i log personalizzate eseguendone la trasmissione a un hub eventi.
* Inviare log attività di Azure AD a log di Monitoraggio di Azure per abilitare visualizzazioni avanzate, monitoraggio e avvisi per i dati connessi.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="supported-reports"></a>Report supportati

Usando questa funzionalità, è possibile instradare i log di accesso e i log di controllo di Azure AD all'account di archiviazione di Azure, a un hub eventi, ai log di Monitoraggio di Azure o a una soluzione personalizzata. 

* **Log di controllo**: Il [report delle attività del log di controllo](concept-audit-logs.md) consente di accedere alla cronologia di ogni attività eseguita nel tenant.
* **Log di accesso**: Il [report delle attività di accesso](concept-sign-ins.md) consente di determinare chi ha eseguito le attività segnalate nei log di controllo.

> [!NOTE]
> I log attività di controllo e di accesso correlati a B2C non sono al momento supportati.
>

## <a name="prerequisites"></a>Prerequisiti

Per usare questa funzionalità, sono necessari:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile [iscriversi per ottenere una versione di valutazione gratuita](https://azure.microsoft.com/free/).
* Una [licenza](https://azure.microsoft.com/pricing/details/active-directory/) di Azure AD Free, Basic, Premium 1 o Premium 2 per accedere ai log di controllo di Azure AD nel portale di Azure. 
* Un tenant di Azure AD.
* Un utente con il ruolo di **amministratore globale** o **amministratore della sicurezza** per il tenant di Azure AD.
* Una [licenza](https://azure.microsoft.com/pricing/details/active-directory/) di Azure AD Premium 1 o Premium 2 per accedere ai log di accesso di Azure AD nel portale di Azure. 

A seconda del punto in cui si desidera instradare i dati dei log di controllo, è necessario uno dei due elementi seguenti:

* Un account di archiviazione di Azure, con autorizzazioni *ListKeys*. È consigliabile usare un account di archiviazione generale invece di un account di archiviazione BLOB. Per informazioni sui prezzi per l'archiviazione, vedere il [calcolatore prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* Uno spazio dei nomi di Hub eventi di Azure, per l'integrazione con soluzioni di terze parti.
* Un'area di lavoro di Azure Log Analytics per inviare log a log di Monitoraggio di Azure.

## <a name="cost-considerations"></a>Considerazioni sul costo

Se si dispone già di una licenza di Azure AD, è necessaria una sottoscrizione di Azure per configurare l'account di archiviazione o l'hub eventi. La sottoscrizione di Azure viene fornita gratuitamente, ma è necessario un pagamento per usare le risorse di Azure, tra cui l'account di archiviazione usato per archiviazione e l'hub eventi usato per la trasmissione. La quantità di dati, e quindi il costo addebitato, variano significativamente a seconda delle dimensioni del tenant. 

### <a name="storage-size-for-activity-logs"></a>Dimensioni di archiviazione per i log attività

Ogni evento del log di controllo usa circa 2 KB di archiviazione dati. Per un tenant con 100.000 utenti, che potrebbe comportare circa 1,5 milioni di eventi al giorno, occorrerebbero circa 3 GB di archiviazione dati al giorno. Dato che le scritture vengono eseguite in batch ogni 5 minuti circa, è possibile prevedere circa 9.000 operazioni di scrittura al mese. 

La tabella seguente contiene una stima del costo, a seconda delle dimensioni del tenant, per un account di archiviazione per utilizzo generico v2 negli Stati Uniti occidentali per almeno un anno di conservazione. Usare il [calcolatore prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/) per creare una stima più accurata per il volume di dati previsto per l'applicazione. 

| Categoria di log | Numero di utenti | Eventi al giorno | Volume di dati mensile (stima) | Costo mensile (stima) | Costo annuale (stima) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Audit | 100,000 | 1,5&nbsp;milioni | 90 GB | $1,93 | $23,12 |
| Audit | 1.000 | 15.000 | 900 MB | $0,02 | $0,24 |
| Accessi | 1.000 | 34.800 | 4 GB | $0,13 | $1,56 |
| Accessi | 100,000 | 15&nbsp;milioni | 1,7 TB | $35,41 | $424,92 | 


### <a name="event-hub-messages-for-activity-logs"></a>Messaggi dell'hub eventi per i log attività

Gli eventi sono suddivisi in batch a intervalli di circa cinque minuti e vengono inviati come un singolo messaggio che contiene tutti gli eventi entro tale intervallo di tempo. Un messaggio nell'hub eventi ha una dimensione massima di 256 KB. Se le dimensioni di tutti i messaggi nell'intervallo di tempo superano tale volume, verranno inviati più messaggi. 

Ad esempio, circa 18 eventi al secondo si verificano di solito per un tenant di grandi dimensioni di oltre 100.000 utenti, un tasso che equivale a 5.400 eventi ogni cinque minuti. Dato che i log di controllo sono di circa 2 KB per evento, ciò equivale a 10,8 MB di dati. Vengono quindi inviati 43 messaggi all'hub eventi in quell'intervallo di cinque minuti. 

La tabella seguente contiene una stima del costo mensile per un hub eventi di base negli Stati Uniti occidentali, a seconda del volume di dati degli eventi. Usare il [calcolatore prezzi di Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/) per calcolare una stima accurata del volume di dati previsto per l'applicazione.

| Categoria di log | Numero di utenti | Eventi al secondo | Eventi per intervallo di cinque minuti | Volume per intervallo | Messaggi per intervallo | Messaggi al mese | Costo mensile (stima) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Audit | 100,000 | 18 | 5.400 | 10,8 MB | 43 | 371.520 | $10,83 |
| Audit | 1.000 | 0,1 | 52 | 104 KB | 1 | 8.640 | $10,80 |
| Accessi | 1.000 | 178 | 53.400 | 106,8&nbsp;MB | 418 | 3.611.520 | $11,06 |  

### <a name="azure-monitor-logs-cost-considerations"></a>Considerazioni sui costi dei log di Monitoraggio di Azure

Per esaminare i costi legati alla gestione dei log di Monitoraggio di Azure, vedere [Gestire i costi controllando il volume e la conservazione dei dati nei log di Monitoraggio di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-cost-storage).

## <a name="frequently-asked-questions"></a>Domande frequenti

Questa sezione contiene risposte a domande frequenti e i problemi noti relativi ai log di Azure AD in Monitoraggio di Azure.

**D: Quali log sono inclusi?**

**R**: Sia i log attività di accesso che i log di controllo sono disponibili per l'invio tramite questa funzionalità, tuttavia gli eventi di controllo correlati a B2C non sono attualmente inclusi. Per scoprire quali tipi di log e quali log basati sulle funzionalità sono attualmente supportati, vedere [Schema del log di controllo](reference-azure-monitor-audit-log-schema.md) e [Schema del log di accesso](reference-azure-monitor-sign-ins-log-schema.md). 

-----

**D: Quanto tempo dopo un'azione i log corrispondenti vengono visualizzati nell'hub eventi?**

**R**: I log verranno visualizzati nell'hub eventi entro 2-5 minuti dall'esecuzione dell'azione. Per altre informazioni su Hub eventi, vedere [Informazioni su Hub eventi di Azure](../../event-hubs/event-hubs-about.md).

-----

**D: Quanto tempo dopo un'azione i log corrispondenti vengono visualizzati nell'account di archiviazione?**

**R**: Per gli account di archiviazione di Azure, la latenza è compresa tra 5 e 15 minuti dopo l'esecuzione dell'azione.

-----

**D: Cosa accade se un amministratore modifica il periodo di memorizzazione di un'impostazione di diagnostica?**

**R**: Verrà applicato il nuovo criterio di conservazione ai log raccolti dopo la modifica. I log raccolti prima della modifica dei criteri non saranno interessati.

-----

**D: Qual è il costo per l'archiviazione dei dati?**

**R**: Il costo per l'archiviazione dipende dalle dimensioni dei log e dal periodo di conservazione selezionato. Per un elenco dei costi stimati per i tenant, che dipendono dal volume dei log generati, vedere la sezione [Dimensioni di archiviazione per i log attività](#storage-size-for-activity-logs).

-----

**D: Qual è il costo della trasmissione dei dati ad Hub eventi?**

**R**: Il costo per la trasmissione dipende dal numero di messaggi ricevuti al minuto. Questo articolo illustra come vengono calcolati i costi ed elenca le stime dei costi, che si basano sul numero di messaggi. 

-----

**D: Come è possibile integrare i log attività di Azure AD con il sistema di informazioni di sicurezza e gestione degli eventi?**

**R**: Questa operazione può essere eseguita in due modi:

- Usare Monitoraggio di Azure con Hub eventi per trasmettere i log nel sistema di informazioni di sicurezza e gestione degli eventi. Prima di tutto, [trasmettere i log a un hub eventi](tutorial-azure-monitor-stream-logs-to-event-hub.md) e quindi [configurare il sistema di informazioni di sicurezza e gestione degli eventi](tutorial-azure-monitor-stream-logs-to-event-hub.md#access-data-from-your-event-hub) con l'hub eventi configurato. 

- Usare l'[API Graph per la creazione di report](concept-reporting-api.md) per accedere ai dati e inviarli al sistema di informazioni di sicurezza e gestione degli eventi usando script personalizzati.

-----

**D: Quali strumenti SIEM sono attualmente supportati?** 

**R**: Attualmente, Monitoraggio di Azure è supportato da [Splunk](tutorial-integrate-activity-logs-with-splunk.md), QRadar e [Sumo Logic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory). Per altre informazioni sul funzionamento dei connettori, vedere [Trasmettere i dati di monitoraggio di Azure a un hub eventi per il consumo da parte di uno strumento esterno](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

-----

**D: Come è possibile integrare i log attività di Azure AD con l'istanza di Splunk?**

**R**: Prima di tutto, [indirizzare i log attività di Azure AD a un hub eventi](quickstart-azure-monitor-stream-logs-to-event-hub.md), quindi seguire i passaggi per [integrare i log attività con Splunk](tutorial-integrate-activity-logs-with-splunk.md).

-----

**D: Come è possibile integrare i log attività di Azure AD con Sumo Logic?** 

**R**: Prima di tutto, [indirizzare i log attività di Azure AD a un hub eventi](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory), quindi seguire i passaggi per [installare l'applicazione Azure AD e visualizzare i dashboard in SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards).

-----

**D: È possibile accedere ai dati da un hub eventi senza usare uno strumento di informazioni di sicurezza e gestione degli eventi esterno?** 

**R**: Sì. È possibile usare l'[API di Hub eventi](../../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) per accedere ai log da un'applicazione personalizzata. 

-----


## <a name="next-steps"></a>Passaggi successivi

* [Archiviare i log attività in un account di archiviazione](quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Trasmettere i log attività a un hub eventi](quickstart-azure-monitor-stream-logs-to-event-hub.md)
* [Integrare log attività con Monitoraggio di Azure](howto-integrate-activity-logs-with-log-analytics.md)
