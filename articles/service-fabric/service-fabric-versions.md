---
title: Informazioni sulle versioni cluster di Azure Service Fabric | Microsoft Docs
description: Versioni cluster di Azure Service Fabric supportate
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
ms.date: 9/24/2018
ms.author: aljo
ms.openlocfilehash: d99000e1682b662f4bceb28096395243c894282f
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681608"
---
# <a name="supported-service-fabric-versions"></a>Versioni di Service Fabric supportate

Verificare che il cluster esegua sempre una versione di Service Fabric supportata. Quando viene annunciato il rilascio di una nuova versione di Service Fabric, viene segnalato il termine del periodo di supporto per la versione precedente dopo un minimo di 60 giorni da tale data. Le nuove versioni vengono annunciate nel [blog del team di Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

Fare riferimento ai documenti seguenti per informazioni dettagliate su come assicurarsi che il cluster esegua una versione di Service Fabric supportata.

- [Aggiornare la versione di Service Fabric in un cluster di Azure](service-fabric-cluster-upgrade.md)
- [Aggiornare la versione di Service Fabric in un cluster autonomo in windows server](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Versioni supportate

La tabella seguente elenca le versioni di Service Fabric supportate e le date di fine supporto.

| **Runtime di Service Fabric nel cluster** | **Possibilità di aggiornamento diretto dalla versione del cluster** |**Versioni di SDK / pacchetto NuGet compatibili** | **Data di fine supporto** |
| --- | --- |--- | --- |
| Tutte le versioni di cluster precedenti alla 5.3.121 | 5.1.158* |Versione 2.3 o precedente |20 gennaio 2017 |
| 5.3.* | 5.1.158.* |Versione 2.3 o precedente |24 febbraio 2017 |
| 5.4.* | 5.1.158.* |Versione 2.4 o precedente |10 maggio 2017       |
| 5.5.* | 5.4.164.* |Versione 2.5 o precedente |10 agosto 2017    |
| 5.6.* | 5.4.164.* |Versione 2.6 o precedente |13 ottobre 2017   |
| 5.7.* | 5.4.164.* |Versione 2.7 o precedente |15 dicembre 2017  |
| 6.0.* | 5.6.205.* |Versione 2.8 o precedente |30 marzo 2018     |
| 6.1.* | 5.7.221.* |Versione 3.0 o precedente |15 luglio 2018      |
| 6.2.* | 6.0.232.* |Versione 3.1 o precedente |26 ottobre 2018   |
| 6.3.* | 6.1.480.* |Versione 3.2 o precedente |Marzo 31,2019  |
| 6.4.* | 6.2.301.* |Versione 3.3 o precedente |Versione corrente, nessuna data di fine supporto |

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

 La tabella seguente elenca i sistemi operativi supportati per le versioni supportate di Service Fabric.

| **Sistema operativo** | **Versione di meno recente supportata di Service Fabric** |
| --- | --- |
| Windows Server 2012 R2 | Tutte le versioni |
| Windows Server 2016 | Tutte le versioni |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

