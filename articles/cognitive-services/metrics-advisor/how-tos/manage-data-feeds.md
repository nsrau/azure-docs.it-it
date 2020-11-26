---
title: Gestire i feed di dati in metriche Advisor
titleSuffix: Azure Cognitive Services
description: Informazioni su come gestire i feed di dati aggiunti a metrica Advisor.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: mbullwin
ms.openlocfilehash: fb6eaf44967732d3a41ea92b0896540a40f694e3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184723"
---
# <a name="how-to-manage-your-data-feeds"></a>Procedura: gestire i feed di dati

Informazioni su come gestire i feed di dati caricati in metriche Advisor. Questo articolo descrive come gestire i feed di dati in metriche Advisor.

## <a name="edit-a-data-feed"></a>Modificare un feed di dati

> [!NOTE]
> I dettagli seguenti non possono essere modificati dopo la creazione di un feed di dati. 
> * ID feed di dati
> * Data e ora creazione
> * Dimension
> * Tipo di origine
> * Granularità

Solo l'amministratore di un feed di dati è autorizzato a apportare modifiche. 

Per sospendere o riattivare un feed di dati:

1. Nella pagina dell'elenco di feed di dati fare clic sull'operazione che si desidera eseguire sul feed di dati.

2. Nella pagina Dettagli feed di dati fare clic sul pulsante di opzione **stato** .

Per eliminare un feed di dati: 

1. Nella pagina dell'elenco di feed di dati fare clic su **Elimina** nel feed di dati.

2. Nella pagina Dettagli feed di dati fare clic su **Elimina**.

Quando si modifica l'ora di inizio, è necessario verificare di nuovo lo schema. È possibile modificarlo utilizzando **modifica parametri**.

##  <a name="backfill-your-data-feed"></a>Recupero del feed di dati

Selezionare il pulsante di  **recupero** per attivare un inserimento immediato in un timestamp, per correggere un'operazione di inserimento non riuscita o per eseguire l'override dei dati esistenti.
- L'ora di inizio è inclusa.
- L'ora di fine è esclusiva.
- Il rilevamento delle anomalie viene nuovamente attivato solo nell'intervallo selezionato.

:::image type="content" source="../media/datafeeds/backfill-datafeed.png" alt-text="Recupero feed di dati":::

## <a name="manage-permission-of-a-data-feed"></a>Gestire l'autorizzazione di un feed di dati

L'accesso all'area di lavoro è controllato dalla risorsa Advisor di metrica, che usa Azure Active Directory per l'autenticazione. Un altro livello di controllo delle autorizzazioni viene applicato ai dati della metrica.

Metrica Advisor consente di concedere le autorizzazioni a gruppi diversi di utenti su feed di dati diversi. Sono disponibili due tipi di ruoli: 

- Administrator: chi dispone delle autorizzazioni complete per gestire un feed di dati, inclusa la modifica e l'eliminazione.
- Visualizzatore: utenti che hanno accesso a una visualizzazione di sola lettura del feed di dati.
 

## <a name="advanced-settings"></a>Impostazioni avanzate

Quando si crea un nuovo feed di dati sono disponibili diverse impostazioni avanzate facoltative, che possono essere modificate nella pagina dei dettagli del feed di dati.

### <a name="ingestion-options"></a>Opzioni di inserimento

* **Offset tempo** di inserimento: per impostazione predefinita, i dati vengono inseriti in base alla granularità specificata. Ad esempio, una metrica con un timestamp *giornaliero* verrà inserita un giorno dopo il timestamp. È possibile utilizzare l'offset per ritardare il tempo di inserimento con un numero *positivo* oppure spostarlo in un numero *negativo* .

* **Massima concorrenza**: impostare questo parametro se l'origine dati supporta la concorrenza limitata. In caso contrario, lasciare l'impostazione predefinita.

* **Arresta nuovo tentativo dopo**: se l'inserimento dei dati non è riuscito, verrà effettuato un nuovo tentativo automaticamente entro un periodo di tempo. L'inizio del periodo è l'ora in cui si è verificata la prima inserimento di dati. La lunghezza del periodo viene definita in base alla granularità. Se si lascia il valore predefinito (-1), il valore verrà determinato in base alla granularità riportata di seguito.
    
    | Granularità       | Arresta tentativi dopo           |
    | :------------ | :--------------- |
    | Giornaliero, personalizzato (>= 1 giorno), settimanale, mensile, annuale     | 7 giorni          |
    | Ogni ora, personalizzata (< 1 giorno)       | 72 ore |

* **Intervallo** minimo tra tentativi: è possibile specificare l'intervallo minimo durante il tentativo di pull dei dati dall'origine. Se si lascia il valore predefinito (-1), l'intervallo tra tentativi verrà determinato in base alla granularità riportata di seguito.
    
    | Granularità       | Intervallo minimo tra tentativi           |
    | :------------ | :--------------- |
    | Giornaliero, personalizzato (>= 1 giorno), settimanale, mensile     | 30 minuti          |
    | Ogni ora, personalizzata (< 1 giorno)      | 10 minuti |
    | Annuale | 1 giorno          |
 
### <a name="fill-gap-when-detecting"></a>Gap di riempimento durante il rilevamento: 

> [!NOTE]
> Questa impostazione non influirà sull'origine dati e non influirà sui grafici dei dati visualizzati nel portale. Il riempimento automatico si verifica solo durante il rilevamento delle anomalie.

