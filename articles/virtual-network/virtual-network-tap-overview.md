---
title: Panoramica del TAP di rete virtuale di Azure | Microsoft Docs
description: Informazioni sul TAP di rete virtuale. Il TAP di rete virtuale offre una copia completa del traffico di rete della macchina virtuale che può essere trasmessa a un agente di raccolta di pacchetti.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: 7013c8ed338e727dd79a3845ff3b85749c0f5cee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87836089"
---
# <a name="virtual-network-tap"></a>TAP di rete virtuale
> [!IMPORTANT]
> L'anteprima di TAP per la rete virtuale è attualmente in attesa in tutte le aree di Azure. È possibile inviare un messaggio di posta elettronica all'indirizzo <azurevnettap@microsoft.com> con l'ID sottoscrizione e si riceverà una notifica per gli aggiornamenti futuri dell'anteprima. Nel frattempo, è possibile usare soluzioni basate su agenti o di appliance virtuale di rete che forniscono funzionalità di visibilità di rete e di tocco tramite le [soluzioni dei partner broker di pacchetti](#virtual-network-tap-partner-solutions) disponibili nelle [offerte di Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/networking?page=1&subcategories=appliances%3Ball&search=Network%20Traffic&filters=partners).

Un TAP (Terminal Access Point) di rete virtuale di Azure consente di trasmettere il traffico di rete della macchina virtuale come flusso continuo a un agente di raccolta di pacchetti di rete o a uno strumento di analisi. Lo strumento di raccolta dati o analisi viene fornito da un partner di [appliance virtuale di rete](https://azure.microsoft.com/solutions/network-appliances/) . Per un elenco delle soluzioni dei partner convalidate per l'uso con un TAP di rete virtuale, vedere le [soluzioni dei partner](#virtual-network-tap-partner-solutions).
L'immagine seguente illustra come funziona un TAP di rete virtuale. È possibile aggiungere una configurazione di TAP in un'[interfaccia di rete](virtual-network-network-interface.md) collegata a una macchina virtuale distribuita nella rete virtuale. La destinazione è un indirizzo IP di rete virtuale nella stessa rete virtuale dell'interfaccia di rete monitorata o una rete [virtuale con peering](virtual-network-peering-overview.md). La soluzione dell'agente di raccolta per il TAP di rete virtuale può essere distribuita dietro un bilanciamento del carico interno di Azure per garantire disponibilità elevata.
![Come funziona il TAP di rete virtuale](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Prerequisiti

Prima di creare un TAP di rete virtuale, è necessario aver ricevuto un messaggio di posta elettronica di conferma che è stato registrato nell'anteprima e avere una o più macchine virtuali create usando [Azure Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) modello di distribuzione e una soluzione partner per l'aggregazione del traffico Tap nella stessa area di Azure. Se non è disponibile una soluzione di un partner nella rete virtuale, vedere le [soluzioni dei partner](#virtual-network-tap-partner-solutions) per distribuirne una. È possibile usare la stessa risorsa TAP di rete virtuale per aggregare il traffico da più interfacce di rete nella stessa sottoscrizione o in sottoscrizioni diverse. Se le interfacce di rete monitorate si trovano in sottoscrizioni diverse, le sottoscrizioni devono essere associate allo stesso tenant di Azure Active Directory. Inoltre, le interfacce di rete monitorate e l'endpoint di destinazione per aggregare il traffico TAP possono trovarsi in reti virtuali con peering nella stessa area. Se si usa questo modello di distribuzione, assicurarsi che il [peering di reti virtuali](virtual-network-peering-overview.md) sia abilitato prima di configurare il TAP di rete virtuale.

## <a name="permissions"></a>Autorizzazioni

Gli account usati per applicare la configurazione TAP alle interfacce di rete devono essere assegnati al ruolo [Collaboratore Rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le azioni necessarie dalla tabella seguente:

| Azione | Nome |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Necessaria per creare, aggiornare, leggere ed eliminare una risorsa TAP di rete virtuale |
| Microsoft.Network/networkInterfaces/read | Necessaria per leggere la risorsa interfaccia di rete in cui verrà configurato il TAP |
| Microsoft.Network/tapConfigurations/* | Necessaria per creare, aggiornare, leggere ed eliminare la configurazione TAP per un'interfaccia di rete |


## <a name="virtual-network-tap-partner-solutions"></a>Soluzioni dei partner per TAP di rete virtuale

### <a name="network-packet-brokers"></a>Broker pacchetti di rete

- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)
- [Big Switch Big Monitoring Fabric](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)

### <a name="security-analytics-networkapplication-performance-management"></a>Analisi della sicurezza, gestione delle prestazioni di rete/applicazioni

- [Sicurezza attiva](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch cloud](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis Cybersecurity](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [Riverbed SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [RSA NetWitness® Platform](https://www.rsa.com/azure)
- [Vectra Cognito](https://vectra.ai/microsoftazure)



## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un TAP di rete virtuale](tutorial-tap-virtual-network-cli.md).
