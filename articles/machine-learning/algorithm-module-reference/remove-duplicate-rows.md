---
title: 'Rimuovere le righe Duplicate: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Remove Duplicate Rows nel servizio Azure Machine Learning per rimuovere i duplicati potenziali da un set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: dce90d911085c1f7330a2e0952bb9576c1d765fa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029296"
---
# <a name="remove-duplicate-rows-module"></a>Rimuovere il modulo di righe Duplicate

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per rimuovere i duplicati potenziali da un set di dati.

Si supponga ad esempio i dati simile al seguente e rappresenta più record per i pazienti. 

| PatientID | Initials| Sesso|Age|Ammessi|
|----|----|----|----|----|
|1|F.M.| M| 53| gen|
|2| F.A.M.| M| 53| gen|
|3| F.A.M.| M| 24| gen|
|3| F.M.| M| 24| feb|
|4| F.M.| M| 23| feb|
| | F.M.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| NaN| |
|7| F.A.M.| M| NaN| |

Ovviamente, questo esempio include più colonne con dati potenzialmente duplicati. Se sono effettivamente i duplicati dipende la conoscenza dei dati. 

+ Ad esempio, si potrebbe sapere che molti pazienti hanno lo stesso nome. È non eliminare i duplicati con qualsiasi nome colonne, ma solo il **ID** colonna. In questo modo, solo le righe con valori di ID duplicati vengono esclusi, indipendentemente dal fatto che i pazienti hanno lo stesso nome o non.

+ In alternativa, è possibile decidere di consentire duplicati nel campo ID e usare una combinazione di file per trovare i record univoci, ad esempio nome, cognome, età e sesso.  

Per impostare criteri per il fatto che una riga è duplicata o meno, si specifica una singola colonna o un set di colonne da utilizzare come **chiavi**. Due righe vengono considerate duplicati solo quando i valori nelle **tutti** colonne chiave sono uguali. Se una riga contiene un valore mancante per **chiavi**, essi non verrà considerate righe duplicate. Ad esempio, se genere ed età vengono impostate come chiavi nella precedente tabella, riga 6 e 7 non sono le righe duplicate date hanno valore mancante in Age.

Quando si esegue il modulo, crea un set di dati candidati e restituisce un set di righe che non hanno duplicati nel set di colonne specificato.

> [!IMPORTANT]
> Il set di dati di origine non viene modificato; questo modulo consente di creare un nuovo set di dati che viene filtrata in modo che l'esclusione dei duplicati, in base ai criteri specificati.

## <a name="how-to-use-remove-duplicate-rows"></a>Come usare Remove Duplicate Rows

1. Aggiungere il modulo nell'esperimento. È possibile trovare il **Remove Duplicate Rows** modulo **Data Transformation**, **manipolazione**.  

2. Connettere il set di dati che si desidera verificare per le righe duplicate.

3. Nel **delle proprietà** riquadro, di sotto **espressione di filtro di selezione di colonna della chiave**, fare clic su **Avvia selettore di colonna**, per scegliere le colonne da utilizzare nell'identificazione di duplicati.

    In questo contesto **chiave** non significa che un identificatore univoco. Tutte le colonne selezionate tramite il selettore di colonna vengono designate come **colonne chiave**. Tutte le colonne non selezionate vengono considerate colonne non chiave. La combinazione di colonne selezionate come chiavi determina l'univocità dei record. (Considerarla come istruzione SQL che utilizza più join predicati di uguaglianza.)

    Esempi:

    + "Voglio garantire che gli ID siano univoci": Scegliere solo la colonna ID.
    + "Voglio assicurarsi che la combinazione di nome, cognome e ID sia univoca": Selezionare tutte e tre colonne.

4. Usare la **Mantieni la prima riga duplicata** casella di controllo per indicare quale riga da restituire quando i duplicati vengono trovati:

    + Se selezionata, viene restituita nella prima riga e altri utenti eliminati. 
    + Se si deseleziona questa opzione, viene mantenuta l'ultima riga duplicata nei risultati e altre vengono ignorate. 

5. Eseguire l'esperimento.

6. Per esaminare i risultati, il pulsante destro del modulo, selezionare **set di dati risultati**, fare clic su **Visualize**. 

> [!TIP]
> Se i risultati sono difficili da comprendere, o se si desidera escludere alcune colonne dalla considerazione, è possibile rimuovere le colonne usando il [Select Columns in Dataset](./select-columns-in-dataset.md) modulo.

## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 