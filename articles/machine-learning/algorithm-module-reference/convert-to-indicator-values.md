---
title: Eseguire la conversione in valori di indicatori
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Converti in valori indicatore in Azure Machine Learning per convertire le colonne che contengono valori di categoria in una serie di colonne di indicatori binari.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: f1b194f2c65f95ad4daff0353d05ca589db9ce51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477664"
---
# <a name="convert-to-indicator-values"></a>Eseguire la conversione in valori di indicatori
Questo articolo descrive un modulo della finestra di progettazione di Azure Machine Learning.This article describes a module of Azure Machine Learning designer.

Usare il modulo **Converti in valori indicatore** in Progettazione Azure Machine Learning per convertire le colonne che contengono valori di categoria in una serie di colonne indicatore binario.  

Questo modulo restituisce anche una definizione della trasformazione utilizzata per convertire in valori dell'indicatore. È possibile riutilizzare questa trasformazione in altri set di dati con lo stesso schema, usando il modulo [Applica trasformazione.](apply-transformation.md)

## <a name="how-to-configure-convert-to-indicator-values"></a>Come configurare Converti in valori indicatore

1.  Trovare **Converti in valori indicatore** e trascinarlo nello sformo della pipeline. È possibile trovare questo modulo nella categoria **Trasformazione dati.**
    > [!NOTE]
    > È possibile utilizzare il modulo [Modifica metadati](edit-metadata.md) prima del modulo **Converti** in indici per contrassegnare le colonne di destinazione come categoriche.

1. Connettere il modulo **Converti in** valori indicatore al set di dati contenente le colonne da convertire. 

1. Selezionare **Modifica colonna** per scegliere una o più colonne di categoria.

1. Selezionare l'opzione **Sovrascrivi colonne categoriche** se si desidera generare **solo** le nuove colonne booleane. Per impostazione predefinita, questa opzione è impostata su OFF.
    

    > [!TIP]
    >  Se si sceglie l'opzione per sovrascrivere, la colonna di origine non viene effettivamente eliminata o modificata. Le nuove colonne vengono invece generate e presentate nel set di dati di output e la colonna di origine rimane disponibile nell'area di lavoro. Se è necessario visualizzare i dati originali, è possibile utilizzare il modulo [Aggiungi colonne](add-columns.md) in qualsiasi momento per aggiungere nuovamente la colonna di origine.

1. Inviare la pipeline.

## <a name="results"></a>Risultati

Si supponga di disporre di una colonna con punteggi che indicano se un server ha una probabilità di errore elevata, media o bassa.  

| ID del server | Punteggio di errore |
| --------- | ------------- |
| 10301     | Basso           |
| 10302     | Media        |
| 10303     | Alto          |

Quando si applica **Converti in valori indicatore**, la finestra di progettazione converte una singola colonna di etichette in più colonne contenenti valori booleani:  

| ID del server | Punteggio di errore - Basso | Punteggio di errore - Medio | Punteggio di errore - Alto |
| --------- | ------------------- | ---------------------- | -------------------- |
| 10301     | 1                   | 0                      | 0                    |
| 10302     | 0                   | 1                      | 0                    |
| 10303     | 0                   | 0                      | 1                    |

Ecco come funziona la conversione:  

-   Nella colonna **Punteggio errori** che descrive il rischio sono presenti solo tre valori possibili (Alto, Medio e Basso) e nessun valore mancante. Quindi, vengono create esattamente tre nuove colonne.  

-   Le nuove colonne dell'indicatore vengono denominate in base alle intestazioni di colonna e ai valori della colonna di origine, utilizzando questo modello: * \<colonna di origine \<>- valore di dati>*.  

-   Dovrebbe essere presente un 1 in esattamente una colonna indicatore e 0 in tutte le altre colonne indicatore poiché ogni server può avere una sola valutazione del rischio.  

È ora possibile usare le tre colonne indicatore come funzionalità in un modello di apprendimento automatico.

Il modulo restituisce due output:

- **Set di dati dei risultati**: un set di dati con colonne di valori dell'indicatore convertiti. Anche le colonne non selezionate per la pulizia vengono "passate".
- **Trasformazione dei valori**degli indicatori : trasformazione dei dati utilizzata per la conversione in valori degli indicatori, che possono essere salvati nell'area di lavoro e applicati ai nuovi dati in un secondo momento.

## <a name="apply-a-saved-indicator-values-operation-to-new-data"></a>Applicare un'operazione sui valori degli indicatori salvati ai nuovi dati

Se è necessario ripetere spesso le operazioni sui valori degli indicatori, è possibile salvare i passaggi di manipolazione dei dati come *trasformazione* per riutilizzarli con lo stesso set di dati. Ciò è utile se è necessario reimportare frequentemente e quindi pulire i dati che hanno lo stesso schema.

1. Aggiungere il modulo [Applica trasformazione](apply-transformation.md) alla pipeline.

1. Aggiungere il set di dati da pulire e connetterlo alla porta di input a destra.

1. Espandere il gruppo **Trasformazione dati** nel riquadro sinistro della finestra di progettazione. Individuare la trasformazione salvata e trascinarla nella pipeline.

1. Connettere la trasformazione salvata alla porta di input sinistra di [Applica trasformazione](apply-transformation.md).

   Quando si applica una trasformazione salvata, non è possibile selezionare le colonne da trasformare. Ciò è dovuto al fatto che la trasformazione è stata definita e si applica automaticamente ai tipi di dati specificati nell'operazione originale.

1. Inviare la pipeline.
 
## <a name="technical-notes"></a>Note tecniche  

Questa sezione contiene dettagli sull'implementazione, suggerimenti e risposte alle domande frequenti.

### <a name="usage-tips"></a>Suggerimenti per l'uso

-   solo le colonne contrassegnate come categoriche possono essere convertite in colonne indicatore Se viene visualizzato il seguente errore, è probabile che una delle colonne selezionate non sia categorica:  

     Errore 0056: colonna \<con nome di colonna nome> non è in una categoria consentita.  

     Per impostazione predefinita, la maggior parte delle colonne stringa viene gestita come funzionalità stringa, pertanto è necessario contrassegnarle in modo esplicito come categoriche utilizzando [Modifica metadati](edit-metadata.md).  

-   Non esiste alcun limite al numero di colonne che è possibile convertire in colonne indicatore. Tuttavia, poiché ogni colonna di valori può restituire più colonne indicatore, è possibile convertire ed esaminare solo alcune colonne alla volta.  

-   Se la colonna contiene valori mancanti, viene creata una colonna indicatore separata per la categoria mancante, con questo nome: * \<colonna di origine>- Mancante*  

-   Se la colonna convertita in valori di indicatore contiene numeri, questi devono essere contrassegnati come classificati come qualsiasi altra colonna di entità geografiche. Dopo aver eseguito questa operazione, i numeri vengono considerati come valori discreti. Ad esempio, se si dispone di una colonna numerica con valori MPG che vanno da 25 a 30, verrà creata una nuova colonna indicatore per ogni valore discreto:For example, if you have a numeric column with MPG values ranging from 25 to 30, a new indicator column would be created for each discrete value:  

    | Casa automobilistica       | Autostrada mpg -25 | Autostrada mpg -26 | Autostrada mpg -27 | Autostrada mpg -28 | Autostrada mpg -29 | Autostrada mpg -30 |
    | ---------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
    | Contoso Cars | 0               | 0               | 0               | 0               | 0               | 1               |

- Per evitare di aggiungere troppe dimensioni al set di dati. È consigliabile controllare prima il numero di valori nella colonna e collocare o quantificare i dati in modo appropriato.  


## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 
