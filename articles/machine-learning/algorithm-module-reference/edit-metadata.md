---
title: 'Modificare i metadati: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo di modifica dei metadati nel servizio Azure Machine Learning per cambiare i metadati associati alle colonne in un set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: cfee607aca155b6cf68e5bddc40eb9c752df5e34
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028816"
---
# <a name="edit-metadata-module"></a>Modifica il modulo dei metadati

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per cambiare i metadati associati alle colonne in un set di dati. Verrà modificato il valore e tipo di dati del set di dati dopo aver usato il **Edit Metadata** modulo. 
 
Le modifiche ai metadati tipico potrebbe includere:
  
+ Considerare colonne booleane o numeriche come valori categorici  
  
+ Che indica la colonna che contiene il *classe* etichetta, o i valori che si desidera classificare o stimare  
  
+ Contrassegno delle colonne come funzioni
  
+ Modifica data/ora valori a un valore numerico, o viceversa  
  
+ La ridenominazione delle colonne
  
 Usare [Edit Metadata ogniqualvolta sia necessario modificare la definizione di una colonna, in genere per soddisfare i requisiti per un modulo a valle. Ad esempio, alcuni moduli possono lavorare solo con tipi di dati specifici o richiedono flag sulle colonne, ad esempio `IsFeature` o `IsCategorical`.  
  
 Dopo aver eseguito l'operazione necessaria, è possibile reimpostare i metadati allo stato originale. 
  
## <a name="configure-edit-metadata"></a>Configurare Edit Metadata
  
1.  In Azure Machine Learning, aggiungere [Edit Metadata](./edit-metadata.md) modulo nell'esperimento e connettere il set di dati da aggiornare. È disponibile in **Data Transformation**, nella **Manipulate** categoria.
  
2.  Fare clic su **avviare il selettore di colonna** e scegliere la colonna o set di colonne da usare. È possibile scegliere le colonne singolarmente, per nome o indice, oppure è possibile scegliere un gruppo di colonne, dal tipo.  
  
3.  Selezionare il **tipo di dati** opzione se è necessario assegnare un tipo di dati diversi per le colonne selezionate. Modifica i dati di tipo potrebbe essere necessaria per alcune operazioni:, ad esempio, se il set di dati di origine contenga numeri gestiti come testo, è necessario modificarle in un tipo di dati numerici prima di usare le operazioni matematiche. 

    + I tipi di dati supportati sono `String`, `Integer`, `Double`, `Boolean`, `DateTime`. 

    + Se sono selezionate più colonne, è necessario applicare le modifiche dei metadati **tutti** colonne selezionate. Ad esempio, si supponga che scegliere le colonne numeriche di 2 e 3. È possibile modificarle tutto in un tipo di dati stringa e rinominare le colonne in un'unica operazione. Tuttavia, è possibile modificare una colonna in un tipo di dati stringa e un'altra colonna da float a un integer.
  
    + Se non si specifica un nuovo tipo di dati, i metadati della colonna rimane invariato. 
    
    + Il tipo di colonna e i valori verranno modificati dopo eseguire la [Edit Metadata](./edit-metadata.md) operazione. È possibile recuperare il tipo di dati originale in qualsiasi momento usando [Edit Metadata](./edit-metadata.md) per reimpostare il tipo di dati di colonna.  

    > [!NOTE]
    > Se si modifica qualsiasi tipo di numero di **data/ora** digitare, lasciare il **formato DateTime** campo vuoto. Non è attualmente possibile specificare il formato di dati di destinazione.  

      
4.  Selezionare il **Categorical** opzione per specificare che i valori nelle colonne selezionate devono essere trattati come categorie. 

    Ad esempio, si potrebbe disporre di una colonna che contiene i numeri di 0,1 e 2, ma sapere che i numeri significa in realtà "Smoker", "Non-smoker" e "Sconosciuto". In tal caso, contrassegnando la colonna come categorica è possibile assicurarsi che i valori non vengono usati in calcoli numerici, solo per raggruppare i dati. 
  
5.  Usare la **campi** opzione se si desidera modificare il modo in cui Azure Machine Learning utilizza i dati in un modello.

    + **Funzionalità**: Usare questa opzione per contrassegnare una colonna come una funzionalità, per l'uso con i moduli che agiscono solo su colonne di funzionalità. Per impostazione predefinita, tutte le colonne vengono inizialmente considerate come funzioni.  
  
    + **Etichetta**: Utilizzare questa opzione per contrassegnare l'etichetta (noto anche come l'attributo stimabile, o variabile di destinazione). Molti moduli richiedono che almeno un (e solo uno) essere presente nel set di dati colonna di etichetta. 
    
        In molti casi, Azure Machine Learning in grado di dedurre che una colonna contiene un'etichetta di classe, ma impostando i metadati è possibile assicurarsi che la colonna è identificata in modo corretto. Impostando questa opzione non modifica i valori dei dati, solo il modo in cui alcuni algoritmi di machine learning gestiscono i dati.
  

  
    > [!TIP]
    >  Dispone di dati che non rientrano nelle categorie seguenti?  Ad esempio, il set di dati potrebbe contenere valori, ad esempio identificatori univoci che non sono utili come variabili. In alcuni casi gli ID possono causare problemi se utilizzata in un modello. 
    >   
    >  Per fortuna "dietro le quinte" Azure Machine Learning consente di mantenere tutti i dati, quindi non è necessario eliminare tali colonne dal set di dati. Quando è necessario eseguire operazioni su un set speciale di colonne, semplicemente rimuovere tutte le altre colonne temporaneamente con le [Select Columns in Dataset](./select-columns-in-dataset.md) modulo. In un secondo momento è possibile unire le colonne in set di dati usando il [Add Columns](./add-columns.md) modulo.  
  
6. Usare le opzioni seguenti per cancellare le selezioni precedenti e ripristinare i metadati per i valori predefiniti.  
  
    + **Funzionalità non crittografato**: Utilizzare questa opzione per rimuovere il flag funzionalità.  
  
         Poiché tutte le colonne vengono inizialmente considerate come funzionalità, per i moduli che eseguono operazioni matematiche, si potrebbe essere necessario usare questa opzione per impedire che le colonne numeriche vengono considerate come variabili.
  
    + **Etichetta Cancella**: Usare questa opzione per rimuovere il **etichetta** metadati dalla colonna specificata.  
  
    + **Cancella punteggio**: Usare questa opzione per rimuovere il **punteggio** metadati dalla colonna specificata.  
  
         La possibilità di contrassegnare in modo esplicito una colonna come punteggio non è attualmente disponibile in Azure Machine Learning. Tuttavia, alcuni risultati delle operazioni in una colonna venga contrassegnata come un punteggio internamente. Inoltre, un modulo R personalizzato potrebbe restituire valori di punteggio.
  
  
7.  Per la **nuovi nomi di colonna**, digitare il nuovo nome della colonna selezionata o delle colonne.  
  
    + I nomi di colonna possono usare solo caratteri supportati da UTF-8 di codifica. Le stringhe vuote, i valori null o costituita interamente da spazi di nomi non sono consentiti.  
  
    + Per rinominare più colonne, digitare i nomi sotto forma di elenco delimitato da virgole in ordine di indici di colonne.  
  
    + Tutte le colonne selezionate devono essere rinominate. Non è possibile omettere o ignorare le colonne.  
  
  
8.  Eseguire l'esperimento.  

## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 