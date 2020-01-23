---
title: Indirizzi IP di Azure Integration Runtime
description: Informazioni sugli indirizzi IP per i quali è necessario consentire il traffico in ingresso, in modo da configurare correttamente i firewall per la protezione dell'accesso alla rete agli archivi dati.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: d64475ce524a3e859ed68a46552fedf30068f71d
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514783"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Indirizzi IP di Azure Integration Runtime

Gli indirizzi IP usati da Azure Integration Runtime dipendono dall'area in cui si trova il runtime di integrazione di Azure. *Tutto* I runtime di integrazione di Azure che si trovano nella stessa area utilizzano gli stessi intervalli di indirizzi IP.

> [!IMPORTANT]  
> I flussi di dati non utilizzano attualmente questi indirizzi IP. 
>
> È possibile usare questi intervalli IP per le esecuzioni di spostamento dati, pipeline ed attività esterne. Questi intervalli IP possono essere usati per l'inserimento nell'elenco elementi consentiti in archivi dati/gruppi di sicurezza di rete (NSG)/firewall per l'accesso in ingresso dal runtime di integrazione di Azure. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Indirizzi IP Azure Integration Runtime: aree specifiche

Consentire il traffico dagli indirizzi IP elencati per il runtime di integrazione di Azure nell'area specifica di Azure in cui si trovano le risorse:

|                | Area              | Indirizzi IP                                                 |
| -------------- | ------------------- | ------------------------------------------------------------ |
| Asia           | Asia orientale           | 20.189.104.128/25, </br>20.189.106.0/26, </br>13.75.39.112/28 |
| &nbsp;         | Asia sud-orientale      | 20.43.128.128/25, </br>20.43.130.0/26, </br>40.78.236.176/28 |
| Australia      | Australia orientale      | 20.37.193.0/25,</br>20.37.193.128/26,</br>13.70.74.144/28    |
| &nbsp;         | Australia sudorientale | 20.42.225.0/25,</br>20.42.225.128/26,</br>13.77.53.160/28    |
| Brasile         | Brasile meridionale        | 191.235.224.128/25,</br>191.235.225.0/26,</br>191.233.205.160/28 |
| Canada         | Canada centrale      | 52.228.80.128/25,</br>52.228.81.0/26,</br>13.71.175.80/28    |
| Europa         | Europa settentrionale        | 20.38.82.0/23,</br>20.38.80.192/26,</br>13.69.230.96/28      |
| &nbsp;         | Europa occidentale         | 40.74.26.0/23,</br>40.74.24.192/26,</br>13.69.67.192/28      |
| Francia         | Francia centrale      | 20.43.40.128/25,</br>20.43.41.0/26,</br>40.79.132.112/28     |
| India          | India centrale       | 52.140.104.128/25,</br>52.140.105.0/26,</br>20.43.121.48/28  |
| Giappone          | Giappone orientale          | 20.43.64.128/25,</br>20.43.65.0/26,</br>13.78.109.192/28     |
| Corea          | Corea centrale       | 20.41.64.128/25,</br>20.41.65.0/26,</br>52.231.20.64/28      |
| Sudafrica   | Sudafrica settentrionale  | 102.133.124.104/29,</br>102.133.216.128/25,</br>102.133.217.0/26 |
| Regno Unito | Regno Unito meridionale            | 51.104.24.128/25,</br>51.104.25.0/26,</br>51.104.9.32/28     |
| Stati Uniti  | Stati Uniti centrali          | 20.37.154.0/23,</br>20.37.156.0/26,</br>20.44.10.64/28       |
|                | Stati Uniti orientali             | 20.42.2.0/23,</br>20.42.4.0/26,</br>40.71.14.32/28           |
|                | Stati Uniti Orientali 2            | 20.41.2.0/23,</br>20.41.4.0/26,</br>20.44.17.80/28           |
|                | Stati Uniti orientali 2 EUAP      | 20.39.8.128/26,</br>20.39.8.96/27,</br>40.75.35.144/28       |
|                | Stati Uniti centro-settentrionali    | 40.80.185.0/25,</br>40.80.185.128/26,</br>52.162.111.48/28   |
|                | Stati Uniti centro-meridionali    | 40.119.9.0/25,</br>40.119.9.128/26,</br>13.73.244.32/28      |
|                | Stati Uniti centro-occidentali     | 52.150.137.128/25,</br>52.150.136.192/26,</br>13.71.199.0/28 |
|                | Stati Uniti occidentali             | 40.82.250.0/23,</br>40.82.249.64/26,</br>13.86.219.208/28    |
|                | Stati Uniti occidentali 2            | 20.42.132.0/23,</br>20.42.129.64/26,</br>13.66.143.128/28    |

## <a name="known-issue-with-azure-storage"></a>Problemi noti con archiviazione di Azure

* Quando ci si connette all'account di archiviazione di Azure, le regole di rete IP non hanno effetto sulle richieste provenienti dal runtime di integrazione di Azure nella stessa area dell'account di archiviazione. Per altri dettagli, [fare riferimento a questo articolo](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range). 

  Si consiglia invece di usare [Servizi attendibili durante la connessione ad archiviazione di Azure](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Passaggi successivi

* [Considerazioni sulla sicurezza per lo spostamento dei dati in Azure Data Factory](data-movement-security-considerations.md)
