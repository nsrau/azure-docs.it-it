---
title: Caricare dati da SQL Server in Azure SQL Data Warehouse (SSIS) | Documentazione Microsoft
description: Illustra come creare un pacchetto di SQL Server Integration Services (SSIS) per spostare dati da una vasta gamma di origini dati in SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: e2c252e9-0828-47c2-a808-e3bea46c134a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 03/30/2017
ms.author: cakarst;douglasl;barbkess
ms.openlocfilehash: 6c9cebdd715b6997d0633bc725a3945ba9e0c357
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-ssis"></a>Caricare dati da SQL Server in Azure SQL Data Warehouse (SSIS)
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

Creare un pacchetto di SQL Server Integration Services (SSIS) per caricare i dati da SQL Sever ad Azure SQL Data Warehouse. È possibile, facoltativamente, ristrutturare, trasformare e pulire i dati man mano che passano attraverso il flusso di dati SSIS.

In questa esercitazione si apprenderà come:

* Creare un nuovo progetto di Integration Services in Visual Studio.
* Connettersi a origini dati, inclusi SQL Server come origine e SQL Data Warehouse come destinazione.
* Progettare un pacchetto SSIS che carica i dati dall'origine nella destinazione.
* Eseguire il pacchetto SSIS per caricare i dati.

Questa esercitazione usa SQL Server come origine dati. SQL Server può essere eseguito in locale o in una macchina virtuale di Azure.

## <a name="basic-concepts"></a>Concetti di base
Il pacchetto è l'unità di lavoro in SSIS. I pacchetti correlati sono raggruppati in progetti. Progetti e pacchetti di progettazione vengono creati in Visual Studio con SQL Server Data Tools. Il processo di progettazione è di tipo visivo e consente di trascinare la selezione dei componenti dalla Casella degli strumenti all'area di progettazione, connettere tali componenti e impostare le relative proprietà. Dopo aver completato il pacchetto, è possibile distribuirlo facoltativamente in SQL Server per la gestione, la sicurezza e il monitoraggio completi.

## <a name="options-for-loading-data-with-ssis"></a>Opzioni di caricamento dei dati con SSIS
SQL Server Integration Services (SSIS) è un set di strumenti flessibile che offre un'ampia gamma di opzioni per la connessione e il caricamento dei dati in SQL Data Warehouse.

1. Usare una destinazione ADO.NET per connettersi a SQL Data Warehouse. Questa esercitazione usa una destinazione ADO.NET perché include il minor numero di opzioni di configurazione.
2. Usare una destinazione OLE DB per connettersi a SQL Data Warehouse. Questa opzione può fornire prestazioni leggermente migliori rispetto alla destinazione ADO.NET.
3. Usare l'attività di caricamento BLOB di Azure per inserire i dati nell'archivio BLOB di Azure. Usare quindi l'attività Esegui SQL di SSIS per avviare uno script di Polybase che carica i dati in SQL Data Warehouse. Delle tre opzioni elencate qui, questa è quella che offre le prestazioni migliori. Per ottenere l'attività di caricamento BLOB di Azure, scaricare [Microsoft SQL Server 2016 Integration Services Feature Pack for Azure][Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]. Per altre informazioni su Polybase, vedere la [Guida di PolyBase][PolyBase Guide].

## <a name="before-you-start"></a>Prima di iniziare
Per eseguire questa esercitazione, è necessario:

