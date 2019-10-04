---
title: File di inclusione
description: File di inclusione
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8e0e549f88caf4a541642bab77faf54b5e536b29
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174962"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Distribuire un'immagine con le condizioni del Marketplace

Alcune immagini di macchina virtuale in Azure Marketplace hanno condizioni di licenza e di acquisto aggiuntive che è necessario accettare prima di poter distribuirle a livello di codice.  

Per distribuire una macchina virtuale da questa immagine, è necessario accettarne le condizioni e abilitare la distribuzione a livello di codice. Sarà necessario eseguire questa operazione una sola volta per ogni sottoscrizione. Successivamente, ogni volta che si distribuisce una macchina virtuale a livello di codice dall'immagine, sarà anche necessario specificare i parametri del *piano di acquisto*.

Le sezioni seguenti mostrano come:

* Identificare se un'immagine del Marketplace ha condizioni di licenza aggiuntive 
* Accettare le condizioni a livello di codice
* Specificare i parametri del piano di acquisto quando si distribuisce una macchina virtuale a livello di codice

