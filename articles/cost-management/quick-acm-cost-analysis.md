---
title: Avvio rapido - Esplorare i costi di Azure con l'analisi | Microsoft Docs
description: Questo guida introduttiva consente di usare l'analisi dei costi per esplorare e analizzare i costi aziendali di Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: quickstart
ms.service: cost-management
manager: micflan
ms.custom: seodec18
ms.openlocfilehash: f2e1d19f69b426cee870d2ede489b7c458404704
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374779"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Guida introduttiva: Esplorare e analizzare i costi con l'analisi dei costi

Per poter controllare al meglio e ottimizzare i costi di Azure, è necessario conoscere l'origine dei costi all'interno dell'organizzazione. È anche utile sapere quanto costano i servizi e quali ambienti e sistemi supportano. È fondamentale avere visibilità sul dettaglio dei costi per comprendere in modo approfondito i modelli di spesa aziendali, che possono essere usati per applicare meccanismi di controllo dei costi, ad esempio i budget.

In questa guida introduttiva si usa l'analisi dei costi per esplorare e analizzare i costi aziendali. È possibile visualizzare i costi aggregati per l'organizzazione per capire dove si accumulano i costi nel tempo e identificare le tendenze di spesa. È possibile visualizzare i costi accumulati nel corso del tempo per stimare le tendenze dei costi mensili, trimestrali o addirittura annuali rispetto a un budget. Un budget è utile per rispettare eventuali vincoli finanziari oppure per visualizzare i costi giornalieri o mensili per isolare le irregolarità di spesa. È anche possibile scaricare i dati del report corrente per un'ulteriore analisi o per usarli in un sistema esterno.

In questa guida introduttiva si apprende come:

- Esaminare i costi nell'analisi dei costi
- Personalizzare le visualizzazioni dei costi
- Scaricare i dati dell'analisi dei costi


## <a name="prerequisites"></a>Prerequisiti

L'analisi dei costi supporta diversi tipi di account di Azure. Per visualizzare l'elenco completo dei tipi di account supportati, vedere [Understand Cost Management data](understand-cost-mgt-data.md) (Informazioni sui dati di Gestione costi). Per visualizzare i dati relativi ai costi, è necessario effettuare almeno l'accesso in lettura per l'account Azure.

