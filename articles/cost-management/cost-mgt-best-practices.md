---
title: Ottimizzare gli investimenti per il cloud con Gestione costi di Azure | Microsoft Docs
description: Questo articolo consente di ottenere il massimo valore dagli investimenti per il cloud, ridurre i costi e valutare come viene speso il denaro.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/09/2018
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: ''
ms.openlocfilehash: 0adc821f7a3d5b059d549509b7ed18cda95b86bc
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2018
ms.locfileid: "52273800"
---
# <a name="how-to-optimize-your-cloud-investment-with-azure-cost-management"></a>Come ottimizzare gli investimenti per il cloud con Gestione costi di Azure

Gestione costi di Azure offre gli strumenti per pianificare, analizzare e ridurre la spesa per poter ottimizzare gli investimenti per il cloud. Questo documento offre un approccio metodico alla gestione dei costi ed evidenzia gli strumenti disponibili per risolvere le problematiche legate ai costi dell'organizzazione. Con Azure la creazione e la distribuzione di soluzioni cloud sono davvero semplici. È tuttavia importante che queste soluzioni siano ottimizzate per ridurre al minimo il costo per l'organizzazione. Seguire i principi delineati in questo documento e usare gli strumenti illustrati per assicurare il successo dell'organizzazione.

## <a name="methodology"></a>Metodologia

La gestione dei costi è un problema che l'azienda deve affrontare e risolvere in modo definitivo prima di iniziare a sostenere i costi per le risorse cloud. Per implementare in modo efficiente la gestione dei costi e ottimizzarli, l'organizzazione deve:

- Preparare gli strumenti giusti per avere successo
- Essere responsabile dei costi
- Intraprendere le azioni appropriate per ottimizzare le spese

I tre gruppi principali descritti di seguito devono essere allineati nell'organizzazione per implementare una gestione efficiente dei costi.

- **Finanza**: persone responsabili dell'approvazione delle richieste di budget in tutta l'organizzazione sulla base delle previsioni di spesa per il cloud. Saldano le fatture corrispondenti e assegnano i costi ai vari team per incrementare l'affidabilità.
- **Manager**: responsabili delle decisioni aziendali di un'organizzazione, che devono conoscere la spesa per il cloud per trovare i risultati di spesa ottimali.
- **Team delle app**: tecnici che gestiscono ogni giorno le risorse cloud, sviluppando i servizi necessari per soddisfare le esigenze dell'organizzazione. Questi team necessitano di flessibilità per ottenere il massimo dai budget definiti.

### <a name="key-principles"></a>Principi chiave

Usare i principi descritti di seguito per ottimizzare la gestione dei costi per il cloud nell'organizzazione.

#### <a name="planning"></a>Pianificazione

Una pianificazione iniziale completa consente di adattare l'utilizzo del cloud ai requisiti aziendali specifici. È importante chiedersi:

- Quale problema aziendale si deve risolvere?
- Quali modelli di utilizzo sono previsti per le risorse?

Le risposte saranno utili per selezionare le offerte appropriate, perché determinano l'infrastruttura da usare e come usarla per ottimizzare l'efficienza di Azure.

#### <a name="visibility"></a>Visibilità

Una gestione dei costi ben strutturata consente di fornire alle persone informazioni sui costi di Azure di cui sono responsabili o sul denaro speso. Azure comprende servizi progettati per offrire informazioni dettagliate sulle *destinazioni* di spesa. Questi strumenti offrono diversi vantaggi, ad esempio consentono di trovare le risorse sottoutilizzate, eliminare gli sprechi e ottimizzare le opportunità di risparmio sui costi.

#### <a name="accountability"></a>Affidabilità

Attribuire i costi all'interno dell'organizzazione per assicurarsi che le persone siano responsabili delle spese del proprio team. Per comprendere pienamente le spese dell'organizzazione per Azure, è consigliabile organizzare le risorse per ottimizzare l'analisi dell'attribuzione dei costi. Una buona organizzazione aiuta a gestire e ridurre i costi e a responsabilizzare le persone per quanto riguarda l'efficienza delle spese nell'organizzazione.

#### <a name="optimization"></a>Ottimizzazione

