---
title: Eseguire il mapping al pool di risorse in Azure VMware Solution by CloudSimple sottoscrizione di Azure
description: Viene descritto come eseguire il mapping di un pool di risorse in Azure VMware Solution by CloudSimple alla sottoscrizione di Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efda996e03d46a2f97d19558f7c2930b623a639e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332866"
---
# <a name="map-resource-pools-from-your-private-cloud-to-your-azure-subscription"></a>Eseguire il mapping di pool di risorse dal Cloud privato per la sottoscrizione di Azure

Mapping di sottoscrizione di Azure consente di eseguire il mapping di pool di risorse dal Cloud privato vCenter alla sottoscrizione di Azure. È possibile mappare solo la sottoscrizione in cui è stato creato il servizio CloudSimple.  Creazione di una macchina virtuale VMware dal portale di Azure distribuisce la macchina virtuale nel pool di risorse con mapping.  Nel portale di CloudSimple, è possibile visualizzare e gestire la sottoscrizione di Azure per i cloud privati.

Una sottoscrizione può essere mappata per più pool di risorse di vCenter di un cloud privato.  È necessario eseguire il mapping dei pool di risorse di ogni cloud privato.  Solo i pool di risorse mappati saranno disponibili per la creazione di una macchina virtuale VMware dal portale di Azure.

> [!IMPORTANT]
> Mapping di un pool di risorse, esegue il mapping anche qualsiasi pool di risorse figlio. Un pool di risorse padre non è possibile eseguire il mapping se sono già mappati qualsiasi pool di risorse figlio.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo si presuppone un servizio CloudSimple e il cloud privato nella propria sottoscrizione.  Per creare un servizio CloudSimple, vedere [Guida introduttiva - creare servizio](quickstart-create-cloudsimple-service.md).  Se è necessario creare un cloud privato, vedere [Guida introduttiva - configurare un ambiente cloud privato](quickstart-create-private-cloud.md).

È possibile eseguire il mapping del cluster di vCenter (pool di risorse di primo livello) alla sottoscrizione.  Se si desidera creare un nuovo pool di risorse, vedere [creare un Pool di risorse](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html) articolo sul sito della documentazione di VMware.

## <a name="default-resource-group"></a>Gruppo di risorse predefinito

Creazione di una nuova macchina virtuale CloudSimple dal portale di Azure consente di selezionare il gruppo di risorse.  Una macchina virtuale creata in vCenter di cloud privato in un pool di risorse con mapping saranno visibile nel portale di Azure.  La macchina virtuale individuata verrà inserita nel gruppo di risorse di Azure predefinito.  È possibile modificare il nome del gruppo di risorse predefinito.

## <a name="map-azure-subscription"></a>Eseguire il mapping di sottoscrizione di Azure

1. Accesso di [CloudSimple portale](access-cloudsimple-portal.md).

2. Aprire il **risorse** pagina e selezionare il cloud privato che si desidera eseguire il mapping.

3. Selezionare **le sottoscrizioni di Azure mapping**.

4. Fare clic su **mapping di sottoscrizione di Azure modifica**.

5. Per mappare i pool di risorse disponibili, selezionarli a sinistra e fare clic sulla freccia rivolta verso destra.

6. Per rimuovere i mapping, selezionarle nella parte destra e fare clic sulla freccia rivolta verso sinistra.

    ![Sottoscrizioni Azure](media/resources-azure-mapping.png)

7. Fare clic su **OK**.

## <a name="change-default-resource-group-name"></a>Nome del gruppo di risorse predefinito di modifica

1. Accesso di [CloudSimple portale](access-cloudsimple-portal.md).

2. Aprire il **risorse** pagina e selezionare il cloud privato che si desidera eseguire il mapping.

3. Selezionare **le sottoscrizioni di Azure mapping**.

4. Fare clic su **modifica** sotto il nome di gruppo di risorse di Azure.

    ![Modificare il nome di gruppo di risorse](media/resources-edit-resource-group-name.png)

5. Immettere un nuovo nome per il gruppo di risorse e fare clic su **Submit**.

    ![Immettere nome nuovo gruppo di risorse](media/resources-new-resource-group-name.png)

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare le macchine virtuali VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Altre informazioni su [macchine virtuali CloudSimple](cloudsimple-virtual-machines.md)