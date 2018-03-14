---
title: File di inclusione
description: File di inclusione
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 02/28/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: a3c5290eb0179fe5842c495c2e08f22580d02bda
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Distribuire un'immagine con le condizioni del Marketplace

Alcune immagini di macchine virtuali in Azure Marketplace hanno condizioni di licenza e di acquisto aggiuntive che è necessario accettare prima di procedere alla distribuzione a livello di codice.  

Per distribuire una macchina virtuale da tale immagine, è necessario accettarne le condizioni e abilitare la distribuzione a livello di codice. Questa operazione deve essere eseguita una sola volta nell'ambito della sottoscrizione. Quindi, ogni volta che si distribuisce una macchina virtuale a livello di codice dall'immagine, è anche necessario specificare i parametri del *piano di acquisto*.

Le sezioni seguenti mostrano come:

* Scoprire se un'immagine del Marketplace ha condizioni di licenza aggiuntive 
* Accettare le condizioni a livello di codice
* Specificare i parametri del piano di acquisto quando si distribuisce una macchina virtuale a livello di codice

