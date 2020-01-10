---
title: Più rami nel flusso di dati di mapping
description: Replica di flussi di dati nel flusso di dati di mapping con più rami
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 71fb9f1ba9952be0e6b3910dd1079aa6d3c0482d
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834479"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Creazione di un nuovo ramo nel flusso di dati di mapping

Aggiungere un nuovo ramo per eseguire più set di operazioni e trasformazioni nello stesso flusso di dati. L'aggiunta di un nuovo ramo è utile quando si vuole usare la stessa origine di per più sink o per unire i dati in modo automatico.

Un nuovo ramo può essere aggiunto dall'elenco di trasformazione in modo analogo ad altre trasformazioni. Il **nuovo ramo** sarà disponibile solo come azione quando esiste una trasformazione esistente dopo la trasformazione che si sta tentando di creare un ramo.

![Aggiunta di un nuovo ramo](media/data-flow/new-branch2.png "Aggiunta di un nuovo ramo")

Nell'esempio seguente il flusso di dati legge i dati relativi alle corse dei taxi. L'output aggregato per giorno e fornitore è obbligatorio. Anziché creare due flussi di dati distinti che leggono dalla stessa origine, è possibile aggiungere un nuovo ramo. In questo modo è possibile eseguire entrambe le aggregazioni come parte dello stesso flusso di dati. 

![Aggiunta di un nuovo ramo](media/data-flow/new-branch.png "Aggiunta di un nuovo ramo")