Alcune serie temporali non sono continue. Quando non sono presenti punti dati, Metrics Advisor utilizzerà il valore specificato per riempirli prima del rilevamento delle anomalie per una maggiore accuratezza.
Le opzioni sono: 

* Utilizzo del valore del punto dati effettivo precedente. Viene usato per impostazione predefinita.
* Utilizzando un valore specifico.

### <a name="action-link-template"></a>Modello di collegamento all'azione: 

I modelli di collegamento all'azione vengono usati per predefinire URL http di utilità pratica, che sono costituiti da segnaposto,,, `%datafeed` `%metric` `%timestamp` `%detect_config` e `%tagset` . È possibile usare il modello per il Reindirizzamento da un'anomalia o da un evento imprevisto a un URL specifico per eseguire il drill-down.

:::image type="content" source="../media/action-link-template.png" alt-text="Modello di collegamento all'azione" lightbox="../media/action-link-template.png":::

Dopo aver compilato il collegamento all'azione, fare clic sul **collegamento Vai a azione** nell'opzione azione dell'elenco eventi imprevisti e sul menu di scelta rapida dell'albero degli eventi imprevisti. Sostituire i segnaposto nel modello di collegamento all'azione con i valori corrispondenti dell'anomalia o dell'evento imprevisto.

| Segnaposto | Esempi | Aggiungere commenti |
| ---------- | -------- | ------- |
| `%datafeed` | - | ID feed di dati |
| `%metric` | - | ID metrica |
| `%detect_config` | - | Rileva ID configurazione |
| `%timestamp` | - | Timestamp di un'anomalia o dell'ora di fine di un evento imprevisto persistente |
| `%tagset` | `%tagset`, <br> `[%tagset.get("Dim1")]`, <br> `[ %tagset.get("Dim1", "filterVal")]` | Valori delle dimensioni di un'anomalia o anomalia superiore di un evento imprevisto.   <br> `filterVal`Viene utilizzato per filtrare i valori corrispondenti all'interno delle parentesi quadre.   |

Esempi

* Se il modello di collegamento all'azione è `https://action-link/metric/%metric?detectConfigId=%detect_config` ,
  * Il collegamento all'azione `https://action-link/metric/1234?detectConfigId=2345` passa a anomalie o eventi imprevisti in metrica `1234` e rileva configurazione `2345` .

* Se il modello di collegamento all'azione è `https://action-link?[Dim1=%tagset.get('Dim1','')&][Dim2=%tagset.get('Dim2','')]` , 
    * Il collegamento all'azione sarà `https://action-link?Dim1=Val1&Dim2=Val2` quando l'anomalia è `{ "Dim1": "Val1", "Dim2": "Val2" }` . 
    * Il collegamento all'azione sarà `https://action-link?Dim2=Val2` quando l'anomalia è `{ "Dim1": "", "Dim2": "Val2" } ` , poiché `[Dim1=***&]` viene ignorato per la stringa vuota del valore della dimensione. 

* Se il modello di collegamento all'azione è `https://action-link?filter=[Name/Dim1 eq '%tagset.get('Dim1','')' and ][Name/Dim2 eq '%tagset.get('Dim2','')']` , 
    * Il collegamento all'azione sarà `https://action-link?filter=Name/Dim1 eq 'Val1' and Name/Dim2 eq 'Val2'` quando l'anomalia è `{ "Dim1": "Val1", "Dim2": "Val2" }` , 
    * Il collegamento all'azione si verifica `https://action-link?filter=Name/Dim2 eq 'Val2'` quando l'anomalia è `{ "Dim1": "", "Dim2": "Val2" }` che `[Name/Dim1 eq '***' and ]` viene ignorato per la stringa vuota del valore della dimensione. 
   
### <a name="data-feed-not-available-alert-settings"></a>Impostazioni di avviso "feed di dati non disponibili"

Un feed di dati viene considerato non disponibile se non viene inserito alcun dato dall'origine entro il periodo di tolleranza specificato a partire dal momento in cui viene avviato l'inserimento del feed di dati. In questo caso viene attivato un avviso.

Per configurare un avviso, è prima di tutto necessario [creare un hook](alerts.md#create-a-hook) . Gli avvisi verranno inviati tramite l'hook configurato.

* **Periodo di tolleranza**: l'impostazione del periodo di tolleranza viene utilizzata per determinare quando inviare un avviso se non viene inserito alcun punto dati. Il punto di riferimento è l'ora del primo inserimento. Se l'inserimento ha esito negativo, la metrica Advisor continuerà a eseguire un tentativo a intervalli regolari specificati dalla granularità. Se il periodo di tolleranza continua a non riuscire, verrà inviato un avviso.

* **Auto Snooze**: se questa opzione è impostata su zero, ogni timestamp con *non disponibile* attiva un avviso. Quando si specifica un'impostazione diversa da zero, i timestamp continui dopo il primo timestamp con *non disponibile non* vengono attivati in base all'impostazione specificata.

## <a name="next-steps"></a>Passaggi successivi
- [Configurare le metriche e ottimizzare la configurazione del rilevamento](configure-metrics.md)
- [Regolare il rilevamento anomalie usando il feedback](anomaly-feedback.md)
- [Diagnosticare un evento imprevisto](diagnose-incident.md).
