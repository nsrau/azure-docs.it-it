---
title: Guida introduttiva - Esplorare i costi di Azure con l'analisi dei costi | Microsoft Docs
description: Questo guida introduttiva consente di usare l'analisi dei costi per esplorare e analizzare i costi aziendali di Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/10/2018
ms.topic: quickstart
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 12b7a605350b07565660e9e4d1334b286aa5ac00
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079107"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Guida introduttiva: Esplorare e analizzare i costi di Azure con l'analisi dei costi

Per poter controllare al meglio e ottimizzare i costi di Azure, è necessario conoscere l'origine dei costi all'interno dell'organizzazione. È anche utile sapere quanto costano i servizi e quali ambienti e sistemi supportano. È fondamentale avere visibilità sul dettaglio dei costi per comprendere in modo approfondito i modelli di spesa aziendali, che possono essere usati per applicare meccanismi di controllo dei costi, ad esempio i budget.

In questa guida introduttiva si usa l'analisi dei costi per esplorare e analizzare i costi aziendali. È possibile visualizzare i costi aggregati per l'organizzazione per capire dove si accumulano i costi nel tempo e identificare le tendenze di spesa. È possibile visualizzare i costi accumulati nel corso del tempo per stimare le tendenze dei costi mensili, trimestrali o addirittura annuali rispetto a un budget. Un budget è utile per rispettare eventuali vincoli finanziari oppure per visualizzare i costi giornalieri o mensili per isolare le irregolarità di spesa. È anche possibile scaricare i dati del report corrente per un'ulteriore analisi o per usarli in un sistema esterno.

In questa guida introduttiva si apprende come:

- Esaminare i costi nell'analisi dei costi
- Personalizzare le visualizzazioni dei costi
- Scaricare i dati dell'analisi dei costi


## <a name="prerequisites"></a>Prerequisiti

L'analisi dei costi è disponibile per tutti i clienti con [Contratto Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/). È necessario avere l'accesso in lettura ad almeno uno degli ambiti seguenti per visualizzare i dati dei costi.

- L'ambito dell'*account di fatturazione* viene definito in https://ea.azure.com e richiede l'accesso come Amministratore aziendale. Non sono previsti altri prerequisiti per le impostazioni del Contratto Enterprise. Le informazioni di fatturazione nell'analisi dei costi vengono consolidate per tutte le sottoscrizioni incluse nel Contratto Enterprise. L'account di fatturazione viene spesso indicato con il termine *Contratto Enterprise* o *Registrazione*.

- L'ambito del *reparto* viene definito in https://ea.azure.com e richiede l'accesso come Amministratore del reparto. È necessario che l'impostazione **DA view charges** (Addebiti visualizzazione amministratore del reparto) sia abilitata nel portale EA. Le informazioni di fatturazione nell'analisi dei costi vengono consolidate per tutte le sottoscrizioni che appartengono all'account di registrazione e sono collegate al reparto.

- L'ambito dell'*account di registrazione* viene definito in https://ea.azure.com e richiede l'accesso come Proprietario dell'account. È necessario che l'impostazione **AO view charges** (Addebiti visualizzazione proprietario dell'account) sia abilitata nel portale EA. Le informazioni di fatturazione nell'analisi dei costi vengono consolidate per tutte le sottoscrizioni che appartengono all'account di registrazione. L'account di registrazione viene spesso indicato come *proprietario dell'account*.

- L'ambito del *gruppo di gestione* viene definito in https://portal.azure.com e richiede l'accesso come Lettore Gestione costi (o Lettore). È necessario che l'impostazione **AO view charges** (Addebiti visualizzazione proprietario dell'account) sia abilitata nel portale EA. Le informazioni di fatturazione nell'analisi dei costi vengono consolidate per tutte le sottoscrizioni sotto il gruppo di gestione.

