---
title: Panoramica dei log di diagnostica di Azure
description: Informazioni sui log di diagnostica di Azure in Monitoraggio di Azure e come è possibile usarli per comprendere gli eventi che si verificano all'interno di una risorsa di Azure.
author: nkiest
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: nikiest
ms.subservice: logs
ms.openlocfilehash: 8902e29baa5802e3416bcda97ca59a5576d41829
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244870"
---
# <a name="overview-of-azure-diagnostic-logs"></a>Panoramica dei log di diagnostica di Azure

**I log di diagnostica** forniscono dati completi e frequenti sul funzionamento di una risorsa di Azure. Monitoraggio di Azure rende disponibili due tipi di log di diagnostica:

* **I log del tenant**. Questi log provengono dai servizi a livello di tenant che esistono di fuori di una sottoscrizione ad Azure, ad esempio i log di Azure Active Directory.
* **I log di risorsa**. Questi log provengono dai servizi di Azure che distribuiscono le risorse all'interno di una sottoscrizione ad Azure, ad esempio i gruppi di sicurezza di rete o gli account di archiviazione.

    ![Log di diagnostica di risorsa e altri tipi di log](media/diagnostic-logs-overview/Diagnostics_Logs_vs_other_logs_v5.png)

Il contenuto di questi log varia in base al servizio di Azure e al tipo di risorsa. I contatori delle regole di gruppo di sicurezza di rete e i controlli di insieme di credenziali delle chiavi, ad esempio, sono due tipi di log di diagnostica.

Questi log sono diversi dai [log attività](activity-logs-overview.md). Il log attività fornisce informazioni approfondite sulle operazioni eseguite sulle risorse nella sottoscrizione tramite Resource Manager, ad esempio, la creazione di una macchina virtuale o l'eliminazione di un'app per la logica. Il log attività è un log a livello di sottoscrizione. I log di diagnostica a livello di risorsa includono informazioni sulle operazioni eseguite all'interno della risorsa stessa, ad esempio, il recupero di un segreto da un insieme di credenziali delle chiavi.

Questi log differiscono anche dal log di diagnostica a livello del sistema operativo guest. I log di diagnostica del sistema operativo guest vengono compilati da un agente in esecuzione all'interno di una macchina virtuale o di un altro tipo di risorsa supportato. I log di diagnostica a livello di risorsa non richiedono l'uso di un agente e acquisiscono i dati specifici della risorsa dalla piattaforma di Azure stessa, mentre i log di diagnostica a livello del sistema operativo guest acquisiscono i dati dal sistema operativo e dalle applicazioni in esecuzione in una macchina virtuale.

Non tutti i servizi supportano il log di diagnostica descritto di seguito. [Questo articolo contiene una sezione che elenca i servizi supportati dai log di diagnostica](./../../azure-monitor/platform/diagnostic-logs-schema.md).

## <a name="what-you-can-do-with-diagnostic-logs"></a>Che cosa si può fare con i log di diagnostica
Ecco alcune delle attività che è possibile eseguire con i log di diagnostica:

![Posizione logica dei log di diagnostica](./media/diagnostic-logs-overview/Diagnostics_Logs_Actions.png)

* Salvarli in un [**account di archiviazione**](../../azure-monitor/platform/archive-diagnostic-logs.md) per il controllo o l'ispezione manuale. È possibile specificare il tempo di conservazione in giorni tramite le **impostazioni di diagnostica di risorsa**.
* [Stream vengano **hub eventi** ](diagnostic-logs-stream-event-hubs.md) per l'inserimento tramite un servizio di terze parti o una soluzione analitica personalizzata come Power BI.
* Analizzarli con [Monitoraggio di Azure](../../azure-monitor/platform/collect-azure-metrics-logs.md), qualora i dati vengano scritti subito in Monitoraggio di Azure e non sia necessario scriverli prima nella risorsa di archiviazione.  

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

È possibile usare un account di archiviazione o uno spazio dei nomi di Hub eventi che non si trovi nella stessa sottoscrizione della risorsa che crea i log. L'utente che configura l'impostazione deve disporre dell'accesso RBAC appropriato a entrambe le sottoscrizioni.

> [!NOTE]
>  Non è al momento possibile archiviare i log del flusso di rete in un account di archiviazione che risiede dietro una rete virtuale protetta.

## <a name="diagnostic-settings"></a>Impostazioni di diagnostica

I log di diagnostica di risorsa sono configurati usando le impostazioni di diagnostica delle risorse. I log di diagnostica del tenant sono configurati usando un'impostazione di diagnostica del tenant. **Le impostazioni di diagnostica** per un servizio controllano:

* Destinazione dei log di diagnostica di risorsa e delle metriche, ad esempio un account di archiviazione, un Hub eventi e/o Monitoraggio di Azure.
* Categorie di log e metriche da inviare.
* Periodo di tempo durante il quale ogni categoria di log deve essere mantenuta nell'account di archiviazione.
    - Un periodo di conservazione di zero giorni significa che i log vengono conservati all'infinito. In caso contrario, il valore può essere qualsiasi numero di giorni compreso tra 1 e 365.
    - Se i criteri di conservazione sono impostati, ma la memorizzazione dei log in un account di archiviazione è disabilitata, ad esempio se sono selezionate solo le opzioni Hub eventi o Log Analytics, i criteri di conservazione non hanno alcun effetto.
    - I criteri di conservazione vengono applicati su base giornaliera. Al termine della giornata (UTC), i log relativi a tale giornata che non rientrano più nei criteri di conservazione verranno eliminati. Se, ad esempio, è presente un criterio di conservazione di un giorno, all'inizio della giornata vengono eliminati i log relativi al giorno precedente. Il processo di eliminazione inizia a mezzanotte UTC, ma si noti che possono essere necessarie fino a 24 ore per l'eliminazione dei log dall'account di archiviazione.

Queste impostazioni vengono configurate dalle impostazioni di diagnostica nel portale, con i comandi di Azure PowerShell e CLI, o mediante il [API REST di monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/).

> [!NOTE]
> L'invio delle metriche multidimensionali tramite impostazioni di diagnostica non è attualmente supportato. Le metriche con dimensioni sono esportate come metriche a singola dimensione di tipo flat e aggregate a livello di valori di dimensione.
>
> *Ad esempio*: la metrica "Messaggi in arrivo" su un hub eventi può essere esplorata e rappresentata in un grafico a livello di singola coda. Tuttavia, in caso di esportazione tramite impostazione di diagnostica, la metrica verrà rappresentata come tutti i messaggi in ingresso in tutte le code nell'hub eventi.
>
>

## <a name="supported-services-categories-and-schemas-for-diagnostic-logs"></a>Servizi, categorie e schemi supportati per i log di diagnostica

[Vedere questo articolo](../../azure-monitor/platform/diagnostic-logs-schema.md) per un elenco completo di servizi, categorie di log e schemi supportati usati da questi servizi.

## <a name="next-steps"></a>Passaggi successivi

* [Trasmettere log di diagnostica di Azure a **Hub eventi**](diagnostic-logs-stream-event-hubs.md)
* [Modificare le impostazioni di diagnostica di risorsa usando l'API REST di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/)
* [Analizzare i log di Archiviazione di Azure con Monitoraggio di Azure](collect-azure-metrics-logs.md)
