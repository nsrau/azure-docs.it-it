---
title: Creare il cluster WSFC, il listener e configurare il servizio di bilanciamento del carico interno per un gruppo di disponibilità Always On in una macchina virtuale (VM) di SQL Server con il modello di avvio rapido di Azure
description: Usare i modelli di avvio rapido di Azure per semplificare il processo di creazione di gruppi di disponibilità per VM di SQL Server in Azure usando un modello per creare il cluster, aggiungere VM di SQL Server al cluster, creare il listener e configurare il servizio di bilanciamento del carico interno.
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
ms.date: 01/04/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 9be8717bc9b1d15a59486edf206dd0657a711c06
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360485"
---
# <a name="create-wsfc-listener-and-configure-ilb-for-an-always-on-availability-group-on-a-sql-server-vm-with-azure-quickstart-template"></a>Creare il cluster WSFC, il listener e configurare il servizio di bilanciamento del carico interno per un gruppo di disponibilità Always On in una macchina virtuale (VM) di SQL Server con il modello di avvio rapido di Azure
Questo articolo descrive come usare i modelli di avvio rapido di Azure per automatizzare parzialmente la distribuzione di una configurazione di gruppo di disponibilità Always On per macchine virtuali di SQL Server in Azure. In questo processo vengono usati due modelli di avvio rapido di Azure. 

   | Modello | DESCRIZIONE |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Crea il cluster di failover di Windows e vi aggiunge le VM di SQL Server. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Crea il listener del gruppo di disponibilità e configura il servizio di bilanciamento del carico interno. |
   | &nbsp; | &nbsp; |

Altre parti della configurazione del gruppo di disponibilità devono essere eseguite manualmente, ad esempio la creazione del gruppo di disponibilità e del servizio di bilanciamento del carico interno. Questo articolo illustra la sequenza dei passaggi automatizzati e manuali.
 

