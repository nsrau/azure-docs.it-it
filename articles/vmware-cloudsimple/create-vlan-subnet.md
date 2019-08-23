---
title: Creare VLAN/subnet
description: 'Soluzione VMware di Azure di CloudSimple: viene descritto come creare e gestire VLAN/subnet per i cloud privati e quindi applicare le regole del firewall.'
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e6f781926e2a30ecf6d34274b20991b81f0bb96d
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972914"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>Creare e gestire VLAN/subnet per i cloud privati

Aprire la scheda VLAN/subnet nella pagina rete per creare e gestire VLAN/subnet per i cloud privati. Dopo aver creato una VLAN o una subnet, è possibile applicare le regole del firewall.

## <a name="create-a-vlansubnet"></a>Creare una VLAN/subnet

1. [Accedere al portale di CloudSimple](monitor-activity.md) e selezionare **rete** dal menu laterale.
2. Selezionare **VLAN/subnet**.
3. Fare clic su **Crea VLAN/subnet**.

    ![Pagina VLAN/subnet](media/vlan-subnet-page.png)

4. Selezionare il cloud privato per la nuova VLAN/subnet.
5. Immettere un ID VLAN.
6. Immettere il nome della subnet.
7. Per abilitare il routing sulla VLAN (subnet), specificare l'intervallo CIDR della subnet. Verificare che l'intervallo CIDR non si sovrappongano con le subnet locali, le subnet di Azure o la subnet del gateway.
8. Fare clic su **Invia**.

    ![Crea VLAN/subnet](media/create-new-vlan-subnet-details.png)

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>Usare le informazioni VLAN per configurare un gruppo di porte distribuite in vSphere

Per creare un gruppo di porte distribuite in vSphere, seguire le istruzioni riportate nell'argomento relativo all'aggiunta di un gruppo di porte distribuite in VMware nella <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">Guida alla rete di vSphere</a>. Quando si configura il gruppo di porte distribuite, fornire le informazioni VLAN dalla configurazione CloudSimple.

![Gruppo di porte distribuite](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Selezionare una tabella del firewall

Le tabelle del firewall e le regole associate sono definite nella pagina **rete > firewall** . Per selezionare la tabella del firewall da applicare alla VLAN/subnet per un cloud privato, selezionare la VLAN/subnet fare clic su **allegato tabella firewall** nella pagina **VLAN/Subnet** . Per istruzioni sulla configurazione di tabelle del firewall e sulla definizione di regole, vedere [tabelle del firewall](firewall.md) .

![Collegamento tabella firewall](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Una subnet può essere associata a una tabella del firewall. Una tabella del firewall può essere associata a più subnet.

## <a name="edit-a-vlansubnet"></a>Modificare una VLAN/subnet

Per modificare le impostazioni per una VLAN o una subnet, selezionarla nella pagina **VLAN/subnet** e fare clic sull'icona **modifica** . Apportare le modifiche e fare clic su **submet**.

## <a name="delete-a-vlansubnet"></a>Eliminare una VLAN o una subnet

Per eliminare una VLAN o una subnet, selezionarla nella pagina **VLAN/subnet** e fare clic sull'icona **Elimina** . Fare clic su **Elimina** per confermare.
