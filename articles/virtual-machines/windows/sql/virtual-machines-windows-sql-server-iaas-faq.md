---
title: Domande frequenti su SQL Server in macchine virtuali Windows in Azure| Microsoft Docs
description: Questo articolo offre risposta ad alcune domande frequenti sull'esecuzione di SQL Server in macchine virtuali di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: v-shysun
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: v-shysun
ms.openlocfilehash: 0956d9bdbf6390f2d64f15ca267545ca15289a46
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53339400"
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

1. **È possibile creare un'immagine di disco rigido virtuale da una VM di SQL Server?**

   Sì, ma ci sono alcune considerazioni di cui tenere conto. Se si distribuisce questo disco rigido virtuale in una nuova VM in Azure, non è possibile accedere alla sezione Configurazione di SQL Server nel portale. Per gestire le opzioni di configurazione di SQL Server sarà quindi necessario usare PowerShell. Inoltre, verrà addebitato un costo alla tariffa della VM di SQL su cui si basava originariamente l'immagine. Questo vale anche se si rimuove SQL Server dal disco rigido virtuale prima della distribuzione. 

1. **È possibile impostare configurazioni non visualizzate nella raccolta di macchine virtuali, ad esempio Windows 2008 R2 + SQL Server 2012?**

    No. Per le raccolte di macchine virtuali che includono SQL Server è necessario selezionare una delle immagini disponibili.

## <a name="creation"></a>Creazione

1. **Come si crea una macchina virtuale di Azure con SQL Server?**

   La soluzione più semplice consiste nel creare una macchina virtuale che include SQL Server. Per un'esercitazione sulla registrazione in Azure e sulla creazione di una macchina virtuale di SQL Server dal portale, vedere [Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md). È possibile selezionare un'immagine di macchina virtuale che usa la licenza di SQL Server con costo al secondo oppure usare un'immagine che consente di trasferire la licenza di SQL Server dell'utente. È anche possibile installare manualmente in una macchina virtuale un'edizione di SQL Server con licenza gratuita (Developer o Express) o riutilizzando una licenza locale. Se si usa la funzionalità Bring Your Own License, è necessario avere [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/). Per altre informazioni, vedere [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Guida ai prezzi per le VM di SQL Server in Azure).

1. **Come si esegue la migrazione di un database SQL Server locale nel cloud?**

   Creare prima una macchina virtuale di Azure con un'istanza di SQL Server, quindi eseguire la migrazione dei database locali in tale istanza. Per alcune strategie di migrazione dei dati, vedere [Eseguire la migrazione di un database di SQL Server a SQL Server in una macchina virtuale di Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licenze

1. **Come si installa una copia di SQL Server con licenza in una VM di Azure?**

   Per eseguire questa operazione è possibile procedere in due modi: È possibile eseguire il provisioning di una delle [immagini di macchina virtuale che supporta le licenze](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), un'operazione nota anche come Bring Your Own License (BYOL). Un'altra possibilità consiste nel copiare il supporto di installazione di SQL Server in una VM Windows Server, quindi installare SQL Server nella VM. Tuttavia, se si installa manualmente SQL Server, non è prevista alcuna integrazione del portale e l'estensione SQL Server IaaS Agent non è supportata, pertanto funzionalità quali Backup automatizzato e Applicazione automatica delle patch non funzioneranno in questo scenario. Per questo motivo, è consigliabile usare una delle immagini della raccolta BYOL. Per usare BYOL o il proprio supporto di SQL Server in una VM di Azure, è necessario disporre della [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/). Per altre informazioni, vedere [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Guida ai prezzi per le VM di SQL Server in Azure).


