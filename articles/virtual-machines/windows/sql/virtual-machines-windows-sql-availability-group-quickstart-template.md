---
title: Usare modelli di avvio rapido di Azure per configurare gruppi di disponibilità AlwaysOn per SQL Server in una VM di Azure
description: Usare modelli di avvio rapido di Azure per creare il cluster di Failover di Windows, si aggiungono macchine virtuali SQL Server al cluster, creare il listener e configurare il bilanciamento del carico interno in Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: fb09d91bb3204a1ab3dc4f9df71eabd2ee7d2bd1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60591329"
---
# <a name="use-azure-quickstart-templates-to-configure-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Usare modelli di avvio rapido di Azure per configurare gruppi di disponibilità AlwaysOn per SQL Server in una VM di Azure
Questo articolo descrive come usare i modelli di avvio rapido di Azure per automatizzare parzialmente la distribuzione di una configurazione di gruppo di disponibilità Always On per macchine virtuali di SQL Server in Azure. In questo processo vengono usati due modelli di avvio rapido di Azure. 

   | Modello | Descrizione |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Crea il cluster di failover di Windows e vi aggiunge le VM di SQL Server. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Crea il listener del gruppo di disponibilità e configura il servizio di bilanciamento del carico interno. Questo modello può essere usato solo se è stato creato il cluster di failover Windows con il modello **101-sql-vm-ag-setup**. |
   | &nbsp; | &nbsp; |

Altre parti della configurazione del gruppo di disponibilità devono essere eseguite manualmente, ad esempio la creazione del gruppo di disponibilità e del servizio di bilanciamento del carico interno. Questo articolo illustra la sequenza dei passaggi automatizzati e manuali.
 

## <a name="prerequisites"></a>Prerequisiti 
Per automatizzare la configurazione di un gruppo di disponibilità Always On usando i modelli di avvio rapido sono necessari i prerequisiti seguenti: 
- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Un gruppo di risorse con un controller di dominio. 
- Una o più [macchine virtuali in Azure che eseguono SQL Server 2016 (o superiore) Enterprise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) aggiunte al dominio, nella stessa zona di disponibilità o set di disponibilità[ registrato con il provider di risorse di macchine virtuali SQL](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider).  
- (Non usato da qualsiasi entità) disponibili due indirizzi IP, uno per il bilanciamento del carico interno e uno per il listener del gruppo di disponibilità all'interno della stessa subnet del gruppo di disponibilità. Se viene usato un bilanciamento del carico esistente, è necessario un solo indirizzo IP disponibile.  

## <a name="permissions"></a>Autorizzazioni
Le autorizzazioni seguenti sono necessari per configurare il gruppo disponibilità Always On usando i modelli di avvio rapido di Azure: 

- Un dominio account utente che dispone dell'autorizzazione 'Crea Computer oggetto' nel dominio.  Ad esempio, un account amministratore di dominio ha in genere autorizzazioni sufficienti (ad esempio: account@domain.com). _Questo account deve anche fare parte del gruppo degli amministratori locali in ogni macchina virtuale per creare il cluster._
- L'account utente di dominio che controlla il servizio SQL Server. 


## <a name="step-1---create-the-wsfc-and-join-sql-server-vms-to-the-cluster-using-quickstart-template"></a>Passaggio 1: Creare il cluster WSFC e aggiungere VM di SQL Server al cluster usando il modello di avvio rapido 
Dopo aver registrato le macchine virtuali di SQL Server per il nuovo provider di risorse di macchine virtuali SQL, è possibile aggiungerle in *SqlVirtualMachineGroups*. Questa risorsa definisce i metadati del cluster di failover di Windows, tra cui versione, edizione, nome di dominio completo, account AD per gestire sia il cluster che il servizio SQL e l'account di archiviazione come cloud di controllo. L'aggiunta delle macchine virtuali di SQL Server al gruppo di risorse *SqlVirtualMachineGroups* esegue il bootstrap del servizio Cluster di failover di Windows per creare il cluster e quindi aggiunge le macchine virtuali di SQL Server a tale cluster. Questo passaggio viene automatizzato con il modello di avvio rapido **101-sql-vm-ag-setup** e può essere implementato con i passaggi seguenti:

