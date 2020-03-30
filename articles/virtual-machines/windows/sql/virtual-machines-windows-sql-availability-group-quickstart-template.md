---
title: Configurare il gruppo di disponibilità (modello di guida introduttiva di Azure)Configure availability group (Azure quickstart template)
description: Usare i modelli delle guide rapide di Azure per creare il cluster di failover di Windows, aggiungere le macchine virtuali di SQL Server al cluster, creare il listener e configurare il servizio di bilanciamento del carico interno in Azure.Use Azure quickstart templates to create the Windows failover cluster, join SQL Server VMs to the cluster, create the listener, and configure the internal load balancer in Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: edf810dfc975eebaf261eac7b89106c9e29c759c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022375"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-an-azure-vm"></a>Usare i modelli delle guide rapide di Azure per configurare un gruppo di disponibilità per SQL Server in una macchina virtuale di AzureUse Azure quickstart templates to configure an availability group for SQL Server on an Azure VM
Questo articolo descrive come usare i modelli di avvio rapido di Azure per automatizzare parzialmente la distribuzione di una configurazione del gruppo di disponibilità AlwaysOn AlwaysOn per le macchine virtuali di SQL Server in Azure.This article describes how to use the Azure quickstart templates to partially automate the deployment of an Always On availability group configuration for SQL Server virtual machines in Azure. In questo processo vengono usati due modelli di guida rapida di Azure:Two Azure quickstart templates are used in this process: 

   | Modello | Descrizione |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Crea il cluster di failover di Windows e vi aggiunge le macchine virtuali di SQL Server. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Crea il listener del gruppo di disponibilità e configura il servizio di bilanciamento del carico interno. Questo modello può essere utilizzato solo se il cluster di failover di Windows è stato creato con il modello **101-sql-vm-ag-setup.** |
   | &nbsp; | &nbsp; |

Altre parti della configurazione del gruppo di disponibilità devono essere eseguite manualmente, ad esempio la creazione del gruppo di disponibilità e la creazione del servizio di bilanciamento del carico interno. Questo articolo illustra la sequenza dei passaggi automatizzati e manuali.
 

