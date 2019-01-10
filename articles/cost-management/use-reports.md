---
title: Usare i report di Cloudyn in Azure | Microsoft Docs
description: Questo articolo illustra lo scopo dei report di Cloudyn che sono inclusi nel portale di Cloudyn e come usarli in modo efficace.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/17/2018
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: db24cb1e1b303fbd3f0981390940732685c98baf
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558805"
---
# <a name="reports-available-in-the-cloudyn-portal"></a>Report disponibili nel portale di Cloudyn

Questo articolo illustra lo scopo dei report di Cloudyn costi che sono inclusi nel portale di Cloudyn. Descrive anche come usare in modo efficace i report. La maggior parte dei report presenta le informazioni in modo intuitivo e con un aspetto grafico uniforme. La maggior parte delle azioni che è possibile eseguire in un report, è possibile anche in altri report. Per una panoramica su come usare i report di Cloudyn, incluse le procedure per personalizzare e salvare o pianificare i report, vedere [Informazioni sui report dei costi](understanding-cost-reports.md).

Gestione costi di Azure offre funzionalità simili a quelle di Cloudyn. Gestione costi di Azure è una soluzione di gestione costi nativa di Azure. È un modo per analizzare i costi, creare e gestire budget, esportare i dati, esaminare e implementare gli elementi consigliati per l'ottimizzazione e di conseguenza risparmiare. Per altre informazioni, vedere [Gestione costi di Azure](overview-cost-mgt.md).

## <a name="report-types"></a>Tipi di report

Esistono tre tipi di report di Cloudyn:

- Report nel tempo. Ad esempio, il report dei costi nel tempo. I report nel tempo mostrano una serie temporale di dati in un intervallo selezionato con una risoluzione predefinita e mostrano una risoluzione settimana per gli ultimi due mesi. È possibile usare il raggruppamento e i filtri per ingrandire i vari punti dati.
  - I report nel tempo consentono di visualizzare le tendenze e di rilevare i picchi o le anomalie.
- Report di analisi. Ad esempio, il report di analisi dei costi. Questi report mostrano dati aggregati in un periodo definito dall'utente e consentono di raggruppare e filtrare i dati.
  - I report di analisi consentono di visualizzare i picchi, di individuare le cause alla radice di anomalie e di mostrare una suddivisione granulare dei dati.
- Report tabulari. È possibile visualizzare tutti i report come tabelle, ma alcuni di essi sono visualizzati solo come tabella. Questi report contengono elenchi dettagliati di elementi.
  - I consigli sono report tabulari, non esistono visualizzazioni per questi. Tuttavia, è possibile visualizzare i risultati dei consigli. Ad esempio, i risparmi nel tempo.
  - I report tabulari sono utili come elenchi di azioni o per l'esportazione di dati su cui eseguire altre elaborazioni. Ad esempio, un report di chargeback.

I report dei costi mostrano i costi _effettivi_ oppure _ammortizzati_.

I report dei costi effettivi mostrano i pagamenti eseguiti durante l'intervallo di tempo selezionato. Ad esempio, tutte le tariffe una tantum quali gli acquisti di istanze riservate vengono visualizzate nei report dei costi effettivi come picchi di costo.

I report dei costi ammortizzati distribuiscono le tariffe una tantum durante il periodo a cui sono applicate. Ad esempio, le tariffe una tantum per gli acquisti di istanze riservate vengono ripartite entro i termini della prenotazione e non vengono visualizzate come un picco. La visualizzazione ammortizzata è l'unico modo per visualizzare le tendenze reali e fare proiezioni dei costi.

In alcuni casi l'ammortamento viene presentato come un report distinto. Gli esempi includono i report di analisi dei costi e i report di analisi dei costi ammortizzati. In altri casi l'ammortamento è un criterio del report, ad esempio dei report di allocazione dei costi e dei report di analisi dei costi.

È possibile pianificare il recapito periodico di qualsiasi report. I report dei costi consentono di impostare una soglia, in modo che siano utili per gli avvisi.

## <a name="cost-analysis-vs-cost-allocation"></a>Analisi dei costi e allocazione dei costi

I report di _analisi dei costi_ mostrato i dati di fatturazione dei provider di servizi cloud. Con i report è possibile raggruppare e analizzare in dettaglio diversi segmenti di dati riportati nel file di fatturazione. I report consentono di spostarsi a livello molto dettagliato da una voce di costi all'altra nei dati di fatturazione non elaborati dei fornitori di servizi cloud.

Alcuni report di _analisi dei costi_ non raggruppano i costi per tag delle risorse. Le informazioni di fatturazione basate sui tag vengono visualizzate solo nei report dopo l'allocazione dei costi mediante la creazione di un modello di costo con [Allocazione dei costi a 360°](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

I report di _allocazione dei costi_ sono disponibili dopo la creazione di un modello di costo tramite [Allocazione dei costi a 360°](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs). Cloudyn elabora i dati dei costi e di fatturazione e _abbina_ i dati ai dati di utilizzo e dei tag degli account cloud. Per effettuare l'abbinamento dei dati, Cloudyn richiede l'accesso ai dati di utilizzo. Se l'utente dispone di account per i quali non sono disponibili le credenziali, questi vengono etichettati come _risorse non classificate_.

## <a name="dashboards"></a>Dashboard

I dashboard di Cloudyn offrono una panoramica dei report di livello elevato. I dashboard sono costituiti da widget e ogni widget è essenzialmente un'anteprima del report. Quando si [personalizzano i report](understanding-cost-reports.md#save-and-schedule-reports), li si salva in Report personali e li si aggiunge ai dashboard. Per altre informazioni sui dashboard, vedere [Visualizzare le metriche di costo chiave con i dashboard](dashboards.md).