1. Passare al modello di avvio rapido [**101-sql-vm-ag-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) e selezionare **Deploy to Azure** (Distribuisci in Azure) per avviare il modello nel portale di Azure.
1. Compilare i campi obbligatori per configurare i metadati del cluster di failover di Windows. I campi facoltativi possono essere lasciati vuoti.

    La tabella seguente mostra i valori necessari per il modello: 

   | **Campo** | Value |
   | --- | --- |
   | **Sottoscrizione** |  Sottoscrizione in cui si trovano le VM di SQL Server. |
   |**Gruppo di risorse** | Gruppo di risorse in cui si trovano le VM di SQL Server. | 
   |**Failover Cluster Name** (Nome cluster di failover) | Nome desiderato per il nuovo cluster di failover di Windows. |
   | **Existing Vm List** (Elenco VM esistenti) | VM di SQL Server da includere nel gruppo di disponibilità e che, di conseguenza, faranno parte di questo nuovo cluster. Separare i valori con una virgola e uno spazio (ad esempio: SQLVM1, SQLVM2). |
   | **SQL Server Version** (Versione di SQL Server) | Selezionare la versione di SQL Server delle VM di SQL Server dall'elenco a discesa. Attualmente sono supportate solo le immagini di SQL 2016 e SQL 2017. |
   | **Existing Fully Qualified Domain Name** (Nome di dominio completo esistente) | FQDN esistente per il dominio in cui si trovano le VM di SQL Server. |
   | **Existing Domain Account** (Account di dominio esistente) | Un account utente di dominio esistente con l'autorizzazione 'Crea oggetti computer' nel dominio quando viene creato il [CNO](/windows-server/failover-clustering/prestage-cluster-adds) durante la distribuzione del modello. Ad esempio, un account amministratore di dominio ha in genere autorizzazioni sufficienti (ad esempio: account@domain.com). *Questo account deve anche fare parte del gruppo degli amministratori locali in ogni macchina virtuale per creare il cluster.*| 
   | **Domain Account Password** (Password account di dominio) | Password dell'account utente di dominio indicato in precedenza. | 
   | **Existing Sql Service Account** (Account del servizio SQL Server esistente) | L'account utente di dominio che controlla il [servizio SQL Server](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) durante la distribuzione del gruppo di disponibilità (ad esempio account@domain.com). |
   | **Sql Service Password** (Password del servizio SQL) | Password dell'account utente di dominio che controlla il servizio SQL Server. |
   | **Cloud Witness Name** (Nome cloud di controllo) | Si tratta di un nuovo account di archiviazione di Azure che verrà creato e usato per il cloud di controllo. Questo nome può essere modificato. |
   | **\_artifacts Location** (Posizione elementi) | Questo campo è impostato per impostazione predefinita e non deve essere modificato. |
   | **\_artifacts Location Sas Token** (Token di firma di accesso condiviso posizione elementi) | Questo campo viene lasciato intenzionalmente vuoto. |
   | &nbsp; | &nbsp; |

1. Se si accettano i termini e le condizioni, selezionare la casella di controllo accanto a **Accetto le condizioni riportate sopra** e selezionare **Acquista** per completare la distribuzione del modello di avvio rapido. 
1. Per monitorare la distribuzione, selezionare la distribuzione dall'icona a forma di campanello **Notifiche** nella parte superiore della pagina oppure passare al **Gruppo di risorse** nel portale di Azure, selezionare **Distribuzioni** nel campo **Impostazioni** e scegliere la distribuzione 'Microsoft.Template'. 

   >[!NOTE]
   > Le credenziali specificate durante la distribuzione del modello vengono archiviate solo per la durata della distribuzione. Al termine della distribuzione, le password vengono rimosse e verrà richiesto di specificarle di nuovo in caso di aggiunta di altre macchine virtuali di SQL Server al cluster. 


