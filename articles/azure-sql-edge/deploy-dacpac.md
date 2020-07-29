---
title: Uso di pacchetti di applicazione livello dati del database SQL - SQL Edge di Azure (anteprima)
description: Informazioni sull'uso dei pacchetti di applicazione livello dati in SQL Edge di Azure (anteprima)
keywords: SQL Edge, SqlPackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 0ddd1544c6a51ff1e2f98a28e40d9eb2ee0b47c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84233272"
---
# <a name="sql-database-dac-packages-in-sql-edge"></a>Pacchetti di applicazione livello dati del database SQL in SQL Edge

SQL Edge di Azure (anteprima) è un motore di database relazionale ottimizzato progettato per distribuzioni di IoT ed Edge. È basato sulle versioni più recenti del motore di database di Microsoft SQL Server, che offre funzionalità leader del settore per prestazioni, sicurezza ed elaborazione delle query. Oltre alle funzionalità leader del settore per la gestione dei database relazionali di SQL Server, SQL Edge di Azure offre funzionalità di streaming predefinite per l'analisi in tempo reale e l'elaborazione di eventi complessa.

SQL Edge di Azure fornisce anche un'implementazione nativa di SqlPackage.exe che consente di distribuire un [pacchetto di applicazione livello dati del database SQL](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) durante la distribuzione di SQL Edge. Per distribuire i pacchetti di applicazione livello dati del database SQL in SQL Edge, è possibile usare il parametro SqlPackage esposto tramite l'opzione `module twin's desired properties` del modulo SQL Edge:

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Campo | Descrizione |
|------|-------------|
| SqlPackage | URI dell'archiviazione BLOB di Azure per il file *.zip che contiene il pacchetto di applicazione livello dati del database SQL.
| ASAJobInfo | URI dell'archiviazione BLOB di Azure per il processo Edge di Analisi di flusso di Azure.

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>Usare un pacchetto di applicazione livello dati del database SQL con SQL Edge

Per usare un pacchetto di applicazione livello dati del database SQL (*.dacpac) con SQL Edge, seguire questa procedura:

1. Creare o estrarre un pacchetto di applicazione livello dati del database SQL. Vedere [Estrazione di un'applicazione livello dati da un database](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) per informazioni su come generare un pacchetto di applicazione livello dati per un database di SQL Server esistente.

2. Comprimere il file *.dacpac e caricarlo in un account di archiviazione BLOB di Azure. Per altre informazioni sul caricamento di file nell'archiviazione BLOB di Azure, vedere [Caricare, scaricare ed elencare BLOB con il portale di Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Generare una firma di accesso condiviso per il file ZIP usando il portale di Azure. Per altre informazioni, vedere [Delegare l'accesso con firme di accesso condiviso](../storage/common/storage-sas-overview.md).

4. Aggiornare la configurazione del modulo SQL Edge per includere l'URI di accesso condiviso per il pacchetto di applicazione livello dati. Per aggiornare il modulo SQL Edge, seguire questa procedura:

    1. Nel portale di Azure passare alla distribuzione dell'hub IoT.

    2. Nel riquadro a sinistra selezionare **IoT Edge**.

    3. Nella pagina **IoT Edge** trovare e selezionare l'istanza di IoT Edge in cui è distribuito il modulo SQL Edge.

    4. Nella pagina **Dispositivo IoT Edge** selezionare **Imposta moduli**.

    5. Nella pagina **Imposta moduli** selezionare **Configura** per il modulo SQL Edge.

    6. Nel riquadro **Moduli personalizzati IoT Edge** selezionare **Impostare le proprietà desiderate del modulo gemello**. Aggiornare le proprietà desiderate in modo da includere l'URI per l'opzione `SQLPackage`, come illustrato nell'esempio seguente.

        > [!NOTE]
        > L'URI della firma di accesso condiviso nel codice JSON seguente è solo un esempio. Sostituire l'URI con l'URI effettivo della distribuzione.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "<<<SAS URL for the *.zip file containing the dacpac",
                }
            }
        ```

    7. Selezionare **Salva**.

    8. Nella pagina **Imposta moduli** selezionare **Avanti**.

    9. Nella pagina **Imposta moduli** selezionare **Avanti** e quindi **Invia**.

5. Dopo l'aggiornamento del modulo, il file del pacchetto di applicazione livello dati viene scaricato, decompresso e distribuito nell'istanza di SQL Edge.

A ogni riavvio del contenitore SQL Edge di Azure, il pacchetto di file *.dacpac viene scaricato e valutato per verificare la presenza di modifiche. Se viene rilevata una nuova versione del file dacpac, le modifiche vengono distribuite nel database in SQL Edge.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire SQL Edge tramite il portale di Azure](deploy-portal.md).
- [Trasmettere i dati](stream-data.md)
- [Machine Learning e intelligenza artificiale con ONNX in SQL Edge (anteprima)](onnx-overview.md)