## <a name="prerequisites"></a>Prerequisiti 
Per automatizzare la configurazione di un gruppo di disponibilità Always On usando i modelli di avvio rapido sono necessari i prerequisiti seguenti: 
- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Un gruppo di risorse con un [controller di dominio](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-forest). 
- Una o più [macchine virtuali in Azure che eseguono SQL Server 2016 (o superiore) Enterprise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) aggiunte al dominio, nella stessa zona di disponibilità o set di disponibilità[ registrato con il provider di risorse di macchine virtuali SQL](#register-existing-sql-vm-with-new-resource-provider).  

## <a name="register-existing-sql-vm-with-new-resource-provider"></a>Registrare una macchina virtuale SQL esistente con un nuovo provider di risorse
Poiché questi modelli di avvio rapido di Azure per gruppi di disponibilità si basano sul provider di risorse di macchine virtuali SQL (Microsoft.SqlVirtualMachine), le VM di SQL Server esistenti devono essere registrate con tale provider. Ignorare questo passaggio se la VM di SQL Server è stata creato dopo il dicembre 2018, in quanto tutte le VM di SQL Server create dopo questa data vengono registrate automaticamente. In questa sezione viene descritta la procedura per la registrazione con il provider tramite il portale di Azure, ma è anche possibile usare [PowerShell](virtual-machines-windows-sql-ahb.md#powershell). 

  >[!IMPORTANT]
  > Se si rilascia la risorsa della VM di SQL Server, si tornerà all'impostazione della licenza hardcoded dell'immagine. 

1. Accedere al Portale di Azure e passare a **Tutti i servizi**. 
1. Passare a **Sottoscrizioni** e selezionare la sottoscrizione di interesse.  
1. Nel pannello **Sottoscrizioni**, passare a **Provider di risorse**. 
1. Digitare `sql` nel filtro per visualizzare i provider di risorse correlati a SQL. 
1. Selezionare uno tra *Registra*, *Ripeti registrazione*, o *Annulla registrazione* per il provider **Microsoft.SqlVirtualMachine** in base all'azione desiderata. 

  ![Modificare il provider](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

## <a name="step-1---create-the-wsfc-and-join-sql-server-vms-to-the-cluster-using-quickstart-template"></a>Passaggio 1: Creare il cluster WSFC e aggiungere VM di SQL Server al cluster usando il modello di avvio rapido 
Dopo aver registrato le VM di SQL Server con il nuovo provider di risorse di macchine virtuali SQL, è possibile aggiungerle in *SqlVirtualMachineGroup*. Questa risorsa definisce i metadati del cluster di failover di Windows, tra cui versione, edizione, nome di dominio completo, account AD per gestire il cluster e account di archiviazione come cloud di controllo. L'aggiunta della VM di SQL Server a *SqlVirtualMachineGroup* esegue il bootstrap del servizio Cluster di failover di Windows e unisce le VM di SQL Server al cluster. Questo passaggio viene automatizzato con il modello di avvio rapido **101-sql-vm-ag-setup** e può essere implementato con i passaggi seguenti:

1. Passare al modello di avvio rapido [**101-sql-vm-ag-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) e selezionare **Deploy to Azure** (Distribuisci in Azure) per avviare il modello nel portale di Azure.
1. Compilare i campi obbligatori per configurare i metadati del cluster di failover di Windows. I campi facoltativi possono essere lasciati vuoti.

    La tabella seguente mostra i valori necessari per il modello: 

   | **Campo** | Valore |
   | --- | --- |
   | **Sottoscrizione** |  Sottoscrizione in cui si trovano le VM di SQL Server. |
   |**Gruppo di risorse** | Gruppo di risorse in cui si trovano le VM di SQL Server. | 
   |**Failover Cluster Name** (Nome cluster di failover) | Nome desiderato per il nuovo cluster di failover di Windows. |
   | **Existing Vm List** (Elenco VM esistenti) | VM di SQL Server da includere nel gruppo di disponibilità e che, di conseguenza, faranno parte di questo nuovo cluster. Separare i valori con una virgola e uno spazio (ad esempio: SQLVM1, SQLVM2). |
   | **SQL Server Version** (Versione di SQL Server) | Selezionare la versione di SQL Server delle VM di SQL Server dall'elenco a discesa. Attualmente sono supportate solo le immagini di SQL 2016 e SQL 2017. |
   | **Existing Fully Qualified Domain Name** (Nome di dominio completo esistente) | FQDN esistente per il dominio in cui si trovano le VM di SQL Server. |
   | **Existing Domain Account** (Account di dominio esistente) | Account di dominio esistente con accesso sysadmin a SQL Server. | 
   | **Domain Account Password** (Password account di dominio) | Password dell'account di dominio indicato in precedenza. | 
   | **Existing Sql Service Account** (Account del servizio SQL Server esistente) | Account utente di dominio che viene usato per controllare il servizio SQL Server. Queste informazioni sono reperibili usando [**Gestione configurazione SQL Server**](https://docs.microsoft.com/sql/relational-databases/sql-server-configuration-manager?view=sql-server-2017). |
   | **Sql Service Password** (Password del servizio SQL) | Password dell'account utente di dominio che controlla il servizio SQL Server. |
   | &nbsp; | &nbsp; |

1. Se si accettano i termini e le condizioni, selezionare la casella di controllo accanto a **Accetto le condizioni riportate sopra** e selezionare **Acquista** per completare la distribuzione del modello di avvio rapido. 
1. Per monitorare la distribuzione, selezionare la distribuzione dall'icona a forma di campanello **Notifiche** nella parte superiore della pagina oppure passare al **Gruppo di risorse** nel portale di Azure, selezionare **Distribuzioni** nel campo **Impostazioni** e scegliere la distribuzione 'Microsoft.Template'. 

## <a name="step-2---manually-create-the-availability-group"></a>Passaggio 2: Creare manualmente il gruppo di disponibilità 
Creare manualmente il gruppo di disponibilità come di consueto, usando [PowerShell](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell?view=sql-server-2017), [SQL Server Management Studio](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio?view=sql-server-2017) oppure [Transact-SQL](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql?view=sql-server-2017). 

  >[!IMPORTANT]
  > **Non** creare un listener in questo momento perché la procedura viene automatizzata dal modello di avvio rapido **101-sql-vm-aglistener-setup** nel passaggio 4. 

## <a name="step-3---manually-create-the-internal-load-balancer-ilb"></a>Passaggio 3: Creare manualmente il servizio di bilanciamento del carico interno (ILB)
Il listener del gruppo di disponibilità (AG) Always On richiede un'istanza del servizio Azure Load Balancer interno (ILB). L'ILB fornisce un indirizzo IP mobile per il listener del gruppo di disponibilità che consente il failover e la riconnessione più veloci. Se le VM di SQL Server in un gruppo di disponibilità fanno parte dello stesso set di disponibilità, è possibile usare un servizio Load Balancer Basic. In caso contrario, è necessario usare un Load Balancer Standard.  **L'ILB deve trovarsi nella stessa rete virtuale delle istanze di VM di SQL Server.** Occorre creare solo l'ILB. Il resto della configurazione, ad esempio il pool back-end, il probe di integrità e le regole di bilanciamento del carico, viene gestita dal modello di avvio rapido **101-sql-vm-aglistener-setup** nel passaggio 4. 

1. Nel portale di Azure aprire il gruppo di risorse contenente le macchine virtuali di SQL Server. 
2. Nel gruppo di risorse fare clic su **Aggiungi**.
3. Nei risultati della ricerca cercare il **bilanciamento del carico** e selezionare **Servizio di bilanciamento del carico**, pubblicato da **Microsoft**.
4. Nel pannello **Servizio di bilanciamento del carico** fare clic su**Crea**.
5. Bella finestra di dialogo **Crea servizio di bilanciamento del carico** configurare il servizio di bilanciamento del carico come segue:

   | Impostazione | Valore |
   | --- | --- |
   | **Nome** |Nome che rappresenta il servizio di bilanciamento del carico. Ad esempio **sqlLB**. |
   | **Tipo** |**Interna**: La maggior parte delle implementazioni usa un servizio di bilanciamento del carico interno, che consente alle applicazioni all'interno della stessa rete virtuale di connettersi al gruppo di disponibilità.  </br> **Esterna**: consente alle applicazioni di connettersi al gruppo di disponibilità tramite una connessione Internet pubblica. |
   | **Rete virtuale** |Selezionare la rete virtuale in cui si trovano le istanze di SQL Server. |
   | **Subnet** |Selezionare la subnet in cui si trovano le istanze di SQL Server. |
   | **Assegnazione indirizzi IP** |**Statico** |
   | **Indirizzo IP privato** |Specificare un indirizzo IP disponibile della subnet. che verrà usato quando si creerà un listener nel cluster.|
   | **Sottoscrizione** |Se si hanno più sottoscrizioni, può essere visualizzato questo campo. Selezionare la sottoscrizione da associare a questa risorsa. In genere è la stessa sottoscrizione di tutte le risorse del gruppo di disponibilità. |
   | **Gruppo di risorse** |Selezionare il gruppo di risorse in cui si trovano le istanze di SQL Server. |
   | **Posizione** |Selezionare il percorso di Azure in cui si trovano le istanze di SQL Server. |
   | &nbsp; | &nbsp; |

6. Selezionare **Create**. 


  >[!NOTE]
  > La risorsa IP pubblico per ogni VM di SQL Server deve avere uno SKU standard per essere compatibile con il Load Balancer Standard. Per determinare lo SKU della risorsa IP pubblico della VM, passare a **Gruppo di risorse**, selezionare la risorsa **Indirizzo IP pubblico** per la VM di SQL Server desiderata e individuare il valore sotto **SKU**  nel pannello **Panoramica**. 

## <a name="step-4---create-the-ag-listener-and-configure-the-ilb-with-the-quickstart-template"></a>Passaggio 4: Creare il listener AG e configurare l'ILB con il modello di avvio rapido

Creare il listener del gruppo di disponibilità e configurare automaticamente il servizio di bilanciamento del carico interno (ILB) con il modello di avvio rapido **101-sql-vm-aglistener-setup**, che effettua il provisioning della risorsa Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener. Il modello di avvio rapido**101-sql-vm-aglistener-setup**, tramite il provider di risorse di macchine virtuali SQL, esegue le azioni seguenti:

 - Configura le impostazioni di rete per il cluster e l'ILB. 
 - Configura il pool back-end dell'ILB, il probe di integrità e le regole di bilanciamento del carico.
 - Crea il listener AG con il nome e l'indirizzo IP specificati.

Per configurare l'ILB e creare il listener AG, eseguire le operazioni seguenti:
1. Passare al modello di avvio rapido [**101-sql-vm-aglistener-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) e selezionare **Deploy to Azure** (Distribuisci in Azure) per avviare il modello di avvio rapido nel portale di Azure.
1. Compilare i campi obbligatori per configurare l'ILB e creare il listener del gruppo di disponibilità. I campi facoltativi possono essere lasciati vuoti. 

    La tabella seguente mostra i valori necessari per il modello: 

   | **Campo** | Valore |
   | --- | --- |
   |**Gruppo di risorse** | Gruppo di risorse in cui si trovano le VM di SQL Server e il gruppo di disponibilità. | 
   |**Existing Failover Cluster Name** (Nome cluster di failover esistente) | Nome del cluster a cui sono aggiunte le VM di SQL Server. |
   | **Existing Sql Availability Group** (Gruppo di disponibilità SQL esistente)| Nome del gruppo di disponibilità di cui fanno parte le VM di SQL Server |
   | **Existing Vm List** (Elenco VM esistenti) | Nomi delle VM di SQL Server che fanno parte del gruppo di disponibilità indicato in precedenza. I nomi devono essere separati da una virgola e uno spazio (ad esempio: SQLVM1, SQLVM2). |
   | **Existing Fully Qualified Domain Name** (Nome di dominio completo esistente) | FQDN esistente per il dominio in cui si trovano le VM di SQL Server. |
   | **Listener** | Nome DNS da assegnare al listener. Per impostazione predefinita, questo modello specifica il nome 'aglistener', ma il valore può essere modificato. |
   | **Listener Port** (Porta listener) | Porta da usare per il listener. In genere dovrebbe essere la porta predefinita 1433, di conseguenza questo è il numero di porta specificato dal modello. Tuttavia, se la porta predefinita è stata modificata, per la porta del listener occorre il valore corrispondente. | 
   | **Existing Vnet** (Rete virtuale esistente) | Nome della rete virtuale in cui si trovano le VM di SQL Server e l'ILB. |
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

```PowerShell
# Remove the AG listener
# example: Remove-AzureRmResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzureRmResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="known-issues-and-errors"></a>Problemi ed errori noti
Questa sezione descrive alcuni problemi noti e le possibili risoluzioni. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>Il listener del gruppo di disponibilità per il gruppo di disponibilità '\<Nome-AG>' esiste già
Il gruppo di disponibilità selezionato usato nel modello di avvio rapido di Azure per listener AG contiene già un listener, fisicamente all'interno del gruppo di disponibilità o sotto forma di metadati all'interno del provider di risorse di macchine virtuali SQL.  Rimuovere il listener usando [PowerShell](#remove-availability-group-listener) prima di ridistribuire il modello di avvio rapido **101-sql-vm-aglistener-setup**. 

### <a name="connection-only-works-from-primary-replica"></a>La connessione funziona solo dalla replica primaria
Questo comportamento è probabilmente causato da una distribuzione non riuscita del modello **101-sql-vm-aglistener-setup**, che ha lasciato la configurazione dell'ILB in uno stato incoerente. Verificare che il pool back-end elenchi il set di disponibilità e che esistano regole per il probe di integrità e per le regole di bilanciamento del carico. Se manca qualche elemento, la configurazione dell'ILB è in uno stato incoerente. 

Per risolvere questo problema, rimuovere il listener usando [PowerShell](#remove-availability-group-listener), eliminare il servizio di bilanciamento del carico interno tramite il portale di Azure e ricominciare dal [Passaggio 3](#step-3---manually-create-the-internal-load-balanced-ilb). 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>Richiesta non valida - È possibile aggiornare solo l'elenco di macchine virtuali SQL
Questo errore può verificarsi quando si distribuisce il modello **101-sql-vm-aglistener-setup** se il listener è stato eliminato tramite SQL Server Management Studio (SSMS), ma non è stato eliminato dal provider di risorse di macchine virtuali SQL. L'eliminazione del listener tramite SQL Server Management Studio non rimuove i metadati del listener dal provider di risorse di macchine virtuali SQL. Il listener deve essere eliminato dal provider di risorse usando [PowerShell](#remove-availability-group-listener). 


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Cos'è SQL Server in Macchine virtuali di Azure?](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti su SQL Server in esecuzione in macchine virtuali Windows in Azure](virtual-machines-windows-sql-server-iaas-faq.md)
* [Guida ai prezzi per le VM di SQL Server in Azure](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Note sulla versione di SQL Server in una macchina virtuale Azure](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Come cambiare il modello di licenza per una macchina virtuale SQL Server in Azure](virtual-machines-windows-sql-ahb.md)



