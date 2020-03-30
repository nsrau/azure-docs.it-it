---
title: Effettuare il provisioning di un gateway self-hosted in Gestione API di Azure. Documenti Microsoft
description: Informazioni su come eseguire il provisioning di un gateway self-hosted in Gestione API di Azure.Learn how to provision a self-hosted gateway in Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: d33c5f75234ad7165a9062ecc3bb2a00d502f8c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075279"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Effettuare il provisioning di un gateway self-hosted in Gestione API di AzureProvision a self-hosted gateway in Azure API Management

Il provisioning di una risorsa gateway nell'istanza di Gestione API di Azure è un prerequisito per la distribuzione di un gateway self-hosted. Questo articolo illustra i passaggi per eseguire il provisioning di una risorsa gateway in Gestione API.

> [!NOTE]
> La funzionalità gateway self-hosted è in anteprima. Durante l'anteprima, il gateway self-hosted è disponibile solo nei livelli Developer e Premium senza costi aggiuntivi. Il livello sviluppatore è limitato a una singola distribuzione di gateway self-hosted.

## <a name="prerequisites"></a>Prerequisiti

Completare la guida introduttiva seguente: [Creare un'istanza](get-started-create-service-instance.md) di Gestione API di AzureComplete the following quickstart: Create an Azure API Management instance

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Effettuare il provisioning di un gateway self-hosted

1. Selezionare **I gateway** in **Impostazioni**.
2. Fare clic su **Aggiungi .**
3. Immettere il **nome** e **l'area** del gateway.
> [!TIP]
> **L'area** specifica la posizione prevista dei nodi gateway che verranno associati a questa risorsa gateway. È semanticamente equivalente a una proprietà simile associata a qualsiasi risorsa di Azure, ma può essere assegnato un valore stringa arbitrario.

4. Facoltativamente, immettere una **Descrizione** della risorsa gateway.
5. Facoltativamente, **+** selezionare in **API** per associare una o più API a questa risorsa gateway.
> [!TIP]
> È possibile associare e rimuovere un'API da un gateway nella scheda **Impostazioni** dell'API.

> [!IMPORTANT]
> Per impostazione predefinita, nessuna delle API esistenti verrà associata alla nuova risorsa gateway. Pertanto, i tentativi di richiamarli `404 Resource Not Found` tramite il nuovo gateway genereranno risposte.

6. Fare clic su **Aggiungi**.

Ora è stato eseguito il provisioning della risorsa gateway nell'istanza di Gestione API. È possibile procedere alla distribuzione del gateway.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul gateway self-hosted, vedere Panoramica del gateway self-hosted di Gestione API di AzureTo learn more about the self-hosted gateway, see [Azure API Management self-hosted gateway overview](self-hosted-gateway-overview.md)
* Ulteriori informazioni su come [distribuire un gateway self-hosted a Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
* Altre informazioni su come distribuire un gateway indipendente in DockerLearn more about how to [Deploy a self-hosted gateway to Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