## <a name="prerequisites"></a>Prerequisiti 
Per automatizzare l'installazione di un gruppo di disponibilità AlwaysOnAlways On availability group utilizzando i modelli di avvio rapido, è necessario disporre dei prerequisiti seguenti:To automate the setup of an Always On availability group by using quickstart templates, you must have the following prerequisites: 
- Una [sottoscrizione di Azure.](https://azure.microsoft.com/free/)
- Un gruppo di risorse con un controller di dominio. 
- Una o più macchine virtuali aggiunte al dominio [in Azure che esegue SQL Server 2016 (o versione successiva) Enterprise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) che si trovano nello stesso set di disponibilità o zona di disponibilità e che sono state registrate con il provider di risorse della macchina virtuale [SQL.](virtual-machines-windows-sql-register-with-resource-provider.md)  
- Due indirizzi IP disponibili (non utilizzati da alcuna entità): uno per il servizio di bilanciamento del carico interno e uno per il listener del gruppo di disponibilità all'interno della stessa subnet del gruppo di disponibilità. Se viene utilizzato un servizio di bilanciamento del carico esistente, è necessario un solo indirizzo IP disponibile.  

## <a name="permissions"></a>Autorizzazioni
Le autorizzazioni seguenti sono necessarie per configurare il gruppo di disponibilità AlwaysOn usando i modelli delle guide rapide di Azure:The following permissions are necessary to configure the Always On availability group by using Azure quickstart templates: 

- Un account utente di dominio esistente che dispone dell'autorizzazione **Crea oggetto computer** nel dominio.  Ad esempio, un account amministratore di dominio account@domain.comdispone in genere di autorizzazioni sufficienti (ad esempio: ). _Questo account deve anche fare parte del gruppo degli amministratori locali in ogni macchina virtuale per creare il cluster._
- Account utente di dominio che controlla il servizio SQL Server. 


## <a name="step-1-create-the-failover-cluster-and-join-sql-server-vms-to-the-cluster-by-using-a-quickstart-template"></a>Passaggio 1: Creare il cluster di failover e aggiungere le macchine virtuali di SQL Server al cluster usando un modello di avvio rapidoStep 1: Create the failover cluster and join SQL Server VMs to the cluster by using a quickstart template 
Dopo che le macchine virtuali di SQL Server sono state registrate con il provider di risorse della macchina virtuale SQL, è possibile aggiungere le macchine virtuali di SQL Server a *SqlVirtualMachineGroups*. Questa risorsa definisce i metadati del cluster di failover di Windows.This resource defines the metadata of the Windows failover cluster. I metadati includono la versione, l'edizione, il nome di dominio completo, gli account di Active Directory per gestire sia il cluster che il servizio SQL Server e l'account di archiviazione come cloud di controllo. 

L'aggiunta delle macchine virtuali di SQL Server al gruppo di risorse *SqlVirtualMachineGroups* esegue il bootstrap del servizio Cluster di failover di Windows per creare il cluster e quindi aggiunge le macchine virtuali di SQL Server a tale cluster. Questo passaggio è automatizzato con il modello di avvio rapido **101-sql-vm-ag-setup.** È possibile implementarlo attenendosi alla procedura seguente:

1. Passare al modello di avvio rapido [**101-sql-vm-ag-setup.**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) Selezionare **Quindi Distribuisci in Azure** per aprire il modello di avvio rapido nel portale di Azure.Then, select Deploy to Azure to open the quickstart template in the Azure portal.
1. Compilare i campi obbligatori per configurare i metadati per il cluster di failover di Windows. È possibile lasciare vuoti i campi facoltativi.

   La tabella seguente mostra i valori necessari per il modello: 

   | **Campo** | valore |
   | --- | --- |
   | **Sottoscrizione** |  Sottoscrizione in cui si trovano le VM di SQL Server. |
   |**Gruppo di risorse** | Gruppo di risorse in cui si trovano le VM di SQL Server. | 
   |**Failover Cluster Name** (Nome cluster di failover) | Il nome desiderato per il nuovo cluster di failover di Windows. |
   | **Existing Vm List** (Elenco VM esistenti) | Le macchine virtuali di SQL Server che si desidera partecipare al gruppo di disponibilità e far parte di questo nuovo cluster. Separare questi valori con una virgola e uno spazio (ad esempio: *SQLVM1, SQLVM2*). |
   | **SQL Server Version** (Versione di SQL Server) | La versione di SQL Server delle macchine virtuali di SQL Server. Selezionarlo dall'elenco a discesa. Attualmente sono supportate solo le immagini di SQL Server 2016 e SQL Server 2017. |
   | **Existing Fully Qualified Domain Name** (Nome di dominio completo esistente) | FQDN esistente per il dominio in cui si trovano le VM di SQL Server. |
   | **Existing Domain Account** (Account di dominio esistente) | Un account utente di dominio esistente con l'autorizzazione **Crea oggetto computer** nel dominio come [cNO](/windows-server/failover-clustering/prestage-cluster-adds) viene creato durante la distribuzione del modello. Ad esempio, un account amministratore di dominio account@domain.comdispone in genere di autorizzazioni sufficienti (ad esempio: ). *Questo account deve anche fare parte del gruppo degli amministratori locali in ogni macchina virtuale per creare il cluster.*| 
   | **Domain Account Password** (Password account di dominio) | Password dell'account utente di dominio indicato in precedenza. | 
   | **Existing Sql Service Account** (Account del servizio SQL Server esistente) | Account utente di dominio che controlla il [servizio SQL ServerSQL Server](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) durante la distribuzione del gruppo di disponibilità, ad esempio: account@domain.com). |
   | **Sql Service Password** (Password del servizio SQL) | Password dell'account utente di dominio che controlla il servizio SQL Server. |
   | **Cloud Witness Name** (Nome cloud di controllo) | Un nuovo account di archiviazione di Azure che verrà creato e usato per il cloud di controllo. È possibile modificare questo nome. |
   | **\_artefatti Posizione** | Questo campo è impostato per impostazione predefinita e non deve essere modificato. |
   | **\_Artifacts Posizione Sas Token** | Questo campo viene lasciato intenzionalmente vuoto. |
   | &nbsp; | &nbsp; |

