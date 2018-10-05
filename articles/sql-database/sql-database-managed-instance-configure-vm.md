---
title: Connettere una macchina virtuale client - Istanza gestita di database SQL di Azure | Microsoft Docs
description: Connettersi a un'istanza gestita di database SQL di Azure tramite SQL Server Management Studio da una macchina virtuale di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, srbozovi, bonova
manager: craigg
ms.date: 09/13/2018
ms.openlocfilehash: 08b8e37493ea7bc549a2352aaa6714ef7c65bfdb
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159517"
---
# <a name="configure-azure-vm-to-connect-to-an-azure-sql-database-managed-instance"></a>Configurare una macchina virtuale di Azure per la connessione a un'istanza gestita di database SQL di Azure

In questa guida introduttiva viene illustrato come configurare una macchina virtuale di Azure per la connessione a un'istanza gestita di database SQL di Azure usando SQL Server Management Studio (SSMS). Per una guida introduttiva che illustra come connettersi tramite un computer client locale usando una connessione da punto a sito, vedere [Configurare una connessione da punto a sito](sql-database-managed-instance-configure-p2s.md) 

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva usa come punto di partenza le risorse create nella guida introduttiva seguente: [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-new-subnet-in-the-managed-instance-vnet"></a>Creare una nuova subnet nella rete virtuale dell'istanza gestita

La procedura seguente consente di creare una nuova subnet nella rete virtuale dell'istanza gestita per la connessione di una macchina virtuale di Azure all'istanza gestita. La subnet dell'istanza gestita è dedicata alle istanze gestite e non è possibile creare altre risorse (ad esempio, macchine virtuali di Azure) in tale subnet. 

1. Aprire il gruppo di risorse per l'istanza gestita che è stata creata nella guida introduttiva [Creare un'istanza gestita](sql-database-managed-instance-get-started.md), fare clic sulla rete virtuale per l'istanza gestita e quindi fare clic su **Subnet**.

   ![Risorse dell'istanza gestita](./media/sql-database-managed-instance-configure-vm/resources.png)

2. Fare clic sul segno **+** accanto a **Subnet** per creare una nuova subnet.

   ![Subnet dell'istanza gestita](./media/sql-database-managed-instance-configure-vm/subnets.png)

3. Compilare il modulo con le informazioni richieste, usando le informazioni riportate nella tabella seguente:

   | Impostazione| Valore consigliato | Descrizione |
   | ---------------- | ----------------- | ----------- | 
   | **Nome** | Qualsiasi nome valido|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   | **Intervallo di indirizzi (blocco CIDR)** | Un intervallo valido | Il valore predefinito è appropriato per questa guida introduttiva.|
   | **Gruppo di sicurezza di rete** | Nessuna | Il valore predefinito è appropriato per questa guida introduttiva.|
   | **Tabella di route** | Nessuna | Il valore predefinito è appropriato per questa guida introduttiva.|
   | **Endpoint servizio ** | 0 selezionato | Il valore predefinito è appropriato per questa guida introduttiva.|
   | **Delega subnet** | Nessuna | Il valore predefinito è appropriato per questa guida introduttiva.|
 
   ![Nuova subnet dell'istanza gestita per la macchina virtuale client](./media/sql-database-managed-instance-configure-vm/new-subnet.png)

4. Fare clic su **OK** per creare questa subnet aggiuntiva nella rete virtuale dell'istanza gestita.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Creare una macchina virtuale nella nuova subnet della rete virtuale

La procedura seguente illustra come creare una macchina virtuale nella nuova subnet per la connessione all'istanza gestita. 

## <a name="prepare-the-azure-virtual-machine"></a>Preparare la macchina virtuale di Azure

Poiché l'istanza gestita di SQL viene inserita nella rete virtuale privata, è necessario creare una macchina virtuale di Azure con alcuni strumenti client SQL installati, ad esempio SQL Server Management Studio o Azure Data Studio, per connettersi all'istanza gestita ed eseguire query. In questa guida introduttiva viene usato SQL Server Management Studio.

Il modo più semplice per creare una macchina virtuale client con tutti gli strumenti necessari è usare i modelli di Azure Resource Manager.

1. Fare clic sul pulsante seguente per creare una macchina virtuale client e installare SQL Server Management Studio (assicurarsi di aver eseguito l'accesso al portale di Azure in un'altra scheda del browser):

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. Compilare il modulo con le informazioni richieste, usando le informazioni riportate nella tabella seguente:

   | Impostazione| Valore consigliato | DESCRIZIONE |
   | ---------------- | ----------------- | ----------- |
   | **Sottoscrizione** | Una sottoscrizione valida | Deve trattarsi di una sottoscrizione in cui si dispone dell'autorizzazione per creare nuove risorse |
   | **Gruppo di risorse** |Il gruppo di risorse specificato nella guida introduttiva [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).|Deve essere il gruppo di risorse in cui si trova la rete virtuale.|
   | **Posizione** | Posizione per il gruppo di risorse | Questo valore viene popolato in base al gruppo di risorse selezionato | 
   | **Nome macchina virtuale**  | Qualsiasi nome valido | Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Nome utente amministratore**|Qualsiasi nome utente valido|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Non usare "serveradmin" perché è un ruolo a livello di server riservato.| 
   |**Password**|Qualsiasi password valida|La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   | **Dimensioni macchina virtuale** | Qualsiasi dimensione valida | Il valore predefinito in questo modello, **Standard_B2s, è sufficiente per questa guida introduttiva. |
   | **Posizione**|[resourceGroup().location].| Non modificare questo valore |
   | **Nome rete virtuale**|Località selezionata in precedenza|Per informazioni sulle aree, vedere [Aree di Azure](https://azure.microsoft.com/regions/).|
   | **Nome della subnet**|Nome della subnet creata nella procedura precedente| Non scegliere la subnet in cui è stata creata l'istanza gestita|
   | **artifacts Location** (Posizione elementi) | [deployment().properties.templateLink.uri] Non modificare questo valore |
   | **artifacts location SAS token** (Token di firma di accesso condiviso posizione elementi) | lasciare vuoto | Non modificare questo valore |

   ![Creare una VM client](./media/sql-database-managed-instance-configure-vm/create-client-sql-vm.png)

   Se sono stati usati il nome della rete virtuale suggerito e la subnet predefinita durante la [creazione dell'istanza gestita](sql-database-managed-instance-get-started.md), non è necessario modificare gli ultimi due parametri. In caso contrario, è necessario modificare questi valori in base ai valori immessi durante la configurazione dell'ambiente di rete.

3. Selezionare la casella di controllo **Accetto le condizioni riportate sopra**.
4. Fare clic su **Acquista** per distribuire la macchina virtuale di Azure nella rete.
5. Fare clic sull'icona **Notifiche** per visualizzare lo stato della distribuzione.
   
   Non continuare finché non viene creata la macchina virtuale di Azure. 

## <a name="connect-to-virtual-machine"></a>Connettersi alla macchina virtuale

La procedura seguente illustra come connettersi alla macchina virtuale appena creata con una connessione Desktop remoto.

1. Al termine della distribuzione, passare alla risorsa macchina virtuale.

    ![VM](./media/sql-database-managed-instance-configure-vm/vm.png)  

2. Fare clic su **Connetti**. 
   
   Verrà visualizzato un file con estensione RDP (Remote Desktop Protocol) con l'indirizzo IP pubblico e il numero di porta per la macchina virtuale. 

   ![Modulo RDP](./media/sql-database-managed-instance-configure-vm/rdp.png)  

3. Fare clic su **Scarica file RDP**.
 
   > [!NOTE]
   > È anche possibile usare SSH per connettersi alla macchina virtuale.

4. Chiudere il modulo **Connettere la macchina virtuale**.
5. Per connettersi alla VM, aprire il file RDP scaricato. 
6. Quando richiesto, fare clic su **Connetti**. In Mac, è necessario un client RDP come questo [client Desktop remoto](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) disponibile nel Mac App Store.

6. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale e quindi fare clic su **OK**.

7. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Fare clic su **Sì** o **Continua** per procedere con la connessione.

Nel dashboard di Server Manager viene stabilita la connessione alla macchina virtuale.

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Usare SSMS per connettersi all'istanza gestita

1. Nella macchina virtuale aprire SQL Server Management Studio (SSMS).
 
   L'apertura richiederà alcuni istanti, poiché è necessario il completamento della configurazione per il primo avvio di SQL Server Management Studio.
2. Nella finestra di dialogo **Connetti al server** immettere il **nome host** completo per l'istanza gestita nella casella **Nome server**, selezionare **Autenticazione di SQL Server**, specificare account di accesso e password e quindi fare clic su **Connetti**.

    ![connessione a ssms](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Dopo aver stabilito la connessione, è possibile visualizzare i database di sistema e utente nel nodo Database e vari oggetti nei nodi Sicurezza, Oggetti server, Replica, Gestione, SQL Server Agent e Profiler XEvent.

## <a name="next-steps"></a>Passaggi successivi

- Per una guida introduttiva che illustra come connettersi tramite un computer client locale usando una connessione da punto a sito, vedere [Configurare una connessione da punto a sito](sql-database-managed-instance-configure-p2s.md).
- Per una panoramica delle opzioni di connessione per le applicazioni, vedere [Connettere le applicazioni a un'istanza gestita](sql-database-managed-instance-connect-app.md).
- Per ripristinare un database SQL Server esistente da locale a un'istanza gestita, è possibile eseguire il ripristino da un file di backup del database usando il [Servizio Migrazione del database di Azure (DMS) per la migrazione](../dms/tutorial-sql-server-to-managed-instance.md) o usare il [comando T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) per eseguire un'importazione da un file di backup.
