---
title: Configurare la connettività delle macchine virtuali di Azure
titleSuffix: Azure SQL Managed Instance
description: Connettersi ad Azure SQL Istanza gestita usando SQL Server Management Studio da una macchina virtuale di Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: e3dc2990e810096310617e468a533a65626008ff
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542654"
---
# <a name="quickstart-configure-an-azure-vm-to-connect-to-azure-sql-managed-instance"></a>Guida introduttiva: configurare una macchina virtuale di Azure per la connessione ad Azure SQL Istanza gestita
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Questa Guida introduttiva illustra come configurare una macchina virtuale di Azure per la connessione ad Azure SQL Istanza gestita tramite SQL Server Management Studio (SSMS). 


Per una guida introduttiva che illustra come connettersi da un computer client locale usando invece una connessione da punto a sito, vedere [configurare una connessione da punto a sito](point-to-site-p2s-configure.md).

## <a name="prerequisites"></a>Prerequisiti

Questa Guida introduttiva usa le risorse create in [creare un'istanza gestita](instance-create-quickstart.md) come punto di partenza.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-new-subnet-vnet"></a>Creare una nuova subnet VNet

La procedura seguente consente di creare una nuova subnet in SQL Istanza gestita VNet, in modo che una macchina virtuale di Azure possa connettersi all'istanza gestita. La subnet SQL Istanza gestita è dedicata alle istanze gestite. Non è possibile creare altre risorse, ad esempio macchine virtuali di Azure, in tale subnet.

1. Aprire il gruppo di risorse per l'istanza gestita creata nella Guida introduttiva per [creare un'istanza gestita](instance-create-quickstart.md) . Selezionare la rete virtuale per l'istanza gestita.

   ![Risorse dell'istanza gestita di SQL](./media/connect-vm-instance-configure/resources.png)

2. Selezionare **Subnet** e quindi **+ Subnet** per creare una nuova subnet.

   ![Subnet di SQL Istanza gestita](./media/connect-vm-instance-configure/subnets.png)

3. Compilare il modulo usando le informazioni riportate nella tabella seguente:

   | Impostazione| Valore consigliato | Descrizione |
   | ---------------- | ----------------- | ----------- |
   | **Nome** | Qualsiasi nome valido|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](/azure/architecture/best-practices/resource-naming).|
   | **Intervallo di indirizzi (blocco CIDR)** | Un intervallo valido | Il valore predefinito è appropriato per questa guida introduttiva.|
   | **Gruppo di sicurezza di rete** | nessuno | Il valore predefinito è appropriato per questa guida introduttiva.|
   | **Tabella di route** | nessuno | Il valore predefinito è appropriato per questa guida introduttiva.|
   | **Endpoint servizio** | 0 selezionato | Il valore predefinito è appropriato per questa guida introduttiva.|
   | **Delega subnet** | nessuno | Il valore predefinito è appropriato per questa guida introduttiva.|

   ![Nuova subnet SQL Istanza gestita per macchina virtuale client](./media/connect-vm-instance-configure/new-subnet.png)

4. Selezionare **OK** per creare questa subnet aggiuntiva nel VNet SQL istanza gestita.

## <a name="create-a-vm-in-the-new-subnet"></a>Creare una macchina virtuale nella nuova subnet 

I passaggi seguenti illustrano come creare una macchina virtuale nella nuova subnet per connettersi a SQL Istanza gestita.

## <a name="prepare-the-azure-virtual-machine"></a>Preparare la macchina virtuale di Azure

Poiché SQL Istanza gestita viene inserito nella rete virtuale privata, è necessario creare una macchina virtuale di Azure con uno strumento client SQL installato, ad esempio SQL Server Management Studio o Azure Data Studio. Questo strumento consente di connettersi a SQL Istanza gestita ed eseguire query. In questa guida introduttiva viene usato SQL Server Management Studio.

Il modo più semplice per creare una macchina virtuale client con tutti gli strumenti necessari è usare i modelli di Azure Resource Manager.

