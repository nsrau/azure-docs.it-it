---
title: 'Unisci dati: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Join Join Data in Azure Machine Learning per unire i set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 38606f424e38fc68519181f485b5b698d0705d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477579"
---
# <a name="join-data"></a>Unire dati

Questo articolo descrive come usare il modulo **Join data** nella finestra di progettazione di Azure Machine Learning (anteprima) per unire due set di dati usando un'operazione di join di tipo database.  

## <a name="how-to-configure-join-data"></a>Come configurare Join Data

Per eseguire un join su due set di dati, devono essere correlati da una colonna chiave. Sono supportate anche chiavi composte che utilizzano più colonne.Composite keys using multiple columns are also supported. 

1. Aggiungere i set di dati che si desidera combinare e quindi trascinare il modulo **Join data** nella pipeline. 

    Il modulo è riportato nella categoria **Trasformazione dati,** in **Manipolazione**.

1. Connettere i set di dati al modulo **Unisci dati.** 
 
1. Selezionare **Avvia selettore di colonna** per scegliere le colonne chiave. Ricordarsi di scegliere le colonne sia per gli ingressi sinistro che destro.

    Per un singolo tasto:

    Selezionare una colonna a chiave singola per entrambi gli input.
    
    Per una chiave composita:

    Selezionare tutte le colonne chiave dall'input sinistro e dall'input a destra nello stesso ordine. Il modulo **Unisci dati** unirà le tabelle quando tutte le colonne chiave corrispondono. Selezionare l'opzione **Consenti duplicati e mantieni l'ordine delle colonne nella selezione** se l'ordine delle colonne non corrisponde alla tabella originale. 

    ![column-selector](media/module/join-data-column-selector.png)


1. Selezionare l'opzione **Maiuscole/minuscole** se si desidera mantenere la distinzione tra maiuscole e minuscole in un join di colonna di testo. 
   
1. Utilizzare l'elenco a discesa Tipo di **join** per specificare la modalità di combinazione dei set di dati.  
  
    * **Inner join**: Un *inner join* è l'operazione di join più comune. Restituisce le righe combinate solo quando i valori delle colonne chiave corrispondono.  
  
    * **Left Outer Join**: Un *left outer join* restituisce righe unite per tutte le righe della tabella di sinistra. Quando una riga nella tabella di sinistra non contiene righe corrispondenti nella tabella di destra, la riga restituita contiene valori mancanti per tutte le colonne provenienti dalla tabella di destra. È inoltre possibile specificare un valore di sostituzione per i valori mancanti.  
  
    * **Full Outer Join**: un *full outer join* restituisce tutte le righe della tabella di sinistra (**table1**) e della tabella di destra (**table2**).  
  
         Per ognuna delle righe di entrambe le tabelle che non hanno righe corrispondenti nell'altra, il risultato include una riga contenente i valori mancanti.  
  
    * **Semi-join a**sinistra : un *semi-join a sinistra* restituisce solo i valori della tabella di sinistra quando i valori delle colonne chiave corrispondono.  

1. Per l'opzione **Mantieni le colonne chiave a destra nella tabella unita:**

    * Selezionare questa opzione per visualizzare i tasti di entrambe le tabelle di input.
    * Deselezionare per restituire solo le colonne chiave dall'input sinistro.

1. Inviare la pipeline.

1. Per visualizzare i risultati, fare clic con il pulsante destro del mouse su **Unisci dati** e selezionare **Visualizza**.

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 