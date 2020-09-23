---
title: Caricare il feed di dati in metriche Advisor
titleSuffix: Azure Cognitive Services
description: Come iniziare a caricare i feed di dati in metriche Advisor.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: 83ff710804b43837657ea0da7c8f44c245017c7e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940131"
---
# <a name="how-to-onboard-your-metric-data-to-metrics-advisor"></a>Procedura: caricare i dati delle metriche in metriche Advisor

Usare questo articolo per informazioni sull'onboarding dei dati in metriche Advisor. 

## <a name="data-schema-requirements-and-configuration"></a>Requisiti e configurazione dello schema di dati

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

## <a name="avoid-loading-partial-data"></a>Evitare il caricamento di dati parziali

I dati parziali sono causati da incoerenze tra i dati archiviati in metriche Advisor e l'origine dati. Questo problema può verificarsi quando l'origine dati viene aggiornata dopo il completamento del pull dei dati da parte di Advisor. Metrica Advisor estrae solo i dati da una determinata origine dati una sola volta.

Ad esempio, se una metrica è stata caricata in metrica Advisor per il monitoraggio. Metrica Advisor esegue correttamente il recupero dei dati delle metriche al timestamp A ed esegue il rilevamento delle anomalie. Tuttavia, se i dati delle metriche di quel particolare timestamp A sono stati aggiornati dopo l'inserimento dei dati. Il nuovo valore dei dati non verrà recuperato.

