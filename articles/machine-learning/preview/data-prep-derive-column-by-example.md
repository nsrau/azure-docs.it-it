---
title: Trasformazione Deriva colonna dall'esempio con Azure Machine Learning Workbench
description: Documento di riferimento per la trasformazione Deriva colonna dall'esempio
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
ms.openlocfilehash: 7ee5a720b12152c27a96ee18f1b11e5fc03a531a
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="derive-column-by-example-transformation"></a>Trasformazione Deriva colonna dall'esempio

La trasformazione **Deriva colonna dall'esempio** consente agli utenti di creare un derivato di una o più colonne esistenti tramite gli esempi del risultato derivato offerti dall'utente. Il derivato può essere una qualsiasi combinazione delle trasformazioni supportate di numero, stringa e data. 

Sono supportate le trasformazioni di stringa, data e numero seguenti:

**Trasformazioni di stringa:** 

Sottostringa che include l'estrazione intelligente di numeri e date, concatenazione, modifica di maiuscole/minuscole, mapping di valori costanti.

**Trasformazioni di data:** 

Modifica del formato della data, estrazione di parti della data, contenitori per il mapping di un periodo di tempo.

Le trasformazioni della data sono piuttosto generiche con alcune limitazioni rilevanti:
* Sono supportati i fusi orari.
* Alcuni formati comuni non sono supportati:
    * Formato per la settimana dell'anno ISO 8601, ad esempio "2009-W53-7" 
    * Unix epoch time.
* Tutti i formati distinguono tra maiuscole e minuscole, in particolare "4 am" non viene riconosciuto come ora, ma viene riconosciuto "4 AM".

**Trasformazioni di numeri:** 

Tondi, interi, arrotondati, binning, riempimento con zeri o spazi, divisione o moltiplicazione per una potenza di 1.000.

**Trasformazioni composte:** 

Qualsiasi combinazione di trasformazioni di data, numero o stringa.

## <a name="how-to-use-this-transformation"></a>Come usare questa informazione

Per eseguire questa trasformazione, eseguire la procedura seguente:
1. Selezionare uno o più colonne da cui si desidera derivare il valore. 
2. Selezionare **Derive Column by Example** (Deriva colonna da esempio) dal menu **Trasformazioni**. In alternativa fare clic con il pulsante destro del mouse sull'intestazione di una delle colonne selezionate e selezionare **Derive Column by Example** (Deriva colonna da esempio) dal menu di scelta rapida. Viene aperto l'editor di trasformazione e viene aggiunta una nuova colonna accanto alla colonna selezionata all'estrema destra. Le colonne selezionate possono essere identificate tramite caselle di controllo nelle intestazioni di colonna. L'aggiunta e la rimozione di colonne dalla selezione possono essere eseguite usando le caselle di controllo nelle intestazioni di colonna.
3. Digitare un esempio di *output* in una riga e premere Invio. A questo punto, Workbench analizza la colonna di input, nonché l'output presente per sintetizzare un programma in grado di trasformare gli input inseriti in output. Il programma sintetizzato viene eseguito su tutte le righe nella griglia dei dati. In casi ambigui e complessi potrebbero essere necessari più esempi. A seconda del fatto che si usi la modalità di base o quella avanzata, è possibile specificare più esempi in modi diversi.
4. Esaminare l'output e fare clic su **OK** per accettare la trasformazione.

### <a name="transform-editor-basic-mode"></a>Editor di trasformazione: modalità di base

La modalità di base offre un'esperienza di modifica inline nella griglia dei dati. È possibile specificare gli esempi di output passando alla cella di interesse e digitando il valore. 

Workbench analizza i dati e tenta di identificare i casi limite che devono essere rivisti dall'utente. Durante l'analisi dei dati, nell'intestazione dell'editor di trasformazione viene visualizzato **Analisi dei dati**. Al completamento dell'analisi, nell'intestazione viene visualizzato **No Suggestions** (Nessun suggerimento) oppure **Review next suggested row** (Rivedi riga successiva suggerita). È possibile spostarsi tra i casi limite facendo clic su **Review next suggested row** (Rivedi riga successiva suggerita). Nel caso in cui il valore per una riga sia errato, è necessario inserire il valore corretto come esempio aggiuntivo. 

