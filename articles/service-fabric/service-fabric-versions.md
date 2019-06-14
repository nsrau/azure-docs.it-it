---
title: Le versioni dei cluster è supportato in Azure Service Fabric | Microsoft Docs
description: Informazioni sulle versioni dei cluster in Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/10/2019
ms.author: aljo
ms.openlocfilehash: 8a548bce6da8b3bae004b0be4f6e8f09c9894025
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060351"
---
# <a name="supported-service-fabric-versions"></a>Versioni di Service Fabric supportate

Assicurarsi che il cluster esegua sempre una versione di Azure Service Fabric supportata. Termina un minimo di 60 giorni dopo che Microsoft ne annuncia il rilascio di una nuova versione di Service Fabric, il supporto per la versione precedente. Sono disponibili gli annunci di nuove versioni nel [blog del team di Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

Vedere i documenti seguenti per informazioni dettagliate su come il cluster esegua una versione supportata di Service Fabric:

- [Aggiornare un cluster Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Aggiornare la versione di Service Fabric che viene eseguito nel cluster Windows Server autonomi](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Versioni supportate

Nella tabella seguente elenca le versioni di Service Fabric e le date di fine supporto.

| Runtime di Service Fabric nel cluster | Può eseguire l'aggiornamento direttamente dalla versione del cluster |Versione del pacchetto NuGet o SDK compatibili | Fine del supporto |
| --- | --- |--- | --- |
| Tutte le versioni precedenti alla 5.3.121 del cluster | 5.1.158.* |Versione 2.3 o precedente |20 gennaio 2017 |
| 5.3.* | 5.1.158.* |Versione 2.3 o precedente |24 febbraio 2017 |
| 5.4.* | 5.1.158.* |Versione 2.4 o precedente |10 maggio 2017       |
| 5.5.* | 5.4.164.* |Versione 2.5 o precedente |10 agosto 2017    |
| 5.6.* | 5.4.164.* |Versione 2.6 o precedente |13 ottobre 2017   |
| 5.7.* | 5.4.164.* |Versione 2.7 o precedente |15 dicembre 2017  |
| 6.0.* | 5.6.205.* |Versione 2.8 o precedente |30 marzo 2018     |
| 6.1.* | 5.7.221.* |Versione 3.0 o precedente |15 luglio 2018      |
| 6.2.* | 6.0.232.* |Versione 3.1 o precedente |26 ottobre 2018   |
| 6.3.* | 6.1.480.* |Versione 3.2 o precedente |31 marzo 2019  |
| 6.4.* | 6.2.301.* |Versione 3.3 o precedente |Dal 15 settembre 2019 |
| 6.5.* | 6.4.617.* |Minore o uguale alla versione 3.4 |La versione corrente, pertanto nessuna data di fine |

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

La tabella seguente elenca i sistemi operativi supportati per le versioni supportate di Service Fabric.

| Sistema operativo | Versione di Service Fabric più recente supportata |
| --- | --- |
| Windows Server 2012 R2 | Tutte le versioni |
| Windows Server 2016 | Tutte le versioni |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

## <a name="supported-version-names"></a>Nomi di versione supportata

Nella tabella seguente elenca i nomi di versione di Service Fabric e i relativi numeri di versione.

| Nome della versione | Numero di versione di Windows | Numero di versione di Linux |
| --- | --- | --- |
| 5.3 RTO | 5.3.121.9494 | NA |
| 5.3 CU1 | 5.3.204.9494 | NA |
| 5.3 CU2 | 5.3.301.9590 | NA |
| 5.3 CU3 | 5.3.311.9590 | NA |
| 5.4 CU2 | 5.4.164.9494 | NA |
| 5.5 CU1 | 5.5.216.0    | NA |
| 5.5 CU2 | 5.5.219.0    | NA |
| 5.5 CU3 | 5.5.227.0    | NA |
| 5.5 CU4 | 5.5.232.0    | NA |
| 5.6 RTO | 5.6.204.9494 | NA |
| 5.6 CU2 | 5.6.210.9494 | NA |
| 5.6 CU3 | 5.6.220.9494 | NA |
| 5.7 RTO | 5.7.198.9494 | NA |
| 5.7 CU4 | 5.7.221.9494 | NA |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6.0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU3 | 6.1.472.9494 | NA |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6.4 CU2 | 6.4.622.9590 | NA |
| 6.4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 CU6 | 6.4.658.9590 | NA |
| 6.4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6.5 RTO | 6.5.639.9590 | 6.5.435.1 |