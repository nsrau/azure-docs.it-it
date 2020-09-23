---
title: 'Guida di avvio rapido: Creare un server - Portale di Azure - Database di Azure per MySQL'
description: Questo articolo contiene informazioni dettagliate su come usare il portale di Azure per creare rapidamente un database di Azure di esempio per il server MySQL in circa cinque minuti.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 7/15/2020
ms.openlocfilehash: cdddd9a90911499421351adf0f41ef90f0e2f9a5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906554"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-in-the-azure-portal"></a>Avvio rapido: Creare un server di Database di Azure per MySQL nel portale di Azure

Database di Azure per MySQL è un servizio gestito che consente di eseguire, gestire e ridimensionare database MySQL a disponibilità elevata nel cloud. Questa guida di avvio rapido illustra come creare un server Database di Azure per MySQL in circa cinque minuti usando il portale di Azure.  

Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure
Aprire il Web browser e quindi passare al [portale di Azure](https://portal.azure.com/). Immettere le credenziali per accedere al portale. La visualizzazione predefinita è il dashboard del servizio.

## <a name="create-an-azure-database-for-mysql-server"></a>Creare un server Database di Azure per MySQL
Si crea un database di Azure per il server MySQL con un set definito di [risorse di calcolo e di archiviazione](./concepts-compute-unit-and-storage.md). Il server viene creato all'interno di un [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md).

Seguire questa procedura per creare un database di Azure per il server MySQL:

1. Nell'angolo in alto a sinistra del portale selezionare **Crea una risorsa** (+).

2. Selezionare **Database** > **Database di Azure per MySQL**. È anche possibile immettere **MySQL** nella casella di ricerca per trovare il servizio.

  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png" alt-text="Opzione Database di Azure per MySQL":::

3. Compilare il modulo dei dettagli del nuovo server con le informazioni seguenti:
    
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Creare il modulo del server":::

**Impostazione** | **Valore consigliato** | **Descrizione campo** 
---|---|---
Subscription | Sottoscrizione in uso | Selezionare la sottoscrizione di Azure da usare per il server. Se si hanno più sottoscrizioni, scegliere quella in cui viene fatturata la risorsa.
Resource group | *myresourcegroup* | Specificare il nome di un gruppo di risorse nuovo o esistente. Il gruppo di risorse può essere usato per organizzare le dipendenze appartenenti a un singolo progetto.
Nome server | Nome server univoco | Immettere un nome univoco che identifichi il server del database di Azure per MySQL. Ad esempio, 'mysqldbserver'. Il nome del server può contenere solo lettere minuscole, numeri e il segno meno (-). e deve avere una lunghezza compresa tra 3 e 63 caratteri.
Origine dati |*Nessuno* | Selezionare *Nessuno* per creare un nuovo server da zero. Si selezionerebbe *Backup* se si stesse creando un server da un backup geografico di un server Database di Azure per MySQL esistente.
Accesso amministratore server | myadmin | Immettere un nome utente per l'amministratore del server. Non è possibile usare **azure_superuser**, **admin**, **administrator**, **root**, **guest** o **public** come nome utente dell'amministratore.
Password | *A scelta dell'utente* | Specificare una nuova password per l'account dell'amministratore del server. La lunghezza della password deve essere compresa tra 8 e 128 caratteri, con una combinazione di lettere maiuscole o minuscole, numeri e caratteri non alfanumerici (!, $, #, %, e così via).
Conferma password | *A scelta dell'utente*| Confermare la password dell'account amministratore.
Location | *Area più vicina ai propri utenti*| Scegliere la località più vicina agli utenti o alle altre applicazioni Azure.
Versione | *La versione principale più recente*| La versione principale più recente, a meno che non si abbiano requisiti specifici per cui deve esserne usata un'altra.
Calcolo e archiviazione | **Utilizzo generico**, **Generazione 5**, **2 vCore**, **5 GB**, **7 giorni**, **Con ridondanza geografica** |Configurazioni di calcolo, archiviazione e backup per il nuovo server. Selezionare **Configura server**. Selezionare quindi il piano tariffario appropriato. Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/mysql/). Per abilitare l'archiviazione con ridondanza geografica dei backup del server, selezionare **Con ridondanza geografica** in **Opzioni di ridondanza per il backup**. Selezionare **OK**.

   > [!NOTE]
   > È consigliabile usare il piano tariffario Basic se le esigenze di calcolo e di prestazioni I/O sono adeguate per il carico di lavoro. Si noti che i server creati nel piano tariffario Basic non possono essere scalati in un secondo momento per utilizzo generico o ottimizzati per la memoria. 

4. Selezionare **Rivedi e crea** per effettuare il provisioning del server. Il provisioning può richiedere fino a 20 minuti.
   
