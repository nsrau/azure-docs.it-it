---
title: Usare i report di Gestione costi in Gestione costi di Azure | Microsoft Docs
description: Questo articolo descrive come usare i diversi report di Gestione costi nel portale di Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: ''
ms.openlocfilehash: fa263a4b6f41e2b31328f46b8d1341d0d74c9a85
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="use-cost-management-reports"></a>Usare i report di Gestione costi

Questo articolo descrive come usare i diversi report di Gestione costi nel portale di Cloudyn. La maggior parte dei report Cloudyn presenta le informazioni in modo intuitivo e con un aspetto grafico uniforme. Per una panoramica sui report Cloudyn, vedere [Informazioni sui report dei costi](understanding-cost-reports.md). L'articolo descrive anche varie opzioni e diversi campi usati nella maggior parte dei report.

## <a name="cost-analysis-reports"></a>Report di analisi dei costi

I report di analisi dei costi visualizzano i dati di fatturazione dei provider di servizi cloud. Con i report è possibile raggruppare e analizzare in dettaglio diversi segmenti di dati riportati nel file di fatturazione. I report consentono di spostarsi a livello molto dettagliato da una voce di costi all'altra nei dati di fatturazione non elaborati dei fornitori di servizi cloud.

I report di analisi dei costi non raggruppano i costi per tag. La creazione di report basati su tag è disponibile solo nei report di allocazione costi impostati dopo la creazione di un modello costo con Cost Allocation 360.

### <a name="actual-cost-analysis"></a>Analisi dei costi effettivi

Il report di analisi dei costi effettivi illustra le voci che contribuiscono in maggior misura ai costi, incluse le spese correnti e i corrispettivi una tantum.

 Usare il report di analisi dei costi effettivi per:

- Analizzare e monitorare i costi effettivi di un intervallo di tempo specificato
- Pianificare un avviso di soglia
- Analizzare i costi di showback e chargeback

#### <a name="to-use-the-actual-cost-analysis-report"></a>Per usare il report di analisi dei costi effettivi

Eseguire almeno i passaggi seguenti. È anche possibile usare altre opzioni e altri campi.

1. Selezionare un intervallo di date.
2. Selezionare un filtro.

È possibile fare clic con il pulsante destro del mouse sui risultati del report per analizzarli più approfonditamente e visualizzare informazioni più dettagliate.

![Esempio di report di analisi dei costi effettivi](./media/use-reports/actual-cost-analysis.png)

### <a name="actual-cost-over-time"></a>Costi effettivi nel tempo

Il report dei costi effettivi nel tempo è un report di analisi dei costi standard che distribuisce i costi su una risoluzione di tempo definita. Il report visualizza la spesa nel tempo per consentire l'osservazione delle tendenze e il rilevamento di eventuali irregolarità di spesa. Questo report illustra le voci che contribuiscono in maggior misura ai costi, incluse le spese correnti e i corrispettivi una tantum di istanze riservate corrisposti in un periodo di tempo selezionato.

Usare il report dei costi effettivi nel tempo per:

- Seguire le tendenze dei costi nel tempo.
- Individuare eventuali irregolarità nei costi.
- Identificare i punti non chiari relativi ai costi correlati ad Amazon Web Services.

#### <a name="to-use-the-actual-cost-over-time-report"></a>Per usare il report dei costi effettivi nel tempo:

Eseguire almeno i passaggi seguenti. È anche possibile usare altre opzioni e altri campi.

- Selezionare un intervallo di date.

È ad esempio possibile selezionare diversi gruppi per visualizzarne i costi nel tempo e quindi aggiungere i filtri per limitare i risultati.

![Esempio di report dei costi effettivi nel tempo](./media/use-reports/actual-cost-over-time.png)



### <a name="amortized-cost-reports"></a>Report dei costi ammortizzati

Questo set di report dei costi ammortizzati illustra i corrispettivi di servizio non basati sull'utilizzo linearizzati o i costi una tantum e li ripartisce nel tempo in modo uniforme nel corso della loro vita utile.

Ecco alcuni esempi di corrispettivi una tantum:

- Corrispettivi annuali per il supporto tecnico
- Corrispettivi annuali per i componenti di sicurezza
- Corrispettivi per l'acquisto di istanze riservate
- Spese per l'acquisto di alcuni elementi di Azure Marketplace

Nel file di fatturazione, i corrispettivi una tantum sono caratterizzati dal fatto che la data di inizio e la data di fine del consumo del servizio (timestamp) hanno valori uguali. Cloudyn quindi li riconosce come corrispettivi una tantum che possono essere ammortizzati. Altri servizi in base al consumo con costi di utilizzo su richiesta non possono essere ammortizzati.

Per illustrare i costi ammortizzati, esaminare l'immagine di esempio seguente di un report dei costi effettivi nel tempo. Nell'esempio è illustrato un picco dei costi il 23 agosto che potrebbe sembrare un'anomalia in confronto alla tendenza dei costi giornalieri consueta. L'analisi della causa radice e l'esame di diversi dati consentono di identificare questo costo come costo della prenotazione APN annua del servizio AWS. Si tratta di un corrispettivo una tantum per un acquisto effettuato e fatturato quel giorno. Nella prossima sezione è possibile vedere in che modo il costo viene ammortizzato.