### <a name="transform-editor-advanced-mode"></a>Editor di trasformazione: modalità avanzata

La modalità avanzata offre un'esperienza più ricca per quanto riguarda la derivazione delle colonne dall'esempio. Tutti gli esempi vengono visualizzati in un'unica posizione. È anche possibile esaminare tutti i casi limiti in una posizione facendo clic su **Show suggested examples** (Mostra esempi suggeriti). 

Nella modalità avanzata è possibile aggiungere tutte le righe come riga di esempio facendo doppio clic sulla riga nella griglia. Dopo aver copiato la riga come esempio, è anche possibile modificare i dati delle colonne di origine per creare un esempio sintetico. In questo modo, è possibile aggiungere i casi che non sono attualmente presenti nei dati di esempio.

L'utente può passare dalla **Modalità di base** alla **Modalità avanzata** facendo clic sui collegamenti nell'editor di trasformazione.

### <a name="transform-editor-send-feedback"></a>Editor di trasformazione: invio di commenti e suggerimenti

Facendo clic sul collegamento **Send feedback** (Invia commenti e suggerimenti) viene aperta la finestra di dialogo **Feedback** (Commenti e suggerimenti) in cui la casella dei commenti include già gli esempi forniti dall'utente. L'utente deve esaminare il contenuto della casella dei commenti e fornire ulteriori dettagli per consentire a Microsoft di comprendere il problema. Se non vuole condividere dati con Microsoft, l'utente deve eliminare i dati di esempio già inseriti prima di fare clic sul pulsante **Send Feedback** (Invia commenti e suggerimenti). 

### <a name="editing-existing-transformation"></a>Modifica della trasformazione esistente

L'utente può modificare una trasformazione **Deriva colonna da esempio** esistente selezionando l'opzione **Modifica** del passaggio di trasformazione. Facendo clic su **Modifica** viene aperto l'editor di trasformazione in **modalità avanzata** e vengono visualizzati tutti gli esempi forniti durante la creazione della trasformazione.

## <a name="examples-of-string-transformations-by-example"></a>Esempi di trasformazioni di stringhe da esempio


>[!NOTE] 
>I valori in **grassetto** rappresentano gli esempi inseriti per completare la trasformazione nel set di dati visualizzato.


### <a name="s1-extracting-file-names-from-file-paths"></a>S1. Estrazione di nomi di file dai percorsi dei file

Numero di esempi necessari per questo caso: 2

|Input|Output|
|:-----|:-----|
|C:\Python35\Tools\pynche\TypeinViewer.py|**TypeinViewer.py**|
|C:\Python35\Strumenti\pynche\webcolors.txt|webcolors.txt|
|C:\Python35\Strumenti\pynche\websafe.txt|websafe.txt|
|C:\Python35\Strumenti\pynche\X\rgb.txt|rgb.txt|
|C:\Python35\Strumenti\pynche\X\xlicense.txt|xlicense.txt|
|C:\Python35\Strumenti\Script\2to3.py|2to3.py|
|C:\Python35\Strumenti\Script\analyze_dxp.py|**analyze_dxp.py**|
|C:\Python35\Strumenti\Script\byext.py|byext.py|
|C:\Python35\Strumenti\Script\byteyears.py|byteyears.py|
|C:\Python35\Strumenti\Script\checkappend.py|checkappend.py|

### <a name="s2-case-manipulation-during-string-extraction"></a>S2. Modifica di maiuscole e minuscole durante l'estrazione della stringa

Numero di esempi necessari per questo caso: 3

