---
title: File di inclusione
description: File di inclusione
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: eedce1ef3a4e7d56cfaf3142a72e370c96f9f575
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664527"
---
### <a name="virtual-machines"></a>Macchine virtuali

|  |  |
|---------|---------|
| [Allow custom VM image from a Resource Group](../articles/azure-policy/scripts/allow-custom-vm-image.md) (Consentire un'immagine di macchina virtuale personalizzata da un gruppo di risorse) |  Richiede che le immagini personalizzate provengano da un gruppo di risorse approvato. Si specifica il nome del gruppo di risorse approvato. |
| [SKU consentiti per account di archiviazione e macchine virtuali](../articles/azure-policy/scripts/allowed-skus-storage.md) | Richiede che gli account di archiviazione e le macchine virtuali usino SKU approvati. Usa i criteri predefiniti per garantire SKU approvati. Si specifica una matrice di SKU di macchine virtuali approvati e una matrice di SKU di account di archiviazione approvati. |
| [Immagini delle macchine virtuali approvate](../articles/azure-policy/scripts/allowed-custom-images.md) | Richiede che solo le immagini personalizzate approvate vengano distribuite nell'ambiente in uso. Si specifica una matrice di ID di immagini approvate. |
| [Controllare se l'estensione non esiste](../articles/azure-policy/scripts/audit-ext-not-exist.md) | Controlla se un'estensione non viene distribuita con una macchina virtuale. Si specifica il server di pubblicazione e il tipo dell'estensione per controllare se è stata distribuita. |
| [Estensioni di macchine virtuali non consentite](../articles/azure-policy/scripts/not-allowed-vm-ext.md) | Impedisce l'uso di estensioni specificate. Si specifica una matrice che contiene i tipi di estensioni non consentiti. |