1. **SQL Server Integration Services (SSIS)**. SSIS è un componente di SQL Server e richiede una versione di valutazione o una versione con licenza di SQL Server. Per ottenere una versione di valutazione di SQL Server 2016 Preview, vedere la pagina delle [versioni di valutazione di SQL Server][SQL Server Evaluations].
2. **Visual Studio** Per ottenere la versione gratuita di Visual Studio Community Edition, vedere [Visual Studio Community][Visual Studio Community].
3. **SQL Server Data Tools per Visual Studio (SSDT)**. Per ottenere SQL Server Data Tools per Visual Studio, vedere [Scaricare SQL Server Data Tools (SSDT)][Download SQL Server Data Tools (SSDT)].
4. **Dati di esempio**. Questa esercitazione usa dati di esempio, archiviati nel database di esempio AdventureWorks in SQL Server, come dati di origine da caricare in SQL Data Warehouse. Per ottenere il database di esempio AdventureWorks, vedere la pagina dei [database di esempio AdventureWorks 2014][AdventureWorks 2014 Sample Databases].
5. **Database di SQL Data Warehouse e relative autorizzazioni**. Questa esercitazione si connette a un'istanza di SQL Data Warehouse e vi carica i dati. È necessario avere le autorizzazioni per creare una tabella e caricare i dati.
6. **Regola del firewall**. Per poter caricare dati in SQL Data Warehouse, è necessario creare una regola del firewall in SQL Data Warehouse con l'indirizzo IP del computer locale.

## <a name="step-1-create-a-new-integration-services-project"></a>Passaggio 1: Creare un nuovo progetto di Integration Services
1. Avviare Visual Studio.
2. Scegliere **Nuovo | Progetto** dal menu **File**.
3. Passare ai tipi di progetto **Installati | Modelli | Business Intelligence | Integration Services** .
4. Selezionare **Progetto di Integration Services**. Specificare i valori per **Nome** e **Percorso** e quindi scegliere **OK**.

Visual Studio viene aperto e crea un nuovo progetto di Integration Services (SSIS). Visual Studio apre quindi la finestra di progettazione per il nuovo pacchetto SSIS singolo (package.dtsx) nel progetto. Vengono visualizzate le aree dello schermo seguenti:

* A sinistra la **Casella degli strumenti** dei componenti SSIS.
* Al centro l'area di progettazione con più schede. In genere si usano almeno le schede **Flusso di controllo** e **Flusso di dati**.
* A destra i riquadri **Esplora soluzioni** e **Proprietà**.
  
    ![][01]

## <a name="step-2-create-the-basic-data-flow"></a>Passaggio 2: Creare il flusso di dati di base
1. Trascinare un'attività Flusso di dati dalla Casella degli strumenti al centro dell'area di progettazione, nella scheda **Flusso di controllo** .
   
    ![][02]
2. Fare doppio clic sull'attività Flusso di dati per passare alla scheda Flusso di dati.
3. Dall'elenco Altre origini nella Casella degli strumenti trascinare un'origine ADO.NET nell'area di progettazione. Con l'adattatore di origine ancora selezionato modificare il nome in **Origine SQL Server** nel riquadro **Proprietà**.
4. Dall'elenco Altre destinazioni nella Casella degli strumenti trascinare una destinazione ADO.NET nell'area di progettazione sotto l'origine ADO.NET. Con l'adattatore di destinazione ancora selezionato modificare il nome in **Destinazione SQL DW** nel riquadro **Proprietà**.
   
    ![][09]

## <a name="step-3-configure-the-source-adapter"></a>Passaggio 3: Configurare l'adattatore di origine
1. Fare doppio clic sull'adattatore di origine per aprire la **Editor origine ADO.NET**.
   
    ![][03]
2. Nella scheda **Gestione connessione** di **Editor origine ADO.NET** fare clic sul pulsante **Nuovo** accanto all'elenco **Gestione connessione ADO.NET** per aprire la finestra di dialogo **Configura gestione connessione ADO.NET** e creare le impostazioni di connessione per il database SQL Server da cui questa esercitazione carica i dati.
   
    ![][04]
3. Nella finestra di dialogo **Configura gestione connessione ADO.NET** fare clic sul pulsante **Nuovo** per aprire la finestra di dialogo **Gestione connessione** e creare una nuova connessione dati.
   
    ![][05]
