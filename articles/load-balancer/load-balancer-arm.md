---
title: Azure Resource Manager support for Load Balancer
description: In this article, use Azure PowerShell and templates with Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 57a60a65dee995695224aa1b451e848ea8768ab1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215399"
---
# <a name="azure-resource-manager-support-with-azure-load-balancer"></a>Azure Resource Manager support with Azure Load Balancer



Azure Resource Manager è il framework di gestione preferito dei servizi in Azure. È ora possibile gestire Azure Load Balancer con API e strumenti basati su Azure Resource Manager.

## <a name="concepts"></a>Concetti

Con Resource Manager, Azure Load Balancer contiene le seguenti risorse figlio:

* Front-end IP configuration – a load balancer can include one or more frontend IP addresses, otherwise known as a virtual IPs (VIPs). Questi indirizzi IP vengono usati come ingresso per il traffico.
* Back-end address pool – This pool is a collection of IP addresses associated with the virtual machine Network Interface Card (NIC) to which load is distributed.
* Load-balancing rules – a rule property maps a given frontend IP and port combination to a set of back-end IP addresses and port combination. A single load balancer can have multiple load-balancing rules. Each rule is a combination of a frontend IP and port and back-end IP and port associated with VMs.
* Probe: le probe consentono di tenere traccia dell'integrità delle istanze della macchina virtuale. Se un probe di integrità non riesce, l'istanza della macchina virtuale viene esclusa automaticamente dalla rotazione.
* Inbound NAT rules – NAT rules defining the inbound traffic flowing through the frontend IP and distributed to the back-end IP.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Modelli introduttivi

Azure Resource Manager ti permette di eseguire il provisioning delle tue applicazioni usando un modello dichiarativo. Con un unico modello, puoi distribuire più servizi insieme alle rispettive dipendenze. Puoi usare lo stesso modello per distribuire ripetutamente la tua applicazione durante ogni fase del suo ciclo di vita.

Templates may include definitions for:
* **Macchine virtuali**
* **Reti virtuali**
* **Set di disponibilità**
* **Network interfaces (NICs)**
* **Account di archiviazione**
* **Servizi di bilanciamento del carico**
* **Gruppi di sicurezza di rete**
* **Public IPs.** 

Con i modelli è possibile creare tutto il necessario per un'applicazione complessa. Il file modello può essere verificato nel sistema di gestione dei contenuti per il controllo della versione e la collaborazione.

[Altre informazioni sui modelli](../azure-resource-manager/resource-manager-template-walkthrough.md)

[Altre informazioni sulle risorse di rete](../networking/networking-overview.md)

For Quickstart templates using Azure Load Balancer, see the [GitHub repository](https://github.com/Azure/azure-quickstart-templates) that hosts a set of community-generated templates.

Esempi di modelli:

* [2 macchine virtuali in un bilanciamento del carico e regole di bilanciamento del carico](https://go.microsoft.com/fwlink/?LinkId=544799)
* [2 VMs in a VNET with an Internal Load Balancer and load balancer rules](https://go.microsoft.com/fwlink/?LinkId=544800)
* [2 VMs in a load balancer and configure NAT rules on the LB](https://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Configurazione del bilanciamento del carico di Azure con PowerShell o l'interfaccia della riga di comando

Get started with Azure Resource Manager cmdlets, command-line tools, and REST APIs

* [cmdlet di rete di Azure](https://docs.microsoft.com/powershell/module/az.network#networking) possono essere usati per creare un bilanciamento del carico.
* [Come creare un servizio di bilanciamento del carico tramite Gestione risorse di Azure](load-balancer-get-started-ilb-arm-ps.md)
* [Uso dell'interfaccia della riga di comando di Azure con Gestione risorse di Azure](../xplat-cli-azure-resource-manager.md)
* [API REST di bilanciamento del carico](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>Passaggi successivi

[Get started creating an Internet facing load balancer](load-balancer-get-started-internet-arm-ps.md) and configure the type of [distribution mode](load-balancer-distribution-mode.md) for specific network traffic behavior.

Informazioni su come gestire [le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md). These settings are important when your application needs to keep connections alive for servers behind a load balancer.
