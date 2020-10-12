---
title: Gestione dell'accesso alla rete pubblica per l'hub Azure
description: Documentazione su come disabilitare e abilitare l'accesso alla rete pubblica per l'hub Internet
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: c82f98df8fb79fa10f2e30b219c1a02bb646e2de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85937519"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>Gestione dell'accesso alla rete pubblica per l'hub Internet delle cose

Per limitare l'accesso solo all' [endpoint privato per l'hub Internet delle cose nel VNet](virtual-network-support.md), disabilitare l'accesso alla rete pubblica. A tale scopo, usare portale di Azure o l' `publicNetworkAccess` API. 

## <a name="turn-off-public-network-access-using-azure-portal"></a>Disabilitare l'accesso alla rete pubblica con portale di Azure

1. Visita [portale di Azure](https://portal.azure.com)
2. Passare all'hub IoT.
3. Selezionare **rete** dal menu a sinistra.
4. In "Consenti accesso alla rete pubblica a" selezionare **disabilitato**
5. Selezionare **Salva**.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="Immagine che Mostra portale di Azure dove disattivare l'accesso alla rete pubblica" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

Per abilitare l'accesso alla rete pubblica, selezionare **abilitato**, quindi **Salva**.

## <a name="ip-filter"></a>Filtro IP 

Se l'accesso alla rete pubblica è disabilitato, tutte le regole del [filtro IP](iot-hub-ip-filtering.md) verranno ignorate. Poiché tutti gli indirizzi IP della rete Internet pubblica sono bloccati. Per utilizzare il filtro IP, utilizzare l'opzione **intervalli IP selezionati** .

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>Correzione di bug con endpoint compatibile con l'hub eventi predefinito

Si è verificato un bug con l'hub Internet [, in cui l'endpoint compatibile con l'hub eventi predefinito](iot-hub-devguide-messages-read-builtin.md) continua a essere accessibile tramite Internet pubblico quando l'accesso alla rete pubblica per l'hub Internet è disabilitato. Per ulteriori informazioni e per informazioni su questo bug, vedere la pagina [relativa alla disabilitazione dell'accesso alla rete pubblica per l'hub Internet che disabilita l'accesso all'endpoint predefinito dell'hub eventi](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix).