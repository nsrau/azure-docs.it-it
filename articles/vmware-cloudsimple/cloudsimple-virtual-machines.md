---
title: Soluzione VMware di CloudSimple-Panoramica delle macchine virtuali di Azure
description: Informazioni sulle macchine virtuali CloudSimple e sui relativi vantaggi.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0f4967bbd12107bf6a04cb80537d4425c75c5f46
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812484"
---
# <a name="cloudsimple-virtual-machines-overview"></a>Panoramica delle macchine virtuali CloudSimple

CloudSimple consente di gestire le macchine virtuali VMware dal portale di Azure.  Un cluster o un pool di risorse dal cluster vSphere viene gestito tramite Azure eseguendone il mapping alla sottoscrizione.  La macchina virtuale CloudSimple offre la gestione self-service di macchine virtuali VMware dal portale di Azure.  

Per creare una macchina virtuale CloudSimple da Azure, è necessario che esista un modello di macchina virtuale nel cloud privato vCenter.  Il modello viene usato per personalizzare il sistema operativo e le applicazioni.  La macchina virtuale modello può essere finalizzata per soddisfare i criteri di sicurezza aziendali.  È possibile usare il modello per creare macchine virtuali e utilizzarle da portale di Azure usando un modello Self-Service.

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
* Informazioni su come eseguire [il mapping della sottoscrizione di Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)