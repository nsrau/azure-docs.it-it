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
ms.openlocfilehash: 99194c42dbc6ef07301be517021bf0fb4b4e7c23
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173513"
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
    ![Search CloudSimple Service @ no__t-1
3. Selezionare **Servizi CloudSimple**.
4. Fare clic su **Aggiungi** per creare un nuovo servizio.
    ![Add CloudSimple Service @ no__t-1
5. Selezionare la sottoscrizione in cui si vuole creare il servizio CloudSimple.
6. Selezionare il gruppo di risorse per il servizio. Per aggiungere un nuovo gruppo di risorse, fare clic su **Crea nuovo**.
7. Immettere il nome per identificare il servizio.
8. Immettere il CIDR per il gateway del servizio. Specificare una subnet/28 che non si sovrappone ad alcuna subnet locale, subnet di Azure o subnet CloudSimple pianificate. Non è possibile modificare il CIDR dopo che il servizio è stato creato.

    ![Creazione del servizio CloudSimple](media/create-cloudsimple-service.png)
9. Fare clic su **OK**.

Il servizio viene creato e aggiunto all'elenco dei servizi.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come eseguire il [provisioning di nodi](create-nodes.md)
* Informazioni su come [creare un cloud privato](create-private-cloud.md)
* Informazioni su come [configurare un ambiente cloud privato](quickstart-create-private-cloud.md)
