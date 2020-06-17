---
title: Gestire i costi di Azure con l'automazione
description: Questo articolo illustra come gestire i costi di Azure con l'automazione.
author: bandersmsft
ms.author: banders
ms.date: 04/15/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 0727f98b917944f3721c6c6758fde05c2efd8773
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84449832"
---
# <a name="manage-costs-with-automation"></a>Gestire i costi con l'automazione

È possibile usare l'automazione di Gestione costi per creare un set personalizzato di soluzioni con cui recuperare e gestire i dati sui costi. Questo articolo riguarda gli scenari comuni di automazione di Gestione costi e le opzioni disponibili in base alla situazione. Per lo sviluppo tramite API, vengono presentati esempi di richieste API comuni per accelerare il processo.

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>Automatizzare il recupero dei dati sui costi per l'analisi offline

Potrebbe essere necessario scaricare i dati sui costi di Azure per unirli ad altri set di dati. Oppure potrebbe essere necessario integrare i dati sui costi nei propri sistemi. Sono disponibili diverse opzioni in base alla quantità di dati coinvolti. In ogni caso, per usare le API e gli strumenti, è necessario avere le autorizzazioni di Gestione costi nell'ambito appropriato. Per altre informazioni, vedere [Assegnare l'accesso ai dati](https://docs.microsoft.com/azure/cost-management-billing/costs/assign-access-acm-data).

## <a name="suggestions-for-handling-large-datasets"></a>Suggerimenti per la gestione di set di dati di grandi dimensioni

Se l'organizzazione usa diffusamente Azure tra molte risorse o sottoscrizioni, sarà presente una grande quantità di dati dettagliati sull'utilizzo. Spesso Excel non è in grado di caricare tali file di grandi dimensioni. In questa situazione è consigliabile scegliere le opzioni seguenti:

**Power BI**

Power BI consente di inserire e gestire grandi quantità di dati. I clienti con contratto Enterprise possono usare l'app modello di Power BI per analizzare i costi per l'account di fatturazione. Il report contiene le principali visualizzazioni usate dai clienti. Per altre informazioni, vedere [Analizzare i costi di Azure con l'app modello di Power BI](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app).

**Connettore dati di Power BI**

Per analizzare i dati quotidianamente, è consigliabile usare il [connettore dati di Power BI](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-azure-cost-management) per eseguire un'analisi dettagliata. Tutti i report creati vengono mantenuti aggiornati dal connettore mentre vengono accumulati altri costi.

**Esportazioni di Gestione costi**

A volte non è necessario analizzare i dati quotidianamente. In questo caso, è consigliabile usare la funzionalità [Esportazioni](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) per pianificare le esportazioni di dati in un account di archiviazione di Azure. È quindi possibile caricare i dati in Power BI, se necessario, o analizzarli in Excel se le dimensioni del file sono sufficientemente ridotte. Le esportazioni sono disponibili nel portale di Azure oppure è possibile configurarle con l'[API Esportazioni](https://docs.microsoft.com/rest/api/cost-management/exports).

**API Dettagli utilizzo**

Se il set di dati sui costi è di piccole dimensioni, provare a usare l'[API Dettagli utilizzo](https://docs.microsoft.com/rest/api/consumption/usageDetails). Nel caso di una quantità elevata di dati sui costi, è consigliabile richiedere la quantità di dati sull'utilizzo minima possibile per un periodo. A tale scopo, specificare un intervallo di tempo ridotto o usare un filtro nella richiesta. Ad esempio, in uno scenario in cui sono necessari tre anni di dati sui costi, l'API offre prestazioni migliori se si effettuano più chiamate per intervalli di tempo diversi invece di una singola. Da qui, è possibile caricare i dati in Excel per un'ulteriore analisi.

## <a name="automate-retrieval-with-usage-details-api"></a>Automatizzare il recupero con l'API Dettagli utilizzo

L'[API Dettagli utilizzo ](https://docs.microsoft.com/rest/api/consumption/usageDetails) offre un modo semplice per ottenere dati sui costi non elaborati e non aggregati corrispondenti alla fattura di Azure. L'API è utile quando l'organizzazione necessita di una soluzione di recupero dei dati a livello di codice. È consigliabile usare l'API per l'analisi di set di dati sui costi di dimensioni ridotte. Nel caso di set di dati più grandi, è invece consigliabile usare le altre soluzioni identificate in precedenza. I dati in Dettagli utilizzo vengono forniti per ogni contatore al giorno. Vengono usati per il calcolo della fattura mensile. La versione disponibile a livello generale di queste API è `2019-10-01`. Usare `2019-04-01-preview` per accedere alla versione di anteprima per la prenotazione e gli acquisti in Azure Marketplace con le API.

### <a name="usage-details-api-suggestions"></a>Suggerimenti per l'API Dettagli utilizzo

**Pianificazione delle richieste**

È consigliabile effettuare _non più di una richiesta_ al giorno all'API Dettagli utilizzo. Per altre informazioni sulla frequenza di aggiornamento dei dati sui costi e su come viene gestito l'arrotondamento, vedere [Informazioni sui dati di Gestione costi](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data#rated-usage-data-refresh-schedule).

**Scegliere ambiti di primo livello senza filtro**

Usare l'API per ottenere tutti i dati necessari nell'ambito di livello più alto disponibile. Attendere che vengano inseriti tutti i dati necessari prima di eseguire operazioni di filtro, raggruppamento o analisi aggregate. L'API è ottimizzata specificamente per fornire grandi quantità di dati sui costi non elaborati e non aggregati. Per altre informazioni sugli ambiti disponibili in Gestione costi, vedere [Informazioni e utilizzo degli ambiti](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-work-scopes). Una volta scaricati i dati necessari per un ambito, usare Excel per analizzare ulteriormente i dati con i filtri e le tabelle pivot.

## <a name="example-usage-details-api-requests"></a>Esempi di richieste all'API Dettagli utilizzo

Le richieste di esempio seguenti vengono usate dai clienti Microsoft negli scenari comuni che si potrebbero riscontrare.

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>Ottenere i dettagli di utilizzo per un ambito durante un intervallo di date specifico

I dati di utilizzo restituiti dalla richiesta corrispondono alla data in cui sono stati ricevuti dal sistema di fatturazione. Possono includere i costi di più fatture.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01

```

### <a name="get-amortized-cost-details"></a>Ottenere dettagli sui costi ammortizzati

Se sono necessari i costi effettivi per dimostrare gli acquisti accumulati, impostare *metric* su `ActualCost` nella richiesta seguente. Per usare i costi ammortizzati ed effettivi, è necessario usare la versione `2019-04-01-preview`. La versione corrente dell'API funziona allo stesso modo della versione `2019-10-01`, ad eccezione del nuovo attributo type/metric e dei nomi di proprietà modificati. Se si ha un Contratto del cliente Microsoft, i filtri sono `startDate` e `endDate` nell'esempio seguente.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Recuperare in modo ricorrente set di dati sui costi di grandi dimensioni con Esportazioni

La funzionalità Esportazioni è una soluzione che consente di pianificare i dump dei dati sui costi a intervalli regolari. Si tratta della soluzione consigliata per recuperare dati sui costi non aggregati per le organizzazioni i cui file sull'utilizzo sono troppo grandi per chiamare e scaricare dati in modo affidabile tramite l'API Dettagli utilizzo. I dati vengono inseriti in un account di archiviazione di Azure a scelta. Da qui è possibile caricarli nei propri sistemi e analizzarli in base alle esigenze dei team. Per configurare le esportazioni nel portale di Azure, vedere [Esportare dati](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data).

## <a name="data-latency-and-rate-limits"></a>Latenza dei dati e limiti di velocità

È consigliabile chiamare le API non più di una volta al giorno. I dati di Gestione costi vengono aggiornati ogni quattro ore man mano che vengono ricevuti dati di utilizzo dai provider di risorse di Azure. Le chiamate più frequenti non forniranno dati aggiuntivi, ma al contrario genereranno un aumento del carico. Per altre informazioni sulla frequenza con cui cambiano i dati e su come viene gestita la latenza, vedere [Informazioni sui dati di Gestione costi](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data).

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>Codice di errore 429 - Il numero di chiamate ha superato i limiti di velocità

Per garantire un'esperienza coerente per tutti i sottoscrittori di Gestione costi, le API di Gestione costi prevedono un limite di velocità. Quando si raggiunge il limite, si riceve il codice di stato `429: Too many requests`. I limiti di velocità effettiva correnti per le API sono i seguenti:

- 30 chiamate al minuto per ambito, per utente o per applicazione.
- 200 chiamate al minuto per tenant, per utente o per applicazione.

## <a name="next-steps"></a>Passaggi successivi

- [Analizzare i costi di Azure con l'app modello di Power BI](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app).
- [Creare e gestire dati esportati](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) con Esportazioni.
- Altre informazioni sull'[API Dettagli utilizzo](https://docs.microsoft.com/rest/api/consumption/usageDetails).