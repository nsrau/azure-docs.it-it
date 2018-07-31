---
title: Log attività di Azure Active Directory in Monitoraggio di Azure (anteprima) | Microsoft Docs
description: Panoramica dei log attività di Azure Active Directory in Monitoraggio di Azure (anteprima)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 9b594360bc760fa9eec8ab9900c3aadcb10e9db6
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240109"
---
# <a name="azure-active-directory-activity-logs-in-azure-monitor-preview"></a>Log attività di Azure Active Directory in Monitoraggio di Azure (anteprima)

Ora è possibile inviare i log attività di Azure AD tramite Monitoraggio di Azure al proprio account di archiviazione o hub eventi. Con l'anteprima pubblica dei log di Azure Active Directory in Monitoraggio di Azure, è possibile:

* Archiviare i log di controllo per un account di archiviazione di Azure, in modo da conservare i dati per un periodo di tempo prolungato
* Trasmettere i log di controllo a un hub eventi di Azure per l'analisi usando diffusi strumenti SIEM come Splunk e QRadar
* Integrare i log di controllo con soluzioni per i log personalizzate eseguendone la trasmissione a un hub eventi

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

## <a name="supported-reports"></a>Report supportati

Usando questa funzionalità, è possibile trasmettere i log attività di controllo e i log attività degli accessi a un account di archiviazione di Azure, a un hub eventi o a una soluzione personalizzata. 

