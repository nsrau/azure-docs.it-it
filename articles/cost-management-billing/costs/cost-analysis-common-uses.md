---
title: Utilizzi dell'analisi dei costi comune in Gestione costi di Azure
description: Questo articolo illustra come ottenere risultati per le attività di analisi dei costi comune in Gestione costi di Azure.
author: bandersmsft
ms.author: banders
ms.date: 05/27/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 130d313c1ca549f3a4e6f1ec1bbac2a16a753709
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142511"
---
# <a name="common-cost-analysis-uses"></a>Utilizzi dell'analisi dei costi comune

Gli utenti di Gestione costi di Azure spesso desiderano rispondere alle domande poste da molti altri utenti. Questo articolo illustra come ottenere risultati per le attività di analisi dei costi comune in Gestione costi.

## <a name="view-forecasted-costs"></a>Visualizzare i costi previsti

I costi previsti vengono visualizzati nelle aree di analisi dei costi per le visualizzazioni di area e istogramma in pila. La previsione è basata sull'utilizzo delle risorse cronologiche. Le modifiche apportate all'uso delle risorse influiscono sui costi previsti.

Nel portale di Azure passare all'analisi dei costi per l'ambito di interesse. Ad esempio: **Gestione dei costi e fatturazione** > **Gestione dei costi** > **Analisi dei costi**.

Nella visualizzazione predefinita il grafico in alto presenta le sezioni costo effettivo/ammortizzato e costo previsto. Il colore a tinta unita del grafico indica il costo effettivo/ammortizzato. Il colore ombreggiato mostra il costo previsto.