Impegnarsi per ridurre le spese basandosi sulle conclusioni raggiunte grazie alla pianificazione e alla maggiore visibilità sui costi. Prendere in considerazione la possibilità di ottimizzare acquisti e gestione delle licenze e di implementare le modifiche alla distribuzione dell'infrastruttura, esaminate più avanti in questo documento.

#### <a name="iteration"></a>Iterazione

Tutti quelli che fanno parte dell'organizzazione devono essere coinvolti nel ciclo di vita della gestione dei costi. Il loro coinvolgimento deve essere continuativo per poter ottimizzare i costi. Questo processo iterativo deve essere applicato in modo rigoroso e deve diventare un elemento fondamentale per una governance del cloud responsabile all'interno dell'organizzazione.

![Principi chiave](./media/cost-mgt-best-practices/principles.png)

## <a name="plan-with-cost-in-mind"></a>Pianificare tenendo presenti i costi

Prima di distribuire le risorse cloud, valutare gli elementi seguenti:

- L'offerta di Azure che meglio soddisfa le proprie esigenze
- Le risorse che si prevede di usare
- Quanto potrebbero costare

Azure offre strumenti per semplificare il processo di valutazione. Gli strumenti consentono di avere una buona idea dell'investimento necessario per abilitare i carichi di lavoro. È quindi possibile selezionare la configurazione migliore per la situazione specifica.

### <a name="azure-onboarding-options"></a>Opzioni di onboarding di Azure

Il primo passaggio per ottimizzare l'esperienza con Gestione costi consiste nell'analizzare e scegliere l'offerta di Azure più adatta. Pensare a come si prevede di usare Azure in futuro. Considerare anche come si vuole configurare il modello di fatturazione. Porsi le domande seguenti prima di decidere:

- Per quanto tempo si prevede di usare Azure? Si tratta di un test oppure si intende creare un'infrastruttura a lungo termine?
- Quanto si è disposti a pagare per Azure? È meglio pagare in anticipo un prezzo ridotto o ricevere una fattura alla fine del mese?

Per altre informazioni sulle diverse opzioni, vedere [Come acquistare Azure](https://azure.microsoft.com/pricing/purchase-options/). Molti dei modelli di fatturazione più comuni sono indicati di seguito.

#### <a name="freehttpsazuremicrosoftcomfree"></a>[Free](https://azure.microsoft.com/free/)

- 12 mesi dei servizi gratuiti più diffusi
- 200 dollari di credito per esplorare i servizi per 30 giorni
- Più di 25 servizi sempre gratuiti

#### <a name="pay-as-you-gohttpsazuremicrosoftcomoffersms-azr-0003p"></a>[Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p)

- Nessun pagamento minimo o impegno
- Prezzi competitivi
- Pagamento in base all'utilizzo
- Massima libertà di annullamento

#### <a name="enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement"></a>[Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)

- Opzioni per gli impegni monetari iniziali
- Possibilità di pagare Azure a prezzo ridotto

## <a name="estimate-the-cost-of-your-solution"></a>Stimare il costo della soluzione

Prima di distribuire qualsiasi infrastruttura, valutare quanto costerà la soluzione. La valutazione aiuterà a creare un budget iniziale per l'organizzazione per il carico di lavoro. Sarà quindi possibile usare il budget nel tempo per valutare la validità della stima iniziale e confrontarla con il costo effettivo della soluzione distribuita.

### <a name="azure-pricing-calculator"></a>Calcolatore prezzi di Azure

Il calcolatore prezzi di Azure consente di creare diverse combinazioni di servizi di Azure per visualizzare una stima dei costi. È possibile implementare la soluzione in Azure in modi diversi, ognuno dei quali potrebbe influire sulla spesa complessiva. Considerando in anticipo tutte le esigenze di infrastruttura della distribuzione cloud, è possibile usare lo strumento nel modo più efficiente possibile. È possibile realizzare una stima attendibile delle spese previste in Azure.

Per altre informazioni, vedere [Calcolatore prezzi di Azure](https://azure.microsoft.com/pricing/calculator).

### <a name="azure-migrate"></a>Azure Migrate

Azure Migrate è un servizio che valuta i carichi di lavoro correnti dell'organizzazione nei data center locali. Offre informazioni su ciò che potrebbe essere necessario per una soluzione sostitutiva di Azure. In primo luogo, Migrate analizza i computer locali per determinare se la migrazione è fattibile, quindi suggerisce il dimensionamento delle macchine virtuali in Azure per ottimizzare le prestazioni. Infine, crea anche una stima dei costi per una soluzione basata su Azure.

Per altre informazioni, vedere [Azure Migrate](../migrate/migrate-overview.md).

## <a name="analyze-and-manage-your-costs"></a>Analizzare e gestire i costi

Rimanere informati su come i costi dell'organizzazione evolvono nel tempo. Usare le tecniche seguenti per conoscere e gestire le spese correttamente.

### <a name="organize-and-tag-your-resources"></a>Organizzare e contrassegnare le risorse

Organizzare le risorse tenendo in considerazione i costi. Quando si creano sottoscrizioni e gruppi di risorse, considerare i team responsabili dei costi associati. Assicurarsi che la creazione di report tenga presente l'organizzazione. Le sottoscrizioni e i gruppi di risorse sono strumenti adatti per organizzare e attribuire le spese all'interno dell'organizzazione. I tag offrono un modo valido per attribuire i costi. È possibile usare i tag come filtro e per creare raggruppamenti quando si analizzano i dati e si esaminano i costi. I clienti con contratto Enterprise possono anche creare reparti in cui inserire le sottoscrizioni. L'organizzazione basata sui costi in Azure consente di fare in modo che le persone rilevanti all'interno dell'organizzazione continuino a essere responsabili della riduzione delle spese del team.

### <a name="use-cost-analysis"></a>Usare l'analisi dei costi

Analisi dei costi consente di analizzare in modo approfondito i costi aziendali usando le proprietà delle risorse standard. Rispondere alle domande frequenti seguenti per eseguire un'analisi di base. Rispondendo periodicamente a queste domande sarà possibile rimanere informati e prendere le decisioni necessarie prestando più attenzione ai costi.

- **Costi stimati per il mese corrente**: quante spese sono state sostenute finora questo mese? Il budget verrà rispettato?
- **Esaminare le anomalie**: eseguire controlli di routine per assicurarsi che i costi rimangano entro una fascia ragionevole per il normale utilizzo. Quali sono le tendenze? Sono presenti outlier?
- **Riconciliazione di fatture**: gli ultimi costi fatturati sono superiori a quelli del mese precedente? Come sono cambiate le abitudini di spesa da un mese all'altro?
- **Chargeback interno**: ora che si conosce l'ammontare degli addebiti, come si possono suddividere tali addebiti per l'organizzazione?

Per altre informazioni, vedere l'[analisi dei costi](quick-acm-cost-analysis.md).

### <a name="export-billing-data-on-a-schedule"></a>Esportare i dati di fatturazione in base a una pianificazione

Se è necessario importare i dati di fatturazione in un sistema esterno, ad esempio un dashboard o un sistema finanziario, è possibile pianificare report automatizzati ogni giorno per evitare di scaricare manualmente i file ogni mese. È anche possibile esportare i dati di fatturazione in un account di archiviazione di Azure e ricevere una notifica tramite i [gruppi di azioni](../monitoring-and-diagnostics/monitoring-action-groups.md). È quindi possibile usare i dati di Azure combinandoli con i dati personalizzati e usandoli nei propri sistemi.

Per altre informazioni sull'esportazione dei dati di fatturazione, vedere [Creare e gestire dati esportati](tutorial-export-acm-data.md).

### <a name="create-budgets"></a>Creare i budget

Dopo aver identificato e analizzato i modelli di spesa, è importante iniziare a impostare i limiti per sé e per i team. I budget di Azure consentono di impostare un budget basato sull'utilizzo o sui costi con più soglie e avvisi. Assicurarsi di rivedere regolarmente i budget creati per verificare lo stato del burn-down dei budget e apportare le modifiche necessarie. I budget di Azure consentono anche di configurare un trigger di automazione quando viene raggiunta una determinata soglia di budget. È ad esempio possibile configurare il servizio per arrestare le macchine virtuali oppure spostare l'infrastruttura in un altro piano tariffario in risposta a un trigger di budget.

Per altre informazioni, vedere [Budget di Azure](tutorial-acm-create-budgets.md).

Per altre informazioni sull'automazione basata sui budget, vedere [Automazione basata sui budget](../billing/billing-cost-management-budget-scenario.md).

## <a name="act-to-optimize"></a>Realizzare l'ottimizzazione
Ottimizzare la spesa nei modi seguenti.

### <a name="cut-out-waste"></a>Eliminare gli sprechi

Dopo aver distribuito l'infrastruttura in Azure, è importante assicurarsi che sia in uso. Il modo più semplice per iniziare subito a risparmiare è analizzare le risorse e rimuovere quelle non in uso e quindi fare in modo che le altre siano usate nel modo più efficiente possibile.

#### <a name="azure-advisor"></a>Azure Advisor

Azure Advisor è un servizio che, tra le altre cose, identifica le macchine virtuali con un utilizzo ridotto della CPU o della rete. In questo modo è possibile decidere di arrestare o ridimensionare i computer in base al costo previsto per continuare a eseguirli. Advisor fornisce anche consigli per l'acquisto di istanze riservate. I consigli si basano sugli ultimi 30 giorni di utilizzo delle macchine virtuali. I consigli, se messi in pratica, consentono di ridurre le spese.

Per altre informazioni, vedere [Azure Advisor](../advisor/advisor-overview.md).

### <a name="size-your-vms-properly"></a>Ridimensionare le macchine virtuali in modo appropriato

Il dimensionamento delle macchine virtuali ha un impatto significativo sul costo complessivo di Azure. Il numero di macchine virtuali necessarie in Azure potrebbe non equivalere a quelle attualmente distribuite in un data center locale. Assicurarsi di scegliere le dimensioni adatte per i carichi di lavoro che si intende eseguire.

Per altre informazioni, vedere [Azure IaaS: proper sizing and cost](https://azure.microsoft.com/resources/videos/azurecon-2015-azure-iaas-proper-sizing-and-cost/) (IaaS di Azure: dimensionamento corretto e costi).

### <a name="use-purchase-discounts"></a>Usare gli sconti per gli acquisti

Azure offre diversi sconti che l'organizzazione può sfruttare per risparmiare denaro.

#### <a name="azure-reservations"></a>Prenotazioni di Azure

Le prenotazioni di Azure consentono di effettuare il pagamento anticipato della capacità di calcolo delle macchine virtuali o dei database SQL per un periodo di uno o tre anni. Il pagamento anticipato consentirà di ottenere uno sconto sulle risorse usate. Le prenotazioni di Azure possono ridurre in modo significativo i costi di calcolo delle macchine virtuali o dei database SQL, ovvero fino al 72% rispetto ai prezzi con pagamento in base al consumo, a fronte di un impegno anticipato di uno o tre anni. Le prenotazioni offrono uno sconto a livello di fatturazione e non hanno alcuna ripercussione sullo stato di runtime delle macchine virtuali o dei database SQL.

Per altre informazioni, vedere [Informazioni sulle prenotazioni di Azure](../billing/billing-save-compute-costs-reservations.md).

#### <a name="use-azure-hybrid-benefit"></a>Usare il vantaggio Azure Hybrid

Se si hanno già licenze di Windows Server o SQL Server nelle distribuzioni locali, è possibile usare il programma Vantaggio Azure Hybrid per risparmiare in Azure. Con il vantaggio per Windows Server, ogni licenza copre il costo del sistema operativo (fino a un massimo di due macchine virtuali) e si pagano semplicemente i costi di calcolo di base. È possibile usare le licenze esistenti di SQL Server per risparmiare fino al 55% sulle opzioni di database SQL basate su vCore. Le opzioni includono SQL Server in Macchine virtuali di Azure e SQL Server Integration Services.

Per altre informazioni, vedere [Calcolatore del risparmio generato dal vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/).

### <a name="other-resources"></a>Altre risorse:

Azure offre anche un servizio che consente di creare servizi che sfruttano la capacità in eccedenza in Azure per ridurre le tariffe. Per altre informazioni, vedere [Usare le macchine virtuali con priorità bassa in Batch](../batch/batch-low-pri-vms.md).

## <a name="next-steps"></a>Passaggi successivi
- Se non si ha familiarità con Gestione costi, vedere [Che cos'è la gestione dei costi di Azure?](overview-cost-mgt.md) per informazioni su come consente di monitorare e controllare le spese di Azure e di ottimizzare l'uso delle risorse.
