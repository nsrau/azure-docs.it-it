---
title: Progettare il primo Database di Azure per PostgreSQL tramite il portale di Azure | Documentazione Microsoft
description: In questa esercitazione viene illustrato come progettare il primo Database di Azure per PostgreSQL tramite il portale di Azure.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: tutorial
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 977d47fb72d1c9e2a482602aacc456ae95547676
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---
# <a name="design-your-first-azure-database-for-postgresql-using-the-azure-portal"></a>Progettare il primo Database di Azure per PostgreSQL tramite il portale di Azure

Il Database di Azure per PostgreSQL è un servizio gestito che consente di eseguire, gestire e scalare i database PostgreSQL a disponibilità elevata nel cloud. Tramite il portale di Azure, è possibile gestire facilmente il server e progettare un database.

In questa esercitazione si userà il portale di Azure per imparare a:
> [!div class="checklist"]
> * Creare un database di Azure per PostgreSQL
> * Configurare il firewall del server
> * Usare l'utilità [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) per creare un database
> * Caricare dati di esempio
> * Eseguire query sui dati
> * Aggiornare i dati
> * Ripristinare i dati

## <a name="prerequisites"></a>Prerequisiti
Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure.
Accedere al [Portale di Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Creare un database di Azure per PostgreSQL

Verrà creato un database di Azure per il server PostgreSQL con un set definito di [risorse di calcolo e di archiviazione](./concepts-compute-unit-and-storage.md). Il server viene creato all'interno di un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md).

Seguire questi passaggi per creare un database di Azure per il server PostgreSQL:
1.    Fare clic sul pulsante **+ Nuovo** nell'angolo superiore sinistro del portale di Azure.
2.    Selezionare **Database** nella pagina **Nuovo** e selezionare il **Database di Azure per PostgreSQL** nella pagina **Database**.
 ![Database di Azure per PostgreSQL - Creare il database](./media/tutorial-design-database-using-azure-portal/1-create-database.png)

3.    Compilare il modulo per i dettagli del nuovo server con le informazioni seguenti, come illustrato nell'immagine precedente:
    - Nome del server: **mypgserver 20170401** (il nome di un server esegue il mapping al nome DNS e pertanto deve essere univoco a livello globale) 
    - Sottoscrizione: se si dispone di più sottoscrizioni, scegliere la sottoscrizione appropriata in cui la risorsa esiste o per cui è configurata.
    - Gruppo di risorse: **myresourcegroup**
    - L'accesso dell'amministratore del server e la password scelta
    - Località
    - Versione di PostgreSQL

  > [!IMPORTANT]
  > L'account di accesso amministratore server e la password qui specificati sono necessari per accedere al server e ai relativi database più avanti in questa guida introduttiva. Prendere nota di queste informazioni per usarle in seguito.

4.    Fare clic su **Piano tariffario** per specificare il livello di servizio e il livello delle prestazioni per il nuovo database. Per questa guida introduttiva, selezionare il livello **Basic**, **50 unità di calcolo** e **50 GB** di archiviazione inclusi.
 ![Database di Azure per PostgreSQL - Selezionare il livello del servizio](./media/tutorial-design-database-using-azure-portal/2-service-tier.png)
5.    Fare clic su **OK**.
6.    Fare clic su **Crea** per eseguire il provisioning del server. Il provisioning richiede alcuni minuti.

  > [!TIP]
  > Selezionare l'opzione **Aggiungi al dashboard** per tener traccia facilmente delle distribuzioni.

7.    Sulla barra degli strumenti fare clic su **Notifiche** per monitorare il processo di distribuzione.
 ![Database di Azure per PostgreSQL - Vedere le notifiche](./media/tutorial-design-database-using-azure-portal/3-notifications.png)
   
  Per impostazione predefinita, il database **postgres** viene creato nel server. Il database [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) è un database predefinito che può essere usato da utenti, utilità e applicazioni di terze parti. 

## <a name="configure-a-server-level-firewall-rule"></a>Configurare una regola del firewall a livello di server

Il Database di Azure per il servizio PostgreSQL crea un firewall a livello di server. Questo firewall impedisce alle applicazioni e agli strumenti esterni di connettersi al server e ai database sul server, a meno che non venga creata una regola del firewall per aprire il firewall per indirizzi IP specifici. 

1.    Al termine della distribuzione, fare clic su **Tutte le risorse** dal menu a sinistra e digitare il nome **mypgserver 20170401** per la ricerca del server appena creato. Fare clic sul nome del server elencato nei risultati della ricerca. Si apre la pagina **Panoramica** del server in cui vengono fornite le opzioni per una configurazione aggiuntiva.
 
 ![Database di Azure per PostgreSQL - Cercare il server ](./media/tutorial-design-database-using-azure-portal/4-locate.png)

2.    Nel pannello del server, selezionare **Sicurezza connessione**. 
3.    Fare clic nella casella di testo in **Nome regola** e aggiungere una nuova regola firewall alla whitelist dell'intervallo IP per la connettività. Per questa esercitazione, consentire tutti gli indirizzi IP digitando **Rule Name = AllowAllIps**, **IP iniziale = 0.0.0.0** e **IP finale = 255.255.255.255** e quindi fare clic su **Salva**. È possibile impostare una regola firewall che copra un intervallo IP che sia in grado di connettersi dalla rete.
 
 ![Database di Azure per PostgreSQL - Creare una regola del firewall](./media/tutorial-design-database-using-azure-portal/5-firewall-2.png)

4.    Fare clic su **Salva** e quindi fare clic su **X** per chiudere la pagina **Sicurezza connessione**.

  > [!NOTE]
  > Il server PostgreSQL di Azure comunica sulla porta 5432. Se si sta tentando di connettersi da una rete aziendale, il traffico in uscita attraverso la porta 5432 potrebbe non essere autorizzato dal firewall della rete. In questo caso, non sarà possibile connettersi al server del database SQL di Azure, a meno che il reparto IT non apra la porta 5432.
  >


## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione

Quando viene creato il Database di Azure per il server PostgreSQL, viene creato anche il database con valore predefinito **postgres**. Per connettersi al server del database, è necessario fornire le informazioni sull'host e le credenziali di accesso.

1. Dal menu a sinistra nel portale di Azure, fare clic su **Tutte le risorse** e cercare il server appena creato **mypgserver 20170401**.

  ![Database di Azure per PostgreSQL - Cercare il server ](./media/tutorial-design-database-using-azure-portal/4-locate.png)

3. Fare clic sul nome del server **mypgserver-20170401**.
4. Selezionare la pagina del server **Panoramica**. Annotare il **Nome server** e il **nome di accesso dell'amministratore del server**.

 ![Database di Azure per PostgreSQL - Accesso dell'amministratore del server](./media/tutorial-design-database-using-azure-portal/6-server-name.png)


## <a name="connect-to-postgresql-database-using-psql-in-cloud-shell"></a>Connettersi al database PostgreSQL tramite psql in Cloud Shell

Si usi ora l'utilità della riga di comando psql per connettersi al Database di Azure per il server PostgreSQL. 
1. Avviare Azure Cloud Shell tramite l'icona del terminale nel riquadro di spostamento in alto.

   ![Database di Azure per PostgreSQL - Icona del terminale di Azure Cloud Shell](./media/tutorial-design-database-using-azure-portal/7-cloud-shell.png)

2. Azure Cloud Shell si apre nel browser, consentendo di digitare i comandi bash.

   ![Database di Azure per PostgreSQL - Prompt di Azure Shell Bash](./media/tutorial-design-database-using-azure-portal/8-bash.png)

3. Al prompt di Cloud Shell, connettersi al Database di Azure per il server PostgreSQL tramite i comandi psql. Il formato seguente è usato per connettersi a un Database di Azure per il server PostgreSQL con l'utilità [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
   ```bash
   psql --host=<myserver> --port=<port> --username=<server admin login> --dbname=<database name>
   ```

   Ad esempio, il comando seguente consente di connettersi al database predefinito denominato **postgres** sul server PostgreSQL **mypgserver 20170401.postgres.database.azure.com** usando le credenziali di accesso. Immettere la password dell'amministratore del server quando viene richiesto.

   ```bash
   psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
   ```

## <a name="create-a-new-database"></a>Creare un nuovo database
Dopo aver eseguito la connessione al server, creare un database vuoto al prompt.
```bash
CREATE DATABASE mypgsqldb;
```

Nel prompt, eseguire il comando seguente per cambiare la connessione nel database appena creato **mypgsqldb**.
```bash
\c mypgsqldb
```
## <a name="create-tables-in-the-database"></a>Creare tabelle nel database
Dopo aver appreso come connettersi al Database di Azure per PostgreSQL, si può passare al completamento di alcune attività di base.

In primo luogo, è possibile creare una tabella e caricarla con alcuni dati. Creare una tabella che tiene traccia delle informazioni riguardanti l'inventario.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

È possibile visualizzare ora la tabella appena creata nell'elenco di tabelle digitando:
```sql
\dt
```

## <a name="load-data-into-the-tables"></a>Caricare i dati nelle tabelle
Ora che abbiamo una tabella, possiamo inserire alcuni dati al suo interno. Nella finestra del prompt dei comandi aperta, eseguire la query seguente per inserire alcune righe di dati
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

A questo punto, sono presenti due righe di dati di esempio nella tabella creata in precedenza.

## <a name="query-and-update-the-data-in-the-tables"></a>Eseguire una query e aggiornare i dati nelle tabelle
Eseguire la query seguente per recuperare informazioni dalla tabella del database. 
```sql
SELECT * FROM inventory;
```

Si possono anche aggiornare i dati nelle tabelle
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

La riga viene aggiornata di conseguenza quando si recuperano dati.
```sql
SELECT * FROM inventory;
```

## <a name="restore-data-to-a-previous-point-in-time"></a>Ripristinare i dati a un punto precedente nel tempo
Si supponga di aver eliminato accidentalmente questa tabella. Questa situazione non è facile da ripristinare. Il Database di Azure per PostgreSQL consente di tornare in qualsiasi punto nel tempo (negli ultimi 7 giorni (Basic) e 35 giorni (Standard)) e ripristinare questo punto nel tempo in un nuovo server. È possibile usare questo nuovo server per ripristinare i dati eliminati. La procedura seguente consente di ripristinare il server di esempio in un punto precedente all'aggiunta della tabella.

1.    Nel Database di Azure per la pagina PostgreSQL del server, fare clic su **Ripristina** sulla barra degli strumenti. Si apre la pagina **Ripristina** .
  ![Portale di Azure - Opzioni del modulo di ripristino](./media/tutorial-design-database-using-azure-portal/9-azure-portal-restore.png)
2.    Compilare il modulo **Ripristina** con le informazioni obbligatorie:

  ![Portale di Azure - Opzioni del modulo di ripristino](./media/tutorial-design-database-using-azure-portal/10-azure-portal-restore.png)
  - **Punto di ripristino**: selezionare un punto nel tempo precedente alla modifica del server
  - **Server di destinazione**: fornire un nuovo nome del server che si desidera ripristinare
  - **Posizione**: non è possibile selezionare l'area, per impostazione predefinita è la stessa del server di origine
  - **Piano tariffario**: non è possibile modificare questo valore quando si ripristina un server. È uguale al server di origine. 
3.    Fare clic su **OK** per ripristinare il server per [ripristinare in un punto nel tempo](./howto-restore-server-portal.md) precedente all'eliminazione delle tabelle. Il ripristino di un server in un altro punto nel tempo crea un duplicato del nuovo server come il server originale nel punto nel tempo specificato, purché sia entro il periodo di conservazione per il [livello di servizio](./concepts-service-tiers.md) applicato.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si apprenderà come usare il portale di Azure e altre utilità per:
> [!div class="checklist"]
> * Creare un database di Azure per PostgreSQL
> * Configurare il firewall del server
> * Usare l'utilità [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) per creare un database
> * Caricare dati di esempio
> * Eseguire query sui dati
> * Aggiornare i dati
> * Ripristinare i dati

Successivamente, per informazioni su come usare l'Interfaccia della riga di comando di Azure per eseguire attività simili, esaminare questa esercitazione: [Progettare il primo Database di Azure per PostgreSQL tramite l'Interfaccia della riga di comando di Azure](tutorial-design-database-using-azure-cli.md)

