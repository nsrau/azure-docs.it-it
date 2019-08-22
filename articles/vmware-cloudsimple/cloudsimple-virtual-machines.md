---
title: Soluzione VMware di Azure di CloudSimple-Panoramica delle macchine virtuali
description: Informazioni sulle macchine virtuali CloudSimple e sui relativi vantaggi.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 213ab51dae20d281a1a0e0f8ea18f4bde888e64d
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877909"
---
# <a name="cloudsimple-virtual-machines-overview"></a>Panoramica delle macchine virtuali CloudSimple

CloudSimple consente di gestire le macchine virtuali (VM) VMware dal portale di Azure.  Un cluster o un pool di risorse dal cluster vSphere viene gestito tramite Azure eseguendone il mapping alla sottoscrizione.

Per creare una macchina virtuale CloudSimple da Azure, è necessario che esista un modello di macchina virtuale nel cloud privato vCenter.  Il modello viene usato per personalizzare il sistema operativo e le applicazioni.  La macchina virtuale modello può essere finalizzata per soddisfare i criteri di sicurezza aziendali.  È possibile usare il modello per creare macchine virtuali e quindi utilizzarle dalla portale di Azure usando un modello Self-Service.

## <a name="benefits"></a>Vantaggi

Le macchine virtuali CloudSimple da portale di Azure offrono un meccanismo self-service per consentire agli utenti di creare e gestire macchine virtuali VMware.

* Creare una macchina virtuale CloudSimple nel cloud privato vCenter
* Gestisci proprietà VM
  * Aggiungi/Rimuovi dischi
  * Aggiungi/Rimuovi NIC
* Operazioni di risparmio energia della VM CloudSimple
  * Accensione e spegnimento
  * Ripristina macchina virtuale
* Elimina macchina virtuale

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come usare [macchine virtuali VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Informazioni su come eseguire [il mapping della sottoscrizione di Azure](azure-subscription-mapping.md)