## <a name="step-2---manually-create-the-availability-group"></a>Passaggio 2: Creare manualmente il gruppo di disponibilità 
Creare manualmente il gruppo di disponibilità come di consueto, usando [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell), o [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

  >[!IMPORTANT]
  > **Non** creare un listener in questo momento perché la procedura viene automatizzata dal modello di avvio rapido **101-sql-vm-aglistener-setup** nel passaggio 4. 

## <a name="step-3---manually-create-the-internal-load-balancer-ilb"></a>Passaggio 3: Creare manualmente il servizio di bilanciamento del carico interno (ILB)
Always On listener gruppo di disponibilità (AG) richiede un interno Azure (bilanciamento del carico). L'ILB fornisce un indirizzo IP mobile per il listener del gruppo di disponibilità che consente il failover e la riconnessione più veloci. Se le VM di SQL Server in un gruppo di disponibilità fanno parte dello stesso set di disponibilità, è possibile usare un servizio Load Balancer Basic. In caso contrario, è necessario usare un Load Balancer Standard.  **L'ILB deve trovarsi nella stessa rete virtuale delle istanze di VM di SQL Server.** Occorre creare solo l'ILB. Il resto della configurazione, ad esempio il pool back-end, il probe di integrità e le regole di bilanciamento del carico, viene gestita dal modello di avvio rapido **101-sql-vm-aglistener-setup** nel passaggio 4. 

1. Nel portale di Azure aprire il gruppo di risorse contenente le macchine virtuali di SQL Server. 
2. Nel gruppo di risorse fare clic su **Aggiungi**.
3. Nei risultati della ricerca cercare il **bilanciamento del carico** e selezionare **Servizio di bilanciamento del carico**, pubblicato da **Microsoft**.
4. Nel pannello **Servizio di bilanciamento del carico** fare clic su**Crea**.
5. Bella finestra di dialogo **Crea servizio di bilanciamento del carico** configurare il servizio di bilanciamento del carico come segue:

   | Impostazione | Value |
   | --- | --- |
   | **Nome** |Nome che rappresenta il servizio di bilanciamento del carico. Ad esempio **sqlLB**. |
   | **Tipo** |**Interna**: La maggior parte delle implementazioni usa un servizio di bilanciamento del carico interno, che consente alle applicazioni all'interno della stessa rete virtuale di connettersi al gruppo di disponibilità.  </br> **Esterna**: consente alle applicazioni di connettersi al gruppo di disponibilità tramite una connessione Internet pubblica. |
   | **Rete virtuale** | Selezionare la rete virtuale in cui si trovano le istanze di SQL Server. |
   | **Subnet** | Selezionare la subnet in cui si trovano le istanze di SQL Server. |
   | **Assegnazione indirizzi IP** |**Statico** |
   | **Indirizzo IP privato** | Specificare un indirizzo IP disponibile della subnet. |
   | **Sottoscrizione** |Se si hanno più sottoscrizioni, può essere visualizzato questo campo. Selezionare la sottoscrizione da associare a questa risorsa. In genere è la stessa sottoscrizione di tutte le risorse del gruppo di disponibilità. |
   | **Gruppo di risorse** |Selezionare il gruppo di risorse in cui si trovano le istanze di SQL Server. |
   | **Posizione** |Selezionare il percorso di Azure in cui si trovano le istanze di SQL Server. |
   | &nbsp; | &nbsp; |

6. Selezionare **Create**. 


  >[!IMPORTANT]
  > La risorsa IP pubblico per ogni VM di SQL Server deve avere uno SKU standard per essere compatibile con il Load Balancer Standard. Per determinare lo SKU della risorsa IP pubblico della VM, passare a **Gruppo di risorse**, selezionare la risorsa **Indirizzo IP pubblico** per la VM di SQL Server desiderata e individuare il valore sotto **SKU**  nel pannello **Panoramica**. 

## <a name="step-4---create-the-ag-listener-and-configure-the-ilb-with-the-quickstart-template"></a>Passaggio 4: Creare il listener AG e configurare l'ILB con il modello di avvio rapido

Creare il listener del gruppo di disponibilità e configurare automaticamente il servizio di bilanciamento del carico interno (ILB) con il modello di avvio rapido **101-sql-vm-aglistener-setup**, che effettua il provisioning della risorsa Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener. Il modello di avvio rapido**101-sql-vm-aglistener-setup**, tramite il provider di risorse di macchine virtuali SQL, esegue le azioni seguenti:

 - Crea una nuova risorsa IP front-end (in base al valore di indirizzo IP specificato durante la distribuzione) per il listener. 
 - Configura le impostazioni di rete per il cluster e l'ILB. 
 - Configura il pool back-end dell'ILB, il probe di integrità e le regole di bilanciamento del carico.
 - Crea il listener AG con il nome e l'indirizzo IP specificati.
 
   >[!NOTE]
   > Il modello **101-sql-vm-aglistener-setup** può essere usato solo se è stato creato il cluster di failover Windows con il modello **101-sql-vm-ag-setup**.
   
   
Per configurare l'ILB e creare il listener AG, eseguire le operazioni seguenti:
1. Passare al modello di avvio rapido [**101-sql-vm-aglistener-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) e selezionare **Deploy to Azure** (Distribuisci in Azure) per avviare il modello di avvio rapido nel portale di Azure.
1. Compilare i campi obbligatori per configurare l'ILB e creare il listener del gruppo di disponibilità. I campi facoltativi possono essere lasciati vuoti. 

    La tabella seguente mostra i valori necessari per il modello: 

   | **Campo** | Value |
   | --- | --- |
   |**Gruppo di risorse** | Gruppo di risorse in cui si trovano le VM di SQL Server e il gruppo di disponibilità. | 
   |**Existing Failover Cluster Name** (Nome cluster di failover esistente) | Nome del cluster a cui sono aggiunte le VM di SQL Server. |
   | **Existing Sql Availability Group** (Gruppo di disponibilità SQL esistente)| Nome del gruppo di disponibilità di cui fanno parte le VM di SQL Server |
   | **Existing Vm List** (Elenco VM esistenti) | Nomi delle VM di SQL Server che fanno parte del gruppo di disponibilità indicato in precedenza. I nomi devono essere separati da una virgola e uno spazio (ad esempio: SQLVM1, SQLVM2). |
   | **Listener** | Nome DNS da assegnare al listener. Per impostazione predefinita, questo modello specifica il nome 'aglistener', ma il valore può essere modificato. Il nome non deve superare i 15 caratteri. |
   | **Listener Port** (Porta listener) | Porta da usare per il listener. In genere dovrebbe essere la porta predefinita 1433, di conseguenza questo è il numero di porta specificato dal modello. Tuttavia, se la porta predefinita è stata modificata, per la porta del listener occorre il valore corrispondente. | 
   | **Listener IP** (IP listener) | Indirizzo IP da usare per il listener.  Questo indirizzo IP verrà creato durante la distribuzione del modello, quindi specificare un indirizzo IP che non è già in uso.  |
   | **Existing Subnet** (Subnet esistente) | *Nome* della subnet interna delle VM di SQL Server (ad esempio: default). Questo valore può essere determinato passando al **Gruppo di risorse**, selezionando la **Rete virtuale**, selezionando **Subnet** nel riquadro **Impostazioni** e quindi copiando il valore in **Nome**. |
   | **Existing Internal Load Balancer** (Servizio di bilanciamento del carico interno esistente) | Nome dell'ILB creato nel passaggio 3. |
   | **Porta probe** | Porta probe da usare per l'ILB. Il modello usa 59999 per impostazione predefinita, ma questo valore può essere modificato. |
   | &nbsp; | &nbsp; |

1. Se si accettano i termini e le condizioni, selezionare la casella di controllo accanto a **Accetto le condizioni riportate sopra** e selezionare **Acquista** per completare la distribuzione del modello di avvio rapido. 
1. Per monitorare la distribuzione, selezionare la distribuzione dall'icona a forma di campanello **Notifiche** nella parte superiore della pagina oppure passare al **Gruppo di risorse** nel portale di Azure, selezionare **Distribuzioni** nel campo **Impostazioni** e scegliere la distribuzione 'Microsoft.Template'. 

   >[!NOTE]
   >Se la distribuzione si interrompe a metà, sarà necessario [rimuovere il listener appena creato](#remove-availability-group-listener) manualmente tramite PowerShell prima di ridistribuire il modello di avvio rapido **101-sql-vm-aglistener-setup**. 

## <a name="remove-availability-group-listener"></a>Rimuovere il listener del gruppo di disponibilità
Se in un secondo momento è necessario rimuovere il listener del gruppo di disponibilità configurato dal modello, è necessario passare attraverso il provider di risorse di macchine virtuali SQL. Poiché il listener è stato registrato tramite il provider di risorse di macchine virtuali SQL, eliminarlo semplicemente tramite SQL Server Management Studio non è sufficiente. Occorre eliminarlo tramite il provider di risorse di macchine virtuali SQL usando PowerShell. Questa operazione rimuove i metadati del listener AG dal provider di risorse di macchine virtuali SQL ed elimina fisicamente il listener dal gruppo di disponibilità. 

Il frammento di codice seguente elimina il listener del gruppo di disponibilità SQL sia dal provider di risorse SQL che dal gruppo di disponibilità: 

```powershell
# Remove the AG listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Errori comuni
Questa sezione descrive alcuni problemi noti e le possibili risoluzioni. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>Il listener del gruppo di disponibilità per il gruppo di disponibilità '\<Nome-AG>' esiste già
Il gruppo di disponibilità selezionato usato nel modello di avvio rapido di Azure per listener AG contiene già un listener, fisicamente all'interno del gruppo di disponibilità o sotto forma di metadati all'interno del provider di risorse di macchine virtuali SQL.  Rimuovere il listener usando [PowerShell](#remove-availability-group-listener) prima di ridistribuire il modello di avvio rapido **101-sql-vm-aglistener-setup**. 

### <a name="connection-only-works-from-primary-replica"></a>La connessione funziona solo dalla replica primaria
Questo comportamento è probabilmente causato da una distribuzione non riuscita del modello **101-sql-vm-aglistener-setup**, che ha lasciato la configurazione dell'ILB in uno stato incoerente. Verificare che il pool back-end elenchi il set di disponibilità e che esistano regole per il probe di integrità e per le regole di bilanciamento del carico. Se manca qualche elemento, la configurazione dell'ILB è in uno stato incoerente. 

Per risolvere questo problema, rimuovere il listener usando [PowerShell](#remove-availability-group-listener), eliminare il servizio di bilanciamento del carico interno tramite il portale di Azure e ricominciare dal Passaggio 3. 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>Richiesta non valida - È possibile aggiornare solo l'elenco di macchine virtuali SQL
Questo errore può verificarsi quando si distribuisce il modello **101-sql-vm-aglistener-setup** se il listener è stato eliminato tramite SQL Server Management Studio (SSMS), ma non è stato eliminato dal provider di risorse di macchine virtuali SQL. L'eliminazione del listener tramite SQL Server Management Studio non rimuove i metadati del listener dal provider di risorse di macchine virtuali SQL. Il listener deve essere eliminato dal provider di risorse usando [PowerShell](#remove-availability-group-listener). 

### <a name="domain-account-does-not-exist"></a>L'account di dominio non esiste
Questo errore può essere causato da uno di due motivi. L'account di dominio specificato non esiste davvero o mancano i dati del [nome dell'entità utente (UPN)](/windows/desktop/ad/naming-properties#userprincipalname). Il modello **101-sql-vm-ag-setup** prevede un account di dominio nel formato UPN (ad esempio user@domain.com), ma potrebbe mancare in alcuni account di dominio. Ciò può verificarsi in genere quando non viene eseguita la migrazione di un utente locale come primo account di amministratore di dominio quando il server viene alzato di livello a controller di dominio o quando un utente viene creato tramite PowerShell. 

 Verificare che l'account esista. Se esiste, potrebbe trattarsi del secondo caso. Per risolvere il problema, seguire questa procedura:

1. Nel controller di dominio aprire la finestra **Utenti e computer di Active Directory** dall'opzione **Strumenti** in **Server Manager**. 
2. Passare all'account selezionando **Utenti** nel riquadro sinistro.
3. Fare clic con il pulsante destro del mouse sull'account desiderato e scegliere **Proprietà**.
4. Selezionare la scheda **Account** e verificare se **Nome accesso utente** è vuoto. In caso affermativo, questa è la causa dell'errore. 

    ![Un account utente vuoto indica un UPN mancante](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. Immettere il **Nome accesso utente** corrispondente al nome dell'utente e quindi selezionare il dominio appropriato nell'elenco a discesa. 
6. Selezionare **Applica** per salvare le modifiche e chiudere la finestra di dialogo selezionando **OK**. 

   Dopo aver apportato queste modifiche, tentare di distribuire il modello di avvio rapido di Azure ancora una volta. 



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Cos'è SQL Server in Macchine virtuali di Azure?](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti su SQL Server in esecuzione in macchine virtuali Windows in Azure](virtual-machines-windows-sql-server-iaas-faq.md)
* [Guida ai prezzi per le VM di SQL Server in Azure](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Note sulla versione di SQL Server in una macchina virtuale Azure](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Come cambiare il modello di licenza per una macchina virtuale SQL Server in Azure](virtual-machines-windows-sql-ahb.md)



