---
title: 'Join Data: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo join join data nel servizio Azure Machine Learning per unire i set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterlu
ms.date: 06/01/2019
ms.openlocfilehash: ec9de6975931219ae45543833421234c4d71e6af
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693086"
---
# <a name="join-data"></a>Unire dati

Questo articolo descrive come usare il modulo **join data** nell'interfaccia visiva del servizio Azure Machine Learning per unire due set di dati usando un'operazione di join di tipo database.  

## <a name="how-to-configure-join-data"></a>Come configurare i dati di join

Per eseguire un join su due set di impostazioni, questi devono essere correlati da una colonna chiave. Sono supportate anche le chiavi composite che usano più colonne. 

1. Aggiungere i set di dati che si desidera combinare, quindi trascinare il modulo **join data** nella pipeline. 

    È possibile trovare il modulo nella categoria **trasformazione dati** , in **manipolazione**.

1. Connettere i set di dati al modulo **join data** . 
 
1. Selezionare **Avvia selettore di colonna** per scegliere le colonne chiave. Ricordarsi di scegliere le colonne per gli input di sinistra e di destra.

    Per una singola chiave:

    Selezionare una singola colonna chiave per entrambi gli input.
    
    Per una chiave composta:

    Selezionare tutte le colonne chiave dall'input di sinistra e quello destro nello stesso ordine. Il modulo **join data** si unirà alle tabelle quando tutte le colonne chiave corrispondono. Selezionare l'opzione **Consenti duplicati e Mantieni l'ordine delle colonne nella selezione** se l'ordine delle colonne non corrisponde a quello della tabella originale. 

    ![column-selector](media/module/join-data-column-selector.png)


1. Selezionare l'opzione **maiuscole/minuscole** se si desidera mantenere la distinzione tra maiuscole e minuscole in un join di colonna di testo 
   
1. Utilizzare l'elenco a discesa **tipo di join** per specificare la modalità di combinazione dei set di impostazioni.  
  
    * **Inner join**: un *inner join* è l'operazione di join più comune. Restituisce le righe combinate solo quando i valori delle colonne chiave corrispondono.  
  
    * **Left outer join**: un *Left outer join* restituisce righe unite in join per tutte le righe della tabella di sinistra. Quando una riga nella tabella di sinistra non ha righe corrispondenti nella tabella di destra, la riga restituita contiene i valori mancanti per tutte le colonne che provengono dalla tabella di destra. È anche possibile specificare un valore sostitutivo per i valori mancanti.  
  
    * **Full outer join**: un *full outer join* restituisce tutte le righe della tabella a sinistra (**Tabella1**) e dalla tabella di destra (**Table2**).  
  
         Per ognuna delle righe di una tabella in cui non sono presenti righe corrispondenti, il risultato include una riga che contiene i valori mancanti.  
  
    * **Left semi join**: un *semi join a sinistra* restituisce solo i valori della tabella di sinistra quando i valori delle colonne chiave corrispondono.  

1. Per l'opzione **Mantieni colonne chiave corrette nella tabella unita in join**:

    * Selezionare questa opzione per visualizzare le chiavi da entrambe le tabelle di input.
    * Deseleziona per restituire solo le colonne chiave dall'input di sinistra.

1. Eseguire la pipeline oppure selezionare il modulo join data e selezionare l'opzione **Esegui** selezionata per eseguire il join.

1. Per visualizzare i risultati, fare clic con il pulsante destro del mouse sul set di **dati join Data**  > **results**  > **Visualizza**.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 