![Esempio di report dei costi effettivi nel tempo con un costo una tantum](./media/use-reports/actual-amort-example.png)

#### <a name="to-use-the-amortized-cost-over-time-report"></a>Per usare il report dei costi ammortizzati nel tempo:

Eseguire almeno i passaggi seguenti. È anche possibile usare altre opzioni e altri campi.

1. Selezionare un intervallo di date.
2. Selezionare un servizio e un provider.

Procedendo con l'esempio precedente, nell'immagine seguente è possibile vedere che il costo una tantum è ora ammortizzato:

![Esempio di report dei costi ammortizzati nel tempo](./media/use-reports/amort-cost-over-time.png)

L'immagine precedente illustra il costo ammortizzato della prenotazione APN nel tempo. Questo report illustra l'ammortamento del corrispettivo una tantum e il costo dell'APN come acquisto della prenotazione annuale. Il costo dell'APN viene distribuito uniformemente su base giornaliera come 1/365 del costo anticipato della prenotazione.

## <a name="cost-allocation-analysis-reports"></a>Report di analisi dell'allocazione costi

I report di analisi dell'allocazione costi sono disponibili dopo la creazione di un modello di costi tramite Cost Allocation 360. Cloudyn elabora i dati dei costi o di fatturazione e abbina i dati ai dati di utilizzo e dei tag degli account cloud. Per effettuare l'abbinamento dei dati, Cloudyn richiede l'accesso ai dati di utilizzo. Gli account per i quali non sono disponibili le credenziali vengono etichettati come risorse non classificate.

### <a name="cost-analysis-report"></a>Report di analisi dei costi

Il report di analisi dei costi offre informazioni approfondite sul consumo e sulla spesa relativi al cloud durante un intervallo di tempo selezionato. Questo report usa i criteri impostati nello strumento di gestione dell'allocazione costi.

Modalità di calcolo di Cloudyn per questo report

Per garantire che l'allocazione mantenga l'integrità di ogni account collegato, Cloudyn applica il principio dell'affinità degli account. L'affinità garantisce che a un account che non usa un servizio specifico non venga allocato alcun costo di tale servizio. I costi accumulati nell'account rimangono all'interno di questo e non vengono calcolati in base ai criteri di allocazione. Si supponga ad esempio di avere cinque account collegati. Se solo tre di questi usano servizi di archiviazione, il costo di tali servizi viene allocato attraverso tag solo ai tre account.

 Usare il report di analisi dei costi per:

- Offrire una visualizzazione aggregata dell'intera distribuzione per un intervallo di tempo specifico.
- Visualizzare i costi per categorie di tag in base ai criteri creati nel modello di costi.

#### <a name="to-use-the-cost-analysis-report"></a>Per usare il report di analisi dei costi:

1. Selezionare un intervallo di date.
2. Aggiungere tag in base alle esigenze.
3. Aggiungere gruppi.
4. Scegliere un modello di costi creato in precedenza.

L'immagine seguente illustra un report di analisi dei costi di esempio in formato di grafico radiale. Gli anelli rappresentano i gruppi. L'anello esterno rappresenta il servizio e l'anello interno rappresenta l'unità.

![Esempio di report di analisi dei costi](./media/use-reports/cost-analysis01.png)



Ecco un esempio delle stesse informazioni in una visualizzazione tabella.

![Esempio di report di analisi dei costi](./media/use-reports/cost-analysis02.png)



### <a name="cost-over-time-report"></a>Report sui costi nel tempo

Il report dei costi nel tempo visualizza la spesa nel tempo, per consentire l'individuazione di tendenze e il rilevamento di irregolarità nella distribuzione. Essenzialmente illustra i costi distribuiti in un periodo definito. Il report include le voci che contribuiscono in maggior misura ai costi, incluse le spese correnti e i corrispettivi una tantum di istanze riservate corrisposti in un periodo di tempo selezionato. In questo report è possibile usare i criteri impostati in Cost Manager 360°.

Usare il report dei costi nel tempo per:

- Visualizzare le modifiche nel tempo e comprendere quali influssi cambiano da un giorno (o da un intervallo di date) al successivo.
- Analizzare i costi nel tempo per un'istanza specifica.
- Comprendere il motivo per cui si è verificato un aumento dei costi per un'istanza specifica.

#### <a name="to-use-the-cost-over-time-report"></a>Per usare il report dei costi nel tempo:

1. Selezionare un intervallo di date.
2. Aggiungere tag in base alle esigenze.
3. Aggiungere gruppi.
4. Scegliere un modello di costi creato in precedenza.
5. Selezionare costi effettivi o ammortizzati.
6. Scegliere se applicare regole di allocazione per offrire una visualizzazione dati di fatturazione non elaborati o ricalcolare il costo in base a una visualizzazione.

Ecco un esempio del report.

![Esempio di costi nel tempo](./media/use-reports/cost-over-time.png)



## <a name="next-steps"></a>Passaggi successivi

- Se non si è ancora completata la prima esercitazione per Gestione costi, leggere le informazioni riportate in [Esaminare l'utilizzo e i costi](tutorial-review-usage.md).
