---
title: Residenza dei dati per Azure Network Watcher | Microsoft Docs
description: Comprendere la residenza dei dati per il servizio Network Watcher
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
ms.openlocfilehash: dff5519c1b36a0a7738cb2529c2f4460ecf61e48
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88117836"
---
# <a name="data-residency-for-azure-network-watcher"></a>Residenza dei dati per Azure Network Watcher
Azure Network Watcher non archivia i dati del cliente tranne il servizio di monitoraggio connessione (anteprima).


## <a name="connection-monitor-preview-data-residency"></a>Residenza dei dati di monitoraggio connessione (anteprima)
Il servizio di *monitoraggio connessione (anteprima)* archivia i dati dei clienti. Questi dati vengono archiviati automaticamente da Network Watcher in una singola area. Quindi, il *monitoraggio della connessione (anteprima)* soddisfa automaticamente i requisiti di residenza dei dati in area, tra cui quelli specificati in [Centro protezione](https://azuredatacentermap.azurewebsites.net/).

## <a name="singapore-data-residency"></a>Residenza dei dati di Singapore

In Azure, la funzionalità che consente di archiviare i dati dei clienti in una singola area è attualmente disponibile solo nell'area Asia sud-orientale (Singapore) della Asia Pacifico geografica. Per tutte le altre aree, i dati dei clienti vengono archiviati in geografico. Per ulteriori informazioni, vedere [Centro protezione](https://azuredatacentermap.azurewebsites.net/).

> [!Note]
> **Replica precedente** I clienti hanno la possibilità di archiviare gli indirizzi IP degli utenti finali con la relativa istanza Network Watcher in modo che Network Watcher possa monitorare la raggiungibilità, la latenza e le modifiche alla topologia di rete correlate agli indirizzi IP degli utenti finali. Questi indirizzi IP dell'utente finale possono essere classificati come dati del cliente. A partire dal 15 luglio 2020, Network Watcher archiviati i dati in una singola area. (I dati del cliente non vengono più replicati in HK). Questi dati non vengono più replicati in HK. I dati dei clienti sono e sono stati crittografati a riposo.
> 
> Se i dati del cliente fossero accessibili da terze parti, ciò consentirebbe alla terza parte di conoscerne l'indirizzo IP, ma non concederebbe l'accesso di terze parti al computer o al dispositivo associato all'indirizzo IP. Network Watcher ritiene che non vi siano terze parti che accedono ai dati dei clienti in HK.

## <a name="next-steps"></a>Passaggi successivi

* Leggi una panoramica del [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).
