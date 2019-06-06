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
ms.openlocfilehash: cacd5510147ce997efec922f4b4656956a098d88
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676939"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Registrare il provider di risorse Microsoft.VMwareCloudSimple nella sottoscrizione di Azure

Il servizio CloudSimple consente all'utente di Azure VMware Solution by CloudSimple. Per usare il servizio CloudSimple, è necessario prima abilitarlo nella sottoscrizione di Azure. È quindi possibile registrare il servizio Microsoft.VMwareCloudSimple come provider di risorse.

## <a name="enable-the-cloudsimple-service"></a>Abilitare il servizio CloudSimple

Per abilitare il servizio CloudSimple nella sottoscrizione di Azure, aprire una richiesta di supporto con [supporto tecnico Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Selezionare le opzioni seguenti quando si invia la richiesta.

* Tipo di problema: **Tecnico**
* Sottoscrizione: **L'ID sottoscrizione**
* Tipo di servizio: **Soluzione VMware da CloudSimple**
* Tipo di problema: **Quota di nodi dedicato**
* Sottotipo di problema: **Aumentare la quota di nodi dedicati**
* Oggetto: **Abilitare il servizio CloudSimple**

È anche possibile contattare il rappresentante dell'account Microsoft al [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com). Fornire l'ID della sottoscrizione di Azure nel messaggio di posta elettronica.  

Dopo che il servizio CloudSimple è abilitato per la sottoscrizione, è possibile abilitare il provider di risorse nella sottoscrizione.

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