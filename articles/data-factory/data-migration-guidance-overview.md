---
title: Usare Azure Data Factory per migrare i dati dal data Lake e data warehouse ad Azure
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
ms.openlocfilehash: 0be9cbc9c5af2e0778654ef70c5350b48f10c35d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73675767"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Usare Azure Data Factory per migrare i dati dal data Lake o da data warehouse ad Azure

Se si desidera eseguire la migrazione di data Lake o Enterprise data warehouse (EDW) a Microsoft Azure, è consigliabile utilizzare Azure Data Factory. Azure Data Factory è particolarmente adatto agli scenari seguenti:

- Migrazione di carichi di lavoro di Big data dal servizio di archiviazione semplice di Amazon (Amazon S3) o da un Hadoop Distributed File System locale (HDFS) ad Azure
- Migrazione di EDW da Oracle Exadata, Netezza, Teradata o Amazon per lo spostamento in Azure

Azure Data Factory possibile spostare i dati petabyte (PB) per la migrazione di data Lake e decine di terabyte (TB) di dati per la migrazione data warehouse.

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Motivi per cui è possibile usare Azure Data Factory per la migrazione dei dati

- Azure Data Factory possibile aumentare facilmente la quantità di potenza di elaborazione per spostare i dati in modo senza server con prestazioni elevate, resilienza e scalabilità. Paghi solo per ciò che usi. Si noti inoltre quanto segue: 
  - Azure Data Factory non presenta limitazioni per il volume di dati o per il numero di file.
  - Azure Data Factory possibile usare completamente la larghezza di banda di rete e di archiviazione per ottenere il volume più elevato di velocità effettiva di spostamento dei dati nell'ambiente.
  - Azure Data Factory usa un metodo con pagamento in base al consumo, in modo da pagare solo il tempo usato per eseguire la migrazione dei dati in Azure.  
- Azure Data Factory possibile eseguire un carico cronologico unico e i caricamenti incrementali pianificati.
- Azure Data Factory usa il runtime di integrazione di Azure (IR) per spostare i dati tra gli endpoint di data Lake e warehouse accessibili pubblicamente. Può anche usare il runtime di integrazione self-hosted per lo stato di trasferimento dei dati per gli endpoint data Lake e warehouse nella rete virtuale di Azure (VNet) o dietro un firewall.
- Azure Data Factory dispone di sicurezza di livello aziendale: è possibile utilizzare Windows Installer (MSI) o l'identità del servizio per l'integrazione da servizio a servizio protetta oppure utilizzare Azure Key Vault per la gestione delle credenziali.
- Azure Data Factory offre un'esperienza di creazione senza codice e un dashboard di monitoraggio completo e integrato.  

## <a name="online-vs-offline-data-migration"></a>Migrazione dei dati online e offline

Azure Data Factory è uno strumento di migrazione dei dati online standard per trasferire i dati in una rete (Internet, ER o VPN). Mentre con la migrazione dei dati offline, gli utenti inviano fisicamente i dispositivi di trasferimento dei dati dalla propria organizzazione a un Data Center di Azure.  

Quando si sceglie un approccio di migrazione online e offline, sono necessarie tre considerazioni principali:  

- Dimensioni dei dati da migrare
- Larghezza di banda della rete
- Finestra di migrazione

Si supponga, ad esempio, di pianificare l'uso di Azure Data Factory per completare la migrazione dei dati entro due settimane (la *finestra di migrazione*). Si noti la linea di taglio rosa/blu nella tabella seguente. La cella rosa più bassa per una determinata colonna Mostra le dimensioni dei dati e l'associazione della larghezza di banda di rete la cui finestra di migrazione è più vicina a ma meno di due settimane. (Qualsiasi associazione di dimensioni/larghezza di banda in una cella blu ha una finestra di migrazione in linea di più di due settimane). 

![online e offline](media/data-migration-guidance-overview/online-offline.png) questa tabella consente di determinare se è possibile soddisfare la finestra di migrazione desiderata tramite la migrazione in linea (Azure Data Factory) in base alle dimensioni dei dati e alla larghezza di banda di rete disponibile. Se la finestra di migrazione in linea è più di due settimane, è consigliabile usare la migrazione offline.

> [!NOTE]
> Grazie alla migrazione online, è possibile eseguire il caricamento dei dati cronologici e i feed incrementali end-to-end tramite un singolo strumento.  Con questo approccio, i dati possono essere mantenuti sincronizzati tra l'archivio esistente e il nuovo archivio durante l'intera finestra di migrazione. Ciò significa che è possibile ricompilare la logica ETL nel nuovo archivio con i dati aggiornati.


## <a name="next-steps"></a>Passaggi successivi

- [Migrare i dati da AWS S3 ad Azure](data-migration-guidance-s3-azure-storage.md)
- [Eseguire la migrazione dei dati dal cluster Hadoop locale ad Azure](data-migration-guidance-hdfs-azure-storage.md)
- [Eseguire la migrazione dei dati dal server Netezza locale ad Azure](data-migration-guidance-netezza-azure-sqldw.md)
