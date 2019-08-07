---
title: Eseguire il mapping della sottoscrizione di Azure ai pool di risorse in una soluzione VMware di Azure di CloudSimple
description: Viene descritto come eseguire il mapping di un pool di risorse nella soluzione VMware di Azure CloudSimple alla sottoscrizione di Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1bf721f35500d2ff1344996e7750c5e574f40f31
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816258"
---
# <a name="map-resource-pools-from-your-private-cloud-to-your-azure-subscription"></a>Eseguire il mapping dei pool di risorse dal cloud privato alla sottoscrizione di Azure

Il mapping delle sottoscrizioni di Azure consente di eseguire il mapping dei pool di risorse dal cloud privato vCenter alla sottoscrizione di Azure. È possibile eseguire il mapping solo della sottoscrizione in cui è stato creato il servizio CloudSimple.  La creazione di una macchina virtuale VMware dalla portale di Azure distribuisce la macchina virtuale nel pool di risorse di cui è stato eseguito il mapping.  Nel portale di CloudSimple è possibile visualizzare e gestire la sottoscrizione di Azure per i cloud privati.

È possibile eseguire il mapping di una sottoscrizione a più pool di risorse vCenter di un cloud privato.  È necessario eseguire il mapping dei pool di risorse di ogni cloud privato.  Solo i pool di risorse di cui è stato eseguito il mapping saranno disponibili per la creazione di una macchina virtuale VMware dalla portale di Azure.

> [!IMPORTANT]
> Il mapping di un pool di risorse esegue anche il mapping dei pool di risorse figlio. Impossibile eseguire il mapping di un pool di risorse padre se è già stato eseguito il mapping di un pool di risorse figlio.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che l'utente disponga di un servizio CloudSimple e di un cloud privato nella sottoscrizione.  Per creare un servizio CloudSimple, vedere [Guida introduttiva-creare un servizio](quickstart-create-cloudsimple-service.md).  Se è necessario creare un cloud privato, vedere [Guida introduttiva-configurare un ambiente cloud privato](quickstart-create-private-cloud.md).

È possibile eseguire il mapping del cluster vCenter (pool di risorse radice) alla sottoscrizione.  Se si vuole creare un nuovo pool di risorse, vedere [l'articolo creare un pool di risorse](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html) nel sito della documentazione VMware.

## <a name="default-resource-group"></a>Gruppo di risorse predefinito

La creazione di una nuova macchina virtuale CloudSimple da portale di Azure consente di selezionare il gruppo di risorse.  Una macchina virtuale creata nel cloud privato vCenter in un pool di risorse mappato sarà visibile in portale di Azure.  La macchina virtuale individuata verrà inserita nel gruppo di risorse di Azure predefinito.  È possibile modificare il nome del gruppo di risorse predefinito.

## <a name="map-azure-subscription"></a>Mappare la sottoscrizione di Azure

1. Accedere al [portale di CloudSimple](access-cloudsimple-portal.md).

2. Aprire la pagina **risorse** e selezionare il cloud privato di cui si desidera eseguire il mapping.

3. Selezionare **mapping sottoscrizioni di Azure**.

4. Fare clic su **Modifica mapping della sottoscrizione di Azure**.

5. Per mappare i pool di risorse disponibili, selezionarli a sinistra e fare clic sulla freccia rivolta verso destra.

6. Per rimuovere i mapping, selezionarli a destra e fare clic sulla freccia rivolta verso sinistra.

    ![Sottoscrizioni di Azure](media/resources-azure-mapping.png)

7. Fare clic su **OK**.

## <a name="change-default-resource-group-name"></a>Modificare il nome del gruppo di risorse predefinito

1. Accedere al [portale di CloudSimple](access-cloudsimple-portal.md).

2. Aprire la pagina **risorse** e selezionare il cloud privato di cui si desidera eseguire il mapping.

3. Selezionare **mapping sottoscrizioni di Azure**.

4. Fare clic su **modifica** in nome gruppo di risorse di Azure.

    ![Modificare il nome del gruppo di risorse](media/resources-edit-resource-group-name.png)

5. Immettere un nuovo nome per il gruppo di risorse e fare clic su **Submit (Invia**).

    ![Immettere il nome del nuovo gruppo di risorse](media/resources-new-resource-group-name.png)

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare macchine virtuali VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Altre informazioni sulle [macchine virtuali CloudSimple](cloudsimple-virtual-machines.md)