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
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: 5b3c65361f45c8ad5bdf3adf89dc3002c229d0d0
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650401"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Domande frequenti su SQL Server in esecuzione in macchine virtuali Windows in Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Questo articolo fornisce le risposte ad alcune delle domande più comuni sull'esecuzione di [SQL Server in macchine virtuali Windows in Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Questo articolo descrive i problemi specifici di SQL Server in macchine virtuali Windows. Se si esegue SQL Server in macchine virtuali Linux, vedere le [domande frequenti su Linux](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Immagini

1. **Quali immagini della raccolta di macchine virtuali di SQL Server sono disponibili?** 

   Azure mantiene le immagini delle macchine virtuali per tutte le principali versioni supportate di SQL Server in tutte le edizioni sia per Linux che per Windows. Per altre informazioni, vedere l’elenco completo di [immagini di macchine virtuali Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) e di [immagini di macchine virtuali Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **Le immagini della raccolta di macchine virtuali di SQL Server esistenti vengono aggiornate?**

   Ogni due mesi, le immagini di SQL Server nella raccolta di macchine virtuali vengono aggiornate con gli aggiornamenti di Linux e Windows più recenti. Per le immagini Windows, si includono tutti gli aggiornamenti contrassegnati come importanti in Windows Update, tra cui gli aggiornamenti di sicurezza e i Service Pack di SQL Server importanti. Per le immagini Linux si includono gli aggiornamenti di sistema più recenti. Gli aggiornamenti cumulativi di SQL Server vengono gestiti in modo diverso per Linux e Windows. Per Linux, gli aggiornamenti cumulativi di SQL Server sono inclusi nell'aggiornamento. Tuttavia al momento le macchine virtuali Windows non vengono aggiornate con gli aggiornamenti cumulativi di SQL Server o Windows Server.

1. **Le immagini di macchine virtuali di SQL Server possono essere rimosse dalla raccolta?**

   Sì. Azure mantiene solo un'immagine per ogni versione e per ogni edizione principale. Ad esempio, quando viene rilasciato un nuovo Service Pack di SQL Server, Azure aggiunge una nuova immagine alla raccolta per quel Service Pack. L'immagine di SQL Server del Service Pack precedente viene immediatamente rimossa dal portale di Azure. Tuttavia è ancora disponibile per il provisioning di PowerShell per i successivi tre mesi. Dopo tre mesi, l'immagine del Service Pack precedente non è più disponibile. Questi criteri di rimozione sono applicabili anche se una versione di SQL Server non è più supportata perché raggiunge la fine del ciclo di vita.


1. **È possibile distribuire un'immagine precedente di SQL Server non visibile nell'portale di Azure?**

   Sì, con PowerShell. Per altre informazioni sulla distribuzione di macchine virtuali di SQL Server usando PowerShell, consultare [Come eseguire il provisioning di macchine virtuali di SQL Server con Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

1. **È possibile creare un'immagine generalizzata di Azure SQL Server Marketplace della macchina virtuale SQL Server e usarla per distribuire le VM?**

   Sì, ma è necessario [registrare ogni macchina virtuale SQL Server con il provider di risorse SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md) per gestire la VM SQL Server nel portale, nonché usare funzionalità come l'applicazione automatica di patch e i backup automatici. Quando si esegue la registrazione con il provider di risorse, è necessario specificare anche il tipo di licenza per ogni macchina virtuale SQL Server. 

1. **È possibile usare il proprio disco rigido virtuale per distribuire una macchina virtuale SQL Server?**

   Sì, ma è necessario [registrare ogni macchina virtuale SQL Server con il provider di risorse SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md) per gestire la VM SQL Server nel portale, nonché usare funzionalità come l'applicazione automatica di patch e i backup automatici.

1. **È possibile impostare configurazioni non visualizzate nella raccolta di macchine virtuali, ad esempio Windows 2008 R2 + SQL Server 2012?**

   No. Per le raccolte di macchine virtuali che includono SQL Server è necessario selezionare una delle immagini disponibili sia dal portale di Azure che tramite [PowerShell](virtual-machines-windows-ps-sql-create.md). Tuttavia, si ha la possibilità di distribuire una macchina virtuale Windows e di installarla autonomamente SQL Server. È quindi necessario [registrare la macchina virtuale di SQL Server con il provider di risorse SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md) per gestire la VM SQL Server nel portale, nonché usare funzionalità come l'applicazione automatica di patch e i backup automatici. 


## <a name="creation"></a>Creazione

1. **Come si crea una macchina virtuale di Azure con SQL Server?**

   Il metodo più semplice consiste nel creare una macchina virtuale che includa SQL Server. Per un'esercitazione sull'iscrizione ad Azure e sulla creazione di una VM SQL Server dal portale, vedere effettuare il provisioning di [una macchina virtuale SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md). È possibile selezionare un'immagine di macchina virtuale che usa la licenza di SQL Server con costo al secondo oppure usare un'immagine che consente di trasferire la licenza di SQL Server dell'utente. È anche possibile installare manualmente in una macchina virtuale un'edizione di SQL Server con licenza gratuita (Developer o Express) o riutilizzando una licenza locale. Assicurarsi di [registrare la macchina virtuale di SQL Server con il provider di risorse SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md) per gestire la VM SQL Server nel portale, nonché usare funzionalità come l'applicazione automatica di patch e i backup automatici. Se si usa la funzionalità Bring Your Own License, è necessario avere [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/). Per altre informazioni, vedere [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Guida ai prezzi per le VM di SQL Server in Azure).

1. **Come si esegue la migrazione di un database SQL Server locale nel cloud?**

   Creare prima una macchina virtuale di Azure con un'istanza di SQL Server, quindi eseguire la migrazione dei database locali in tale istanza. Per alcune strategie di migrazione dei dati, vedere [Eseguire la migrazione di un database di SQL Server a SQL Server in una macchina virtuale di Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licenze

1. **Come si installa una copia di SQL Server con licenza in una VM di Azure?**

   È possibile eseguire questa operazione in tre modi. Se sei un cliente con contratto Enterprise, puoi effettuare il provisioning di una delle [Immagini di macchina virtuale che supporta le licenze](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), nota anche come Bring-your-own-License (BYOL). Se si dispone di [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default), è possibile abilitare il [vantaggio Azure Hybrid](virtual-machines-windows-sql-ahb.md) su un'immagine con pagamento in base al consumo (PAYG) esistente. In alternativa, è possibile copiare il supporto di installazione di SQL Server in una macchina virtuale di Windows Server, quindi installare SQL Server nella macchina virtuale. Assicurarsi di registrare la macchina virtuale SQL Server con il [provider di risorse](virtual-machines-windows-sql-register-with-resource-provider.md) per funzionalità quali la gestione del portale, il backup automatizzato e l'applicazione automatica delle patch. 

1. **È necessario pagare la licenza di SQL Server in una VM di Azure se viene utilizzata solo per standby/failover?**

   Per avere una licenza passiva gratuita per un gruppo di disponibilità secondario o un'istanza del cluster di failover di standby, è necessario soddisfare tutti i criteri seguenti, come descritto nel [PDF della Guida alle licenze](https://download.microsoft.com/download/7/8/C/78CDF005-97C1-4129-926B-CE4A6FE92CF5/SQL_Server_2017_Licensing_guide.pdf):

   1. La [mobilità delle licenze](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) è [garantita tramite Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3). 
   1. L'istanza di SQL Server passiva non fornisce dati SQL Server ai client o esegue carichi di lavoro SQL Server attivi. Viene usato solo per la sincronizzazione con il server primario e in caso contrario mantiene il database passivo in uno stato warm standby. Se vengono utilizzati i dati, ad esempio i report ai client che eseguono carichi di lavoro di SQL Server attivi o che eseguono qualsiasi "lavoro", ad esempio backup aggiuntivi dal server secondario, deve essere un'istanza di SQL Server a pagamento con licenza. 
   1. La licenza Active SQL Server è coperta da Software Assurance e consente di usare **un'** istanza di SQL Server secondaria passiva, con la stessa quantità di risorse di calcolo del server attivo concesso in licenza. 
   1. La macchina virtuale SQL Server secondaria usa il [modello di licenza](virtual-machines-windows-sql-ahb.md)Bring-your-own-License (BYOL) o vantaggio Azure Hybrid (vantaggio Azure Hybrid). 

1. **È possibile modificare una VM per l'uso di una licenza di SQL Server, se è stata creata da una delle immagini della raccolta con pagamento in base al consumo?**

   Sì. È possibile passare facilmente un'immagine della raccolta con pagamento in base al consumo (PAYG) a bring your own License (BYOL) abilitando la [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/faq/).  Per altre informazioni, vedere [Come cambiare il livello di licenza per una macchina virtuale SQL Server](virtual-machines-windows-sql-ahb.md). Questa funzionalità è attualmente disponibile solo per i clienti del cloud pubblico.

1. **Per cambiare modello di licenza sono necessari tempi di inattività di SQL Server?**

   No. Per [modificare il modello di licenza](virtual-machines-windows-sql-ahb.md) non è necessario alcun tempo di inattività di SQL Server perché la modifica ha effetto immediato e non richiede un riavvio della macchina virtuale. Tuttavia, per registrare la macchina virtuale di SQL Server con il provider di risorse SQL Server VM, l' [estensione SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) è un prerequisito e l'installazione dell'estensione SQL IaaS in modalità _completa_ riavvia il servizio di SQL Server. Di conseguenza, se è necessario installare l'estensione SQL IaaS, installarla in modalità _Lightweight_ per la funzionalità limitata oppure installarla in modalità _completa_ durante una finestra di manutenzione. L'estensione SQL IaaS installata in modalità _Lightweight_ può essere aggiornata alla modalità _completa_ in qualsiasi momento, ma richiede il riavvio del servizio SQL Server. 

1. **È possibile usare la portale di Azure per gestire più istanze nella stessa VM?**

   No. La gestione del portale è una funzionalità fornita dal provider di risorse SQL Server VM, che si basa sull'estensione dell'agente IaaS SQL Server. Di conseguenza, le stesse limitazioni si applicano al provider di risorse per l'estensione. Il portale può gestire solo un'istanza predefinita o un'istanza denominata, purché sia stata configurata correttamente. Per ulteriori informazioni su queste limitazioni, vedere [SQL Server estensione dell'agente IaaS](virtual-machines-windows-sql-server-agent-extension.md). 

1. **Le sottoscrizioni CSP possono attivare Vantaggio Azure Hybrid?**

   Sì. Vantaggio Azure Hybrid è disponibile per le sottoscrizioni CSP. I clienti CSP devono prima di tutto distribuire un'immagine con pagamento in base al consumo e quindi [cambiare il modello di licenza](virtual-machines-windows-sql-ahb.md) in Bring Your Own License.

1. **La registrazione della macchina virtuale con il nuovo provider di risorse SQL Server VM comporta costi aggiuntivi?**

   No. Il provider di risorse SQL Server VM consente la gestione aggiuntiva per SQL Server in una macchina virtuale di Azure senza addebiti aggiuntivi. 

1. **Il provider di risorse SQL Server VM è disponibile per tutti i clienti?**
 
   Sì, purché la macchina virtuale SQL Server sia stata distribuita nel cloud pubblico usando il modello di Gestione risorse e non il modello classico. Tutti gli altri clienti possono registrarsi con il nuovo provider di risorse SQL Server VM. Tuttavia, solo i clienti con il vantaggio [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) possono usare la propria licenza attivando il [vantaggio Azure Hybrid (vantaggio Azure Hybrid)](https://azure.microsoft.com/pricing/hybrid-benefit/) in una macchina virtuale SQL Server. 

1. **Cosa accade alla risorsa del provider di risorse (_Microsoft. SqlVirtualMachine_) se la risorsa della macchina virtuale viene spostata o eliminata?** 

   Quando la risorsa Microsoft.Compute/VirtualMachine viene eliminata o spostata, alla risorsa Microsoft.SqlVirtualMachine associata viene notificata la necessità di replicare in modo asincrono l'operazione.

1. **Cosa accade alla macchina virtuale se la risorsa del provider di risorse (_Microsoft. SqlVirtualMachine_) viene eliminata?**

    Quando viene eliminata la risorsa Microsoft.SqlVirtualMachine, la risorsa Microsoft.Compute/VirtualMachine non è compromessa. Tuttavia, le modifiche della licenza verranno riportate all’immagine originale. 

1. **È possibile registrare macchine virtuali SQL Server distribuite in modo autonomo con il provider di risorse SQL Server VM?**

    Sì. Se SQL Server è stato distribuito dal proprio supporto e l'estensione di SQL IaaS è stata installata, è possibile registrare la macchina virtuale di SQL Server sul provider di risorse per migliorare la gestione con l’estensione IaaS di SQL. Tuttavia, non è possibile convertire una macchina virtuale SQL Server distribuita in base al consumo.

1. **È possibile cambiare il modello di licenza in una macchina virtuale SQL Server distribuita con il modello classico?**

   No. La modifica del modello di licenza non è supportata in una macchina virtuale classica. È possibile eseguire la migrazione della macchina virtuale al modello di Azure Resource Manager e registrarsi con il provider di risorse SQL Server VM. Dopo che la VM è stata registrata con il provider di risorse SQL Server VM, le modifiche al modello di licenza saranno disponibili nella macchina virtuale. 
   


## <a name="administration"></a>Amministrazione

1. **È possibile installare una seconda istanza di SQL Server nella stessa VM? È possibile modificare le funzionalità installate nell'istanza predefinita?**

   Sì. Il supporto di installazione di SQL Server si trova in una cartella nell'unità **C** . Eseguire **Setup.exe** da tale percorso per aggiungere nuove istanze di SQL Server o per modificare altre funzionalità di SQL Server installate nella macchina virtuale. Si noti che alcune funzionalità, ad esempio il backup automatizzato, l'applicazione automatica di patch e l'integrazione di Azure Key Vault, funzionano solo per l'istanza predefinita o un'istanza denominata configurata correttamente (vedere la domanda 3). 

1. **È possibile disinstallare l'istanza predefinita di SQL Server?**

   Sì, ma ci sono alcune considerazioni di cui tenere conto. Per prima cosa, la fatturazione associata a SQL Server può continuare a essere eseguita a seconda del modello di licenza per la macchina virtuale. Secondo, come indicato nella risposta precedente, sono disponibili funzionalità che si basano sull' [estensione SQL Server agente IaaS](virtual-machines-windows-sql-server-agent-extension.md). Se si disinstalla l'istanza predefinita senza rimuovere anche l'estensione IaaS, l'estensione continua a cercare l'istanza predefinita e potrebbe generare errori nel registro eventi. Questi errori provengono dalle due origini seguenti: **Microsoft SQL Server Credential Management** e **Microsoft SQL Server IaaS Agent**. Uno degli errori potrebbe essere simile al seguente:

      Si è verificato un errore di rete o specifico dell'istanza mentre veniva stabilita la connessione a SQL Server. Il server non è stato trovato o non è accessibile.

   Se si decide di disinstallare l'istanza predefinita, disinstallare anche l'[estensione SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md). 

1. È **possibile utilizzare un'istanza denominata di SQL Server con l'estensione IaaS**?
   
   Sì, se l'istanza denominata è l'unica istanza del SQL Server e se l'istanza predefinita originale è stata disinstallata [correttamente](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance). Se non è presente un'istanza predefinita e sono presenti più istanze denominate in una singola macchina virtuale SQL Server, l'installazione dell'estensione dell'agente IaaS SQL Server non verrà completata. 

1. **È possibile rimuovere completamente SQL Server da una macchina virtuale SQL Server?**

   Sì, ma continuerà a essere addebitato il costo della macchina virtuale SQL Server come descritto in [linee guida sui prezzi per SQL Server macchine virtuali di Azure](virtual-machines-windows-sql-server-pricing-guidance.md). Se SQL Server non è più necessario, è possibile distribuire una nuova macchina virtuale ed eseguire la migrazione di dati e applicazioni alla nuova macchina virtuale. Sarà quindi possibile rimuovere la macchina virtuale di SQL Server.
   
## <a name="updating-and-patching"></a>Aggiornamento e applicazione di patch

1. **Come si passa a una versione/un'edizione diversa di SQL Server in una macchina virtuale di Azure?**

   I clienti possono cambiare versione/edizione di SQL Server usando supporti di configurazione che contengono la versione o l'edizione desiderata di SQL Server. Dopo aver cambiato edizione, usare il portale di Azure per modificare la proprietà dell'edizione della macchina virtuale in modo che rispecchi correttamente la fatturazione per la macchina virtuale. Per altre informazioni, vedere [Change Edition of a SQL Server VM](virtual-machines-windows-sql-change-edition.md). Non esiste alcuna differenza di fatturazione per versioni diverse di SQL Server, quindi una volta modificata la versione di SQL Server, non è necessaria alcuna azione aggiuntiva.

1. **Dove è possibile ottenere il supporto di installazione per modificare l'edizione o la versione di SQL Server?**

  I clienti che dispongono di [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) possono ottenere i supporti di installazione da [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). I clienti che non hanno Software Assurance possono usare il supporto di installazione da un Marketplace SQL Server immagine di macchina virtuale con l'edizione desiderata.

1. **Come si applicano gli aggiornamenti e i Service Pack a una VM di SQL Server?**

   Le macchine virtuali consentono di controllare il computer host e di decidere quindi quando e come applicare gli aggiornamenti. Per il sistema operativo, è possibile applicare manualmente gli aggiornamenti di Windows oppure abilitare un servizio di pianificazione definito [Applicazione automatica delle patch](virtual-machines-windows-sql-automated-patching.md). Applicazione automatica delle patch installa tutti gli aggiornamenti contrassegnati come importanti, inclusi gli aggiornamenti di SQL Server in tale categoria. Gli aggiornamenti facoltativi di SQL Server devono essere installati manualmente.

1. **È possibile aggiornare l'istanza di SQL Server 2008/2008 R2 dopo averla registrata con il provider di risorse VM SQL Server?**

   Sì. È possibile utilizzare qualsiasi supporto di installazione per aggiornare la versione e l'edizione di SQL Server, quindi è possibile aggiornare la [modalità di estensione IaaS di SQL](virtual-machines-windows-sql-server-agent-extension.md#change-management-modes) da _Nessun agente_ a _completo_. In questo modo si otterrà l'accesso a tutti i vantaggi dell'estensione IaaS di SQL, ad esempio la gestibilità del portale, i backup automatici e l'applicazione automatica delle patch. 

## <a name="general"></a>Generale

1. **Le istanze del cluster di failover di SQL Server sono supportate nelle macchine virtuali di Azure?**

   Sì. È possibile [creare un cluster di failover di Windows in Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) e usare Spazi di archiviazione diretta (S2D) per l'archiviazione del cluster. In alternativa, è possibile usare soluzioni di clustering o archiviazione di terze parti come descritto in [Disponibilità elevata e ripristino di emergenza per SQL Server nelle macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > A questo punto, l' [estensione dell'agente IaaS completa SQL Server](virtual-machines-windows-sql-server-agent-extension.md) non è supportata per SQL Server FCI in Azure. Si consiglia di disinstallare l'estensione _completa_ dalle macchine virtuali che fanno parte dell'istanza FCI e installare l'estensione in modalità _Lightweight_ . Questa estensione supporta le funzionalità di, ad esempio il backup automatizzato e l'applicazione di patch e alcune funzionalità del portale per SQL Server. Queste funzionalità non funzioneranno per le macchine virtuali SQL Server dopo la disinstallazione dell'agente _completo_ .

1. **Qual è la differenza tra SQL Server VM e il servizio del database SQL?**

   Dal punto di vista concettuale l'esecuzione di SQL Server in una macchina virtuale di Azure non è diversa dall'esecuzione di SQL Server in un centro dati remoto. Per contro, il servizio [Database SQL](../../../sql-database/sql-database-technical-overview.md) offre una soluzione DaaS (Database-as-a-service). Con Database SQL non si ha accesso ai computer che ospitano i database. Per un confronto completo, vedere [Scegliere un'opzione di SQL Server cloud: database SQL di Azure (PaaS) o SQL Server in macchine virtuali di Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Come si installa SQL Server Data Tools in una VM di Azure?**

    Scaricare e installare SQL Server Data Tools da [Microsoft SQL Server Data Tools - Business Intelligence per Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **Le transazioni distribuite con MSDTC sono supportate in SQL Server VM?**
   
    Sì. DTC locale è supportato per SQL Server 2016 SP2 e versioni successive. Tuttavia, le applicazioni devono essere testate quando si utilizza Gruppi di disponibilità Always On, perché le transazioni in corso durante un failover avranno esito negativo e dovranno essere ritentate. DTC cluster è disponibile a partire da Windows Server 2019. 

## <a name="resources"></a>Risorse

**Macchine virtuali Windows**:

* [Panoramica di SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Effettuare il provisioning di una macchina virtuale Windows di SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrazione di un database a SQL Server su una macchina virtuale di Azure](virtual-machines-windows-migrate-sql.md)
* [Disponibilità elevata e ripristino di emergenza di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Performance best practices for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-performance.md) (Procedure consigliate sulle prestazioni per SQL Server nelle macchine virtuali di Azure)
* [Modelli di applicazione e strategie di sviluppo per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Macchine virtuali Linux**:

* [Panoramica di SQL Server in una macchina virtuale Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Effettuare il provisioning di una macchina virtuale Linux con SQL Server](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Domande frequenti (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Documentazione di SQL Server in Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
