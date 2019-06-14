---
title: Creare Azure VMware Solution by CloudSimple - servizio
description: Viene descritto come creare il servizio CloudSimple nel portale di Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fc08ce48b3b5a4b2cd28cb66e6dd4d9eae3f3885
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676954"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>Creare soluzione VMware CloudSimple - servizio di Azure

Per iniziare a usare Azure VMware soluzione da CloudSimple, creare la soluzione VMware Azure dal servizio CloudSimple nel portale di Azure.

> [!NOTE]
> Prima di creare il servizio CloudSimple, è necessario registrare il provider di risorse Microsoft.VMwareCloudSimple nella sottoscrizione di Azure. Seguire i passaggi descritti in [abilitare il provider di risorse nella sottoscrizione di Azure Microsoft.VMwareCloudSimple](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Creare il servizio

1. Selezionare **Tutti i servizi**.

2. Cercare **CloudSimple servizi**.

    ![Servizio di ricerca CloudSimple](media/create-cloudsimple-service-search.png)

3. Selezionare **CloudSimple servizi**.

4. Fare clic su **Add** per creare un nuovo servizio.

    ![Aggiungere servizio CloudSimple](media/create-cloudsimple-service-add.png)

5. Selezionare la sottoscrizione in cui si desidera creare il servizio CloudSimple.

6. Selezionare il gruppo di risorse per il servizio. Per aggiungere un nuovo gruppo di risorse, fare clic su **Crea nuovo**.

7. Immettere il nome per identificare il servizio.

8. Immettere il CIDR per il gateway del servizio. Specificare/28 subnet che non si sovrapponga con una qualsiasi delle subnet esistenti.  Sono incluse le subnet in locale, le subnet di Azure, o qualsiasi pianificata CloudSimple subnet. È possibile modificare il CIDR dopo la creazione del servizio.

    ![Creazione del servizio CloudSimple](media/create-cloudsimple-service.png)

9. Fare clic su **OK**.

Il servizio viene creato e aggiunto all'elenco dei servizi.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un cloud privato](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Informazioni su come [configurare un ambiente cloud privato](quickstart-create-private-cloud.md)
