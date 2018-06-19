---
title: "Portale di Azure: creare un'istanza gestita di database SQL | Microsoft Docs"
description: Creare un'istanza gestita di database SQL di Azure in una rete virtuale.
keywords: esercitazione su database SQL, creare un'istanza gestita di database SQL
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 05/09/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: e337a5c7c203e2e1048149dfeff71436a4d2752f
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850611"
---
# <a name="create-an-azure-sql-database-managed-instance-in-the-azure-portal"></a>Creare un'istanza gestita di database SQL di Azure nel portale di Azure

Questa esercitazione illustra come creare un'istanza gestita di database SQL di Azure (anteprima) in una subnet dedicata di una rete virtuale con il portale di Azure e quindi connettersi all'istanza gestita usando SQL Server Management Studio in una macchina virtuale nella stessa rete virtuale.

> [!div class="checklist"]
> * Inserire la sottoscrizione nell'elenco elementi consentiti
> * Configurare una rete virtuale
> * Creare una nuova tabella di route e una route
> * Applicare la tabella di route alla subnet dell'istanza gestita
> * Creare un'istanza gestita
> * Creare una nuova subnet nella rete virtuale per una macchina virtuale
> * Creare una macchina virtuale nella nuova subnet della rete virtuale
> * Connettersi alla macchina virtuale
> * Installare SSMS e connettersi all'istanza gestita


Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

> [!IMPORTANT]
> Per un elenco delle aree geografiche in cui è attualmente disponibile Istanza gestita, vedere [Migrate your databases to a fully managed service with Azure SQL Database Managed Instance](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/) (Eseguire la migrazione dei database a un servizio completamente gestito con Istanza gestita di database SQL di Azure).
 
## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure

