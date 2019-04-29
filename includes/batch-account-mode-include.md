---
title: File di inclusione
description: File di inclusione
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 05/04/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: b5bb5da2b68bae36353b81a04993814b395ce67b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60721704"
---
> [!NOTE]
> Quando si crea un account Batch, è possibile scegliere tra due modalità di *allocazione del pool*, ovvero **sottoscrizione utente** e **servizio Batch**. Nella maggior parte dei casi si userà la modalità predefinita servizio Batch, nella quale i pool vengono allocati in background nelle sottoscrizioni gestite da Azure. Nella modalità sottoscrizione utente alternativa, le macchine virtuali e le altre risorse di Batch vengono create direttamente nella sottoscrizione in fase di creazione di un pool. La modalità sottoscrizione utente è obbligatoria se si vogliono creare pool di Batch usando [istanze di macchina virtuale riservate di Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/). Per creare un account Batch in modalità Sottoscrizione utente, è anche necessario registrare la sottoscrizione in Azure Batch e associare l'account ad Azure Key Vault.