1. **È necessario pagare la licenza di SQL Server in una VM di Azure se viene utilizzata solo per standby/failover?**

   Se è disponibile Software Assurance e si usa la mobilità delle licenze come descritto in Domande frequenti sulle licenze di Macchine virtuali (https://azure.microsoft.com/pricing/licensing-faq/)), non è necessario pagare la licenza per un’istanza di SQL Server che funge da replica secondaria passiva in una distribuzione a disponibilità elevata. In caso contrario, è necessario pagare la licenza.

1. **È possibile modificare una VM per l'uso di una licenza di SQL Server, se è stata creata da una delle immagini della raccolta con pagamento in base al consumo?**

   Sì. È possibile alternare facilmente i due modelli di licenza, indipendentemente dall'immagine distribuita in origine. Per altre informazioni, vedere [Come cambiare il livello di licenza per una macchina virtuale SQL](virtual-machines-windows-sql-ahb.md).

1. **È consigliabile usare immagini BYOL o RP di macchine virtuali SQL per creare una nuova macchina virtuale di SQL?**

   Le immagini Bring Your Own License (BYOL) sono disponibili solo per i clienti EA. Altri clienti che dispongono di Software Assurance devono usare il provider di risorse di macchine virtuali SQL per creare una macchina virtuale SQL con [Vantaggio Azure Hybrid (AHB)](https://azure.microsoft.com/pricing/licensing-faq/). 

1. **Per cambiare modello di licenza sono necessari tempi di inattività di SQL Server?**

    No. Per [modificare il modello di licenza](virtual-machines-windows-sql-ahb.md) non è necessario alcun tempo di inattività di SQL Server perché la modifica ha effetto immediato e non richiede un riavvio della macchina virtuale. 

1. **Le sottoscrizioni CSP possono attivare Vantaggio Azure Hybrid?**

   Sì. Il [modello di licenza può essere modificato](virtual-machines-windows-sql-ahb.md) da chi dispone di una sottoscrizione CSP. 

1. **La registrazione di una macchina virtuale con il nuovo provider di risorse di macchine virtuali SQL comporta costi aggiuntivi?**

    No. Il provider di risorse di macchine virtuali SQL consente di migliorare la gestione di SQL Server in macchine virtuali di Azure senza spese aggiuntive. 

1. **Il provider di risorse di macchine virtuali SQL è disponibile per tutti i clienti?**
 
   Sì. Tutti i clienti possono registrarsi al nuovo provider di risorse di macchine virtuali SQL. Tuttavia, solo i clienti con il vantaggio Software Assurance possono attivare [Vantaggio Azure Hybrid (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) (o BYOL) in una macchina virtuale SQL Server. 

1. **Cosa accade alla risorsa  _* Microsoft.SqlVirtualMachine_* se la risorsa della macchina virtuale viene spostata o eliminata?** 

   Quando la risorsa Microsoft.Compute/VirtualMachine viene eliminata o spostata, alla risorsa Microsoft.SqlVirtualMachine associata viene notificata la necessità di replicare in modo asincrono l'operazione.

1. **Cosa accade alla macchina virtuale se la risorsa  _* Microsoft.SqlVirtualMachine_* viene eliminata?**

   Quando viene eliminata la risorsa Microsoft.SqlVirtualMachine, la risorsa Microsoft.Compute/VirtualMachine non è compromessa. Tuttavia, le modifiche della licenza verranno riportate all’immagine originale. 

1. **È possibile registrare macchine virtuali SQL Server distribuite autonomamente con il provider di risorse di macchine virtuali SQL?**

   Sì. Se SQL Server è stato distribuito dal proprio supporto e l'estensione di SQL IaaS è stata installata, è possibile registrare la macchina virtuale di SQL Server sul provider di risorse per migliorare la gestione con l’estensione IaaS di SQL. Non è però possibile convertire una macchina virtuale SQL distribuita autonomamente al pagamento in base al consumo.  

## <a name="administration"></a>Administration

1. **È possibile installare una seconda istanza di SQL Server nella stessa VM? È possibile modificare le funzionalità installate nell'istanza predefinita?**

   Sì. Il supporto di installazione di SQL Server si trova in una cartella nell'unità **C** . Eseguire **Setup.exe** da tale percorso per aggiungere nuove istanze di SQL Server o per modificare altre funzionalità di SQL Server installate nella macchina virtuale. Si noti che alcune funzionalità, ad esempio Backup automatizzato, Applicazione automatica delle patch e Integrazione di Azure Key Vault, funzionano solo nell'istanza predefinita.

1. **È possibile disinstallare l'istanza predefinita di SQL Server?**

   Sì, ma ci sono alcune considerazioni di cui tenere conto. Come indicato nella risposta precedente, le funzionalità che si basano sull'[estensione SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md) funzionano solo nell'istanza predefinita. Se si disinstalla l'istanza predefinita, l'estensione continua a cercarla e può generare errori del log eventi. Questi errori provengono dalle due origini seguenti: **Microsoft SQL Server Credential Management** e **Microsoft SQL Server IaaS Agent**. Uno degli errori potrebbe essere simile al seguente:

      Si è verificato un errore di rete o specifico dell'istanza mentre veniva stabilita la connessione a SQL Server. Il server non è stato trovato o non è accessibile.

   Se si decide di disinstallare l'istanza predefinita, disinstallare anche l'[estensione SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md).

1. **È possibile rimuovere completamente SQL Server da una VM di SQL?**

   Sì, ma continueranno a essere addebitati i costi per la VM di SQL, come descritto in [Guida ai prezzi per le VM di SQL Server in Azure](virtual-machines-windows-sql-server-pricing-guidance.md). Se SQL Server non è più necessario, è possibile distribuire una nuova macchina virtuale ed eseguire la migrazione di dati e applicazioni alla nuova macchina virtuale. Sarà quindi possibile rimuovere la macchina virtuale di SQL Server.
   
## <a name="updating-and-patching"></a>Aggiornamento e applicazione di patch

1. **Come si esegue l'aggiornamento a una nuova versione o a una nuova edizione di SQL Server in una VM di Azure?**

   Attualmente non è disponibile alcun aggiornamento sul posto per SQL Server in esecuzione in una VM di Azure. Creare una nuova macchina virtuale di Azure con la versione o l'edizione di SQL Server voluta, quindi eseguire la migrazione dei database nel nuovo server tramite [tecniche di migrazione dati](virtual-machines-windows-migrate-sql.md) standard.

1. **Come si applicano gli aggiornamenti e i Service Pack a una VM di SQL Server?**

   Le macchine virtuali consentono di controllare il computer host e di decidere quindi quando e come applicare gli aggiornamenti. Per il sistema operativo, è possibile applicare manualmente gli aggiornamenti di Windows oppure abilitare un servizio di pianificazione definito [Applicazione automatica delle patch](virtual-machines-windows-sql-automated-patching.md). Applicazione automatica delle patch installa tutti gli aggiornamenti contrassegnati come importanti, inclusi gli aggiornamenti di SQL Server in tale categoria. Gli aggiornamenti facoltativi di SQL Server devono essere installati manualmente.

## <a name="general"></a>Generale

1. **Le istanze del cluster di failover di SQL Server sono supportate nelle macchine virtuali di Azure?**

   Sì. È possibile [creare un cluster di failover di Windows in Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) e usare Spazi di archiviazione diretta (S2D) per l'archiviazione del cluster. In alternativa, è possibile usare soluzioni di clustering o archiviazione di terze parti come descritto in [Disponibilità elevata e ripristino di emergenza per SQL Server nelle macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > A questo punto, l'[Estensione Agente IaaS di SQL Server](virtual-machines-windows-sql-server-agent-extension.md) non è supportata per le istanze del cluster di failover di SQL Server in Azure. È consigliabile disinstallare l'estensione dalle macchine virtuali che fanno parte delle istanze del cluster di failover. Questa estensione supporta funzionalità quali Backup automatizzato, Applicazione automatica delle patch e alcune funzionalità del portale per SQL. Queste funzionalità non funzioneranno per le macchine virtuali di SQL dopo la disinstallazione dell'agente.

1. **Qual è la differenza tra VM di SQL Server e servizio Database SQL?**

   Dal punto di vista concettuale l'esecuzione di SQL Server in una macchina virtuale di Azure non è diversa dall'esecuzione di SQL Server in un centro dati remoto. Per contro, il servizio [Database SQL](../../../sql-database/sql-database-technical-overview.md) offre una soluzione DaaS (Database-as-a-service). Con Database SQL non si ha accesso ai computer che ospitano i database. Per un confronto completo, vedere [Scegliere un'opzione di SQL Server cloud: database SQL di Azure (PaaS) o SQL Server in macchine virtuali di Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Come si installa SQL Server Data Tools in una VM di Azure?**

    Scaricare e installare SQL Server Data Tools da [Microsoft SQL Server Data Tools - Business Intelligence per Visual Studio 2013](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

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
