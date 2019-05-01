---
title: Metriche e avvisi di DNS di Azure | Microsoft Docs
description: Informazioni sugli avvisi e le metriche di DNS di Azure.
services: dns
documentationcenter: na
author: vhorne
manager: jennoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: victorh
ms.openlocfilehash: a9d8bc172eb5f5e0e119a0bde56fb167f7a0c2b2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64699146"
---
# <a name="azure-dns-metrics-and-alerts"></a>Metriche e avvisi di DNS di Azure
DNS di Azure è un servizio di hosting per i domini DNS che offre la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. Questo articolo descrive le metriche e gli avvisi per il servizio DNS di Azure.

## <a name="azure-dns-metrics"></a>Metriche di DNS di Azure

DNS di Azure fornisce le metriche per i clienti consentire loro di monitorare aspetti specifici delle relative zone DNS ospitate nel servizio. In aggiunta con le metriche di DNS di Azure, è possibile configurare e ricevere avvisi in base alle condizioni di interesse. Le metriche sono disponibili tramite il [servizio Monitoraggio di Azure](../azure-monitor/index.yml). DNS di Azure contiene le metriche seguenti tramite Monitoraggio di Azure per le zone DNS:

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

È anche possibile visualizzare la [definizione di queste metriche](../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) nella pagina di documentazione di Monitoraggio di Azure.
>[!NOTE]
> In questo momento le metriche sono disponibili solo per le zone DNS pubbliche ospitate in DNS di Azure. Se in DNS di Azure sono ospitate zone private, le metriche non indicheranno i dati per queste zone. In aggiunta le metriche e la funzionalità di avviso sono supportate solo nel cloud pubblico di Azure. Il supporto per i cloud sovrani verrà effettuato in un secondo momento. 

L'elemento più granulare che è possibile visualizzare le metriche per è una zona DNS. Attualmente non è possibile visualizzare le metriche per i singoli record di risorse all'interno di una zona.

### <a name="query-volume"></a>Volume delle query

La metrica *Query Volume* (Volume delle query) in DNS di Azure mostra il volume delle query DNS, ovvero il traffico di query, che è stato ricevuto da DNS di Azure per la propria zona DNS. L'unità di misura è Conteggio e l'aggregazione è data dal totale di tutte le query ricevute in un periodo di tempo. 

Per visualizzare questa metrica, selezionare l'esperienza di esplorazione Metriche (anteprima) nella scheda Monitoraggio del portale di Azure. Selezionare la zona DNS dall'elenco a discesa Risorsa, quindi la metrica Query volume (Volume delle query) e selezionare Somma come aggregazione. Lo screenshot seguente illustra un esempio.  Per altre informazioni sull'esperienza e i grafici di Esplora metriche, vedere [Esplora metriche di Monitoraggio di Azure](../azure-monitor/platform/metrics-charts.md).

![Volume delle query](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Figura: metriche Query Volume (Volume delle query) di DNS Azure*

### <a name="record-set-count"></a>Numero di set di record
La metrica *Record Set Count* (Numero di set di record) mostra il numero di recordset in DNS di Azure per la propria zona DNS. Tutti i recordset definiti nella zona vengono conteggiati. L'unità di misura è il Conteggio e l'aggregazione è il numero massimo di tutti i recordset. Per visualizzare questa metrica, selezionare l'esperienza di esplorazione **Metriche (anteprima)** nella scheda **Monitoraggio** del portale di Azure. Selezionare la zona DNS dall'elenco a discesa **Risorsa**, quindi la metrica **Record Set Count** (Numero di seti di record) e selezionare **Max** come **Aggregazione**. Per altre informazioni sull'esperienza e i grafici di Esplora metriche, vedere [Esplora metriche di Monitoraggio di Azure](../azure-monitor/platform/metrics-charts.md). 

![Numero di set di record](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Figura: metrica Record Set Count (Numero di seti di record) di DNS di Azure*


### <a name="record-set-capacity-utilization"></a>Uso della capacità di set di record
La metrica *Record Set Capacity Utilization* (Uso della capacità di set di record) in DNS di Azure mostra la percentuale di uso della capacità del recordset per una zona DNS. Ogni zona DNS in DNS di Azure è soggetta a un limite di recordset che definisce il numero massimo di recordset consentiti per la zona (vedere [Limiti di DNS](dns-zones-records.md#limits)). Pertanto questa metrica mostra quando si sta per raggiungere il limite del recordset. Ad esempio, se nella la zona DNS sono presenti 500 recordset configurati e il limite di recordset predefinito è 5.000, la metrica RecordSetCapacityUtilization mostrerà il valore del 10%, che viene ottenuto dividendo 500 per 5.000. L'unità di misura è la **percentuale** e il tipo di **aggregazione** è **massima**. Per visualizzare questa metrica, selezionare l'esperienza di esplorazione Metriche (anteprima) nella scheda Monitoraggio del portale di Azure. Selezionare la zona DNS dall'elenco a discesa Risorsa, quindi la metrica Record Set Capacity Utilization (Uso della capacità di set di record) e selezionare Max come Aggregazione. Lo screenshot seguente illustra un esempio. Per altre informazioni sull'esperienza e i grafici di Esplora metriche, vedere [Esplora metriche di Monitoraggio di Azure](../azure-monitor/platform/metrics-charts.md). 

![Numero di set di record](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Figura: metriche Record Set Capacity Utilization (Uso della capacità di set di record) di DNS di Azure*

## <a name="alerts-in-azure-dns"></a>Avvisi in DNS di Azure
Monitoraggio di Azure offre la possibilità di emettere degli avvisi in base ai valori delle metriche disponibili. Le metriche DNS sono disponibili nella nuova esperienza di configurazione degli avvisi. Come descritto in dettaglio nella [documentazione degli avvisi di Monitoraggio di Azure](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md), è possibile selezionare la zona DNS come risorsa, scegliere il tipo di segnale della metrica e configurare la logica di avviso e altri parametri, ad esempio **Periodo** e **Frequenza**. È anche possibile definire un [gruppo di azioni](../azure-monitor/platform/action-groups.md) nel caso in cui venga soddisfatta la condizione di avviso, in base al quale l'avviso verrà inviato tramite le azioni scelte. Per altre informazioni su come configurare gli avvisi per le metriche di Monitoraggio di Azure, vedere [Creare, visualizzare e gestire gli avvisi tramite Monitoraggio di Azure](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). 

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [DNS di Azure](dns-overview.md).
