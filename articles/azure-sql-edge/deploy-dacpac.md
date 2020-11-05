---
title: Uso dei pacchetti DACPAC e BACPAC del database SQL-Azure SQL Edge
description: Informazioni sull'uso di dacpac e i file BACPAC in Azure SQL Edge
keywords: SQL Edge, SqlPackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 40bd0eda16f9f96dd356eef900369ab25854e9f9
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392249"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>Pacchetti DACPAC e BACPAC del database SQL in SQL Edge

SQL Edge di Azure un motore di database relazionale ottimizzato e progettato per distribuzioni IoT e perimetrali. Si basa sulle versioni più recenti di Microsoft SQL motore di database, che offre funzionalità leader del settore per le prestazioni, la sicurezza e l'elaborazione delle query. Oltre alle funzionalità leader del settore per la gestione dei database relazionali di SQL Server, SQL Edge di Azure offre funzionalità di streaming predefinite per l'analisi in tempo reale e l'elaborazione di eventi complessa.

Azure SQL Edge fornisce un meccanismo nativo che consente di distribuire un pacchetto [dacpac e BACPAC del database SQL](/sql/relational-databases/data-tier-applications/data-tier-applications) durante o dopo la distribuzione di SQL Edge.

I pacchetti DACPAC e BACPAC del database SQL possono essere distribuiti in SQL Edge usando la `MSSQL_PACKAGE` variabile di ambiente. La variabile di ambiente può essere configurata con uno dei seguenti elementi.  
- Percorso di una cartella locale all'interno del contenitore SQL contenente i file dacpac e BACPAC. È possibile eseguire il mapping di questa cartella a un volume host usando i punti di montaggio o i contenitori del volume di dati. 
- Un percorso di file locale nel mapping del contenitore SQL al file dacpac o BACPAC. Questo percorso di file può essere mappato a un volume host usando i punti di montaggio o i contenitori del volume di dati. 
- Un percorso di file locale all'interno del contenitore SQL che viene mappato a un file zip contenente i file dacpac o BACPAC. Questo percorso di file può essere mappato a un volume host usando i punti di montaggio o i contenitori del volume di dati. 
- Un URL di firma di accesso condiviso BLOB di Azure in un file zip contenente i file dacpac e BACPAC.
- Un URL di firma di accesso condiviso BLOB di Azure in un file dacpac o BACPAC. 

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>Usare un pacchetto di applicazione livello dati del database SQL con SQL Edge

Per distribuire o importare un pacchetto di applicazione livello dati del database SQL `(*.dacpac)` o un file BACPAC `(*.bacpac)` usando l'archiviazione BLOB di Azure e un file zip, seguire questa procedura. 

1. Creare/estrarre un pacchetto di applicazione livello dati o esportare un file BACPAC usando il meccanismo indicato di seguito. 
    - Creare o estrarre un pacchetto di applicazione livello dati del database SQL. Vedere [Estrazione di un'applicazione livello dati da un database](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) per informazioni su come generare un pacchetto di applicazione livello dati per un database di SQL Server esistente.
    - Esportazione di un pacchetto di applicazione livello dati distribuito o di un database. Vedere [esportare un'applicazione livello dati](/sql/relational-databases/data-tier-applications/export-a-data-tier-application/) per informazioni su come generare un file BACPAC per un database di SQL Server esistente.

2. Eseguire il Zip `*.dacpac` o il `*.bacpac` file e caricarlo in un account di archiviazione BLOB di Azure. Per altre informazioni sul caricamento di file nell'archiviazione BLOB di Azure, vedere [Caricare, scaricare ed elencare BLOB con il portale di Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Generare una firma di accesso condiviso per il file ZIP usando il portale di Azure. Per altre informazioni, vedere [Delegare l'accesso con firme di accesso condiviso](../storage/common/storage-sas-overview.md).

4. Aggiornare la configurazione del modulo SQL Edge per includere l'URI di accesso condiviso per il pacchetto di applicazione livello dati. Per aggiornare il modulo SQL Edge, seguire questa procedura:

    1. Nel portale di Azure passare alla distribuzione dell'hub IoT.

    2. Nel riquadro a sinistra selezionare **IoT Edge**.

    3. Nella pagina **IoT Edge** trovare e selezionare l'istanza di IoT Edge in cui è distribuito il modulo SQL Edge.

    4. Nella pagina **Dispositivo IoT Edge** selezionare **Imposta moduli**.

    5. Nella pagina **imposta moduli** fare clic sul modulo Azure SQL Edge.

    6. Nel riquadro **aggiorna IOT Edge modulo** selezionare variabili di **ambiente**. Aggiungere la `MSSQL_PACKAGE` variabile di ambiente e specificare l'URL SAS generato nel passaggio 3 precedente come valore per la variabile di ambiente. 

    7. Selezionare **Aggiorna**.

    8. Nella pagina **set Modules** selezionare **Review + create**.

    9. Nella pagina **set Modules** selezionare **create**.

5. Dopo l'aggiornamento del modulo, i file del pacchetto vengono scaricati, decompressi e distribuiti nell'istanza di SQL Edge.

A ogni riavvio del contenitore Edge di Azure SQL, SQL Edge tenta di scaricare il pacchetto di file compresso e di valutare le modifiche. Se viene rilevata una nuova versione del file dacpac, le modifiche vengono distribuite nel database in SQL Edge.

## <a name="known-issue"></a>Problema noto

Durante alcune distribuzioni di DACPAC o BACPAC, gli utenti possono riscontrare un timeout dei comandi, causando l'esito negativo dell'operazione di distribuzione dacpac. Se si verifica questo problema, usare la SQLPackage.exe (o gli strumenti client di SQL) per applicare DACPAC o BACPAC manualmente. 

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire SQL Edge tramite il portale di Azure](deploy-portal.md).
- [Trasmettere i dati](stream-data.md)
- [Machine Learning e intelligenza artificiale con ONNX in SQL Edge](onnx-overview.md)