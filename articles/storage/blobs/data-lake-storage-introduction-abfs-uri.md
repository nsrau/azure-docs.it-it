---
title: Usare l'URI di Azure Data Lake Storage Gen2
description: Usare l'URI di Azure Data Lake Storage Gen2
services: storage
author: jamesbak
ms.topic: conceptual
ms.author: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: e4f148ac6f66cfbd7d89eff3623418197c2e0a33
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681183"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Usare l'URI di Azure Data Lake Storage Gen2

Il driver di [file system Hadoop](https://www.aosabook.org/en/hdfs.html) compatibile con Azure Data Lake Storage Gen2 è noto per l'identificatore di schema `abfs` (file system del BLOB di Azure). Coerente con altri driver di file system Hadoop, il driver ABFS usa un formato URI per indirizzare file e directory all'interno di un account che supporta Data Lake Storage Gen2.

## <a name="uri-syntax"></a>Sintassi dell'URI

La sintassi dell'URI per Data Lake Storage Gen2 dipende dal fatto che l'account di archiviazione è configurato per avere Data Lake Storage Gen2 come file system predefinito.

Se l'account con supporto per Data Lake Storage Gen2 che si vuole indirizzare **non è** impostato come file system predefinito durante la creazione dell'account, la sintassi abbreviata dell'URI è:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identificatore di schema**: il protocollo `abfs` viene usato come identificatore di schema. È possibile connettersi con o senza una connessione Secure Sockets Layer o SSL. Usare `abfss` per connettersi con una connessione Secure Sockets Layer.

2. **File system**: il percorso padre che contiene i file e le cartelle. È uguale a Contenitori nel servizio BLOB di Archiviazione di Azure.

3. **Nome dell'account**: il nome assegnato all'account di archiviazione durante la creazione.

4. **Percorso**: una rappresentazione della struttura della directory delimita da una barra (`/`).

5. **Nome del file**: il nome del singolo file. Questo parametro è facoltativo se si fa riferimento a una directory.

Se, tuttavia, l'account che si vuole indirizzare è impostato come file system predefinito durante la creazione dell'account, la sintassi abbreviata dell'URI è:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Percorso**: una rappresentazione della struttura della directory delimita da una barra (`/`).

2. **Nome del file**: il nome del singolo file.


## <a name="next-steps"></a>Passaggi successivi

- [Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
