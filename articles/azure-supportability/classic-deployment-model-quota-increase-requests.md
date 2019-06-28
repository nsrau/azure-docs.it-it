---
title: Modello di distribuzione classica di Azure | Microsoft Docs
description: Modello di distribuzione classica di Azure
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/20/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 5ae2a56c084116ae8d57a16696223e7990258558
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313257"
---
# <a name="classic-deployment-model"></a>Modello di distribuzione classica

Modello di distribuzione classica è la generazione precedente ' s land modalità di distribuzione di Azure impone un limite di quota di vCPU globale per le macchine virtuali e set di scalabilità di macchine virtuali. Modello di distribuzione classica non è più consigliato e a questo punto viene sostituito dal modello di Resource Manager. Per altre informazioni sulla distribuzione di questi due modelli e i vantaggi di Resource Manager, fare riferimento alla pagina di modello di distribuzione Resource Manager. Quando viene creata una nuova sottoscrizione, è assegnata una quota predefinita di Vcpu. In qualsiasi momento una nuova macchina virtuale deve essere distribuita usando il modello di distribuzione classica, la somma dell'utilizzo di CPU virtuali nuove ed esistenti in tutte le aree non deve superare la quota di vCPU approvata per il modello di distribuzione classica. Altre informazioni sulle quote nella sottoscrizione di Azure e nella pagina limiti di servizio.

È possibile richiedere un aumento del limite di Vcpu per il modello di distribuzione classica tramite Pannello supporto o l'utilizzi + Quota pannello nel portale.

