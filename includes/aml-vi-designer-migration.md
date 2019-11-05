---
title: File di inclusione
description: File di inclusione
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: xiaoharper
ms.author: zhanxia
ms.date: 10/18/2019
ms.openlocfilehash: 08833f869a7838fe893b8e941072078f89033c2b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517852"
---
1. Accedere a [Azure Machine Learning Studio](https://ml.azure.com).

1. Aggiornare l'area di lavoro a Enterprise Edition.

    Dopo l'aggiornamento, tutti gli esperimenti dell'interfaccia visiva vengono convertiti in bozze di pipeline nella finestra di progettazione.
    
    > [!NOTE]
    > Non è necessario eseguire l'aggiornamento a Enterprise Edition per convertire i servizi Web dell'interfaccia visiva in endpoint in tempo reale.
    
1. Passare alla sezione relativa alla finestra di progettazione dell'area di lavoro per visualizzare l'elenco delle bozze della pipeline. 
    
    Per trovare i servizi Web convertiti, è possibile passare a **endpoint** > **endpoint in tempo reale**.

1. Selezionare una bozza della pipeline per aprirla.

    Se si è verificato un errore durante il processo di conversione, verrà visualizzato un messaggio di errore con le istruzioni per risolvere il problema. 

### <a name="known-issues"></a>Problemi noti

 Di seguito sono riportati i problemi di migrazione noti che devono essere risolti manualmente:

- **Importa dati** o **Esporta moduli dati**
        
    Se si dispone di un modulo **Import** data o **Export Data** nell'esperimento, è necessario aggiornare l'origine dati per l'uso di un archivio dati. Per informazioni su come creare un archivio dati, vedere [come accedere ai dati nei servizi di archiviazione di Azure](../articles/machine-learning/service/how-to-access-data.md). Le informazioni sull'account di archiviazione cloud sono state aggiunte nei commenti del modulo **Importa dati** o **Esporta dati** per praticità. 
      