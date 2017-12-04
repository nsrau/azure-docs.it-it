---
title: Informazioni su Azure Migrate | Microsoft Docs
description: Panoramica del servizio Azure Migrate.
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 7b313bb4-c8f4-43ad-883c-789824add3288
ms.service: migrate
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/23/2017
ms.author: raynew
ms.openlocfilehash: d3d5a3bcd3be55d1915ff7fdc6d82aebbb992fc7
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2017
---
# <a name="about-azure-migrate"></a>Informazioni su Azure Migrate

Il servizio Azure Migrate valuta i carichi di lavoro locali per la migrazione ad Azure. Il servizio valuta l'idoneità alla migrazione e il dimensionamento in base alle prestazioni e offre stime dei costi per l'esecuzione dei computer locali in Azure. È il servizio ideale se si stanno prendendo in considerazione migrazioni in modalità lift-and-shift o si affrontano le prime fasi di valutazione della migrazione. Al termine della valutazione, si possono usare servizi come Azure Site Recovery e Migrazione del database di Azure per eseguire la migrazione dei computer ad Azure.

> [!NOTE]
> Azure Migrate è attualmente in anteprima e supporta carichi di lavoro di produzione.

## <a name="why-use-azure-migrate"></a>Vantaggi di Azure Migrate

Azure Migrate consente di eseguire le operazioni seguenti.

- **Valutare l'idoneità per Azure**: valutare se i computer locali sono idonei per l'esecuzione in Azure. 
- **Ottenere le dimensioni consigliate**: dimensionamento consigliato per le VM di Azure dopo la migrazione, in base alla cronologia delle prestazioni delle VM locali. 
- **Stimare i costi mensili**: costi stimati per l'esecuzione dei computer locali in Azure.
- **Eseguire una migrazione con affidabilità elevata**: il raggruppamento dei computer locali per la valutazione consente di ottenere una valutazione più affidabile visualizzando le dipendenze. È possibile visualizzare con precisione le dipendenze per un computer specifico o per tutti i computer di un gruppo.

## <a name="current-limitations"></a>Limitazioni correnti