- L'ambito della *sottoscrizione* viene definito in https://portal.azure.com e richiede l'accesso come Lettore Gestione costi (o Lettore). È necessario che l'impostazione **AO view charges** (Addebiti visualizzazione proprietario dell'account) sia abilitata nel portale EA. Le informazioni di fatturazione nell'analisi dei costi vengono consolidate per tutte le risorse e i gruppi di risorse nella sottoscrizione.

- L'ambito del *gruppo di risorse* viene definito in https://portal.azure.com e richiede l'accesso come Lettore Gestione costi (o Lettore). È necessario che l'impostazione **AO view charges** (Addebiti visualizzazione proprietario dell'account) sia abilitata nel portale EA. Le informazioni di fatturazione nell'analisi dei costi vengono consolidate per tutte le risorse nel gruppo di risorse.



Per altre informazioni sulla configurazione delle impostazioni **DA view charges** (Addebiti visualizzazione amministratore del reparto) e **AO view charges** (Addebiti visualizzazione proprietario dell'account), vedere [Abilitazione dell'accesso ai costi](../billing/billing-enterprise-mgmt-grp-troubleshoot-cost-view.md#enabling-access-to-costs).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

- Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Esaminare i costi nell'analisi dei costi

Per esaminare i costi con analisi dei costi, nel portale di Azure, passare a **Gestione costi + Fatturazione** &gt; **Gestione costi** &gt; **Cambia ambito**, scegliere un ambito e quindi fare clic su **Selezionare**.

L'ambito selezionato viene usato attraverso Gestione costi per fornire il consolidamento dati e per controllare l'accesso alle informazioni sui costi. Quando si usano gli ambiti non vengono multi-selezionati. Invece, si seleziona un ambito più ampio a cui altri utenti si avvicinano e quindi si filtra in basso ciò che si desidera. È importante comprendere questo perché alcuni utenti non devono avere accesso a un ambito padre a cui si avvicinano gli ambiti figlio.

Aprire **Apri Analisi dei costi**.

La visualizzazione dell'analisi dei costi iniziale include le aree seguenti:

**Totale**: visualizza i costi totali per il mese corrente.

**Budget**: visualizza il limite di spesa per l'ambito selezionato, se disponibile.

**Costo accumulato**: visualizza le spese giornaliere accumulate totali, a partire dall'inizio del mese. Dopo aver [creato un budget](tutorial-acm-create-budgets.md) per l'account di fatturazione o la sottoscrizione, è possibile visualizzare rapidamente la tendenza di spesa rispetto al budget. Passare il puntatore del mouse su una data per visualizzare il costo accumulato per quel giorno.

**Grafici pivot (ad anello)** - Offrono pivot dinamici, suddividendo il costo totale in base a un set comune di proprietà standard. Mostrano i costi accumulati per il mese corrente, in ordine decrescente. È possibile cambiare i grafici pivot in qualsiasi momento selezionando un pivot diverso. I costi vengono classificati in base a servizio (categoria del contatore), località (area) e ambito figlio per impostazione predefinita. Ad esempio, gli account di registrazione sotto gli account di fatturazione, i gruppi di risorse sotto le sottoscrizioni e le risorse sotto i gruppi di risorse.

![Visualizzazione iniziale dell'analisi dei costi](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Personalizzare le visualizzazioni dei costi

La visualizzazione predefinita fornisce risposte rapide alle domande più comuni, ad esempio:

- Quanto è stato speso?
- Il budget verrà rispettato?

Esistono tuttavia molti i casi in cui è necessaria un'analisi più approfondita. La personalizzazione inizia nella parte superiore della pagina, con la selezione della data.

Per impostazione predefinita, l'analisi dei costi mostra i dati per il mese corrente. Usare il selettore di data per passare rapidamente all'ultimo mese, al mese corrente, al trimestre di calendario corrente, all'anno di calendario corrente o a un intervallo di date personalizzato di propria scelta. Selezionare l'ultimo mese è il modo più rapido per analizzare la fattura di Azure più recente e riconciliare facilmente le spese. Le opzioni relative a trimestre e anno corrente consentono di tenere traccia dei costi rispetto ai budget a lungo termine. È anche possibile selezionare un intervallo di date diverso, ad esempio, un singolo giorno, gli ultimi sette giorni o un altro periodo fino a un anno prima del mese corrente.

![Selettore date](./media/quick-acm-cost-analysis/date-selector.png)

Per impostazione predefinita, l'analisi dei costi mostra i costi **accumulati**. I costi accumulati includono tutti i costi per ogni singolo giorno oltre a quelli dei giorni precedenti, con una visualizzazione in continua crescita dei costi giornalieri accumulati. Questa visualizzazione è ottimizzata per mostrare la tendenza rispetto a un budget per l'intervallo di tempo selezionato.

È disponibile anche una visualizzazione **giornaliera** che mostra i costi sostenuti ogni giorno. La visualizzazione giornaliera non visualizza una tendenza di incremento. La visualizzazione è progettata per mostrare eventuali irregolarità, ad esempio impennate o flessioni dei costi da un giorno all'altro. Se si seleziona un budget, la visualizzazione giornaliera mostra anche una stima del budget giornaliero. Quando i costi giornalieri sono costantemente al di sopra del budget giornaliero stimato, è probabile che il budget mensile venga superato. Il budget stimato giornaliero è semplicemente un mezzo per poter visualizzare il budget a un livello più basilare. Quando si verificano fluttuazioni nei costi giornalieri, il confronto tra il budget giornaliero stimato e il budget mensile è meno preciso.

![Visualizzazione giornaliera](./media/quick-acm-cost-analysis/daily-view.png)

È possibile usare l'opzione **Raggruppa per** per selezionare una categoria di gruppi e cambiare i dati visualizzati nel grafico ad area del totale nella parte superiore. Il raggruppamento consente di visualizzare rapidamente come la spesa viene organizzata per tipo di risorsa. Ecco una visualizzazione dei costi dei servizi di Azure nell'ultimo mese.

![Visualizzazione cumulativa giornaliera raggruppata](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

I grafici pivot sotto la visualizzazione del totale superiore mostrano visualizzazioni per categorie di raggruppamenti e filtri diverse. Quando si seleziona una categoria di gruppi, il set completo di dati per la visualizzazione del totale è nella parte inferiore della visualizzazione. Di seguito è riportato un esempio per i gruppi di risorse.

![Dati completi per la visualizzazione corrente](./media/quick-acm-cost-analysis/full-data-set.png)

L'immagine precedente mostra i nomi dei gruppi di risorse. I tag di visualizzazione per le risorse non sono disponibili in alcuna visualizzazione di analisi dei costi, nonché in alcun filtro o raggruppamento.

Quando si raggruppano i costi in base a un attributo specifico, vengono visualizzati in ordine decrescente i primi 10 gruppi di risorse che hanno contribuito maggiormente ai costi. Se sono presenti più di 10 gruppi, vengono visualizzati i primi nove gruppi, oltre a un gruppo **Others** (Altri), che riunisce tutti gli altri gruppi.

Le macchine virtuali, le risorse di rete e le risorse di archiviazione *classiche* (Gestione dei servizi di Azure o ASM) non condividono dati di fatturazione dettagliati. Tali risorse vengono unite nel gruppo **Classic services** (Servizi classici) quando si raggruppano i costi.


## <a name="download-cost-analysis-data"></a>Scaricare i dati dell'analisi dei costi

È possibile **scaricare** informazioni dall'analisi dei costi per generare un file CSV per tutti i dati attualmente visualizzati nel portale di Azure. Eventuali filtri o raggruppamenti applicati vengono inclusi nel file. Nel file CSV vengono inclusi anche i dati sottostanti per il grafico del totale della parte superiore, che non sono visualizzati attivamente.

## <a name="next-steps"></a>Passaggi successivi

Passare alla prima esercitazione per apprendere come creare e gestire i budget.

> [!div class="nextstepaction"]
> [Creare e gestire i budget](tutorial-acm-create-budgets.md)
