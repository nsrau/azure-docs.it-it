---
title: 'Soluzione VMware di Azure di CloudSimple: creare un servizio CloudSimple'
description: Viene descritto come creare il servizio CloudSimple nel portale di Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d6d4af28d79b2375e774da98d4fdb1ad9dc22063
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035728"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Creare la soluzione VMware di Azure tramite il servizio CloudSimple

Per iniziare a usare la soluzione VMware di Azure con CloudSimple, creare la soluzione VMware di Azure tramite il servizio CloudSimple nel portale di Azure.

> [!IMPORTANT]
> Prima di creare il servizio CloudSimple, è necessario registrare il provider di risorse Microsoft. VMwareCloudSimple nella sottoscrizione di Azure. Seguire i passaggi in [abilitare il provider di risorse Microsoft. VMwareCloudSimple nella sottoscrizione di Azure](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-the-service"></a>Creare il servizio

1. Selezionare **Tutti i servizi**.
2. Cercare i **Servizi CloudSimple**.
    ![Cerca servizio CloudSimple](media/create-cloudsimple-service-search.png)
3. Selezionare **Servizi CloudSimple**.
4. Fare clic su **Aggiungi** per creare un nuovo servizio.
    ![Aggiungere il servizio CloudSimple](media/create-cloudsimple-service-add.png)
5. Selezionare la sottoscrizione in cui si vuole creare il servizio CloudSimple.
6. Selezionare il gruppo di risorse per il servizio. Per aggiungere un nuovo gruppo di risorse, fare clic su **Crea nuovo**.
7. Immettere il nome per identificare il servizio.
8. Immettere il CIDR per il gateway del servizio. Specificare una subnet/28 che non si sovrappone ad alcuna subnet locale, subnet di Azure o subnet CloudSimple pianificate. Non è possibile modificare il CIDR dopo che il servizio è stato creato.

    ![Creazione del servizio CloudSimple](media/create-cloudsimple-service.png)
9. Fare clic su **OK**.

Il servizio viene creato e aggiunto all'elenco dei servizi.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un cloud privato](create-private-cloud.md)
* Informazioni su come [configurare un ambiente cloud privato](quickstart-create-private-cloud.md)