## <a name="budget-information-in-reports"></a>Informazioni sul budget nei report

Molti report di Cloudyn mostrano informazioni sul budget dopo che l'utente ne ha creato uno manualmente. Pertanto i report non mostreranno informazioni sul budget fino a quando non si crea un budget. Per altre informazioni, vedere [Impostazioni di Gestione budget](#budget-management-settings).

## <a name="reports-and-reporting-features"></a>Report e funzionalità di creazione dei report

Cloudyn include i report e le funzionalità di creazione dei report seguenti.

### <a name="cost-navigator-report"></a>Report Esplorazione costi

Il report Esplorazione costi è un modo rapido per visualizzare i consumi in fattura tramite una visualizzazione dashboard. È dotato di un subset di filtri e di viste di base per mostrare immediatamente una visualizzazione di riepilogo dei costi dell'organizzazione. I costi vengono visualizzati per data. Poiché il report è da intendersi come una visualizzazione iniziale dei costi, non è flessibile o né completo come molti altri report o dashboard personalizzati creati dall'utente.

Per impostazione predefinita, le principali viste del report mostrano:

- I costi nel tempo che mostrano una visualizzazione di un grafico a barre di una settimana lavorativa. È possibile modificare **Intervallo di date** per modificare l'intervallo di date del grafico a barre.
- Spese per servizio tramite un grafico a torta.
- Classificazione delle risorse per tag, tramite un grafico a torta.
- Spese per entità di costo tramite un grafico a torta.
- Il costo totale, per data in una visualizzazione ad elenco.

### <a name="cost-analysis-report"></a>Report di analisi dei costi

Il report Analisi dei costi è un calcolo di showback e chargeback, in base i criteri dell'utente. Aggrega l'uso del cloud durante un intervallo di tempo selezionato, dopo aver applicato tutte le regole di allocazione al costo. Ad esempio, calcola i costi per tag, riassegna i costi delle risorse senza tag e, facoltativamente, consente di allocare l'uso di istanze riservate.

I criteri configurati in [Allocazione dei costi a 360°](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) vengono usati nel report Analisi dei costi e i risultati vengono combinati con le informazioni dei dati non elaborati del proprio fornitore di servizi cloud.

Come viene calcolato il report? Per garantire che l'allocazione mantenga l'integrità di ogni account collegato, il servizio Cloudyn applica il principio dell'_affinità degli account_. L'affinità garantisce che a un account che non usa un servizio specifico non venga allocato alcun costo per tale servizio. I costi accumulati nell'account rimangono all'interno di questo e non vengono calcolati in base ai criteri di allocazione. Si supponga ad esempio di avere cinque account collegati. Se solo tre di questi usano servizi di archiviazione, il costo di tali servizi viene allocato attraverso tag solo ai tre account.

Usare il report di analisi dei costi per:

- Calcolare il chargeback/showback dell'organizzazione
- Classificare tutti i costi
- Offrire una visualizzazione aggregata dell'intera distribuzione per un intervallo di tempo specifico.
- Visualizzare i costi per categorie di tag in base ai criteri creati nel modello di costi.

Per usare il report di analisi dei costi:

1. Selezionare un intervallo di date.
2. Aggiungere tag in base alle esigenze.
3. Aggiungere gruppi.
4. Scegliere un modello di costi creato in precedenza.

### <a name="cost-over-time-report"></a>Report sui costi nel tempo

Il report Costo nel tempo mostra i risultati dell'allocazione dei costi come serie temporali. Consente di osservare le tendenze e di rilevare eventuali irregolarità nella distribuzione. Essenzialmente illustra i costi distribuiti in un periodo definito. Il report include le voci che contribuiscono in maggior misura ai costi, incluse le spese correnti e i corrispettivi una tantum di istanze riservate corrisposti in un periodo di tempo selezionato. In questo report vengono usati i criteri impostati in [Allocazione dei costi a 360°](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

Usare il report dei costi nel tempo per:

- Visualizzare le modifiche nel tempo e comprendere quali influssi cambiano da un giorno (o da un intervallo di date) al successivo.
- Analizzare i costi nel tempo per un'istanza specifica.
- Comprendere il motivo per cui si è verificato un aumento dei costi per un'istanza specifica.

Per usare il report dei costi nel tempo:

1. Selezionare un intervallo di date.
2. Aggiungere tag in base alle esigenze.
3. Aggiungere gruppi.
4. Scegliere un modello di costi creato in precedenza.
5. Selezionare costi effettivi o ammortizzati.
6. Scegliere se applicare regole di allocazione per offrire una visualizzazione dati di fatturazione non elaborati o ricalcolare il costo in base a una visualizzazione.

### <a name="actual-cost-analysis-report"></a>Report Analisi dei costi effettivi

Il report Analisi dei costi effettivi illustra i costi del provider senza modifiche. Mostra le voci che contribuiscono in maggior misura ai costi, incluse le spese correnti e le tariffe una tantum.

È possibile usare il report per visualizzare le informazioni sui costi per le sottoscrizioni. Nel report le sottoscrizioni di Azure vengono visualizzate come **nome dell'account** e **numero di conto**. Gli **account collegati** mostrano le sottoscrizioni di AWS. Per visualizzare i costi per sottoscrizione, una suddivisione per ogni account, in **Gruppi** selezionare il tipo di sottoscrizione dell'utente.

Usare il report di analisi dei costi effettivi per:

- Analizzare e monitorare i costi del provider non elaborati affrontati durante un intervallo di tempo specificato.
- Pianificare un avviso relativo alla soglia.
- Analizzare i costi non modificati sostenuti dall'account e dall'entità.

### <a name="actual-cost-over-time-report"></a>Report Costi effettivi nel tempo

