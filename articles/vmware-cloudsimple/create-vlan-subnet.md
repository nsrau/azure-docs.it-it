---
title: Creare VLAN/subnet - Soluzione Di Azure VMware di CloudSimpleCreate VLNs/subnets - Azure VMware Solution by CloudSimple
description: 'Soluzioni VMware di Azure da CloudSimple: viene descritto come creare e gestire VLAN/subnet per i cloud privati e quindi applicare le regole del firewall.'
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0160513eb9abca54adbc3819b982348dc202c90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565996"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>Creare e gestire VLAN/subnet per i cloud privati

Aprire la scheda VLAN/Subnet nella pagina Rete per creare e gestire VLAN/subnet per i cloud privati. Dopo aver creato una VLAN/subnet, è possibile applicare le regole del firewall.

## <a name="create-a-vlansubnet"></a>Creare una rete Virtuale/subnetCreate a VLAN/subnet

1. [Accedere al portale CloudSimple](access-cloudsimple-portal.md) e selezionare **Rete** nel menu laterale.
2. Selezionare **VLAN/subnet**.
3. Fare clic su **Crea VLAN/Subnet**.

    ![VLAN/pagina subnet](media/vlan-subnet-page.png)

4. Selezionare il cloud privato per la nuova VLAN/subnet.
5. Immettere un ID VLAN.
6. Immettere il nome della subnet.
7. Per abilitare il routing sulla VLAN (subnet), specificare l'intervallo CIDR della subnet. Assicurarsi che l'intervallo CIDR non si sovrapponga a nessuna delle subnet locali, delle subnet di Azure o della subnet del gateway.
8. Fare clic su **Submit**.

    ![Crea VLAN/subnet](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> Esiste una quota di 30 VLAN per cloud privato. Questi limiti possono essere aumentati [contattando](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)il supporto .

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>Utilizzare le informazioni VLAN per impostare un gruppo di porte distribuite in vSphereUse VLAN information to set up a distributed port group in vSphere

Per creare un gruppo di porte distribuite in vSphere, seguire le istruzioni nell'argomento di VMware "Aggiungere un gruppo di porte distribuite" nella Guida alla <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">rete di vSphere</a>. Quando si configura il gruppo di porte distribuite, fornire le informazioni VLAN dalla configurazione CloudSimple.

![Gruppo di porte distribuiteDistributed Port Group](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Selezionare una tabella firewall

Le tabelle del firewall e le regole associate sono definite nella pagina Tabelle del firewall > di **rete.** Per selezionare la tabella firewall da applicare alla VLAN/subnet per un cloud privato, selezionare **l'allegato** della tabella Firewall o VLAN nella pagina **VLAN/Subnets.** Vedere [Tabelle firewall](firewall.md) per istruzioni sulla configurazione delle tabelle del firewall e sulla definizione delle regole.

![Collegamento tabella firewall](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Una subnet può essere associata a una tabella del firewall. Una tabella firewall può essere associata a più subnet.

## <a name="edit-a-vlansubnet"></a>Modificare una rete Virtuale/subnet

Per modificare le impostazioni di una VLAN/Subnet, selezionarla nella pagina **VLAN/Subnets** e fare clic sull'icona **Modifica.** Apportare le modifiche e fare clic su **Submet**.

## <a name="delete-a-vlansubnet"></a>Eliminare una Rete Virtuale/subnet

Per eliminare una VLAN/Subnet, selezionarla nella pagina **VLAN/Subnets** e fare clic sull'icona **Elimina.** Fare clic su **Elimina** per confermare.