|Input|Output|
|:-----|:-----|
|REINDEER CT & DEAD END; NEW HANOVER; Stazione 332; 12-10-2015 @ 17:10:52;|**New Hanover**|
|BRIAR PATH & WHITEMARSH LN;  HATFIELD TOWNSHIP; Stazione 345; 12-10-2015 @ 17:29:21;|Hatfield Township|
|HAWS AVE; NORRISTOWN; 12-10-2015 @ 14:39:21-Stazione:STA27;|**Norristown**|
|AIRY ST & SWEDE ST;  NORRISTOWN; Stazione 308A; 12-10-2015 @ 16:47:36;|**Norristown**|
|CHERRYWOOD CT & DEAD END;  LOWER POTTSGROVE; Stazione 329; 12-10-2015 @ 16:56:52;|Lower Pottsgrove|
|CANNON AVE & W 9TH ST;  LANSDALE; Stazione 345; 12-10-2015 @ 15:39:04;|Lansdale|
|LAUREL AVE & OAKDALE AVE;  HORSHAM; Stazione 352; 12-10-2015 @ 16:46:48;|Horsham|
|COLLEGEVILLE RD & LYWISKI RD;  SKIPPACK; Stazione 336; 12-10-2015 @ 16:17:05;|Skippack|
|MAIN ST & OLD SUMNEYTOWN PIKE; LOWER SALFORD; Stazione 344; 12-10-2015 @ 16:51:42;|Lower Salford|
|BLUEROUTE & RAMP I476 NB TO CHEMICAL RD; PLYMOUTH; 12-10-2015 @ 17:35:41;|Plymouth|
|RT202 PKWY & KNAPP RD; MONTGOMERY; 12-10-2015 @ 17:33:50;|Montgomery|
|BROOK RD & COLWELL LN; PLYMOUTH; 12-10-2015 @ 16:32:10;|Plymouth|

### <a name="s3-date-format-manipulation-during-string-extraction"></a>S3. Modifica del formato della data durante l'estrazione della stringa

Numero di esempi necessari per questo caso: 1

|Input|Output|
|:-----|:-----|
|MONTGOMERY AVE & WOODSIDE RD;  LOWER MERION; Stazione 313; 12-11-2015 @ 04:11:35;|**12 novembre 2015 4:00**|
|DREYCOTT LN & W LANCASTER AVE;  LOWER MERION; Stazione 313; 12-11-2015 @ 01:29:52;|12 novembre 2015 1:00|
|E LEVERING MILL RD & CONSHOHOCKEN STATE RD; LOWER MERION; 12-11-2015 @ 07:29:58;|12 novembre 2015 7:00|
|PENN VALLEY RD & MANOR RD;  LOWER MERION; Station 313; 12-10-2015 @ 20:53:30;|12 ottobre 2015 20:00|
|BELMONT AVE & OVERHILL RD; LOWER MERION; 12-10-2015 @ 23:02:27;|12 ottobre 2015 23:00|
|W MONTGOMERY AVE & PENNSWOOD RD; LOWER MERION; 12-10-2015 @ 19:25:22;|12 ottobre 2015 19:00|
|ROSEMONT AVE & DEAD END;  LOWER MERION; Stazione 313; 12-10-2015 @ 18:43:07;|12 ottobre 2015 18:00|
|AVIGNON DR & DEAD END; LOWER MERION; 12-10-2015 @ 20:01:29-Station:STA24;|12 ottobre 2015 20:00|

### <a name="s4-concatenating-strings"></a>S4. Concatenazione di stringhe

Numero di esempi necessari per questo caso: 1

>[!NOTE] 
>In questo esempio il carattere speciale · rappresenta gli spazi nella colonna di Output.

|Nome|Iniziale del secondo nome|Cognome|Output|
|:-----|:-----|:-----|:-----|
|Laquanda||Lohmann|Laquanda··Lohmann|
|Claudio|Una |Chew|**Claudio·A·Chew**|
|Sarah-Jane|S|Smith|Sarah-Jane·S·Smith|
|Brandi||Blumenthal|Brandi··Blumenthal|
|Jesusita|R|Journey|Jesusita·R·Journey|
|Hermina||Hults|Hermina··Hults|
|Anne-Marie|W|Jones|Anne-Marie·W·Jones|
|Rico||Ropp|Rico··Ropp|
|Lauren-May||Fullmer|Lauren-May··Fullmer|
|Marc|T|Maine|Marc·T·Maine|
|Angie||Adelman|Angie··Adelman|
|John-Paul||Smith|John-Paul··Smith|
|Song|W|Staller|Song·W·Staller|
|Jill||Jefferies|Jill··Jefferies|
|Ruby-Grace|M|Simmons|Ruby-Grace·M·Simmons|

