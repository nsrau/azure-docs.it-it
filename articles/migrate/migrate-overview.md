---
title: Informazioni su Azure Migrate | Microsoft Docs
description: Panoramica del servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 02/26/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 45eac1d1ecb173ba0a62ab13f47b7ee6e12f7af3
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="about-azure-migrate"></a>Informazioni su Azure Migrate

Il servizio Azure Migrate valuta i carichi di lavoro locali per la migrazione ad Azure. Il servizio valuta l'idoneità alla migrazione dei computer locali in Azure e il dimensionamento in base alle prestazioni e fornisce stime dei costi per l'esecuzione dei computer locali in Azure. È il servizio ideale se si stanno prendendo in considerazione migrazioni in modalità lift-and-shift o si affrontano le prime fasi di valutazione della migrazione. Al termine della valutazione, è possibile usare servizi come [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) e [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview), per eseguire la migrazione dei computer in Azure.

## <a name="why-use-azure-migrate"></a>Vantaggi di Azure Migrate

Azure Migrate consente di eseguire le operazioni seguenti.

- **Valutare l'idoneità per Azure**: valutare se i computer locali sono idonei per l'esecuzione in Azure. 
- **Ottenere informazioni sulle dimensioni consigliate**: ottenere informazioni sulle dimensioni consigliate per le macchine virtuali di Azure in base alla cronologia delle prestazioni delle macchine virtuali locali. 
- **Stimare i costi mensili**: ottenere una stima dei costi per l'esecuzione dei computer locali in Azure.  
- **Eseguire la migrazione in tutta tranquillità**: visualizzare le dipendenze dei computer locali per creare gruppi di computer di cui eseguire la valutazione e la migrazione insieme. 

## <a name="current-limitations"></a>Limitazioni correnti

- Attualmente è possibile valutare solo macchine virtuali (VM) VMware locali per la migrazione a VM di Azure. Le VM VMware devono essere gestite dal server vCenter (versione 5.5, 6.0 o 6.5)

