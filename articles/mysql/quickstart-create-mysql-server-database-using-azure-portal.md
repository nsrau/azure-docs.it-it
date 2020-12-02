---
title: 'Guida di avvio rapido: Creare un server - Portale di Azure - Database di Azure per MySQL'
description: Questo articolo contiene informazioni dettagliate su come usare il portale di Azure per creare un database di Azure di esempio per il server MySQL in circa cinque minuti.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: f71bcc1fd3b92a32a3e6d9fa056bae7131a663bd
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492608"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-by-using-the-azure-portal"></a>Avvio rapido: Creare un database di Azure per il server MySQL con il portale di Azure

Database di Azure per MySQL è un servizio gestito che consente di eseguire, gestire e dimensionare database MySQL a disponibilità elevata nel cloud. Questa guida di avvio rapido mostra come usare il portale di Azure per creare un server singolo del database di Azure per MySQL. Mostra anche come connettersi al server.

## <a name="prerequisites"></a>Prerequisiti
È necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-an-azure-database-for-mysql-single-server"></a>Creare un server singolo del database di Azure per MySQL
1. Passare al [portale di Azure](https://portal.azure.com/) per creare un server singolo del database di Azure per MySQL. Cercare e selezionare **Database di Azure per MySQL**:

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Trovare Database di Azure per MySQL":::

1. Selezionare **Aggiungi**.

2. Nella pagina **Selezionare l'opzione di distribuzione del database di Azure per MySQL** selezionare **Server singolo**:
   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/choose-singleserver.png" alt-text="Screenshot che mostra l'opzione Server singolo.":::

3. Immettere le impostazioni di base per un nuovo server singolo:

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Screenshot che mostra la pagina Crea server MySQL.":::

   **Impostazione** | **Valore consigliato** | **Descrizione**
   ---|---|---
   Subscription | Sottoscrizione in uso | Selezionare la sottoscrizione di Azure da usare.
   Resource group | **myresourcegroup** | Immettere un nuovo gruppo di risorse o uno esistente nella sottoscrizione.
   Nome server | **mydemoserver** | Immettere un nome univoco. Il nome del server può contenere solo lettere minuscole, numeri e il segno meno (-) Deve contenere da 3 a 63 caratteri.
   Origine dati |**Nessuno** | Selezionare **Nessuno** per creare un nuovo server da zero. Selezionare **Backup** solo se viene eseguito il ripristino di un backup geografico di un server esistente.
   Location |La posizione desiderata | Selezionare una posizione nell'elenco.
   Versione | La versione principale più recente| Usare la versione principale più recente. Vedere [tutte le versioni supportate](../postgresql/concepts-supported-versions.md).
   Calcolo e archiviazione | Usare i valori predefiniti| Il piano tariffario predefinito è **Utilizzo generico** con **4 vCore** e **100 GB** di spazio di archiviazione. La conservazione dei backup è impostata su **7 giorni** con l'opzione di backup **Con ridondanza geografica**.<br/>Esaminare la pagina relativa ai [prezzi](https://azure.microsoft.com/pricing/details/mysql/) e aggiornare le impostazioni predefinite, se necessario.
   Nome utente amministratore | **mydemoadmin** | Immettere il nome utente dell'amministratore del server. Non è possibile usare **azure_superuser**, **admin**, **administrator**, **root**, **guest** o **public** come nome utente dell'amministratore.
   Password | Una password | Una nuova password per l'utente amministratore del server. La lunghezza della password deve essere compresa tra 8 e 128 caratteri e deve contenere una combinazione di lettere maiuscole o minuscole, numeri e caratteri non alfanumerici (!, $, #, %, e così via).
  

   > [!NOTE]
   > È consigliabile usare il piano tariffario Basic se le esigenze di calcolo e di prestazioni I/O sono adeguate per il carico di lavoro. Si noti che per i server creati nel piano tariffario Basic non è possibile passare in un secondo momento ai piani per utilizzo generico o ottimizzati per la memoria.

4. Selezionare **Rivedi e crea** per effettuare il provisioning del server.

5. Attendere che la pagina del portale visualizzi **La distribuzione è stata completata**. Selezionare **Vai alla risorsa** per accedere alla pagina del server appena creato:

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/deployment-complete.png" alt-text="Screenshot che mostra il messaggio La distribuzione è stata completata.":::

[Problemi? Segnalarli](https://aka.ms/mysql-doc-feedback).

## <a name="configure-a-server-level-firewall-rule"></a>Configurare una regola del firewall a livello di server

Per impostazione predefinita, il nuovo server è protetto con un firewall. Per connettersi, è necessario fornire l'accesso all'indirizzo IP completando i passaggi seguenti:

1. Passare a **Sicurezza delle connessioni** dal riquadro sinistro per la risorsa server. Se non si è certi di come trovare una risorsa, vedere [Come aprire una risorsa](../azure-resource-manager/management/manage-resources-portal.md#open-resources).

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Screenshot che mostra la pagina Sicurezza delle connessioni > Regole del firewall.":::

2. Selezionare **Aggiungi indirizzo IP client corrente** e quindi **Salva**.

   > [!NOTE]
   > Per evitare problemi di connettività, controllare se la rete consente il traffico in uscita sulla porta 3306 usata dal Database di Azure per MySQL. 

È possibile aggiungere altri indirizzi IP o fornire un intervallo IP per la connessione al server da tali IP. Per altre informazioni, vedere [Come gestire le regole del firewall in un server di Database di Azure per MySQL](./concepts-firewall-rules.md).


[Problemi? Segnalarli](https://aka.ms/mysql-doc-feedback)

## <a name="connect-to-the-server-by-using-mysqlexe"></a>Connettersi al server usando mysql.exe
È possibile usare [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) o [MySQL Workbench](./connect-workbench.md) per connettersi al server dall'ambiente locale. In questa guida di avvio rapido verrà usato mysql.exe in [Azure Cloud Shell](../cloud-shell/overview.md) per la connessione al server.


1. Aprire Azure Cloud Shell nel portale selezionando il primo pulsante nella barra degli strumenti, come illustrato nello screenshot seguente. Annotare il nome del server, il nome dell'amministratore del server e la sottoscrizione del nuovo server nella sezione **Panoramica**, come illustrato nello screenshot.

    > [!NOTE]
    > Se si apre Cloud Shell per la prima volta, verrà chiesto di creare un gruppo di risorse e un account di archiviazione. Si tratta di un passaggio da completare una sola volta. Verrà automaticamente collegato per tutte le sessioni.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Screenshot che mostra Cloud Shell nel portale di Azure.":::
2. Eseguire il comando seguente nel terminale di Azure Cloud Shell. Sostituire i valori mostrati qui con il nome del server e il nome dell'utente amministratore effettivi. Per il Database di Azure per MySQL è necessario aggiungere `@\<servername>` al nome utente dell'amministratore, come illustrato di seguito: 

      ```azurecli-interactive
      mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p
      ```

      Di seguito è riportato come appare nel terminale di Cloud Shell:

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
3. Nello stesso terminale di Azure Cloud Shell creare un database denominato `guest`:
      ```
      mysql> CREATE DATABASE guest;
      Query OK, 1 row affected (0.27 sec)
      ```
4. Passare al database `guest`:
      ```
      mysql> USE guest;
      Database changed
      ```
5. Immettere `quit` e quindi **INVIO** per uscire da mysql.

[Problemi? Segnalarli](https://aka.ms/mysql-doc-feedback).

## <a name="clean-up-resources"></a>Pulire le risorse
La creazione di un server di Database di Azure per MySQL in un gruppo di risorse è stata completata.  Se non si prevede di aver bisogno di queste risorse in futuro, è possibile eliminarle eliminando il gruppo di risorse o è possibile eliminare semplicemente il server MySQL. Per rimuovere il gruppo di risorse, seguire questa procedura:
1. Accedere al portale di Azure e selezionare **Gruppi di risorse**.
2. Nell'elenco di gruppi di risorse selezionare il nome del gruppo di risorse desiderato.
3. Nella pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
4. Nella finestra di dialogo di conferma, digitare il nome del gruppo di risorse e quindi selezionare **Elimina**.

Per eliminare il server, è possibile selezionare **Elimina** nella pagina **Panoramica** del server come illustrato di seguito:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="Screenshot che mostra il pulsante Elimina nella pagina di panoramica del server.":::

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
>[Creare un'app PHP in Windows con MySQL](../app-service/tutorial-php-mysql-app.md) <br/>

> [!div class="nextstepaction"]
>[Creare un'app PHP in Linux con MySQL](../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%3fpivots%3dplatform-linux)<br/><br/>

[Le informazioni cercate non sono disponibili? Segnalarli](https://aka.ms/mysql-doc-feedback).