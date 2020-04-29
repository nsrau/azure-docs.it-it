---
title: Panoramica delle macchine virtuali
titleSuffix: Azure VMware Solution by CloudSimple
description: Informazioni sulle macchine virtuali CloudSimple e sui relativi vantaggi.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 436a5ab6fb286ce206f981487b6a29d774ecd1a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77024909"
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
  * Reimpostare una VM
* Eliminazione di una macchina virtuale

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come usare [macchine virtuali VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Informazioni su come eseguire [il mapping della sottoscrizione di Azure](azure-subscription-mapping.md)