> [!NOTE]
> Il supporto per Hyper-V è in programma e sarà disponibile a breve. Nel frattempo, per pianificare la migrazione di carichi di lavoro Hyper-V è consigliabile usare [Azure Site Recovery Deployment Planner](http://aka.ms/asr-dp-hyperv-doc). 

- È possibile individuare fino a 1000 VM in una singola individuazione e fino a 1500 VM in un singolo progetto. È inoltre possibile valutare fino a 400 VM in una singola valutazione. Se è necessario individuarne o valutarne di più, si può aumentare il numero di individuazioni o valutazioni. [Altre informazioni](how-to-scale-assessment.md).
- È possibile creare un progetto Azure Migrate solo nell'area Stati Uniti centro-occidentali o Stati Uniti orientali. Ciò non impedisce, tuttavia, di pianificare una migrazione per una diversa località di Azure di destinazione. La località del progetto di migrazione viene usata solo per l'archiviazione dei metadati individuati nell'ambiente locale.
- Azure Migrate supporta solo dischi gestiti per la valutazione della migrazione.

## <a name="what-do-i-need-to-pay-for"></a>Costo del servizio

Altre informazioni sui prezzi di Azure Migrate sono disponibili [qui](https://azure.microsoft.com/en-in/pricing/details/azure-migrate/).


## <a name="whats-in-an-assessment"></a>Elementi inclusi nella valutazione

Una valutazione consente di identificare l'idoneità di Azure per le macchine virtuali locali e ottenere indicazioni sulle dimensioni consigliate e sui costi stimati per l'esecuzione delle macchine virtuali in Azure. È possibile personalizzare le valutazioni in base alle esigenze modificando le relative proprietà. Di seguito sono elencate le proprietà che vengono considerate nella creazione di una valutazione. 

**Proprietà** | **Dettagli**
--- | ---
**Posizione di destinazione** | Area di Azure in cui si vuole eseguire la migrazione.<br/><br/>Azure Migrate supporta attualmente 30 aree, tra cui Asia orientale, Asia sud-orientale, Australia orientale, Australia sud-orientale, Brasile meridionale, Canada centrale, Canada orientale, Cina orientale, Cina settentrionale, Corea centrale, Corea meridionale, Europa occidentale, Europa settentrionale, Germania centrale, Germania nordorientale, Giappone occidentale, Giappone orientale, India centrale, India meridionale, India occidentale, Regno Unito meridionale, Regno Unito occidentale, Stati Uniti centrali, Stati Uniti centro-meridionali, Stati Uniti centro-occidentali, Stati Uniti centro-settentrionali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali e Stati Uniti occidentali 2. La località predefinita è Stati Uniti occidentali 2. 
**Ridondanza dell'archiviazione** | Tipo di [ridondanza dell'archiviazione](https://docs.microsoft.com/azure/storage/common/storage-redundancy) che le VM di Azure useranno dopo la migrazione. Per impostazione predefinita viene usata l'archiviazione con ridondanza locale. Si noti che Azure Migrate supporta solo valutazioni basate su Managed Disks e il servizio Managed Disks supporta solo l'archiviazione con ridondanza locale, per questo motivo la proprietà attualmente prevede solo l'opzione di archiviazione con ridondanza locale. 
**Criterio di dimensionamento** | Criterio che Azure Migrate deve usare per dimensionare correttamente le VM per Azure. È possibile definire il dimensionamento in base alla *cronologia delle prestazioni* delle VM locali o definire le dimensioni delle VM *come in locale* per Azure, senza considerare la cronologia delle prestazioni. Per impostazione predefinita viene usato il dimensionamento in base alle prestazioni.
**Piani dei prezzi** | Per il calcolo dei costi, la valutazione considera l'eventuale iscrizione a Software Assurance e l'idoneità per il [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Vengono considerate anche le [offerte di Azure](https://azure.microsoft.com/support/legal/offer-details/) eventualmente sottoscritte ed è possibile specificare gli sconti specifici della sottoscrizione (%) di cui si può usufruire con l'offerta. 
**Piano tariffario** | È possibile specificare il [piano tariffario (Basic o Standard)](../virtual-machines/windows/sizes-general.md) per le VM di Azure di destinazione. Se, ad esempio, si prevede di eseguire la migrazione di un ambiente di produzione, considerare il livello Standard, che offre VM con bassa latenza, anche se a un costo superiore. D'altra parte, se si ha un ambiente di sviluppo e test, potrebbe essere preferibile il livello Basic, che ha VM con latenza maggiore e costi minori. Per impostazione predefinita, viene usato il livello [Standard](../virtual-machines/windows/sizes-general.md).
**Cronologia delle prestazioni** | Applicabile solo se il criterio di dimensionamento è basato sulle prestazioni. Per impostazione predefinita, Azure Migrate valuta le prestazioni dei computer locali usando la cronologia delle prestazioni dell'ultimo giorno, con un valore percentile del 95%. È possibile modificare questi valori nelle proprietà della valutazione. 
**Fattore di comfort** | Durante la valutazione, Azure Migrate considera un buffer (fattore di comfort), che viene applicato ai dati sull'utilizzo delle VM (CPU, memoria, disco e rete). Il fattore di comfort tiene conto di aspetti come utilizzo stagionale, breve cronologia delle prestazioni e probabile aumento dell'utilizzo futuro.<br/><br/> Da una VM con 10 core e un utilizzo del 20%, ad esempio, si ottiene normalmente una VM con 2 core. Con un fattore di comfort pari a 2.0x, invece, il risultato è una VM con 4 core. L'impostazione predefinita del fattore di comfort è 1.3x.


## <a name="how-does-azure-migrate-work"></a>Funzionamento di Azure Migrate

1.  Creare un progetto Azure Migrate.
2.  Azure Migrate usa una VM locale denominata appliance dell'agente di raccolta per individuare informazioni sui computer locali. Per creare l'appliance, scaricare un file di installazione in formato Open Virtualization Appliance (con estensione ova) e importarlo come macchina virtuale nel server vCenter locale.
3.  Connettersi alla macchina virtuale mediante connessione alla console nel server vCenter, specificare una nuova password per la macchina virtuale durante la connessione e quindi eseguire l'applicazione agente di raccolta nella macchina virtuale per avviare l'individuazione.
4.  L'agente di raccolta raccoglie i metadati delle VM usando cmdlet di VMware PowerCLI. L'individuazione è senza agente e non comporta installazioni nelle VM o negli host VMware. I metadati raccolti includono informazioni sulle VM come core, memoria, dischi, dimensioni dei dischi e schede di rete. Vengono anche raccolti dati sulle prestazioni delle VM, tra cui utilizzo di CPU e memoria, operazioni di I/O al secondo e velocità effettiva (in MBps) dei dischi e output di rete (in MBps).
5.  I metadati vengono inseriti nel progetto Azure Migrate e possono essere visualizzati nel portale di Azure.
6.  Ai fini della valutazione, riunire le macchine virtuali individuate in gruppi. È ad esempio possibile raggruppare le macchine virtuali che eseguono la stessa applicazione. Per un raggruppamento più preciso, è anche possibile usare la visualizzazione delle dipendenze per verificare le dipendenze per un computer specifico o per tutti i computer di un gruppo e ottimizzare il gruppo.
7.  Una volta creato il gruppo, creare una valutazione per il gruppo. 
8.  Dopo che è stata completata, la valutazione può essere visualizzata nel portale o scaricata in formato Excel.



  ![Architettura della pianificazione in Azure](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>Requisiti relativi alle porte

La tabella offre un riepilogo delle porte necessarie per le comunicazioni di Azure Migrate.

|Componente          |Comunicazione con     |Porta necessaria  |Motivo   |
|-------------------|------------------------|---------------|---------|
|Agente di raccolta          |Servizio Azure Migrate   |TCP 443        |L'agente di raccolta si connette al servizio sulla porta SSL 443.|
|Agente di raccolta          |Server vCenter          |9443 (impostazione predefinita)   | Per impostazione predefinita, l'agente di raccolta si connette al server vCenter sulla porta 9443. Se il server è in ascolto su una porta diversa, la porta deve essere configurata come porta in uscita nella macchina virtuale dell'agente di raccolta. |
|VM locale     | Area di lavoro di Operations Management Suite (OMS)          |[TCP 443](../log-analytics/log-analytics-windows-agent.md) |L'agente MMA usa la porta TCP 443 per la connessione a Log Analytics. Questa porta è necessaria solo se si usa la funzionalità di visualizzazione delle dipendenze e si installa Microsoft Monitoring Agent (MMA). |


  
## <a name="what-happens-after-assessment"></a>Operazioni successive alla valutazione

Dopo aver valutato i computer locali per la migrazione con il servizio Azure Migrate, è possibile usare alcuni strumenti per eseguire la migrazione.

- **Azure Site Recovery**: è possibile usare Azure Site Recovery per eseguire la migrazione ad Azure come descritto di seguito.
  - Preparare le risorse di Azure, includendo una sottoscrizione di Azure, una rete virtuale di Azure e un account di archiviazione.
  - Preparare i server VMware locali per la migrazione. Verificare i requisiti di supporto di VMware per Site Recovery, preparare i server VMware per l'individuazione e preparare l'installazione del servizio Mobility di Site Recovery nelle VM di cui si vuole eseguire la migrazione. 
  - Configurare la migrazione. Configurare un insieme di credenziali dei servizi di ripristino, le impostazioni di migrazione per l'origine e la destinazione e i criteri di replica e quindi abilitare la replica. È possibile eseguire un'esercitazione sul ripristino di emergenza per verificare che la migrazione di una VM ad Azure funzioni correttamente.
  - Effettuare un failover per eseguire la migrazione dei computer locali ad Azure. 
  - [Vedere altre informazioni](../site-recovery/tutorial-migrate-on-premises-to-azure.md) nell'esercitazione sulla migrazione con Site Recovery.

- **Migrazione del database di Azure**: se i computer locali eseguono un database come SQL Server, MySQL oppure Oracle, è possibile eseguirne la migrazione ad Azure usando il servizio Migrazione del database di Azure. [Altre informazioni](https://azure.microsoft.com/campaigns/database-migration/).



## <a name="next-steps"></a>Passaggi successivi 
[Completare un'esercitazione](tutorial-assessment-vmware.md) per creare una valutazione per una VM VMware locale.