Il report dei costi effettivi nel tempo è un report di analisi dei costi standard che distribuisce i costi su una risoluzione di tempo definita. Il report visualizza la spesa nel tempo per consentire l'osservazione delle tendenze e il rilevamento di eventuali irregolarità di spesa. Questo report illustra le voci che contribuiscono in maggior misura ai costi, incluse le spese correnti e i corrispettivi una tantum di istanze riservate corrisposti in un periodo di tempo selezionato.

Usare il report dei costi effettivi nel tempo per:

- Seguire le tendenze dei costi nel tempo.
- Individuare eventuali irregolarità nei costi.
- Identificare i punti non chiari relativi ai costi correlati ai provider di servizi cloud.

### <a name="amortized-cost-reports"></a>Report Costi ammortizzati

Questo set di report dei costi ammortizzati illustra i corrispettivi di servizio non basati sull'utilizzo linearizzati o i costi una tantum e li ripartisce nel tempo in modo uniforme nel corso della loro vita utile. Ecco alcuni esempi di corrispettivi una tantum:

- Corrispettivi annuali per il supporto tecnico
- Corrispettivi annuali per i componenti di sicurezza
- Corrispettivi per l'acquisto di istanze riservate
- Spese per l'acquisto di alcuni elementi di Azure Marketplace

Nel file di fatturazione, le tariffe una tantum sono caratterizzate dal fatto che la data di inizio e la data di fine del consumo del servizio, ovvero timestamp, hanno gli stessi valori. A questo punto il servizio Cloudyn le riconosce come tariffe una tantum che vengono ammortizzate. Altri servizi in base al consumo con costi di relativi all'uso su richiesta non vengono ammortizzati.

I report dei costi ammortizzati includono:

- Analisi dei costi ammortizzati
- Costi ammortizzati nel tempo

### <a name="cost-analysis-report"></a>Report di analisi dei costi

Il report di analisi dei costi offre informazioni approfondite sul consumo e sulla spesa relativi al cloud durante un intervallo di tempo selezionato. Questo report usa i criteri impostati in [Allocazione dei costi a 360°](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) nel report Analisi dei costi.

Modalità di calcolo di Cloudyn per questo report

Per garantire che l'allocazione mantenga l'integrità di ogni account collegato, Cloudyn applica il principio dell'_affinità degli account_. L'affinità garantisce che a un account che non usa un servizio specifico non venga allocato alcun costo per tale servizio. I costi accumulati nell'account rimangono al suo interno e non vengono calcolati in base ai criteri di allocazione. Si supponga ad esempio di avere cinque account collegati. Se solo tre di questi usano servizi di archiviazione, il costo di tali servizi viene allocato attraverso tag solo ai tre account.

Usare il report di analisi dei costi per:

- Offrire una visualizzazione aggregata dell'intera distribuzione per un intervallo di tempo specifico.
- Visualizzare i costi per categorie di tag in base ai criteri creati nel modello di costi.

### <a name="cost-over-time-report"></a>Report sui costi nel tempo

Il report Costi nel tempo mostra la spesa nel tempo, per consentire l'individuazione di tendenze e il rilevamento di irregolarità nella distribuzione. Essenzialmente illustra i costi distribuiti in un periodo definito. Il report include le voci che contribuiscono in maggior misura ai costi, incluse le spese correnti e i corrispettivi una tantum di istanze riservate corrisposti in un periodo di tempo selezionato. In questo report vengono usati i criteri impostati in [Allocazione dei costi a 360°](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

Usare il report dei costi nel tempo per:

- Visualizzare le modifiche nel tempo e comprendere quali influssi cambiano da un giorno (o da un intervallo di date) al successivo.
- Analizzare i costi nel tempo per un'istanza specifica.
- Comprendere il motivo per cui si è verificato un aumento dei costi per un'istanza specifica.

### <a name="custom-charges-report"></a>Report Addebiti personalizzati

Gli utenti Enterprise e CSP spesso si ritrovano a offrire servizi aggiuntivi ai propri clienti esterni o interni, oltre al personale consumo di risorse cloud. L'utente definisce gli addebiti personalizzati per i servizi o gli sconti aggiunti alla fatturazione del cliente o ai report di chargeback come voci personalizzate.

Gli addebiti per il servizio personalizzato riflettono i servizi che normalmente non vengono visualizzati in una fattura. Gli addebiti personalizzati creati vengono quindi mostrati nel report dei costi.

*Gli addebiti personalizzati non sono prezzi personalizzati*. L'elenco di addebiti personalizzati non mostra possibili tariffe diverse applicate. Ad esempio, gli addebiti nella fatturazione AWS vengono visualizzati in base ai costi reali.

Per creare un addebito personalizzato:

1. In **Custom Charges** (Addebiti personalizzati) fare clic su **Aggiungi nuovo**. Viene visualizzata la finestra di dialogo _Add New Custom Charge_ (Aggiungi nuovo addebito personalizzato).
2. In **Nome del provider** immettere il nome del provider.
3. In **Nome servizio** immettere il tipo di servizio.
4. In **Descrizione** aggiungere una descrizione per l'addebito personalizzato.
5. In **Tipo** immettere la **Percentuale** selezionata e nell'elenco a discesa Servizi selezionare i servizi da includere come addebiti personalizzati nei report dei costi.
6. In **Pagamento** scegliere se si tratta di una tariffa una tantum o di una tariffa ricorrente. Se l'addebito è una tariffa ricorrente, selezionare Amortized (Ammortizzato) se si desidera che l'addebito venga ammortizzato e selezionare il numero di mesi.
7. In **Date** se è stata selezionata una tariffa una tantum, in **Data di validità** immettere la data di pagamento dell'addebito. Se è stata selezionata la tariffa ricorrente, immettere l'intervallo di date tra cui la data di inizio e data di fine dell'addebito.
8. Nell'**albero Entità** selezionare le entità a cui si desidera applicare l'addebito e quindi selezionare **Attivo**.

