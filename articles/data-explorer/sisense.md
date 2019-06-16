---
title: Visualizzare i dati da Esplora dati di Azure con Sisense
description: In questo articolo, informazioni su come configurare Esplora dati di Azure come origine dati per Sisense e visualizzare i dati.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358184"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Visualizzare i dati da Esplora dati di Azure in Sisense

Sisense è una piattaforma analitica business intelligence che ti permette di creare applicazioni di analitica che offrono esperienze utente altamente interattiva. La business intelligence e dashboard report software consente di accedere e combinare i dati in pochi clic. È possibile connettersi ai dati strutturati e non strutturati origini, join di tabelle da più origini con scripting minima e la codifica e creare report e dashboard interattivi di web. In questo articolo si apprenderà come configurare Esplora dati di Azure come origine dati per Sisense e visualizzare i dati da un cluster di esempio.

## <a name="prerequisites"></a>Prerequisiti

È necessario quanto segue per completare questo articolo:

* [Scaricare e installare app Sisense](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* Creare un cluster e database che include i dati di esempio StormEvents. Per altre informazioni, vedere [Avvio rapido: Creare un database e un cluster di Esplora dati di Azure](create-cluster-database-portal.md) e [Inserire i dati di esempio in Esplora dati di Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Connettersi ai dashboard Sisense usando connettore JDBC Esplora dati di Azure

1. Scaricare e copiare le versioni più recenti dei file jar seguenti a *... \Sisense\DataConnectors\jdbcdrivers\adx* 

    * attivazione 1.1.jar
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-collections4-4.1.jar
    * commons-lang3-3.5.jar
    * gson-2.8.0.jar
    * jcip-annotations-1.0-1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * mail-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. Aprire **Sisense** app.
1. Selezionare **Data** scheda e selezionare **+ ElastiCube** per creare un nuovo modello ElastiCube.
    
    ![Selezionare ElastiCube](media/sisense/data-select-elasticube.png)

1. Nelle **Aggiungi nuovo modello ElastiCube**, denominare il modello ElastiCube e **salvare**.
   
    ![Aggiungi nuovo modello ElastiCube](media/sisense/add-new-elasticube-model.png)

1. Selezionare **+ dati**.

    ![Fare clic sul pulsante dei dati](media/sisense/select-data.png)

1. Nelle **selezionare connettore** scheda, selezionare **JDBC generico** connector.

    ![Scegliere il connettore JDBC](media/sisense/select-connector.png)

1. Nel **Connect** scheda, completare i campi seguenti per il **JDBC generico** connettore e selezionare **Next**.

    ![Impostazioni del connettore JDBC](media/sisense/jdbc-connector.png)

    |Campo |Descrizione |
    |---------|---------|
    |Connection String     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |Cartella di file JAR JDBC  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |Nome della classe del driver    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |User Name   |    Nome utente AAD     |
    |Password     |   Password utente AAD      |

1. Nel **selezione dati del** scheda, cercare **seleziona Database** per selezionare il database corrispondente a cui si dispone delle autorizzazioni. In questo esempio, selezionare *test1*.

    ![Selezionare il database](media/sisense/select-database.png)

1. Nelle *testare* riquadro (nome database):
    1. Selezionare il nome della tabella per visualizzare l'anteprima della tabella e visualizzare i nomi delle colonne di tabella. È possibile rimuovere le colonne non necessarie.
    1. Selezionare la casella di controllo della tabella rilevante per selezionare tale tabella. 
    1. Selezionare **Operazione completata**.

    ![Selezionare una tabella](media/sisense/select-table-see-columns.png)    

1. Selezionare **compilazione** per compilare il set di dati. 

    * Nel **compilare** finestra, seleziona **compilazione**.

      ![Finestra di compilazione](media/sisense/build-window.png)

    * Attendere il processo di compilazione è completo e quindi selezionare **compilazione riuscita**.

      ![Compilazione completata](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Creare dashboard Sisense

1. Nelle **Analitica** scheda, seleziona **+**  >  **nuovo Dashboard** a creare dashboard con questo set di dati.

    ![Nuovo dashboard](media/sisense/new-dashboard.png)

1. Scegliere un dashboard e selezionare **Create**. 

    ![Creare un dashboard](media/sisense/create-dashboard.png)

1. Sotto **nuovo Widget**, selezionare **+ seleziona i dati** per creare un nuovo widget. 

    ![Aggiungere campi al dashboard StormEvents](media/sisense/storm-dashboard-add-field.png)  

1. Selezionare **+ aggiungere più dati** per aggiungere altre colonne al grafo. 

    ![Aggiungere altri dati a grafo](media/sisense/add-more-data.png)

1. Selezionare **+ Widget** per creare un altro widget. Trascinare e rilasciare i widget per ridisporre i dashboard.

    ![Storm Dashboard](media/sisense/final-dashboard.png)

È ora possibile esplorare i dati con visual analitica, compilare dei dashboard aggiuntive e trasformare i dati in analisi approfondite per avere un impatto sull'azienda.

## <a name="next-steps"></a>Passaggi successivi

* [Scrivere query per Esplora dati di Azure](write-queries.md)

