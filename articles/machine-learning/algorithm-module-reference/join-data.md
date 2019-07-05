---
title: 'Unire i dati: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Join dei dati nel servizio Azure Machine Learning di join per unire i set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterclu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7e814f5ea4bd47ceb0697e860c946039ce39ae1f
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518014"
---
# <a name="join-data"></a>Unire dati

Questo articolo descrive come usare il **unire dati** modulo nell'interfaccia visual di servizio di Azure Machine Learning per unire due set di dati tramite un'operazione di join basato su database.  

## <a name="how-to-configure-join-data"></a>Come configurare i dati di Join

Per eseguire un join su due set di dati, questi devono essere correlati da una colonna chiave. Sono supportate anche chiavi composte con più colonne. 

1. Aggiungere il set di dati da combinare e quindi trascinare il **unire dati** modulo nell'esperimento. 

    È possibile trovare il modulo nel **Data Transformation** categoria, in **manipolazione**.

1. Connettere il set di dati per il **Join dati** modulo. 
 
1. Selezionare **Avvia selettore di colonna** per scegliere una o più colonne chiave. Ricordarsi di scegliere le colonne per entrambi gli input sinistro e destro.

    Per una singola chiave:

    Selezionare una singola colonna chiave per entrambi gli input.
    
    Per una chiave composta:

    Selezionare tutte le colonne chiave di input sinistro e destro input nello stesso ordine. Il **unire dati** modulo verrà aggiunti tabelle quando tutte le colonne chiave corrispondono. Selezionare l'opzione **consentire duplicati e mantenere l'ordine delle colonne nella selezione** se l'ordine delle colonne non corrisponde alla tabella originale. 

    ![column-selector](media/module/join-data-column-selector.png)


1. Selezionare il **maiuscole/minuscole** opzione se si desidera mantenere distinzione maiuscole/minuscole in un join di colonne di testo. 
   
1. Usare la **tipo di Join** elenco a discesa per specificare come devono essere combinati i set di dati.  
  
    * **Inner Join**: Un' *inner join* è l'operazione di join più comune. Restituisce le righe combinate solo quando i valori delle colonne chiave corrispondono.  
  
    * **Left Outer Join**: Oggetto *outer join sinistro* restituisce le righe unite per tutte le righe della tabella di sinistra. Quando una riga nella tabella di sinistra non ha righe corrispondenti nella tabella di destra, la riga restituita contiene i valori mancanti per tutte le colonne provenienti dalla tabella di destra. È anche possibile specificare un valore sostitutivo per i valori mancanti.  
  
    * **Full Outer Join**: Oggetto *full outer join* restituisce tutte le righe della tabella di sinistra (**table1**) e dalla tabella di destra (**table2**).  
  
         Per ognuna delle righe in delle tabelle che non esistono righe corrispondenti in altra, il risultato include una riga che contiene i valori mancanti.  
  
    * **Left semi-Join**: Oggetto *left semi join* restituisce solo i valori della tabella di sinistra quando i valori delle colonne chiave corrispondono.  

1. Per l'opzione **mantenere le colonne chiave a destra nella tabella unita in join**:

    * Selezionare questa opzione per visualizzare le chiavi di entrambe le tabelle di input.
    * Deselezionare per restituire solo le colonne chiave dell'input di sinistra.

1. Eseguire l'esperimento, o selezionare il modulo Join dei dati e selezionata **Esegui selezionati** per eseguire il join.

1. Per visualizzare i risultati, fare doppio clic sui **dati Join** > **set di dati risultati** > **Visualize**.

## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 