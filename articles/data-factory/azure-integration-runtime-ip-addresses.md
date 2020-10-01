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
ms.openlocfilehash: 55d8b5ebdfb226247f8a500f36e6df3ae02ea58a
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91619050"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Indirizzi IP di Azure Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gli indirizzi IP usati da Azure Integration Runtime dipendono dall'area in cui si trova il runtime di integrazione di Azure. *Tutto* I runtime di integrazione di Azure che si trovano nella stessa area utilizzano gli stessi intervalli di indirizzi IP.

> [!IMPORTANT]  
> I flussi di dati e Azure Integration Runtime che consentono la rete virtuale gestita non supportano l'uso di intervalli IP fissi.
>
> È possibile usare questi intervalli IP per le esecuzioni di spostamento dati, pipeline ed attività esterne. Questi intervalli IP possono essere usati per filtrare negli archivi dati/gruppi di sicurezza di rete (NSG)/firewall per l'accesso in ingresso dal runtime di integrazione di Azure. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Indirizzi IP Azure Integration Runtime: aree specifiche

Consentire il traffico dagli indirizzi IP elencati per il runtime di integrazione di Azure nell'area specifica di Azure in cui si trovano le risorse. È possibile ottenere un elenco di intervalli IP di tag del servizio dal [collegamento di download dell'intervallo IP dei tag del servizio](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files). Ad esempio, se l'area di Azure è **AustraliaEast**, è possibile ottenere un elenco di intervalli IP da **DataFactory. AustraliaEast**.


## <a name="known-issue-with-azure-storage"></a>Problemi noti con archiviazione di Azure

* Quando ci si connette all'account di archiviazione di Azure, le regole di rete IP non hanno effetto sulle richieste provenienti dal runtime di integrazione di Azure nella stessa area dell'account di archiviazione. Per altri dettagli, [fare riferimento a questo articolo](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range). 

  Si consiglia invece di usare [Servizi attendibili durante la connessione ad archiviazione di Azure](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Passaggi successivi

* [Considerazioni sulla sicurezza dello spostamento dei dati in Azure Data Factory](data-movement-security-considerations.md)
