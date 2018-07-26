---
title: Ridimensionare un volume o pool di capacità per Azure NetApp Files | Microsoft Docs
description: Viene descritto come modificare le dimensioni di un volume o un pool di capacità.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to-article
ms.date: 04/03/2018
ms.author: b-juche
ms.openlocfilehash: 72da85fb7296d02bc6d5f7fcd3279953c312c920
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39012009"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Ridimensionare un pool di capacità o un volume
È possibile modificare le dimensioni di un pool o un volume di capacità in base alle esigenze. 

## <a name="resize-the-capacity-pool"></a>Ridimensionare il pool di capacità 

È possibile modificare le dimensioni del pool di capacità in incrementi o decrementi di 4 TiB. Il ridimensionamento del pool di capacità modifica la capacità di Azure NetApp Files acquistata.

1. Nel pannello Gestisci account NetApp fare clic sul pool di capacità che si intende ridimensionare. 
2. Fare clic con il pulsante destro del mouse sul nome del pool di capacità oppure fare clic sull'icona "..." alla fine della riga del pool di capacità per visualizzare il menu di scelta rapida. 
3. Usare le opzioni del menu di scelta rapida per ridimensionare o eliminare il pool di capacità.

## <a name="resize-a-volume"></a>Ridimensionare un volume

È possibile modificare le dimensioni di un volume in base alle esigenze. L'utilizzo della capacità di un volume concorre al calcolo della capacità di cui è stato effettuato il provisioning del pool.

1. Nel pannello Gestisci account NetApp fare clic su **Volumi**. 
2. Fare clic con il pulsante destro del mouse sul nome del volume da ridimensionare oppure fare clic sull'icona "..." alla fine della riga del volume per visualizzare il menu di scelta rapida.
3. Usare le opzioni del menu di scelta rapida per ridimensionare o eliminare il volume.

