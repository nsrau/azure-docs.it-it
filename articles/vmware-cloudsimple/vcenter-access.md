---
title: Azure VMware Solutions (AVS)-accedere a vSphere client
description: Viene descritto come accedere a vCenter dal cloud privato AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ad018ea89b194d42ab1867a0569725c4c3680f7d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022665"
---
# <a name="access-your-avs-private-cloud-vcenter-portal"></a>Accedi al portale di AVS privato Cloud vCenter

È possibile avviare AVS private cloud vCenter Portal da portale di Azure o AVS Portal. vCenter Portal consente di gestire l'infrastruttura VMware nel cloud privato AVS.

## <a name="before-you-begin"></a>Prima di iniziare

È necessario stabilire la connessione di rete e la risoluzione dei nomi DNS deve essere abilitata per l'accesso al portale di vCenter. È possibile stabilire una connessione di rete al cloud privato AVS usando una delle opzioni riportate di seguito.

* [Connettersi da locale a AVS usando ExpressRoute](on-premises-connection.md)
* [Configurare una connessione VPN al cloud privato AVS](set-up-vpn.md)

Per configurare la risoluzione dei nomi DNS per i componenti dell'infrastruttura di VMware del cloud privato AVS, vedere [configurare DNS per la risoluzione dei nomi per AVS private cloud vCenter Access da workstation locali](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Accedere a Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="access-vcenter-from-azure-portal"></a>Accedere a vCenter da portale di Azure

È possibile avviare il portale vCenter del cloud privato AVS da portale di Azure.

1. Selezionare **Tutti i servizi**.

2. Cercare **AVS Services**.

3. Selezionare il servizio AVS del cloud privato AVS a cui si vuole connettersi.

4. Nella pagina **Overview** fare clic su **View VMware AVS private Clouds**

    ![Panoramica di AVS Service](media/cloudsimple-service-overview.png)

5. Selezionare AVS private cloud dall'elenco dei cloud privati AVS e fare clic su **Launch vSphere client**.

    ![Avviare vSphere client](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-avs-portal"></a>Accedere a vCenter dal portale AVS

È possibile avviare il portale vCenter del cloud privato AVS da AVS Portal.

1. Accedere al [portale AVS](access-cloudsimple-portal.md).

2. Dalle **risorse** selezionare il cloud privato AVS a cui si vuole accedere e fare clic su **avvia client vSphere**.

    ![Avviare vSphere Client-Resources](media/cloudsimple-portal-resources-launch-vcenter.png)

3. È anche possibile avviare il portale vCenter dalla schermata di riepilogo del cloud privato AVS.

    ![Avviare vSphere Client-Summary](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Passaggi successivi

* [Creare e gestire VLAN/subnet per i cloud privati AVS](create-vlan-subnet.md)
* [Modello di autorizzazione AVS private cloud di VMware vCenter](learn-private-cloud-permissions.md)