### <a name="s5-generating-initials"></a>S5. Generazione di iniziali

Numero di esempi necessari per questo caso: 2

|Nome completo|Output|
|:-----|:-----|
|Laquanda Lohmann|**L.L.**|
|Claudio Chew|C.C.|
|Sarah-Jane Smith|S.S.|
|Brandi Blumenthal|B.B.|
|Jesusita Journey|J.J.|
|Hermina Hults|H.H.|
|Anne-Marie Jones|A.J.|
|Rico Ropp|R.R.|
|Lauren-May Fullmer|L.F.|
|Marc Maine|M.M.|
|Angie Adelman|A.A.|
|John-Paul Smith|**J.S.**|
|Song Staller|S.S.|
|Jill Jefferies|J.J.|
|Ruby-Grace Simmons|R.S.|


### <a name="s6-mapping-constant-values"></a>S6. Mapping dei valori costanti

Numero di esempi necessari per questo caso: 3

|Sesso amministrativo|Output|
|:-----|:-----:|
|Maschio|**0**|
|Femmina|**1**|
|Sconosciuto|**2**|
|Femmina|1|
|Femmina|1|
|Maschio|0|
|Sconosciuto|2|
|Maschio|0|
|Femmina|1|

## <a name="examples-of-number-transformations-by-example"></a>Esempi di trasformazioni del numero da esempio

>[!NOTE] 
>I valori in **grassetto** rappresentano gli esempi inseriti per completare la trasformazione nel set di dati visualizzato.


### <a name="n1-rounding-to-nearest-10"></a>N1. Arrotondamento al numero in base 10 più vicino

Numero di esempi necessari per questo caso: 1

|Input|Output|
|-----:|-----:|
|112|**110**|
|117|120|
|11112|11110|
|11119|11120|
|548|550|

### <a name="n2-rounding-down-to-nearest-10"></a>N2. Arrotondamento per difetto al numero in base 10 più vicino

Numero di esempi necessari per questo caso: 2

|Input|Output|
|-----:|-----:|
|112|**110**|
|117|**110**|
|11112|11110|
|11119|11110|
|548|540|

### <a name="n3-rounding-to-nearest-005"></a>N3. Arrotondamento per eccesso o per difetto del secondo numero decimale

Numero di esempi necessari per questo caso: 2

|Input|Output|
|-----:|-----:|
|-75,5812935|**-75,60**|
|-75,2646799|-75,25|
|-75,3519752|-75,35|
|-75,343513|**-75,35**|
|-75,6033497|-75,60|
|-75,283245|-75,30|

### <a name="n4-binning"></a>N4. Binning

Numero di esempi necessari per questo caso: 1

|Input|Output|
|-----:|:-----:|
|20,16|**20-25**|
|14.32|10-15|
|5,44|5-10|
|3,84|0-5|
|3,73|0-5|
|7,36|5-10|

### <a name="n5-scaling-by-1000"></a>N5. Ridimensionamento per 1.000

Numero di esempi necessari per questo caso: 1

|Input|Output|
|-----:|-----:|
|-243|**-243.000**|
|-12,5|-12.500|
|-2.345,23292|-2.345.232,92|
|-1.202,3433|-1.202.343,3|
|1.202,3433|1.202.343,3|

### <a name="n6-padding"></a>N6. Riempimento

Numero di esempi necessari per questo caso: 1

|Codice|Output|
|-----:|-----:|
|5828|**05828**|
|44130|44130|
|49007|49007|
|29682|29682|
|4759|04759|
|10029|10029|
|7204|07204|

## <a name="examples-of-date-transformations-by-example"></a>Esempi di trasformazioni della data da esempio

>[!NOTE] 
>I valori in **grassetto** rappresentano gli esempi inseriti per completare la trasformazione nel set di dati visualizzato.


### <a name="d1-extracting-date-parts"></a>D1. Estrazione di parti della data

Queste parti della data sono state estratte tramite trasformazioni diverse dall'esempio sullo stesso set di dati. Le stringhe in grassetto rappresentano gli esempi inseriti per la relativa trasformazione.

