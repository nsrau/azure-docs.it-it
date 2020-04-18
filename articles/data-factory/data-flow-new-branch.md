---
title: Più filiali nel mapping del flusso di datiMultiple branches in mapping data flow
description: Replica dei flussi di dati nel mapping del flusso di dati con più rami
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: daebd6a4a00d82706ceda68f94736f21b8fe5a84
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606403"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Creazione di un nuovo ramo nel flusso di dati di mappingCreating a new branch in mapping data flow

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aggiungere un nuovo ramo per eseguire più set di operazioni e trasformazioni sullo stesso flusso di dati. L'aggiunta di un nuovo ramo è utile quando si desidera utilizzare la stessa origine per più sink o per l'autounione dei dati.

È possibile aggiungere un nuovo ramo dall'elenco delle trasformazioni in modo simile ad altre trasformazioni. **Nuovo ramo** sarà disponibile come azione solo quando è presente una trasformazione esistente dopo la trasformazione che si sta tentando di creare un ramo.

![Aggiunta di un nuovo ramo](media/data-flow/new-branch2.png "Aggiunta di un nuovo ramo")

Nell'esempio seguente, il flusso di dati legge i dati di viaggio in taxi. È necessario utilizzare l'output aggregato sia per giorno che per fornitore. Anziché creare due flussi di dati separati che leggono dalla stessa origine, è possibile aggiungere un nuovo ramo. In questo modo entrambe le aggregazioni possono essere eseguite come parte dello stesso flusso di dati. 

![Aggiunta di un nuovo ramo](media/data-flow/new-branch.png "Aggiunta di un nuovo ramo")