Per informazioni sull'assegnazione dell'accesso ai dati di Gestione costi di Azure, vedere [Assegnare l'accesso ai dati](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

- Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Esaminare i costi nell'analisi dei costi

Per esaminare i costi nell'analisi dei costi, aprire l'ambito nel portale di Azure e scegliere **Analisi dei costi** dal menu. Passare ad esempio a **Sottoscrizioni**, selezionare una sottoscrizione nell'elenco e quindi selezionare **Analisi dei costi** nel menu. Usare l'etichetta **Ambito** per passare a un ambito diverso nell'analisi dei costi. Per altre informazioni sugli ambiti, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).

L'ambito selezionato viene usato in tutto il servizio Gestione costi per fornire il consolidamento dati e per controllare l'accesso alle informazioni sui costi. Quando si usano gli ambiti non vengono multi-selezionati. Si seleziona piuttosto un ambito più ampio, a cui fanno riferimento gli altri, quindi si applica il filtro fino agli ambiti annidati necessari. È importante comprendere questo approccio perché alcuni utenti potrebbero non avere accesso a un singolo ambito padre, che copre più ambiti annidati.

La visualizzazione dell'analisi dei costi iniziale include le aree seguenti.

**Visualizzazione Costi accumulati**: rappresenta la configurazione della visualizzazione predefinita dell'analisi dei costi. Ogni visualizzazione include le impostazioni relative a intervallo di date, granularità, tipo di raggruppamento e filtro. La visualizzazione predefinita mostra i costi accumulati per il periodo di fatturazione corrente, ma è possibile sostituirla con altre visualizzazioni incluse. Per altre informazioni, vedere [Personalizzare le visualizzazioni dei costi](#customize-cost-views).

**Costo effettivo**: mostra il totale dei costi di utilizzo e acquisto per il mese corrente, man mano che vengono accumulati, e che compariranno in fattura.

**Previsione**: mostra il totale dei costi previsti per il periodo di tempo scelto. (La previsione è in anteprima.)

**Budget**: visualizza il limite di spesa per l'ambito selezionato, se disponibile.

**Accumulated granularity** (Granularità accumulata): mostra il totale dei costi giornalieri accumulati, a partire dall'inizio del periodo di fatturazione. Dopo aver [creato un budget](tutorial-acm-create-budgets.md) per l'account di fatturazione o la sottoscrizione, è possibile visualizzare rapidamente la tendenza di spesa rispetto al budget. Passare il puntatore del mouse su una data per visualizzare il costo accumulato per quel giorno.

**Grafici pivot (ad anello)** : offrono pivot dinamici, suddividendo il costo totale in base a un set comune di proprietà standard. Mostrano i costi in ordine decrescente per il mese corrente. È possibile cambiare i grafici pivot in qualsiasi momento selezionando un pivot diverso. I costi vengono classificati in base a servizio (categoria del contatore), località (area) e ambito figlio per impostazione predefinita. Ad esempio, gli account di registrazione sotto gli account di fatturazione, i gruppi di risorse sotto le sottoscrizioni e le risorse sotto i gruppi di risorse.

![Vista iniziale dell'analisi dei costi nel portale di Azure](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Personalizzare le visualizzazioni dei costi

Per l'analisi dei costi sono disponibili quattro visualizzazioni predefinite, ottimizzate per gli obiettivi più comuni:

Visualizza | Risponde a domande di questo tipo
--- | ---
Costi accumulati | Quali sono state finora le spese in questo mese? Il budget verrà rispettato?
Costi giornalieri | Si sono verificati aumenti dei costi giornalieri negli ultimi 30 giorni?
Costo per servizio | Come è cambiato l'utilizzo mensile nelle ultime tre fatture?
Costo per risorsa | Quali risorse sono costate maggiormente finora in questo mese?

![Selettore visualizzazione che mostra una selezione di esempio di questo mese](./media/quick-acm-cost-analysis/view-selector.png)

Esistono tuttavia molti i casi in cui è necessaria un'analisi più approfondita. La personalizzazione inizia nella parte superiore della pagina, con la selezione della data.

Per impostazione predefinita, l'analisi dei costi mostra i dati per il mese corrente. Usare il selettore data per passare rapidamente a intervalli di date comuni, ad esempio gli ultimi sette giorni, l'ultimo mese, l'anno corrente o un intervallo di date personalizzato. Le sottoscrizioni con pagamento in base al consumo includono inoltre intervalli di date basati sul periodo di fatturazione, che non è associato al mese di calendario, ad esempio il periodo di fatturazione corrente o l'ultima fattura. Usare i collegamenti **< INDIETRO** e **AVANTI >** nella parte superiore del menu per passare rispettivamente al periodo precedente o successivo. Ad esempio, **< INDIETRO** consente di passare dagli **ultimi sette giorni** a **8-14 giorni fa** o **15-21 giorni fa**.

![Selettore date che mostra una selezione di esempio di questo mese](./media/quick-acm-cost-analysis/date-selector.png)

Per impostazione predefinita, l'analisi dei costi mostra i costi **accumulati**. I costi accumulati includono tutti i costi per ogni singolo giorno oltre a quelli dei giorni precedenti, con una visualizzazione in continua crescita dei costi giornalieri aggregati. Questa visualizzazione è ottimizzata per mostrare la tendenza rispetto a un budget per l'intervallo di tempo selezionato.

Usare la visualizzazione del grafico di previsione per identificare potenziali violazioni del budget. In caso di potenziale violazione del budget, l'eccesso di spesa previsto viene mostrato in rosso. Nel grafico viene anche visualizzato un indicatore. Passando con il mouse sul simbolo, viene visualizzata la data stimata della violazione del budget.

![Esempio di potenziale violazione del budget](./media/quick-acm-cost-analysis/budget-breach.png)

È disponibile anche una visualizzazione **giornaliera** che mostra i costi sostenuti ogni giorno. La visualizzazione giornaliera non visualizza una tendenza di incremento. La visualizzazione è progettata per mostrare eventuali irregolarità, ad esempio impennate o flessioni dei costi da un giorno all'altro. Se si seleziona un budget, la visualizzazione giornaliera mostra anche una stima del budget giornaliero.

Quando i costi giornalieri sono costantemente al di sopra del budget giornaliero stimato, è probabile che il budget mensile venga superato. Il budget stimato giornaliero è un mezzo per poter visualizzare il budget a un livello più basilare. Quando si verificano fluttuazioni nei costi giornalieri, il confronto tra il budget giornaliero stimato e il budget mensile è meno preciso.

Ecco una visualizzazione giornaliera delle spese recenti con la previsione di spesa attivata.
![Visualizzazione giornaliera che mostra i costi giornalieri di esempio per il mese corrente](./media/quick-acm-cost-analysis/daily-view.png)

Se si disattiva la previsione di spesa, le spese previste per date future non vengono visualizzate. Inoltre, se si esaminano i costi relativi a periodi di tempo passati, la previsione dei costi non mostra i costi.

In genere, è possibile visualizzare dati o notifiche per le risorse utilizzate entro 8-12 ore.


Usare **Raggruppa per** per raggruppare proprietà comuni per suddividere i costi e identificare i gruppi che hanno contribuito maggiormente ai costi. Per eseguire il raggruppamento in base ai tag delle risorse, ad esempio, selezionare la chiave del tag da usare per il raggruppamento. I costi verranno suddivisi in base a ogni valore di tag, con un segmento extra per le risorse a cui non è applicato tale tag.

La maggior parte delle [risorse di Azure supporta l'assegnazione di tag](../azure-resource-manager/tag-support.md). Alcuni tag, tuttavia, non sono disponibili per Gestione costi e la fatturazione. Inoltre, i tag del gruppo di risorse non sono supportati. Gestione costi supporta solo i tag delle risorse a partire dalla data in cui i tag vengono applicati direttamente alla risorsa. Guardare il video [How to review tag policies with Azure Cost Management](https://www.youtube.com/watch?v=nHQYcYGKuyw) (Come rivedere i criteri di tag con Gestione costi di Azure) per informazioni sull'uso dei criteri di tag di Azure per migliorare la visibilità dei dati di costo.

Ecco una visualizzazione dei costi dei servizi di Azure per il mese corrente.

![Visualizzazione giornaliera accumulata raggruppata che mostra i costi dei servizi di Azure di esempio per il mese scorso](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Per impostazione predefinita, l'analisi dei costi mostra tutti i costi di utilizzo e di acquisto che vengono accumulati e che verranno visualizzati sulla fattura, anche detti **costi effettivi**. La visualizzazione dei costi effettivi è ideale per la riconciliazione della fattura. Tuttavia, i picchi di acquisti nei costi possono essere allarmanti quando si esaminano le anomalie di spesa e altre variazioni nei costi. Per appiattire i picchi causati dai costi degli acquisti di prenotazioni, passare a **Costo ammortizzato**.

![Alternare tra costi effettivi e ammortizzati per vedere la distribuzione degli acquisti di prenotazioni nel periodo e la relativa allocazione alle risorse usate nella prenotazione](./media/quick-acm-cost-analysis/metric-picker.png)

I costi ammortizzati suddividono gli acquisti di prenotazioni in blocchi giornalieri e li distribuiscono per la durata del periodo di prenotazione. Ad esempio, invece di un acquisto di € 365 il 1o gennaio, verrà visualizzato un acquisto di € 1 ogni giorno dal 1o gennaio al 31 dicembre. Oltre all'ammortamento di base, questi costi vengono anche riallocati e associati usando le risorse specifiche che hanno usato la prenotazione. Ad esempio, se un addebito giornaliero di € 1 viene diviso tra due macchine virtuali, per quel giorno vengono visualizzati due addebiti di € 0,50. Se parte della prenotazione non viene utilizzata quel giorno, vengono visualizzati un solo addebito di € 0,50 associato alla macchina virtuale applicabile e un altro addebito di € 0,50 con tipo di addebito `UnusedReservation`. Si noti che i costi delle prenotazioni inutilizzate possono essere visualizzati solo tra i costi ammortizzati.

Poiché i costi vengono rappresentati diversamente, è importante notare che le visualizzazioni di costi effettivi e costi ammortizzati mostrano numeri totali differenti. In generale, il costo totale dei mesi con l'acquisto di una prenotazione diminuisce quando vengono visualizzati i costi ammortizzati e il costo dei mesi successivi all'acquisto di una prenotazione aumenta. L'ammortamento è disponibile solo per gli acquisti di prenotazioni e non si applica agli acquisti in Azure Marketplace in questo momento.

L'immagine seguente mostra i nomi dei gruppi di risorse. È possibile raggruppare per tag per visualizzare i costi totali per tag oppure usare la visualizzazione **Costo per risorsa** per vedere tutti i tag relativi a una specifica risorsa.

![Dati completi per la visualizzazione corrente che mostrano il nome del gruppo di risorse](./media/quick-acm-cost-analysis/full-data-set.png)

Quando si raggruppano i costi in base a un attributo specifico, vengono visualizzati in ordine decrescente i primi 10 gruppi di risorse che hanno contribuito maggiormente ai costi. Se sono presenti più di 10 gruppi, vengono visualizzati i primi nove gruppi che hanno contribuito, nonché un gruppo **Altri** che rappresenta tutti gli altri gruppi combinati. Quando si raggruppa in base ai tag, viene visualizzato un gruppo **Senza tag** per i costi a cui non è applicata la chiave del tag. **Senza tag** è sempre ultimo, anche se i costi senza tag sono superiori ai costi con tag. Se sono presenti 10 o più valori di tag, i costi senza tag faranno parte del gruppo **Altri**. Passare alla visualizzazione tabella e modificare la granularità in **Nessuna** per visualizzare tutti i valori classificati in base al costo, dal più alto al più basso.

Le macchine virtuali, le risorse di rete e le risorse di archiviazione classiche non condividono dati di fatturazione dettagliati. Tali risorse vengono unite nel gruppo **Classic services** (Servizi classici) quando si raggruppano i costi.

I grafici pivot nel grafico principale mostrano raggruppamenti diversi per offrire un quadro più ampio dei costi complessivi per il periodo di tempo e i filtri selezionati. Selezionare una proprietà o un tag per visualizzare i costi aggregati per qualsiasi dimensione.

![Esempio di grafici pivot](./media/quick-acm-cost-analysis/pivot-charts.png)

È possibile visualizzare il set di dati completo per qualsiasi visualizzazione. Le selezioni effettuate o i filtri applicati influiscono sui dati presentati. Per visualizzare il set di dati completo, selezionare l'elenco del **tipo di grafico** e quindi selezionare la visualizzazione **Tabella**.

![Dati per la visualizzazione corrente in vista tabella](./media/quick-acm-cost-analysis/chart-type-table-view.png)


## <a name="understanding-grouping-and-filtering-options"></a>Informazioni sulle opzioni di raggruppamento e filtro

La tabella seguente contiene alcune delle opzioni di raggruppamento e filtro più comuni, oltre a indicazioni su quando usarle.

| Proprietà | Quando usare le autorizzazioni |
| --- | --- |
| **Periodo di fatturazione** | Suddivide i costi per mese di fattura. Questa opzione è importante per le sottoscrizioni con pagamento in base al consumo e per quelle di sviluppo/test, che non sono vincolate a mesi di calendario. Gli account EA/MCA possono usare i mesi di calendario nello strumento di selezione data o la granularità mensile per raggiungere lo stesso obiettivo. |
| **Tipo di addebito** | Suddivide i costi di utilizzo, acquisto, rimborso e prenotazioni inutilizzate. Gli acquisti di prenotazioni e i rimborsi sono disponibili solo quando si usano i costi effettivi e non i costi ammortizzati. I costi delle prenotazioni inutilizzate sono disponibili solo quando si esaminano i costi ammortizzati. |
| **Cloud** | Suddivide i costi in base ad AWS e Azure. I costi di AWS sono disponibili solo per gruppi di gestione, account di fatturazione esterni e sottoscrizioni esterne. |
| **Reparto** / **Sezione della fattura** | Suddivide i costi per reparto EA o sezione della fattura MCA. Questa opzione è disponibile solo per gli account di fatturazione EA/MCA e per i profili di fatturazione MCA. |
| **Account di registrazione** | Suddivide i costi per proprietario di account EA. Questa opzione è disponibile solo per gli account di fatturazione EA e i reparti. |
| **Frequenza** | Suddivide i costi in base all'utilizzo, una tantum e ricorrenti. |
| **Contatore** | Suddivide i costi per contatore dell'utilizzo di Azure. Questa opzione è disponibile solo per l'utilizzo di Azure. Tutti gli acquisti e l'utilizzo del Marketplace verranno visualizzati come **Non specificato** o **Non assegnato**. |
| **Tipo di autore** | Suddivide i costi di AWS, Azure e Marketplace. |
| **Prenotazione** | Suddivide i costi per prenotazione. Qualsiasi utilizzo che non include una prenotazione verrà visualizzato come **Non specificato**. |
| **Risorsa** | Suddivide i costi per risorsa. Tutti gli acquisti verranno visualizzati come **Non specificato** perché vengono applicati a livello di account di fatturazione EA/PAYG o profilo di fatturazione MCA.  |
| **Gruppo di risorse** | Suddivide i costi per gruppo di risorse. Questa opzione è disponibile solo per l'utilizzo non classico. L'utilizzo di risorse classico verrà visualizzato come **altro** e gli acquisti come **Non specificato**. |
| **Tipo di risorsa** | Suddivide i costi per tipo di risorsa. Questa opzione è disponibile solo per l'utilizzo non classico. L'utilizzo di risorse classico verrà visualizzato come **altro** e gli acquisti come **Non specificato**. |
| **Nome del servizio** o **Categoria del contatore** | Suddivide i costi per servizio di Azure. Questa opzione è disponibile solo per l'utilizzo di Azure. Tutti gli acquisti e l'utilizzo del Marketplace verranno visualizzati come **Non specificato** o **Non assegnato**. |
| **Livello di servizio** o **Sottocategoria del contatore** | Suddivide i costi in base alla sottoclassificazione del contatore dell'utilizzo di Azure. Questa opzione è disponibile solo per l'utilizzo di Azure. Tutti gli acquisti e l'utilizzo del Marketplace verranno visualizzati come **Non specificato** o **Non assegnato**. |
| **Sottoscrizione** | Suddivide i costi per sottoscrizione. Tutti gli acquisti vengono visualizzati come **Non specificato**. |
| **Tag** | Suddivide i costi per valori di tag per una specifica chiave di tag. |

Per altre informazioni sui termini, vedere [Informazioni sui termini usati nel file su utilizzo e addebiti di Azure](../billing/billing-understand-your-usage.md).


## <a name="saving-and-sharing-customized-views"></a>Salvataggio e condivisione di visualizzazioni personalizzate

Salvare le visualizzazioni personalizzate e condividerle con altre persone aggiungendo l'analisi dei costi al dashboard del portale di Azure oppure copiando un collegamento all'analisi.

Per aggiungere l'analisi dei costi, selezionare l'icona Aggiungi nell'angolo in alto a destra. Aggiungendo l'analisi dei costi verrà salvata solo la visualizzazione grafico o tabella principale. Condividere il dashboard per concedere ad altre persone l'accesso al riquadro. Tenere presente che in questo modo si condivide solo la configurazione del dashboard e non si concede ad altre persone l'accesso ai dati sottostanti. Se non si ha accesso ai costi ma si ha accesso a un dashboard condiviso, verrà visualizzato un messaggio di accesso negato.

Per condividere un collegamento all'analisi dei costi, selezionare **Condividi** nella parte superiore del pannello. Verrà visualizzato un URL personalizzato, che apre questa specifica visualizzazione per questo specifico ambito. Se non si ha accesso ai costi e si ottiene questo URL, verrà visualizzato un messaggio di accesso negato.

Per altre informazioni su come concedere l'accesso ai costi per ogni ambito supportato, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).

## <a name="automation-and-offline-analysis"></a>Automazione e analisi offline

A volte è necessario scaricare i dati per un'ulteriore analisi, unirli con dati personali o integrarli nei propri sistemi. Il servizio Gestione costi offre alcune opzioni diverse. Per iniziare, se è necessario un riepilogo ad hoc a livello generale, come quello che si ottiene nell'analisi dei costi, creare la visualizzazione necessaria. Scaricarla quindi selezionando **Esporta** e **Scarica i dati in un file CSV** o **Scarica i dati in Excel**. Il download in Excel offre informazioni di contesto aggiuntive per la visualizzazione usata per generarlo, come ambito, configurazione della query, totale e data di generazione.

Se è necessario il set di dati completo, non aggregato, scaricarlo dall'account di fatturazione. Dall'elenco dei servizi nel riquadro di spostamento a sinistra del portale passare quindi a **Gestione dei costi e fatturazione**. Selezionare l'account di fatturazione, se applicabile. Passare a **Utilizzo e addebiti** e quindi selezionare l'icona **Download** per il periodo di fatturazione desiderato.

Adottare un approccio simile per automatizzare la ricezione dei dati sui costi. Usare l'[API Query](/rest/api/cost-management/query) per un'analisi più completa con filtro dinamico, raggruppamento e aggregazione oppure l'[API UsageDetails](/rest/api/consumption/usageDetails) per il set di dati completo, non aggregato. La versione disponibile a livello generale (GA) di queste API è 2019-01-01. Usare **2019-04-01-preview** per ottenere l'accesso all'anteprima degli acquisti di prenotazioni e del Marketplace all'interno di queste API.

Ad esempio, di seguito è riportata una visualizzazione aggregata dei costi ammortizzati suddivisi per tipo di addebito (utilizzo, acquisto o rimborso), tipo di autore (Azure o Marketplace), gruppo di risorse (campo vuoto per gli acquisti) e prenotazione (campo vuoto se non applicabile).

```
POST https://management.azure.com/{scope}/providers/Microsoft.CostManagement/query?api-version=2019-04-01-preview
Content-Type: application/json

{
  "type": "AmortizedCost",
  "timeframe": "Custom",
  "timePeriod": { "from": "2019-04-01", "to": "2019-04-30" },
  "dataset": {
    "granularity": "None",
    "aggregation": {
      "totalCost": { "name": "PreTaxCost", "function": "Sum" }
    },
    "grouping": [
      { "type": "dimension", "name": "ChargeType" },
      { "type": "dimension", "name": "PublisherType" },
      { "type": "dimension", "name": "Frequency" },
      { "type": "dimension", "name": "ResourceGroup" },
      { "type": "dimension", "name": "SubscriptionName" },
      { "type": "dimension", "name": "SubscriptionId" },
      { "type": "dimension", "name": "ReservationName" },
      { "type": "dimension", "name": "ReservationId" },
    ]
  },
}
```

Se invece non è necessaria l'aggregazione e si preferisce il set di dati completo, non elaborato:

```
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

Se è necessario che i costi effettivi mostrino gli acquisti mentre vengono accumulati, impostare **type**/**metric** su **ActualCost**. Per altre informazioni su queste API, vedere la documentazione delle API [Query](/rest/api/cost-management/query) e [UsageDetails](/rest/api/consumption/usageDetails). Si noti che i documenti pubblicati si riferiscono alla versione disponibile a livello generale. Entrambi sono tuttavia applicabili alla versione delle API *2019-04-01-preview*, ad eccezione del nuovo attributo type/metric e dei nomi di proprietà cambiati. (Per altre informazioni sui nomi delle proprietà, vedere di seguito.)

Le API di Gestione costi funzionano in tutti gli ambiti per le risorse: gruppo di risorse, sottoscrizione e gruppo di gestione tramite controllo degli accessi in base al ruolo di Azure, account di fatturazione EA (registrazioni), reparti e account di registrazione tramite accesso al portale EA. Per altre informazioni sugli ambiti, tra cui come determinare l'ID ambito o gestire l'accesso, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).

## <a name="next-steps"></a>Passaggi successivi

Passare alla prima esercitazione per apprendere come creare e gestire i budget.

> [!div class="nextstepaction"]
> [Creare e gestire i budget](tutorial-acm-create-budgets.md)
