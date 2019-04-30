---
title: File di inclusione
description: File di inclusione
services: virtual-machines-windows, virtual-machines-linux
author: rockboyfor
ms.service: multiple
ms.topic: include
origin.date: 10/09/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 4d2235eaea457c89d01a632afa5dd5a862bec344
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880547"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Distribuire un'immagine con le condizioni del Marketplace

Alcune immagini di macchina virtuale in Azure Marketplace hanno condizioni di licenza e di acquisto aggiuntive che è necessario accettare prima di poter distribuirle a livello di codice.  

Per distribuire una macchina virtuale da questa immagine, è necessario accettarne le condizioni e abilitare la distribuzione a livello di codice. Sarà necessario eseguire questa operazione una sola volta per ogni sottoscrizione. Successivamente, ogni volta che si distribuisce una macchina virtuale a livello di codice dall'immagine, sarà anche necessario specificare i parametri del *piano di acquisto*.

Le sezioni seguenti mostrano come:

* Identificare se un'immagine del Marketplace ha condizioni di licenza aggiuntive 
* Accettare le condizioni a livello di codice
* Specificare i parametri del piano di acquisto quando si distribuisce una macchina virtuale a livello di codice

<!-- Update_Description: update meta properties, wording update -->