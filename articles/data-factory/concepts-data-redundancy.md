---
title: Ridondanza dei dati in Azure Data Factory | Microsoft Docs
description: Informazioni sui meccanismi di ridondanza dei metadati in Azure Data Factory
services: data-factory
documentationcenter: ''
author: nabhishek
manager: weehyongtok
ms.reviewer: abnarain
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: abnarain
ms.openlocfilehash: 7f95adc264ed91e75eef668b43f674ddeb7d9e89
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350025"
---
# <a name="azure-data-factory-data-redundancy"></a>**Ridondanza dei dati Azure Data Factory**

I dati di Azure Data Factory includono metadati (pipeline, set di dati, servizi collegati, Runtime di integrazione e trigger) e dati di monitoraggio (pipeline, trigger e esecuzioni di attività). 

In tutte le aree (eccetto Brasile meridionale e Asia sud-orientale), i dati Azure Data Factory vengono archiviati e replicati nell' [area abbinata](../best-practices-availability-paired-regions.md#azure-regional-pairs) per proteggersi dalla perdita di metadati. Durante gli errori del Data Center a livello di area, Microsoft può avviare un failover a livello di area dell'istanza di Azure Data Factory. Nella maggior parte dei casi, non è richiesta alcuna azione da parte dell'utente. Al termine del failover gestito da Microsoft, sarà possibile accedere al Azure Data Factory nell'area di failover. 

A causa dei requisiti di residenza dei dati in Brasile meridionale e Asia sudorientale, i dati Azure Data Factory vengono archiviati [solo in aree locali](../storage/common/storage-redundancy.md#locally-redundant-storage). Per l'Asia sudorientale, tutti i dati vengono archiviati in Singapore. Per il Brasile meridionale, tutti i dati vengono archiviati in Brasile. Quando l'area viene persa a causa di un'emergenza significativa, Microsoft non sarà in grado di ripristinare i dati Azure Data Factory.  

> [!NOTE]
> Il failover gestito da Microsoft non si applica al runtime di integrazione self-hosted perché questa infrastruttura è in genere gestita dal cliente. Se la pagina è configurata nella macchina virtuale di Azure, è consigliabile usare [Azure Site Recovery](../site-recovery/site-recovery-overview.md) per gestire il [failover delle VM di Azure](../site-recovery/azure-to-azure-architecture.md) in un'altra area.



## <a name="using-source-control-in-azure-data-factory"></a>**Uso del controllo del codice sorgente in Azure Data Factory**

Per assicurarsi di poter tenere traccia e controllare le modifiche apportate ai metadati di Azure data factory, è consigliabile configurare il controllo del codice sorgente per l'Azure Data Factory. Consente inoltre di accedere ai file JSON dei metadati per pipeline, set di dati, servizi collegati e trigger. Azure Data Factory consente di usare un repository git diverso (Azure DevOps e GitHub). 

 Informazioni su come configurare il [controllo del codice sorgente in Azure Data Factory](./source-control.md). 

> [!NOTE]
> In caso di emergenza (perdita di area), è possibile eseguire il provisioning di nuovi data factory manualmente o in modo automatico. Una volta creato il nuovo data factory, è possibile ripristinare le pipeline, i set di DataSet e i servizi collegati JSON dal repository Git esistente. 



## <a name="data-stores"></a>**Archivi dati**

Azure Data Factory consente di spostare i dati tra archivi dati presenti in locale e nel cloud. Per garantire la continuità aziendale con gli archivi dati, è necessario fare riferimento alle raccomandazioni per la continuità aziendale per ognuno di questi archivi dati. 

 

## <a name="see-also"></a>Vedi anche

- [Coppie di aree di Azure](../best-practices-availability-paired-regions.md)
- [Residenza dei dati in Azure](https://azure.microsoft.com/global-infrastructure/data-residency/)