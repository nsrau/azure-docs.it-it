---
title: Soluzione VMware da CloudSimple - Panoramica di macchine virtuali di Azure
description: Informazioni sulle macchine virtuali CloudSimple e i relativi vantaggi.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 59f5438bbedea2ff7793a5df95f1d3df58b9bba6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576986"
---
# <a name="cloudsimple-virtual-machines-overview"></a>Panoramica delle macchine virtuali CloudSimple

CloudSimple consente di gestire le macchine virtuali VMware nel portale di Azure.  Un cluster o un pool di risorse dal cluster vSphere viene gestito tramite Azure eseguendone il mapping alla sottoscrizione.  Macchina virtuale CloudSimple offre gestione self-service delle macchine virtuali VMware nel portale di Azure.  

Per creare una VM CloudSimple da Azure, un modello di macchina virtuale deve esistere nel vCenter del Cloud privato.  Il modello viene usato per personalizzare il sistema operativo e applicazioni.  Il modello di macchina virtuale può essere finalizzato per soddisfare i criteri di sicurezza dell'organizzazione.  È possibile utilizzare il modello per creare macchine virtuali e il loro uso dal portale di Azure con un modello self-service.

## <a name="benefits"></a>Vantaggi

Le macchine virtuali CloudSimple dal portale di Azure forniscono un meccanismo self-service agli utenti di creare e gestire macchine virtuali VMware.

* Creare una VM CloudSimple in vCenter del Cloud privato Microsoft
* Gestire le proprietà della macchina virtuale
  * Aggiungere o rimuovere dischi
  * Aggiungere o rimuovere interfacce di rete
* Operazioni di potenza della VM CloudSimple
  * Accensione e spegnimento
  * Ripristina macchina virtuale
* Elimina macchina virtuale

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [utilizzare macchine virtuali VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Informazioni su come [eseguire il mapping della sottoscrizione di Azure](https://docs.azure.cloudsimple.com/azuresubscriptionmapping/)