---
title: Panoramica di Azure Data Factory Connector
description: Informazioni sui connettori supportati in Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 9b346c24b6a363e66e56f6c16b70058df3864c88
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945830"
---
# <a name="azure-data-factory-connector-overview"></a>Panoramica di Azure Data Factory Connector

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory supporta gli archivi dati e i formati seguenti tramite copia, flusso di dati, ricerca, Ottieni metadati ed Elimina attività. Fare clic su ogni archivio dati per ottenere informazioni sulle funzionalità supportate e sulle configurazioni corrispondenti.

## <a name="supported-data-stores"></a>Archivi dati supportati

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="integrate-with-more-data-stores"></a>Integrazione con più archivi dati

Azure Data Factory possibile raggiungere un set più ampio di archivi dati rispetto all'elenco menzionato in precedenza. Se è necessario spostare i dati da e verso un archivio dati che non si trova nell'elenco Azure Data Factory Connector incorporato, di seguito sono riportate alcune opzioni estendibili:
- Per database e data warehouse, in genere è possibile trovare un driver ODBC corrispondente, con cui è possibile usare il [connettore ODBC generico](connector-odbc.md).
- Per le applicazioni SaaS:
    - Se fornisce API RESTful, è possibile usare il [connettore Rest generico](connector-rest.md).
    - Se è presente un feed OData, è possibile usare il [connettore generico OData](connector-odata.md).
    - Se fornisce API SOAP, è possibile usare il [connettore http generico](connector-http.md).
    - Se è presente un driver ODBC, è possibile usare il [connettore ODBC generico](connector-odbc.md).
- Per gli altri, controllare se è possibile caricare o esporre dati in qualsiasi archivio dati supportato da ADF, ad esempio BLOB di Azure/file/FTP/SFTP e così via, quindi lasciare che ADF rilevi da questa posizione. È possibile richiamare un meccanismo personalizzato di caricamento dei dati tramite [funzione di Azure](control-flow-azure-function-activity.md), [attività personalizzata](transform-data-using-dotnet-custom-activity.md), [databricks](transform-data-databricks-notebook.md) / [HDInsight](transform-data-using-hadoop-hive.md), [attività Web](control-flow-web-activity.md)e così via.

## <a name="supported-file-formats"></a>Formati di file supportati

Azure Data Factory supporta i formati di file seguenti. Vedere ogni articolo per le impostazioni basate sul formato.

- [Formato Avro](format-avro.md)
- [Formato binario](format-binary.md)
- [Formato Common Data Model](format-common-data-model.md)
- [Formato di testo delimitato](format-delimited-text.md)
- [Formato delta](format-delta.md)
- [Formato Excel](format-excel.md)
- [Formato JSON](format-json.md)
- [Formato ORC](format-orc.md)
- [Formato Parquet](format-parquet.md)
- [Formato XML](format-xml.md)

## <a name="next-steps"></a>Passaggi successivi

- [Attività di copia](copy-activity-overview.md)
- [Flusso di dati per mapping](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Ottenere attività di metadati](control-flow-get-metadata-activity.md)
- [Eliminare attività](delete-activity.md)