[![Costo stimato](./media/cost-analysis-common-uses/enrollment-forecast.png)](./media/cost-analysis-common-uses/enrollment-forecast.png#lightbox)

## <a name="view-forecasted-costs-grouped-by-service"></a>Visualizzare i costi previsti raggruppati per servizio

La visualizzazione predefinita non mostra il gruppo di costi previsti per un servizio, quindi è necessario aggiungere un gruppo tramite selezione.

Nel portale di Azure passare all'analisi dei costi per l'ambito di interesse. Ad esempio: **Gestione dei costi e fatturazione** > **Gestione dei costi** > **Analisi dei costi**.

Selezionare **Raggruppa per** > **Nome servizio**.

La visualizzazione mostra i costi raggruppati per ogni servizio. Il costo previsto non viene calcolato per ogni servizio. Viene previsto per il **Totale** di tutti i servizi.

[![Costo previsto raggruppato](./media/cost-analysis-common-uses/forecast-group-by-service.png)](./media/cost-analysis-common-uses/forecast-group-by-service.png#lightbox)

## <a name="view-forecasted-costs-for-a-service"></a>Visualizzare i costi previsti per un servizio

È possibile visualizzare i costi previsti limitati a un singolo servizio. È ad esempio possibile volere visualizzare i costi previsti solo per le macchine virtuali.

1. Nel portale di Azure passare all'analisi dei costi per l'ambito di interesse. Ad esempio: **Gestione dei costi e fatturazione** > **Gestione dei costi** > **Analisi dei costi**.
1. Selezionare **Aggiungi filtro** quindi selezionare **Nome servizio**.
1. Nell'elenco di **scelta** selezionare un servizio. Ad esempio, selezionare **macchine virtuali**.

Verificare il costo effettivo per la selezione e il costo previsto.

È possibile aggiungere altre personalizzazioni alla visualizzazione.

1. Aggiungere un secondo filtro per **Contatore** e selezionare un valore da filtrare per un singolo tipo di contatore sotto il nome del servizio selezionato.
1. Raggruppare per **Risorsa** per visualizzare le risorse specifiche che stanno determinando un incremento dei costi. Il costo previsto non viene calcolato per ogni servizio. Viene previsto per il **Totale** di tutte le risorse.

[![Costo previsto per un servizio](./media/cost-analysis-common-uses/forecast-by-service.png)](./media/cost-analysis-common-uses/forecast-by-service.png#lightbox)

## <a name="view-your-azure-and-aws-costs-together"></a>Visualizzare i costi di Azure e AWS insieme  

Per visualizzare i costi di Azure e AWS insieme, è possibile usare gli ambiti del gruppo di gestione in Azure.

1. Creare un gruppo di gestione o selezionarne uno esistente.
1. Assegnare le sottoscrizioni di Azure esistenti desiderate al gruppo di gestione.
1. Assegnare lo *stesso* gruppo di gestione all'account collegato del connettore.
1. Passare all'analisi dei costi e selezionare **Costi accumulati**.
1. Selezionare **Raggruppa per** - **Provider**.

## <a name="view-cost-breakdown-by-azure-service"></a>Visualizzare la suddivisione dei costi per servizio di Azure

La visualizzazione dei costi da parte di un servizio di Azure può aiutare a comprendere meglio le parti più costose dell'infrastruttura. Ad esempio, i costi di calcolo della macchina virtuale potrebbero essere ridotti. Tuttavia, è possibile che si accumulino costi di rete significativi a causa della quantità di informazioni emesse dalle macchine virtuali. Comprendere i principali fattori di costo dei servizi di Azure è essenziale per poter modificare l'utilizzo del servizio, in base alle esigenze.

1. Nel portale di Azure passare all'analisi dei costi per l'ambito di interesse. Ad esempio: **Gestione dei costi e fatturazione** > **Gestione dei costi** > **Analisi dei costi**.
1. Selezionare **Costo per servizio** e quindi raggruppare per **Livello di servizio**.
1. Modificare la visualizzazione in **Tabella**.

[![Ripartizione dei costi per servizio di Azure](./media/cost-analysis-common-uses/breakdown-by-service.png)](./media/cost-analysis-common-uses/breakdown-by-service.png#lightbox)

## <a name="review-invoiced-charges-in-cost-analysis"></a>Esaminare le tariffe fatturate in Analisi dei costi

Per visualizzare i dettagli della fattura nel portale di Azure, passare ad Analisi dei costi per l'ambito associato alla fattura che si sta analizzando. Selezionare la visualizzazione **Dettagli della fattura**, in cui vengono mostrati gli addebiti indicati nella fattura.

[![Esempio che mostra i dettagli della fattura](./media/cost-analysis-common-uses/invoice-details.png)](./media/cost-analysis-common-uses/invoice-details.png#lightbox)

La visualizzazione dei dettagli della fattura consente di identificare il servizio con costi imprevisti e determinare le risorse direttamente associate alla risorsa in Analisi dei costi. Se, ad esempio, si vogliono analizzare gli addebiti per il servizio Macchine virtuali, passare alla visualizzazione **Costi accumulati**. Impostare quindi la granularità su **Giornaliera**, filtrare gli addebiti per **Nome del servizio: Macchine virtuali** e raggruppare gli addebiti per **Risorsa**.

[![Esempio che mostra i costi accumulati per Macchine virtuali](./media/cost-analysis-common-uses/virtual-machines.png)](./media/cost-analysis-common-uses/virtual-machines.png#lightbox)

## <a name="view-cost-breakdown-by-azure-resource"></a>Visualizzare la ripartizione dei costi per servizio di Azure

I servizi sono compilati con le risorse di Azure. La revisione dei costi in base alle risorse consente di identificare rapidamente i collaboratori del costo primario. Se un servizio dispone di risorse troppo onerose, provare ad apportare modifiche per ridurre i costi.

1. Nel portale di Azure passare all'analisi dei costi per l'ambito di interesse. Ad esempio: **Gestione dei costi e fatturazione** > **Gestione dei costi** > **Analisi dei costi**.
1. Selezionare **Costo per risorsa**.
1. Modificare la visualizzazione in **Tabella**.

[![Visualizzare la ripartizione dei costi per risorsa di Azure](./media/cost-analysis-common-uses/cost-by-resource.png)](./media/cost-analysis-common-uses/cost-by-resource.png#lightbox)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Visualizzare la ripartizione dei costi in base alle dimensioni selezionate

Le dimensioni consentono di organizzare i costi in base ai diversi valori dei metadati indicati negli addebiti. È ad esempio possibile raggruppare i costi in base alla località.

1. Nel portale di Azure passare all'analisi dei costi per l'ambito di interesse. Ad esempio: **Gestione dei costi e fatturazione** > **Gestione dei costi** > **Analisi dei costi**.
1. Selezionare il filtro **Raggruppa per**.  
    [![Selezionare un elemento da Raggruppa per](./media/cost-analysis-common-uses/group-by.png)](./media/cost-analysis-common-uses/group-by.png#lightbox)
1. Facoltativamente, è possibile salvare la vista per un uso successivo.
1. Fare clic su un grafico a torta sotto al grafico per visualizzare dati più dettagliati.  
    [![Visualizzare la ripartizione dei costi in base alle dimensioni selezionate](./media/cost-analysis-common-uses/drill-down.png)](./media/cost-analysis-common-uses/drill-down.png#lightbox)

## <a name="view-costs-per-day-or-by-month"></a>Visualizzare i costi al giorno o al mese

Esaminare i costi giornalieri e mensili può essere utile per comprendere meglio se è presente un'ora della settimana o dell'anno in cui i costi sono maggiori. Se si registra più traffico di clienti in un periodo festivo, questo comporta un aumento corrispondente dei costi di Azure? Venerdì è un giorno più costoso del lunedì?

1. Nel portale di Azure passare all'analisi dei costi per l'ambito di interesse. Ad esempio: **Gestione dei costi e fatturazione** > **Gestione dei costi** > **Analisi dei costi**.
1. Impostare la **Granularità** su **Mensile** o **Giornaliera**.

[![Visualizzare i costi al giorno](./media/cost-analysis-common-uses/daily-granularity.png)](./media/cost-analysis-common-uses/daily-granularity.png#lightbox)


## <a name="view-your-spot-vm-charges"></a>Visualizzare gli addebiti per le VM spot

Le VM spot possono offrire risparmi sui costi significativi per i carichi di lavoro in grado di gestire le interruzioni. I carichi di lavoro vengono eseguiti in una capacità di Azure inutilizzata. Poiché possono essere eliminati in qualsiasi momento, le VM spot garantiscono uno sconto significativo. Per visualizzare gli addebiti per le VM spot, procedere come segue.

1. Nel portale di Azure passare all'analisi dei costi per l'ambito di interesse. Ad esempio, **Gestione dei costi e fatturazione** > **Gestione dei costi** > **Analisi dei costi**.
2. Aggiungere un filtro per **Modello di determinazione prezzi: Spot**.

![Esempio che mostra il filtro per le VM spot](./media/cost-analysis-common-uses/spot-vm-filter.png)

La dimensione Modello di determinazione prezzi viene usata anche per visualizzare gli addebiti su richiesta e delle prenotazioni.

## <a name="view-your-reservation-charges"></a>Visualizzare gli addebiti per la prenotazione

Le istanze riservate offrono un modo per risparmiare denaro con Azure. Con le prenotazioni è possibile acquistare in anticipo un determinato numero di risorse nel tempo. L'analisi dei costi mostra gli addebiti visualizzati nella fattura. Gli addebiti vengono visualizzati come costi effettivi o ammortizzati nel corso del periodo di prenotazione.

1. Nel portale di Azure passare all'analisi dei costi per l'ambito di interesse. Ad esempio, **Gestione dei costi e fatturazione** > **Gestione dei costi** > **Analisi dei costi**.
1. Aggiungere un filtro per **Modello di determinazione prezzi: Prenotazione**.
1. In **Ambito** e accanto al costo visualizzato fare clic sul simbolo della freccia verso il basso e selezionare la metrica **Costo effettivo** o **Costo ammortizzato**.

![Selezionare una metrica di costo](./media/cost-analysis-common-uses/metric-cost.png)

Ogni metrica influisce sulla modalità di visualizzazione dei dati per gli addebiti delle prenotazioni.

**Costo effettivo**: mostra l'acquisto come visualizzato nella fattura. Se, ad esempio, è stata acquistata una prenotazione di un anno per $ 1200 a gennaio, l'analisi dei costi mostra un costo di $ 1200 nel mese di gennaio per la prenotazione. Non viene visualizzato alcun costo di prenotazione per gli altri mesi dell'anno. Se si raggruppano i costi effettivi per macchina virtuale, una macchina virtuale che ha ricevuto il vantaggio della prenotazione per un determinato mese presenterà un costo pari a zero per il mese.

**Costo ammortizzato**: mostra l'acquisto della prenotazione ripartito come costo ammortizzato per la durata del periodo di prenotazione. Usando lo stesso esempio precedente, l'analisi dei costi mostra un costo di $ 100 per ogni mese dell'anno se è stata acquistata una prenotazione di un anno per $ 1200 a gennaio. Se in questo esempio si raggruppano i costi per macchina virtuale, si noterà che i costi sono attribuiti a ogni macchina virtuale che ha ricevuto il vantaggio della prenotazione.

## <a name="view-your-reservation-utilization"></a>Visualizzare l'utilizzo della prenotazione

Dopo l'acquisto di una prenotazione, è importante tenere traccia dell'utilizzo in modo da poter usufruire dei servizi acquistati. Se, ad esempio, sono state acquistate 10 macchine virtuali per un anno e ne sono state usate solo cinque, la metà dell'acquisto è stata sprecata. Esistono due modi diversi per valutare l'utilizzo:

### <a name="view-unused-ri-costs-in-cost-analysis"></a>Visualizzare i costi delle istanze riservate inutilizzate nell'analisi dei costi

Per identificare la quantità di costi attualmente sprecata ogni mese per l'acquisto della prenotazione, seguire questa procedura.

1. Nel portale di Azure passare all'analisi dei costi per l'ambito a cui è applicata la prenotazione. Ad esempio, **Gestione dei costi e fatturazione** > **Gestione dei costi** > **Analisi dei costi**.
1. Aggiungere un filtro per **Modello di determinazione prezzi: Prenotazione**.
1. Selezionare la visualizzazione **Costo ammortizzato**.
1. Impostare la granularità su **Mensile**.
1. Impostare il periodo di tempo sull'anno corrente o sul periodo della prenotazione.
1. Impostare il tipo di grafico su **Istogramma (in pila)** .
1. Raggruppare gli addebiti per **Tipo di addebito**.
1. Esaminare i risultati per i valori `unusedreservation`.

[![Esempio che mostra l'uso della prenotazione](./media/cost-analysis-common-uses/view-reservation-cost.png)](./media/cost-analysis-common-uses/view-reservation-cost.png#lightbox)

### <a name="view-utilization-in-reservations"></a>Visualizzare l'utilizzo nelle prenotazioni

Per istruzioni dettagliate, vedere [Ottimizzare l'uso delle prenotazioni](../reservations/manage-reserved-vm-instance.md#optimize-reservation-use).

## <a name="view-costs-for-a-specific-tag"></a>Visualizzare i costi per un tag specifico

Molti utenti di Azure applicano i tag alle risorse, ad esempio un centro di costo o un ambiente di sviluppo (produzione e test), per classificare meglio gli addebiti. I tag vengono visualizzati come dimensione nell'analisi dei costi. È possibile usare la dimensione per ottenere informazioni dettagliate sulle categorizzazioni personalizzate per l'assegnazione di tag.

Il supporto per i tag si applica all'uso segnalato *dopo* che il tag è stato applicato alla risorsa. I tag non vengono applicati retroattivamente per l'accumulo dei costi.

1. Nel portale di Azure passare all'analisi dei costi per l'ambito di interesse. Ad esempio: **Gestione dei costi e fatturazione** > **Gestione dei costi** > **Analisi dei costi**.
1. Selezionare **Raggruppa per** per il tag.

[![Visualizzare i costi per un tag specifico](./media/cost-analysis-common-uses/tag.png)](./media/cost-analysis-common-uses/tag.png#lightbox)

## <a name="download-your-usage-details"></a>Scaricare i dettagli di utilizzo

Il file di report dei dettagli sull'utilizzo in formato CSV fornisce una ripartizione di tutti gli addebiti accumulati per una fattura. È possibile utilizzare il report per confrontarlo e comprendere meglio la fattura. Ogni addebito inserito in fattura corrisponde alla ripartizione degli addebiti nel report sull'utilizzo.

1. Nel portale di Azure passare alla scheda **Utilizzo e addebiti** per un account o una sottoscrizione di fatturazione. Ad esempio: **Gestione dei costi e fatturazione** > **Fatturazione** > **Utilizzo e addebiti**.
1. Selezionare la voce da scaricare e fare clic sul simbolo di download.  
    [![Scaricare l'utilizzo e gli addebiti](./media/cost-analysis-common-uses/download1.png)](./media/cost-analysis-common-uses/download1.png#lightbox)
1.  Selezionare il file di utilizzo da scaricare.  
    ![Scegliere un file di utilizzo da scaricare](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Visualizzare la ripartizione dei costi di EA mensile

La registrazione EA accumula i costi per l'intera organizzazione. Conoscere il modo in cui i costi vengono accumulati e fatturati nel tempo consente di coinvolgere le parti interessate appropriate per garantire che i costi siano gestiti in modo responsabile.

Vengono mostrati i costi solo per la registrazione attiva. Se una registrazione inattiva è stata trasferita in una attiva, i costi della registrazione precedente non vengono mostrati in Gestione costi.


1. Nel portale di Azure passare a **Gestione dei costi e fatturazione** > **Panoramica**.
1. Fare clic su **Ripartizione** per il mese corrente e visualizzare il burn-down dell'impegno monetario.  
    [![Panoramica dei costi EA - riepilogo della ripartizione](./media/cost-analysis-common-uses/breakdown1.png)](./media/cost-analysis-common-uses/breakdown1.png#lightbox)
1.  Fare clic sulla scheda **Utilizzo e addebiti** e visualizzare la ripartizione del mese precedente nell'intervallo di tempo scelto.  
    [![Scheda Utilizzo e addebiti](./media/cost-analysis-common-uses/breakdown2.png)](./media/cost-analysis-common-uses/breakdown2.png#lightbox)

## <a name="view-enrollment-monthly-cost-by-term"></a>Visualizzare il costo mensile per l'iscrizione per termine

Usare una visualizzazione grafica dei costi mensili della registrazione per comprendere le tendenze dei costi e gli importi fatturati per un determinato periodo.

1. Nel portale di Azure passare all'analisi dei costi per l'ambito di interesse. Ad esempio: **Gestione dei costi e fatturazione** > **Gestione dei costi** > **Analisi dei costi**.
1. Selezionare la registrazione e impostare il termine di registrazione.
1. Impostare la granularità su mensile, quindi impostare la vista su **Colonna    (in pila)** .

È possibile raggruppare e filtrare i dati per un'analisi più dettagliata.

[![Costo di registrazione mensile per periodo](./media/cost-analysis-common-uses/enrollment-term1.png)](./media/cost-analysis-common-uses/enrollment-term1.png#lightbox)

## <a name="view-ea-enrollment-accumulated-costs"></a>Visualizzare i costi accumulati per la registrazione EA

Consente di visualizzare i costi cumulativi netti nel tempo per comprendere le spese complessive per l'organizzazione per un determinato periodo.

1. Nel portale di Azure passare all'analisi dei costi per l'ambito di interesse. Ad esempio: **Gestione dei costi e fatturazione** > **Gestione dei costi** > **Analisi dei costi**.
1. Selezionare la registrazione, quindi visualizzare i costi accumulati correnti.

[![Costi accumulati per la registrazione](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)](./media/cost-analysis-common-uses/cost-analysis-enrollment.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi
- Se non è stata ancora completata la prima guida introduttiva di Gestione costi, esaminarla in [Avviare l’analisi dei costi](quick-acm-cost-analysis.md).
- Leggere la [Documentazione di Gestione dei costi di Azure](../index.yml).
