---
title: Soluzione Azure VMware di CloudSimple - Accesso al client vSphere
description: Descrive come accedere a vCenter del cloud privato.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 18d9463bc512257034860e1188372879524924f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022665"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>Accedi al portale private cloud per vCenter

È possibile avviare il portale private cloud vCenter dal portale di Azure o dal portale CloudSimple.You can launch your Private Cloud vCenter portal from Azure portal or CloudSimple portal.  Il portale vCenter consente di gestire l'infrastruttura VMware sul cloud privato.

## <a name="before-you-begin"></a>Prima di iniziare

È necessario stabilire una connessione di rete e abilitare la risoluzione dei nomi DNS per l'accesso al portale vCenter.  È possibile stabilire una connessione di rete al cloud privato utilizzando una delle opzioni riportate di seguito.

* [Connettersi da locale a CloudSimple usando ExpressRouteConnect from on-premises to CloudSimple using ExpressRoute](on-premises-connection.md)
* [Configurare una connessione VPN al cloud privato CloudSimple](set-up-vpn.md)

Per configurare la risoluzione dei nomi DNS dei componenti dell'infrastruttura Private Cloud VMware, vedere [Configure DNS for name resolution for Private Cloud vCenter access from on-premises workstations](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di [https://portal.azure.com](https://portal.azure.com)Azure all'indirizzo .

## <a name="access-vcenter-from-azure-portal"></a>Accedere a vCenter dal portale di AzureAccess vCenter from Azure portal

È possibile avviare il portale vCenter del cloud privato dal portale di Azure.You can launch vCenter portal of your Private Cloud from Azure portal.

1. Selezionare **Tutti i servizi**.

2. Cercare **CloudSimple Services**.

3. Selezionare il servizio CloudSimple del cloud privato a cui si desidera connettersi.

4. Nella pagina **Panoramica** fare clic su **Visualizza cloud privati VMware**

    ![Panoramica del servizio CloudSimple](media/cloudsimple-service-overview.png)

5. Selezionare il cloud privato dall'elenco dei cloud privati e fare clic su **Avvia client vSphere**.

    ![Avvia vSphere Client](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>Accesso a vCenter dal portale CloudSimple

È possibile avviare il portale vCenter del cloud privato dal portale CloudSimple.

1. Accedi al tuo [portale CloudSimple](access-cloudsimple-portal.md).

2. Dalle **risorse** selezionare il Cloud privato, a cui si desidera accedere e fare clic su **Avvia vSphere Client**.

    ![Avviare vSphere Client - Risorse](media/cloudsimple-portal-resources-launch-vcenter.png)

3. È inoltre possibile avviare il portale vCenter dalla schermata di riepilogo del cloud privato.

    ![Avvia client vSphere - Riepilogo](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Passaggi successivi

* [Creare e gestire VLAN/subnet per i cloud privati](create-vlan-subnet.md)
* [Modello di autorizzazione CloudSimple Private Cloud di VMware vCenter](learn-private-cloud-permissions.md)