---
title: Panoramica delle metriche in Microsoft Azure | Microsoft Docs
description: Panoramica delle metriche e dei casi d'uso in Microsoft Azure
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 405ec51c-0946-4ec9-b535-60f65c4a5bd1
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: johnkem
ms.openlocfilehash: 32ca0ce5148ae2641500c98cb04b0d7db1987960
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Panoramica delle metriche in Microsoft Azure
In questo articolo sono descritte le metriche disponibili in Microsoft Azure, i loro vantaggi e come iniziare a usarle.  

## <a name="what-are-metrics"></a>Cosa sono le metriche?
Il monitoraggio di Azure consente di usare la telemetria per ottenere visibilità sulle prestazioni e sull'integrità dei carichi di lavoro in Azure. Il tipo di dati di telemetria Azure più importante è rappresentato dalle metriche (altrimenti dette contatori delle prestazioni) generate dalla maggior parte delle risorse di Azure. Il monitoraggio di Azure offre alcuni modi per configurare e usare queste metriche per il monitoraggio e la risoluzione dei problemi.

## <a name="what-can-you-do-with-metrics"></a>A cosa servono le metriche?
Le metriche sono un'importante fonte di dati di telemetria e consentono di eseguire le attività seguenti:

* **Tenere traccia delle prestazioni** della risorsa (ad esempio, una VM, un sito Web o un'app per la logica) tracciandone le metriche in un grafico del portale da aggiungere a un dashboard.
* **Ricevere la notifica di un problema** che influisce sulle prestazioni della risorsa quando una metrica supera una determinata soglia.
* **Configurare le azioni automatiche**, ad esempio la scalabilità automatica di una risorsa o la generazione di un runbook quando una metrica supera una determinata soglia.
* **Eseguire analisi avanzate** o creare report relativi alle tendenze delle prestazioni o di uso della risorsa.
* **Archiviare** la cronologia relativa alle prestazioni o all'integrità della risorsa a scopi di **conformità/verifica**.

## <a name="what-are-the-characteristics-of-metrics"></a>Quali sono le caratteristiche delle metriche?
Le metriche presentano le caratteristiche seguenti:

* Tutte le metriche hanno **frequenza di un minuto**. L'utente riceve il valore della metrica generata dalla risorsa ogni minuto, così da ottenere una visibilità quasi in tempo reale sullo stato e sull'integrità della risorsa.
* Le metriche sono **disponibili immediatamente**. Non è necessario fornire il consenso esplicito o configurare la diagnostica aggiuntiva.
* Per ogni metrica è possibile accedere alla **cronologia degli ultimi 30 giorni** . È possibile vedere rapidamente le tendenze recenti e mensili riguardo alle prestazioni o all'integrità della risorsa.
* Alcune metriche possono avere attributi di coppia nome-valore denominati **dimensioni**. Queste consentono di segmentare ulteriormente ed esplorare una metrica in modo più significativo.

È anche possibile:

* Configurare una **regola di avviso per la metrica che invia una notifica o esegue un'azione automatica** quando la metrica supera la soglia definita. Il ridimensionamento automatico è un'azione speciale automatica che consente di aumentare le istanze di una risorsa per soddisfare le richieste in entrata o i carichi sul sito Web o sulle risorse di calcolo. È possibile configurare una regola di impostazione del ridimensionamento automatico per aumentare o ridurre le istanze in base a una metrica che supera una soglia.

* **Reindirizzare** tutte le metriche a Application Insights o a Log Analytics (OMS) per abilitare le analisi istantanee, la funzione di ricerca e gli avvisi personalizzati sui dati delle metriche provenienti dalle risorse. È inoltre possibile trasmettere le metriche a un hub eventi, per poi avere la possibilità di reindirizzarle all'analisi di flusso di Azure o alle app personalizzate per effettuare un'analisi quasi in tempo reale. Impostare il flusso di Hub eventi usando le impostazioni di diagnostica.

* **Archiviare le metriche da conservare** per un lungo periodo o usarle per creare report offline. È possibile reindirizzare le metriche all'archiviazione BLOB di Azure quando si configurano le impostazioni di diagnostica per la risorsa.

* Rilevare, accedere e **visualizzare facilmente tutte le metriche** tramite il Portale di Azure quando si seleziona una risorsa e si tracciano le metriche in un grafico.

* **Usare** le metriche mediante le nuove API REST del Monitoraggio di Azure.

* **Eseguire query** sulle metriche usando i cmdlet di PowerShell o l'API REST multi-piattaforma.

  ![Reindirizzamento delle metriche nel monitoraggio di Azure](./media/monitoring-overview-metrics/Metrics_Overview_v4.png)

## <a name="access-metrics-via-the-portal"></a>Accedere alle metriche tramite il portale
Di seguito è descritta una procedura rapida per la creazione di un grafico delle metriche tramite il Portale di Azure.

### <a name="to-view-metrics-after-creating-a-resource"></a>Per visualizzare le metriche dopo aver creato una risorsa
1. Aprire il Portale di Azure.
2. Creare un sito Web per il Servizio app di Azure.
3. Dopo aver creato un sito Web, accedere al pannello **Panoramica** del sito Web.
4. È possibile visualizzare nuove metriche come un riquadro **Monitoraggio**. Successivamente è possibile modificare il riquadro e selezionare altre metriche.

   ![Metriche su una risorsa nel monitoraggio di Azure](./media/monitoring-overview-metrics/MetricsOverview1.png)

### <a name="to-access-all-metrics-in-a-single-place"></a>Per accedere a tutte le metriche in un'unica posizione
1. Aprire il Portale di Azure.
2. Accedere alla scheda **Monitoraggio** e selezionare l'opzione **Metriche**.
3. Selezionare la sottoscrizione, il gruppo di risorse e il nome della risorsa dall'elenco a discesa.
4. Visualizzare l'elenco delle metriche disponibili. Successivamente selezionare la metrica desiderata e tracciarla.
5. È possibile aggiungerla al dashboard facendo clic sul segnaposto nell'angolo superiore destro.

   ![Accedere a tutte le metriche in un'unica posizione nel monitoraggio di Azure](./media/monitoring-overview-metrics/MetricsOverview2.png)

> [!NOTE]
> È possibile accedere alle metriche a livello di host dalle VM (basate su Azure Resource Manager) e dai set di scalabilità di macchine virtuali senza altre eventuali configurazioni di diagnostica. Queste nuove metriche a livello di host sono disponibili per le istanze di Windows e Linux. Queste metriche non vanno confuse con le metriche a livello di sistema operativo guest a cui si accede quando si attiva la Diagnostica di Azure sulle VM o sui set di scalabilità di macchine virtuali. Per altre informazioni sulla configurazione della Diagnostica, vedere [What is Microsoft Azure Diagnostics](../azure-diagnostics.md) (Che cos'è la Diagnostica di Microsoft Azure?).
>
>

Monitoraggio di Azure ha anche una nuova esperienza di creazione di grafici per metriche disponibile in anteprima. Questa esperienza consente agli utenti di sovrapporre delle metriche da più risorse in un grafico. Gli utenti possono anche tracciare, segmentare e filtrare metriche multidimensionali usando questa nuova esperienza di creazione di grafici per metriche. Per altre informazioni, [fare clic qui](https://aka.ms/azuremonitor/new-metrics-charts)

## <a name="access-metrics-via-the-rest-api"></a>Accedere alle metriche mediante l'API REST
È possibile accedere alle metriche di Azure tramite le API del Monitoraggio di Azure. Sono due le API che consentono di rilevare le metriche e accedervi:

* Usare l'[API REST relativa alle definizione delle metriche di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) per accedere all'elenco di metriche, ed eventuali dimensioni, disponibili per un servizio.
* Usare l'[API REST delle metriche di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/metrics) per segmentare, filtrare e accedere ai dati delle metriche effettive.

> [!NOTE]
> Questo articolo è dedicato alle metriche accessibili tramite la [nuova API per le metriche](https://docs.microsoft.com/rest/api/monitor/) per le risorse di Azure. La versione della nuova API per le definizioni delle metriche e l'API per le metriche è 2017-05-01-preview. È possibile accedere alle metriche e alle definizioni delle metriche legacy con la versione API 2014-04-01.
>
>

Per una procedura ancor più dettagliata con le API REST del Monitoraggio di Azure, vedere [Procedura dettagliata sull'API REST del Monitoraggio di Azure](monitoring-rest-api-walkthrough.md).

## <a name="export-metrics"></a>Esportare le metriche
È possibile accedere al pannello **Log di diagnostica** nella scheda **Monitoraggio** e visualizzare le opzioni di esportazione delle metriche. È possibile selezionare le metriche (e i log di diagnostica) da reindirizzare nell'archiviazione BLOB, negli Hub eventi di Azure o in OMS per i casi d'uso descritti in precedenza in questo articolo.

 ![Opzioni di esportazione per le metriche nel monitoraggio di Azure](./media/monitoring-overview-metrics/MetricsOverview3.png)

È possibile configurare questa opzione tramite i modelli di Resource Manager, [PowerShell](insights-powershell-samples.md), l'[interfaccia della riga di comando di Azure](insights-cli-samples.md) o l'[API REST](https://msdn.microsoft.com/library/dn931943.aspx).

## <a name="take-action-on-metrics"></a>Eseguire operazioni sulle metriche
Per ricevere notifiche o eseguire azioni automatizzate su dati delle metriche, è possibile configurare regole di avviso o impostazioni di ridimensionamento automatico.

### <a name="configure-alert-rules"></a>Configurare regole di avviso
È possibile configurare le regole di avviso sulle metriche. Queste regole di avviso possono verificare se una metrica ha superato una determinata soglia. Esistono due possibilità di avvisi metrica offerte da Monitoraggio di Azure.

Avvisi metrica: possono quindi comunicare tramite e-mail o generare un webhook che può essere usato per eseguire qualsiasi script personalizzato. È anche possibile usare il webhook per configurare le integrazioni dei prodotti di terze parti.

 ![Metriche e regole di avviso nel monitoraggio di Azure](./media/monitoring-overview-metrics/MetricsOverview4.png)

Avvisi metrica quasi in tempo reale (anteprima): possono monitorare più metriche e soglie per una risorsa, quindi comunicare tramite un [gruppo di azioni](/monitoring-action-groups.md). Altre informazioni sugli [avvisi metrica quasi in tempo reale](https://aka.ms/azuremonitor/near-real-time-alerts).


### <a name="autoscale-your-azure-resources"></a>Ridimensionare automaticamente le risorse di Azure
Alcune risorse di Azure supportano l'aumento o la riduzione di più istanze per gestire i carichi di lavoro. Il ridimensionamento automatico si applica ai servizi app (App Web), ai set di scalabilità di macchine virtuali e ai Servizi cloud di Azure. È possibile configurare le regole di ridimensionamento automatico in modo da ridurre o aumentare il numero di istanze quando una data metrica che influisce sul carico di lavoro supera una soglia specificata. Per ulteriori informazioni, vedere [Panoramica della scalabilità automatica](monitoring-overview-autoscale.md).

 ![Metriche e ridimensionamento automatico nel Monitoraggio di Azure](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="learn-about-supported-services-and-metrics"></a>Altre informazioni su metriche e servizi supportati
È possibile visualizzare un elenco dettagliato di tutti i servizi supportati e delle relative metriche in [Metriche del Monitoraggio di Azure: metriche supportate per tipo di risorsa](monitoring-supported-metrics.md).

## <a name="next-steps"></a>Passaggi successivi
Fare riferimento ai collegamenti forniti all'interno dell'articolo. In più, è possibile ottenere informazioni su:  

* [Metriche comuni per il ridimensionamento automatico](insights-autoscale-common-metrics.md)
* [Come creare regole di avviso](insights-alerts-portal.md)
* [Analizzare i log di Archiviazione di Azure con Log Analytics](../log-analytics/log-analytics-azure-storage.md)
