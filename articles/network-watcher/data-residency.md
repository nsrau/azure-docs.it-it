---
title: Residenza dei dati per Azure Network Watcher | Microsoft Docs
description: Questo articolo consente di comprendere la residenza dei dati per il servizio Network Watcher di Azure.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: damendo
ms.openlocfilehash: 9451b6636f2f87806e3d1e39fec4e9e4d4390485
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706823"
---
# <a name="data-residency-for-azure-network-watcher"></a>Residenza dei dati per Azure Network Watcher
Fatta eccezione per il servizio monitoraggio connessione (anteprima), Azure Network Watcher non archivia i dati dei clienti.


## <a name="connection-monitor-preview-data-residency"></a>Residenza dei dati di monitoraggio connessione (anteprima)
Il servizio di monitoraggio connessione (anteprima) archivia i dati dei clienti. Questi dati vengono archiviati automaticamente da Network Watcher in una singola area. Quindi, monitoraggio connessione (anteprima) soddisfa automaticamente i requisiti di residenza dei dati in area, inclusi i requisiti specificati nel [Centro protezione](https://azuredatacentermap.azurewebsites.net/).

## <a name="singapore-data-residency"></a>Residenza dei dati per Singapore

In Azure, la funzionalità che consente l'archiviazione dei dati dei clienti in una singola area è attualmente disponibile solo nell'area Asia sudorientale (Singapore) del Asia Pacifico geografico. Per tutte le altre aree, i dati dei clienti vengono archiviati in geografico. Per ulteriori informazioni, vedere [Centro protezione](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Passaggi successivi

* Leggi una panoramica del [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).
