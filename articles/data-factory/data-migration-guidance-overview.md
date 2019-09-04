---
title: Usare Azure Data Factory per migrare i dati dal data Lake e data warehouse ad Azure | Microsoft Docs
description: Usare Azure Data Factory per migrare i dati dal data Lake e data warehouse ad Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 7/30/2019
ms.openlocfilehash: 937a076b3e0e3c5170779d3449776f0aa1cf5b49
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258992"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Usare Azure Data Factory per migrare i dati dal data Lake o da data warehouse ad Azure 

Azure Data Factory può essere lo strumento per eseguire la migrazione dei dati quando si vuole eseguire la migrazione di data Lake o Enterprise data warehouse (EDW) in Azure. La migrazione di data Lake e data warehouse migrazione sono correlate agli scenari seguenti: 

- Migrazione di carichi di lavoro di Big data da AWS S3, file System Hadoop locale in Azure. 
- EDW migrazione da Oracle Exadata, Netezza, Teradata, AWS spostamento in Azure. 

Azure Data Factory possibile spostare i dati PBs per la migrazione di data Lake e decine di TB per la migrazione del data warehouse. 

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Motivi per cui è possibile usare Azure Data Factory per la migrazione dei dati 

- Azure Data Factory possibile aumentare facilmente la quantità di potenza per spostare i dati in modo senza server con prestazioni elevate, resilienza e scalabilità, pagando solo per le risorse usate.  
  - Azure Data Factory non prevede alcuna limitazione per il volume di dati e il numero di file.
  - Azure Data Factory possibile 100% usare la larghezza di banda di rete e di archiviazione per ottenere la massima velocità effettiva di spostamento dei dati nell'ambiente in uso.   
  - Azure Data Factory segue la strategia con pagamento in base al consumo, in modo che sia necessario pagare solo per il tempo in cui si usa Azure Data Factory per eseguire la migrazione dei dati in Azure.  
- Azure Data Factory è in grado di eseguire un carico cronologico monouso, nonché il carico incrementale pianificato. 
- Azure Data Factory USA Azure IR per lo stato di trasferimento dei dati tra endpoint data Lake/warehouse accessibili pubblicamente o in alternativa usare un runtime di integrazione self-hosted per lo trasferimento dei dati per gli endpoint data Lake/warehouse all'interno di VNet o dietro il firewall. 
- Azure Data Factory ha una sicurezza di livello aziendale: usare l'identità del servizio o l'identità del servizio per l'integrazione da servizio a servizio protetta o in alternativa sfruttare Azure Key Vault per la gestione delle credenziali. 
- Azure Data Factory offre un'esperienza di creazione senza codice e un dashboard di monitoraggio integrato completo.  

## <a name="online-vs-offline-data-migration"></a>Migrazione dei dati online e offline

Azure Data Factory è un tipico strumento di migrazione dei dati online per trasferire dati sulla rete (Internet, ER o VPN), in cui la migrazione dei dati offline consente agli utenti di inviare fisicamente i dispositivi di trasferimento dati dall'organizzazione al Data Center di Azure.  

Quando si seleziona l'approccio online rispetto alla migrazione offline, sono necessarie tre considerazioni principali:  

- Dimensioni dei dati da migrare. 
- Larghezza di banda di rete. 
- Finestra di migrazione.   

Per completare la migrazione dei dati entro due settimane (finestra di migrazione), è possibile visualizzare una linea taglia nell'immagine seguente per visualizzare quando è consigliabile usare lo strumento di migrazione in linea (Azure Data Factory) con dimensioni dei dati e larghezza di banda di rete diverse.   

![online rispetto a offline](media/data-migration-guidance-overview/online-offline.png)

> [!NOTE]
> Il vantaggio dell'approccio alla migrazione in linea è che è possibile eseguire il caricamento dei dati cronologici e i feed incrementali end-to-end di uno strumento.  In questo modo, i dati possono essere mantenuti sincronizzati tra l'archivio esistente e quello nuovo durante l'intera finestra di migrazione, in modo che sia possibile ricompilare la logica ETL nel nuovo archivio con i dati aggiornati. 


## <a name="next-steps"></a>Passaggi successivi

- [Migrare i dati da AWS S3 ad Azure](data-migration-guidance-s3-azure-storage.md)
- [Eseguire la migrazione dei dati dal cluster Hadoop locale ad Azure](data-migration-guidance-hdfs-azure-storage.md)
- [Eseguire la migrazione dei dati dal server Netezza locale ad Azure](data-migration-guidance-netezza-azure-sqldw.md)
