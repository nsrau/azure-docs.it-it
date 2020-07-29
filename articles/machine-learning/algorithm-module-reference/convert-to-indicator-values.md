---
title: Eseguire la conversione in valori di indicatori
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Convert to Indicator values in Azure Machine Learning per convertire le colonne contenenti valori categorici in una serie di colonne indicatore binario.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: f1b194f2c65f95ad4daff0353d05ca589db9ce51
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79477664"
---
# <a name="convert-to-indicator-values"></a>Eseguire la conversione in valori di indicatori
Questo articolo descrive un modulo di Azure Machine Learning Designer.

Usare il modulo **Convert to Indicator values** in Azure Machine Learning Designer per convertire le colonne contenenti valori categorici in una serie di colonne indicatore binario.  

Questo modulo restituisce inoltre una definizione della trasformazione utilizzata per convertire i valori degli indicatori. È possibile riusare questa trasformazione in altri set di impostazioni con lo stesso schema usando il modulo [Apply Transformation](apply-transformation.md) .

## <a name="how-to-configure-convert-to-indicator-values"></a>Come configurare Convert to Indicator values

1.  Trovare i **valori Converti in indicatore** e trascinarli nella bozza della pipeline. È possibile trovare questo modulo nella categoria **trasformazione dati** .
    > [!NOTE]
    > È possibile usare il modulo [Modifica metadati](edit-metadata.md) prima del modulo **Convert to Indiciator values** per contrassegnare le colonne di destinazione come categorico.

1. Connettere il modulo **Convert to Indicator values** al set di dati contenente le colonne che si desidera convertire. 

1. Selezionare **modifica colonna** per scegliere una o più colonne categoriche.

1. Selezionare l'opzione **Sovrascrivi colonne categoriche** se si desidera restituire **solo** le nuove colonne booleane. Per impostazione predefinita, questa opzione è impostata su OFF.
    

    > [!TIP]
    >  Se si sceglie l'opzione per la sovrascrittura, la colonna di origine non viene effettivamente eliminata o modificata. Al contrario, le nuove colonne vengono generate e presentate nel set di dati di output e la colonna di origine rimane disponibile nell'area di lavoro. Se è necessario visualizzare i dati originali, è possibile usare il modulo [Aggiungi colonne](add-columns.md) in qualsiasi momento per aggiungere nuovamente la colonna di origine.

1. Inviare la pipeline.

## <a name="results"></a>Risultati

Si supponga di disporre di una colonna con punteggi che indichino se un server ha una probabilità alta, media o bassa di errore.  

| ID del server | Punteggio errore |
| --------- | ------------- |
| 10301     | Basso           |
| 10302     | Media        |
| 10303     | Alta          |

Quando si applica **Convert a valori indicatore**, la finestra di progettazione converte una singola colonna di etichette in più colonne contenenti valori booleani:  

| ID del server | Punteggio errore-basso | Punteggio di errore-medio | Punteggio errore-alto |
| --------- | ------------------- | ---------------------- | -------------------- |
| 10301     | 1                   | 0                      | 0                    |
| 10302     | 0                   | 1                      | 0                    |
| 10303     | 0                   | 0                      | 1                    |

Ecco come funziona la conversione:  

-   Nella colonna del **Punteggio di errore** che descrive il rischio sono disponibili solo tre valori possibili (alto, medio e basso) e nessun valore mancante. Quindi, vengono create esattamente tre nuove colonne.  

-   Le nuove colonne indicatore sono denominate in base alle intestazioni di colonna e ai valori della colonna di origine, usando questo modello: *\<source column>- \<data value>* .  

-   Deve esistere un valore 1 in una sola colonna indicatore e 0 in tutte le altre colonne indicatore poiché ogni server può avere una sola classificazione di rischio.  

È ora possibile usare le tre colonne indicatore come funzionalità in un modello di machine learning.

Il modulo restituisce due output:

- **Set di dati results**: set di dati con colonne con valori di indicatore convertiti. Anche le colonne non selezionate per la pulizia sono "passate".
- **Transformation values Transformation**: trasformazione dei dati usata per la conversione in valori indicatore, che possono essere salvati nell'area di lavoro e applicati ai nuovi dati in un secondo momento.

## <a name="apply-a-saved-indicator-values-operation-to-new-data"></a>Applicare un'operazione di valore indicatore salvato ai nuovi dati

Se è necessario ripetere spesso le operazioni relative ai valori degli indicatori, è possibile salvare i passaggi di manipolazione dei dati come *trasformazione* per riutilizzarlo con lo stesso set di dati. Questa operazione è utile se è necessario reimportare e quindi pulire i dati con lo stesso schema.

1. Aggiungere il modulo [Apply Transformation](apply-transformation.md) alla pipeline.

1. Aggiungere il set di dati che si desidera pulire e connettere il set di dati alla porta di input di destra.

1. Espandere il gruppo **trasformazione dati** nel riquadro a sinistra di progettazione. Individuare la trasformazione salvata e trascinarla nella pipeline.

1. Connettere la trasformazione salvata alla porta di input sinistra di [Apply Transformation](apply-transformation.md).

   Quando si applica una trasformazione salvata, non è possibile selezionare le colonne da trasformare. Ciò è dovuto al fatto che la trasformazione è stata definita e viene applicata automaticamente ai tipi di dati specificati nell'operazione originale.

1. Inviare la pipeline.
 
## <a name="technical-notes"></a>Note tecniche  

Questa sezione contiene informazioni dettagliate sull'implementazione, suggerimenti e risposte alle domande più frequenti.

### <a name="usage-tips"></a>Suggerimenti per l'uso

-   solo le colonne contrassegnate come categoriche possono essere convertite in colonne indicatore Se viene visualizzato l'errore seguente, è probabile che una delle colonne selezionate non sia categorica:  

     Errore 0056: la colonna con nome \<column name> non è in una categoria consentita.  

     Per impostazione predefinita, la maggior parte delle colonne stringa viene gestita come funzionalità di stringa, pertanto è necessario contrassegnarle in modo esplicito come categorico usando [Modifica metadati](edit-metadata.md).  

-   Non esiste alcun limite al numero di colonne che è possibile convertire in colonne indicatore. Tuttavia, poiché ogni colonna di valori può produrre più colonne indicatore, potrebbe essere necessario convertire ed esaminare solo alcune colonne alla volta.  

-   Se la colonna contiene valori mancanti, viene creata una colonna indicatore separata per la categoria mancante, con il nome seguente: * \<source column> -Missing*  

-   Se la colonna convertita in valori indicatore contiene numeri, questi devono essere contrassegnati come categorici come qualsiasi altra colonna di funzionalità. Al termine di questa operazione, i numeri vengono considerati come valori discreti. Se, ad esempio, è presente una colonna numerica con valori MPG compresi tra 25 e 30, viene creata una nuova colonna indicatore per ogni valore discreto:  

    | Casa automobilistica       | Mpg autostrada-25 | Mpg autostrada-26 | Mpg autostrada-27 | Mpg autostrada-28 | Mpg autostrada-29 | Mpg autostrada-30 |
    | ---------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
    | Automobili contoso | 0               | 0               | 0               | 0               | 0               | 1               |

- Per evitare l'aggiunta di troppe dimensioni al set di dati. È consigliabile innanzitutto verificare il numero di valori nella colonna e bin o quantizzare i dati in modo appropriato.  


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 
