---
title: 'Rimuovi righe duplicate: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Remove duplicate rows nel servizio Azure Machine Learning per rimuovere potenziali duplicati da un set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: b16e745de277d5aa262f1e1624df22f97d0cf29c
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128531"
---
# <a name="remove-duplicate-rows-module"></a>Rimuovi modulo righe duplicate

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per rimuovere potenziali duplicati da un set di dati.

Si supponga, ad esempio, che i dati abbiano un aspetto simile al seguente e che rappresenti più record per i pazienti. 

| PatientID | Initials| Sesso|Tempo di risoluzione|Ammessi|
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

Ovviamente, questo esempio include più colonne con dati potenzialmente duplicati. Il fatto che siano duplicati dipende dalla propria conoscenza dei dati. 

+ Ad esempio, si potrebbe capire che molti pazienti hanno lo stesso nome. I duplicati non vengono eliminati usando le colonne dei nomi, bensì solo la colonna **ID** . In questo modo, vengono escluse solo le righe con valori ID duplicati, indipendentemente dal fatto che i pazienti abbiano lo stesso nome o meno.

+ In alternativa, è possibile decidere di consentire i duplicati nel campo ID e usare un'altra combinazione di file per trovare record univoci, ad esempio nome, cognome, età e sesso.  

Per impostare i criteri per determinare se una riga è duplicata o meno, è necessario specificare una singola colonna o un set di colonne da utilizzare come **chiavi**. Due righe sono considerate duplicate solo quando i valori in **tutte** le colonne chiave sono uguali. Se una riga contiene un valore mancante per le **chiavi**, non verranno considerate righe duplicate. Se, ad esempio, Gender e Age vengono impostati come chiavi nella tabella precedente, la riga 6 e 7 non sono righe duplicate, dato che mancano valori di età.

Quando si esegue il modulo, viene creato un set di dati candidato e viene restituito un set di righe prive di duplicati nel set di colonne specificato.

> [!IMPORTANT]
> Il set di dati di origine non viene modificato. Questo modulo crea un nuovo set di dati filtrato in modo da escludere i duplicati in base ai criteri specificati.

## <a name="how-to-use-remove-duplicate-rows"></a>Come usare Rimuovi righe duplicate

1. Aggiungere il modulo all'esperimento. È possibile trovare il modulo **Rimuovi righe duplicate** in **trasformazione dati**, **manipolazione**.  

2. Connettere il set di dati che si desidera controllare per le righe duplicate.

3. Nel riquadro **Proprietà** , in **espressione filtro selezione colonna chiave**, fare clic su **Avvia selettore colonna**per scegliere le colonne da utilizzare per l'identificazione dei duplicati.

    In questo contesto, **Key** non significa un identificatore univoco. Tutte le colonne selezionate utilizzando il selettore di colonna vengono designate come **colonne chiave**. Tutte le colonne non selezionate sono considerate colonne non chiave. La combinazione di colonne selezionate come chiavi determina l'univocità dei record. È possibile considerarlo come un'istruzione SQL che utilizza più join Equals.

    Esempi:

    + "Voglio assicurarmi che gli ID siano univoci": Scegliere solo la colonna ID.
    + "Voglio assicurarmi che la combinazione di nome, cognome e ID sia univoca": Selezionare tutte e tre le colonne.

4. Utilizzare la casella di controllo **Mantieni prima riga duplicata** per indicare la riga da restituire quando vengono trovati i duplicati:

    + Se questa opzione è selezionata, viene restituita la prima riga e altre eliminate. 
    + Se si deseleziona questa opzione, l'ultima riga duplicata viene mantenuta nei risultati e altri vengono eliminati. 

5. Eseguire l'esperimento.

6. Per esaminare i risultati, fare clic con il pulsante destro del mouse sul modulo, scegliere **risultati set di dati**e fare clic su **Visualizza**. 

> [!TIP]
> Se i risultati sono difficili da comprendere o se si desidera escludere alcune colonne dalla considerazione, è possibile rimuovere le colonne utilizzando il modulo [Select Columns in DataSet](./select-columns-in-dataset.md) .

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 