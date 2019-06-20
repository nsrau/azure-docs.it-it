---
title: Abilitare la soluzione VMware Azure dal servizio CloudSimple
description: Viene descritto come abilitare il servizio CloudSimple in una sottoscrizione di Azure e quindi registrare il provider di risorse CLoudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2553aa95d5028c510b4e1a1b7f51a9f410bcea51
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154855"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Registrare il provider di risorse Microsoft.VMwareCloudSimple nella sottoscrizione di Azure

Il servizio CloudSimple consente all'utente di Azure VMware Solution by CloudSimple. È possibile registrare il servizio Microsoft.VMwareCloudSimple come provider di risorse.

## <a name="register-the-resource-provider"></a>Registrare il provider di risorse

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **Tutti i servizi**.

3. Cercare e selezionare **sottoscrizioni**.

    ![Selezionare le sottoscrizioni](media/cloudsimple-service-select-subscriptions.png)

4. Selezionare la sottoscrizione in cui si desidera abilitare il servizio CloudSimple.

5. Fare clic su **provider di risorse** per la sottoscrizione.

6. Uso **Microsoft.VMwareCloudSimple** per filtrare il provider di risorse.

7. Selezionare il provider di risorse e fare clic su **registrare**.

    ![Registrare il provider di risorse](media/cloudsimple-service-enable-resource-provider.png)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un servizio CloudSimple](create-cloudsimple-service.md)
* Informazioni su come [configurare un ambiente cloud privato](quickstart-create-private-cloud.md)
