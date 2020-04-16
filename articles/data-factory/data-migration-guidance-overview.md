---
title: Eseguire la migrazione dei dati dal data lake e dal data warehouse in AzureMigrate data from data lake and data warehouse to Azure
description: Usare Azure Data Factory per eseguire la migrazione dei dati dal data lake e dal data warehouse ad Azure.Use Azure Data Factory to migrate data from your data lake and data warehouse to Azure.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: 4408546c892299e5bbbc22b00a4b334c36eda616
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416437"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Usare Azure Data Factory per eseguire la migrazione dei dati dal data lake o dal data warehouse ad AzureUse Azure Data Factory to migrate data from your data lake or data warehouse to Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Se si vuole eseguire la migrazione del data lake o del data warehouse aziendale (EDW) a Microsoft Azure, è consigliabile usare Azure Data Factory.If you want to migrate your data lake or enterprise data warehouse (EDW) to Microsoft Azure, consider using Azure Data Factory. Azure Data Factory è adatto agli scenari seguenti:Azure Data Factory is well-suited to the following scenarios:

- Migrazione del carico di lavoro dei Big Data da Amazon Simple Storage Service (Amazon S3) o un file system distribuito Hadoop (HDFS) locale ad Azure
- Migrazione EDW da Oracle Exadata, Netezza, Teradata o Amazon Redshift ad Azure

Azure Data Factory può spostare petabyte (PB) di dati per la migrazione di data lake e decine di terabyte (TB) di dati per la migrazione del data warehouse.

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Perché è possibile usare Azure Data Factory per la migrazione dei datiWhy Azure Data Factory can be used for data migration

- Azure Data Factory può facilmente aumentare la quantità di potenza di elaborazione per spostare i dati senza server con prestazioni elevate, resilienza e scalabilità. E si paga solo per quello che si utilizza. Si noti inoltre quanto segue: 
  - Azure Data Factory non ha limitazioni sul volume di dati o sul numero di file.
  - Azure Data Factory può usare completamente la larghezza di banda di rete e archiviazione per ottenere il massimo volume di velocità effettiva di spostamento dei dati nell'ambiente.
  - Azure Data Factory uses a pay-as-you-go method, so that you pay only for the time you actually use to run the data migration to Azure.  
- Azure Data Factory può eseguire sia un caricamento cronologico una tantera che un caricamento incrementale pianificato.
- Azure Data Factory usa il runtime di integrazione di Azure per spostare i dati tra gli endpoint data lake e warehouse accessibili pubblicamente. Può anche usare il controllo di accesso a motore self-hosted per spostare i dati per gli endpoint data lake e warehouse all'interno di Rete virtuale di Azure o dietro un firewall.
- Azure Data Factory ha una sicurezza di livello enterprise: è possibile usare Windows Installer (MSI) o l'identità del servizio per l'integrazione protetta da servizio a servizio oppure usare l'insieme di credenziali delle chiavi di Azure per la gestione delle credenziali.
- Azure Data Factory offre un'esperienza di creazione e-modifica senza codice e un dashboard di monitoraggio completo e integrato.  

## <a name="online-vs-offline-data-migration"></a>Migrazione dei dati online e offline

Azure Data Factory è uno strumento di migrazione dei dati online standard per trasferire dati in rete (Internet, ER o VPN). Mentre con la migrazione dei dati offline, gli utenti spedisco fisicamente i dispositivi di trasferimento dei dati dall'organizzazione a un data center di Azure.As with offline data migration, users physically ship data-transfer devices from their organization to an Azure Data Center.  

Quando si sceglie tra un approccio di migrazione online e offline, è necessario fare tre considerazioni principali:There are three key considerations when you choose between an online and offline migration approach:  

- Dimensioni dei dati da migrare
- Larghezza di banda della rete
- Finestra di migrazione

Si supponga, ad esempio, di voler usare Azure Data Factory per completare la migrazione dei dati entro due settimane (finestra di *migrazione).* Si noti la linea di taglio rosa/blu nella tabella seguente. La cella rosa più bassa per una determinata colonna mostra l'associazione dimensione dei dati/larghezza di banda di rete la cui finestra di migrazione è più vicina ma inferiore a due settimane. (Qualsiasi associazione di dimensioni/larghezza di banda in una cella blu ha una finestra di migrazione online di più di due settimane.) 

![Online vs](media/data-migration-guidance-overview/online-offline.png) offline Questa tabella consente di determinare se è possibile soddisfare la finestra di migrazione desiderata tramite la migrazione online (Azure Data Factory) in base alle dimensioni dei dati e alla larghezza di banda di rete disponibile. Se la finestra di migrazione online è più di due settimane, è consigliabile utilizzare la migrazione offline.

> [!NOTE]
> Utilizzando la migrazione online, è possibile ottenere sia il caricamento dei dati cronologici che i feed incrementali end-to-end tramite un unico strumento.  Tramite questo approccio, i dati possono essere mantenuti sincronizzati tra l'archivio esistente e il nuovo archivio durante l'intera finestra di migrazione. Ciò significa che è possibile ricostruire la logica ETL nel nuovo archivio con i dati aggiornati.


## <a name="next-steps"></a>Passaggi successivi

- [Eseguire la migrazione dei dati da AWS S3 ad Azure](data-migration-guidance-s3-azure-storage.md)
- [Eseguire la migrazione dei dati dal cluster hadoop locale in AzureMigrate data from on-premises hadoop cluster to Azure](data-migration-guidance-hdfs-azure-storage.md)
- [Eseguire la migrazione dei dati da un server Netezza locale ad Azure](data-migration-guidance-netezza-azure-sqldw.md)
