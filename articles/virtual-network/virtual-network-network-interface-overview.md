---
title: Interfacce di rete | Documentazione Microsoft
description: Informazioni sulle interfacce di rete di Azure in Azure Resource Manager.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: f58b503f-18bf-4377-aa63-22fc8a96e4be
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 6e96471c4f61e1ebe15c23f87ac646001d8e30ee
ms.openlocfilehash: 586877781f4b74c49936a8129db49a67018bab65


---
# <a name="network-interfaces"></a>Interfacce di rete
Un'interfaccia di rete è l'interconnessione tra una macchina virtuale (VM) e la rete software sottostante. Questo articolo spiega cos'è un'interfaccia di rete e come viene usata nel modello di distribuzione Azure Resource Manager.

Microsoft consiglia di distribuire nuove risorse usando il modello di distribuzione di Resource Manager, ma è possibile anche distribuire VM con connettività di rete nel modello di distribuzione [classica](virtual-network-ip-addresses-overview-classic.md) . Se si ha familiarità con il modello classico, esistono importanti differenze nella rete di VM nel modello di distribuzione Resource Manager. Per ulteriori informazioni sulle differenze, leggere l'articolo [Virtual machine networking - Classic](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) (Rete di macchine virtuali - Classico).

In Azure un'interfaccia di rete:

1. È una risorsa che è possibile creare ed eliminare e che ha le sue impostazioni configurabili.
2. Deve essere connessa a una subnet in una rete virtuale di Azure quando viene creata. Se non si ha familiarità con le reti virtuali, leggere l'articolo [Panoramica della rete virtuale](virtual-networks-overview.md) . L'interfaccia di rete deve essere connessa a una rete virtuale esistente nello stesso [percorso](https://azure.microsoft.com/regions) e nella stessa [sottoscrizione](../azure-glossary-cloud-terminology.md#subscription) di Azure come interfaccia di rete. Dopo la creazione di un'interfaccia di rete, è possibile modificare la subnet a cui è connessa, ma non la rete virtuale.
3. Ha un nome assegnato che non può essere modificato dopo la creazione dell'interfaccia di rete. Il nome deve essere univoco all'interno di un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md#resource-groups)di Azure, ma non deve necessariamente essere univoco all'interno della sottoscrizione, del percorso di Azure in cui è creato o dalla rete virtuale a cui è connesso. Generalmente vengono create più interfacce di rete in una sottoscrizione di Azure. Si consiglia di ideare una convenzione di denominazione che semplifichi la gestione di più interfacce di rete rispetto all'uso di nomi predefiniti. Per trovare alcuni suggerimenti, vedere l'articolo [Recommended naming conventions for Azure resources](../guidance/guidance-naming-conventions.md) (Convenzioni di denominazione consigliate per le risorse di Azure).
4. Può essere collegata a una sola VM a patto che esista nello stesso percorso dell'interfaccia di rete.
5. Ha un indirizzo MAC, che viene mantenuto con l'interfaccia di rete finché resta collegato a una VM. L'indirizzo MAC viene mantenuto se la VM viene riavviata (dall'interno del sistema operativo) o arrestata (deallocata) e avviata tramite il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure. Se viene scollegata da una VM e collegata a un'altra VM, l'interfaccia di rete riceve un indirizzo MAC diverso. Se l'interfaccia di rete viene eliminata, l'indirizzo MAC viene assegnato ad altre interfacce.
6. Deve avere un indirizzo IP primario **IPv4** *privato* statico o dinamico assegnato.
7. Può avere una risorsa di indirizzo IP pubblico associata.
8. Supporta la rete accelerata con Single-Root I/O Virtualization (SR-IOV) per VM di dimensioni specifiche che eseguono versioni specifiche del sistema operativo Microsoft Windows Server. Per ulteriori informazioni su questa funzionalità di ANTEPRIMA, leggere l'articolo [Accelerated networking for a virtual machine](virtual-network-accelerated-networking-powershell.md) (Rete accelerata per una macchina virtuale).
9. Può ricevere traffico non destinato a indirizzi IP privati assegnati se è abilitato l'inoltro IP per l'interfaccia di rete. Se, ad esempio, una VM esegue un software firewall, instrada i pacchetti non destinati ai suoi indirizzi IP. La VM continuerà a eseguire software in grado di instradare o inoltrare il traffico, ma a tale scopo, è necessario che sia abilitato l'inoltro IP per un'interfaccia di rete.
10. Viene spesso creata nello stesso gruppo di risorse della VM a cui è collegata o nella stessa rete virtuale a cui è connessa, anche se non è necessario.

## <a name="vms-with-multiple-network-interfaces"></a>VM con più interfacce di rete
Si possono collegare a una VM più schede di interfaccia di rete, ma in questo caso tenere presente quanto segue:  

* Le dimensioni della VM devono supportare più schede di interfaccia di rete. Per altre informazioni sulle dimensioni delle VM che supportano più schede di interfaccia di rete, vedere gli articoli [Dimensioni delle VM Windows Server](../virtual-machines/virtual-machines-windows-sizes.md) e [Dimensioni delle VM Linux](../virtual-machines/virtual-machines-linux-sizes.md).
* La VM deve essere creata con almeno due schede di interfaccia di rete. Se la VM viene creata con una sola scheda di interfaccia di rete anche se le dimensioni della VM ne supportano più di una, non sarà possibile collegare altre schede di interfaccia di rete alla VM dopo la creazione. Purché la VM sia stata creata con almeno due schede di interfaccia di rete, è possibile collegare altre schede di interfaccia di rete alla VM dopo la creazione, purché le dimensioni della VM supportino più di due schede di interfaccia di rete.  
* È possibile scollegare le schede di interfaccia di rete secondarie (la scheda di interfaccia di rete primaria non può essere scollegata) da una VM se alla VM sono collegate almeno tre schede di interfaccia di rete. Se alla VM sono collegate due schede di interfaccia di rete o meno, non è possibile scollegare le schede di interfaccia di rete.  
* L'ordine delle NIC all'interno della macchina virtuale sarà casuale e potrebbe cambiare con gli aggiornamenti dell'infrastruttura di Azure. Tuttavia, gli indirizzi IP e gli indirizzi MAC Ethernet corrispondenti resteranno invariati. Si supponga, ad esempio, che il sistema operativo identifichi Azure NIC1 come Eth1. Eth1 ha l'indirizzo IP 10.1.0.100 e l'indirizzo MAC 00-0D-3A-B0-39-0D. Dopo l'aggiornamento e il riavvio di un'infrastruttura di Azure, il sistema operativo potrebbe identificare Azure NIC1 come Eth2, ma gli indirizzi IP e MAC rimarranno gli stessi di quando il sistema operativo ha identificato Azure NIC1 come Eth1. Quando un riavvio viene eseguito dal cliente, l'ordine delle schede di interfaccia di rete rimane invariato nel sistema operativo.  
* Se la VM è membro di un [set di disponibilità](../azure-glossary-cloud-terminology.md#availability-set), tutte le VM del set di disponibilità devono avere una sola scheda di interfaccia di rete o più schede di interfaccia di rete. Se le VM hanno più schede di interfaccia di rete, il numero delle schede di ogni VM non deve essere lo stesso, purché ogni VM abbia almeno due schede di interfaccia di rete.

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni su come creare una VM con una singola interfaccia di rete, leggere l'articolo [Creare una VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .
* Per informazioni su come creare una VM con più interfacce di rete, leggere l'articolo [Distribuire una VM con più interfacce di rete](virtual-network-deploy-multinic-arm-ps.md) .
* Per informazioni su come creare un'interfaccia di rete con più configurazioni IP, leggere l'articolo [Multiple IP addresses for Azure virtual machines](virtual-network-multiple-ip-addresses-powershell.md) (Più indirizzi IP per le macchine virtuali di Azure).




<!--HONumber=Nov16_HO3-->


