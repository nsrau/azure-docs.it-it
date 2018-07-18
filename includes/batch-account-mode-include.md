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
ms.openlocfilehash: 62bb91a2e51c39caf31719f72d68a6edab9205bc
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38943375"
---
> [!NOTE]
> Quando si crea un account Batch, è possibile scegliere tra due modalità di *allocazione del pool*, ovvero **sottoscrizione utente** e **servizio Batch**. Nella maggior parte dei casi si userà la modalità predefinita servizio Batch, nella quale i pool vengono allocati in background nelle sottoscrizioni gestite da Azure. Nella modalità sottoscrizione utente alternativa, le macchine virtuali e le altre risorse di Batch vengono create direttamente nella sottoscrizione in fase di creazione di un pool. La modalità sottoscrizione utente è obbligatoria se si vogliono creare pool di Batch usando [istanze di macchina virtuale riservate di Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/). Per creare un account Batch in modalità Sottoscrizione utente, è anche necessario registrare la sottoscrizione in Azure Batch e associare l'account ad Azure Key Vault.