È possibile tentare di eseguire il [recupero](manage-data-feeds.md#backfill-your-data-feed) dei dati cronologici (descritti più avanti) per attenuare le incoerenze, ma ciò non attiverà nuovi avvisi di anomalie se gli avvisi per quei punti di tempo sono già stati attivati. Questo processo può aggiungere un carico di lavoro aggiuntivo al sistema e non è automatico.

Per evitare il caricamento di dati parziali, è consigliabile usare due approcci:

* Generare dati in una transazione:

    Verificare che i valori delle metriche per tutte le combinazioni di dimensioni nello stesso timestamp vengano archiviati nell'origine dati in un'unica transazione. Nell'esempio precedente, attendere che i dati di tutte le origini dati siano pronti e quindi caricarli in metriche Advisor in un'unica transazione. La metrica Advisor può eseguire regolarmente il polling del feed di dati fino a quando i dati non vengono recuperati correttamente o parzialmente.

* Ritardare l'inserimento dei dati impostando un valore appropriato per il parametro di **offset tempo** di inserimento:

    Impostare il parametro di **offset tempo** di inserimento per il feed di dati per ritardare l'inserimento fino a quando i dati non sono completamente preparati. Questa operazione può essere utile per alcune origini dati che non supportano transazioni come l'archiviazione tabelle di Azure. Per informazioni dettagliate, vedere [Impostazioni avanzate](manage-data-feeds.md#advanced-settings) .

## <a name="add-a-data-feed-using-the-web-based-workspace"></a>Aggiungere un feed di dati tramite l'area di lavoro basata sul Web

Dopo aver eseguito l'accesso al portale di Advisor di metrica e aver scelto l'area di lavoro, **fare clic su inizia.** Quindi, nella pagina principale dell'area di lavoro, fare clic su **Aggiungi feed di dati** nel menu a sinistra.

### <a name="add-connection-settings"></a>Aggiungi impostazioni di connessione

Immettere quindi un set di parametri per connettere l'origine dati della serie temporale. 
* **Tipo di origine**: il tipo di origine dati in cui sono archiviati i dati della serie temporale.
* **Granularità**: intervallo tra i punti dati consecutivi nei dati delle serie temporali. Attualmente la metrica Advisor supporta: annuale, mensile, settimanale, giornaliera, oraria e personalizzata. L'intervallo minimo supportato dall'opzione di personalizzazione è pari a 60 secondi.
  * **Secondi**: numero di secondi durante i quali *granularityName* è impostato per la *personalizzazione*.
* Inserire **i dati a partire da (UTC)**: ora di inizio della linea di base per l'inserimento dei dati. *startOffsetInSeconds* viene spesso usato per aggiungere un offset per semplificare la coerenza dei dati.

Successivamente, sarà necessario specificare le informazioni di connessione per l'origine dati e le query personalizzate utilizzate per convertire i dati nello schema richiesto. Per informazioni dettagliate sugli altri campi e sulla connessione di diversi tipi di origini dati, vedere [aggiungere feed di dati da origini dati diverse](../data-feeds-from-different-sources.md).

[!INCLUDE [query requirements](../includes/query-requirements.md)]

### <a name="verify-and-get-schema"></a>Verificare e ottenere lo schema

Dopo aver impostato la stringa di connessione e la stringa di query, selezionare **Verifica e ottieni schema** per verificare la connessione ed eseguire la query per ottenere lo schema di dati dall'origine dati. Questa operazione normalmente richiede alcuni secondi, a seconda della connessione all'origine dati. Se si verifica un errore in questa fase, verificare che:

* La stringa di connessione e la query siano corrette.
* L'istanza di Advisor metriche sia in grado di connettersi all'origine dati in presenza di impostazioni del firewall.

### <a name="schema-configuration"></a>Configurazione dello schema

Una volta caricato lo schema di dati, selezionare i campi appropriati.

Se il timestamp di un punto dati viene omesso, Metrics Advisor utilizzerà il timestamp quando si inserisce il punto dati. Per ogni feed di dati, è possibile specificare al massimo una colonna come timestamp. Se viene visualizzato un messaggio indicante che non è possibile specificare una colonna come timestamp, controllare la query o l'origine dati e verificare se nel risultato della query sono presenti più timestamp, non solo nei dati di anteprima. Quando si esegue l'inserimento dei dati, la metrica Advisor può utilizzare solo un blocco, ad esempio un giorno, un'ora, in base alla granularità, dei dati di serie temporali dall'origine specificata ogni volta.

|Selezione  |Descrizione  |Note  |
|---------|---------|---------|
| **Nome visualizzato** | Nome da visualizzare nell'area di lavoro anziché il nome della colonna originale. | |
|**Timestamp**     | Timestamp di un punto dati. Se omesso, Advisor metriche userà il timestamp quando il punto dati viene inserito. Per ogni feed di dati, è possibile specificare al massimo una colonna come timestamp.        | facoltativo. Deve essere specificato con al massimo una colonna. Se **non è possibile specificare una colonna come errore timestamp** , controllare la query o l'origine dati per ottenere timestamp duplicati.      |
|**Measure**     |  Valori numerici nel feed di dati. Per ogni feed di dati, è possibile specificare più misure, ma è necessario selezionare almeno una colonna come misura.        | Deve essere specificato con almeno una colonna.        |
|**Dimensione**     | Valori di categoria. Una combinazione di valori diversi identifica una particolare serie temporale unidimensionale, ad esempio paese, lingua, tenant. È possibile selezionare zero o più colonne come dimensioni. Nota: prestare attenzione quando si seleziona una colonna non di tipo stringa come dimensione. | facoltativo.        |
|**Ignora**     | Ignora la colonna selezionata.        | facoltativo. Vedere il testo seguente.       |

Se si desidera ignorare le colonne, è consigliabile aggiornare la query o l'origine dati per escludere tali colonne. È inoltre possibile ignorare le colonne utilizzando **Ignora colonne** , quindi **ignorare** le colonne specifiche. Se una colonna deve essere una dimensione e viene erroneamente impostata come *ignorata*, le metriche di Advisor potrebbero essere l'inserimento di dati parziali. Si supponga, ad esempio, che i dati della query siano i seguenti:

| ID riga | Timestamp | Paese | Linguaggio | Income |
| --- | --- | --- | --- | --- |
| 1 | 2019/11/10 | Cina | ZH-CN | 10000 |
| 2 | 2019/11/10 | Cina | EN-US | 1000 |
| 3 | 2019/11/10 | US | ZH-CN | 12000 |
| 4 | 2019/11/11 | US | EN-US | 23000 |
| ... | ...| ... | ... | ... |

Se *Country* è una dimensione e la *lingua* viene impostata come *ignorata*, la prima e la seconda riga avranno le stesse dimensioni. In metrica Advisor viene utilizzato arbitrariamente un valore delle due righe. In questo caso, le righe non verranno aggregate in Advisor.

### <a name="automatic-roll-up-settings"></a>Impostazioni di rollup automatico

> [!IMPORTANT]
> Per abilitare l'analisi delle cause principali e altre funzionalità di diagnostica, è necessario configurare le **impostazioni di rollup automatico** . Una volta abilitate, le impostazioni di rollup automatico non possono essere modificate.

Metrica Advisor può eseguire automaticamente un'aggregazione, ad esempio SUM, MAX, MIN, in ogni dimensione durante l'inserimento, quindi compila una gerarchia che verrà usata nell'analisi dei case radice e in altre funzionalità di diagnostica. 

Esaminare gli scenari seguenti:

* *Non è necessario includere l'analisi rollup per i dati personali.*

    Non è necessario usare il rollup di Advisor per la metrica.

* *I dati sono già stati sottoposti a rollup e il valore della dimensione è rappresentato da: NULL o vuoto (impostazione predefinita), solo NULL, altri.*

    Questa opzione indica che la metrica di Advisor non deve eseguire il rollup dei dati perché le righe sono già state sommate. Se, ad esempio, si seleziona *solo null*, la seconda riga di dati nell'esempio seguente sarà considerata come un'aggregazione di tutti i paesi e della lingua *en-US*; la quarta riga di dati che ha un valore vuoto per *Country* sarà tuttavia considerata una riga ordinaria che potrebbe indicare dati incompleti.
    
    | Paese | Linguaggio | Income |
    |---------|----------|--------|
    | Cina   | ZH-CN    | 10000  |
    | NULL  | EN-US    | 999999 |
    | US      | EN-US    | 12000  |
    |         | EN-US    | 5000   |

* *È necessario Advisor di metrica per eseguire il rollup dei dati calcolando Sum/max/min/avg/count e rappresentarlo per <some string>*

    Alcune origini dati, ad esempio Cosmos DB o archiviazione BLOB di Azure, non supportano determinati calcoli come *Group by* o *Cube*. Metrica Advisor fornisce l'opzione rollup per generare automaticamente un cubo di dati durante l'inserimento.
    Questa opzione significa che è necessario Advisor di metrica per calcolare il rollup usando l'algoritmo selezionato e usare la stringa specificata per rappresentare il rollup in metriche Advisor. Questa operazione non modificherà i dati nell'origine dati.
    Si supponga, ad esempio, di disporre di un set di serie temporali che corrisponde alle metriche di vendita con la dimensione (paese, area geografica). Per un determinato timestamp, potrebbe essere simile al seguente:


    | Country       | Region           | Sales |
    |---------------|------------------|-------|
    | Canada        | Alberta          | 100   |
    | Canada        | British Columbia | 500   |
    | Stati Uniti | Montana          | 100   |


    Dopo aver abilitato il rollup automatico con *Sum*, le combinazioni di dimensioni vengono calcolate da metrica Advisor e vengono sommate le metriche durante l'inserimento dei dati. Il risultato potrebbe essere:

    | Country       | Region           | Sales |
    | ------------ | --------------- | ---- |
    | Canada        | Alberta          | 100   |
    | NULL          | Alberta          | 100   |
    | Canada        | British Columbia | 500   |
    | NULL          | British Columbia | 500   |
    | Stati Uniti | Montana          | 100   |
    | NULL          | Montana          | 100   |
    | NULL          | NULL             | 700   |
    | Canada        | NULL             | 600   |
    | Stati Uniti | NULL             | 100   |

    `(Country=Canada, Region=NULL, Sales=600)` indica che la somma delle vendite in Canada (tutte le aree) è 600.

    Di seguito è riportata la trasformazione nel linguaggio SQL.

    ```mssql
    SELECT
        dimension_1,
        dimension_2,
        ...
        dimension_n,
        sum (metrics_1) AS metrics_1,
        sum (metrics_2) AS metrics_2,
        ...
        sum (metrics_n) AS metrics_n
    FROM
        each_timestamp_data
    GROUP BY
        CUBE (dimension_1, dimension_2, ..., dimension_n);
    ```

    Prima di usare la funzionalità di rollup automatico, tenere presente quanto segue:

    * Se si vuole usare *Sum* per aggregare i dati, assicurarsi che le metriche siano additive in ogni dimensione. Di seguito sono riportati alcuni esempi di metriche *non additive* :
      * Metriche basate su frazioni. Sono inclusi il rapporto, la percentuale e così via. Ad esempio, non è necessario aggiungere la percentuale di disoccupazione di ogni stato per calcolare il tasso di disoccupazione dell'intero paese.
      * Sovrapposizione nella dimensione. Ad esempio, non è necessario aggiungere il numero di persone in ogni sport per calcolare il numero di persone che amano lo sport, perché esiste una sovrapposizione tra di esse, una persona può essere simile a più sport.
    * Per garantire l'integrità dell'intero sistema, le dimensioni del cubo sono limitate. Attualmente, il limite è 1 milione. Se i dati superano tale limite, l'inserimento non riuscirà per tale timestamp.

## <a name="advanced-settings"></a>Impostazioni avanzate

Sono disponibili diverse impostazioni avanzate per consentire l'inserimento dei dati in modo personalizzato, ad esempio specificando l'offset di inserimento o la concorrenza. Per ulteriori informazioni, vedere la sezione [Impostazioni avanzate](manage-data-feeds.md#advanced-settings) nell'articolo gestione di feed di dati.

## <a name="specify-a-name-for-the-data-feed-and-check-the-ingestion-progress"></a>Specificare un nome per il feed di dati e verificare lo stato di inserimento
 
Assegnare al feed di dati un nome personalizzato, che verrà visualizzato nell'area di lavoro. Quindi fare clic su **Submit (Invia**). Nella pagina Dettagli feed di dati, è possibile usare la barra di stato inserimento per visualizzare le informazioni sullo stato.

:::image type="content" source="../media/datafeeds/ingestion-progress.png" alt-text="Barra di stato inserimento" lightbox="../media/datafeeds/ingestion-progress.png":::


Per controllare i dettagli dell'errore di inserimento: 

1. Fare clic su **Mostra dettagli**.
2. Fare clic su **stato** , **quindi scegliere** **errore o errore**.
3. Passare il puntatore del mouse su un inserimento non riuscito e visualizzare il messaggio dettagliato visualizzato.

:::image type="content" source="../media/datafeeds/check-failed-ingestion.png" alt-text="Verifica inserimento non riuscito":::

Uno stato di *errore* indica che l'inserimento per questa origine dati verrà ritentato in seguito.
Uno stato di *errore* indica che le metriche di Advisor non verranno ritentate per l'origine dati. Per ricaricare i dati, è necessario attivare manualmente un recupero o un ricaricamento.

È anche possibile ricaricare lo stato di avanzamento di un inserimento facendo clic su **Aggiorna stato**. Al termine dell'inserimento dei dati, è possibile fare clic su metriche e verificare i risultati del rilevamento anomalie.

## <a name="next-steps"></a>Passaggi successivi
- [Gestire i feed di dati](manage-data-feeds.md)
- [Configurazioni per origini dati diverse](../data-feeds-from-different-sources.md)
- [Configurare le metriche e ottimizzare la configurazione del rilevamento](configure-metrics.md)