1. Verificare di essere connessi al portale di Azure in un'altra scheda del browser. Selezionare quindi il pulsante seguente per creare una macchina virtuale client e installare SQL Server Management Studio:

   [![Immagine che mostra un pulsante con etichetta "Distribuisci in Azure".](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json)

2. Compilare il modulo usando le informazioni riportate nella tabella seguente:

   | Impostazione| Valore consigliato | Descrizione |
   | ---------------- | ----------------- | ----------- |
   | **Sottoscrizione** | Una sottoscrizione valida | Deve trattarsi di una sottoscrizione in cui si dispone dell'autorizzazione per creare nuove risorse. |
   | **Gruppo di risorse** |Il gruppo di risorse specificato nella Guida introduttiva per la [creazione di SQL istanza gestita](instance-create-quickstart.md)|Deve essere il gruppo di risorse in cui si trova la rete virtuale.|
   | **Posizione** | Posizione per il gruppo di risorse | Questo valore viene popolato in base al gruppo di risorse selezionato. |
   | **Nome macchina virtuale**  | Qualsiasi nome valido | Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](/azure/architecture/best-practices/resource-naming).|
   |**Nome utente amministratore**|Qualsiasi nome utente valido|Per informazioni sui nomi validi, vedere [Regole di denominazione e restrizioni](/azure/architecture/best-practices/resource-naming). Non usare "serveradmin" perché è un ruolo a livello di server riservato.<br>Questo nome utente viene usato ogni volta che ci si [connette alla macchina virtuale](#connect-to-the-virtual-machine).|
   |**Password**|Qualsiasi password valida|La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).<br>Questa password viene usata ogni volta che ci si [connette alla macchina virtuale](#connect-to-the-virtual-machine).|
   | **Dimensioni macchina virtuale** | Qualsiasi dimensione valida | Il valore predefinito in questo modello, **Standard_B2s**, è sufficiente per questo argomento di avvio rapido. |
   | **Posizione**|[resourceGroup().location].| Non modificare questo valore. |
   | **Nome rete virtuale**|Rete virtuale in cui è stata creata l'istanza gestita|
   | **Nome della subnet**|Nome della subnet creata nella procedura precedente| Non scegliere la subnet in cui è stata creata l'istanza gestita.|
   | **artifacts Location** (Posizione elementi) | [deployment().properties.templateLink.uri] | Non modificare questo valore. |
   | **artifacts location SAS token** (Token di firma di accesso condiviso posizione elementi) | Lasciare vuoto il campo | Non modificare questo valore. |

   ![Creare una VM client](./media/connect-vm-instance-configure/create-client-sql-vm.png)

   Se è stato usato il nome VNet suggerito e la subnet predefinita durante la [creazione del istanza gestita SQL](instance-create-quickstart.md), non è necessario modificare gli ultimi due parametri. In caso contrario, è necessario modificare questi valori in base ai valori immessi durante la configurazione dell'ambiente di rete.

3. Selezionare la casella di controllo **Accetto le condizioni riportate sopra**.
4. Selezionare **Acquista** per distribuire la macchina virtuale di Azure nella rete.
5. Selezionare l'icona **Notifiche** per visualizzare lo stato della distribuzione.

> [!IMPORTANT]
> Non continuare finché non sono trascorsi circa 15 minuti dalla creazione della macchina virtuale per dare agli script di post-creazione il tempo di installare SQL Server Management Studio.

## <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale

I passaggi seguenti illustrano come connettersi alla macchina virtuale appena creata usando una connessione Desktop remoto.

1. Al termine della distribuzione, passare alla risorsa macchina virtuale.

    ![VM](./media/connect-vm-instance-configure/vm.png)  

2. Selezionare **Connetti**.

   Verrà visualizzato un file con estensione RDP (Remote Desktop Protocol) con l'indirizzo IP pubblico e il numero di porta per la macchina virtuale.

   ![Modulo RDP](./media/connect-vm-instance-configure/rdp.png)  

3. Selezionare **Scarica file RDP**.

   > [!NOTE]
   > È anche possibile usare SSH per connettersi alla macchina virtuale.

4. Chiudere il modulo **Connettere la macchina virtuale**.
5. Per connettersi alla VM, aprire il file RDP scaricato.
6. Quando richiesto, selezionare **Connect** (Connetti). In un Mac è necessario un client RDP come [questo desktop remoto client](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) da Mac App Store.

7. Immettere il nome utente e la password specificati durante la creazione della macchina virtuale, quindi scegliere **OK**.

8. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Scegliere **Sì** o **Continua** per procedere con la connessione.

Nel dashboard di Server Manager viene stabilita la connessione alla macchina virtuale.

## <a name="connect-to-sql-managed-instance"></a>Connettersi a Istanza gestita di SQL 

1. Nella macchina virtuale aprire SQL Server Management Studio.

   Sono necessari alcuni istanti per l'apertura, perché è necessario completare la configurazione poiché è la prima volta che SSMS è stato avviato.
2. Nella finestra di dialogo **Connetti al server** immettere il **nome host** completo per l'istanza gestita nella casella **nome server** . Selezionare **Autenticazione di SQL Server**, specificare il nome utente e la password e quindi selezionare **Connetti**.

    ![Connessione a SSMS](./media/connect-vm-instance-configure/ssms-connect.png)  

Dopo aver stabilito la connessione, è possibile visualizzare i database di sistema e utente nel nodo Database e vari oggetti nei nodi Sicurezza, Oggetti server, Replica, Gestione, SQL Server Agent e Profiler XEvent.

## <a name="next-steps"></a>Passaggi successivi

- Per una guida introduttiva che illustra come connettersi tramite un computer client locale usando una connessione da punto a sito, vedere [Configurare una connessione da punto a sito](point-to-site-p2s-configure.md).
- Per una panoramica delle opzioni di connessione per le applicazioni, vedere [Connettere le applicazioni a Istanza gestita di SQL](connect-application-instance.md).
- Per ripristinare un database di SQL Server esistente da locale a un'istanza gestita, è possibile usare il [servizio migrazione del database di Azure per la migrazione](../../dms/tutorial-sql-server-to-managed-instance.md) oppure il [comando T-SQL RESTORE](restore-sample-database-quickstart.md) per eseguire il ripristino da un file di backup del database.
