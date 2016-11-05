---
title: Interfacce di rete | Microsoft Docs
description: Informazioni sulle interfacce di rete di Azure in Azure Resource Manager.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: jdial

---
# <a name="network-interfaces"></a>Interfacce di rete
Un'interfaccia di rete è l'interconnessione tra una macchina virtuale (VM) e la rete software sottostante. Questo articolo spiega cos'è un'interfaccia di rete e come viene usata nel modello di distribuzione Azure Resource Manager.

Microsoft consiglia di distribuire nuove risorse usando il modello di distribuzione di Resource Manager, ma è possibile anche distribuire VM con connettività di rete nel modello di distribuzione [classica](virtual-network-ip-addresses-overview-classic.md) . Se si ha familiarità con il modello classico, esistono importanti differenze nella rete di VM nel modello di distribuzione Resource Manager. Per ulteriori informazioni sulle differenze, leggere l'articolo [Virtual machine networking - Classic](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) (Rete di macchine virtuali - Classico).

In Azure un'interfaccia di rete:

1. È una risorsa che è possibile creare ed eliminare e che ha le sue impostazioni configurabili.
2. Deve essere connessa a una subnet in una rete virtuale di Azure quando viene creata. Se non si ha familiarità con le reti virtuali, leggere l'articolo [Panoramica della rete virtuale](virtual-networks-overview.md) . L'interfaccia di rete deve essere connessa a una rete virtuale esistente nello stesso [percorso](https://azure.microsoft.com/regions) e nella stessa [sottoscrizione](../azure-glossary-cloud-terminology.md#subscription) di Azure come interfaccia di rete. Dopo la creazione di un'interfaccia di rete, è possibile modificare la subnet a cui è connessa, ma non la rete virtuale.
3. Ha un nome assegnato che non può essere modificato dopo la creazione dell'interfaccia di rete. Il nome deve essere univoco all'interno di un [gruppo di risorse](../resource-group-overview.md#resource-groups)di Azure, ma non deve necessariamente essere univoco all'interno della sottoscrizione, del percorso di Azure in cui è creato o dalla rete virtuale a cui è connesso. Generalmente vengono create più interfacce di rete in una sottoscrizione di Azure. Si consiglia di ideare una convenzione di denominazione che semplifichi la gestione di più interfacce di rete rispetto all'uso di nomi predefiniti. Per trovare alcuni suggerimenti, vedere l'articolo [Recommended naming conventions for Azure resources](../guidance/guidance-naming-conventions.md) (Convenzioni di denominazione consigliate per le risorse di Azure).
4. Può essere collegata a una sola VM a patto che esista nello stesso percorso dell'interfaccia di rete.
5. Ha un indirizzo MAC, che viene mantenuto con l'interfaccia di rete finché resta collegato a una VM. L'indirizzo MAC viene mantenuto se la VM viene riavviata (dall'interno del sistema operativo) o arrestata (deallocata) e avviata tramite il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure. Se viene scollegata da una VM e collegata a un'altra VM, l'interfaccia di rete riceve un indirizzo MAC diverso. Se l'interfaccia di rete viene eliminata, l'indirizzo MAC viene assegnato ad altre interfacce.
6. Deve avere un indirizzo IP primario **IPv4** *privato* statico o dinamico assegnato.
7. Può avere una risorsa di indirizzo IP pubblico associata.
8. Supporta la rete accelerata con Single-Root I/O Virtualization (SR-IOV) per VM di dimensioni specifiche che eseguono versioni specifiche del sistema operativo Microsoft Windows Server. Per ulteriori informazioni su questa funzionalità di ANTEPRIMA, leggere l'articolo [Accelerated networking for a virtual machine](virtual-network-accelerated-networking-powershell.md) (Rete accelerata per una macchina virtuale).
9. Può ricevere traffico non destinato a indirizzi IP privati assegnati se è abilitato l'inoltro IP per l'interfaccia di rete. Se, ad esempio, una VM esegue un software firewall, instrada i pacchetti non destinati ai suoi indirizzi IP. La VM continuerà a eseguire software in grado di instradare o inoltrare il traffico, ma a tale scopo, è necessario che sia abilitato l'inoltro IP per un'interfaccia di rete.
10. Viene spesso creata nello stesso gruppo di risorse della VM a cui è collegata o nella stessa rete virtuale a cui è connessa, anche se non è necessario.

È possibile collegare più interfacce di rete alla stessa VM, a patto che la VM le supporti. Per altre informazioni sulle dimensioni delle macchine virtuali che supportano più interfacce di rete, leggere gli articoli [Dimensioni delle VM di Windows Server](../virtual-machines/virtual-machines-windows-sizes.md) e [Dimensioni delle VM di Linux](../virtual-machines/virtual-machines-linux-sizes.md).

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni su come creare una VM con una singola interfaccia di rete, leggere l'articolo [Creare una VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .
* Per informazioni su come creare una VM con più interfacce di rete, leggere l'articolo [Distribuire una VM con più interfacce di rete](virtual-network-deploy-multinic-arm-ps.md) .
* Per informazioni su come creare un'interfaccia di rete con più configurazioni IP, leggere l'articolo [Multiple IP addresses for Azure virtual machines](virtual-network-multiple-ip-addresses-powershell.md) (Più indirizzi IP per le macchine virtuali di Azure).

<!--HONumber=Oct16_HO2-->


