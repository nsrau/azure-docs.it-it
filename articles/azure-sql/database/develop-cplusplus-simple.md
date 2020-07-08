---
title: Connettersi al database SQL tramite C e C++
description: Usare il codice di esempio in questa guida introduttiva per creare con C++ un'applicazione moderna, supportata da un database relazionale potente nel cloud con il database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: cpp
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/12/2018
ms.openlocfilehash: 610e21064c26734461ba8fd6639868dc930f926c
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963938"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Connettersi al database SQL tramite C e C++
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Questo post è destinato agli sviluppatori C e C++ che tentano di connettersi al database SQL di Azure. È suddiviso in sezioni, pertanto è possibile passare alla sezione desiderata.

## <a name="prerequisites-for-the-cc-tutorial"></a>Prerequisiti per l'esercitazione su C/C++

Assicurarsi di avere quanto segue:

* Un account Azure attivo. Se non si ha un account, è possibile iscriversi per ottenere una [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). Per compilare ed eseguire questo esempio, è necessario installare i componenti del linguaggio C++.
* [Sviluppo di Linux per Visual Studio](https://docs.microsoft.com/cpp/linux/?view=vs-2019). Se si esegue lo sviluppo su Linux, è anche necessario installare l'estensione di Visual Studio per Linux.

## <a name="azure-sql-database-and-sql-server-on-virtual-machines"></a><a id="AzureSQL"></a>Database SQL di Azure ed SQL Server nelle macchine virtuali

Il database SQL di Azure è basato su Microsoft SQL Server ed è progettato per offrire un servizio a disponibilità elevata, efficiente e scalabile. L'uso di Azure SQL su un database proprietario eseguito in locale presenta molti vantaggi. Con SQL di Azure non è necessario installare, configurare, gestire o gestire il database, ma solo il contenuto e la struttura del database. Alcuni elementi tipici dei database che destano preoccupazione, ad esempio la ridondanza e la tolleranza di errore, sono incorporati.

Azure dispone attualmente di due opzioni per l'hosting dei carichi di lavoro di SQL Server: database SQL di Azure, database come servizio e SQL Server in macchine virtuali (VM). Non verranno fornite informazioni dettagliate sulle differenze tra questi due, ad eccezione del fatto che il database SQL di Azure è la soluzione migliore per le nuove applicazioni basate sul cloud, in modo da sfruttare i vantaggi dei costi e l'ottimizzazione delle prestazioni offerti dai servizi cloud. Se si intende eseguire la migrazione o estendere le applicazioni locali al cloud, SQL server nella macchina virtuale di Azure può rappresentare l'opzione più appropriata. Per semplificare le operazioni in questo articolo, è possibile creare un database SQL di Azure.

## <a name="data-access-technologies-odbc-and-ole-db"></a><a id="ODBC"></a>Tecnologie di accesso ai dati: ODBC e OLE DB

La connessione al database SQL di Azure non è diversa e attualmente esistono due modi per connettersi ai database: ODBC (Open Database Connectivity) e OLE DB (collegamento a oggetti e database di incorporamento). Negli ultimi anni Microsoft si è allineata a [ODBC per l'accesso ai dati relazionali nativi](https://blogs.msdn.microsoft.com/sqlnativeclient/20../../microsoft-is-aligning-with-odbc-for-native-relational-data-access/). ODBC è relativamente semplice e molto più veloce rispetto a OLE DB. Si tenga tuttavia presente che ODBC usa una vecchia API di tipo C.

## <a name="step-1--creating-your-azure-sql-database"></a><a id="Create"></a>Passaggio 1: Creazione di un database SQL di Azure

Vedere la [pagina introduttiva](single-database-create-quickstart.md) per informazioni su come creare un database di esempio.  In alternativa, è possibile seguire questo [breve video di due minuti](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) per creare un database SQL di Azure usando il portale di Azure.

## <a name="step-2--get-connection-string"></a><a id="ConnectionString"></a>Passaggio 2: Ottenere la stringa di connessione

Al termine del provisioning del database SQL di Azure, è necessario eseguire la procedura seguente per determinare le informazioni di connessione e aggiungere l'indirizzo IP del client per l'accesso al firewall.

In [portale di Azure](https://portal.azure.com/)passare alla stringa di connessione ODBC del database SQL di Azure usando **Mostra stringhe di connessione del database** elencate come parte della sezione Panoramica per il database:

![ODBCConnectionString](./media/develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/develop-cplusplus-simple/dbconnection.png)

Copiare il contenuto della stringa **ODBC (include Node.js) [SQL authentication]**. Questa stringa verrà usata successivamente per connettersi dall'interprete della riga di comando ODBC di C++ . La stringa include informazioni dettagliate quali il driver, il server e altri parametri di connessione al database.

## <a name="step-3--add-your-ip-to-the-firewall"></a><a id="Firewall"></a>Passaggio 3: Aggiungere l'indirizzo IP al firewall

Passare alla sezione firewall per il server e aggiungere l' [indirizzo IP del client al firewall usando questi passaggi](firewall-configure.md) per assicurarsi che sia possibile stabilire una connessione riuscita:

![AddyourIPWindow](./media/develop-cplusplus-simple/ip.png)

A questo punto, è stato configurato il database SQL di Azure e si è pronti per connettersi dal codice C++.

## <a name="step-4-connecting-from-a-windows-cc-application"></a><a id="Windows"></a>Passaggio 4: Connessione da un'applicazione C/C++ per Windows

È possibile connettersi facilmente al [database SQL di Azure usando ODBC in Windows usando questo esempio](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) che si compila con Visual Studio. L'esempio implementa un interprete della riga di comando ODBC che può essere usato per connettersi al database SQL di Azure. In questo esempio viene accettato come argomento della riga di comando un file DSN (Database Source Name ) oppure la stringa di connessione dettagliata copiata in precedenza dal portale di Azure. Visualizzare la pagina delle proprietà per il progetto e incollare la stringa di connessione come argomento di comando, come illustrato di seguito:

![Propsfile DNS](./media/develop-cplusplus-simple/props.png)

Assicurarsi di specificare i dettagli di autenticazione corretti per il database come parte della stringa di connessione al database.

Avviare l'applicazione per compilarla. Se la connessione ha esito positivo, verrà visualizzata la finestra seguente. È anche possibile eseguire alcuni semplici comandi SQL come **crea tabella** per convalidare la connettività al database:

![Comandi SQL](./media/develop-cplusplus-simple/sqlcommands.png)

In alternativa, è possibile creare un file DSN mediante la procedura guidata avviata quando non è specificato alcun argomento di comando. Si consiglia di provare anche questa opzione. È possibile usare questo file DSN per l'automazione e per proteggere le impostazioni di autenticazione:

![Creare un file DSN](./media/develop-cplusplus-simple/datasource.png)

Congratulazioni! È stata correttamente stabilita una connessione ad Azure SQL mediante C++ e ODBC in Windows. È possibile continuare la lettura per eseguire la stessa operazione sulla piattaforma Linux.

## <a name="step-5-connecting-from-a-linux-cc-application"></a><a id="Linux"></a>Passaggio 5: Connessione da un'applicazione C/C++ per Linux

Se non hai ancora ascoltato le notizie, Visual Studio ora ti permette di sviluppare anche un'applicazione C++ per Linux. Per informazioni su questo nuovo scenario, vedere il blog [Visual C++ for Linux Development](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) (Visual C++ per sviluppo di applicazioni Linux). Per la compilazione per Linux è necessario un computer remoto in cui è in esecuzione la distribuzione Linux. Se non è disponibile, è possibile configurarne rapidamente l'uso usando [macchine virtuali Linux di Azure](../../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Per questa esercitazione, si supponga di avere configurata una distribuzione di Linux Ubuntu 16.04. I passaggi qui di seguito si applicano anche a Ubuntu 15.10, Red Hat 6 e Red Hat 7.

I passaggi seguenti consentono di installare le librerie necessarie per ODBC ed SQL per la distribuzione:

```console
    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*
```

Avviare Visual Studio. In Strumenti -> Opzioni -> Multipiattaforma -> Gestione connessioni aggiungere una connessione alla casella di Linux:

![Opzioni degli strumenti](./media/develop-cplusplus-simple/tools.png)

Dopo aver stabilito la connessione tramite SSH, creare un modello di progetto vuoto (Linux):

![Nuovo modello di progetto](./media/develop-cplusplus-simple/template.png)

È quindi possibile aggiungere un [nuovo file di origine C e sostituirlo con questo contenuto](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Tramite le API ODBC SQLAllocHandle SQLSetConnectAttr ed SQLDriverConnect dovrebbe essere possibile inizializzare e stabilire una connessione al database.
Come nell'esempio di ODBC per Windows, è necessario sostituire la chiamata SQLDriverConnect con i dettagli dei parametri della stringa di connessione al database copiata in precedenza dal portale di Azure.

```c
     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);
```

L'ultima operazione da eseguire prima della compilazione consiste nell'aggiungere **odbc** come dipendenza della libreria:

![Aggiunta di ODBC come libreria di input](./media/develop-cplusplus-simple/lib.png)

Per avviare l'applicazione, visualizzare la console Linux nel menu **Debug**:

![Console Linux](./media/develop-cplusplus-simple/linuxconsole.png)

Se la connessione è stata stabilita correttamente, viene visualizzato il nome del database corrente stampato nella console Linux:

![Output della finestra della console Linux](./media/develop-cplusplus-simple/linuxconsolewindow.png)

Congratulazioni! L'esercitazione è stata completata ed è ora possibile connettersi al database SQL di Azure da C++ in piattaforme Windows e Linux.

## <a name="get-the-complete-cc-tutorial-solution"></a><a id="GetSolution"></a>Ottenere la soluzione completa per l'esercitazione su C/C++

La soluzione GetStarted contenente tutti gli esempi riportati in questo articolo è disponibile su GitHub:

* [Esempio di ODBC di C++ per Windows](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), scaricare l'esempio di ODBC di C++ per Windows per connettersi ad Azure SQL
* [Esempio di ODBC di C++ per Linux](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), scaricare l'esempio di ODBC di C++ per Linux per connettersi ad Azure SQL

## <a name="next-steps"></a>Passaggi successivi

* Esaminare la [Panoramica sullo sviluppo di database SQL](develop-overview.md)
* Per altre informazioni, vedere le [informazioni di riferimento sulle API ODBC](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Modelli di progettazione per applicazioni SaaS multi-tenant con il database SQL di Azure](saas-tenancy-app-design-patterns.md)
* Esplorare tutte le [funzionalità del database SQL](https://azure.microsoft.com/services/sql-database/)
