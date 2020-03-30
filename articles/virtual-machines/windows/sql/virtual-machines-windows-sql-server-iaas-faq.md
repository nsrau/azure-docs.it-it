---
title: Domande frequenti su SQL Server in macchine virtuali Windows in Azure| Microsoft Docs
description: Questo articolo offre risposta ad alcune domande frequenti sull'esecuzione di SQL Server in macchine virtuali di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: 3b73c329c3db54ba78db15ced8e919af4d4a45d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249737"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Domande frequenti su SQL Server in esecuzione in macchine virtuali Windows in Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Questo articolo fornisce le risposte ad alcune delle domande più comuni sull'esecuzione di [SQL Server in macchine virtuali Windows in Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Questo articolo descrive i problemi specifici di SQL Server in macchine virtuali Windows. Se si esegue SQL Server in macchine virtuali Linux, vedere le [domande frequenti su Linux](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>Immagini

1. **Quali immagini della raccolta di macchine virtuali di SQL Server sono disponibili?** 

   Azure mantiene le immagini delle macchine virtuali per tutte le principali versioni supportate di SQL Server in tutte le edizioni sia per Linux che per Windows. Per altre informazioni, vedere l’elenco completo di [immagini di macchine virtuali Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) e di [immagini di macchine virtuali Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **Le immagini della raccolta di macchine virtuali di SQL Server esistenti vengono aggiornate?**

   Ogni due mesi, le immagini di SQL Server nella raccolta di macchine virtuali vengono aggiornate con gli aggiornamenti di Linux e Windows più recenti. Per le immagini Windows, si includono tutti gli aggiornamenti contrassegnati come importanti in Windows Update, tra cui gli aggiornamenti di sicurezza e i Service Pack di SQL Server importanti. Per le immagini Linux si includono gli aggiornamenti di sistema più recenti. Gli aggiornamenti cumulativi di SQL Server vengono gestiti in modo diverso per Linux e Windows. Per Linux, gli aggiornamenti cumulativi di SQL Server sono inclusi nell'aggiornamento. Tuttavia al momento le macchine virtuali Windows non vengono aggiornate con gli aggiornamenti cumulativi di SQL Server o Windows Server.

1. **Le immagini di macchine virtuali di SQL Server possono essere rimosse dalla raccolta?**

   Sì. Azure mantiene solo un'immagine per ogni versione e per ogni edizione principale. Ad esempio, quando viene rilasciato un nuovo Service Pack di SQL Server, Azure aggiunge una nuova immagine alla raccolta per quel Service Pack. L'immagine di SQL Server del Service Pack precedente viene immediatamente rimossa dal portale di Azure. Tuttavia è ancora disponibile per il provisioning di PowerShell per i successivi tre mesi. Dopo tre mesi, l'immagine del Service Pack precedente non è più disponibile. Questi criteri di rimozione sono applicabili anche se una versione di SQL Server non è più supportata perché raggiunge la fine del ciclo di vita.


1. **È possibile distribuire un'immagine precedente di SQL Server che non è visibile nel portale di Azure?**

   Sì, con PowerShell. Per altre informazioni sulla distribuzione di macchine virtuali di SQL Server usando PowerShell, consultare [Come eseguire il provisioning di macchine virtuali di SQL Server con Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

1. **È possibile creare un'immagine generalizzata di Azure SQL Server Marketplace della macchina virtuale di SQL Server e usarla per distribuire le macchine virtuali?**

   Sì, ma è quindi necessario [registrare ogni macchina virtuale](virtual-machines-windows-sql-register-with-resource-provider.md) di SQL Server con il provider di risorse della macchina virtuale di SQL Server per gestire la macchina virtuale di SQL Server nel portale, nonché usare funzionalità quali l'applicazione automatica di patch e backup automatici. Quando si esegue la registrazione con il provider di risorse, è inoltre necessario specificare il tipo di licenza per ogni macchina virtuale di SQL Server.When registering with the resource provider, you will also need to specify the license type for each SQL Server VM. 

1. **È possibile usare il proprio disco rigido virtuale per distribuire una macchina virtuale di SQL Server?**

   Sì, ma è quindi necessario [registrare ogni macchina virtuale](virtual-machines-windows-sql-register-with-resource-provider.md) di SQL Server con il provider di risorse della macchina virtuale di SQL Server per gestire la macchina virtuale di SQL Server nel portale, nonché usare funzionalità quali l'applicazione automatica di patch e backup automatici.

1. **È possibile impostare configurazioni non visualizzate nella raccolta di macchine virtuali, ad esempio Windows 2008 R2 + SQL Server 2012?**

   No. Per le raccolte di macchine virtuali che includono SQL Server è necessario selezionare una delle immagini disponibili sia dal portale di Azure che tramite [PowerShell](virtual-machines-windows-ps-sql-create.md). Tuttavia, è possibile distribuire una macchina virtuale Windows e installare automaticamente SQL Server in esso. È quindi necessario [registrare la macchina virtuale](virtual-machines-windows-sql-register-with-resource-provider.md) di SQL Server con il provider di risorse della macchina virtuale di SQL Server per gestire la macchina virtuale di SQL Server nel portale, nonché utilizzare funzionalità quali l'applicazione automatica di patch e backup automatici. 


## <a name="creation"></a>Creazione

1. **Come si crea una macchina virtuale di Azure con SQL Server?**

   Il metodo più semplice consiste nel creare una macchina virtuale che include SQL Server.The easiest method is to create a Virtual Machine that includes SQL Server. Per un'esercitazione sull'iscrizione ad Azure e sulla creazione di una macchina virtuale di SQL Server dal portale, vedere Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure.For a tutorial on signing up for Azure and creating a SQL Server VM from the portal, see [Provision a SQL Server virtual machine in the Azure portal.](virtual-machines-windows-portal-sql-server-provision.md) È possibile selezionare un'immagine di macchina virtuale che usa la licenza di SQL Server con costo al secondo oppure usare un'immagine che consente di trasferire la licenza di SQL Server dell'utente. È anche possibile installare manualmente in una macchina virtuale un'edizione di SQL Server con licenza gratuita (Developer o Express) o riutilizzando una licenza locale. Assicurarsi di [registrare la macchina virtuale](virtual-machines-windows-sql-register-with-resource-provider.md) di SQL Server con il provider di risorse della macchina virtuale di SQL Server per gestire la macchina virtuale di SQL Server nel portale, nonché utilizzare funzionalità quali l'applicazione automatica di patch e backup automatici. Se si usa la funzionalità Bring Your Own License, è necessario avere [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/). Per altre informazioni, vedere [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Guida ai prezzi per le VM di SQL Server in Azure).

1. **Come si esegue la migrazione di un database SQL Server locale nel cloud?**

   Creare prima una macchina virtuale di Azure con un'istanza di SQL Server, quindi eseguire la migrazione dei database locali in tale istanza. Per alcune strategie di migrazione dei dati, vedere [Eseguire la migrazione di un database di SQL Server a SQL Server in una macchina virtuale di Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Gestione delle licenze

1. **Come si installa una copia di SQL Server con licenza in una VM di Azure?**

   Ci sono tre modi per farlo. Se si è un cliente Enterprise Agreement (EA), è possibile eseguire il provisioning di una delle immagini della [macchina virtuale che supporta le licenze,](virtual-machines-windows-sql-server-iaas-overview.md#BYOL)nota anche come BYOL (Bring Your Own License). Se si dispone di [software assurance,](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)è possibile abilitare il [vantaggio Azure Hybrid](virtual-machines-windows-sql-ahb.md) in un'immagine PAYG (PayG) con pagamento in base al cliente. In alternativa, è possibile copiare il supporto di installazione di SQL Server in una macchina virtuale di Windows Server e quindi installare SQL Server nella macchina virtuale. Assicurarsi di registrare la macchina virtuale di SQL Server con il provider di [risorse](virtual-machines-windows-sql-register-with-resource-provider.md) per funzionalità quali la gestione del portale, il backup automatizzato e l'applicazione automatica di patch. 

1. **È possibile modificare una VM per l'uso di una licenza di SQL Server, se è stata creata da una delle immagini della raccolta con pagamento in base al consumo?**

   Sì. È possibile passare facilmente un'immagine della raccolta con pagamento in base al client (PAYG) per portare la propria licenza (BYOL) abilitando il [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/faq/).  Per altre informazioni, vedere [Come cambiare il livello di licenza per una macchina virtuale SQL Server](virtual-machines-windows-sql-ahb.md). Questa funzionalità è attualmente disponibile solo per i clienti del cloud pubblico.

1. **Per cambiare modello di licenza sono necessari tempi di inattività di SQL Server?**

   No. Per [modificare il modello di licenza](virtual-machines-windows-sql-ahb.md) non è necessario alcun tempo di inattività di SQL Server perché la modifica ha effetto immediato e non richiede un riavvio della macchina virtuale. Tuttavia, per registrare la macchina virtuale di SQL Server con il provider di risorse della macchina virtuale di SQL Server, [l'estensione SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) è un prerequisito e l'installazione dell'estensione sql IaaS in modalità _completa_ riavvia il servizio SQL Server. Di conseguenza, se è necessario installare l'estensione SQL IaaS, installarla in modalità _leggera_ per funzionalità limitate oppure installarla in modalità _completa_ durante una finestra di manutenzione. L'estensione SQL IaaS installata in modalità _leggera_ può essere aggiornata in modalità _completa_ in qualsiasi momento, ma richiede un riavvio del servizio SQL ServerSQL Server . 
   
1. **È possibile cambiare modello di licenza in una macchina virtuale di SQL Server distribuita usando il modello classico?**

   No. La modifica del modello di licenza non è supportata in una macchina virtuale classica. È possibile eseguire la migrazione della macchina virtuale al modello di Azure Resource Manager e registrarsi con il provider di risorse della macchina virtuale di SQL Server.You may migrate your VM to the Azure Resource Manager model and register with the SQL Server VM resource provider. Dopo aver registrato la macchina virtuale con il provider di risorse della macchina virtuale di SQL Server, le modifiche al modello di licenza saranno disponibili nella macchina virtuale.

1. **È possibile usare il portale di Azure per gestire più istanze nella stessa macchina virtuale?**

   No. La gestione del portale è una funzionalità fornita dal provider di risorse VM di SQL Server, che si basa sull'estensione dell'agente IaaS di SQL Server.Portal management is a feature provided by the SQL Server VM resource provider, which relies on the SQL Server IaaS Agent extension. Di conseguenza, le stesse limitazioni si applicano al provider di risorse come all'estensione. Il portale può gestire solo un'istanza predefinita o un'istanza denominata, purché sia stata configurata correttamente. Per ulteriori informazioni su queste limitazioni, vedere [Estensione dell'agente SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). 

1. **Le sottoscrizioni CSP possono attivare Vantaggio Azure Hybrid?**

   Sì. Vantaggio Azure Hybrid è disponibile per le sottoscrizioni CSP. I clienti CSP devono prima di tutto distribuire un'immagine con pagamento in base al consumo e quindi [cambiare il modello di licenza](virtual-machines-windows-sql-ahb.md) in Bring Your Own License.
   
 
1. **È necessario pagare la licenza di SQL Server in una VM di Azure se viene utilizzata solo per standby/failover?**

   Per disporre di una licenza passiva gratuita per un gruppo di disponibilità secondario di standby o un'istanza cluster di failover, è necessario soddisfare tutti i criteri seguenti, come descritto dalle [Condizioni per](https://www.microsoft.com/licensing/product-licensing/products)le licenze del prodotto:

   1. È possibile [mobilità](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) delle licenze tramite [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3). 
   1. L'istanza passiva di SQL ServerSQL Server non fornisce dati di SQL ServerSQL Server ai client né esegue carichi di lavoro di SQL Server attivi. Viene utilizzato solo per la sincronizzazione con il server primario e in caso contrario mantenere il database passivo in uno stato warm standby. Se serve dati, ad esempio report ai client che eseguono carichi di lavoro SQL Server attivi o esegue qualsiasi operazione diversa da quella specificata nei termini del prodotto, deve essere un'istanza di SQL Server con licenza a pagamento. Nell'istanza secondaria è consentita l'attività seguente: verifiche di coerenza del database o CheckDB, backup completi, backup del log delle transazioni e monitoraggio dei dati sull'utilizzo delle risorse. È inoltre possibile eseguire contemporaneamente l'istanza di ripristino di emergenza primaria e corrispondente per brevi periodi di test di ripristino di emergenza ogni 90 giorni. 
   1. La licenza di SQL Server attiva è coperta da Software Assurance e consente **un'istanza** secondaria passiva di SQL Server, con una quantità fino alla stessa quantità di calcolo del server attivo con licenza, solo. 
   1. La macchina virtuale SQL Server secondaria usa la licenza di ripristino di [emergenza](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) nel portale di Azure.The secondary SQL Server VM utilizes the Disaster Recovery license in the Azure portal.
   
1. **Che cosa è considerato un'istanza passiva?**

   L'istanza passiva di SQL ServerSQL Server non fornisce dati di SQL ServerSQL Server ai client né esegue carichi di lavoro di SQL Server attivi. Viene utilizzato solo per la sincronizzazione con il server primario e in caso contrario mantenere il database passivo in uno stato warm standby. Se serve dati, ad esempio report ai client che eseguono carichi di lavoro SQL Server attivi o esegue qualsiasi operazione diversa da quella specificata nei termini del prodotto, deve essere un'istanza di SQL Server con licenza a pagamento. Nell'istanza secondaria è consentita l'attività seguente: verifiche di coerenza del database o CheckDB, backup completi, backup del log delle transazioni e monitoraggio dei dati sull'utilizzo delle risorse. È inoltre possibile eseguire contemporaneamente l'istanza di ripristino di emergenza primaria e corrispondente per brevi periodi di test di ripristino di emergenza ogni 90 giorni.
   

1. **Quali scenari possono utilizzare il vantaggio Distaster Recovery (DR)?**

   La [guida alle licenze](https://aka.ms/sql2019licenseguide) fornisce scenari in cui è possibile utilizzare il vantaggio di ripristino di emergenza. Per ulteriori informazioni, consulta le Condizioni per il prodotto e contatta i contatti di licenza o il gestore dell'account.

1. **Quali sottoscrizioni supportano il vantaggio Ripristino di emergenza(DR)?**

   Programmi completi che offrono diritti di sottoscrizione equivalenti di Software Assurance come supporto di benefit fisso per il vantaggio DR. Questo include. ma non è limitato a, il valore aperto (OV), Open Value Subscription (OVS), Enterprise Agreement (EA), Enterprise Subscription Agreement (EAS) e il Server e Cloud Enrollment (SCE). Fare riferimento alle [condizioni del prodotto](https://www.microsoft.com/licensing/product-licensing/products) e rivolgersi ai contatti di licenza o al responsabile di acocunt per ulteriori informazioni. 

   
 ## <a name="resource-provider"></a>Provider di risorse

1. **La registrazione della macchina virtuale con il nuovo provider di risorse della macchina virtuale di SQL Server comporta costi aggiuntivi?**

   No. Il provider di risorse della macchina virtuale SQL Server consente solo una gestibilità aggiuntiva per SQL Server in macchine virtuali di Azure senza costi aggiuntivi. 

1. **Il provider di risorse VM di SQL Server è disponibile per tutti i clienti?**
 
   Sì, purché la macchina virtuale di SQL Server sia stata distribuita nel cloud pubblico usando il modello di Resource Manager e non il modello classico. Tutti gli altri clienti possono registrarsi con il nuovo provider di risorse VM di SQL Server.All other customers are able to register with the new SQL Server VM resource provider. Tuttavia, solo i clienti con il vantaggio Software Assurance possono usare la propria licenza attivando il vantaggio Azure Hybrid (AHB) in una macchina virtuale di SQL Server.However, only customers with the [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) benefit can use their own license by activating the [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) on a SQL Server VM. 

1. **Cosa accade alla risorsa del provider di risorse (_Microsoft.SqlVirtualMachine_) se la risorsa VM viene spostata o eliminata?** 

   Quando la risorsa Microsoft.Compute/VirtualMachine viene eliminata o spostata, alla risorsa Microsoft.SqlVirtualMachine associata viene notificata la necessità di replicare in modo asincrono l'operazione.

1. **Cosa succede alla macchina virtuale se la risorsa del provider di risorse (_Microsoft.SqlVirtualMachine_) viene eliminata?**

    Quando viene eliminata la risorsa Microsoft.SqlVirtualMachine, la risorsa Microsoft.Compute/VirtualMachine non è compromessa. Tuttavia, le modifiche della licenza verranno riportate all’immagine originale. 

1. **È possibile registrare macchine virtuali SQL Server distribuite con il provider di risorse VM di SQL Server?**

    Sì. Se SQL Server è stato distribuito dal proprio supporto e l'estensione di SQL IaaS è stata installata, è possibile registrare la macchina virtuale di SQL Server sul provider di risorse per migliorare la gestione con l’estensione IaaS di SQL. Tuttavia, non è possibile convertire una macchina virtuale di SQL Server distribuita in conpagamento in base al client.


   


## <a name="administration"></a>Amministrazione

1. **È possibile installare una seconda istanza di SQL Server nella stessa macchina virtuale? È possibile modificare le funzionalità installate dell'istanza predefinita?**

   Sì. Il supporto di installazione di SQL Server si trova in una cartella nell'unità **C** . Eseguire **Setup.exe** da tale percorso per aggiungere nuove istanze di SQL Server o per modificare altre funzionalità di SQL Server installate nella macchina virtuale. Si noti che alcune funzionalità, ad esempio Il backup automatico, l'applicazione automatica di patch e l'integrazione dell'insieme di credenziali delle chiavi di Azure, funzionano solo con l'istanza predefinita o un'istanza denominata configurata correttamente (vedere la domanda 3). 

1. **È possibile disinstallare l'istanza predefinita di SQL Server?**

   Sì, ma ci sono alcune considerazioni di cui tenere conto. In primo luogo, la fatturazione associata a SQL Server può continuare a verificarsi a seconda del modello di licenza per la macchina virtuale. In secondo luogo, come indicato nella risposta precedente, sono disponibili funzionalità che si basano [sull'estensione dell'agente IaaS](virtual-machines-windows-sql-server-agent-extension.md)di SQL Server . Se si disinstalla l'istanza predefinita senza rimuovere anche l'estensione IaaS, l'estensione continua a cercare l'istanza predefinita e può generare errori del registro eventi. Questi errori hanno le due origini seguenti: **Microsoft SQL Server Credential Management** e **Microsoft SQL Server IaaS Agent**. Uno degli errori potrebbe essere simile al seguente:

      Si è verificato un errore di rete o specifico dell'istanza mentre veniva stabilita la connessione a SQL Server. Il server non è stato trovato o non è accessibile.

   Se si decide di disinstallare l'istanza predefinita, disinstallare anche l'[estensione SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md). 

1. **È possibile utilizzare un'istanza denominata di SQL Server con l'estensione IaaS**?
   
   Sì, se l'istanza denominata è l'unica istanza in SQL Server e se l'istanza predefinita originale è stata [disinstallata correttamente.](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance) Se non è presente alcuna istanza predefinita e sono presenti più istanze denominate in una singola macchina virtuale di SQL Server, l'estensione dell'agente IaaS di SQL Server non verrà installata. 

1. **È possibile rimuovere COMPLETAMENTE SQL Server da una macchina virtuale di SQL Server?**

   Sì, ma continuerai a essere addebitato per la macchina virtuale di SQL Server come descritto in Linee guida per i prezzi per le macchine virtuali di SQL Server Azure .Yes, but you will continue to be charged for your SQL Server VM as described in [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md). Se SQL Server non è più necessario, è possibile distribuire una nuova macchina virtuale ed eseguire la migrazione di dati e applicazioni alla nuova macchina virtuale. Sarà quindi possibile rimuovere la macchina virtuale di SQL Server.
   
## <a name="updating-and-patching"></a>Aggiornamento e applicazione di patch

1. **Come si passa a una versione/un'edizione diversa di SQL Server in una macchina virtuale di Azure?**

   I clienti possono cambiare versione/edizione di SQL Server usando supporti di configurazione che contengono la versione o l'edizione desiderata di SQL Server. Dopo aver cambiato edizione, usare il portale di Azure per modificare la proprietà dell'edizione della macchina virtuale in modo che rispecchi correttamente la fatturazione per la macchina virtuale. Per altre informazioni, vedere [Modificare l'edizione di una macchina virtuale di SQL Server.For more information,](virtual-machines-windows-sql-change-edition.md)see change edition of a SQL Server VM . Non vi è alcuna differenza di fatturazione per le diverse versioni di SQL Server, pertanto una volta modificata la versione di SQL Server, non sono necessarie ulteriori azioni.

1. **Dove è possibile ottenere il supporto di installazione per modificare l'edizione o la versione di SQL Server?**

   I clienti che dispongono di [software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) possono ottenere il supporto di installazione dal Volume [Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). I clienti che non dispongono di software assurance possono usare il supporto di installazione di un'immagine della macchina virtuale di SQL Server di Marketplace con l'edizione desiderata.
   
1. **Come si applicano gli aggiornamenti e i Service Pack a una VM di SQL Server?**

   Le macchine virtuali consentono di controllare il computer host e di decidere quindi quando e come applicare gli aggiornamenti. Per il sistema operativo, è possibile applicare manualmente gli aggiornamenti di Windows oppure abilitare un servizio di pianificazione definito [Applicazione automatica delle patch](virtual-machines-windows-sql-automated-patching.md). Applicazione automatica delle patch installa tutti gli aggiornamenti contrassegnati come importanti, inclusi gli aggiornamenti di SQL Server in tale categoria. Gli aggiornamenti facoltativi di SQL Server devono essere installati manualmente.

1. **È possibile aggiornare l'istanza di SQL Server 2008 / 2008 R2 dopo averla registrato con il provider di risorse VM di SQL Server?**

   Sì. È possibile utilizzare qualsiasi supporto di installazione per aggiornare la versione e l'edizione di SQL Server e quindi aggiornare la modalità di [estensione di SQL IaaS](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)) da _nessun agente_ a _pieno_. In questo modo sarà possibile accedere a tutti i vantaggi dell'estensione SQL IaaS, ad esempio la gestibilità del portale, i backup automatici e l'applicazione automatica di patch. 

1. **Come è possibile ottenere aggiornamenti della sicurezza estesi gratuiti per le istanze di SQL Server 2008 e SQL Server 2008 R2 di SQL Server 2008 e SQL Server 2008 R2?**

   È possibile ottenere [aggiornamenti della sicurezza estesa gratuiti](virtual-machines-windows-sql-server-2008-eos-extend-support.md) spostando SQL Server così com'è in una macchina virtuale SQL di Azure.You can get free extended security updates by moving your SQL Server as-is to an Azure SQL virtual machine. Per ulteriori informazioni, vedere [Fine delle opzioni di supporto](/sql/sql-server/end-of-support/sql-server-end-of-life-overview). 
  
   

## <a name="general"></a>Generale

1. **Le istanze del cluster di failover di SQL ServerSQL Server sono supportate nelle macchine virtuali di Azure?**

   Sì. È possibile installare un'istanza del cluster di failover usando [condivisioni file Premium (PFS)](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) o spazi di [archiviazione diretti (S2D)](virtual-machines-windows-portal-sql-create-failover-cluster.md) per il sottosistema di archiviazione. Le condivisioni file Premium forniscono capacità di I/O al secondo e di velocità effettiva in grado di soddisfare le esigenze di molti carichi di lavoro. Per carichi di lavoro che richiedono un utilizzo intensivo di I/O, è consigliabile usare gli spazi di archiviazione direttamente in base ai dischi Premium o Ultra manged. In alternativa, è possibile usare soluzioni di clustering o archiviazione di terze parti come descritto in [Disponibilità elevata e ripristino di emergenza per SQL Server nelle macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > Al momento, l'estensione completa dell'agente IaaS di SQL Server non è supportata per l'applicazione dell'autenticazione FCI di SQL Server in Azure.At this time, the _full_ [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md) is not supported for SQL Server FCI on Azure. È consigliabile disinstallare l'estensione _completa_ dalle macchine virtuali che fanno parte dell'fCI e installare l'estensione in modalità _leggera._ Questa estensione supporta funzionalità, ad esempio Backup automatico e applicazione di patch e alcune funzionalità del portale per SQL Server.This extension supports features, such as Automated Backup and Patching and some portal features for SQL Server. Queste funzionalità non funzioneranno per le macchine virtuali di SQL Server dopo la disinstallazione dell'agente _completo._

1. **Qual è la differenza tra le macchine virtuali di SQL Server e il servizio di database SQL?**

   Dal punto di vista concettuale l'esecuzione di SQL Server in una macchina virtuale di Azure non è diversa dall'esecuzione di SQL Server in un centro dati remoto. Per contro, il servizio [Database SQL](../../../sql-database/sql-database-technical-overview.md) offre una soluzione DaaS (Database-as-a-service). Con Database SQL non si ha accesso ai computer che ospitano i database. Per un confronto completo, vedere [Scegliere un'opzione di SQL Server cloud: database SQL di Azure (PaaS) o SQL Server in VM di Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Come si installa SQL Server Data Tools in una VM di Azure?**

    Scaricare e installare SQL Server Data Tools da [Microsoft SQL Server Data Tools - Business Intelligence per Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **Le transazioni distribuite con MSDTC sono supportate nelle macchine virtuali di SQL Server?**
   
    Sì. DTC locale è supportato per SQL Server 2016 SP2 e versioni successive. Tuttavia, le applicazioni devono essere testate quando si usano gruppi di disponibilità AlwaysOnAlways On availability groups, poiché le transazioni in corso durante un failover avranno esito negativo e devono essere ritentate. DTC in cluster è disponibile a partire da Windows Server 2019. 

## <a name="resources"></a>Risorse

**Macchine virtuali Windows**:

* [Panoramica di SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Effettuare il provisioning di una macchina virtuale Windows di SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrazione di un database a SQL Server in una macchina virtuale di AzureMigrating a Database to SQL Server on an Azure VM](virtual-machines-windows-migrate-sql.md)
* [Disponibilità elevata e ripristino di emergenza di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-performance.md)
* [Modelli di applicazione e strategie di sviluppo per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Macchine virtuali Linux**:

* [Panoramica di SQL Server in una macchina virtuale Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Effettuare il provisioning di una macchina virtuale Linux con SQL Server](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Domande frequenti (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Documentazione di SQL Server su LinuxSQL Server on Linux documentation](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
