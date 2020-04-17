---
title: Versioni del cluster supportate in Azure Service FabricSupported cluster versions in Azure Service Fabric
description: Informazioni sulle versioni del cluster in Azure Service Fabric, incluso un collegamento alle versioni più recenti del blog del team di Service Fabric.Learn about cluster versions in Azure Service Fabric, including a link to the newest releases from the Service Fabric team blog.
ms.topic: troubleshooting
ms.date: 03/02/2020
ms.openlocfilehash: b76e498926492ce7acaa696dfe9a0299fb5573e0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460529"
---
# <a name="supported-service-fabric-versions"></a>Versioni di Service Fabric supportate

Assicurarsi che il cluster esegua sempre una versione di Azure Service Fabric supportata. Un minimo di 60 giorni dopo aver annunciato il rilascio di una nuova versione di Service Fabric, il supporto per la versione precedente termina. Gli annunci delle nuove versioni sono disponibili nel blog del team di [Service Fabric](https://azure.microsoft.com/updates/?product=service-fabric).

Fare riferimento ai documenti seguenti per informazioni dettagliate su come mantenere il cluster che esegue una versione supportata di Service Fabric:Refer to the following documents for details on how to keep your cluster running a supported Service Fabric version:

- [Aggiornare un cluster di Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Aggiornare la versione di Service Fabric in esecuzione nel cluster autonomo di Windows ServerUpgrade the Service Fabric version that runs on your standalone Windows Server cluster](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Versioni supportate

Nella tabella seguente sono elencate le versioni di Service Fabric e le relative date di fine del supporto.

| Runtime di Service Fabric nel cluster | Possibilità di aggiornamento diretto dalla versione del cluster |Versione compatibile del pacchetto SDK o NuGet | Fine del supporto |
| --- | --- |--- | --- |
| Tutte le versioni del cluster precedenti alla 5.3.121 | 5.1.158.* |Versione 2.3 o precedente |20 gennaio 2017 |
| 5.3.* | 5.1.158.* |Versione 2.3 o precedente |24 febbraio 2017 |
| 5.4.* | 5.1.158.* |Versione 2.4 o precedente |10 maggio 2017       |
| 5.5.* | 5.4.164.* |Versione 2.5 o precedente |10 agosto 2017    |
| 5.6.* | 5.4.164.* |Versione 2.6 o precedente |13 ottobre 2017   |
| 5.7.* | 5.4.164.* |Versione 2.7 o precedente |15 dicembre 2017  |
| 6.0.* | 5.6.205.* |Versione 2.8 o precedente |martedì 30 marzo 2018     |
| 6.1.* | 5.7.221.* |Versione 3.0 o precedente |giovedì 15 luglio 2018      |
| 6.2.* | 6.0.232.* |Versione 3.1 o precedente |giovedì 26 ottobre 2018   |
| 6.3.* | 6.1.480.* |Versione 3.2 o precedente |giovedì 31 marzo 2019  |
| 6.4.* | 6.2.301.* |Versione 3.3 o precedente |giovedì 15 settembre 2019 |
| 6.5.* | 6.4.617.* |Minore o uguale alla versione 3.4 |1o agosto 2020 |
| 7.0.466.* | 6.4.664.* |Minore o uguale alla versione 4.0|1o agosto 2020  |
| 7.0.466.* | 6.5.* |Minore o uguale alla versione 4.0|1o agosto 2020 |
| 7.0.470.* | 7.0.466.* |Minore o uguale alla versione 4.0 |1o agosto 2020  |
| 7.1.409.* | 7.0.466.* |Minore o uguale alla versione 4.0 |Versione corrente, quindi nessuna data di fine |
## <a name="supported-operating-systems"></a>Sistemi operativi supportati

Nella tabella seguente sono elencati i sistemi operativi supportati per le versioni di Service Fabric supportate.

| Sistema operativo | Versione di Service Fabric supportata meno recente |
| --- | --- |
| Windows Server 2012 R2 | Tutte le versioni |
| Windows Server 2016 | Tutte le versioni |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 (in windows Server) | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

## <a name="supported-version-names"></a>Nomi di versione supportati

Nella tabella seguente sono elencati i nomi di versione di Service Fabric e i numeri di versione corrispondenti.

| Nome della versione | Numero di versione di Windows | Numero di versione Linux |
| --- | --- | --- |
| 5.3 RTO | 5.3.121.9494 | ND |
| 5.3 CU1 | 5.3.204.9494 | ND |
| 5.3 CU2 | 5.3.301.9590 | ND |
| 5.3 CU3 | 5.3.311.9590 | ND |
| 5.4 CU2 | 5.4.164.9494 | ND |
| 5,5 CU1 | 5.5.216.0    | ND |
| 5,5 CU2 | 5.5.219.0    | ND |
| 5,5 CU3 | 5.5.227.0    | ND |
| 5,5 CU4 | 5.5.232.0    | ND |
| 5.6 RTO | 5.6.204.9494 | ND |
| 5.6 CU2 (in inglese) | 5.6.210.9494 | ND |
| 5.6 CU3 | 5.6.220.9494 | ND |
| 5.7 RTO | 5.7.198.9494 | ND |
| 5,7 CU4 | 5.7.221.9494 | ND |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6.0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU3 | 6.1.472.9494 | ND |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6.4 CU2 | 6.4.622.9590 | ND |
| 6.4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 CU6 | 6.4.658.9590 | ND |
| 6.4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6.4 CU8 | 6.4.670.9590 | ND |
| 6.5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6,5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6,5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6,5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6,5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 7.0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 7.0 CU2 (in base al 2%1) | 7.0.464.9590 | 7.0.464.1 |
| 7.0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7.0 CU4 | 7.0.470.9590 | 7.0.469.1 |
