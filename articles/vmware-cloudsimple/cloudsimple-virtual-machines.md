---
title: Panoramica delle macchine virtualiVirtual machines overview
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024909"
---
# <a name="cloudsimple-virtual-machines-overview"></a>Panoramica delle macchine virtuali CloudSimple

CloudSimple consente di gestire le macchine virtuali VMware (VM) dal portale di Azure.CloudSimple allows you to manage VMware virtual machines (VMs) from the Azure portal.  Un cluster o un pool di risorse dal cluster vSphere viene gestito tramite Azure eseguendo ne esegue il mapping alla sottoscrizione.

Per creare una macchina virtuale CloudSimple da Azure, è necessario che nel centro vCenter del cloud privato sia presente un modello di macchina virtuale.  Il modello viene utilizzato per personalizzare il sistema operativo e le applicazioni.  La macchina virtuale del modello può essere sottoposta a protezione del database per soddisfare i criteri di sicurezza aziendali.  È possibile usare il modello per creare macchine virtuali e quindi usarle dal portale di Azure usando un modello self-service.

## <a name="benefits"></a>Vantaggi

Le macchine virtuali CloudSimple dal portale di Azure forniscono agli utenti un meccanismo self-service per creare e gestire le macchine virtuali VMware.CloudSimple virtual machines from Azure portal provide a self-service mechanism for users to create and manage VMware virtual machines.

* Creare una macchina virtuale CloudSimple nel vCenter del cloud privatoCreate a CloudSimple VM on your Private Cloud vCenter
* Gestire le proprietà della macchina virtualeManage VM properties
  * Aggiungere/rimuovere dischi
  * Aggiungere/rimuovere schede di interfaccia di rete
* Operazioni di alimentazione della macchina virtuale CloudSimple
  * Accensione e spegnimento
  * Reimpostare una VM
* Eliminazione di una macchina virtuale

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [utilizzare le macchine virtuali VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Informazioni su come eseguire il mapping della sottoscrizione di [AzureLearn](azure-subscription-mapping.md) how to Map your Azure subscription
