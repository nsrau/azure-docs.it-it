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
ms.openlocfilehash: 22c7243cd966f458610c2abc67ed5540b37357b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658816"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Monitoraggio dell'utilizzo e dei costi stimati in Monitoraggio di Azure

> [!NOTE]
> Questo articolo descrive come visualizzare l'utilizzo e i costi stimati in più funzionalità di monitoraggio di Azure.This article, describes how to view usage and estimated costs across multiple Azure monitoring features. Gli articoli correlati per componenti specifici di Monitoraggio di Azure includono:Related articles for specific components of Azure Monitor include:
> - [Manage usage and costs with Azure Monitor Logs](manage-cost-storage.md) descrive come controllare i costi modificando il periodo di conservazione dei dati e come analizzare e avvisare sull'utilizzo dei dati.
> - [Manage usage and costs for Application Insights](../../azure-monitor/app/pricing.md) descrive come analizzare l'utilizzo dei dati in Application Insights.Manage usage and costs for Application Insights describes how to analyze data usage in Application Insights.

## <a name="azure-monitor-pricing-model"></a>Modello di determinazione dei prezzi di Monitoraggio di AzureAzure Monitor pricing model

Il modello di fatturazione di base di Azure Monitor è un prezzo basato sul consumo basato sul cloud ("Pay-As-You-Go"). Si paga solo per le risorse utilizzate. Sono disponibili dettagli sui prezzi per [avvisi, metriche, notifiche,](https://azure.microsoft.com/pricing/details/monitor/) [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) e [Application Insights.](https://azure.microsoft.com/pricing/details/application-insights/) 

Oltre al modello con pagamento in base al numero di pagamenti per i dati di log, Log Analytics dispone di prenotazioni di capacità, che consentono di risparmiare fino al 25% rispetto al prezzo con pagamento in base al calo. Il prezzo di prenotazione della capacità consente di acquistare una prenotazione a partire da 100 GB/giorno. Qualsiasi utilizzo superiore al livello di prenotazione verrà fatturato alla tariffa con pagamento in base al consumo. [Ulteriori informazioni](https://azure.microsoft.com/pricing/details/monitor/) sui prezzi di prenotazione della capacità.

Alcuni clienti avranno accesso [ai piani tariffari](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers) di Log Analytics legacy e al [piano tariffario di Enterprise Application Insights legacy.](https://docs.microsoft.com/azure/azure-monitor/app/pricing#legacy-enterprise-per-node-pricing-tier) 

## <a name="understanding-your-azure-monitor-costs"></a>Informazioni sui costi di Monitoraggio di AzureUnderstanding your Azure Monitor costs

Ci sono due fasi per comprendere i costi. Il primo è quando si considera Monitoraggio di Azure come soluzione di monitoraggio. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>Stima dei costi per la gestione dell'ambiente

Se non si usano ancora i log di Monitoraggio di Azure, è possibile usare il [calcolatore](https://azure.microsoft.com/pricing/calculator/?service=monitor) dei prezzi di Monitoraggio di Azure per stimare il costo dell'uso di Monitoraggio di Azure.If you're not yet using Azure Monitor Logs, you can use the Azure Monitor pricing calculator to estimate the cost of using Azure Monitor. Iniziare immettendo "Monitor di Azure" nella casella di ricerca e facendo clic sul riquadro di Monitoraggio di Azure risultante. Scorrere la pagina verso il basso fino ad Monitoraggio di Azure e selezionare una delle opzioni dall'elenco a discesa Tipo:

- Query e avvisi delle metriche  
- Log Analytics
- Application Insights

In ognuno di questi, il calcolatore dei prezzi vi aiuterà a stimare i costi probabili in base all'utilizzo previsto.

Ad esempio, con Log Analytics è possibile immettere il numero di macchine virtuali e il GB di dati che si prevede di raccogliere da ogni macchina virtuale. In genere, da 1 GB a 3 GB di mese di dati viene esitato da una tipica macchina virtuale di Azure.Typically 1 GB to 3 GB of data month is inged from a typical Azure VM. Se si sta già valutando i log di Monitoraggio di Azure, è possibile usare le statistiche dei dati dal proprio ambiente. Vedere di seguito per determinare il [numero di macchine virtuali monitorate](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) e il volume di dati in cui [l'area di lavoro sta ingerendo.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)

Analogamente per Application Insights, se si abilita la funzionalità "Stima del volume di dati in base all'attività dell'applicazione", è possibile fornire input sull'applicazione (richieste al mese e visualizzazioni di pagina al mese, nel caso in cui si raccolga dati di telemetria lato client), e poi la calcolatrice vi dirà la quantità mediana e 90esimo percentile di dati raccolti da applicazioni simili. Queste applicazioni comprendono la gamma di configurazione di Application Insights (ad esempio alcune hanno il campionamento predefinito, altre non hanno campionamento e così via), quindi hai ancora il controllo per ridurre il volume di dati che inserisci molto al di sotto del livello mediano usando il campionamento. Ma questo è un punto di partenza per capire cosa stanno vedendo altri clienti simili. [Altre informazioni](https://docs.microsoft.com/azure/azure-monitor/app/pricing#estimating-the-costs-to-manage-your-application) sulla stima dei costi per Application Insights.Learn more about estimating costs for Application Insights.

### <a name="understanding-your-usage-and-estimated-costs"></a>Informazioni sull'utilizzo e sui costi stimati

È importante comprendere e tenere traccia dell'utilizzo una volta tramite Monitoraggio di Azure e sono disponibili un set completo di strumenti per semplificare questa operazione. 

Azure offre una grande quantità di funzionalità utili nell'hub [Gestione costi di Azure e fatturazione.](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Dopo aver aperto l'hub **Gestione costi di Azure - Fatturazione,** fare clic su Gestione **costi** e selezionare [l'ambito](https://docs.microsoft.com/azure/cost-management/understand-work-scopes) (il set di risorse da analizzare). 

Quindi, per visualizzare i costi di Monitoraggio di Azure per gli ultimi 30 giorni, fare clic sul riquadro **Costi giornalieri,** scegliere "Ultimi 30 giorni" in Date relative e aggiungere un filtro che seleziona i nomi dei servizi:Then to see the Azure Monitor costs for the last 30 days, click on the Daily Costs tile, choose "Last 30 days" under Relative dates, and add a filter which selects the Service names:

1. Monitoraggio di Azure
2. Application Insights
3. Log Analytics
4. Informazioni dettagliate e analisi

Ciò si traduce in una visualizzazione come:

![Screenshot di Gestione dei costi di AzureAzure Cost Management screenshot](./media/usage-estimated-costs/010.png)

Da qui, è possibile eseguire il drill-in da questo riepilogo dei costi accumulato per ottenere i dettagli più fini nella visualizzazione "Costo per risorsa". Nei piani tariffari correnti, i dati del log di Azure vengono addebitati sullo stesso set di metri indipendentemente dal fatto che abbiano origine da Log Analytics o Application Insights. Per separare i costi dall'utilizzo di Log Analytics o Application Insights, è possibile aggiungere un filtro in **Tipo di risorsa.** Per visualizzare tutti i costi di Application Insights, filtrare il tipo di risorsa in "microsoft.insights/components" e per i costi di Log Analytics, filtrare Tipo di risorsa in "microsoft.operationalinsights/workspaces". 

Sono disponibili ulteriori dettagli sull'utilizzo [scaricandolo dal portale](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)di Azure. Nel foglio di calcolo scaricato è possibile visualizzare l'utilizzo per risorsa di Azure al giorno. In questo foglio di calcolo di Excel, l'utilizzo delle risorse di Application Insights è reperibile filtrando prima la colonna "Meter Category" per visualizzare "Application Insights" e "Log Analytics" e quindi aggiungendo un filtro nella colonna "Instance ID" che è "contains microsoft.insights/components".  La maggior parte dell'utilizzo di Application Insights viene segnalata nei contatori con la categoria di contatori di Log Analytics, poiché è presente un singolo back-end di log per tutti i componenti di Monitoraggio di Azure.Most Application Insights usage is reported on meters with the Meter Category of Log Analytics, since there is a single logs backend for all Azure Monitor components.  Solo le risorse di Application Insights nei piani tariffari legacy e nei test Web in più passaggi vengono segnalate con una categoria di contatori di Application Insights.Only Application Insights resources on legacy pricing tiers and multi-step web tests are reported with a Meter Category of Application Insights.  L'utilizzo viene visualizzato nella colonna "Quantità consumata" e l'unità per ogni voce viene visualizzata nella colonna "Unità di misura".  Sono disponibili ulteriori dettagli per comprendere la fattura di [Microsoft Azure.](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) 

> [!NOTE]
> L'approccio preferito per comprendere in modo ampio i costi di **gestione dei costi** nell'hub Gestione costi di Azure e **fatturazione** è l'approccio preferito per comprendere in modo ampio i costi di monitoraggio.  Le esperienze **di utilizzo e costi stimati** per Log Analytics e Application Insights forniscono informazioni più dettagliate per ognuna di queste parti di Monitoraggio di Azure.The Usage and Estimated Costs experiences for Log [Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs) and [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/pricing#understand-your-usage-and-estimate-costs) provide deeper insights for each of those parts of Azure Monitor.

Un'altra opzione per visualizzare l'utilizzo di Monitoraggio di Azure è la pagina **Utilizzo e costi stimati** nell'hub Monitor.Another option for viewing your Azure Monitor usage is the Usage and estimated costs page in the Monitor hub. Viene illustrato l'utilizzo di funzionalità di monitoraggio di base, ad esempio [avvisi, metriche, notifiche,](https://azure.microsoft.com/pricing/details/monitor/) [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)e Azure Application [Insights.](https://azure.microsoft.com/pricing/details/application-insights/) Per i clienti con piani tariffari disponibili prima di aprile 2018, è incluso anche l'utilizzo di Log Analytics acquistato tramite l'offerta Insights e Analytics.

In questa pagina gli utenti possono visualizzare l'utilizzo delle relative risorse per gli ultimi 31 giorni, aggregate per sottoscrizione. `Drill-ins`tendenze di utilizzo nel periodo di 31 giorni. Per effettuare questa stima, è necessario assemblare una grande quantità di dati. Attendere con pazienza il caricamento della pagina.

Questo esempio mostra il monitoraggio dell'utilizzo, con una stima dei costi risultanti:

![Schermata del portale Utilizzo e costi stimati](./media/usage-estimated-costs/001.png)

Selezionare il collegamento nella colonna relativa all'utilizzo mensile per aprire un grafico che mostra le tendenze di utilizzo negli ultimi 31 giorni: 

![Schermata con grafico a barre Inclusi per nodo](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Diritti di sottoscrizione di Operations Management Suite

I clienti che hanno acquistato Microsoft Operations Management Suite E1 ed E2 possono usufruire dei diritti di inserimento dati per nodo per [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) e [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing). Per ricevere questi diritti per le aree di lavoro di Log Analytics o le risorse di Application Insights in una determinata sottoscrizione: 

- Le aree di lavoro di Log Analytics devono usare il piano tariffario "Per nodo (OMS)".
- Le risorse di Application Insights devono usare il piano tariffario "Enterprise".

A seconda del numero di nodi della suite acquistata dall'organizzazione, lo spostamento di alcune sottoscrizioni in un piano tariffario Con pagamento in base al consumo (Per GB) potrebbe essere vantaggioso, ma ciò richiede un'attenta considerazione.

> [!WARNING]
> Se l'organizzazione dispone di Microsoft Operations Management Suite E1 ed E2 correnti, è in genere consigliabile mantenere le aree di lavoro di Log Analytics nel piano tariffario "Per nodo (OMS)" e le risorse di Application Insights nel piano tariffario "Enterprise". 
>
