---
title: 'Modificare i metadati: Informazioni di riferimento sui moduli'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo di modifica dei metadati nel servizio di Azure Machine Learning per modificare i metadati che ha associato le colonne in un set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 282652adb917450c262e08bf10c3c6e537b829e7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072712"
---
# <a name="edit-metadata-module"></a>Modifica il modulo dei metadati

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare il modulo di modifica dei dati per modificare i metadati che ha associato le colonne in un set di dati. Il valore e tipo di dati del set di dati verrà modificato dopo l'uso del modulo di modifica dei metadati.

Le modifiche ai metadati tipico potrebbe includere:
  
+ Considerare colonne booleane o numeriche come valori categorici.
  
+ Che indica la colonna che contiene il **classe** assegnare un'etichetta o contiene i valori che si desidera classificare o stimare.
  
+ Contrassegno delle colonne come funzioni.
  
+ Modifica dei valori di data/ora in valori numerici o viceversa.
  
+ La ridenominazione delle colonne.
  
 Usare i metadati modifica ogni volta che è necessario modificare la definizione di una colonna, in genere per soddisfare i requisiti per un modulo a valle. Ad esempio, alcuni moduli funzionano solo con tipi di dati specifici o richiedono flag sulle colonne, ad esempio `IsFeature` o `IsCategorical`.  
  
 Dopo aver eseguito l'operazione richiesta, è possibile reimpostare i metadati allo stato originale.
  
## <a name="configure-edit-metadata"></a>Configurare Edit Metadata
  
1. In Azure Machine Learning, aggiungere il modulo di modifica dei metadati all'esperimento e connettere il set di dati da aggiornare. È possibile trovare il set di dati sotto **Data Transformation** nel **Manipulate** categoria.
  
1. Selezionare **avviare il selettore di colonna** e scegliere la colonna o set di colonne da usare. È possibile scegliere le colonne singolarmente per nome o indice, oppure è possibile scegliere un gruppo di colonne dal tipo.  
  
1. Selezionare il **tipo di dati** opzione se è necessario assegnare un tipo di dati diversi per le colonne selezionate. Potrebbe essere necessario modificare il tipo di dati per determinate operazioni. Ad esempio, se il set di dati di origine contenga numeri gestiti come testo, è necessario modificare tali a un tipo di dati numerici prima di usare le operazioni matematiche.

    + I tipi di dati supportati sono **stringa**, **Integer**, **Double**, **booleani**, e **DateTime**.

    + Se si selezionano più colonne, è necessario applicare le modifiche dei metadati *tutti* colonne selezionate. Ad esempio, si supponga che scegliere le colonne numeriche di due o tre. È possibile modificarli tutti su una stringa di tipo di dati e rinominare le colonne in un'unica operazione. Tuttavia, è possibile modificare una colonna in un tipo di dati stringa e un'altra colonna da float a un integer.
  
    + Se non si specifica un nuovo tipo di dati, i metadati della colonna rimane invariato.

    + Il tipo di colonna e valori cambierà dopo aver eseguito l'operazione di modifica dei metadati. È possibile recuperare il tipo di dati originale in qualsiasi momento usando i metadati di modifica per reimpostare il tipo di dati di colonna.  

    > [!NOTE]
    > Se si modifica qualsiasi tipo di numero di **data/ora** digitare, lasciare il **formato DateTime** campo vuoto. Attualmente non è possibile specificare il formato di dati di destinazione.  

1. Selezionare il **Categorical** opzione per specificare che i valori nelle colonne selezionate devono essere trattati come categorie.

    Ad esempio, si potrebbe disporre di una colonna che contiene i numeri di 0, 1 e 2, ma sapere che i numeri significa in realtà "Smoker", "Non-smoker" e "Sconosciuto". In tal caso, contrassegnare la colonna come categorica garantisce che i valori vengono usati solo per raggruppare i dati e non nei calcoli numerici.
  
1. Usare la **campi** opzione se si desidera modificare il modo in cui Azure Machine Learning utilizza i dati in un modello.

    + **Funzionalità**: Usare questa opzione per contrassegnare una colonna come funzionalità in moduli che agiscono solo su colonne di funzionalità. Per impostazione predefinita, tutte le colonne vengono inizialmente considerate come funzioni.  
  
    + **Etichetta**: Utilizzare questa opzione per contrassegnare l'etichetta, che è noto anche come la variabile stimabile di attributo o di destinazione. Molti moduli richiedono che tale colonna di etichetta esattamente una è presente nel set di dati.

        In molti casi, Azure Machine Learning in grado di dedurre che una colonna contiene un'etichetta di classe. Impostando questi metadati, è possibile garantire che la colonna è identificata in modo corretto. Impostando questa opzione non modifica i valori dei dati. Modifica solo il modo in cui alcuni algoritmi di machine learning gestiscono i dati.
  
    > [!TIP]
    > Si dispone di dati che non rientrano nelle categorie seguenti? Ad esempio, il set di dati potrebbe contenere valori, ad esempio identificatori univoci che non sono utili come variabili. In alcuni casi tale ID può causare problemi se utilizzata in un modello.
    >
    > Fortunatamente, Azure Machine Learning consente di mantenere tutti i tuoi dati, in modo che non è necessario eliminare tali colonne dal set di dati. Quando è necessario eseguire operazioni su un set speciale di colonne, semplicemente rimuovere tutte le altre colonne temporaneamente con le [Select Columns in Dataset](select-columns-in-dataset.md) modulo. In un secondo momento è possibile unire le colonne in set di dati usando il [Add Columns](add-columns.md) modulo.  
  
1. Usare le opzioni seguenti per cancellare le selezioni precedenti e ripristinare i metadati per i valori predefiniti.  
  
    + **Funzionalità non crittografato**: Utilizzare questa opzione per rimuovere il flag funzionalità.  
  
         Tutte le colonne vengono inizialmente considerate come funzioni. Per i moduli che eseguono operazioni matematiche, si potrebbe essere necessario usare questa opzione per impedire che le colonne numeriche vengono considerate come variabili.
  
    + **Etichetta Cancella**: Usare questa opzione per rimuovere il **etichetta** metadati dalla colonna specificata.  
  
    + **Cancella punteggio**: Usare questa opzione per rimuovere il **punteggio** metadati dalla colonna specificata.  
  
         È attualmente non è possibile contrassegnare in modo esplicito una colonna come punteggio in Azure Machine Learning. Tuttavia, alcuni risultati delle operazioni in una colonna venga contrassegnata come un punteggio internamente. Inoltre, un modulo R personalizzato potrebbe restituire valori di punteggio.

1. Per la **nuovi nomi di colonna**, immettere il nuovo nome della colonna selezionata o delle colonne.  
  
    + I nomi di colonna possono usare solo caratteri supportati da UTF-8 di codifica. Non sono consentiti stringhe vuote, i valori null o nomi costituite interamente da spazi.  
  
    + Per rinominare più colonne, immettere i nomi sotto forma di elenco delimitato da virgole in ordine degli indici di colonna.  
  
    + Tutte le colonne selezionate devono essere rinominate. Non è possibile omettere o ignorare le colonne.  
  
1. Eseguire l'esperimento.  

## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio di Azure Machine Learning.