|Datetime|Giorno della settimana|Data|Mese|Year|Hour|Minuto|Second|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|31-gen-2031 05:54:18|**Ven**|**31**|**gen**|**2031**|**5**|**54**|**18**|
|17-gen-1990 13:32:01|Mer|17|gen|1990|13|32|01|
|14-feb-2034 05:36:07|Mar|14|feb|2034|5|36|07|
|14-mar-2002 13:16:16|Giov|14|mar|2002|13|16|16|
|21-gen-1985 05:44:43|Lun|21|gen|1985|5|44|**43**|
|16-ago-1985 01:11:56|Ven|16|ago|1985|1|11|56|
|20-dic-2033 18:36:29|Mar|20|dic|2033|18|36|29|
|16-lug-1984 10:21:59|Lun|16|lug|1984|10|21|59|
|13-gen-2038 10:59:36|Mer|13|gen|2038|10|59|36|
|14-ago-1982 15:13:54|Sab|14|ago|1982|15|13|54|
|22-nov-2030 08:18:08|Ven|22|nov|2030|8|18|08|
|21-ott-1997 08:42:58|Mar|21|ott|1997|8|42|58|
|28-nov-2006 14:19:15|Mar|28|nov|2006|14|19|15|
|29-apr-2031 04:59:45|Mar|29|apr|2031|4|59|45|
|29-gen-2032 02:38:36|Giov|29|gen|2032|2|38|36|
|11-mag-2028 15:31:52|Giov|11|mag|2028|15|31|52|
|15-lug-1977 12:45:39|Ven|15|lug|1977|12|45|39|
|27-gen-2029 05:55:41|Sab|27|gen|2029|5|55|41|
|03-mar-2024 10:17:49|Dom|3|mar|2024|10|17|49|
|14-apr-2010 00:23:13|Mer|14|apr|2010|0|23|13|

### <a name="d2-formatting-dates"></a>D2. Formattazione delle date

Le formattazioni della data sono state eseguite tramite trasformazioni diverse dall'esempio sullo stesso set di dati. Le stringhe in grassetto rappresentano gli esempi inseriti per la relativa trasformazione.

|Datetime|Formato1|Formato2|Formato3|Formato4|Formato5|
|-----:|-----:|-----:|-----:|-----:|-----:|
|31-gen-2031 05:54:18|**31/1/2031**|**Venerdì, 31 gennaio 2031**|**31012031 5:54**|**31/1/2031 5:54**|**Q1 2031**|
|17-gen-1990 13:32:01|17/1/1990|Mercoledì, 17 gennaio 1990|17011990 13:32|17/1/1990 13:32|Q1 1990|
|14-feb-2034 05:36:07|14/2/2034|Martedì, 14 febbraio 2034|14022034 5:36|14/2/2034 5:36|Q1 2034
|14-mar-2002 13:16:16|14/3/2002|Giovedì, 14 marzo 2002|14032002 13:16|14/3/2002 13:16|Q1 2002
|21-gen-1985 05:44:43|21/1/1985|Lunedì, 21 gennaio 1985|21011985 5:44|21/1/1985 5:44|Q1 1985
|16-ago-1985 01:11:56|16/8/1985|Venerdì, 16 agosto 1985|16081985 1:11|16/8/1985 1:11|Q3 1985
|20-dic-2033 18:36:29|20/12/2033|Martedì, 20 dicembre 2033|20122033 18:36|20/12/2033 18:36|Q4 2033
|16-lug-1984 10:21:59|16/7/1984|Lunedì, 16 luglio 1984|16071984 10:21|16/7/1984 10:21|Q3 1984
|13-gen-2038 10:59:36|13/1/2038|Mercoledì, 13 gennaio 2038|13012038 10:59|13/1/2038 10:59|Q1 2038
|14-ago-1982 15:13:54|14/8/1982|Sabato, 14 agosto 1982|14081982 15:13|14/8/1982 15:13|Q3 1982
|22-nov-2030 08:18:08|22/11/2030|Venerdì, 22 novembre 2030|22112030 8:18|22/11/2030 8:18|Q4 2030
|21-ott-1997 08:42:58|21/10/1997|Martedì, 21 ottobre 1997|21101997 8:42|21/10/1997 8:42|Q4 1997
|28-nov-2006 14:19:15|28/11/2006|Martedì, 28 novembre 2006|28112006 14:19|28/11/2006 14:19|Q4 2006
|29-apr-2031 04:59:45|29/4/2031|Martedì, 29 aprile 2031|29042031 4:59|29/4/2031 4:59|Q2 2031
|29-gen-2032 02:38:36|29/1/2032|Giovedì, 29 gennaio 2032|29012032 2:38|29/1/2032 2:38|Q1 2032
|11-mag-2028 15:31:52|11/5/2028|Giovedì, 11 maggio 2028|11052028 15:31|11/5/2028 15:31|Q2 2028
|15-lug-1977 12:45:39|15/7/1977|Venerdì, 15 luglio 1977|15071977 12:45|15/7/1977 12:45|Q3 1977
|27-gen-2029 05:55:41|27/1/2029|Sabato, 27 gennaio 2029|27012029 5:55|27/1/2029 5:55|Q1 2029
|03-mar-2024 10:17:49|3/3/2024|Domenica, 3 marzo 2024|03032024 10:17|3/3/2024 10:17|Q1 2024
|14-apr-2010 00:23:13|14/4/2010|Mercoledì, 14 aprile 2010|14042010 0:23|14/4/2010 00:23|Q2 2010


