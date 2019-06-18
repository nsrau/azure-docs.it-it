---
title: File di inclusione
description: File di inclusione
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: b6587a3928c2ddf0d00c90e07643525314690e42
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180284"
---
### <a name="virtual-machines"></a>Macchine virtuali

|  |  |
|---------|---------|
| [Allow custom VM image from a Resource Group](../articles/governance/policy/samples/allow-custom-vm-image.md) (Consentire un'immagine di macchina virtuale personalizzata da un gruppo di risorse) |  Richiede che le immagini personalizzate provengano da un gruppo di risorse approvato. Si specifica il nome del gruppo di risorse approvato. |
| [SKU consentiti per account di archiviazione e macchine virtuali](../articles/governance/policy/samples/allowed-skus-storage.md) | Richiede che gli account di archiviazione e le macchine virtuali usino SKU approvati. Usa i criteri predefiniti per garantire SKU approvati. Si specifica una matrice di SKU di macchine virtuali approvati e una matrice di SKU di account di archiviazione approvati. |
| [Immagini delle macchine virtuali approvate](../articles/governance/policy/samples/allowed-custom-images.md) | Richiede che solo le immagini personalizzate approvate vengano distribuite nell'ambiente in uso. Si specifica una matrice di ID di immagini approvate. |
| [Controllare se l'estensione non esiste](../articles/governance/policy/samples/audit-extension-not-exist.md) | Controlla se un'estensione non viene distribuita con una macchina virtuale. Si specifica il server di pubblicazione e il tipo dell'estensione per controllare se è stata distribuita. |
| [Estensioni di macchine virtuali non consentite](../articles/governance/policy/samples/not-allowed-vm-extension.md) | Impedisce l'uso di estensioni specificate. Si specifica una matrice che contiene i tipi di estensioni non consentiti. |