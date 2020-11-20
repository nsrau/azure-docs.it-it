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
ms.openlocfilehash: 2e6a92a4d08f1603f480a990ad437a90302a8189
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966089"
---
# <a name="data-residency-for-azure-network-watcher"></a>Residenza dei dati per Azure Network Watcher
Fatta eccezione per il servizio monitoraggio connessione (anteprima), Azure Network Watcher non archivia i dati dei clienti.


## <a name="connection-monitor-preview-data-residency"></a>Residenza dei dati di monitoraggio connessione (anteprima)
Il servizio di monitoraggio connessione (anteprima) archivia i dati dei clienti. Questi dati vengono archiviati automaticamente da Network Watcher in una singola area. Quindi, monitoraggio connessione (anteprima) soddisfa automaticamente i requisiti di residenza dei dati in area, inclusi i requisiti specificati nel [Centro protezione](https://azuredatacentermap.azurewebsites.net/).

## <a name="data-residency"></a>Residenza dei dati
In Azure, la funzionalità che consente l'archiviazione dei dati dei clienti in una singola area è attualmente disponibile solo nell'area Asia sudorientale (Singapore) dell'area geografica di Asia Pacifico geografica e del Brasile meridionale (stato di San Paolo) del Brasile. Per tutte le altre aree i dati dei clienti vengono archiviati in Geo. Per ulteriori informazioni, vedere [Centro protezione](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Passaggi successivi

* Leggi una panoramica del [Network Watcher](./network-watcher-monitoring-overview.md).