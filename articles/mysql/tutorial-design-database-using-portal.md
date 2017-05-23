---
title: Progettare il primo Database di Azure per il database MySQL - Portale di Azure | Documentazione Microsoft
description: In questa esercitazione viene illustrato come creare e gestire il database di Azure per il server e il database MySQL tramite il portale di Azure.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2958486a0ec055cf1fe334e97389536b2c2bb01f
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---

# <a name="design-your-first-azure-database-for-mysql-database"></a>Progettare il primo database di Azure per il database MySQL

Il database di Azure per MySQL è un servizio gestito che consente di eseguire, gestire e scalare dei database MySQL a disponibilità elevata nel cloud. Tramite il portale di Azure, è possibile gestire facilmente il server e progettare un database.

In questa esercitazione si userà il portale di Azure per imparare a:

> [!div class="checklist"]
> * Creare un database di Azure per MySQL
> * Configurare il firewall del server
> * Usare lo [strumento della riga di comando mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) per creare un database
> * Caricare dati di esempio
> * Eseguire query sui dati
> * Aggiornare i dati
> * Ripristinare i dati

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure.
Aprire il browser preferito e visitare il [portale di Microsoft Azure](https://portal.azure.com/). Immettere le credenziali per accedere al portale. La visualizzazione predefinita è il dashboard del servizio.

## <a name="create-an-azure-database-for-mysql-server"></a>Creare un database di Azure per il server MySQL
Verrà creato un database di Azure per MySQL con un set definito di [risorse di calcolo e di archiviazione](./concepts-compute-unit-and-storage.md). Il server viene creato all'interno di un [gruppo di risorse di Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).

1.    Passare a **Database** -> **MySQL**. Se non si trova MySQL Server nella categoria **Database**, fare clic su **Visualizza tutto** per mostrare tutti i servizi di database disponibili. È anche possibile digitare **MySQL** nella casella di ricerca per trovare rapidamente il servizio.
![2-1 Passare a MySQL](./media/tutorial-design-database-using-portal/2_1-Navigate-to-MySQL.png)

2.    Fare clic sull'icona **MySQL** e quindi fare clic su **Crea**.

Nel nostro esempio, compilare il modulo Database di Azure per MySQL con le informazioni seguenti:

| **Campo modulo** | **Descrizione campo** |
|----------------|-----------------------|
| *Server name* (Nome server) | mysqlserver4demo (il nome server è univoco a livello globale) |
| *Sottoscrizione* | MySQLaaS (selezionare dall'elenco a discesa) |
| *Gruppo di risorse* | myresource (creare un gruppo di risorse o usarne uno esistente) |
| *Accesso amministratore server* | myadmin (definire il nome dell'account amministratore) |
| *Password* | impostare la password dell'account amministratore |
| *Conferma password* | confermare la password dell'account amministratore |
| *Posizione* | Europa settentrionale (scegliere tra **Europa settentrionale** e **Stati Uniti occidentali**) |
| *Versione* | 5.6 (scegliere la versione del server MySQL) |
| *Configura prestazioni* | Basic (scegliere **Livello di prestazioni**, **Unità di calcolo**, **Archiviazione**, quindi fare clic su **OK**) |

Fare quindi clic su **Crea**. Tra uno o due minuti, si otterrà un nuovo database di Azure per il server MySQL in esecuzione nel cloud. È possibile fare clic sul pulsante **Notifiche** sulla barra degli strumenti per monitorare il processo di distribuzione.

> [!TIP]
> Si consiglia di collocare i servizi di Azure nella stessa area e di selezionare la posizione più vicina. È anche possibile selezionare l'opzione **Aggiungi al dashboard** per tener traccia facilmente delle distribuzioni.

![2-2 Creare il server](./media/tutorial-design-database-using-portal/2_2-Create-server.png)

## <a name="configure-firewall"></a>Configurare il firewall
I database di Azure per MySQL sono protetti da un firewall. Per impostazione predefinita, vengono rifiutate tutte le connessioni al server e ai database all'interno del server. Prima di connettersi al Database di Azure per MySQL dal client per la prima volta, è necessario configurare il firewall e aggiungere l'indirizzo IP della rete pubblica del client (o intervallo di indirizzi IP) all'elenco whitelist.

1.    Fare clic sul server appena creato e quindi fare clic su **Sicurezza connessione**.

![3-1 Sicurezza della connessione](./media/tutorial-design-database-using-portal/3_1-Connection-security.png)

2.    È possibile scegliere **Aggiungi indirizzo IP corrente** o configurare le regole del firewall qui. Ricordarsi di fare clic su **Salva** dopo aver creato le regole.

È ora possibile connettersi al server usando lo strumento della riga di comando di mysql o lo strumento MySQL Workbench GUI.

> Il Database di Azure per il server MySQL comunica sulla porta 3306. Se si sta tentando di connettersi da una rete aziendale, il traffico in uscita attraverso la porta 3306 potrebbe non essere autorizzato dal firewall della rete. In questo caso, non sarà possibile connettersi al server MySQL di Azure, a meno che il reparto IT non apra la porta 3306.

## <a name="get-connection-information"></a>Ottenere informazioni di connessione
Ottenere il nome completo del server MySQL di Azure nel portale di Azure. Usare il nome completo del server per connettersi al server tramite lo strumento della riga di comando mysql.

1.    Nel [portale Azure](https://portal.azure.com/), fare clic su **Tutte le risorse** dal menu a sinistra e fare clic sul proprio Database di Azure per il server MySQL.

2.    Fare clic su **Proprietà**. Annotare **NOME SERVER** e **ACCESSO AMMINISTRATORE SERVER**.
![4-2 Proprietà del server](./media/tutorial-design-database-using-portal/4_2-server-properties.png)

In questo esempio, il nome del server è *mysql4doc.database.windows.net*, e l'account di accesso dell'amministratore del server è *mysqladmin@mysql4doc*.

## <a name="connect-to-the-server-using-mysql"></a>Connettersi al server usando mysql
Usare lo [strumento della riga di comando mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) per stabilire una connessione al Database di Azure per il server MySQL. In questo esempio, il comando è:
```cmd
mysql -h mysqlserver4demo.database.windows.net -u myadmin@mysqlserver4demo -p
```

## <a name="create-a-blank-database"></a>Creazione di un database vuoto
Dopo aver eseguito la connessione al server, creare un database vuoto con cui lavorare.
```sql
CREATE DATABASE mysampledb;
```

Nel prompt eseguire il comando seguente per cambiare la connessione nel database appena creato:
```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Creare tabelle nel database
Dopo aver appreso come connettersi al database di Azure per il database MySQL, si può passare al completamento di alcune attività di base.

In primo luogo, è possibile creare una tabella e caricarla con alcuni dati. Creare una tabella che contenga le informazioni riguardanti l'inventario.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Caricare i dati nelle tabelle
Ora che abbiamo una tabella, possiamo inserire alcuni dati al suo interno. Nella finestra del prompt dei comandi aperta, eseguire la query seguente per inserire alcune righe di dati.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

A questo punto, ci sono due righe di dati di esempio nella tabella creata in precedenza.

## <a name="query-and-update-the-data-in-the-tables"></a>Eseguire una query e aggiornare i dati nelle tabelle
Eseguire la query seguente per recuperare informazioni dalla tabella del database.
```sql
SELECT * FROM inventory;
```

Si possono anche aggiornare query e aggiornare i dati nelle tabelle.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

La riga viene aggiornata di conseguenza quando si recuperano dati.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Ripristinare un database a un momento precedente
Si supponga di aver eliminato accidentalmente questa tabella. Si tratta di un elemento che non è facile da ripristinare. Il database di Azure per MySQL consente di tornare in qualsiasi punto degli ultimi 35 giorni e di ripristinare questo punto nel tempo in un nuovo server. È possibile usare questo nuovo server per ripristinare i dati eliminati. La procedura seguente consente di ripristinare il server di esempio in un punto precedente all'aggiunta della tabella.

1-. Nel Database di Azure per la pagina di MySQL del server, fare clic su **Ripristina** sulla barra degli strumenti. Si apre la pagina **Ripristina** .
![10-1 Ripristinare un database](./media/tutorial-design-database-using-portal/10_1-restore-a-db.png)

2- Compilare il modulo **Ripristina** con le informazioni obbligatorie:

-    Punto di ripristino: selezionare un punto nel tempo precedente alla modifica del server.
-    Server di destinazione: fornire un nuovo nome del server che si desidera ripristinare.
-    Posizione: non è possibile selezionare l'area, per impostazione predefinita è la stessa del server di origine.
-    Piano tariffario: non è possibile modificare questo valore quando si ripristina un server. È uguale al server di origine.
![10-2 modulo di ripristino](./media/tutorial-design-database-using-portal/10_2-restore-form.png)

3- Fare clic su **OK** per ripristinare il server per [ripristinare in un punto nel tempo](./howto-restore-server-portal.md) precedente all'eliminazione delle tabelle. Il ripristino di un server in un altro punto nel tempo crea un duplicato del nuovo server come il server originale nel punto nel tempo specificato, purché sia entro il periodo di conservazione per il livello di servizio applicato.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si userà il portale di Azure per imparare a:

> [!div class="checklist"]
> * Creare un database di Azure per MySQL
> * Configurare il firewall del server
> * Usare lo [strumento della riga di comando mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) per creare un database
> * Caricare dati di esempio
> * Eseguire query sui dati
> * Aggiornare i dati
> * Ripristinare i dati

[Creare e gestire Database di Azure per le regole del firewall MySQL tramite il portale di Azure](./howto-manage-firewall-using-portal.md)

