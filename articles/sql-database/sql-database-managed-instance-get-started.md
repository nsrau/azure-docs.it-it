---
title: "Portale di Azure: creare un'istanza gestita di SQL | Microsoft Docs"
description: Creare un'istanza gestita di SQL, un ambiente di rete e una VM client per l'accesso.
keywords: esercitazione su database SQL, creare un'istanza gestita di SQL
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: quickstart
ms.date: 08/13/2018
ms.author: jovanpop-msft
ms.openlocfilehash: cb378c2d2773096992ef688653fd77b2625f8754
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2018
ms.locfileid: "42023078"
---
# <a name="create-an-azure-sql-managed-instance"></a>Creare un'istanza gestita di SQL di Azure

Questa guida introduttiva illustra come creare un'istanza gestita di SQL in Azure. Istanza gestita di database SQL di Azure è un'istanza del motore di database di SQL Server PaaS (piattaforma distribuita come servizio) che consente di eseguire e ridimensionare database di SQL Server a disponibilità elevata nel cloud di Azure. Questa guida introduttiva illustra come iniziare a creare un'istanza gestita di SQL.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="prepare-network-environment"></a>Preparare l'ambiente di rete

Istanza gestita di SQL è un servizio sicuro inserito nella rete virtuale di Azure. Per creare un'istanza gestita, è necessario preparare l'ambiente di rete di Azure, che include:
 - Rete virtuale di Azure in cui verrà inserita l'istanza gestita.
 - Subnet nella rete virtuale di Azure in cui verranno inserite le istanze gestite.
 - Route definita dall'utente che consentirà a Istanza gestita di comunicare con i servizi di Azure che controllano e gestiscono l'istanza.

La subnet è dedicata alle istanze gestite e non è possibile creare altre risorse (ad esempio, macchine virtuali di Azure) in tale subnet. In questa guida introduttiva verranno create due subnet nella rete virtuale di Azure per poter inserire le istanze gestite nella subnet dedicata alle istanze gestite e le altre risorse nella subnet predefinita.

1. Distribuire l'ambiente di rete di Azure preparato per Istanza gestita di database SQL di Azure facendo clic sul pulsante seguente:

    <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener"> <img src="http://azuredeploy.net/deploybutton.png"> </a>

    Questo pulsante aprirà un modulo nel portale di Azure in cui è possibile configurare l'ambiente di rete prima di distribuirlo.

2. Modificare facoltativamente i nomi della rete virtuale e delle subnet e gli intervalli IP associati alle risorse di rete. Fare quindi clic sul pulsante "Acquista" per creare e configurare l'ambiente:

    ![Creare l'ambiente dell'istanza gestita](./media/sql-database-managed-instance-get-started/create-mi-network-arm.png)

    > [!Note]
    > Questa distribuzione di Azure Resource Manager creerà due subnet nella rete virtuale, una per le istanze gestite denominata **ManagedInstances** e l'altra denominata **Default** per le altre risorse, ad esempio la macchina virtuale client che può essere usata per connettersi a Istanza gestita. Se non sono necessarie due subnet, sarà possibile eliminare quella predefinita in un secondo momento, ma in tal caso non sarà possibile completare il passaggio 3 di questa guida introduttiva, [Preparare il computer client](#prepare-client-machine).

    > [!Note]
    > Se si modificano i nomi della rete virtuale e delle subnet, prendere nota dei nomi nuovi perché saranno necessari nelle sezioni seguenti. Nella parte restante dell'esercitazione si presuppone che siano state create la rete virtuale denominata **MyNewVNet**, la subnet **ManagedInstances** per le istanze gestite di SQL e la subnet **Default** per le macchine virtuali e le altre risorse.

## <a name="create-a-managed-instance"></a>Creare un'istanza gestita