_Quando gli addebiti vengono assegnati a un'entità, gli utenti non possono modificarli. Gli addebiti che vengono aggiunti da un amministratore a un'entità padre sono di sola lettura._

Per visualizzare gli addebiti personalizzati:

Gli addebiti personalizzati vengono visualizzati nel report dei costi. Ad esempio, aprire il report Analisi dei costi effettivi, quindi in **Extended Filters** (Filtri estesi) selezionare **Autonomi**. Quindi applicare il filtro per mostrare gli **addebiti personalizzati**.

### <a name="cost-allocation-360"></a>Allocazione dei costi a 360°

Allocazione dei costi a 360° consente di creare modelli di allocazione dei costi personalizzati per assegnare i costi alle risorse cloud usate. Molti report mostrano le informazioni dei modelli di costo personalizzato che sono stati creati con i modelli di costo personalizzato. Alcuni report mostrano le informazioni solo dopo che l'utente ha creato un modello di costo personalizzato con allocazione dei costi.

Per altre informazioni sulla creazione dei modelli di costo personalizzati, vedere [Esercitazione: Gestire i costi tramite Cloudyn](tutorial-manage-costs.md).

### <a name="cost-vs-budget-over-time-report"></a>Report Costi e budget nel tempo

Il report Costi e budget nel tempo consente di confrontare i principali collaboratori dei costi rispetto al budget. Il budget assegnato viene visualizzato nel report in modo che sia possibile visualizzare il consumo del budget, superato, non superato o in pari, nel corso del tempo. L'uso di Mostra/nascondi campi nella parte superiore del report consente di scegliere di visualizzare il costo, il budget, il costo accumulato e il budget totale.

### <a name="current-month-projected-cost-report"></a>Report Previsione dei costi per il mese corrente

Il report Previsione dei costi per il mese corrente offre informazioni dettagliate di riepilogo sui costi da inizio mese. Questo report mostra i costi dall'inizio del mese, del mese precedente e i costi totali previsti per il mese corrente. La previsione dei costi per il mese corrente viene calcolata come somma del costo mensile aggiornato e di una proiezione in base al costo monitorato negli ultimi 30 giorni.

Usare il report Previsione dei costi per il mese corrente per:

- Proiettare i costi mensili per servizio
- Proiettare i costi mensili per account

### <a name="annual-projected-cost-report"></a>Report Previsione dei costi annuali

Il report Previsione dei costi annuali consente di visualizzare i costi annuali previsti in base alle tendenze di spesa precedenti. Mostra i 12 mesi successivi della previsione complessiva dei costi. Le proiezioni vengono effettuate usando una funzione della tendenza estrapolata nei 12 mesi successivi, in base ai costi associati agli ultimi 30 giorni di uso.

### <a name="budget-management-settings"></a>Impostazioni di Gestione budget

Gestione budget consente di impostare un budget per l'anno fiscale.

Per aggiungere un budget a un'entità:

1. Nella pagina di gestione del budget, in **Entità** selezionare l'entità per cui si vuole creare il budget.
2. Nell'anno del budget selezionare l'anno per cui si vuole creare il budget.
3. Impostare il budget per ogni mese e quindi fare clic su **Salva**.

Per importare un file per il budget annuo:

1. In **Azioni** selezionare **Esporta** per scaricare un modello CSV vuoto da usare come base per il budget.
2. Compilare il file CSV con i movimenti del budget e salvarlo in locale.
3. In **Azioni** selezionare **Importa**.
4. Selezionare il file salvato e quindi fare clic su **OK**.

Per esportare il budget completato come file CSV, in **Azioni** selezionare **Esporta** per scaricare il file.

Al termine dell'operazione, il budget viene mostrato nel report Analisi dei costi e nel report Costo e tra costo e budget nel tempo. È anche possibile pianificare i report in base alle soglie di budget.

### <a name="azure-resource-explorer-report"></a>Report Azure Resource Explorer

Il report Azure Resource Explorer mostra un elenco in blocco di tutte le risorse di Azure disponibili in Cloudyn. Per usare in modo efficace il report, gli account Azure devono aver abilitato le metriche estese. Le metriche estese consentono a Cloudyn di accedere alle macchine virtuali di Azure. Per altre informazioni, consultare [Aggiungere metriche estese per le macchine virtuali di Azure](azure-vm-extended-metrics.md).

### <a name="azure-resources-over-time-report"></a>Report Risorse di Azure nel tempo

Il report Risorse di Azure nel tempo mostra una suddivisione di tutte le risorse in esecuzione in un determinato periodo. Per usare in modo efficace il report, gli account Azure devono aver abilitato le metriche estese. Le metriche estese consentono a Cloudyn di accedere alle macchine virtuali di Azure. Per altre informazioni, consultare [Aggiungere metriche estese per le macchine virtuali di Azure](azure-vm-extended-metrics.md).

### <a name="instance-explorer-report"></a>Report Esplora istanze

Il report Esplora istanze viene usato per visualizzare varie metriche per le risorse delle macchine virtuali. È possibile esaminare in dettaglio le istanze specifiche per visualizzare informazioni quali:
- Intervalli di esecuzione delle istanze
- Ciclo di vita nel periodo selezionato
- Uso della CPU
- Input di rete
- Traffico di output
- Dischi attivi

Il report Esplora istanze raccoglie tutti gli intervalli in esecuzione all'interno dell'intervallo di date definito e aggrega i dati di conseguenza. Per visualizzare ogni intervallo di esecuzione durante l'intervallo di date, espandere l'istanza. Il costo di ogni istanza viene calcolato per l'intervallo di date selezionato in base ai listini prezzi di AWS e di Azure. Non vengono applicati sconti. È possibile aggiungere altri campi al report usando Mostra/nascondi campi.