Accedere al [Portale di Azure](https://portal.azure.com/#create/Microsoft.SQLManagedInstance).

## <a name="whitelist-your-subscription"></a>Inserire la sottoscrizione nell'elenco elementi consentiti

La funzionalità Istanza gestita è stata inizialmente rilasciata come anteprima pubblica controllata per la quale è necessario che la sottoscrizione venga inserita nell'elenco elementi consentiti. Se la sottoscrizione non è già inserita nell'elenco elementi consentiti, usare la procedura seguente per visualizzare e accettare le condizioni per l'anteprima e inviare una richiesta per l'inserimento nell'elenco elementi consentiti.

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
2. Individuare **Istanza gestita** e quindi selezionare **Azure SQL Database Managed Instance (preview)** (Istanza gestita di database SQL di Azure - anteprima).
3. Fare clic su **Crea**.

   ![Creazione dell'istanza gestita](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. Selezionare la sottoscrizione, fare clic su **Condizioni per l'anteprima** e quindi specificare le informazioni richieste.

   ![Condizioni per l'anteprima di Istanza gestita](./media/sql-database-managed-instance-tutorial/preview-terms.png)

5. Al termine fare clic su **OK**.

   ![Condizioni per l'anteprima di Istanza gestita](./media/sql-database-managed-instance-tutorial/preview-approval-pending.png)

   > [!NOTE]
   > In attesa dell'approvazione dell'anteprima, è possibile continuare e completare le sezioni successive di questa esercitazione.

## <a name="configure-a-virtual-network-vnet"></a>Configurare una rete virtuale

La procedura seguente illustra come creare una nuova rete virtuale [Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md) che verrà usata dall'istanza gestita. Per altre informazioni sulla configurazione della rete virtuale, vedere l'articolo relativo alla [configurazione di una rete virtuale per Istanza gestita](sql-database-managed-instance-vnet-configuration.md).

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
2. Individuare e quindi selezionare **Rete virtuale**, verificare che la modalità di distribuzione selezionata sia **Resource Manager** e infine fare clic su **Crea**.

   ![Creazione della rete virtuale](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. Compilare il modulo relativo alla rete virtuale con le informazioni richieste, usando le informazioni riportate nella tabella seguente:

   | Impostazione| Valore consigliato | Descrizione |
   | ------ | --------------- | ----------- |
   |**Nome**|Qualsiasi nome valido|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Spazio degli indirizzi**|Qualsiasi intervallo di indirizzi valido, ad esempio 10.14.0.0/24|Nome dell'indirizzo della rete virtuale nella notazione CIDR.|
   |**Sottoscrizione**|Sottoscrizione in uso|Per informazioni dettagliate sulle sottoscrizioni, vedere [Subscriptions](https://account.windowsazure.com/Subscriptions) (Sottoscrizioni).|
   |**Gruppo di risorse**|Qualsiasi gruppo di risorse valido (nuovo o esistente)|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Posizione**|Qualsiasi località valida| Per informazioni sulle aree, vedere [Aree di Azure](https://azure.microsoft.com/regions/).|
   |**Nome della subnet**|Qualsiasi nome di subnet valido, ad esempio mi_subnet|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Intervallo di indirizzi subnet**|Qualsiasi indirizzo di subnet valido, ad esempio 10.14.0.0/28. Usare uno spazio indirizzi di subnet di dimensioni inferiori rispetto allo spazio indirizzi stesso per lasciare spazio per la creazione di altre subnet nella stessa rete virtuale, ad esempio una subnet per l'hosting di app di test/client o subnet del gateway per la connessione dall'ambiente locale o da altre reti virtuali.|Intervallo di indirizzi della subnet nella notazione CIDR. Deve essere incluso nello spazio indirizzi della rete virtuale.|
   |**Endpoint servizio**|Disabled|Abilitare uno o più endpoint servizio per la subnet.|
   ||||

   ![Modulo per la creazione della rete virtuale](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. Fare clic su **Crea**.

## <a name="create-new-route-table-and-a-route"></a>Creare una nuova tabella di route e una route

La procedura seguente illustra come creare una route 0.0.0.0/0 con hop successivo di tipo Internet.

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
2. Individuare e quindi selezionare **Tabella di route** e infine fare clic su **Crea** nella pagina Tabella di route. 

   ![Creazione della tabella di route](./media/sql-database-managed-instance-tutorial/route-table-create.png)

3. Compilare il modulo relativo alla tabella di route con le informazioni richieste, usando le informazioni riportate nella tabella seguente:

   | Impostazione| Valore consigliato | Descrizione |
   | ------ | --------------- | ----------- |
   |**Nome**|Qualsiasi nome valido|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Sottoscrizione**|Sottoscrizione in uso|Per informazioni dettagliate sulle sottoscrizioni, vedere [Subscriptions](https://account.windowsazure.com/Subscriptions) (Sottoscrizioni).|
   |**Gruppo di risorse**|Selezionare il gruppo di risorse creato nella procedura precedente|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Posizione**|Selezionare la località specificata nella procedura precedente| Per informazioni sulle aree, vedere [Aree di Azure](https://azure.microsoft.com/regions/).|
   |**Disabilita propagazione route BGP**|Attivato||
   ||||

   ![Modulo per la creazione della tabella di route](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

4. Fare clic su **Crea**.
5. Al termine della creazione della tabella di route, aprire la tabella di route appena creata.

   ![Tabella di route](./media/sql-database-managed-instance-tutorial/route-table.png)

6. Fare clic su **Route** e quindi su **Aggiungi**.

   ![Aggiunta alla tabella di route](./media/sql-database-managed-instance-tutorial/route-table-add.png)

7.  Aggiungere la **route 0.0.0.0/0 con hop successivo di tipo Internet** come **unica** route, usando le informazioni riportate nella tabella seguente:

    | Impostazione| Valore consigliato | DESCRIZIONE |
    | ------ | --------------- | ----------- |
    |**Nome route**|Qualsiasi nome valido|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
    |**Prefisso indirizzo**|0.0.0.0/0|Indirizzo IP di destinazione a cui si applica la route, nella notazione CIDR.|
    |**Tipo hop successivo**|Internet|L'hop successivo gestisce i pacchetti corrispondenti per la route.|
    |||

    ![Route](./media/sql-database-managed-instance-tutorial/route.png)

8. Fare clic su **OK**.

## <a name="apply-the-route-table-to-the-managed-instance-subnet"></a>Applicare la tabella di route alla subnet dell'istanza gestita

La procedura seguente illustra come impostare la nuova tabella di route nella subnet dell'istanza gestita.

1. Per impostare la tabella di route nella subnet dell'istanza gestita, aprire la rete virtuale creata in precedenza.
2. Fare clic su **Subnet** e quindi sulla subnet dell'istanza gestita (**mi_subnet** nello screenshot seguente).

    ![Subnet](./media/sql-database-managed-instance-tutorial/subnet.png)

11. Fare clic su **Tabella di route** e quindi selezionare **myMI_route_table**.

    ![Impostare la tabella di route](./media/sql-database-managed-instance-tutorial/set-route-table.png)

12. Fare clic su **Save**

    ![Impostare la tabella di route: salvataggio](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)

## <a name="create-a-managed-instance"></a>Creare un'istanza gestita

La procedura seguente illustra come creare un'istanza gestita dopo che l'anteprima è stata approvata.

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
2. Individuare **Istanza gestita** e quindi selezionare **Azure SQL Database Managed Instance (preview)** (Istanza gestita di database SQL di Azure - anteprima).
3. Fare clic su **Crea**.

   ![Creazione dell'istanza gestita](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

4. Selezionare la sottoscrizione e verificare che le condizioni per l'anteprima risultino **Accettate**.

   ![Anteprima di Istanza gestita accettata](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

5. Compilare il modulo relativo all'istanza gestita con le informazioni richieste, usando le informazioni riportate nella tabella seguente:

   | Impostazione| Valore consigliato | DESCRIZIONE |
   | ------ | --------------- | ----------- |
   |**Nome istanza gestita**|Qualsiasi nome valido|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Account di accesso amministratore istanza gestita**|Qualsiasi nome utente valido|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Non usare "serveradmin" perché è un ruolo a livello di server riservato.| 
   |**Password**|Qualsiasi password valida|La password deve contenere almeno 16 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Gruppo di risorse**|Gruppo di risorse creato in precedenza||
   |**Posizione**|Località selezionata in precedenza|Per informazioni sulle aree, vedere [Aree di Azure](https://azure.microsoft.com/regions/).|
   |**Rete virtuale**|Rete virtuale creata in precedenza|

   ![Modulo per la creazione dell'istanza gestita](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

6. Fare clic su **Piano tariffario** per definire le dimensioni delle risorse di calcolo e di archiviazione ed esaminare le opzioni del piano tariffario. Per impostazione predefinita, all'istanza vengono assegnati gratuitamente 32 GB di spazio di archiviazione, che potrebbero non essere sufficienti per le proprie applicazioni.
7. Usare i dispositivi di scorrimento o le caselle di testo per specificare la quantità di spazio di archiviazione e il numero di core virtuali. 
   ![piano tariffario istanza gestita](./media/sql-database-managed-instance-tutorial/managed-instance-pricing-tier.png)

8. Al termine, fare clic su **Applica** per salvare le impostazioni selezionate.  
9. Fare clic su **Crea** per distribuire l'istanza gestita.
10. Fare clic sull'icona **Notifiche** per visualizzare lo stato della distribuzione.
 
   ![Stato di avanzamento della distribuzione](./media/sql-database-managed-instance-tutorial/deployment-progress.png)

11. Fare clic su **Distribuzione in corso** per aprire la finestra dell'istanza gestita e monitorare ulteriormente lo stato di avanzamento della distribuzione.
 
   ![Stato di avanzamento della distribuzione 2](./media/sql-database-managed-instance-tutorial/managed-instance.png)

Mentre viene eseguita la distribuzione, passare alla procedura successiva.

> [!IMPORTANT]
> Per la prima istanza in una subnet, i tempi di distribuzione sono in genere molto più lunghi che in caso di istanze successive. Il completamento può richiedere talvolta più di 24 ore. Non annullare l'operazione di distribuzione perché la durata è superiore al previsto. Questa durata della distribuzione della prima istanza è una situazione temporanea. Poco dopo l'inizio dell'anteprima pubblica è prevista una riduzione significativa dei tempi di distribuzione.

## <a name="create-a-new-subnet-in-the-vnet-for-a-virtual-machine"></a>Creare una nuova subnet nella rete virtuale per una macchina virtuale

La procedura seguente illustra come creare una seconda subnet nella rete virtuale per una macchina virtuale in cui si installa SQL Server Management Studio e quindi connettersi all'istanza gestita.

1. Aprire la risorsa rete virtuale.
 
   ![VNet](./media/sql-database-managed-instance-tutorial/vnet.png)

2. Fare clic su **Subnet** e quindi su **+Subnet**.
 
   ![Aggiungere una subnet](./media/sql-database-managed-instance-tutorial/add-subnet.png)

3. Compilare il modulo relativo alla subnet con le informazioni richieste, usando le informazioni riportate nella tabella seguente:

   | Impostazione| Valore consigliato | Descrizione |
   | ------ | --------------- | ----------- |
   |**Nome**|Qualsiasi nome valido|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Intervallo di indirizzi (blocco CIDR)**|Qualsiasi intervallo di indirizzi valido all'interno della rete virtuale (usare l'impostazione predefinita)||
   |**Gruppo di sicurezza di rete**|Nessuna||
   |**Tabella di route**|Nessuna||
   |**Endpoint servizio**|Nessuna||

   ![Dettagli della subnet della VM](./media/sql-database-managed-instance-tutorial/vm-subnet-details.png)

4. Fare clic su **OK**.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Creare una macchina virtuale nella nuova subnet della rete virtuale

La procedura seguente illustra come creare una macchina virtuale nella stessa rete virtuale in cui viene creata l'istanza gestita. 

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
2. Selezionare **Calcolo** e quindi **Windows Server 2016 Datacenter** o **Windows 10**. In questa sezione dell'esercitazione si usa Windows Server. La configurazione di Windows 10 è molto simile. 

   ![Calcolo](./media/sql-database-managed-instance-tutorial/compute.png)

3. Compilare il modulo relativo alla macchina virtuale con le informazioni richieste, usando le informazioni riportate nella tabella seguente:

   | Impostazione| Valore consigliato | Descrizione |
   | ------ | --------------- | ----------- |
   |**Nome**|Qualsiasi nome valido|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   | **Tipo di disco VM**|SSD|Le unità SSD offrono il miglior rapporto tra prezzo e prestazioni.|   
   |**Nome utente**|Qualsiasi nome utente valido|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).| 
   |**Password**|Qualsiasi password valida|La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).| 
   |**Sottoscrizione**|Sottoscrizione in uso|Per informazioni dettagliate sulle sottoscrizioni, vedere [Subscriptions](https://account.windowsazure.com/Subscriptions) (Sottoscrizioni).|
   |**Gruppo di risorse**|Gruppo di risorse creato in precedenza||
   |**Posizione**|Località selezionata in precedenza||
   |**Si possiede già una licenza di Windows?**|No |Se si possiedono licenze di Windows con un contratto Software Assurance attivo, è possibile usare il Vantaggio Azure Hybrid per risparmiare sui costi delle risorse di calcolo.|
   ||||

   ![Modulo per la creazione della macchina virtuale](./media/sql-database-managed-instance-tutorial/virtual-machine-create-form.png)

4. Fare clic su **OK**.
5. Selezionare una dimensione per la VM. Per visualizzare altre dimensioni, selezionare **Visualizza tutto** o modificare il filtro **Supported disk type** (Tipo di disco supportato). Per questa esercitazione è sufficiente una macchina virtuale di piccole dimensioni.

    ![Dimensioni delle macchine virtuali](./media/sql-database-managed-instance-tutorial/virtual-machine-size.png)  

6. Fare clic su **Seleziona**.
7. Nel modulo **Impostazioni** fare clic su **Subnet** e quindi selezionare **vm_subnet**. Non scegliere la subnet in cui viene effettuato il provisioning dell'istanza gestita, ma un'altra subnet nella stessa rete virtuale.

    ![Impostazioni della VM](./media/sql-database-managed-instance-tutorial/virtual-machine-settings.png)  

8. Fare clic su **OK**.
9. Nella pagina di riepilogo esaminare i dettagli dell'offerta e quindi fare clic su **Crea** per avviare la distribuzione della macchina virtuale.
 
## <a name="connect-to-virtual-machine"></a>Connettersi alla macchina virtuale

La procedura seguente illustra come connettersi alla macchina virtuale appena creata con una connessione Desktop remoto.

1. Al termine della distribuzione, passare alla risorsa macchina virtuale.

    ![VM](./media/sql-database-managed-instance-tutorial/vm.png)  

2. Fare clic sul pulsante **Connetti** nelle proprietà della macchina virtuale. Verrà creato e scaricato un file Remote Desktop Protocol, con estensione rdp.
3. Per connettersi alla VM, aprire il file RDP scaricato. 
4. Quando richiesto, fare clic su **Connetti**. In Mac, è necessario un client RDP come questo [client Desktop remoto](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) disponibile nel Mac App Store.

5. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale e quindi fare clic su **OK**.

6. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Fare clic su **Sì** o **Continua** per procedere con la connessione.

Nel dashboard di Server Manager viene stabilita la connessione alla macchina virtuale.

> [!IMPORTANT]
> Non continuare finché il provisioning dell'istanza gestita non è completato. Al termine del provisioning, recuperare il nome host per l'istanza nel campo **Istanza gestita** della scheda **Panoramica** relativa all'istanza gestita. Il nome sarà simile al seguente: **drfadfadsfd.tr23.westus1-a.worker.database.windows.net**.

## <a name="install-ssms-and-connect-to-the-managed-instance"></a>Installare SSMS e connettersi all'istanza gestita

La procedura seguente illustra come scaricare e installare SSMS e quindi connettersi all'istanza gestita.

1. In Server Manager fare clic su **Server locale** nel riquadro sinistro.

    ![Proprietà in Server Manager](./media/sql-database-managed-instance-tutorial/server-manager-properties.png)  

2. Nel riquadro **Proprietà** fare clic su **Attivata** per modificare Configurazione sicurezza avanzata IE.
3. Nella finestra di dialogo **Sicurezza avanzata di Internet Explorer** fare clic su **Disattivata** nella sezione relativa agli amministratori e quindi su **OK**.

    ![Sicurezza avanzata di Internet Explorer](./media/sql-database-managed-instance-tutorial/internet-explorer-security-configuration.png)  
4. Aprire **Internet Explorer** dalla barra delle applicazioni.
5. Selezionare **Usa impostazioni di sicurezza e compatibilità consigliate** e quindi fare clic su **OK** per completare la configurazione di Internet Explorer 11.
6. Immettere https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms nella casella dell'indirizzo URL e premere **INVIO**. 
7. Scaricare la versione più recente di SQL Server Management Studio e fare clic su **Esegui** quando richiesto.
8. Quando richiesto, fare clic su **Installa** per iniziare.
9. Al termine dell'installazione, fare clic su **Chiudi**.
10. Aprire SQL Server Management Studio.
11. Nella finestra di dialogo **Connetti al server** immettere il *nome host* per l'istanza gestita nella casella **Nome server**, selezionare **Autenticazione di SQL Server**, specificare account di accesso e password e quindi fare clic su **Connetti**.

    ![connessione a ssms](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

Dopo aver stabilito la connessione, è possibile visualizzare i database di sistema e utente nel nodo Database e vari oggetti nei nodi Sicurezza, Oggetti server, Replica, Gestione, SQL Server Agent e Profiler XEvent.

> [!NOTE]
> Per ripristinare un database SQL esistente in un'istanza gestita, è possibile eseguire il ripristino da un file di backup del database usando [Servizio Migrazione del database di Azure per la migrazione](../dms/tutorial-sql-server-to-managed-instance.md) o il [comando T-SQL RESTORE](sql-database-managed-instance-restore-from-backup-tutorial.md) oppure eseguire un'[importazione da un file BACPAC](sql-database-import.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come creare un'istanza gestita di database SQL di Azure (anteprima) in una subnet dedicata di una rete virtuale con il portale di Azure e quindi connettersi all'istanza gestita usando SQL Server Management Studio in una macchina virtuale nella stessa rete virtuale.  Si è appreso come: 

> [!div class="checklist"]
> * Inserire la sottoscrizione nell'elenco elementi consentiti
> * Configurare una rete virtuale
> * Creare una nuova tabella di route e una route
> * Applicare la tabella di route alla subnet dell'istanza gestita
> * Creare un'istanza gestita
> * Creare una nuova subnet nella rete virtuale per una macchina virtuale
> * Creare una macchina virtuale nella nuova subnet della rete virtuale
> * Connettersi alla macchina virtuale
> * Installare SSMS e connettersi all'istanza gestita

Passare all'esercitazione successiva per apprendere come ripristinare un backup di database in un'istanza gestita di database SQL di Azure.

> [!div class="nextstepaction"]
>[Ripristinare un backup di database in un'istanza gestita di database SQL di Azure](sql-database-managed-instance-restore-from-backup-tutorial.md)