### <a name="d3-mapping-time-to-time-periods"></a>D3. Mapping di un periodo di tempo

Il mapping di un periodo di tempo è stato eseguito tramite trasformazioni diverse dall'esempio sullo stesso set di dati. Le stringhe in grassetto rappresentano gli esempi inseriti per la relativa trasformazione.

|Datetime|Periodo (secondi)|Periodo (minuti)|Periodo (due ore)|Periodo (30 minuti)|
|-----:|-----:|-----:|-----:|-----:|
|31-gen-2031 05:54:18|**0-20**|**45-60**|**5:00-7:00**|**5:30-6:00**|
|17-gen-1990 13:32:01|**0-20**|30-45|13:00-15:00|13:30-14:00|
|14-feb-2034 05:36:07|0-20|30-45|5:00-7:00|5:30-6:00|
|14-mar-2002 13:16:16|0-20|15-30|13:00-15:00|13:00-13:30|
|21-gen-1985 05:44:43|40-60|30-45|5:00-7:00|5:30-6:00|
|16-ago-1985 01:11:56|40-60|0-15|1:00-3:00|1:00-1:30|
|20-dic-2033 18:36:29|20-40|30-45|17:00-19:00|18:30-19:00|
|16-lug-1984 10:21:59|40-60|15-30|09:00-11:00|10:00-10:30|
|13-gen-2038 10:59:36|20-40|45-60|09:00-11:00|10:30-11:00|
|14-ago-1982 15:13:54|40-60|0-15|15:00-17:00|15:00-15:30|
|22-nov-2030 08:18:08|0-20|15-30|7:00-9:00|8:00-8:30|
|21-ott-1997 08:42:58|40-60|30-45|7:00-9:00|8:30-9:00|
|28-nov-2006 14:19:15|0-20|15-30|13:00-15:00|14:00-14:30|
|29-apr-2031 04:59:45|40-60|45-60|3:00-5:00|4:30-5:00|
|29-gen-2032 02:38:36|20-40|30-45|1:00-3:00|2:30-3:00|
|11-mag-2028 15:31:52|40-60|30-45|15:00-17:00|15:30-16:00|
|15-lug-1977 12:45:39|20-40|45-60|11:00-13:00|12:30-13:00|
|27-gen-2029 05:55:41|40-60|45-60|5:00-7:00|5:30-6:00|
|03-mar-2024 10:17:49|40-60|15-30|09:00-11:00|10:00-10:30|
|14-apr-2010 00:23:13|0-20|15-30|23:00-1:00|0:00-0:30|

## <a name="examples-of-composite-transformations-by-example"></a>Esempi di trasformazioni composite da esempio

