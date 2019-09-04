---
title: Versioni cluster supportate in Azure Service Fabric | Microsoft Docs
description: Informazioni sulle versioni del cluster in Service Fabric di Azure.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/21/2019
ms.author: atsenthi
ms.openlocfilehash: ca7a3cfe62520ed82dcf494a33f3bd188dbebe76
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279989"
---
# <a name="supported-service-fabric-versions"></a>Versioni di Service Fabric supportate

Verificare che il cluster esegua sempre una versione di Azure Service Fabric supportata. Almeno 60 giorni dopo l'annuncio del rilascio di una nuova versione di Service Fabric, termina il supporto per la versione precedente. Gli annunci di nuove versioni sono disponibili nel Blog del [team di Service Fabric](https://azure.microsoft.com/updates/?product=service-fabric).

Per informazioni dettagliate su come fare in modo che il cluster esegua una versione di Service Fabric supportata, vedere i documenti seguenti:

- [Aggiornare un cluster di Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Aggiornare la versione di Service Fabric eseguita nel cluster di Windows Server autonomo](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Versioni supportate

Nella tabella seguente sono elencate le versioni di Service Fabric e le date di fine del supporto.

| Service Fabric Runtime nel cluster | È possibile eseguire l'aggiornamento direttamente dalla versione del cluster |SDK compatibile o versione del pacchetto NuGet | Fine del supporto |
| --- | --- |--- | --- |
| Tutte le versioni del cluster prima di 5.3.121 | 5.1.158.* |Versione 2.3 o precedente |20 gennaio 2017 |
| 5.3.* | 5.1.158.* |Versione 2.3 o precedente |24 febbraio 2017 |
| 5.4.* | 5.1.158.* |Versione 2.4 o precedente |10 maggio 2017       |
| 5.5.* | 5.4.164.* |Versione 2.5 o precedente |10 agosto 2017    |
| 5.6.* | 5.4.164.* |Versione 2.6 o precedente |13 ottobre 2017   |
| 5.7.* | 5.4.164.* |Versione 2.7 o precedente |15 dicembre 2017  |
| 6.0.* | 5.6.205.* |Versione 2.8 o precedente |30 marzo 2018     |
| 6.1.* | 5.7.221.* |Versione 3.0 o precedente |15 luglio 2018      |
| 6.2.* | 6.0.232.* |Versione 3.1 o precedente |26 ottobre 2018   |
| 6.3.* | 6.1.480.* |Versione 3.2 o precedente |31 marzo 2019  |
| 6.4.* | 6.2.301.* |Versione 3.3 o precedente |15 settembre 2019 |
| 6,5. * | 6.4.617.* |Minore o uguale alla versione 3,4 |Versione corrente, quindi nessuna data di fine |

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

Nella tabella seguente sono elencati i sistemi operativi supportati per le versioni di Service Fabric supportate.

| Sistema operativo | Versione Service Fabric supportata più recente |
| --- | --- |
| Windows Server 2012 R2 | Tutte le versioni |
| Windows Server 2016 | Tutte le versioni |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

## <a name="supported-version-names"></a>Nomi di versione supportati

Nella tabella seguente sono elencati i nomi delle versioni di Service Fabric e i numeri di versione corrispondenti.

| Nome versione | Numero di versione di Windows | Numero di versione di Linux |
| --- | --- | --- |
| 5,3 RTO | 5.3.121.9494 | ND |
| 5,3 CU1 | 5.3.204.9494 | ND |
| 5,3 CU2 | 5.3.301.9590 | ND |
| 5,3 CU3 | 5.3.311.9590 | ND |
| 5,4 CU2 | 5.4.164.9494 | ND |
| 5,5 CU1 | 5.5.216.0    | ND |
| 5,5 CU2 | 5.5.219.0    | ND |
| 5,5 CU3 | 5.5.227.0    | ND |
| 5,5 CU4 | 5.5.232.0    | ND |
| 5,6 RTO | 5.6.204.9494 | ND |
| 5,6 CU2 | 5.6.210.9494 | ND |
| 5,6 CU3 | 5.6.220.9494 | ND |
| 5,7 RTO | 5.7.198.9494 | ND |
| 5,7 CU4 | 5.7.221.9494 | ND |
| 6,0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6,0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6,0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6,1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6,1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6,1 CU3 | 6.1.472.9494 | ND |
| 6,1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6,2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6,2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6,2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6,2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6,3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6,3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6,3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6,4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6,4 CU2 | 6.4.622.9590 | ND |
| 6,4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6,4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6,4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6,4 CU6 | 6.4.658.9590 | ND |
| 6,4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6,4 CU8 | 6.4.670.9590 | ND |
| 6,5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6,5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6,5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6,5 CU3 | 6.5.664.9590 | 6.5.466.1 |