4. Nella finestra di dialogo **Gestione connessione** eseguire queste operazioni.
   
   1. Per **Provider**selezionare il provider di dati SqlClient.
   2. Per **Nome server**immettere il nome di SQL Server.
   3. Nella sezione **Accesso al server** selezionare o immettere le informazioni di autenticazione.
   4. Nella sezione **Connessione al database** selezionare il database di esempio AdventureWorks.
   5. Fare clic su **Test connessione**.
      
       ![][06]
   6. Nella finestra di dialogo che segnala i risultati del test di connessione fare clic su **OK** per tornare alla finestra di dialogo **Gestione connessione**.
   7. Nella finestra di dialogo **Gestione connessione** fare clic su **OK** per tornare alla finestra di dialogo **Configura gestione connessione ADO.NET**.
5. Nella finestra di dialogo **Configura gestione connessione ADO.NET** fare clic su **OK** per tornare a **Editor origine ADO.NET**.
6. Nell'elenco **Nome della tabella o Nome della vista** in **Editor origine ADO.NET** selezionare la tabella **Sales.SalesOrderDetail**.
   
    ![][07]
7. Fare clic su **Anteprima** per visualizzare le prime 200 righe di dati nella tabella di origine nella finestra di dialogo **Anteprima risultati query**.
   
    ![][08]
8. Nella finestra di dialogo **Anteprima risultati query** fare clic su **Chiudi** per tornare a **Editor origine ADO.NET**.
9. In **Editor origine ADO.NET** fare clic su **OK** per completare la configurazione dell'origine dati.

## <a name="step-4-connect-the-source-adapter-to-the-destination-adapter"></a>Passaggio 4: Connettere l'adattatore di origine all'adattatore di destinazione
1. Selezionare l'adattatore di origine nell'area di progettazione.
2. Selezionare la freccia blu che si estende dall'adattatore di origine e trascinarla nell'editor di destinazione finché non si ancora.
   
    ![][10]
   
    In un pacchetto SSIS tipico si usano molti altri componenti dalla Casella degli strumenti di SSIS tra l'origine e destinazione per ristrutturare, trasformare e pulire i dati man mano che passano attraverso il flusso di dati SSIS. Per mantenere questo esempio il più semplice possibile, l'origine viene connessa direttamente alla destinazione.

## <a name="step-5-configure-the-destination-adapter"></a>Passaggio 5: Configurare l'adattatore di destinazione
1. Fare doppio clic sull'adattatore di destinazione per aprire **Editor destinazione ADO.NET**.
   
    ![][11]
2. Nella scheda **Gestione connessione** di **Editor destinazione ADO.NET** fare clic sul pulsante **Nuovo** accanto all'elenco **Gestione connessione** per aprire la finestra di dialogo **Configura gestione connessione ADO.NET** e creare le impostazioni di connessione per il database di Azure SQL Data Warehouse in cui questa esercitazione carica i dati.
3. Nella finestra di dialogo **Configura gestione connessione ADO.NET** fare clic sul pulsante **Nuovo** per aprire la finestra di dialogo **Gestione connessione** e creare una nuova connessione dati.
4. Nella finestra di dialogo **Gestione connessione** eseguire queste operazioni.
   1. Per **Provider**selezionare il provider di dati SqlClient.
   2. Per **Nome server**immettere il nome di SQL Data Warehouse.
   3. Nella sezione **Accesso al server** selezionare **Usa autenticazione di SQL Server** e immettere le informazioni di autenticazione.
   4. Nella sezione **Connessione al database** selezionare un database di SQL Data Warehouse esistente.
   5. Fare clic su **Test connessione**.
   6. Nella finestra di dialogo che segnala i risultati del test di connessione fare clic su **OK** per tornare alla finestra di dialogo **Gestione connessione**.
   7. Nella finestra di dialogo **Gestione connessione** fare clic su **OK** per tornare alla finestra di dialogo **Configura gestione connessione ADO.NET**.
5. Nella finestra di dialogo **Configura gestione connessione ADO.NET** fare clic su **OK** per tornare a **Editor destinazione ADO.NET**.
6. In **Editor destinazione ADO.NET** fare clic su **Nuovo** accanto all'elenco **Usa una tabella o una vista** per aprire la finestra di dialogo **Crea tabella** per creare una nuova tabella di destinazione con un elenco di colonne corrispondente alla tabella di origine.
   
    ![][12a]