La procedura seguente illustra come creare un'istanza gestita dopo che l'anteprima è stata approvata.

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
2. Individuare **Istanza gestita** e quindi selezionare **Azure SQL Database Managed Instance (preview)** (Istanza gestita di database SQL di Azure - anteprima).
3. Fare clic su **Create**(Crea).

   ![Creare un'istanza gestita](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

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
   |**Rete virtuale**|Rete virtuale creata in precedenza| Scegliere l'elemento **MyNewVNet/ManagedInstances** se nel passaggio precedente non sono stati modificati i nomi. In caso contrario, scegliere il nome della rete virtuale e la subnet delle istanze gestite immessi nella sezione precedente. **Non usare la subnet predefinita perché non è configurata per ospitare le istanze gestite**. |

   ![Modulo per la creazione dell'istanza gestita](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

6. Fare clic su **Piano tariffario** per definire le dimensioni delle risorse di calcolo e di archiviazione ed esaminare le opzioni del piano tariffario. Per impostazione predefinita, all'istanza vengono assegnati gratuitamente 32 GB di spazio di archiviazione, **che potrebbero non essere sufficienti per le proprie applicazioni**.
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
> Per la prima istanza in una subnet, i tempi di distribuzione sono in genere molto più lunghi che in caso di istanze successive. Il completamento può richiedere talvolta più di 24 ore. Non annullare l'operazione di distribuzione perché la durata è superiore al previsto. Questa durata della distribuzione della prima istanza è una situazione temporanea. Poco dopo l'inizio dell'anteprima pubblica è prevista una riduzione significativa dei tempi di distribuzione. Creare la seconda istanza gestita nella subnet richiederà qualche minuto.

## <a name="prepare-client-machine"></a>Preparare il computer client

Poiché l'istanza gestita di SQL viene inserita nella rete virtuale privata, è necessario creare una VM di Azure con alcuni strumenti client SQL installati, ad esempio SQL Server Management Studio o SQL Operations Studio, per connettersi all'istanza gestita ed eseguire query.

> [!Note]
> Invece della macchina virtuale di Azure client, è possibile configurare una connessione [da punto a sito](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) e connettersi all'istanza gestita dal computer locale.

Il modo più semplice per creare una macchina virtuale client con tutti gli strumenti necessari è usare i modelli di Azure Resource Manager.

1. Fare clic sul pulsante seguente (verificare di essere connessi al portale di Azure in un'altra scheda del browser):

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. Nel modulo che verrà aperto immettere il nome della macchina virtuale, il nome utente amministratore e la password che si userà per connettersi alla macchina virtuale.

    ![Creare una VM client](./media/sql-database-managed-instance-get-started/create-client-sql-vm.png)

    Se non sono stati modificati il nome della rete virtuale e la subnet predefinita, non è necessario modificare gli ultimi due parametri. In caso contrario, sostituire questi valori con i valori immessi quando si è configurato l'ambiente di rete.

3. Fare clic sul pulsante "Acquista" per distribuire la VM di Azure nella rete preparata.

4. Connettersi alla VM usando la connessione Desktop remoto e trovare SQL Server Management Studio o SQL Operations Studio che vengono automaticamente installati nella macchina virtuale.

5. Aprire SSMS e immettere il **nome host** per l'istanza gestita nella casella **Nome server**, selezionare **Autenticazione di SQL Server**, specificare account di accesso e password nella finestra di dialogo **Connetti al server** e quindi fare clic su **Connetti**.

    ![connessione a ssms](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

Dopo aver stabilito la connessione, è possibile visualizzare i database di sistema e utente nel nodo Database e vari oggetti nei nodi Sicurezza, Oggetti server, Replica, Gestione, SQL Server Agent e Profiler XEvent.

## <a name="next-steps"></a>Passaggi successivi

 - [Connettere le applicazioni a Istanza gestita](sql-database-managed-instance-connect-app.md).
 - [Eseguire la migrazione dei database da locale a Istanza gestita](sql-database-managed-instance-migrate.md).


