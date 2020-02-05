---
title: Azure VMware Solutions (AVS)-Panoramica delle macchine virtuali
description: Informazioni sulle macchine virtuali AVS e sui relativi vantaggi.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e6cc6d51e37cea98cc553a236295311d21f595a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024909"
---
# <a name="avs-virtual-machines-overview"></a>Panoramica delle macchine virtuali AVS

AVS consente di gestire le macchine virtuali (VM) VMware dal portale di Azure. Un cluster o un pool di risorse dal cluster vSphere viene gestito tramite Azure eseguendone il mapping alla sottoscrizione.

Per creare una VM AVS da Azure, è necessario che esista un modello di macchina virtuale in AVS private cloud vCenter. Il modello viene usato per personalizzare il sistema operativo e le applicazioni. La macchina virtuale modello può essere finalizzata per soddisfare i criteri di sicurezza aziendali. È possibile usare il modello per creare macchine virtuali e quindi utilizzarle dalla portale di Azure usando un modello Self-Service.

## <a name="benefits"></a>Vantaggi

Le macchine virtuali AVS da portale di Azure offrono un meccanismo self-service per consentire agli utenti di creare e gestire macchine virtuali VMware.

* Creare una VM AVS in AVS private cloud vCenter
* Gestisci proprietà VM
  * Aggiungi/Rimuovi dischi
  * Aggiungi/Rimuovi NIC
* Operazioni di risparmio energia della VM AVS
  * Accensione e spegnimento
  * Reimposta macchina virtuale
* Elimina macchina virtuale

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come usare [macchine virtuali VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Informazioni su come eseguire [il mapping della sottoscrizione di Azure](azure-subscription-mapping.md)
