---
title: Trasformazione Combine Columns by Example (Combina colonne in base all'esempio) usando Azure Machine Learning Workbench
description: Il documento di riferimento per la trasformazione "Combine Columns by Example" (Combina colonne in base all'esempio)
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
ms.openlocfilehash: 528d7ff5f74948fd1ae7b829d4b2fc38fa2f1109
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="combine-columns-by-example-transformation"></a>Trasformazione "Combine Columns by Example" (Combina colonne in base all'esempio)
Questa trasformazione consente all'utente di aggiungere una nuova colonna combinando i valori di più colonne. L'utente può specificare un separatore o fornire esempi di valori combinati per eseguire questa trasformazione. Quando l'utente fornisce esempi di combinazione, la trasformazione viene gestita dallo stesso motore **By-Example** usato nella trasformazione **Derive Column by Example** (Deriva colonna in base all'esempio).

## <a name="how-to-perform-this-transformation"></a>Come eseguire questa trasformazione

Per eseguire questa trasformazione, eseguire la procedura seguente:
1. Selezionare due o più colonne da combinare in un'unica colonna. 
2. Selezionare **Combine Columns by Example** (Combina colonne in base all'esempio) dal menu **Transforms** (Trasformazioni). In alternativa fare clic con il pulsante destro del mouse sull'intestazione di una delle colonne selezionate e selezionare **Combine Columns by Example** (Combina colonne in base all'esempio) dal menu di scelta rapida. Viene aperto l'Editor di trasformazione e viene aggiunta una nuova colonna accanto alla colonna selezionata all'estremità destra. La nuova colonna contiene i valori combinati separati da un separatore predefinito. Le colonne selezionate possono essere identificate tramite caselle di controllo nelle intestazioni di colonna. L'aggiunta e la rimozione di colonne dalla selezione possono essere eseguite usando le caselle di controllo.
3. È possibile aggiornare il valore combinato nella colonna appena creata. Il valore aggiornato viene usato come esempio per apprendere la trasformazione.
4. Fare clic su **OK** per accettare la trasformazione.

### <a name="transform-editor-advanced-mode"></a>Editor di trasformazione: modalità avanzata

La modalità avanzata fornisce un'esperienza più completa per la combinazione di colonne. 

La selezione di **Separator** (Separatore) in **Combine Columns by** (Combina colonne in base all'esempio) consente di specificare le stringhe nella casella di testo **Separator** (Separatore). Premere TAB al di fuori della casella di testo **Separator** (Separatore) per visualizzare in anteprima i risultati della griglia di dati. Premere **OK** per eseguire il commit della trasformazione.

La selezione di **Examples** (Esempi) in **Combine Columns by** (Combina colonne in base all'esempio) consente all'utente di specificare esempi di valori combinati. Per alzare di livello una riga, ad esempio, fare doppio clic sulle righe nella griglia. Digitare l'output previsto nella casella di testo per la riga alzata di livello. Premere TAB al di fuori della casella di testo **Separator** (Separatore) per visualizzare in anteprima i risultati della griglia di dati. Premere **OK** per eseguire il commit della trasformazione. 

L'utente può passare dalla **Modalità di base** alla **Modalità avanzata** facendo clic sui collegamenti nell'editor di trasformazione.

### <a name="transform-editor-send-feedback"></a>Editor di trasformazione: invio di commenti e suggerimenti

Facendo clic sul collegamento **Send feedback** (Invia commenti e suggerimenti) viene aperta la finestra di dialogo **Feedback** (Commenti e suggerimenti) in cui la casella dei commenti include già gli esempi forniti dall'utente. L'utente deve esaminare il contenuto della casella dei commenti e fornire ulteriori dettagli per consentire a Microsoft di comprendere il problema. Se non vuole condividere dati con Microsoft, l'utente deve eliminare i dati di esempio già inseriti prima di fare clic sul pulsante **Send Feedback** (Invia commenti e suggerimenti). 

### <a name="editing-existing-transformation"></a>Modifica della trasformazione esistente

L'utente può modificare una trasformazione **Combine Columns By Example** (Combina colonne in base all'esempio) esistente selezionando l'opzione **Edit** (Modifica) del passaggio di trasformazione. Facendo clic su **Edit** (Modifica) si apre l'Editor di trasformazione nella **modalità base**. È possibile inserire la **modalità avanzata** facendo clic sul collegamento nell'intestazione. Di seguito vengono riportati tutti gli esempi forniti durante la creazione della trasformazione.

## <a name="example-using-separators"></a>Esempio di utilizzo di separatori

In questo esempio come separatore viene usata una virgola seguita da uno spazio per combinare le colonne *Street*, *City*, *State* e *ZIP*.

|Street|city|Stato|ZIP|Colonna|
|:----|:----|:----|:----|:----|
|16011 N.E. 36th Way|REDMOND|WA|98052|16011 N.E. 36th Way, REDMOND, WA, 98052|
|16021 N.E. 36th Way|REDMOND|WA|98052|16021 N.E. 36th Way, REDMOND, WA, 98052|
|16031 N.E. 36th Way|REDMOND|WA|98052|16031 N.E. 36th Way, REDMOND, WA, 98052|
|16041 N.E. 36th Way|REDMOND|WA|98052|16041 N.E. 36th Way, REDMOND, WA, 98052|
|16051 N.E. 36th Way|REDMOND|WA|98052|16051 N.E. 36th Way, REDMOND, WA, 98052|
|16061 N.E. 36th Way|REDMOND|WA|98052|16061 N.E. 36th Way, REDMOND, WA, 98052|
|3460 157th Avenue NE|REDMOND|WA|98052|3460 157th Avenue NE, REDMOND, WA, 98052|
|3350 157th Ave N.E.|REDMOND|WA|98052|3350 157th Ave N.E., REDMOND, WA, 98052|
|3240 157th Avenue N.E.|REDMOND|WA|98052|3240 157th Avenue N.E., REDMOND, WA, 98052|

## <a name="example-using-by-example"></a>Esempio usando By-Example

Il valore in **grassetto** è stato fornito come esempio.

|Data|Mese|Year|Hour|Minuto|Second|Colonna combinata|
|:----|:----|:----|:----|:----|:----|:----|
|13|ott|2016|15|01|23|**13-Oct-2016 15:01:23 PDT**|
|16|ott|2016|16|22|33|16-Oct-2016 15:01:33 PDT|
|17|ott|2016|12|43|12|17-Oct-2016 15:01:12 PDT|
|12|nov|2016|14|22|44|12-Nov-2016 15:01:44 PDT|
|23|nov|2016|01|52|45|23-Nov-2016 15:01:45 PDT|
|16|gen|2017|22|34|56|16-Jan-2016 15:01:56 PDT|
|23|mar|2017|01|55|25|23-Mar-2016 15:01:25 PDT|
|16|apr|2017|11|34|36|16-Apr-2016 15:01:36 PDT|