7. Nella finestra di dialogo **Crea tabella** eseguire queste operazioni:
   
   1. Modificare il nome della tabella di destinazione in **SalesOrderDetail**.
   2. Rimuovere la colonna **rowguid** . Il tipo di dati **uniqueidentifier** non è supportato in SQL Data Warehouse.
   3. Modificare il tipo di dati della colonna **LineTotal** in **money**. Il tipo di dati **decima** l non è supportato in SQL Data Warehouse. Per informazioni sui tipi di dati supportati, vedere [CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)][CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)].
      
       ![][12b]
   4. Fare clic su **OK** per creare la tabella e tornare a **Editor destinazione ADO.NET**.
8. In **Editor destinazione ADO.NET** selezionare la scheda **Mapping** per vedere in che modo le colonne nell'origine vengono mappate alle colonne nella destinazione.
   
    ![][13]
9. Fare clic su **OK** per completare la configurazione dell'origine dati.

## <a name="step-6-run-the-package-to-load-the-data"></a>Passaggio 6: Eseguire il pacchetto per caricare i dati
Per eseguire il pacchetto, fare clic sul pulsante **Avvia** sulla barra degli strumenti o selezionare una delle opzioni **Esegui** nel menu **Debug**.

Quando inizia l'esecuzione del pacchetto, verranno visualizzate rotelline gialle che ruotano per indicare l'attività, nonché il numero di righe elaborate.

![][14]

Al termine dell'esecuzione del pacchetto vengono visualizzati segni di spunta verde per indicare che l'operazione è riuscita, nonché il numero totale di righe di dati caricati dall'origine alla destinazione.

![][15]

Congratulazioni. SQL Server Integration Services è stato usato correttamente per caricare i dati in Azure SQL Data Warehouse.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni relative al flusso di dati SSIS. Iniziare da qui: [Flusso di dati][Data Flow].
* Informazioni su come eseguire il debug e risolvere i problemi relativi ai pacchetti direttamente nell'ambiente di progettazione. Iniziare da qui: [Strumenti per la risoluzione dei problemi di sviluppo di pacchetti][Troubleshooting Tools for Package Development].
* Informazioni su come distribuire i progetti e i pacchetti SSIS nel server Integration Services o in un altro percorso di archiviazione. Iniziare da qui: [Distribuzione di progetti e pacchetti][Deployment of Projects and Packages].

<!-- Image references -->
[01]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-designer-01.png
[02]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-data-flow-task-02.png
[03]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-03.png
[04]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-manager-04.png
[05]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-05.png
[06]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/test-connection-06.png
[07]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-07.png
[08]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/preview-data-08.png
[09]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/source-destination-09.png
[10]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/connect-source-destination-10.png
[11]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-destination-11.png
[12a]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-before-12a.png
[12b]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-after-12b.png
[13]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/column-mapping-13.png
[14]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-running-14.png
[15]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-success-15.png

<!-- Article references -->

<!-- MSDN references -->
[PolyBase Guide]: https://msdn.microsoft.com/library/mt143171.aspx
[Download SQL Server Data Tools (SSDT)]: https://msdn.microsoft.com/library/mt204009.aspx
[CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)]: https://msdn.microsoft.com/library/mt203953.aspx
[Data Flow]: https://msdn.microsoft.com/library/ms140080.aspx
[Troubleshooting Tools for Package Development]: https://msdn.microsoft.com/library/ms137625.aspx
[Deployment of Projects and Packages]: https://msdn.microsoft.com/library/hh213290.aspx

<!--Other Web references-->
[Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]: http://go.microsoft.com/fwlink/?LinkID=626967
[SQL Server Evaluations]: https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016
[Visual Studio Community]: https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx
[AdventureWorks 2014 Sample Databases]: https://msftdbprodsamples.codeplex.com/releases/view/125550
