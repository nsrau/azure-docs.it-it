---
title: Archivi dati supportati nella condivisione dati di Azure
description: Informazioni sugli archivi dati supportati per l'uso di condivisione dati di Azure.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 762cea6dce3e0c6be3f5e977c5f9de806ca0880e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516396"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Archivi dati supportati nella condivisione dati di Azure

La condivisione di dati di Azure offre una condivisione dei dati aperta e flessibile, inclusa la possibilità di condividere da e in archivi dati diversi. I provider di dati possono condividere dati da un tipo di archivio dati e i relativi consumer di dati possono scegliere l'archivio dati in cui ricevere i dati. 

Questo articolo illustra il set completo di archivi dati di Azure supportati nella condivisione dati di Azure. È inoltre possibile trovare informazioni sulle combinazioni di archivi dati che possono essere utilizzati da provider di dati e consumer di dati. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Quali archivi dati sono supportati nella condivisione dati di Azure? 

La tabella seguente illustra in dettaglio le origini dati supportate per la condivisione di dati di Azure. 

| Archivio dati | Condivisione basata su snapshot | Condivisione sul posto 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Archivio BLOB di Azure |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Database SQL di Azure |Anteprima pubblica | |
| Azure SQL Data Warehouse |Anteprima pubblica | |
| Esplora dati di Azure | |[Anteprima limitata](https://aka.ms/azuredatasharepreviewsignup) |

## <a name="data-store-support-matrix"></a>Matrice di supporto dell'archivio dati

La condivisione di dati di Azure offre la flessibilità dei consumer di dati per la scelta di un archivio dati in cui accettare i dati. Ad esempio, i dati condivisi dal database SQL di Azure possono essere ricevuti in Azure Data Lake Store Gen2, nel database SQL di Azure o in Azure SQL Data Warehouse. I clienti possono scegliere il formato in cui ricevere i dati durante la configurazione di una condivisione dati ricevuta. 

Nella tabella seguente vengono illustrate le diverse combinazioni e scelte che i consumer di dati hanno per l'accettazione e la configurazione della condivisione di dati. Per ulteriori informazioni su come configurare i mapping dei set di dati, vedere [come configurare i mapping dei set di dati](how-to-configure-mapping.md).

|  | Archiviazione BLOB di Azure | Azure SQL Data Lake Gen1 | Azure SQL Data Lake Gen2 | Database SQL di Azure | Azure SQL Data Warehouse 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Archivio BLOB di Azure |✓ ||✓|
| Azure Data Lake Storage Gen1 |✓ | |✓|
| Azure Data Lake Storage Gen2 |✓ | |✓|
| Database SQL di Azure |✓ | |✓|✓|✓|
| Azure SQL Data Warehouse |✓ | |✓|✓|✓|

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, procedere con l'esercitazione sulla [condivisione dei dati](share-your-data.md).
