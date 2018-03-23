---
title: Connettersi al database SQL tramite C e C++ | Documentazione Microsoft
description: Usare il codice di esempio in questa guida introduttiva per creare con C++ un'applicazione moderna, supportata da un database relazionale potente nel cloud con il database SQL di Azure.
services: sql-database
author: edmacauley
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.devlang: cpp
ms.topic: article
ms.date: 03/06/2017
ms.author: edmacauley
ms.openlocfilehash: 659b413b2686a50a0681e7307b51188e6e6d0859
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Connettersi al database SQL tramite C e C++
Questo post è destinato agli sviluppatori di C e C++ che vogliono connettersi al database SQL di Azure. È suddiviso in sezioni, pertanto è possibile passare alla sezione desiderata. 

## <a name="prerequisites-for-the-cc-tutorial"></a>Prerequisiti per l'esercitazione su C/C++
Assicurarsi di avere quanto segue:

* Un account Azure attivo. Se non si ha un account, è possibile iscriversi per ottenere una [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). Per compilare ed eseguire questo esempio, è necessario installare i componenti del linguaggio C++.
* [Sviluppo di Linux per Visual Studio](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e). Se si esegue lo sviluppo su Linux, è anche necessario installare l'estensione di Visual Studio per Linux. 

## <a id="AzureSQL"></a>Database SQL di Azure ed SQL Server nelle macchine virtuali
Azure SQL si basa su Microsoft SQL Server ed è progettato per garantire un servizio efficiente, scalabile e a disponibilità elevata. L'uso di SQL Azure presenta diversi vantaggi rispetto all'uso del database proprietario in esecuzione in locale. Con SQL Azure non è necessario installare, configurare, conservare o gestire il database, ma solo il contenuto e la struttura. Alcuni elementi tipici dei database che destano preoccupazione, ad esempio la ridondanza e la tolleranza di errore, sono incorporati. 

Azure attualmente offre due opzioni per l'hosting dei carichi di lavoro su SQL Server: database SQL di Azure, database come servizio ed SQL Server in Macchine virtuali (VM). Non verranno qui descritte nel dettaglio le differenze tra queste due opzioni. Il database SQL di Azure rappresenta tuttavia la soluzione migliore per le nuove applicazioni basate sul cloud per sfruttare la riduzione dei costi e l'ottimizzazione delle prestazioni offerte dai servizi cloud. Se si intende eseguire la migrazione o estendere le applicazioni locali al cloud, SQL server nella macchina virtuale di Azure può rappresentare l'opzione più appropriata. Per semplicità, in questo articolo verrà creato un database SQL di Azure. 

## <a id="ODBC"></a>Tecnologie di accesso ai dati: ODBC e OLE DB
La connessione al database SQL di Azure è del tutto analoga e attualmente sono disponibili due modi per connettersi ai database: ODBC (Open Database Connectivity) e OLE DB (Object Linking and Embedding Database). Negli ultimi anni Microsoft si è allineata a [ODBC per l'accesso ai dati relazionali nativi](https://blogs.msdn.microsoft.com/sqlnativeclient/2011/08/29/microsoft-is-aligning-with-odbc-for-native-relational-data-access/). ODBC è relativamente semplice e molto più veloce rispetto a OLE DB. Si tenga tuttavia presente che ODBC usa una vecchia API di tipo C. 

## <a id="Create"></a>Passaggio 1: Creazione di un database SQL di Azure
Vedere la [pagina introduttiva](sql-database-get-started-portal.md) per informazioni su come creare un database di esempio.  In alternativa, è possibile guardare questo [breve video di due minuti](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) per creare un database SQL di Azure mediante il portale di Azure.

## <a id="ConnectionString"></a>Passaggio 2: Ottenere la stringa di connessione
Dopo aver eseguito il provisioning del database SQLdi Azure, è necessario eseguire la procedura seguente per determinare le informazioni di connessione e aggiungere l'IP client per l'accesso al firewall. 

Nel [portale di Azure](https://portal.azure.com/) passare alla stringa di connessione ODBC del database SQL di Azure usando l'opzione **Show database connection strings** (Mostra stringhe di connessione del database) elencata come parte della sezione relativa alle informazioni generali del database: 

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Copiare il contenuto della stringa **ODBC (include Node.js) [SQL authentication]**. Questa stringa verrà usata successivamente per connettersi dall'interprete della riga di comando ODBC di C++ . La stringa include informazioni dettagliate quali il driver, il server e altri parametri di connessione al database. 

## <a id="Firewall"></a>Passaggio 3: Aggiungere l'indirizzo IP al firewall
Passare alla sezione relativa al firewall per il server di database e aggiungere l'[IP client al firewall seguendo i passaggi seguenti](sql-database-configure-firewall-settings.md) per assicurarsi di stabilire correttamente la connessione: 

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

A questo punto, il database SQL di Azure è stato configurato ed è possibile connettersi dal codice C++. 

## <a id="Windows"></a>Passaggio 4: Connessione da un'applicazione C/C++ per Windows
È possibile connettersi facilmente al [database SQL di Azure tramite ODBC in Windows seguendo questo esempio](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) compilato con Visual Studio. Nell'esempio viene implementato un interprete della riga di comando ODBC che può essere usato per connettersi al database SQL di Azure. In questo esempio viene accettato come argomento della riga di comando un file DSN (Database Source Name ) oppure la stringa di connessione dettagliata copiata in precedenza dal portale di Azure. Visualizzare la pagina delle proprietà per il progetto e incollare la stringa di connessione come argomento di comando, come illustrato di seguito: 

![Propsfile DNS](./media/sql-database-develop-cplusplus-simple/props.png)

Assicurarsi di specificare i dettagli di autenticazione corretti per il database come parte della stringa di connessione al database. 

Avviare l'applicazione per compilarla. Se la connessione ha esito positivo, verrà visualizzata la finestra seguente. È anche possibile eseguire alcuni semplici comandi SQL come **crea tabella** per convalidare la connettività al database:

![Comandi SQL](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

In alternativa, è possibile creare un file DSN mediante la procedura guidata avviata quando non è specificato alcun argomento di comando. Si consiglia di provare anche questa opzione. È possibile usare questo file DSN per l'automazione e per proteggere le impostazioni di autenticazione: 

![Creare un file DSN](./media/sql-database-develop-cplusplus-simple/datasource.png)

Congratulazioni! È stata correttamente stabilita una connessione ad Azure SQL mediante C++ e ODBC in Windows. È possibile continuare la lettura per eseguire la stessa operazione sulla piattaforma Linux. 

## <a id="Linux"></a>Passaggio 5: Connessione da un'applicazione C/C++ per Linux
Visual Studio ora consente di sviluppare anche applicazioni C++ per Linux. Per informazioni su questo nuovo scenario, vedere il blog [Visual C++ for Linux Development](https://blogs.msdn.microsoft.com/vcblog/2016/03/30/visual-c-for-linux-development/) (Visual C++ per sviluppo di applicazioni Linux). Per la compilazione per Linux è necessario un computer remoto in cui è in esecuzione la distribuzione Linux. Se non si ha disponibile un computer remoto con Linux, è possibile configurarlo rapidamente seguendo i passaggi presenti nell'articolo [Linux Azure Virtual machines](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Macchine virtuali di Linux in Azure). 

Per questa esercitazione, si supponga di avere configurata una distribuzione di Linux Ubuntu 16.04. I passaggi qui di seguito si applicano anche a Ubuntu 15.10, Red Hat 6 e Red Hat 7. 

I passaggi seguenti consentono di installare le librerie necessarie per ODBC ed SQL per la distribuzione:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Avviare Visual Studio. In Strumenti -> Opzioni -> Multipiattaforma -> Gestione connessioni aggiungere una connessione alla casella di Linux: 

![Opzioni degli strumenti](./media/sql-database-develop-cplusplus-simple/tools.png)

Dopo aver stabilito la connessione tramite SSH, creare un modello di progetto vuoto (Linux): 

![Nuovo modello di progetto](./media/sql-database-develop-cplusplus-simple/template.png)

È quindi possibile aggiungere un [nuovo file di origine C e sostituirlo con questo contenuto](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Tramite le API ODBC SQLAllocHandle SQLSetConnectAttr ed SQLDriverConnect dovrebbe essere possibile inizializzare e stabilire una connessione al database. Come nell'esempio di ODBC per Windows, è necessario sostituire la chiamata SQLDriverConnect con i dettagli dei parametri della stringa di connessione al database copiata in precedenza dal portale di Azure. 

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

L'ultima operazione da eseguire prima della compilazione consiste nell'aggiungere **odbc** come dipendenza della libreria: 

![Aggiunta di ODBC come libreria di input](./media/sql-database-develop-cplusplus-simple/lib.png)

Per avviare l'applicazione, visualizzare la console Linux nel menu **Debug**: 

![Console Linux](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Se la connessione è stata stabilita correttamente, viene visualizzato il nome del database corrente stampato nella console Linux: 

![Output della finestra della console Linux](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Congratulazioni! L'esercitazione è stata completata ed è ora possibile connettersi al database SQL di Azure da C++ nelle piattaforme Windows e Linux.

## <a id="GetSolution"></a>Ottenere la soluzione completa per l'esercitazione su C/C++
La soluzione GetStarted contenente tutti gli esempi riportati in questo articolo è disponibile su GitHub:

* [Esempio di ODBC di C++ per Windows](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), scaricare l'esempio di ODBC di C++ per Windows per connettersi ad Azure SQL
* [Esempio di ODBC di C++ per Linux](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), scaricare l'esempio di ODBC di C++ per Linux per connettersi ad Azure SQL

## <a name="next-steps"></a>Passaggi successivi
* Rivedere l'articolo [Panoramica dello sviluppo di database SQL](sql-database-develop-overview.md)
* Per altre informazioni, vedere le [informazioni di riferimento sulle API ODBC](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Risorse aggiuntive
* [Schemi progettuali per applicazioni SaaS multi-tenant con il database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Esplorare tutte le [funzionalità del database SQL](https://azure.microsoft.com/services/sql-database/)

