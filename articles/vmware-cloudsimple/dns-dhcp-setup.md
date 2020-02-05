---
title: 'Azure VMware Solutions (AVS): configurare il DNS del carico di lavoro e DHCP per AVS private cloud'
description: Viene descritto come configurare DNS e DHCP per le applicazioni e i carichi di lavoro in esecuzione nell'ambiente di cloud privato AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2a5cae868f2d8f3689f05dd9d466715ab2008a3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024688"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-avs-private-cloud"></a>Configurare applicazioni e carichi di lavoro DNS e DHCP nel cloud privato AVS

Le applicazioni e i carichi di lavoro in esecuzione in un ambiente cloud privato AVS richiedono la risoluzione dei nomi e i servizi DHCP per la ricerca e l'assegnazione di indirizzi IP. Per fornire questi servizi, è necessaria un'infrastruttura DHCP e DNS corretta. È possibile configurare una macchina virtuale per fornire questi servizi nell'ambiente di cloud privato AVS. 

## <a name="prerequisites"></a>Prerequisiti

* Un gruppo di porte distribuite con VLAN configurata
* Instrada il programma di installazione a server DNS locali o basati su Internet
* Modello di macchina virtuale o ISO per creare una macchina virtuale

## <a name="linux-based-dns-server-setup"></a>Configurazione del server DNS basato su Linux

Linux offre diversi pacchetti per la configurazione dei server DNS. Di seguito è [riportato un esempio di installazione di DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) con istruzioni per la configurazione di un server DNS BIND open source.

## <a name="windows-based-setup"></a>Installazione basata su Windows

Negli argomenti Microsoft viene descritto come configurare un server Windows come server DNS e come server DHCP.

* [Windows Server come server DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Windows Server come server DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
