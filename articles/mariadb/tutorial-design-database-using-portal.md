---
title: "Esercitazione: Progettare un'istanza di Database di Azure per MariaDB con il portale di Azure"
description: Questa esercitazione illustra come creare e gestire un database e un server di Database di Azure per MariaDB usando il portale di Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: tutorial
ms.date: 04/15/2019
ms.custom: mvc
ms.openlocfilehash: 1eb24d90c3aefa81f53a3e31c0bd460f45e5a250
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617700"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-database-by-using-the-azure-portal"></a>Esercitazione: Progettare un database di Database di Azure per MariaDB con il portale di Azure

Database di Azure per MariaDB è un servizio gestito che è possibile usare per eseguire, gestire e ridimensionare database MySQL a disponibilità elevata nel cloud. Tramite il portale di Azure, è possibile gestire facilmente il server e progettare un database.

In questa esercitazione si userà il portale di Azure per imparare a:

> [!div class="checklist"]
> * Creare un database di Azure per MariaDB
> * Configurare il firewall del server
> * Usare lo strumento da riga di comando mysql per creare un database
> * Caricare dati di esempio
> * Eseguire query sui dati
> * Aggiornare i dati
> * Ripristinare i dati

Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Nel browser passare al [portale di Azure](https://portal.azure.com/). Immettere le credenziali per accedere al portale. La visualizzazione predefinita è il dashboard del servizio.

## <a name="create-an-azure-database-for-mariadb-server"></a>Creare un database di Azure per un server MariaDB

Si crea un server di Database di Azure per MariaDB con un set definito di [risorse di calcolo e di archiviazione](concepts-pricing-tiers.md). Il server viene creato all'interno di un [gruppo di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

1. Selezionare il pulsante **Crea una risorsa** (+) nell'angolo superiore sinistro del portale.

2. Selezionare **Database** > **Database di Azure per MariaDB**. È anche possibile digitare **MariaDB** nella casella di ricerca per trovare il servizio.
   
   ![Passare a MySQL](./media/tutorial-design-database-using-portal/1-Navigate-to-mariadb.png)

3. Selezionare il riquadro **Database di Azure per MariaDB** e quindi selezionare **Crea**. Immettere o selezionare le informazioni richieste.
   
   ![Modulo per la creazione](./media/tutorial-design-database-using-portal/2-create-form.png)

    Impostazione | Valore consigliato | Descrizione campo 
    ---|---|---
    Nome server | *Nome server univoco* | Scegliere un nome univoco per identificare il database di Azure per il server MariaDB. Ad esempio, **mydemoserver**. Al nome del server specificato viene aggiunto il nome di dominio *.mariadb.database.azure.com*. Il nome del server può contenere solo lettere minuscole, numeri e il segno meno (-) Deve contenere tra 3 e 63 caratteri.
    Sottoscrizione | *sottoscrizione in uso* | Selezionare la sottoscrizione di Azure da usare per il server. Se si hanno più sottoscrizioni, scegliere quella in cui viene fatturata la risorsa.
    Gruppo di risorse | **myresourcegroup** | Immettere il nome di un nuovo gruppo di risorse o selezionare un gruppo di risorse esistente.
    Selezionare l'origine | **Vuoto** | Selezionare **Vuoto** per creare un nuovo server. Se si sta creando un server da un backup geografico di un server di Database di Azure per MariaDB esistente, selezionare **Backup**.
    Accesso amministratore server | **myadmin** | Account di accesso da usare per la connessione al server. Il nome di accesso dell'amministratore non può essere **azure_superuser**, **admin**, **administrator**, **root**, **guest** o **public**.
    Password | *A scelta dell'utente* | Immettere una nuova password per l'account dell'amministratore del server. Deve contenere tra 8 e 128 caratteri. La password deve contenere caratteri di tre delle categorie seguenti: lettere maiuscole, lettere minuscole, numeri (0-9) e caratteri non alfanumerici (!, $, #, % e così via).
    Conferma password | *A scelta dell'utente*| Confermare la password dell'account amministratore.
    Località | *Area più vicina ai propri utenti*| Selezionare la località più vicina agli utenti o alle altre applicazioni Azure.
    Versione | *Ultima versione*| Ultima versione, a meno che non si abbiano requisiti specifici per cui deve esserne usata un'altra.
    Piano tariffario | Vedere la descrizione. | Configurazioni di calcolo, archiviazione e backup per il nuovo server. Selezionare **Piano tariffario** > **Utilizzo generico**. Mantenere i valori predefiniti per le impostazioni seguenti:<br><ul><li>**Generazione di calcolo** (Generazione 5)</li><li>**vCore** (4 vCore)</li><li>**Archiviazione** (100 GB)</li><li>**Periodo di conservazione backup** (7 giorni)</li></ul><br>Per abilitare l'archiviazione con ridondanza geografica dei backup del server, in **Opzioni di ridondanza per il backup** selezionare **Con ridondanza geografica**. <br><br>Per salvare la selezione del piano tariffario, selezionare **OK**. Lo screenshot successivo mostra queste selezioni.
    
   ![Piano tariffario](./media/tutorial-design-database-using-portal/3-pricing-tier.png)

4. Selezionare **Create**. Dopo pochi minuti, un nuovo database di Azure per il server MariaDB sarà in esecuzione nel cloud. Per monitorare il processo di distribuzione, selezionare **Notifiche** sulla barra degli strumenti.

## <a name="configure-the-firewall"></a>Configurare il firewall

Il servizio Database di Azure per MariaDB è protetto da un firewall. Per impostazione predefinita, vengono rifiutate tutte le connessioni al server e ai database all'interno del server. Prima di connettersi per la prima volta a Database di Azure per MariaDB, configurare il firewall per aggiungere l'indirizzo IP della rete pubblica del computer client (o l'intervallo di indirizzi IP).

1. Selezionare il server appena creato e quindi selezionare **Sicurezza delle connessioni**.
   
   ![Sicurezza delle connessioni](./media/tutorial-design-database-using-portal/1-Connection-security.png)
2. È possibile selezionare **Aggiungi indirizzo IP corrente** o configurare le regole del firewall in questa posizione. Ricordarsi di selezionare **Salva** dopo aver creato le regole.

È ora possibile connettersi al server usando lo strumento da riga di comando mysql o MySQL Workbench.

> [!TIP]
> Il server di Database di Azure per MariaDB comunica sulla porta 3306. Se si cerca di connettersi da una rete aziendale, il traffico in uscita sulla porta 3306 potrebbe non essere consentito dal firewall della rete. In questo caso, per connettersi al server di Database di Azure per MariaDB è necessario che il reparto IT apra la porta 3306.

## <a name="get-connection-information"></a>Ottenere informazioni di connessione

Ottenere i valori di **Nome server** (completo) e **Nome di accesso dell'amministratore server** per il server di Database di Azure per MariaDB dal portale di Azure. Il nome del server completo viene usato per connettersi al server tramite lo strumento da riga di comando mysql. 

1. Nel menu a sinistra nel [portale di Azure](https://portal.azure.com/) selezionare **Tutte le risorse**. Immettere il nome del server e cercare il server di Database di Azure per MariaDB. Selezionare il nome del server per visualizzare i dettagli.

2. Nella pagina **Panoramica** prendere nota dei valori per **Nome server** e **Nome di accesso dell'amministratore server**. È anche possibile selezionare il pulsante **copia** accanto a ogni campo per copiare il valore negli Appunti.

   ![Proprietà del server](./media/tutorial-design-database-using-portal/2-server-properties.png)

In questo esempio il nome del server è **mydemoserver.mariadb.database.azure.com** e il nome di accesso dell'amministratore server è **myadmin\@mydemoserver**.

## <a name="connect-to-the-server-by-using-mysql"></a>Connettersi al server usando mysql

Usare lo [strumento da riga di comando mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) per stabilire una connessione al server di Database di Azure per MariaDB. È possibile eseguire lo strumento da riga di comando mysql da Azure Cloud Shell nel browser o dal computer tramite gli strumenti mysql installati in locale. Per aprire Azure Cloud Shell, selezionare il pulsante **Prova** in un blocco di codice in questo articolo oppure visitare il portale di Azure e fare clic sull'icona **>_** sulla barra degli strumenti in alto a destra. 

Immettere il comando per la connessione:

```azurecli-interactive
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Creazione di un database vuoto

Dopo avere stabilito la connessione al server, creare un database vuoto con cui lavorare:

```sql
CREATE DATABASE mysampledb;
```

Al prompt, eseguire il comando seguente per connettersi al database appena creato:

```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Creare tabelle nel database

Dopo aver appreso come connettersi al database di Database di Azure per MariaDB, è possibile completare alcune attività di base.

In primo luogo, creare una tabella e caricarvi alcuni dati. Creare una tabella per archiviare le informazioni riguardanti l'inventario:

```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-in-the-tables"></a>Caricare i dati nelle tabelle

Ora che si dispone di una tabella, inserire alcuni dati. Nella finestra del prompt dei comandi aperta, eseguire la query seguente per inserire alcune righe di dati:

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

## <a name="query-and-update-the-data-in-the-tables"></a>Eseguire una query e aggiornare i dati nelle tabelle

Eseguire la query seguente per recuperare informazioni dalla tabella del database:

```sql
SELECT * FROM inventory;
```

È anche possibile aggiornare i dati nelle tabelle:

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

La riga viene aggiornata quando si recuperano dati:

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Ripristinare un database a un momento precedente

Immaginare di avere eliminato accidentalmente un'importante tabella di database e di non poter ripristinare i dati facilmente. In Database di Azure per MariaDB è possibile ripristinare il server a un punto nel tempo, creando una copia dei database nel nuovo server. È possibile usare questo nuovo server per ripristinare i dati eliminati. La procedura seguente consente di ripristinare il server di esempio a un punto nel tempo precedente all'aggiunta della tabella:

1. Nel portale di Azure individuare il database di Azure per MariaDB. Nella pagina **Panoramica** selezionare **Ripristina**.

   ![Per ripristinare un database](./media/tutorial-design-database-using-portal/1-restore-a-db.png)

2. Nella pagina **Ripristina** immettere o selezionare le informazioni seguenti:
   
   ![Modulo di ripristino](./media/tutorial-design-database-using-portal/2-restore-form.png)
   
   - **Punto di ripristino**: selezionare un punto nel tempo per il ripristino, incluso nell'intervallo di tempo elencato. Assicurarsi di convertire il fuso orario locale in ora UTC.
   - **Ripristina nel nuovo server**: immettere il nome del nuovo server in cui eseguire il ripristino.
   - **Località**: l'area corrisponde a quella del server di origine e non può essere modificata.
   - **Piano tariffario**: il piano tariffario corrisponde a quello del server di origine e non può essere modificato.
   
3. Selezionare **OK** per [ripristinare il server a un punto nel tempo](./howto-restore-server-portal.md) precedente all'eliminazione della tabella. Il ripristino di un server crea una nuova copia del server al punto nel tempo specificato. 

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si userà il portale di Azure per imparare a:

> [!div class="checklist"]
> * Creare un database di Azure per MariaDB
> * Configurare il firewall del server
> * Usare lo strumento da riga di comando mysql per creare un database
> * Caricare dati di esempio
> * Eseguire query sui dati
> * Aggiornare i dati
> * Ripristinare i dati

> [!div class="nextstepaction"]
> [Come connettere le applicazioni a Database di Azure per MariaDB](./howto-connection-string.md)
