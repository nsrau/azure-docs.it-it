---
title: Monitoraggio dell'utilizzo e dei costi stimati in Monitoraggio di Azure
description: Panoramica dell'uso della pagina Utilizzo e costi stimati di Monitoraggio di Azure
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: eb96537f67c61fb31759da020068f784d0e89993
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87323401"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Monitoraggio dell'utilizzo e dei costi stimati in Monitoraggio di Azure

> [!NOTE]
> Questo articolo descrive come visualizzare l'utilizzo e i costi stimati tra più funzionalità di monitoraggio di Azure. Gli articoli correlati per i componenti specifici di monitoraggio di Azure includono:
> - [Gestire l'utilizzo e i costi con i log di monitoraggio di Azure](manage-cost-storage.md) descrive come controllare i costi modificando il periodo di conservazione dei dati e come analizzare e inviare avvisi sull'utilizzo dei dati.
> - [Gestire l'utilizzo e i costi per Application Insights](../app/pricing.md) descrive come analizzare l'utilizzo dei dati in Application Insights.

## <a name="azure-monitor-pricing-model"></a>Modello di determinazione dei prezzi di monitoraggio di Azure

Il modello di fatturazione di base di monitoraggio di Azure è un piano tariffario basato sul consumo (con pagamento in base al consumo). Si paga solo per le risorse usate. I dettagli relativi ai prezzi sono disponibili per [avvisi, metriche, notifiche](https://azure.microsoft.com/pricing/details/monitor/), [log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) e [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Oltre al modello con pagamento in base al consumo per i dati di log, Log Analytics dispone di prenotazioni di capacità, che consentono di risparmiare fino al 25% rispetto al prezzo con pagamento in base al consumo. I prezzi per la prenotazione della capacità consentono di acquistare una prenotazione a partire da 100 GB/giorno. Qualsiasi utilizzo sopra il livello di prenotazione verrà fatturato in base alla tariffa con pagamento in base al consumo. [Altre](https://azure.microsoft.com/pricing/details/monitor/) informazioni sui prezzi di prenotazione della capacità.

Alcuni clienti avranno accesso ai [piani tariffari log Analytics legacy](./manage-cost-storage.md#legacy-pricing-tiers) e al piano [tariffario Enterprise Application Insights legacy](../app/pricing.md#legacy-enterprise-per-node-pricing-tier). 

## <a name="understanding-your-azure-monitor-costs"></a>Informazioni sui costi di monitoraggio di Azure

Esistono due fasi per comprendere i costi. Il primo consiste nel prendere in considerazione monitoraggio di Azure come soluzione di monitoraggio. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>Stima dei costi per la gestione dell'ambiente

Se non si usano ancora i log di monitoraggio di Azure, è possibile usare il [calcolatore dei prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/calculator/?service=monitor) per stimare il costo dell'uso di monitoraggio di Azure. Per iniziare, immettere "Monitoraggio di Azure" nella casella di ricerca e fare clic sul riquadro Monitoraggio di Azure che viene visualizzato. Scorrere la pagina verso il basso fino a monitoraggio di Azure e selezionare una delle opzioni dall'elenco a discesa Tipo:

- Query e avvisi relativi alle metriche  
- Log Analytics
- Application Insights

In ognuno di questi, il calcolatore dei prezzi consente di stimare i costi probabili in base all'utilizzo previsto.

Ad esempio, con Log Analytics è possibile immettere il numero di macchine virtuali e i GB di dati che si prevede di raccogliere da ogni macchina virtuale. In genere da 1 GB a 3 GB di dati mensili viene inserito da una tipica macchina virtuale di Azure. Se si stanno già valutando i log di Monitoraggio di Azure, è possibile usare le statistiche dei dati del proprio ambiente. Vedere di seguito per informazioni su come determinare il [numero di macchine virtuali monitorate](./manage-cost-storage.md#understanding-nodes-sending-data) e il [volume di dati che l'area di lavoro sta inserendo](./manage-cost-storage.md#understanding-ingested-data-volume).

Analogamente, per Application Insights, se si Abilita la funzionalità "stimare il volume di dati in base all'attività dell'applicazione", è possibile fornire input sull'applicazione (richieste al mese e visualizzazioni di pagina al mese, nel caso in cui vengano raccolti dati di telemetria lato client), quindi il calcolatore indica la quantità mediana e 90 ° di dati raccolti da applicazioni simili. Queste applicazioni coprono l'intero spettro di configurazione di Application Insights (ad esempio, alcune hanno il campionamento predefinito, altre sono sprovviste di campionamento e così via), quindi è comunque possibile ridurre il volume di dati inseriti al di sotto del livello mediano usando il campionamento. Si tratta tuttavia di un punto di partenza per capire cosa accade altri clienti simili. [Altre](../app/pricing.md#estimating-the-costs-to-manage-your-application) informazioni su come stimare i costi per Application Insights.

### <a name="understanding-your-usage-and-estimated-costs"></a>Informazioni sull'utilizzo e sui costi stimati

È importante comprendere e tenere traccia dell'utilizzo una volta con monitoraggio di Azure e sono disponibili numerosi strumenti per semplificare questa operazione. 

Azure fornisce una grande quantità di funzionalità utili nell'hub [Gestione dei costi e fatturazione](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json) di Azure. Dopo aver aperto **Gestione costi di Azure** e l'hub fatturazione, fare clic su **Gestione costi** e selezionare l' [ambito](../../cost-management-billing/costs/understand-work-scopes.md) (il set di risorse da analizzare). 

Per visualizzare i costi di monitoraggio di Azure per gli ultimi 30 giorni, fare clic sul riquadro **costi giornalieri** , scegliere "ultimi 30 giorni" in date relative e aggiungere un filtro che consente di selezionare i nomi dei servizi:

1. Monitoraggio di Azure
2. Application Insights
3. Log Analytics
4. Informazioni dettagliate e analisi

In questo modo si ottiene una visualizzazione simile alla seguente:

![Screenshot di gestione costi di Azure](./media/usage-estimated-costs/010.png)

Da qui è possibile eseguire il drill-through da questo riepilogo dei costi accumulati per ottenere i dettagli più precisi nella visualizzazione "costo per risorsa". Nei piani tariffari correnti, i dati dei log di Azure vengono addebitati sullo stesso set di contatori, indipendentemente dal fatto che abbia origine da Log Analytics o Application Insights. Per separare i costi dal Log Analytics o Application Insights utilizzo, è possibile aggiungere un filtro per il **tipo di risorsa**. Per visualizzare tutti i costi di Application Insights, filtrare il tipo di risorsa in "Microsoft. Insights/Components" e, per Log Analytics i costi, filtrare il tipo di risorsa in "Microsoft. operationalinsights/Workspaces". 

Per informazioni dettagliate sull'utilizzo, [scaricare l'utilizzo dal portale di Azure](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). Nel foglio di calcolo scaricato è possibile visualizzare l'utilizzo per ogni risorsa di Azure al giorno. In questo foglio di calcolo di Excel, l'utilizzo delle risorse di Application Insights è disponibile filtrando prima di tutto la colonna "Categoria del contatore" per visualizzare "Application Insights" e "Log Analytics", quindi aggiungendo un filtro alla colonna "ID istanza" che risulta "contiene microsoft.insights/components".  La maggior parte dell'utilizzo di Application Insights viene segnalata nei contatori con la categoria Contatore di Log Analytics, dal momento che è presente un singolo back-end per tutti i componenti di Monitoraggio di Azure.  Solo le risorse Application Insights sui piani tariffari legacy e i test Web in più passaggi vengono riportate con una categoria Contatore di Application Insights.  L'utilizzo viene visualizzato nella colonna "Quantità utilizzata" e l'unità per ogni voce viene visualizzata nella colonna "Unità di misura".  Maggiori dettagli sono disponibili nelle [informazioni sulla fattura di Microsoft Azure](../../cost-management-billing/understand/review-individual-bill.md). 

> [!NOTE]
> L'uso della **gestione dei costi** in **Gestione costi di Azure** e nell'hub di fatturazione è l'approccio preferito per comprendere in modo esteso i costi di monitoraggio.  Le esperienze di **utilizzo e i costi stimati** per [log Analytics](./manage-cost-storage.md#understand-your-usage-and-estimate-costs)  e [Application Insights](../app/pricing.md#understand-your-usage-and-estimate-costs) forniscono informazioni approfondite per ognuna di queste parti di monitoraggio di Azure.

Un'altra opzione per visualizzare l'utilizzo di monitoraggio di Azure è la pagina **utilizzo e costi stimati** nell'hub monitoraggio. Viene illustrato l'utilizzo delle funzionalità di monitoraggio di base, ad esempio [avvisi, metriche, notifiche](https://azure.microsoft.com/pricing/details/monitor/), [Azure log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)e [applicazione Azure Insights](https://azure.microsoft.com/pricing/details/application-insights/). Per i clienti con piani tariffari disponibili prima di aprile 2018, è incluso anche l'utilizzo di Log Analytics acquistato tramite l'offerta Insights e Analytics.

In questa pagina gli utenti possono visualizzare l'utilizzo delle relative risorse per gli ultimi 31 giorni, aggregate per sottoscrizione. `Drill-ins` Mostra le tendenze di utilizzo nel periodo di 31 giorni. Per effettuare questa stima, è necessario assemblare una grande quantità di dati. Attendere con pazienza il caricamento della pagina.

Questo esempio mostra il monitoraggio dell'utilizzo, con una stima dei costi risultanti:

![Schermata del portale Utilizzo e costi stimati](./media/usage-estimated-costs/001.png)

Selezionare il collegamento nella colonna relativa all'utilizzo mensile per aprire un grafico che mostra le tendenze di utilizzo negli ultimi 31 giorni: 

![Schermata con grafico a barre Inclusi per nodo](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Diritti della sottoscrizione di Operations Management Suite

I clienti che hanno acquistato Microsoft Operations Management Suite E1 ed E2 possono usufruire dei diritti di inserimento dati per nodo per [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) e [Application Insights](../app/pricing.md). Per ricevere questi diritti per le aree di lavoro di Log Analytics o le risorse di Application Insights in una determinata sottoscrizione: 

- Le aree di lavoro di Log Analytics devono usare il piano tariffario "Per nodo (OMS)".
- Application Insights risorse deve usare il piano tariffario "Enterprise".

A seconda del numero di nodi della Suite acquistata dall'organizzazione, il trasferimento di alcune sottoscrizioni in un piano tariffario con pagamento in base al consumo (per GB) potrebbe essere vantaggioso, ma ciò richiede un'attenta considerazione.

> [!WARNING]
> Se l'organizzazione ha Microsoft Operations Management Suite E1 ed E2 correnti, in genere è consigliabile lasciare le aree di lavoro Log Analytics nel piano tariffario "per nodo (OMS)" e le risorse Application Insights nel piano tariffario "Enterprise". 
>