5. Selezionare **Notifiche** sulla barra degli strumenti (icona a forma di campana) per monitorare il processo di distribuzione.
   
Per impostazione predefinita, vengono creati i database seguenti nel server: **information_schema**, **mysql**, **performance_schema** e **sys**.

## <a name="configure-a-server-level-firewall-rule"></a>Configurare una regola del firewall a livello di server
Per impostazione predefinita, il server creato viene protetto con un firewall e non è accessibile pubblicamente. Per concedere l'accesso all'indirizzo IP, passare alla risorsa server nel portale di Azure e selezionare **Sicurezza delle connessioni** dal menu a sinistra per la risorsa server. Se non si è certi di come trovare una risorsa, vedere [Come aprire una risorsa](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources).

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Sicurezza delle connessioni - Regole del firewall":::
   
Selezionare ora **Aggiungi indirizzo IP client corrente** e quindi selezionare **Salva**. È possibile aggiungere altri indirizzi IP o fornire un intervallo IP per la connessione al server da tali IP. Per altre informazioni, vedere [Come gestire le regole del firewall nel server di Database di Azure per MySQL](./concepts-firewall-rules.md)

> [!NOTE]
> Controllare se la rete consente il traffico in uscita sulla porta 3306 usata dal Database di Azure per MySQL per evitare problemi di connettività.  

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>Connettersi al server di Database di Azure per MySQL usando il client da riga di comando mysql
È possibile scegliere [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) o [MySQL Workbench](./connect-workbench.md) per connettersi al server dall'ambiente locale. In questa guida di avvio rapido verrà eseguito **mysql.exe** in [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) per la connessione al server.

1. Avviare Azure Cloud Shell nel portale selezionando l'icona evidenziata in alto a sinistra. Annotare il nome del server, il nome dell'account di accesso dell'amministratore del server, la password e la sottoscrizione per il server appena creato dalla sezione **Panoramica** come illustrato nell'immagine seguente.

    >[!NOTE]
    >Se si avvia Cloud Shell per la prima volta, verrà richiesto di creare un gruppo di risorse e un account di archiviazione. Questo passaggio è occasionale e verrà automaticamente collegato per tutte le sessioni. 

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Visualizzazione completa di Cloud Shell nel portale":::
2. Eseguire questo comando nel terminale Azure Cloud Shell. Sostituire i valori con il nome effettivo del server e il nome di accesso dell'utente amministratore. Il nome utente dell'amministratore richiede un valore '@\<servername>', come indicato di seguito per Database di Azure per MySQL  

  ```azurecli-interactive
  mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  Ecco come si presenta l'esperienza nel terminale Cloud Shell
  ```
  Requesting a Cloud Shell.Succeeded.
  Connecting terminal...

  Welcome to Azure Cloud Shell

  Type "az" to use Azure CLI
  Type "help" to learn about Cloud Shell

  user@Azure:~$mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
  Enter password:
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 64796
  Server version: 5.6.42.0 Source distribution

  Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  mysql>
  ```
3. Nello stesso terminale Azure Cloud Shell creare un database **guest** 
  ```
  mysql> CREATE DATABASE guest;
  Query OK, 1 row affected (0.27 sec)
  ```
4. Passare a **guest** del database
  ```
  mysql> USE guest;
  Database changed 
  ```
5. Digitare ```quit``` e quindi premere INVIO per uscire da mysql.   

## <a name="clean-up-resources"></a>Pulire le risorse
La creazione di un server di Database di Azure per MySQL in un gruppo di risorse è stata completata.  Se non si prevede di aver bisogno di queste risorse in futuro, è possibile eliminarle eliminando il gruppo di risorse o è possibile eliminare semplicemente il server MySQL. Per rimuovere il gruppo di risorse, seguire questa procedura:
1. Accedere al portale di Azure e selezionare **Gruppi di risorse**. 
2. Nell'elenco dei gruppi di risorse scegliere il nome del gruppo di risorse.
3. Nella pagina Panoramica del gruppo di risorse selezionare **Elimina gruppo di risorse**.
4. Nella finestra di dialogo di conferma, digitare il nome del gruppo di risorse e quindi selezionare **Elimina**.

Per eliminare il server, è possibile fare clic sul pulsante **Elimina** nella pagina **Panoramica** del server come illustrato di seguito:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="Eliminare le risorse":::

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
>[Creare un'app PHP in Windows con MySQL](../app-service/app-service-web-tutorial-php-mysql.md)
>[Creare un'app PHP in Linux con MySQL](../app-service/containers/tutorial-php-mysql-app.md)
>[Creare un'app Spring basata su Java con MySQL](https://docs.microsoft.com/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)