* **Log di controllo**: il [report sull'attività relativo ai log di controllo](active-directory-reporting-activity-audit-logs.md) consente di accedere alla cronologia di ogni attività eseguita nel tenant.
* **Accessi**: il [report sull'attività relativo agli accessi](active-directory-reporting-activity-sign-ins.md) permette di determinare chi ha eseguito le attività segnalate nel report dei log di controllo.

> [!NOTE]
> I log attività di controllo e degli accessi correlati a B2C al momento non sono supportati.
>

## <a name="prerequisites"></a>Prerequisiti

Per usare questa funzionalità, sono necessari:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile [iscriversi per ottenere una versione di valutazione gratuita](https://azure.microsoft.com/free/).
* Una [licenza](https://azure.microsoft.com/pricing/details/active-directory/) di Azure AD gratuita, Base, Premium 1 o Premium 2 per l'accesso ai log di Azure AD nel portale di Azure. 

A seconda della posizione in cui inviare i dati dei log di controllo, è necessario:

* Un account di archiviazione di Azure, con autorizzazioni *ListKeys*. È consigliabile usare un account di archiviazione generale invece di un account di archiviazione BLOB. Per informazioni sui prezzi per l'archiviazione, vedere la pagina per il [calcolo dei prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* Spazio dei nomi Hub eventi di Azure, per l'integrazione con soluzioni di terze parti.

> [!NOTE]
> Per accedere ai log attività di Azure AD, è necessario essere un *amministratore globale* o un *amministratore della sicurezza* nel tenant di Azure AD.
>

## <a name="cost-considerations"></a>Considerazioni sul costo

Se si dispone già di una licenza di Azure AD, è necessaria una sottoscrizione di Azure per configurare l'account di archiviazione o l'hub eventi. La sottoscrizione di Azure viene fornita gratuitamente, ma è necessario un pagamento per usare le risorse di Azure, tra cui l'account di archiviazione usato per archiviazione e l'hub eventi usato per la trasmissione. La quantità di dati, e quindi il costo addebitato, variano significativamente a seconda delle dimensioni del tenant. 

### <a name="storage-size-for-activity-logs"></a>Dimensioni di archiviazione per i log attività

Ogni evento del log di controllo usa fino a circa 2 KB di archiviazione dati. Per un tenant con 100.000 utenti, che potrebbe comportare circa 1,5 milioni di eventi al giorno, occorrerebbero circa 3 GB di archiviazione dati al giorno. Poiché le scritture vengono eseguite in batch ogni 5 minuti circa, è possibile prevedere circa 9000 operazioni di scrittura al mese. La tabella seguente contiene una stima approssimativa del costo, a seconda delle dimensioni del tenant, per un account di archiviazione per utilizzo generico v2 negli Stati Uniti occidentali per almeno un anno di conservazione. Usare la pagina per il [calcolo dei prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/) per creare una stima più accurata per il volume di dati previsto per l'applicazione. Ogni evento del log di controllo usa fino a circa 2 KB di archiviazione dati. Per un tenant con 100.000 utenti, che potrebbe comportare circa 1,5 milioni di eventi al giorno, occorrerebbero circa 3 GB di archiviazione dati al giorno. Poiché le scritture vengono eseguite in batch ogni 5 minuti circa, è possibile prevedere circa 9000 operazioni di scrittura al mese. La tabella seguente contiene una stima approssimativa del costo, a seconda delle dimensioni del tenant, per un account di archiviazione per utilizzo generico v2 negli Stati Uniti occidentali per almeno un anno di conservazione. Usare la pagina per il [calcolo dei prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/) per creare una stima più accurata per il volume di dati previsto per l'applicazione. 

| Categoria di log | Numero di utenti | Numero di eventi al giorno | Volume approssimativo di dati al mese | Costo approssimativo al mese | Costo approssimativo all'anno |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Audit | 100.000 | 1,5 milioni | 90 GB | $1,93 | $23,12 |
| Audit | 1000 | 15000 | 900 MB | $0,02 | $0,24 |
| Accessi | 1000 | 34800 | 4 GB | $0,13 | $1,56 |
| Accessi | 100.000 | 15 milioni | 1,7 TB | $35,41 | $424,92 | 


### <a name="event-hub-messages-for-activity-logs"></a>Messaggi dell'hub eventi per i log attività

Gli eventi sono suddivisi in batch in intervalli di circa cinque minuti e vengono inviati come un singolo messaggio che contiene tutti gli eventi entro tale intervallo di tempo. Un messaggio nell'hub eventi ha una dimensione massima di 256 KB. Se le dimensioni di tutti i messaggi nell'intervallo di tempo superano tale volume, vengono inviati più messaggi. 

Ad esempio, in genere si verificano circa 18 eventi al secondo per un tenant di grandi dimensioni con più di 100.000 utenti, pari a 5400 eventi ogni 5 minuti. Poiché i log di controllo sono circa di 2 KB per ogni evento, ciò corrisponde a 10,8 MB di dati, pertanto verranno inviati 43 messaggi all'hub eventi in tale intervallo di 5 minuti. La tabella seguente contiene una stima approssimativa del costo per un hub eventi di base negli Stati Uniti occidentali, a seconda del volume di dati degli eventi. Usare la pagina per il [calcolo dei prezzi di Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/) per calcolare una stima accurata per il volume di dati previsto per l'applicazione.

| Categoria di log | Numero di utenti | Numero di eventi al secondo | Numero di eventi per intervallo di 5 minuti | Volume per intervallo | Numero di messaggi per intervallo | Numero di messaggi per mese | Costo approssimativo al mese |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Audit | 100.000 | 18 | 5400 | 10,8 MB | 43 | 371.520 | $10,83 |
| Audit | 1000 | 0,1 | 52 | 104 KB | 1 | 8640 | $10,8 |
| Accessi | 1000 | 178 | 53400 | 106,8 MB | 418 | 3.611.520 | $11,06 |  


## <a name="frequently-asked-questions"></a>Domande frequenti

Questa sezione contiene le domande frequenti e i problemi noti relativi ai log di Azure Active Directory in Monitoraggio di Azure.

**D: Da dove è consigliabile iniziare?** 

**R:** Iniziare con la [Panoramica](reporting-azure-monitor-diagnostics-overview.md) per avere un'idea degli elementi necessari per distribuire questa funzionalità. Una volta acquisita familiarità con i prerequisiti, consultare le esercitazioni per informazioni su come configurare e inviare i log agli hub eventi.

---

**D: Quali log sono inclusi?**

**R:** Sia i log degli accessi che quelli di controllo sono disponibili per l'invio mediante questa funzionalità, tuttavia gli eventi di controllo correlati a B2C attualmente non sono inclusi. Per informazioni sui tipi di log e sui log basati sulle funzionalità attualmente supportati, vedere le pagine relative allo [schema dei log di controllo](reporting-azure-monitor-diagnostics-audit-log-schema.md) e allo [schema del log di accesso](reporting-azure-monitor-diagnostics-sign-in-log-schema.md). 

---

**D: Quanto tempo dopo un'azione i log corrispondenti vengono visualizzati in Hub eventi?**

**R:** I log vengono visualizzati in Hub eventi entro un periodo da due a cinque minuti dall'esecuzione dell'azione. Per altre informazioni su Hub eventi, vedere [Che cos'è Hub eventi?](/azure/event-hubs/event-hubs-what-is-event-hubs.md).

---

**D: Quanto tempo dopo un'azione i log corrispondenti vengono visualizzati negli account di archiviazione?**

**R:** Per gli account di archiviazione di Azure, la latenza è da 5 a 15 minuti dall'esecuzione dell'azione.

---

**D: Qual è il costo per l'archiviazione dei dati?**

**R:** Il costo per l'archiviazione dipende dalle dimensioni dei log, oltre che dal periodo di conservazione selezionato. Per una stima approssimativa dei costi per i tenant a seconda del volume dei log generati, consultare la [Panoramica](reporting-azure-monitor-diagnostics-overview.md).

---

**D: Qual è il costo per la trasmissione dei dati ad Hub eventi?**

**R:** Il costo per la trasmissione dipende dal numero di messaggi ricevuti al minuto. Leggere la [Panoramica](reporting-azure-monitor-diagnostics-overview.md) per altre informazioni su come viene calcolato il costo, nonché per ottenere una stima approssimativa basata sul numero di messaggi. 

---

**D: Quali strumenti SIEM sono attualmente supportati?** 

**R:** Monitoraggio di Azure è attualmente supportato da Splunk, QRadar e SumoLogic. Tuttavia, Splunk è l'unico strumento SIEM supportato per i log di Azure Active Directory. Per altre informazioni sul funzionamento dei connettori, vedere [Trasmettere i dati di monitoraggio di Azure a un hub eventi per il consumo da parte di uno strumento esterno](/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs).

---

**D: È possibile accedere ai dati da Hub eventi senza usare uno strumento SIEM esterno?** 

**R:** Sì, è possibile usare l'[API di Hub eventi](/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph) per accedere ai log da un'applicazione personalizzata. 

---


## <a name="next-steps"></a>Passaggi successivi

* [Archiviare i log attività in un account di archiviazione](reporting-azure-monitor-diagnostics-azure-storage-account.md)
* [Trasmettere i log di attività a Hub eventi](reporting-azure-monitor-diagnostics-azure-event-hub.md)
* [Altre informazioni sui log di diagnostica di Azure](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)