- Attualmente è possibile valutare macchine virtuali (VM) VMware locali per la migrazione a VM di Azure.
> [!NOTE]
> Il supporto per Hyper-V è in programma e verrà abilitato tra pochi mesi. Nel frattempo, per pianificare la migrazione di carichi di lavoro Hyper-V è consigliabile usare Azure Site Recovery Deployment Planner. 
- È possibile valutare fino a 1000 VM in una singola valutazione e fino a 1500 computer in un singolo progetto Azure Migrate. Se è necessario valutarne di più, si può aumentare il numero di progetti o valutazioni. [Altre informazioni](how-to-scale-assessment.md)
- Le VM da valutare devono essere gestite da un server vCenter versione 5.5, 6.0 o 6.5.
- È possibile creare un progetto Azure Migrate solo nell'area Stati Uniti centro-occidentali. Ciò non impedisce, tuttavia, di pianificare una migrazione per una diversa località di Azure di destinazione. La località del progetto di migrazione viene usata solo per l'archiviazione dei metadati individuati nell'ambiente locale.
- Il portale di Azure Migrate è attualmente disponibile solo in lingua inglese. 
- Azure Migrate attualmente supporta solo la replica di tipo [archiviazione con ridondanza locale](../storage/common/storage-introduction.md#replication).

## <a name="what-do-i-need-to-pay-for"></a>Costo del servizio

Azure Migrate è disponibile senza costi aggiuntivi. Durante l'anteprima pubblica, tuttavia, verranno applicati addebiti aggiuntivi per l'uso delle funzionalità di visualizzazione delle dipendenze. Per supportare la [visualizzazione delle dipendenze](concepts-dependency-visualization.md), Azure Migrate crea per impostazione predefinita un'area di lavoro di Log Analytics. Se si usa la visualizzazione delle dipendenze oppure si usa l'area di lavoro all'esterno di Azure Migrate, viene addebitato l'utilizzo dell'area di lavoro. [Vedere altre informazioni](https://azure.microsoft.com/en-us/pricing/details/insight-analytics/) sugli addebiti. Quando il servizio sarà disponibile a livello generale, per l'uso delle funzionalità di visualizzazione delle dipendenze non verrà applicato alcun addebito.


## <a name="whats-in-an-assessment"></a>Elementi inclusi nella valutazione

Le valutazioni di Azure Migrate sono basate sulle impostazioni riepilogate nella tabella.

**Impostazione** | **Dettagli**
--- | ---
**Posizione di destinazione** | Località di Azure verso cui si vuole eseguire la migrazione. Per impostazione predefinita, è la località in cui si crea il progetto Azure Migrate. È possibile modificare questa impostazione.   
**Ridondanza dell'archiviazione** | Tipo di archiviazione che verrà usato dalle VM di Azure dopo la migrazione. L'impostazione predefinita è l'archiviazione con ridondanza locale.
**Piani dei prezzi** | La valutazione considera l'eventuale iscrizione a Software Assurance e la possibilità di usare il [vantaggio Azure Hybrid Use](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Considera anche le offerte di Azure da applicare e consente di indicare sconti specifici della sottoscrizione (%) di cui si può usufruire con l'offerta. 
**Piano tariffario** | È possibile specificare il [piano tariffario (Basic o Standard)](../virtual-machines/windows/sizes-general.md) delle VM di Azure. Questo consente di eseguire la migrazione a una famiglia di VM di Azure idonea, a seconda che ci si trovi in un ambiente di produzione. Per impostazione predefinita viene usato il piano [Standard](../virtual-machines/windows/sizes-general.md).
**Cronologia delle prestazioni** | Per impostazione predefinita, Azure Migrate valuta le prestazioni dei computer locali usando la cronologia di un mese, con un valore percentile del 95%. È possibile modificare questa impostazione.
**Fattore di comfort** | Durante la valutazione, Azure Migrate considera un buffer (fattore di comfort), che viene applicato ai dati sull'utilizzo delle VM (CPU, memoria, disco e rete). Il fattore di comfort tiene conto di aspetti come utilizzo stagionale, breve cronologia delle prestazioni e probabile aumento dell'utilizzo futuro.<br/><br/> Da una VM con 10 core e un utilizzo del 20%, ad esempio, si ottiene normalmente una VM con 2 core. Con un fattore di comfort pari a 2.0x, invece, il risultato è una VM con 4 core. L'impostazione predefinita del fattore di comfort è 1.3x.


## <a name="how-does-azure-migrate-work"></a>Funzionamento di Azure Migrate

1.  Creare un progetto Azure Migrate.
2.  Azure Migrate usa una VM locale denominata appliance dell'agente di raccolta per individuare informazioni sui computer locali. Per creare l'appliance, scaricare il file di installazione in formato Open Virtualization Appliance (con estensione ova) e importarlo come VM nel server vCenter locale.
3.  Connettersi alla VM con credenziali di sola lettura per il server vCenter ed eseguire l'agente di raccolta.
4.  L'agente di raccolta raccoglie i metadati delle VM usando cmdlet di VMware PowerCLI. L'individuazione è senza agente e non comporta installazioni nelle VM o negli host VMware. I metadati raccolti includono informazioni sulle VM come core, memoria, dischi, dimensioni dei dischi e schede di rete. Vengono anche raccolti dati sulle prestazioni delle VM, tra cui utilizzo di CPU e memoria, operazioni di I/O al secondo e velocità effettiva (in MBps) dei dischi e output di rete (in MBps).
5.  I metadati vengono inseriti nel progetto Azure Migrate e possono essere visualizzati nel portale di Azure.
6.  Ai fini della valutazione, raccogliere le VM in gruppi. Ad esempio, si possono raggruppare le VM che eseguono la stessa app. È possibile raggruppare le VM con l'assegnazione di tag in vCenter o nel portale di vCenter. Usare la visualizzazione per verificare le dipendenze per un computer specifico o per tutti i computer di un gruppo.
7.  Creare una valutazione per un gruppo.
8.  Dopo che è stata completata, la valutazione può essere visualizzata nel portale o scaricata in formato Excel.



  ![Architettura della pianificazione in Azure](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>Requisiti relativi alle porte

La tabella offre un riepilogo delle porte necessarie per le comunicazioni di Azure Migrate.

|Componente          |Comunicazione con     |Porta necessaria  |Motivo   |
|-------------------|------------------------|---------------|---------|
|Agente di raccolta          |Servizio Azure Migrate   |TCP 443        |L'agente di raccolta si connette al servizio sulla porta SSL 443.|
|Agente di raccolta          |Server vCenter          |9443 (impostazione predefinita)   | Per impostazione predefinita, l'agente di raccolta si connette al server vCenter sulla porta 9443. Se il server è in ascolto su un'altra porta, deve essere configurata come porta in uscita nella VM dell'agente di raccolta. |
|VM locale     | Area di lavoro di OMS          |[TCP 443](../log-analytics/log-analytics-windows-agents.md#system-requirements-and-required-configuration) |L'agente MMA usa la porta TCP 443 per la connessione a Log Analytics. Questa porta è necessaria solo se si usa la funzionalità di visualizzazione delle dipendenze e si installa l'agente MMA. |


  
## <a name="what-happens-after-assessment"></a>Operazioni successive alla valutazione

Dopo aver valutato i computer locali per la migrazione con il servizio Azure Migrate, è possibile usare alcuni strumenti per eseguire la migrazione.

- **Azure Site Recovery**: è possibile usare Azure Site Recovery per eseguire la migrazione ad Azure come descritto di seguito.
  - Preparare le risorse di Azure, includendo una sottoscrizione di Azure, una rete virtuale di Azure e un account di archiviazione.
  - Preparare i server VMware locali per la migrazione. Verificare i requisiti di supporto di VMware per Site Recovery, preparare i server VMware per l'individuazione e preparare l'installazione del servizio Mobility di Site Recovery nelle VM di cui si vuole eseguire la migrazione. 
  - Configurare la migrazione. Configurare un insieme di credenziali dei servizi di ripristino, le impostazioni di migrazione per l'origine e la destinazione e i criteri di replica e quindi abilitare la replica. È possibile eseguire un'esercitazione sul ripristino di emergenza per verificare che la migrazione di una VM ad Azure funzioni correttamente.
  - Effettuare un failover per eseguire la migrazione dei computer locali ad Azure. 
  - [Vedere altre informazioni](../site-recovery/tutorial-migrate-on-premises-to-azure.md) nell'esercitazione sulla migrazione con Site Recovery.

- **Migrazione del database di Azure**: se i computer locali eseguono un database come SQL Server, MySQL oppure Oracle, è possibile eseguirne la migrazione ad Azure usando il servizio Migrazione del database di Azure. [Altre informazioni](https://azure.microsoft.com/campaigns/database-migration/)



## <a name="next-steps"></a>Passaggi successivi 
[Completare un'esercitazione](tutorial-assessment-vmware.md) per creare una valutazione per una VM VMware locale.