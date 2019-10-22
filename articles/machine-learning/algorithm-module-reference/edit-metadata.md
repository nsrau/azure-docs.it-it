---
title: 'Modifica metadati: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Modifica metadati nel servizio Azure Machine Learning per modificare i metadati associati alle colonne in un set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: b6e28577fefe4892d719e211e16edafed1504e87
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693820"
---
# <a name="edit-metadata-module"></a>Modulo Modifica metadati

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare il modulo Edit data per modificare i metadati associati alle colonne in un set di dati. Il valore e il tipo di dati del set di dati cambieranno dopo l'uso del modulo di modifica dei metadati.

Le modifiche tipiche dei metadati possono includere:
  
+ Trattamento di colonne booleane o numeriche come valori categorici.
  
+ Indica la colonna che contiene l'etichetta della **classe** o che contiene i valori che si desidera classificare o stimare.
  
+ Contrassegno delle colonne come caratteristiche.
  
+ Modifica dei valori di data/ora in valori numerici o viceversa.
  
+ Ridenominazione di colonne.
  
 Usare Modifica metadati ogni volta che è necessario modificare la definizione di una colonna, in genere per soddisfare i requisiti per un modulo downstream. Alcuni moduli, ad esempio, funzionano solo con tipi di dati specifici o richiedono flag sulle colonne, ad esempio `IsFeature` o `IsCategorical`.  
  
 Dopo aver eseguito l'operazione richiesta, è possibile ripristinare lo stato originale dei metadati.
  
## <a name="configure-edit-metadata"></a>Configurare la modifica dei metadati
  
1. In Azure Machine Learning aggiungere il modulo Modifica metadati alla pipeline e connettere il set di dati che si vuole aggiornare. È possibile trovare il set di **dati in trasformazione dati** nella categoria **modifica** .
  
1. Selezionare **avviare il selettore di colonna** e scegliere la colonna o il set di colonne da utilizzare. È possibile scegliere le colonne singolarmente in base al nome o all'indice oppure è possibile scegliere un gruppo di colonne per tipo.  
  
1. Selezionare l'opzione **tipo di dati** se è necessario assegnare un tipo di dati diverso alle colonne selezionate. Potrebbe essere necessario modificare il tipo di dati per determinate operazioni. Se, ad esempio, il set di dati di origine contiene numeri gestiti come testo, è necessario modificarli in un tipo di dati numerico prima di utilizzare operazioni matematiche.

    + I tipi di dati supportati sono **String**, **Integer**, **Double**, **Boolean**e **DateTime**.

    + Se si selezionano più colonne, è necessario applicare le modifiche dei metadati a *tutte* le colonne selezionate. Si immagini, ad esempio, di scegliere due o tre colonne numeriche. È possibile modificarli tutti in un tipo di dati stringa e rinominarli in un'unica operazione. Tuttavia, non è possibile modificare una colonna in un tipo di dati stringa e un'altra colonna da float a un Integer.
  
    + Se non si specifica un nuovo tipo di dati, i metadati della colonna non vengono modificati.

    + Il tipo e i valori della colonna cambieranno dopo l'esecuzione dell'operazione di modifica dei metadati. È possibile ripristinare il tipo di dati originale in qualsiasi momento utilizzando Modifica metadati per reimpostare il tipo di dati della colonna.  

    > [!NOTE]
    > Se si modifica un tipo di numero nel tipo **DateTime** , lasciare vuoto il campo **formato DateTime** . Attualmente non è possibile specificare il formato dei dati di destinazione.  

1. Selezionare l'opzione **categorica** per specificare che i valori nelle colonne selezionate devono essere considerati come categorie.

    Ad esempio, si potrebbe avere una colonna contenente i numeri 0, 1 e 2, ma si sa che i numeri significano effettivamente "Smoker", "non-smoker" e "Unknown". In tal caso, contrassegnando la colonna come categorico si garantisce che i valori vengano utilizzati solo per raggruppare i dati e non nei calcoli numerici.
  
1. Utilizzare l'opzione **Fields** se si desidera modificare il modo in cui Azure Machine Learning utilizza i dati in un modello.

    + **Funzionalità**: usare questa opzione per contrassegnare una colonna come funzionalità nei moduli che funzionano solo su colonne di funzionalità. Per impostazione predefinita, tutte le colonne vengono inizialmente trattate come funzionalità di.  
  
    + **Etichetta**: usare questa opzione per contrassegnare l'etichetta, nota anche come attributo stimabile o variabile di destinazione. Per molti moduli è necessario che nel set di dati sia presente esattamente una sola colonna Label.

        In molti casi, Azure Machine Learning possibile dedurre che una colonna contiene un'etichetta di classe. Impostando questi metadati, è possibile verificare che la colonna sia identificata correttamente. L'impostazione di questa opzione non comporta la modifica dei valori dei dati. Viene modificato solo il modo in cui alcuni algoritmi di Machine Learning gestiscono i dati.
  
    > [!TIP]
    > Sono presenti dati che non rientrano in queste categorie? Ad esempio, il set di dati potrebbe contenere valori quali identificatori univoci che non sono utili come variabili. A volte tali ID possono causare problemi quando vengono utilizzati in un modello.
    >
    > Fortunatamente, Azure Machine Learning mantiene tutti i dati, in modo da non dover eliminare tali colonne dal set di dati. Quando è necessario eseguire operazioni su un set speciale di colonne, è sufficiente rimuovere temporaneamente tutte le altre colonne usando il modulo [Select Columns in DataSet](select-columns-in-dataset.md) . In seguito sarà possibile unire di nuovo le colonne nel set di dati usando il modulo [Add columns](add-columns.md) .  
  
1. Usare le opzioni seguenti per cancellare le selezioni precedenti e ripristinare i metadati ai valori predefiniti.  
  
    + **Cancella funzionalità**: usare questa opzione per rimuovere il flag funzionalità.  
  
         Tutte le colonne vengono inizialmente trattate come funzionalità. Per i moduli che eseguono operazioni matematiche, potrebbe essere necessario usare questa opzione per impedire che le colonne numeriche vengano considerate come variabili.
  
    + **Cancella etichetta**: usare questa opzione per rimuovere i metadati delle **etichette** dalla colonna specificata.  
  
    + **Clear Score**: usare questa opzione per rimuovere i metadati del **Punteggio** dalla colonna specificata.  
  
         Attualmente non è possibile contrassegnare in modo esplicito una colonna come punteggio in Azure Machine Learning. Tuttavia, alcune operazioni generano una colonna contrassegnata internamente come punteggio. Inoltre, un modulo R personalizzato potrebbe restituire valori di punteggio.

1. Per i **nuovi nomi di colonna**, immettere il nuovo nome della colonna o delle colonne selezionate.  
  
    + I nomi di colonna possono usare solo i caratteri supportati dalla codifica UTF-8. Non sono consentite stringhe vuote, valori null o nomi costituiti interamente da spazi.  
  
    + Per rinominare più colonne, immettere i nomi come elenco delimitato da virgole nell'ordine degli indici di colonna.  
  
    + Tutte le colonne selezionate devono essere rinominate. Non è possibile omettere o ignorare le colonne.  
  
1. Eseguire la pipeline.  

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per il servizio Azure Machine Learning.
