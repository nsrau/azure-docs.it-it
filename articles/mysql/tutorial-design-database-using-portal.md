---
title: Progettare il primo Database di Azure per il database MySQL - Portale di Azure | Microsoft Docs
description: In questa esercitazione viene illustrato come creare e gestire il database di Azure per il server e il database MySQL tramite il portale di Azure.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.topic: tutorial
ms.date: 11/03/2017
ms.custom: mvc
ms.openlocfilehash: a7f38484e000b05a57cad9bc95abb255414d0162
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="design-your-first-azure-database-for-mysql-database"></a>Progettare il primo database di Azure per il database MySQL
Il database di Azure per MySQL è un servizio gestito che consente di eseguire, gestire e scalare dei database MySQL a disponibilità elevata nel cloud. Tramite il portale di Azure, è possibile gestire facilmente il server e progettare un database.

In questa esercitazione si userà il portale di Azure per imparare a:

> [!div class="checklist"]
> * Creare un database di Azure per MySQL
> * Configurare il firewall del server
> * Usare lo strumento da riga di comando mysql per creare un database
> * Caricare dati di esempio
> * Eseguire query sui dati
> * Aggiornare i dati
> * Ripristinare i dati

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure
Aprire il Web browser preferito e visitare il [portale di Microsoft Azure](https://portal.azure.com/). Immettere le credenziali per accedere al portale. La visualizzazione predefinita è il dashboard del servizio.

## <a name="create-an-azure-database-for-mysql-server"></a>Creare un database di Azure per il server MySQL
Verrà creato un database di Azure per MySQL con un set definito di [risorse di calcolo e di archiviazione](./concepts-compute-unit-and-storage.md). Il server viene creato all'interno di un [gruppo di risorse di Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).

1. Passare a **database** > **Database di Azure per MySQL**. Se non si trova MySQL Server nella categoria **Database**, fare clic su **Visualizza tutto** per mostrare tutti i servizi di database disponibili. È possibile anche digitare **Database di Azure per MySQL** nella casella di ricerca per trovare rapidamente il servizio.
![2-1 Passare a MySQL](./media/tutorial-design-database-using-portal/2_1-Navigate-to-MySQL.png)

2. Fare clic sul riquadro **Database di Azure per MySQL** e quindi fare clic su **Crea**.

Nel nostro esempio compilare il modulo Database di Azure per MySQL con le informazioni seguenti:

| **Impostazione** | **Valore consigliato** | **Descrizione campo** |
|---|---|---|
| *Server name* (Nome server) | myserver4demo  | Il nome del server deve essere univoco a livello globale. |
| *Sottoscrizione* | mysubscription | Selezionare la sottoscrizione dall'elenco a discesa. |
| *Gruppo di risorse* | myresourcegroup | Creare un gruppo di risorse o usarne uno esistente. |
| *Nome di accesso amministratore server* | myadmin | Configurare il nome dell'account amministratore. |
| *Password* |  | Impostare una password complessa per l'account amministratore. |
| *Conferma password* |  | Confermare la password dell'account amministratore. |
| *Posizione* |  | Selezionare un'area disponibile. |
| *Versione* | 5.7 | Scegliere la versione più recente. |
| *Configura prestazioni* | Base con 50 unità di calcolo, 50 GB  | Scegliere **Piano tariffario**, **Unità di calcolo**, **Archiviazione (GB)** e quindi fare clic su **OK**. |
| *Aggiungi al dashboard* | Controllo | È consigliabile selezionare questa casella per trovare facilmente il server in un secondo momento. |
Fare quindi clic su **Crea**. Dopo pochi minuti, un nuovo database di Azure per il server MySQL sarà in esecuzione nel cloud. È possibile fare clic sul pulsante **Notifiche** sulla barra degli strumenti per monitorare il processo di distribuzione.

## <a name="configure-firewall"></a>Configurare il firewall
I database di Azure per MySQL sono protetti da un firewall. Per impostazione predefinita, vengono rifiutate tutte le connessioni al server e ai database all'interno del server. Prima di connettersi per la prima volta al database di Azure per MySQL, configurare il firewall per aggiungere l'indirizzo IP della rete pubblica del computer client (o un intervallo di indirizzi IP).

1. Fare clic sul server appena creato e quindi fare clic su **Sicurezza connessione**.
   ![3-1 Sicurezza della connessione](./media/tutorial-design-database-using-portal/3_1-Connection-security.png)
2. È possibile scegliere **Aggiungi indirizzo IP corrente** o configurare le regole del firewall qui. Ricordarsi di fare clic su **Salva** dopo aver creato le regole.
È ora possibile connettersi al server usando lo strumento da riga di comando mysql o lo strumento MySQL Workbench GUI.

> [!TIP]
> Il Database di Azure per il server MySQL comunica sulla porta 3306. Se si sta tentando di connettersi da una rete aziendale, il traffico in uscita attraverso la porta 3306 potrebbe non essere autorizzato dal firewall della rete. In questo caso non è possibile connettersi al server MySQL di Azure, a meno che il reparto IT non apra la porta 3306.

## <a name="get-connection-information"></a>Ottenere informazioni di connessione
Ottenere il **Nome server** completo e il **Nome di accesso dell'amministratore server** per il database di Azure per il server MySQL dal portale di Azure. Usare il nome completo del server per connettersi al server tramite lo strumento da riga di comando mysql. 

1. Nel [portale di Azure](https://portal.azure.com/) fare clic su **Tutte le risorse** nel menu a sinistra, digitare il nome e cercare il database di Azure per il server MySQL. Selezionare il nome del server per visualizzare i dettagli.

2. Nell'intestazione Impostazioni fare clic su **Proprietà**. Prendere nota del **NOME SERVER** e del **NOME DI ACCESSO DELL'AMMINISTRATORE SERVER**. È possibile fare clic sul pulsante Copia accanto a ogni campo per copiarlo negli Appunti.
   ![4-2 Proprietà del server](./media/tutorial-design-database-using-portal/4_2-server-properties.png)

In questo esempio il nome del server è *myserver4demo.mysql.database.azure.com* e l'account di accesso dell'amministratore del server è *myadmin@myserver4demo*.

## <a name="connect-to-the-server-using-mysql"></a>Connettersi al server usando mysql
Usare lo [strumento da riga di comando mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) per stabilire una connessione al database di Azure per il server MySQL. È possibile eseguire lo strumento da riga di comando mysql nel browser usando Azure Cloud Shell o avviarlo dal computer tramite gli strumenti mysql installati localmente. Per avviare Azure Cloud Shell, fare clic sul pulsante `Try It` in un blocco di codice in questo articolo oppure visitare il portale di Azure e fare clic sull'icona `>_` nella barra degli strumenti in alto a destra. 

Digitare il comando per la connessione:
```azurecli-interactive
mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
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
Dopo aver appreso come connettersi al Database di Azure per MySQL, si possono completare alcune attività di base:

In primo luogo, creare una tabella e caricarvi alcuni dati. Creare una tabella che contenga le informazioni riguardanti l'inventario.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Caricare i dati nelle tabelle
Dopo aver creato la tabella, inserirvi alcuni dati. Nella finestra del prompt dei comandi aperta, eseguire la query seguente per inserire alcune righe di dati.
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
Si supponga di avere eliminato un'importante tabella di database e di non poter ripristinare i dati facilmente. Il servizio Database di Azure per MySQL consente di ripristinare il server a un punto nel tempo, creando una copia dei database in un nuovo server. È possibile usare questo nuovo server per ripristinare i dati eliminati. La procedura seguente consente di ripristinare il server di esempio in un punto precedente all'aggiunta della tabella.

1. Nel portale di Azure individuare il database di Azure per MySQL. Nella pagina **Panoramica** fare clic su **Ripristina** nella barra degli strumenti. Verrà visualizzata la pagina Ripristina.

   ![10-1 Ripristinare un database](./media/tutorial-design-database-using-portal/10_1-restore-a-db.png)

2. Compilare il modulo **Ripristina** con le informazioni richieste.
   
   ![10-2 Modulo di ripristino](./media/tutorial-design-database-using-portal/10_2-restore-form.png)
   
   - **Punto di ripristino**: selezionare un punto nel tempo a cui si vuole ripristinare, entro l'intervallo di tempo elencato. Assicurarsi di convertire il fuso orario locale in ora UTC.
   - **Ripristina nel nuovo server**: fornire il nome del nuovo server in cui si vuole memorizzare il database da ripristinare.
   - **Posizione**: l'area è identica a quella del server di origine e non può essere modificata.
   - **Piano tariffario**: il piano tariffario è identico a quello del server di origine e non può essere modificato.
   
3. Fare clic su **OK** per ripristinare il server da [ripristinare in un punto nel tempo](./howto-restore-server-portal.md) precedente all'eliminazione della tabella. Il ripristino di un server crea una nuova copia del server, a partire dal momento nel tempo specificato. 

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si userà il portale di Azure per imparare a:

> [!div class="checklist"]
> * Creare un database di Azure per MySQL
> * Configurare il firewall del server
> * Usare lo strumento da riga di comando mysql per creare un database
> * Caricare dati di esempio
> * Eseguire query sui dati
> * Aggiornare i dati
> * Ripristinare i dati

> [!div class="nextstepaction"]
> [Come connettere le applicazioni a Database di Azure per MySQL](./howto-connection-string.md)
