---
title: Trasformazione Split Column by Example (Dividi colonna in base all'esempio) usando Azure Machine Learning Workbench
description: Il documento di riferimento per la trasformazione Split Column By Example (Dividi colonna in base all'esempio)
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 5d624735a91d0828c4ac3796bde6c17acf6e131a
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="split-column-by-example-transformation"></a>Trasformazione Split Column by Example (Dividi colonna in base all'esempio)
Questa trasformazione divide in modo predittivo il contenuto di una colonna in base a limiti significativi senza richiedere l'input dell'utente. L'algoritmo di divisione seleziona i limiti dopo avere analizzato il contenuto della colonna. Questi limiti possono essere definiti da:
* Un delimitatore predefinito
* Più delimitatori arbitrari presenti in contesti specifici oppure
* Criteri di dati o alcuni tipi di entità

Gli utenti possono anche controllare il comportamento di divisione in modalità avanzata, che consente di specificare i delimitatori o di fornire esempi della divisione desiderata.

In teoria, le operazioni di divisione possono essere eseguite anche nel Workbench usando una serie di trasformazioni *Derive Column by Example* (Deriva colonna in base all'esempio). Tuttavia, se sono presenti numerose colonne, la derivazione di ogni colonna singolarmente può richiedere molto tempo, anche usando l'approccio By-Example. La divisione predittiva consente di eseguire la divisione in modo semplice senza che l'utente debba fornire esempi per ogni colonna. 

## <a name="how-to-perform-this-transformation"></a>Come eseguire questa trasformazione

1. Selezionare la colonna da dividere. 
2. Selezionare **Split Column by Example** (Dividi colonna in base all'esempio) dal menu **Transforms** (Trasformazioni). In alternativa fare clic con il pulsante destro del mouse sull'intestazione della colonna selezionata e scegliere **Split Column by Example** (Dividi colonna in base all'esempio) dal menu di scelta rapida. Verrà aperto l'Editor di trasformazione e nuove colonne verranno aggiunte accanto alla colonna selezionata. A questo punto, il Workbench analizza la colonna di input, determina i limiti della divisione e sintetizza un programma per dividere la colonna nel modo visualizzato nella griglia. Il programma sinterizzato viene eseguito su tutte le righe nella colonna. I delimitatori, se presenti, vengono esclusi dal risultato finale.
3. È possibile fare clic su **Advanced mode** (Modalità avanzata) per un controllo più preciso sulla trasformazione di divisione. 
4. Esaminare l'output e fare clic su **OK** per accettare la trasformazione. 

La trasformazione mira a produrre lo stesso numero di colonne risultanti per tutte le righe. Se una riga non può essere divisa in base a determinati limiti, per impostazione predefinita viene generato il valore *null* per tutte le colonne. Questo comportamento può essere modificato in **modalità avanzata**.

### <a name="transform-editor-advanced-mode"></a>Editor di trasformazione: modalità avanzata
La **modalità avanzata** fornisce un'esperienza più completa per la divisione di colonne. 

Selezionando **Keep Delimiter Columns** (Mantieni colonne delimitatore), i delimitatori vengono inclusi nel risultato finale. Per impostazione predefinita i delimitatori vengono esclusi.

La specifica di **delimitatori** esegue l'override della logica di selezione automatica di delimitatori. È possibile specificare più delimitatori, uno in ogni riga, come **delimitatori**. Tutti questi caratteri vengono usati come delimitatori per dividere la colonna.

In alcuni casi, la divisione di un valore in base a determinati limiti genera un numero diverso di colonne rispetto alla maggior parte degli altri. In questi casi viene usato **Fill Direction** (Direzione riempimento) per stabilire l'ordine in cui è necessario riempire le colonne.

Facendo clic su **Show suggested examples** (Mostra esempi suggeriti) vengono visualizzate le righe rappresentative per cui l'utente deve fornire un esempio di divisione. L'utente può fare clic sulla freccia **verso l'alto** a destra della riga suggerita per alzare di livello la riga come esempio. 

L'utente può **eliminare la colonna** o **inserire nuove colonne** facendo clic con il pulsante destro del mouse sull'intestazione della **tabella di esempi**.

L'utente può copiare e incollare i valori di una cella in un'altra per fornire un esempio di divisione.

L'utente può passare dalla **Modalità di base** alla **Modalità avanzata** facendo clic sui collegamenti nell'editor di trasformazione.

### <a name="transform-editor-send-feedback"></a>Editor di trasformazione: invio di commenti e suggerimenti

Facendo clic sul collegamento **Send feedback** (Invia commenti e suggerimenti) viene aperta la finestra di dialogo **Feedback** (Commenti e suggerimenti) in cui la casella dei commenti include già le selezioni dei parametri e gli esempi forniti dall'utente. L'utente deve esaminare il contenuto della casella dei commenti e fornire ulteriori dettagli per consentire a Microsoft di comprendere il problema. Se non vuole condividere dati con Microsoft, l'utente deve eliminare i dati di esempio già inseriti prima di fare clic sul pulsante **Send Feedback** (Invia commenti e suggerimenti). 


### <a name="editing-an-existing-transformation"></a>Modifica di una trasformazione esistente

L'utente può modificare una trasformazione **Split Column By Example** (Dividi colonna in base all'esempio) esistente selezionando l'opzione **Edit** (Modifica) del passaggio di trasformazione. Facendo clic su **Modifica** viene aperto l'editor di trasformazione in **modalità avanzata** e vengono visualizzati tutti gli esempi forniti durante la creazione della trasformazione.

## <a name="examples-of-splitting-on-a-fixed-single-character-delimiter"></a>Esempi di divisione in base a un delimitatore a carattere singolo predefinito

I campi dati sono spesso separati da un unico delimitatore predefinito, ad esempio una virgola in un formato CSV. La trasformazione di divisione tenta di dedurre automaticamente questi delimitatori. Ad esempio, nello scenario seguente il carattere "." viene dedotto automaticamente come delimitatore.

### <a name="splitting-ip-addresses"></a>Divisione di indirizzi IP

I valori nella prima colonna vengono divisi in modo predittivo in quattro colonne.

|IP|IP_1|IP_2|IP_3|IP_4|
|:-----|:-----|:-----|:-----|:-----|
|192.168.0.102|192|168|0|102|
|192.138.0.101|192|138|0|101|
|192.168.0.102|192|168|0|102|
|192.158.1.202|192|158|1|202|
|192.168.0.102|192|168|0|102|
|192.169.1.102|192|169|1|102|

## <a name="examples-of-splitting-on-multiple-delimiters-within-particular-contexts"></a>Esempi di divisione in base a più delimitatori all'interno di contesti specifici

I dati dell'utente possono includere molti delimitatori diversi che separano campi differenti. Inoltre, solo alcune occorrenze di una stringa di delimitazione, e non tutte, possono essere usate come delimitatore. Nel caso seguente, ad esempio, il set di delimitatori richiesto è "-","," e ":" per generare l'output desiderato. Tuttavia, non tutte le occorrenze del carattere ":" devono funzionare come punto di divisione poiché il valore dell'ora non deve essere diviso, ma mantenuto in una singola colonna. La trasformazione di divisione deduce i delimitatori all'interno di contesti in cui sono presenti nei dati di input rispetto a qualsiasi occorrenza possibile del delimitatore. La trasformazione riconosce i tipi di dati comuni, ad esempio date e ore.   

### <a name="splitting-store-opening-timings"></a>Divisione di intervalli di apertura di uno store

I valori nella colonna *Timings* seguente vengono divisi in modo predittivo nelle nove colonne indicate nella tabella sottostante.

|Timings|
|:-----|
|Monday - Friday: 7:00 am - 6:00 pm,Saturday: 9:00 am - 5:00 pm,Sunday: Closed|
|Monday - Friday: 9:00 am - 6:00 pm,Saturday: 4:00 am - 4:00 pm,Sunday: Closed|
|Monday - Friday: 8:30 am - 7:00 pm,Saturday: 3:00 am - 2:30 pm,Sunday: Closed|
|Monday - Friday: 8:00 am - 6:00 pm,Saturday: 2:00 am - 3:00 pm,Sunday: Closed|
|Monday - Friday: 4:00 am - 7:00 pm,Saturday: 9:00 am - 4:00 pm,Sunday: Closed|
|Monday - Friday: 8:30 am - 4:30 pm,Saturday: 9:00 am - 5:00 pm,Sunday: Closed|
|Monday - Friday: 5:30 am - 6:30 pm,Saturday: 5:00 am - 4:00 pm,Sunday: Closed|
|Monday - Friday: 8:30 am - 8:30 pm,Saturday: 6:00 am - 5:00 pm,Sunday: Closed|
|Monday - Friday: 8:00 am - 9:00 pm,Saturday: 9:00 am - 8:00 pm,Sunday: Closed|
|Monday - Friday: 10:00 am - 9:30 pm,Saturday: 9:30 am - 3:00 pm,Sunday: Closed|

|Timings_1|Timings_2|Timings_3|Timings_4|Timings_5|Timings_6|Timings_7|Timings_8|Timings_9|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|Monday|Friday|7:00 am|6:00 pm|Sabato|9:00 am|5:00 pm|Sunday|Chiuso|
|Monday|Friday|9:00 am|6:00 pm|Sabato|4:00 am|4:00 pm|Sunday|Chiuso|
|Monday|Friday|8:30 am|7:00 pm|Sabato|3:00 am|2:30 pm|Sunday|Chiuso|
|Monday|Friday|8:00 am|6:00 pm|Sabato|2:00 am|3:00 pm|Sunday|Chiuso|
|Monday|Friday|4:00 am|7:00 pm|Sabato|9:00 am|4:00 pm|Sunday|Chiuso|
|Monday|Friday|8:30 am|4:30 pm|Sabato|9:00 am|5:00 pm|Sunday|Chiuso|
|Monday|Friday|5:30 am|6:30 pm|Sabato|5:00 am|4:00 pm|Sunday|Chiuso|
|Monday|Friday|8:30 am|8:30 pm|Sabato|6:00 am|5:00 pm|Sunday|Chiuso|
|Monday|Friday|8:00 am|9:00 pm|Sabato|9:00 am|8:00 pm|Sunday|Chiuso|
|Monday|Friday|10:00 am|9:30 pm|Sabato|9:30 am|3:00 pm|Sunday|Chiuso|

### <a name="splitting-iis-log"></a>Divisione di log IIS

Di seguito è riportato un altro esempio di più delimitatori arbitrari. Questo esempio include anche un delimitatore contestuale "/", che non deve essere diviso negli URL o nei percorsi di file. È impegnativo eseguire questa divisione usando molte trasformazioni *Derive Column by Example* (Deriva colonna in base all'esempio) e fornendo esempi per ogni campo. La trasformazione di divisione consente di eseguire la divisione predittiva senza fornire esempi.

|logtext|
|:-----|
|192.128.138.20 - - [16/Oct/2016 16:22:33 -0200] "GET /images/picture.gif HTTP/1.1" 234 343 www.yahoo.com "http://www.example.com/" "Mozilla/4.0 (compatible; MSIE 4)" "-"|
|10.128.72.213 - - [17/Oct/2016 12:43:12 +0300] "GET /news/stuff.html HTTP/1.1" 200 6233 www.aol.com "http://www.sample.com/" "Mozilla/5.0 (MSIE)" "-"|
|192.165.71.165 - - [12/Nov/2016 14:22:44 -0500] "GET /sample.ico HTTP/1.1" 342 7342 www.facebook.com "-" "Mozilla/5.0 (Windows; U; Windows NT 5.1; rv:1.7.3)" "-"|
|10.166.64.165 - - [23/Nov/2016 01:52:45 -0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.167.1.193 - - [16/Jan/2017 22:34:56 +0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.147.76.193 - - [28/Jan/2017 26:36:16 +0800] "GET /search.php HTTP/1.1" 400 1777 www.bing.com "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)" "-"|
|192.166.64.165 - - [23/Mar/2017 01:55:25 -0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|11.167.1.193 - - [16/Apr/2017 11:34:36 +0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|

Viene operata la seguente divisione:

|logtext_1|logtext_2|logtext_3|logtext_4|logtext_5|logtext_6|logtext_7|logtext_8|logtext_9|logtext_10|logtext_11|logtext_12|logtext_13|logtext_14|logtext_15|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|192.128.138.20|16/Oct/2016|16:22:33|-0200|GET|images/picture.gif|HTTP|1.1|234|343|www.yahoo.com|http://www.example.com/|Mozilla|4.0|compatibile; MSIE 4|
|10.128.72.213|17/Oct/2016|12:43:12|+0300|GET|news/stuff.html|HTTP|1.1|200|6233|www.aol.com|http://www.sample.com/|Mozilla|5.0|MSIE|
|192.165.71.165|12/Nov/2016|14:22:44|-0500|GET|sample.ico|HTTP|1.1|342|7342|www.facebook.com|-|Mozilla|5.0|Windows; U; Windows NT 5.1; rv:1.7.3|
|10.166.64.165|23/Nov/2016|01:52:45|-0800|GET|style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|192.167.1.193|16/Jan/2017|22:34:56|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|
|192.147.76.193|28/Jan/2017|26:36:16|+0800|GET|search.php|HTTP|1.1|400|1777|www.bing.com|-|Mozilla|4.0|compatible; MSIE 6.0; Windows NT 5.1|
|192.166.64.165|23/Mar/2017|01:55:25|-0800|GET|style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|11.167.1.193|16/Apr/2017|11:34:36|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|

## <a name="examples-of-splitting-without-delimiters"></a>Esempi di divisione senza delimitatori
In alcuni casi non esistono delimitatori effettivi e i campi dati possono essere disposti uno accanto all'altro in modo contiguo. In questo caso, la trasformazione di divisione rileva automaticamente i criteri nei dati per dedurre i probabili punti di divisione. Ad esempio, nel seguente scenario è necessario separare l'importo dal tipo di valuta; la trasformazione di divisione deduce automaticamente il limite tra i dati numerici e non numerici come punto di divisione.

### <a name="splitting-amount-with-currency-symbol"></a>Divisione dell'importo con il simbolo di valuta

|Amount|Amount_1|Amount_2|
|:-----|:-----|:-----|
|\$14|$|14|
|£9|£|9|
|\$34|$|34|
|€ 18|€ |18|
|\$42|$|42|
|\$7|$|7|
|£42|£|42|
|\$16|$|16|
|€ 16|€ |16|
|\$15|$|15|
|\$16|$|16|
|€ 64|€ |64|

Nell'esempio seguente si vuole separare i valori del peso dalle unità di misura. Di nuovo la trasformazione di divisione rileva automaticamente il limite significativo e lo preferisce rispetto ad altri possibili delimitatori, ad esempio il carattere ".". 

### <a name="splitting-weights-with-units"></a>Divisione dei pesi con le unità

|Peso|Weight_1|Weight_2|
|:-----|:-----|:-----|
|2.27KG|2.27|KG|
|1L|1|L|
|2.5KG|2.5|KG|
|2KG|2|KG|
|1.7KGA|1.7|KGA|
|3KG|3|KG|
|2KG|2|KG|
|125G|125|G|
|500 G|500|G|
|1,5 KGA|1,5|KGA|

## <a name="technical-notes"></a>Note tecniche

La funzionalità della trasformazione di divisione si basa sulla tecnica **Program Synthesis predittiva**. Con questa tecnica i programmi di trasformazione dei dati vengono appresi automaticamente in base ai dati di input. I programmi vengono sintetizzati in un linguaggio specifico di dominio. Il linguaggio Domain-Specific Language è basato su delimitatori e campi presenti in specifici contesti di espressioni regolari. Altre informazioni su questa tecnologia sono disponibili in una [recente pubblicazione su questo argomento](https://www.microsoft.com/en-us/research/publication/automated-data-extraction-using-predictive-program-synthesis/). 
