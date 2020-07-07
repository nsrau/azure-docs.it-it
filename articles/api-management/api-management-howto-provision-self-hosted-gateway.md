---
title: Effettuare il provisioning di un gateway self-hosted in gestione API di Azure | Microsoft Docs
description: Informazioni su come eseguire il provisioning di un gateway self-hosted in gestione API di Azure.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: 2870a654faad4e760a9d022488cb2c4c406cbeab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82203132"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Effettuare il provisioning di un gateway self-hosted in gestione API di Azure

Il provisioning di una risorsa gateway nell'istanza di gestione API di Azure è un prerequisito per la distribuzione di un gateway self-hosted. Questo articolo illustra i passaggi per eseguire il provisioning di una risorsa gateway in gestione API.

## <a name="prerequisites"></a>Prerequisiti

Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Effettuare il provisioning di un gateway self-hosted

1. Selezionare i **gateway** in **Impostazioni**.
2. Fare clic su **+ Aggiungi**.
3. Immettere il **nome** e l' **area** del gateway.
> [!TIP]
> **Region** specifica la posizione desiderata dei nodi del gateway che verranno associati a questa risorsa del gateway. È semanticamente equivalente a una proprietà simile associata a qualsiasi risorsa di Azure, ma è possibile assegnarle un valore stringa arbitrario.

4. Facoltativamente, immettere una **Descrizione** della risorsa del gateway.
5. Facoltativamente, selezionare **+** in **API** per associare una o più API a questa risorsa gateway.
> [!IMPORTANT]
> Per impostazione predefinita, nessuna delle API esistenti verrà associata alla nuova risorsa del gateway. Pertanto, i tentativi di richiamarli tramite il nuovo gateway comporteranno una `404 Resource Not Found` risposta.

6. Scegliere **Aggiungi**.

A questo punto è stato effettuato il provisioning della risorsa gateway nell'istanza di gestione API. È possibile procedere con la distribuzione del gateway.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul gateway self-hosted, vedere [Panoramica del gateway self-hosted di gestione API di Azure](self-hosted-gateway-overview.md)
* Altre informazioni su come [distribuire un gateway self-hosted in Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
* Altre informazioni su come [distribuire un gateway self-hosted in Docker](how-to-deploy-self-hosted-gateway-docker.md)