1. Se si accettano i termini e le condizioni, selezionare la casella di controllo **Accetto i termini e le condizioni sopra indicati.** Selezionare **quindi Acquista** per completare la distribuzione del modello di avvio rapido. 
1. Per monitorare la distribuzione, selezionare la distribuzione dall'icona a campana Notifiche nell'intestazione di spostamento superiore oppure passare a **Gruppo di risorse** nel portale di Azure.To monitor your deployment, either select the deployment from the **Notifications** bell icon in the top navigation banner or go to Resource Group in the Azure portal. Selezionare Distribuzioni in Impostazioni e scegliere la distribuzione **Microsoft.Template.Select** **Deployments** under **Settings**, and choose the Microsoft.Template deployment. 

>[!NOTE]
> Le credenziali fornite durante la distribuzione del modello vengono archiviate solo per la durata della distribuzione. Al termine della distribuzione, tali password vengono rimosse. Verrà chiesto di fornirli nuovamente se si aggiungono altre macchine virtuali di SQL Server al cluster. 


## <a name="step-2-manually-create-the-availability-group"></a>Passaggio 2: Creare manualmente il gruppo di disponibilitàStep 2: Manually create the availability group 
Creare manualmente il gruppo di disponibilità come di consueto, utilizzando [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)o [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> Non creare un listener in questo momento, perché il modello di avvistamento automatico 101-sql-vm-aglistener-setup esegue automaticamente nel passaggio 4.Do *not* create a listener at this time, because the **101-sql-vm-aglistener-setup** quickstart template does that in step 4. 

## <a name="step-3-manually-create-the-internal-load-balancer"></a>Passaggio 3: Creare manualmente il servizio di bilanciamento del carico internoStep 3: Manually create the internal load balancer
Il listener del gruppo di disponibilità AlwaysOnAlways On availability group listener richiede un'istanza interna di Azure Load Balancer.The Always On availability group listener requires an internal instance of Azure Load Balancer. Il servizio di bilanciamento del carico interno fornisce un indirizzo IP "mobile" per il listener del gruppo di disponibilità che consente un failover e una riconnessione più rapidi. Se le macchine virtuali di SQL Server in un gruppo di disponibilità fanno parte dello stesso set di disponibilità, è possibile usare un servizio di bilanciamento del carico di base. In caso contrario, è necessario usare un servizio di bilanciamento del carico Standard.Otherwise, you need to use a Standard load balancer. 

> [!IMPORTANT]
> Il servizio di bilanciamento del carico interno deve trovarsi nella stessa rete virtuale delle istanze della macchina virtuale di SQL Server.The internal load balancer should be in the same virtual network as the SQL Server VM instances. 

È sufficiente creare il servizio di bilanciamento del carico interno. Nel passaggio 4, il modello di guida introduttiva **101-sql-vm-aglistener-setup** gestisce il resto della configurazione, ad esempio il pool back-end, il probe di integrità e le regole di bilanciamento del carico. 

1. Nel portale di Azure aprire il gruppo di risorse contenente le macchine virtuali di SQL Server. 
2. Nel gruppo di risorse selezionare **Aggiungi**.
3. Cercare **servizio di bilanciamento del carico**. Nei risultati della ricerca selezionare **Load Balancer**, pubblicato da **Microsoft**.
4. Nel pannello **Bilanciamento del carico** selezionare Crea .On the Load Balancer blade, select **Create**.
5. Bella finestra di dialogo **Crea servizio di bilanciamento del carico** configurare il servizio di bilanciamento del carico come segue:

   | Impostazione | valore |
   | --- | --- |
   | **Nome** |Immettere un nome di testo che rappresenta il servizio di bilanciamento del carico. Ad esempio, immettere **sqlLB**. |
   | **Tipo** |**Interno**: la maggior parte delle implementazioni usano un servizio di bilanciamento del carico interno, che consente alle applicazioni all'interno della stessa rete virtuale di connettersi al gruppo di disponibilità.  </br> **Esterno:** consente alle applicazioni di connettersi al gruppo di disponibilità tramite una connessione Internet pubblica. |
   | **Rete virtuale** | Selezionare la rete virtuale in cui si trovano le istanze di SQL Server. |
   | **Subnet** | Selezionare la subnet in cui si trovano le istanze di SQL Server. |
   | **Assegnazione indirizzi IP** |**Statico** |
   | **Indirizzo IP privato** | Specificare un indirizzo IP disponibile della subnet. |
   | **Sottoscrizione** |Se si hanno più sottoscrizioni, può essere visualizzato questo campo. Selezionare la sottoscrizione da associare a questa risorsa. In genere è la stessa sottoscrizione di tutte le risorse per il gruppo di disponibilità. |
   | **Gruppo di risorse** |Selezionare il gruppo di risorse in cui si trovano le istanze di SQL Server. |
   | **Percorso** |Selezionare il percorso di Azure in cui si trovano le istanze di SQL Server. |
   | &nbsp; | &nbsp; |

6. Selezionare **Crea**. 


>[!IMPORTANT]
> La risorsa IP pubblica per ogni macchina virtuale di SQL Serversql Server deve avere uno SKU Standard per essere compatibile con il servizio di bilanciamento del carico Standard.The public IP resource for each SQL Server VM should have a Standard SKU to be compatible with the Standard load balancer. Per determinare lo SKU della risorsa IP pubblica della macchina virtuale, passare a **Gruppo di risorse**, selezionare la risorsa Indirizzo IP **pubblico** per la macchina virtuale di SQL Server e individuare il valore in **SKU** nel riquadro **Panoramica.** 

## <a name="step-4-create-the-availability-group-listener-and-configure-the-internal-load-balancer-by-using-the-quickstart-template"></a>Passaggio 4: Creare il listener del gruppo di disponibilità e configurare il servizio di bilanciamento del carico interno usando il modello di avvio rapidoStep 4: Create the availability group listener and configure the internal load balancer by using the quickstart template

Creare il listener del gruppo di disponibilità e configurare automaticamente il servizio di bilanciamento del carico interno usando il modello di avvio rapido per la configurazione di **101-sql-vm-aglistener.** Il modello esegue il provisioning della risorsa Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener. Il modello di avvio rapido**101-sql-vm-aglistener-setup**, tramite il provider di risorse di macchine virtuali SQL, esegue le azioni seguenti:

- Crea una nuova risorsa IP front-end (in base al valore di indirizzo IP specificato durante la distribuzione) per il listener. 
- Configura le impostazioni di rete per il cluster e il servizio di bilanciamento del carico interno. 
- Configura il pool back-end per il servizio di bilanciamento del carico interno, il probe di integrità e le regole di bilanciamento del carico.
- Crea il listener del gruppo di disponibilità con l'indirizzo IP e il nome forniti.
 
>[!NOTE]
> È possibile utilizzare **101-sql-vm-aglistener-setup** solo se il cluster di failover di Windows è stato creato con il modello **101-sql-vm-ag-setup.**
   
   
Per configurare il servizio di bilanciamento del carico interno e creare il listener del gruppo di disponibilità, eseguire le operazioni seguenti:To configure the internal load balancer and create the availability group listener, do the following:
1. Passare al modello di guida introduttiva 101-sql-vm-aglistener-setup e selezionare **Distribuisci in Azure** per avviare il modello di avvio rapido nel portale di Azure.Go to the [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) quickstart template and select Deploy to Azure to start the quickstart template in the Azure portal.
1. Compilare i campi obbligatori per configurare il servizio di bilanciamento del carico interno e creare il listener del gruppo di disponibilità. È possibile lasciare vuoti i campi facoltativi. 

   La tabella seguente mostra i valori necessari per il modello: 

   | **Campo** | valore |
   | --- | --- |
   |**Gruppo di risorse** | Gruppo di risorse in cui si trovano le VM di SQL Server e il gruppo di disponibilità. | 
   |**Existing Failover Cluster Name** (Nome cluster di failover esistente) | Nome del cluster a cui sono aggiunte le VM di SQL Server. |
   | **Existing Sql Availability Group** (Gruppo di disponibilità SQL esistente)| Nome del gruppo di disponibilità di cui fanno parte le VM di SQL Server |
   | **Existing Vm List** (Elenco VM esistenti) | Nomi delle VM di SQL Server che fanno parte del gruppo di disponibilità indicato in precedenza. Separare i nomi con una virgola e uno spazio (ad esempio: *SQLVM1, SQLVM2*). |
   | **Listener** | Il nome DNS che si desidera assegnare al listener. Per impostazione predefinita, questo modello specifica il nome "aglistener", ma è possibile modificarlo. Il nome non deve superare i 15 caratteri. |
   | **Listener Port** (Porta listener) | La porta che si desidera venga utilizzata dal listener. In genere, questa porta deve essere il valore predefinito 1433. Questo è il numero di porta specificato dal modello. Ma se la porta predefinita è stata modificata, la porta del listener deve usare tale valore. | 
   | **Listener IP** (IP listener) | L'indirizzo IP che si desidera venga utilizzato dal listener. Questo indirizzo verrà creato durante la distribuzione del modello, quindi specificarne uno che non sia già in uso.  |
   | **Existing Subnet** (Subnet esistente) | Nome della subnet interna delle macchine virtuali di SQL Server, ad esempio *default*. È possibile determinare questo valore accedendo a **Gruppo di risorse**, selezionando la rete virtuale, selezionando **Subnet** nel riquadro **Impostazioni** e copiando il valore in **Nome**. |
   | **Existing Internal Load Balancer** (Servizio di bilanciamento del carico interno esistente) | Nome del servizio di bilanciamento del carico interno creato nel passaggio 3.The name of the internal load balancer that you created in step 3. |
   | **Porta probe** | Porta probe che si desidera venga utilizzata dal servizio di bilanciamento del carico interno. Il modello utilizza 59999 per impostazione predefinita, ma è possibile modificare questo valore. |
   | &nbsp; | &nbsp; |

1. Se si accettano i termini e le condizioni, selezionare la casella di controllo **Accetto i termini e le condizioni sopra indicati.** Selezionare **Acquista** per completare la distribuzione del modello di avvio rapido. 
1. Per monitorare la distribuzione, selezionare la distribuzione dall'icona a campana Notifiche nell'intestazione di spostamento superiore oppure passare a **Gruppo di risorse** nel portale di Azure.To monitor your deployment, either select the deployment from the **Notifications** bell icon in the top navigation banner or go to Resource Group in the Azure portal. Selezionare Distribuzioni in Impostazioni e scegliere la distribuzione **Microsoft.Template.Select** **Deployments** under **Settings**, and choose the Microsoft.Template deployment. 

>[!NOTE]
>Se la distribuzione non riesce a metà, sarà necessario rimuovere manualmente [il listener appena creato](#remove-the-availability-group-listener) utilizzando PowerShell prima di ridistribuire il modello di avvio rapido per l'installazione di **101-sql-vm-aglistener.** 

## <a name="remove-the-availability-group-listener"></a>Rimuovere il listener del gruppo di disponibilitàRemove the availability group listener
Se in seguito è necessario rimuovere il listener del gruppo di disponibilità configurato dal modello, è necessario esaminare il provider di risorse della macchina virtuale SQL. Poiché il listener viene registrato tramite il provider di risorse della macchina virtuale SQL, è sufficiente eliminarlo tramite SQL Server Management StudioSQL Server Management Studio.Because the listener is registered through the SQL VM resource provider, just deleting it via SQL Server Management StudioSQL Server Management Studio is insufficient. 

The best method is to delete it through the SQL VM resource provider by using the following code snippet in PowerShell. In questo modo vengono rimossi i metadati del listener del gruppo di disponibilità dal provider di risorse della macchina virtuale SQL. Elimina inoltre fisicamente il listener dal gruppo di disponibilità. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Errori comuni
Questa sezione descrive alcuni problemi noti e le possibili risoluzioni. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>Il listener del gruppo di disponibilità per il gruppo di disponibilità '\<Nome-AG>' esiste già
Il gruppo di disponibilità selezionato usato nel modello di guida rapida di Azure per il listener del gruppo di disponibilità contiene già un listener. È fisicamente all'interno del gruppo di disponibilità o i relativi metadati rimangono all'interno del provider di risorse della macchina virtuale SQL. Rimuovere il listener tramite [PowerShell](#remove-the-availability-group-listener) prima di ridistribuire il modello di avvio rapido per l'installazione di **101-sql-vm-aglistener.** 

### <a name="connection-only-works-from-primary-replica"></a>La connessione funziona solo dalla replica primaria
Questo comportamento è probabilmente da una distribuzione del modello **101-sql-vm-aglistener-setup che** ha lasciato la configurazione del servizio di bilanciamento del carico interno in uno stato incoerente. Verificare che il pool back-end elenchi il set di disponibilità e che esistano regole per il probe di integrità e per le regole di bilanciamento del carico. Se manca qualcosa, la configurazione del servizio di bilanciamento del carico interno è uno stato incoerente. 

Per risolvere questo problema, rimuovere il listener tramite PowerShell , eliminare il servizio di bilanciamento del carico interno tramite il portale di Azure e ricominciare dal passaggio 3.To resolve this behavior, remove the listener by using [PowerShell](#remove-the-availability-group-listener), delete the internal load balancer via the Azure portal, and start again at step 3. 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>Richiesta non valida - È possibile aggiornare solo l'elenco di macchine virtuali SQL
Questo errore può verificarsi quando si distribuisce il modello **101-sql-vm-aglistener-setup** se il listener è stato eliminato tramite SQL Server Management Studio (SSMS), ma non è stato eliminato dal provider di risorse della macchina virtuale SQL. L'eliminazione del listener tramite SSMS non comporta la rimozione dei metadati del listener dal provider di risorse della macchina virtuale SQL. Il listener deve essere eliminato dal provider di risorse tramite [PowerShell.](#remove-the-availability-group-listener) 

### <a name="domain-account-does-not-exist"></a>L'account di dominio non esiste
Questo errore può avere due cause. L'account di dominio specificato non esiste o mancano i dati del [nome dell'entità utente (UPN).](/windows/desktop/ad/naming-properties#userprincipalname) Il modello **101-sql-vm-ag-setup** prevede un account di dominio nel *user@domain.com*modulo UPN (ovvero, ), ma in alcuni account di dominio potrebbe mancarlo. Ciò si verifica in genere quando è stata eseguita la migrazione di un utente locale per essere il primo account di amministratore di dominio quando il server è stato promosso a controller di dominio o quando un utente è stato creato tramite PowerShell.This is typically happens when a local user has been migrated to be the first domain administrator account when the server was promoted to a domain controller, or when a user was created through PowerShell. 

Verificare che l'account esista. Se lo fa, si potrebbe essere in esecuzione nella seconda situazione. Per risolverlo, eseguire le operazioni seguenti:

1. Nel controller di dominio aprire la finestra **Utenti e computer di Active Directory** dall'opzione **Strumenti** in **Server Manager**. 
2. Passare all'account selezionando **Utenti** nel riquadro sinistro.
3. Fare clic con il pulsante destro del mouse sull'account e scegliere **Proprietà**.
4. Selezionare la scheda **Account.** Se la casella **Nome accesso utente** è vuota, questa è la causa dell'errore. 

    ![Un account utente vuoto indica un UPN mancante](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. Compilare la casella **Nome accesso utente** in modo che corrisponda al nome dell'utente e selezionare il dominio corretto dall'elenco a discesa. 
6. Selezionare **Applica** per salvare le modifiche e chiudere la finestra di dialogo selezionando **OK**. 

Dopo aver apportato queste modifiche, provare a distribuire di nuovo il modello di avvio rapido di Azure.After you make these changes, try to deploy the Azure quickstart template once once. 



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica delle macchine virtuali di SQL ServerOverview of SQL Server VMs](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti sulle macchine virtuali di SQL ServerFAQ for SQL Server VMs](virtual-machines-windows-sql-server-iaas-faq.md)
* [Indicazioni sui prezzi per le macchine virtuali di SQL ServerPricing guidance for SQL Server VMs](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Note sulla versione per le macchine virtuali di SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Come cambiare il modello di licenza per una macchina virtuale SQL Server in Azure](virtual-machines-windows-sql-ahb.md)