Usare il report Esplora istanze per:

- Calcolare il costo stimato per ogni computer.
- Creare un elenco completo, che includa le ore di esecuzione aggregate, di tutti i computer attivi durante in un intervallo di tempo.
- Creare un elenco di provider di servizi cloud o di account.
- Visualizzare i computer creati o terminati in un intervallo di tempo.
- Visualizzare tutti i computer attualmente arrestati.
- Visualizzare i tag di ogni computer.

### <a name="instances-over-time-report"></a>Report Istanze nel tempo

L'uso del report Istanze nel tempo consente di visualizzare il numero massimo di computer attivi nell'intervallo di tempo selezionato. Se la risoluzione è stata definita per settimana o per mese, i risultati sono il numero massimo di computer attivi in un giorno qualsiasi di tale mese. Selezionare un intervallo di date per selezionare i filtri che si desidera visualizzare nel report.

### <a name="instance-utilization-over-time-report"></a>Report Uso delle istanze nel tempo

Questo report mostra una suddivisione dell'uso della CPU o della memoria nel corso del tempo per tutte le istanze.

### <a name="compute-power-cost-over-time-report"></a>Report Costo della potenza di calcolo nel tempo

Il report Costo della potenza di calcolo nel tempo offre una suddivisione della potenza di calcolo in un intervallo di date specificato. Anche se altri report mostrano il numero di computer in esecuzione o le ore di runtime, questo report mostra le ore core, le ore dell'unità di calcolo o le ore GB di RAM.

Usare il report per:

- Controllare la potenza di calcolo in un intervallo di date specificato.
- Visualizzare i tempi di calcolo in base ai modelli di allocazione dei costi.

Questo report è collegato ai criteri di [Allocazione dei costi a 360°](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) in modo che i risultati vengano visualizzati in base all'assegnazione di tag definita e i criteri in base al criterio di costo selezionato. Quando l'utente non crea un criterio, i risultati non vengono mostrati.

### <a name="compute-power-average-cost-over-time-report"></a>Report Costo della potenza media di calcolo nel tempo

Usare il report Costo della potenza media di calcolo nel tempo per visualizzare altre informazioni oltre al costo di ogni computer in esecuzione. Il report mostra il costo medio per ora istanza, ora core, ora dell'unità di calcolo e ora di GB di RAM. Il report offre informazioni approfondite sull'efficienza della distribuzione.

Questo report è collegato ai criteri di [Allocazione dei costi a 360°](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) in modo che i risultati vengano visualizzati in base all'assegnazione di tag definita e i criteri in base al criterio di costo selezionato. Quando l'utente non crea un criterio, i risultati non vengono mostrati.

### <a name="s3-cost-over-time-report"></a>Report Costo di S3 nel tempo

Il report Costo di S3 nel tempo offre una suddivisione dei costi di Amazon Simple Storage Service (S3) per bucket nel corso del tempo per un intervallo di tempo specificato. Il report consente di individuare i bucket che rappresentano i principali driver di costo e mostra le tendenze di uso e di spesa di S3.

### <a name="s3-distribution-of-cost-report"></a>Report Distribuzione dei costi S3

Usare il report per analizzare i costi di S3 relativi all'ultimo mese per bucket e classe di archiviazione. È possibile usare il grafico a torta per impostare la soglia di visibilità. In alternativa, è possibile usare la visualizzazione a tabella per visualizzare i subtotali.

### <a name="s3-bucket-properties-report"></a>Report Proprietà del bucket S3

Usare il report per visualizzare le proprietà del bucket S3. È possibile usare il grafico a torta per impostare la soglia di visibilità. In alternativa, è possibile usare la visualizzazione a tabella per visualizzare i subtotali.

### <a name="rds-instances-over-time-report"></a>Report Istanze RDS nel tempo

Usare il report per visualizzare una suddivisione di tutte le istanze di servizio di database relazionale di Amazon (RDS) in esecuzione durante un periodo specificato.

### <a name="rds-active-instances-report"></a>Report Istanze RDS attive

Usare il report per analizzare le istanze RDS attive. Nel report espandere la voce per visualizzare informazioni aggiuntive.

### <a name="azure-reserved-instances-report"></a>Report Istanze riservate di Azure

Il report Istanze riservate di Azure offre una singola visualizzazione di tutte le istanze riservate di Azure. Questo report mostra ogni acquisto come singola voce. Il report mostra anche i dettagli sull'acquisto, ad esempio l'account che ha effettuato l'acquisto, il tipo di acquisto e il tipo di istanza, i giorni rimanenti e così via. È possibile visualizzare o nascondere i dati del report usando Mostra/nascondi campi.

Usare il report Istanze riservate di Azure per visualizzare:

- Un elenco di tutte le prenotazioni per data di acquisto.
- Il tempo rimanente fino alla scadenza dell'istanza riservata.
- Le tariffe una tantum.
- L'account che ha acquistato le istanze riservate e la data di acquisto.

### <a name="aws-reserved-instances-report"></a>Report Istanze riservate di AWS

Il report Istanze riservate di AWS offre una singola visualizzazione di tutte le istanze riservate di AWS. Questo report mostra ogni acquisto come una singola voce e i dettagli sull'acquisto, ad esempio l'account che ha effettuato l'acquisto, il tipo di acquisto e il tipo di istanza, i giorni rimanenti e così via. È possibile visualizzare o nascondere i dati del report usando Mostra/nascondi campi.

Usare il report Istanze riservate di AWS per visualizzare:

- Un elenco di tutte le prenotazioni per data di acquisto.
- Il tempo rimanente fino alla scadenza dell'istanza riservata.
- Le tariffe una tantum.
- L'ID dell'acquisto originale, ovvero l'ID di prenotazione.
- L'account che ha acquistato le istanze riservate e la data di acquisto.

### <a name="ec2-ri-buying-recommendations-report"></a>Report Consigli per l'acquisto dell'istanza riservata EC2

La base dell'uso delle risorse cloud è il modello su richiesta, in cui le risorse vengono addebitate solo se usate. L'utente non prende impegni in anticipo, paga solo per le risorse usate, quando le usa.

AWS offre un modello di determinazione dei prezzi alternativo per i servizi Elastic Compute Cloud (EC2), ovvero l'istanza riservata. Questo modello di determinazione dei prezzi garantisce agli utenti la capacità, ogni volta che ne hanno bisogno per la durata dell'istanza riservata. L'istanza riservata offre sconti significativi rispetto al prezzo su richiesta. Gli utenti in cambio, si impegnano in anticipo a usare un'istanza virtuale. L'impegno è associato a famiglia, dimensioni, zona di disponibilità e sistema operativo specifici nel periodo di durata dell'impegno, uno o tre anni. L'istanza riservata consente ad AWS di pianificare in modo efficiente la capacità futura, nonché di ottenere l'impegno dei clienti a usarne i servizi.

Esistono tre opzioni di pagamento per le istanze riservate, tutte stabilite in anticipo:

- Somma totale il giorno 0, che offre lo sconto più alto
- Nessun pagamento anticipato, in cui il costo dell'istanza riservata viene pagato in rate mensili per tutta la durata dell'istanza riservata, offre lo sconto più basso
- Pagamento parziale anticipato, in cui un quarto o la metà del prezzo viene pagato in anticipo e il resto in rate mensili, con un tasso di sconto che è inferiore, ma si avvicina, al tasso di sconto del pagamento in anticipo

Cloudyn valuta il tempo di attività di ogni computer negli ultimi 30 giorni. Cloudyn consiglia l'acquisto delle istanze riservate quando è più conveniente eseguire il computer con un'istanza riservata rispetto al tempo di attività corrente.

Il report motiva i propri consigli per risparmiare più denaro possibile durante l'anno. I consigli suggeriscono di sostituire le istanze su richiesta con le istanze riservate. È possibile acquistare le istanze riservate direttamente dal report.

Ogni scheda si apre come un report completo. Le sezioni rilevanti delle schede includono:

- **EC2 RI Purchase Impact** (Impatto dell'acquisto di istanze riservate EC2): questa sezione offre una simulazione della differenza tra le istanze riservate e le istanze su richiesta. Fare clic su **Zoom avanti** per visualizzare il report Impatto dell'acquisto delle istanze riservate EC2 completo con i filtri già definiti per i consigli. Questo report mostra l'impatto dell'acquisto di tutti i potenziali acquisti di istanze riservate. È possibile modificare il tempo di attività medio previsto per visualizzare il potenziale risparmio quando si acquistano le istanze riservate EC2.

- **Saving Analysis** (Analisi di risparmio): questa sezione include i possibili risparmi ottenuti e il mese in cui i risparmi vengono resi effettivi quando si seguono i consigli di Cloudyn. I risparmi effettivi e la percentuale risparmiata sono evidenziati in rosso.

- **EC2 RI Type Comparison** (Confronto dei tipi di istanza riservata EC2): questa sezione enfatizza i ritorni sugli investimenti evidenziati della distribuzione consigliata di Cloudyn, incluse tutte le opzioni pertinenti. I risultati di questo report presuppongono che il computer sia in esecuzione al 100% di attività. Fare clic su **Zoom avanti** per aprire il report dettagliato.

- **Instances Over Time** (Istanze nel tempo): questa sezione mostra una suddivisione di tutte le istanze associate ai consigli, istanze su richiesta, istanze riservate e istanze spot. Fare clic su **Zoom avanti** per aprire il report dettagliato.
- **Breakeven Points** (Punti di pareggio): questa sezione mostra una tabella di tutte le possibili distribuzioni consigliate, del ROI e del mese in cui questo si verifica. Fare clic su **Zoom avanti** per aprire il report dettagliato.

### <a name="ec2-reservations-over-time-report"></a>Report Prenotazioni EC2 nel tempo

Il report Prenotazioni EC2 nel tempo traccia lo stato d'uso delle istanze riservate EC2 acquistate. È possibile impostare la risoluzione del report per ora, giorno o settimana.

Usare il report per:

- Visualizzare le prenotazioni acquistate, sia quelle usate che quelle non usate.
- Approfondire la risoluzione per ora per visualizzare l'uso dell'istanza riservata per ora.

### <a name="savings-over-time-report"></a>Report Risparmi nel tempo

Usare il report Risparmi nel tempo per visualizzare i risparmi ottenuti con le istanze riservate e con le istanze spot. Il report mostra il ROI ottenuto nel corso del tempo derivante dagli acquisti di istanze riservate.

Per visualizzare il risparmio delle istanze riservate, raggruppare i risultati per **modello di prezzo** e selezionare **Prenotazione**. Per visualizzare i risparmi delle istanze riservate ottenuti da un tipo di istanza o da un tipo di account specifico, aggiungere il filtro e il raggruppamento pertinente al tipo di account o di istanza.

Per vedere i risparmi derivati dall'uso dell'istanza spot, applicare il filtro **Spot** a **Modello di prezzo**. Il filtro predefinito per questo report è l'istanza riservata e le istanze spot.

### <a name="rds-ri-buying-recommendations-report"></a>Report Consigli per l'acquisto dell'istanza riservata RDS

Il report Consigli per l'acquisto dell'istanza riservata RDS consiglia quando usare le istanze riservate RDS anziché le istanze su richiesta.

Ogni scheda si apre come un report completo. Le sezioni rilevanti delle schede includono:

- **RDS RI Purchase Impact** (Impatto dell'acquisto di istanze riservate RDS): questa sezione offre una simulazione della differenza tra le istanze riservate e le istanze su richiesta. Fare clic su **Zoom avanti** per visualizzare il report sull'impatto dell'acquisto delle istanze riservate RDS completo con i filtri già definiti per i consigli. Questo report consente di vedere l'impatto dell'acquisto di tutti i potenziali acquisti di istanze riservate.  È possibile modificare la media del tempo di attività previsto e visualizzare il potenziale risparmio dovuto all'acquisto di istanze riservate.
- **Saving Analysis** (Analisi di risparmio): questa sezione include i possibili risparmi ottenuti e il mese in cui i risparmi vengono resi effettivi quando si seguono i consigli di Cloudyn. I risparmi effettivi e la percentuale risparmiata sono evidenziati in rosso.

- **RDS RI Type Comparison** (Confronto dei tipi di istanza riservata RDS): questa sezione enfatizza i ROI evidenziati della distribuzione consigliata, incluse tutte le opzioni pertinenti. I risultati di questo report presuppongono che il computer sia in esecuzione al 100% di attività. Fare clic su **Zoom avanti** per aprire il report dettagliato per il computer selezionato.
- **Instances Over Time** (Istanze nel tempo): questa sezione mostra una suddivisione di tutte le istanze associate ai consigli, istanze su richiesta, istanze riservate e istanze spot. Fare clic su **Zoom avanti** per aprire il report dettagliato.

- **Breakeven Points** (Punti di pareggio): questa sezione mostra una tabella di tutte le possibili distribuzioni consigliate, del ROI e del mese in cui questo si verifica. Fare clic su **Zoom avanti** per aprire il report dettagliato.

### <a name="rds-reservations-over-time-report"></a>Report Prenotazioni RDS nel tempo

Usare il report Prenotazioni RDS nel tempo per visualizzare una suddivisione delle prenotazioni usate e non usate durante il periodo specificato.

### <a name="reserved-instance-purchase-impact-report"></a>Report Impatto dell'acquisito sull'istanza riservata

Il report Impatto dell'acquisito sull'istanza riservata EC2 consente di simulare il costo dell'istanza riservata rispetto al costo dell'istanza su richiesta nel tempo. Consente di fare acquisti più consapevoli. Modificare i filtri, quali quelli relativi a runtime medio, termine, piattaforma e altri per prendere decisioni informate quando si considerano gli acquisti dell'istanza riservata.

### <a name="cost-effective-sizing-recommendations-report"></a>Report Consigli sul ridimensionamento conveniente

Il report Consigli sul ridimensionamento conveniente offre risultati per AWS e Azure. Per gli utenti AWS, vengono presi in considerazione gli acquisti dell'istanza riservata e i risultati non includono l'esecuzione dei computer come istanza riservata. Questo report offre un elenco di istanze non usate completamente che sono idonee al ridimensionamento. I consigli si basano sui dati relativi all'uso e alle prestazioni degli ultimi 30 giorni. Ogni consiglio contiene un elenco di candidati da ridimensionare, il motivo del ridimensionamento e un collegamento per visualizzare i dettagli completi e le metriche delle prestazioni dell'istanza. I consigli indicano anche quando passare a tipi di istanza di nuova generazione.

È possibile scaricare l'elenco di ID istanza che è consigliabile ridimensionare da questo report. Per scaricare gli ID istanza, usare il report Tutti i consigli di ridimensionamento.

Si consideri l'esempio di ridimensionamento seguente:

L'utente ha sei istanze m3.xlarge in esecuzione. L'analisi di Cloudyn mostra che cinque di queste presentano un basso uso della CPU. È consigliabile ridimensionarle.

Nel report Impatto sui costi viene calcolato l'impatto sui costi. In questo esempio, espandendo la voce, è possibile visualizzare che il prezzo corrente per un'istanza m3.xlarge (Linux/Unix) è di $ 0,266 all'ora e che un'istanza m3.large (Linux/Unix) costa $ 0,133 all'ora. Pertanto, il costo annuo è di $ 11.651 per cinque istanze m3.xlarge in esecuzione e usate al 100%. Il costo annuo è di $ 5.825 per cinque istanze m3.large in esecuzione e usate al 100%. Il risparmio potenziale è $ 5.825.

Per visualizzare i motivi del ridimensionamento conveniente, fare clic su + per espandere la voce. In **Dettagli**:

- La sezione **Recommendation Justification** (Motivo consiglio) mostra la distribuzione attuale e il numero di istanze per cui si consiglia il ridimensionamento.
- La sezione **Cost Impact** (Impatto sui costi) mostra il calcolo usato per determinare i potenziali risparmi.
- La sezione **Potential Annual Savings** (Potenziali risparmi annuali) mostra i potenziali risparmi annuali quando si effettua il ridimensionamento in base ai consigli di Cloudyn.

### <a name="all-sizing-recommendations-report"></a>Report Tutti i consigli di ridimensionamento

Questo report offre un elenco di istanze non usate completamente che sono idonee al ridimensionamento. I consigli si basano sui dati relativi all'uso e alle prestazioni degli ultimi 30 giorni. Ogni consiglio consente di visualizzare i dettagli completi e le metriche delle prestazioni dell'istanza.

Se l'utente ha acquistato istanze riservate di AWS, questo report contiene i risultati per tutte le istanze in esecuzione, incluse le istanze in esecuzione come istanze riservate.

Usare il report Tutti i consigli di ridimensionamento per:

- Visualizzare un elenco di tutte le istanze che sono candidate al ridimensionamento.
- Esportare un elenco di report contenente i nomi e gli ID dell'istanza.

Per visualizzare i dettagli del consiglio per un'istanza specifica, fare clic su **+** per espandere i dettagli. La sezione Dettagli raccomandazione offre una panoramica del consiglio.

La sezione **Tag** offre l'elenco delle chiavi e dei valori di tag per l'istanza selezionata. Usare i tag nel riquadro sinistro per filtrare la sezione.

La sezione **Utilizzo CPU** offre informazioni sull'uso della CPU per l'istanza nell'ultimo mese, per giorno.

Fare clic sul grafico per eseguire il drill-down e aprire il report sulla CPU dell'istanza nel tempo per visualizzare una suddivisione delle istanze.

- Usare **Mostra/nascondi campi** per aggiungere o rimuovere i campi: Timestamp, CPU media, Min CPU (CPU min), Max CPU (CPU max).
- Usare **Intervallo di date** per immettere una data o un intervallo di date e analizzare un ID di istanza specifico.
- Usare **Extended Filters** (Filtri estesi) per mostrare tutte o un ID dell'istanza specifica
- Fare clic su **Zoom avanti** per aprire il Report sull'uso della CPU

Se l'istanza non è ancora stata monitorata per 30 giorni, vengono visualizzati dati incompleti.

La sezione **Utilizzo memoria (GB)** contiene informazioni sulla quantità di memoria usata. Per gli utenti di AWS, le metriche della memoria non sono automaticamente disponibili e devono essere aggiunte per ogni istanza in AWS. AWS prevede dei costi per abilitare le metriche della memoria per le istanze di EC2.

La sezione **Utilizzo memoria (%)** mostra la percentuale di memoria usata.

La sezione **Network Input Traffic** (Traffico di rete in ingresso) consente di visualizzare uno snapshot nel tempo del traffico di rete, medio e massimo, per l'istanza selezionata. Passare il cursore del mouse sulle voci per vedere la data e il traffico massimo per quell'ora. Fare clic su **Zoom avanti** per aprire il report Traffico di rete in ingresso.

La sezione **Network Output Traffic** (Traffico di rete in uscita) consente di visualizzare uno snapshot del traffico di rete in uscita per l'istanza selezionata. Passare il cursore del mouse sulle voci per vedere la data e il traffico massimo per quell'ora. Fare clic su **Zoom avanti** per aprire il report Traffico di rete in uscita.

### <a name="instance-metrics-explorer-report"></a>Report Esplora metriche dell'istanza

Il report Esplora metriche dell'istanza mostra le metriche delle prestazioni tra cloud per ogni istanza. Usare il report per visualizzare le istanze che vengono usate in modo eccessivo e in modo insufficiente in base a CPU, memoria e soglie delle metriche di rete.

Per visualizzare le prestazioni tra cloud per ogni istanza:

1. In **Intervallo di date** selezionare un intervallo di date per il quale si desidera visualizzare le prestazioni.
2. In **Tag** selezionare i tag che si desidera visualizzare.
3. In **Filtri** selezionare i filtri che si desidera visualizzare nel report.
4. In **Extended Filters** (Filtri estesi) modificare le soglie del report per:
    - CPU media
    - Max CPU (CPU max)
    - Avg Memory (Memoria media)
    - Memoria massima
5. In **Extended Filters** (Filtri estesi) fare clic su **Mostra** e quindi selezionare il tipo di istanze da visualizzare.

Per visualizzare le metriche di un'istanza specifica nel tempo:

- Passare al report Esplora metriche dell'istanza e fare clic su **+** per visualizzare i dettagli.

### <a name="rds-sizing-recommendations-report"></a>Report Consigli di ridimensionamento RDS

Il report Consigli di ridimensionamento RDS offre consigli di ridimensionamento RDS per ottimizzare l'uso del cloud. Offre un elenco di istanze non usate in pieno che sono candidate al ridimensionamento. I consigli di Cloudyn si basano sui dati relativi all'uso e alle prestazioni degli ultimi 30 giorni. È possibile filtrare i consigli in base a nome dell'account, area, tipo di istanza e stato.

### <a name="sizing-threshold-manager-report"></a>Report Gestione ridimensionamento della soglia

I consigli di ridimensionamento predefiniti di Cloudyn vengono calcolati usando un algoritmo complesso per dare consigli di ridimensionamento accurati. È possibile modificare le soglie per i consigli di ridimensionamento.

Per regolare manualmente i consigli sul ridimensionamento della soglia:

1. In Sizing Threshold Manager (Gestione ridimensionamento soglia) modificare le soglie seguenti nel modo desiderato:
    - Average CPU % (% media CPU)
    - % massima di CPU
    - Average Memory % (% media memoria)
    - % massima di memoria
3. Fare clic su **Applica** per salvare le modifiche.
4. Le modifiche vengono applicate immediatamente a tutti i consigli.

Per ripristinare le soglie predefinite:

- In Sizing Threshold Manager (Gestione ridimensionamento soglia) fare clic su **Ripristina impostazioni predefinite**.

### <a name="compute-instance-types-report"></a>Report Tipi di istanza di calcolo

Usare il report Tipi di istanza per:

- Visualizzare i tipi di istanza per servizio, famiglia, nome dell'API e nome.
- Visualizzare dettagli quali CPU, ECU, RAM e rete.

È possibile usare **Cerca** per cercare voci specifiche.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare i report, incluse le procedure per personalizzare e salvare o pianificare i report, vedere [Informazioni sui report dei costi](understanding-cost-reports.md).
- Informazioni sui dashboard inclusi in Cloudyn e su come creare i dashboard personalizzati, vedere [Visualizzare le metriche di costo chiave con i dashboard](dashboards.md).