|durata del viaggio|ora di inizio|ID stazione di partenza|latitudine della stazione di partenza|longitudine della stazione di partenza|Tipo di utente|Colonna|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|61|08-01-2016 16:09:32|107|42.3625|-71.08822|Sottoscrittore|**Un sottoscrittore ha preso una bicicletta dalla stazione 107, lat/long (42.363,-71.088), l'08 gennaio 2016 alle 16:00 circa. La durata del viaggio è stata di 61 minuti**|
|61|17-01-2016 09:28:10|74|42.373268|-71.118579|Customer|Un cliente ha preso una bicicletta dalla stazione 74, lat/long (42.373,-71.119), il 17 gennaio 2016 alle 9:00 circa. La durata del viaggio è stata di 61 minuti|
|62|25-01-2016 08:10:26|176|42.386748020450561|-71.119018793106079|Sottoscrittore|Un sottoscrittore ha preso una bicicletta dalla stazione 176, lat/long (42.387,-71.119), il 25 gennaio 2016 alle 8:00 circa. La durata del viaggio è stata di 62 minuti|
|63|08-01-2016 10:10:29|107|42.3625|-71.08822|Sottoscrittore|Un sottoscrittore ha preso una bicicletta dalla stazione 107, lat/long (42.363,-71.088), l'08 gennaio 2016 alle 10:00 circa. La durata del viaggio è stata di 63 minuti|
|64|15-01-2016 19:42:08|68|42.36507|-71.1031|Sottoscrittore|Un sottoscrittore ha preso una bicicletta dalla stazione 68, lat/long (42.365,-71.103), il 15 gennaio 2016 alle 7:00 circa. La durata del viaggio è stata di 64 minuti|
|64|22-01-2016 18:16:13|115|42.387995|-71.119084|Sottoscrittore|Un sottoscrittore ha preso una bicicletta dalla stazione 115, lat/long (42.388,-71.119), il 22 gennaio 2016 alle 18:00 circa. La durata del viaggio è stata di 64 minuti|
|68|18-01-2016 09:51:52|178|42.359573201090441|-71.101294755935669|Sottoscrittore|Un sottoscrittore ha preso una bicicletta dalla stazione 178, lat/long (42.360,-71.101), il 18 gennaio 2016 alle 9:00 circa. La durata del viaggio è stata di 68 minuti|
|69|14-01-2016 08:57:55|176|42.386748020450561|-71.119018793106079|Sottoscrittore|Un sottoscrittore ha preso una bicicletta dalla stazione 176, lat/long (42.387,-71.119), il 14 gennaio 2016 alle 8:00 circa. La durata del viaggio è stata di 69 minuti|
|69|13-01-2016 22:12:55|141|42.363560158429884|-71.08216792345047|Sottoscrittore|Un sottoscrittore ha preso una bicicletta dalla stazione 141, lat/long (42.364,-71.082), il 13 gennaio 2016 alle 22:00 circa. La durata del viaggio è stata di 69 minuti|
|69|15-01-2016 08:13:09|176|42.386748020450561|-71.119018793106079|Sottoscrittore|Un sottoscrittore ha preso una bicicletta dalla stazione 176, lat/long (42.387,-71.119), il 15 gennaio 2016 alle 8:00 circa. La durata del viaggio è stata di 69 minuti|


## <a name="technical-notes"></a>Note tecniche

### <a name="conditional-transformations"></a>Trasformazioni condizionali
In alcuni casi, è impossibile trovare una trasformazione che soddisfi gli esempi specificati. In questi casi, la trasformazione per derivare una colonna dall'esempio tenta di raggruppare gli input in base a un modello e di apprendere trasformazioni diverse per ogni gruppo. Questa operazione si chiama **trasformazione condizionale**. La **trasformazione condizionale** viene tentata solo per le trasformazioni con una sola colonna di input. 

### <a name="reference"></a>riferimento
Altre informazioni sulla tecnologia di trasformazione della stringa dall'esempio sono reperibili in [questa pubblicazione](https://www.microsoft.com/en-us/research/publication/automating-string-processing-spreadsheets-using-input-output-examples/).
