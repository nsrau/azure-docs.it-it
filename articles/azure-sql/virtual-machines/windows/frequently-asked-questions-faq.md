---
title: Domande frequenti su SQL Server in macchine virtuali Windows in Azure| Microsoft Docs
description: Questo articolo offre risposta ad alcune domande frequenti sull'esecuzione di SQL Server in macchine virtuali di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: d3d8908739d6dda76f4c3d44540c36b36115d6f5
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289398"
---
# <a name="frequently-asked-questions-for-sql-server-on-azure-vms"></a>Domande frequenti per SQL Server in macchine virtuali di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](frequently-asked-questions-faq.md)
> * [Linux](../linux/frequently-asked-questions-faq.md)

Questo articolo fornisce le risposte ad alcune delle domande più comuni sull'esecuzione di [SQL Server in macchine virtuali (VM) di Windows Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Immagini

1. **Quali immagini della raccolta di macchine virtuali di SQL Server sono disponibili?** 

   Azure mantiene le immagini delle macchine virtuali per tutte le principali versioni supportate di SQL Server in tutte le edizioni sia per Linux che per Windows. Per altre informazioni, vedere l’elenco completo di [immagini di macchine virtuali Windows](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) e di [immagini di macchine virtuali Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md#create).

1. **Le immagini della raccolta di macchine virtuali di SQL Server esistenti vengono aggiornate?**

   Ogni due mesi, le immagini di SQL Server nella raccolta di macchine virtuali vengono aggiornate con gli aggiornamenti di Linux e Windows più recenti. Per le immagini Windows, si includono tutti gli aggiornamenti contrassegnati come importanti in Windows Update, tra cui gli aggiornamenti di sicurezza e i Service Pack di SQL Server importanti. Per le immagini Linux si includono gli aggiornamenti di sistema più recenti. Gli aggiornamenti cumulativi di SQL Server vengono gestiti in modo diverso per Linux e Windows. Per Linux, gli aggiornamenti cumulativi di SQL Server sono inclusi nell'aggiornamento. Tuttavia al momento le macchine virtuali Windows non vengono aggiornate con gli aggiornamenti cumulativi di SQL Server o Windows Server.

1. **Le immagini di macchine virtuali di SQL Server possono essere rimosse dalla raccolta?**

   Sì. Azure mantiene solo un'immagine per ogni versione e per ogni edizione principale. Ad esempio, quando viene rilasciato un nuovo Service Pack di SQL Server, Azure aggiunge una nuova immagine alla raccolta per quel Service Pack. L'immagine di SQL Server del Service Pack precedente viene immediatamente rimossa dal portale di Azure. Tuttavia è ancora disponibile per il provisioning di PowerShell per i successivi tre mesi. Dopo tre mesi, l'immagine del Service Pack precedente non è più disponibile. Questi criteri di rimozione sono applicabili anche se una versione di SQL Server non è più supportata perché raggiunge la fine del ciclo di vita.


1. **È possibile distribuire un'immagine meno recente di SQL Server che non è visibile nel portale di Azure?**

   Sì, con PowerShell. Per altre informazioni sulla distribuzione di macchine virtuali di SQL Server usando PowerShell, consultare [Come eseguire il provisioning di macchine virtuali di SQL Server con Azure PowerShell](create-sql-vm-powershell.md).
   
1. **È possibile creare un'immagine generalizzata di Azure Marketplace SQL Server della macchina virtuale SQL Server e usarla per distribuire le VM?**

   Sì, ma è necessario [registrare ogni macchina virtuale di SQL Server con il provider di risorse di VM di SQL Server](sql-vm-resource-provider-register.md) per gestire la macchina virtuale di SQL Server nel portale, nonché usare funzionalità come l'applicazione automatica di patch e i backup automatici. Quando si esegue la registrazione con il provider di risorse, è necessario specificare anche il tipo di licenza per ogni macchina virtuale di SQL Server.

1. **Come è possibile generalizzare SQL Server nella macchina virtuale di Azure al fine di usarlo per distribuire nuove VM?**

   È possibile distribuire una macchina virtuale di Windows Server (senza SQL Server installato) e usare il processo [SQL sysprep](/sql/database-engine/install-windows/install-sql-server-using-sysprep) per generalizzare SQL Server nella VM di Azure (Windows) con il supporto di installazione di SQL Server. I clienti che usano [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3) possono ottenere il supporto di installazione dal [Centro per i contratti multilicenza](https://www.microsoft.com/Licensing/servicecenter/default.aspx). I clienti che non hanno Software Assurance possono usare il supporto di installazione di Azure Marketplace SQL Server immagine di macchina virtuale con l'edizione desiderata.

   In alternativa, è possibile usare una delle immagini SQL Server di Azure Marketplace per generalizzare SQL Server nella macchina virtuale di Azure. Si noti che è necessario eliminare la chiave del Registro di sistema seguente nell'immagine di origine prima di creare un'immagine personalizzata. In caso contrario, è possibile che si verifichi il blot della cartella di bootstrap di installazione di SQL Server e/o che l'estensione SQL IaaS sia in stato di errore.

   Percorso della chiave del Registro di sistema:  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > Per soddisfare i requisiti di conformità e per usare funzionalità facoltative come l'applicazione automatica di patch e i backup automatici, è necessario che le istanze di SQL Server nelle macchine virtuali di Azure, incluse quelle distribuite da immagini generalizzate personalizzate, vengano [registrate con il provider di risorse VM SQL](./sql-vm-resource-provider-register.md?tabs=azure-cli%252cbash). Il provider di risorse consente inoltre di [specificare il tipo di licenza](./licensing-model-azure-hybrid-benefit-ahb-change.md?tabs=azure-portal) per ogni macchina virtuale di SQL Server.

1. **È possibile usare il proprio disco rigido virtuale per distribuire una macchina virtuale di SQL Server?**

   Sì, ma è necessario [registrare ogni macchina virtuale di SQL Server con il provider di risorse di VM di SQL Server](sql-vm-resource-provider-register.md) per gestire la macchina virtuale di SQL Server nel portale, nonché usare funzionalità come l'applicazione automatica di patch e i backup automatici.

1. **È possibile configurare configurazioni non visualizzate nella raccolta di macchine virtuali (ad esempio Windows 2008 R2 + SQL Server 2012)?**

   No. Per le raccolte di macchine virtuali che includono SQL Server è necessario selezionare una delle immagini disponibili sia dal portale di Azure che tramite [PowerShell](create-sql-vm-powershell.md). Tuttavia, si ha la possibilità di distribuire una macchina virtuale Windows e installarvi automaticamente SQL Server. È quindi necessario [registrare la macchina virtuale di SQL Server con il provider di risorse SQL Server VM](sql-vm-resource-provider-register.md) per gestire la macchina virtuale SQL Server nel portale di Azure, nonché usare funzionalità come l'applicazione automatica di patch e i backup automatici. 


## <a name="creation"></a>Creazione

1. **Come si crea una macchina virtuale di Azure con SQL Server?**

   Il metodo più semplice consiste nel creare una macchina virtuale che includa SQL Server. Per un'esercitazione sulla registrazione in Azure e sulla creazione di una macchina virtuale di SQL Server dal portale, vedere [Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure](create-sql-vm-portal.md). È possibile selezionare un'immagine di macchina virtuale che usa la licenza di SQL Server con costo al secondo oppure usare un'immagine che consente di trasferire la licenza di SQL Server dell'utente. È anche possibile installare manualmente in una macchina virtuale un'edizione di SQL Server con licenza gratuita (Developer o Express) o riutilizzando una licenza locale. Verificare di [registrare la macchina virtuale di SQL Server con il provider di risorse di VM di SQL Server](sql-vm-resource-provider-register.md) per gestire la macchina virtuale di SQL Server nel portale, nonché usare funzionalità come l'applicazione automatica di patch e i backup automatici. Se si usa la funzionalità Bring Your Own License, è necessario avere [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/). Per altre informazioni, vedere [Pricing guidance for SQL Server Azure VMs](pricing-guidance.md) (Guida ai prezzi per le VM di SQL Server in Azure).

1. **Come è possibile eseguire la migrazione del database di SQL Server locale al cloud?**

   Creare prima una macchina virtuale di Azure con un'istanza di SQL Server, quindi eseguire la migrazione dei database locali in tale istanza. Per alcune strategie di migrazione dei dati, vedere [Eseguire la migrazione di un database di SQL Server a SQL Server in una macchina virtuale di Azure](migrate-to-vm-from-sql-server.md).

## <a name="licensing"></a>Gestione delle licenze

1. **Come si installa una copia di SQL Server con licenza in una VM di Azure?**

   Questa operazione può essere eseguita in tre modi. Se si è un cliente Enterprise Agreement (EA), è possibile effettuare il provisioning di una delle [Immagini di macchina virtuale che supporta le licenze](sql-server-on-azure-vm-iaas-what-is-overview.md#BYOL), nota anche come Bring-your-own-License (BYOL). Se si dispone di [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default), è possibile abilitare il [vantaggio Azure Hybrid](licensing-model-azure-hybrid-benefit-ahb-change.md) su un'immagine con pagamento in base al consumo (PAYG) esistente. Oppure è possibile copiare il supporto di installazione di SQL Server nella macchina virtuale di Windows Server e quindi installare SQL Server nella macchina virtuale. Verificare di registrare la macchina virtuale di SQL Server con il [provider di risorse](sql-vm-resource-provider-register.md) per ottenere funzionalità quali gestione del portale, backup automatizzato e applicazione di patch automatica. 

1. **È possibile modificare una VM per l'uso di una licenza di SQL Server, se è stata creata da una delle immagini della raccolta con pagamento in base al consumo?**

   Sì. È possibile convertire con facilità un'immagine dalla raccolta con pagamento in base al consumo in Bring Your Own License abilitando il [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/faq/).  Per altre informazioni, vedere [Come cambiare il livello di licenza per una macchina virtuale SQL Server](licensing-model-azure-hybrid-benefit-ahb-change.md). Attualmente questa funzionalità è disponibile solo per i clienti del cloud pubblico e di Azure per enti pubblici.

1. **Per cambiare modello di licenza sono necessari tempi di inattività di SQL Server?**

   No. Per [modificare il modello di licenza](licensing-model-azure-hybrid-benefit-ahb-change.md) non è necessario alcun tempo di inattività di SQL Server perché la modifica ha effetto immediato e non richiede un riavvio della macchina virtuale. Tuttavia, per registrare la macchina virtuale di SQL Server con il provider di risorse di macchine virtuali di SQL Server, l' [estensione IaaS di SQL](sql-server-iaas-agent-extension-automate-management.md) costituisce un prerequisito e l'installazione dell'estensione SQL IaaS in modalità _estesa_ comporta il riavvio del servizio SQL Server. Di conseguenza, se è necessario installare l'estensione SQL IaaS, è possibile scegliere la modalità _leggera_ per ottenere funzionalità limitate oppure installarla in modalità _estesa_ durante una finestra di manutenzione. L'estensione SQL IaaS installata in modalità _leggera_ può essere aggiornata alla modalità _estesa_ in qualsiasi momento, ma richiede il riavvio del servizio SQL Server. 
   
1. **È possibile cambiare i modelli di licenza in una macchina virtuale SQL Server distribuita con il modello classico?**

   No. La modifica di modelli di licenza non è supportata in una macchina virtuale classica. È possibile eseguire la migrazione della macchina virtuale al modello di Azure Resource Manager e registrarsi con il provider di risorse della macchina virtuale di SQL Server. Dopo che la macchina virtuale è stata registrata con il provider di risorse della macchina virtuale di SQL Server, le modifiche al modello di licenza saranno disponibili nella macchina virtuale.

1. **È possibile usare il portale di Azure per gestire più istanze nella stessa macchina virtuale?**

   No. La gestione del portale è una funzionalità fornita dal provider di risorse della macchina virtuale di SQL Server, che si basa sull'estensione dell'agente IaaS di SQL Server. Di conseguenza, le stesse limitazioni si applicano al provider di risorse per l'estensione. Il portale può gestire solo un'istanza predefinita o un'istanza denominata, purché sia stata configurata correttamente. Per altre informazioni su queste limitazioni, vedere [Estensione agente SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md). 

1. **Le sottoscrizioni CSP possono attivare Vantaggio Azure Hybrid?**

   Sì. Vantaggio Azure Hybrid è disponibile per le sottoscrizioni CSP. I clienti CSP devono prima di tutto distribuire un'immagine con pagamento in base al consumo e quindi [cambiare il modello di licenza](licensing-model-azure-hybrid-benefit-ahb-change.md) in Bring Your Own License.
   
 
1. **È necessario pagare la licenza di SQL Server in una VM di Azure se viene utilizzata solo per standby/failover?**

   Per avere una licenza passiva gratuita per un gruppo di disponibilità secondario di standby o un'istanza con cluster di failover, è necessario soddisfare tutti i criteri seguenti, come descritto nelle [condizioni di licenza del prodotto](https://www.microsoft.com/licensing/product-licensing/products):

   1. Si dispone di [mobilità delle licenze](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) tramite [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3). 
   1. L'istanza passiva di SQL Server non serve dati di SQL Server ai client né esegue carichi di lavoro attivi di SQL Server. Viene usata solo per la sincronizzazione con il server primario o altrimenti mantenere il database passivo in uno stato di warm standby. Se serve dati, come report per i client che eseguono carichi di lavoro attivi di SQL Server o che eseguono lavori diversi da quelli specificati nelle condizioni del prodotto, deve trattarsi di un'istanza di SQL Server a pagamento con licenza. Nell'istanza secondaria sono consentite le attività seguenti: verifiche coerenza del database o CheckDB, backup completi, backup del log delle transazioni e monitoraggio dei dati di utilizzo delle risorse. È anche possibile eseguire simultaneamente, ogni 90 giorni, l'istanza primaria e corrispondente di ripristino di emergenza per brevi periodi di test del ripristino di emergenza. 
   1. La licenza attiva di SQL Server è coperta da Software Assurance e consente di usare **un** 'istanza di SQL Server secondaria passiva, solo con una quantità di risorse di calcolo massima pari a quella del server attivo con licenza. 
   1. La macchina virtuale secondaria di SQL Server usa la licenza per il [ripristino di emergenza](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) nel portale di Azure.
   
1. **Che cosa viene considerato istanza passiva?**

   L'istanza passiva di SQL Server non serve dati di SQL Server ai client né esegue carichi di lavoro attivi di SQL Server. Viene usata solo per la sincronizzazione con il server primario o altrimenti mantenere il database passivo in uno stato di warm standby. Se serve dati, come report per i client che eseguono carichi di lavoro attivi di SQL Server o che eseguono lavori diversi da quelli specificati nelle condizioni del prodotto, deve trattarsi di un'istanza di SQL Server a pagamento con licenza. Nell'istanza secondaria sono consentite le attività seguenti: verifiche coerenza del database o CheckDB, backup completi, backup del log delle transazioni e monitoraggio dei dati di utilizzo delle risorse. È anche possibile eseguire simultaneamente, ogni 90 giorni, l'istanza primaria e corrispondente di ripristino di emergenza per brevi periodi di test del ripristino di emergenza.
   

1. **Quali scenari possono utilizzare il Vantaggio Ripristino di emergenza?**

   La [guida alle licenze](https://aka.ms/sql2019licenseguide) offre scenari in cui è possibile usare il Vantaggio Ripristino di emergenza. Per altre informazioni fare riferimento alle condizioni del prodotto e comunicare con i contatti della licenza o con l'account manager.

1. **Quali sottoscrizioni supportano il Vantaggio Ripristino di emergenza?**

   I programmi completi che offrono diritti di sottoscrizione equivalenti a Software Assurance come vantaggio fisso supportano il Vantaggio Ripristino di emergenza. Sono inclusi, ma non è limitato a, il valore aperto (OV), la sottoscrizione Open Value (OVS), il Enterprise Agreement (EA), la sottoscrizione Enterprise Agreement (EAS) e la registrazione del server e del cloud (SCE). Per altre informazioni fare riferimento alle [condizioni del prodotto](https://www.microsoft.com/licensing/product-licensing/products) e comunicare con i contatti della licenza o con l'account manager. 

   
 ## <a name="resource-provider"></a>Provider di risorse

1. **La registrazione di una macchina virtuale con il nuovo provider di risorse di macchine virtuali di SQL Server comporta costi aggiuntivi?**

   No. Il provider di risorse di macchine virtuali di SQL Server consente di migliorare la gestione di SQL Server in macchine virtuali di Azure senza spese aggiuntive. 

1. **Il provider di risorse di macchine virtuali di SQL Server è disponibile per tutti i clienti?**
 
   Sì, purché la macchina virtuale di SQL Server sia stata distribuita nel cloud pubblico usando il modello di Resource Manager e non il modello classico. Tutti gli altri clienti possono registrarsi al nuovo provider di risorse di macchine virtuali di SQL Server. Tuttavia, solo i clienti con il vantaggio di [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) possono usare la propria licenza attivando il [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) in una macchina virtuale di SQL Server. 

1. **Cosa accade alla risorsa del provider di risorse ( _Microsoft.SqlVirtualMachine_ ) se la risorsa della macchina virtuale viene spostata o eliminata?** 

   Quando la risorsa Microsoft.Compute/VirtualMachine viene eliminata o spostata, alla risorsa Microsoft.SqlVirtualMachine associata viene notificata la necessità di replicare in modo asincrono l'operazione.

1. **Cosa accade alla macchina virtuale se la risorsa del provider di risorse ( _Microsoft.SqlVirtualMachine_ ) viene eliminata?**

    Quando viene eliminata la risorsa Microsoft.SqlVirtualMachine, la risorsa Microsoft.Compute/VirtualMachine non è compromessa. Tuttavia, le modifiche della licenza verranno riportate all’immagine originale. 

1. **È possibile registrare macchine virtuali di SQL Server distribuite autonomamente con il provider di risorse di macchine virtuali di SQL Server?**

    Sì. Se SQL Server è stato distribuito dal proprio supporto e l'estensione di SQL IaaS è stata installata, è possibile registrare la macchina virtuale di SQL Server sul provider di risorse per migliorare la gestione con l’estensione IaaS di SQL.    


## <a name="administration"></a>Amministrazione

1. **È possibile installare una seconda istanza di SQL Server nella stessa VM? È possibile modificare le funzionalità installate nell'istanza predefinita?**

   Sì. Il supporto di installazione di SQL Server si trova in una cartella nell'unità **C** . Eseguire **Setup.exe** da tale percorso per aggiungere nuove istanze di SQL Server o per modificare altre funzionalità di SQL Server installate nella macchina virtuale. Si noti che alcune funzionalità, ad esempio Backup automatizzato, Applicazione automatica delle patch e Integrazione di Azure Key Vault, funzionano solo nell'istanza predefinita o per un'istanza denominata che è stata adeguatamente configurata (vedere la domanda 3). I clienti che usano [Software Assurance tramite il vantaggio Azure Hybrid](licensing-model-azure-hybrid-benefit-ahb-change.md) o il modello di gestione delle licenze con **pagamento in base** al consumo possono installare più istanze di SQL Server nella macchina virtuale senza sostenere costi aggiuntivi per le licenze. Altre istanze di SQL Server possono filtrare le risorse di sistema a meno che non siano configurate correttamente 

1. **Qual è il numero massimo di istanze in una macchina virtuale?**
   SQL Server 2012 SQL Server 2019 può supportare le [istanze 50](/sql/sql-server/editions-and-components-of-sql-server-version-15#RDBMSSP) in un server autonomo. Si tratta dello stesso limite indipendentemente da Azure in locale. Vedere le [procedure consigliate](performance-guidelines-best-practices.md#multiple-instances) per apprendere come preparare meglio l'ambiente. 

1. **È possibile disinstallare l'istanza predefinita di SQL Server?**

   Sì, ma ci sono alcune considerazioni di cui tenere conto. Per prima cosa, la fatturazione associata a SQL Server può continuare a essere eseguita in base al modello di licenza della macchina virtuale. Come indicato nella risposta precedente, esistono funzionalità che si basano sull'[estensione SQL Server IaaS Agent](sql-server-iaas-agent-extension-automate-management.md). Se si disinstalla l'istanza predefinita senza rimuovere anche l'estensione IaaS, l'estensione continua a cercare l'istanza predefinita e può generare errori del log eventi. Questi errori provengono dalle due origini seguenti: **Microsoft SQL Server Credential Management** e **Microsoft SQL Server IaaS Agent**. Uno degli errori potrebbe essere simile al seguente:

      Si è verificato un errore di rete o specifico dell'istanza mentre veniva stabilita la connessione a SQL Server. Il server non è stato trovato o non è accessibile.

   Se si decide di disinstallare l'istanza predefinita, disinstallare anche l'[estensione SQL Server IaaS Agent](sql-server-iaas-agent-extension-automate-management.md). 

1. **È possibile utilizzare un'istanza denominata di SQL Server con l'estensione IaaS?**
   
   Sì, se l'istanza denominata è l'unica istanza di SQL Server e se l'istanza predefinita originale è stata [disinstallata correttamente](sql-server-iaas-agent-extension-automate-management.md#named-instance-support). Se non è presente un'istanza predefinita e sono presenti più istanze denominate in una singola macchina virtuale di SQL Server, l'installazione dell'estensione SQL Server IaaS Agent non verrà completata.  

1. **È possibile rimuovere SQL Server e la fatturazione della licenza associata da una macchina virtuale SQL Server?**

   Sì, ma è necessario eseguire passaggi aggiuntivi per evitare che venga addebitata l'istanza di SQL Server, come descritto in [linee guida sui prezzi](pricing-guidance.md). Se si vuole rimuovere completamente l'istanza di SQL Server, è possibile eseguire la migrazione a un'altra macchina virtuale di Azure senza SQL Server pre-installato nella macchina virtuale ed eliminare la macchina virtuale di SQL Server corrente. Se si vuole che la macchina virtuale venga mantenuta ma si interrompa SQL Server fatturazione, attenersi alla procedura seguente: 

   1. Eseguire il backup di tutti i dati, inclusi i database di sistema, se necessario. 
   1. Disinstallare SQL Server completamente, inclusa l'estensione SQL IaaS (se presente).
   1. Installare l'edizione gratuita di [SQL Express](https://www.microsoft.com/sql-server/sql-server-downloads).
   1. Eseguire la registrazione con il provider di risorse VM SQL in [modalità Lightweight](sql-vm-resource-provider-register.md).
   1. opzionale Disabilitare il servizio Express SQL Server disabilitando l'avvio del servizio. 

1. **È possibile usare il portale di Azure per gestire più istanze nella stessa macchina virtuale?**

   No. La gestione del portale viene fornita dal provider di risorse VM SQL, che si basa sull'estensione SQL Server agente IaaS. Di conseguenza, le stesse limitazioni si applicano al provider di risorse come estensione. Il portale può gestire solo un'istanza predefinita o un'istanza denominata purché sia configurata correttamente. Per altre informazioni, vedere [SQL Server estensione dell'agente IaaS](sql-server-iaas-agent-extension-automate-management.md) 


## <a name="updating-and-patching"></a>Aggiornamento e applicazione di patch

1. **Ricerca per categorie modificare una versione o un'edizione diversa di SQL Server in una macchina virtuale di Azure?**

   I clienti possono cambiare versione/edizione di SQL Server usando supporti di configurazione che contengono la versione o l'edizione desiderata di SQL Server. Dopo aver cambiato edizione, usare il portale di Azure per modificare la proprietà dell'edizione della macchina virtuale in modo che rispecchi correttamente la fatturazione per la macchina virtuale. Per altre informazioni, consultare l'articolo su come [cambiare edizione di una VM di SQL Server](change-sql-server-edition.md). Non esiste una differenza di fatturazione per versioni diverse di SQL Server, quindi dopo aver modificato la versione di SQL Server, non sono necessarie altre azioni.

1. **Dove posso trovare il supporto di configurazione per modificare l'edizione o la versione di SQL Server?**

   I clienti che usano [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) possono ottenere il supporto di installazione dal [Centro per i contratti multilicenza](https://www.microsoft.com/Licensing/servicecenter/default.aspx). I clienti che non hanno Software Assurance possono usare il supporto di installazione di Azure Marketplace SQL Server immagine di macchina virtuale con l'edizione desiderata.
   
1. **Come si applicano gli aggiornamenti e i Service Pack a una VM di SQL Server?**

   Le macchine virtuali consentono di controllare il computer host e di decidere quindi quando e come applicare gli aggiornamenti. Per il sistema operativo, è possibile applicare manualmente gli aggiornamenti di Windows oppure abilitare un servizio di pianificazione definito [Applicazione automatica delle patch](automated-patching.md). Applicazione automatica delle patch installa tutti gli aggiornamenti contrassegnati come importanti, inclusi gli aggiornamenti di SQL Server in tale categoria. Gli aggiornamenti facoltativi di SQL Server devono essere installati manualmente.

1. **È possibile aggiornare l'istanza di SQL Server 2008/2008 R2 dopo averla registrata con il provider di risorse di VM di SQL Server?**

   Se il sistema operativo è Windows Server 2008 R2 o versione successiva, sì. È possibile usare qualsiasi supporto di configurazione per aggiornare la versione e l'edizione di SQL Server, quindi è possibile aggiornare la [modalità di estensione SQL IaaS](sql-server-iaas-agent-extension-automate-management.md#management-modes) da _nessun agente_ a _estesa_. In questo modo si otterrà l'accesso a tutti i vantaggi dell'estensione SQL IaaS, ad esempio la gestibilità del portale, i backup automatici e l'applicazione automatica delle patch. Se la versione del sistema operativo è Windows Server 2008, è supportata solo la modalità noagent. 

1. **Come posso ottenere aggiornamenti della sicurezza estesi e gratuiti per la fine del supporto di SQL Server 2008 e le istanze di SQL Server 2008 R2?**

   È possibile ottenere [aggiornamenti della sicurezza estesi gratuiti](sql-server-2008-extend-end-of-support.md) spostando il SQL Server così com'è in una macchina virtuale di Azure. Per altre informazioni, consultare le [opzioni di fine del supporto](/sql/sql-server/end-of-support/sql-server-end-of-life-overview). 
  
   

## <a name="general"></a>Generale

1. **Le istanze del cluster di failover di SQL Server sono supportate nelle macchine virtuali di Azure?**

   Sì. È possibile installare un'istanza del cluster di failover usando [condivisioni file Premium (PFS)](failover-cluster-instance-premium-file-share-manually-configure.md) o [Spazi di archiviazione diretta (S2D)](failover-cluster-instance-storage-spaces-direct-manually-configure.md) per il sottosistema di archiviazione. Le condivisioni file Premium offrono capacità di operazioni di I/O al secondo e velocità effettiva in grado di soddisfare le esigenze di molti carichi di lavoro. Per i carichi di lavoro con I/O elevato, è consigliabile usare spazi di archiviazione diretta in base a dischi Premium o Ultra gestiti. In alternativa, è possibile usare soluzioni di clustering o archiviazione di terze parti come descritto in [disponibilità elevata e ripristino di emergenza per SQL Server in macchine virtuali di Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > A questo punto, l' [estensione SQL Server IaaS Agent](sql-server-iaas-agent-extension-automate-management.md) in modalità _estesa_ è supportato per le istanze del cluster di failover di SQL Server in Azure. Si consiglia di disinstallare l'estensione in modalità _estesa_ dalle macchine virtuali che fanno parte dell'istanza del cluster di failover e di installare l'estensione in modalità _leggera_. Questa estensione supporta funzionalità quali Backup automatizzato, Applicazione automatica delle patch e alcune funzionalità del portale per SQL Server. Queste funzionalità non funzioneranno per le macchine virtuali di SQL Server dopo la disinstallazione dell'agente in modalità _estesa_.

1. **Qual è la differenza tra VM di SQL Server e servizio Database SQL?**

   Dal punto di vista concettuale l'esecuzione di SQL Server in una macchina virtuale di Azure non è diversa dall'esecuzione di SQL Server in un centro dati remoto. Al contrario, il [database SQL di Azure](../../database/sql-database-paas-overview.md) offre database come servizio. Con Database SQL non si ha accesso ai computer che ospitano i database. Per un confronto completo, vedere [Scegliere un'opzione di SQL Server cloud: database SQL di Azure (PaaS) o SQL Server in macchine virtuali di Azure (IaaS)](../../azure-sql-iaas-vs-paas-what-is-overview.md).

1. **Come si installa SQL Server Data Tools in una VM di Azure?**

    Scaricare e installare SQL Server Data Tools da [Microsoft SQL Server Data Tools - Business Intelligence per Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **Le transazioni distribuite con Distributed Transaction Coordinator sono supportate nelle macchine virtuali di SQL Server?**
   
    Sì. Il controllo DTC locale è supportato per SQL Server 2016 SP2 e versioni successive. Tuttavia, le applicazioni devono essere testate quando si usa Gruppi di disponibilità Always On, perché le transazioni in corso durante un failover avranno esito negativo e sarà necessario tentare di eseguirle di nuovo. Il controllo DTC con cluster è disponibile a partire da Windows Server 2019. 

## <a name="resources"></a>Risorse

**Macchine virtuali Windows** :

* [Panoramica di SQL Server in una macchina virtuale Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Effettuare il provisioning di SQL Server in una macchina virtuale Windows](create-sql-vm-portal.md)
* [Migrazione di un database a SQL Server su una macchina virtuale di Azure](migrate-to-vm-from-sql-server.md)
* [Disponibilità elevata e ripristino di emergenza per SQL Server in macchine virtuali di Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](performance-guidelines-best-practices.md)
* [Modelli di applicazione e strategie di sviluppo per SQL Server in macchine virtuali di Azure](application-patterns-development-strategies.md)

**Macchine virtuali Linux** :

* [Panoramica di SQL Server in una macchina virtuale Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Effettuare il provisioning di SQL Server in una VM Linux](../linux/sql-vm-create-portal-quickstart.md)
* [Domande frequenti (Linux)](../linux/frequently-asked-questions-faq.md)
* [Documentazione di SQL Server in Linux](/sql/linux/sql-server-linux-overview)