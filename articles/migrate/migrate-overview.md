---
title: Informazioni su Azure Migrate | Microsoft Docs
description: Panoramica del servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 08/08/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 8371a160d129586f63b2f14946ed34a8d0637f6c
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714241"
---
# <a name="about-azure-migrate"></a>Informazioni su Azure Migrate

Il servizio Azure Migrate valuta i carichi di lavoro locali per la migrazione ad Azure. Il servizio valuta l'idoneità per la migrazione dei computer locali, esegue il dimensionamento in base alle prestazioni e offre stime dei costi per l'esecuzione dei computer locali in Azure. È il servizio ideale se si stanno prendendo in considerazione migrazioni in modalità lift-and-shift o si affrontano le prime fasi di valutazione della migrazione. Al termine della valutazione, è possibile usare servizi come [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) e [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview), per eseguire la migrazione dei computer in Azure.

## <a name="why-use-azure-migrate"></a>Vantaggi di Azure Migrate

Azure Migrate consente di eseguire le operazioni seguenti.

- **Valutare l'idoneità per Azure**: valutare se i computer locali sono idonei per l'esecuzione in Azure.
- **Ottenere informazioni sulle dimensioni consigliate**: ottenere informazioni sulle dimensioni consigliate per le macchine virtuali di Azure in base alla cronologia delle prestazioni delle macchine virtuali locali.
- **Stimare i costi mensili**: ottenere una stima dei costi per l'esecuzione dei computer locali in Azure.  
- **Eseguire la migrazione in tutta tranquillità**: visualizzare le dipendenze dei computer locali per creare gruppi di computer di cui eseguire la valutazione e la migrazione insieme.

## <a name="current-limitations"></a>Limitazioni correnti

- Attualmente è possibile valutare solo macchine virtuali (VM) VMware locali per la migrazione a VM di Azure. Le VM VMware devono essere gestite dal server vCenter (versione 5.5, 6.0 o 6.5).
- Se si vogliono valutare VM Hyper-V e server fisici, usare [Azure Site Recovery Deployment Planner](http://aka.ms/asr-dp-hyperv-doc) per Hyper-V e gli [strumenti dei partner](https://azure.microsoft.com/migration/partners/) per i computer fisici.
- È possibile individuare fino a 1500 VM in una singola individuazione e fino a 1500 VM in un singolo progetto. È inoltre possibile valutare fino a 1500 VM in una singola valutazione.
- Se si vuole eseguire l'individuazione di un ambiente di maggiori dimensioni, è possibile suddividere l'individuazione e creare più progetti. [Altre informazioni](how-to-scale-assessment.md). Azure Migrate supporta fino a 20 progetti per ogni sottoscrizione.
- È possibile creare un progetto Azure Migrate solo nell'area Stati Uniti centro-occidentali o Stati Uniti orientali. Ciò non impedisce di pianificare la migrazione a qualsiasi località di Azure di destinazione. La località del progetto di migrazione viene usata solo per l'archiviazione dei metadati individuati nell'ambiente locale.
- Azure Migrate supporta solo dischi gestiti per la valutazione della migrazione.


## <a name="what-do-i-need-to-pay-for"></a>Costo del servizio

Vedere [altre informazioni](https://azure.microsoft.com/pricing/details/azure-migrate/) sui prezzi di Azure Migrate.


## <a name="whats-in-an-assessment"></a>Elementi inclusi nella valutazione

Le impostazioni di valutazione possono essere personalizzate in base alle esigenze specifiche. La tabella seguente offre un riepilogo delle proprietà della valutazione.

**Proprietà** | **Dettagli**
--- | ---
**Posizione di destinazione** | Area di Azure in cui si vuole eseguire la migrazione.<br/><br/>Azure Migrate supporta attualmente 30 aree. [Verificare le aree](https://azure.microsoft.com/global-infrastructure/services/). Per impostazione predefinita, l'area di destinazione è impostata su Stati Uniti occidentali 2.
**Tipo di archiviazione** | Tipo di dischi che si vuole allocare in Azure. È applicabile quando il criterio di dimensionamento è **Come in locale**. Specificare il tipo di disco di destinazione come disco gestito Premium (impostazione predefinita) o Standard. Per il dimensionamento in base alle prestazioni, le indicazioni relative alle dimensioni dei dischi vengono create automaticamente in base ai dati sulle prestazioni delle VM. 
**Criterio di dimensionamento** | Il dimensionamento può essere basato sulla **cronologia delle prestazioni** delle VM locali oppure essere definito **come in locale** (impostazione predefinita) senza considerare la cronologia delle prestazioni. 
**Offerta Azure** | [Offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) sottoscritta. Azure Migrate stima il costo di conseguenza.
**Vantaggio Azure Hybrid** | Eventuale iscrizione a Software Assurance e idoneità per il [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/) con prezzi scontati.
**Istanze riservate** |  Eventuale disponibilità di [istanze riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/) in Azure. Azure Migrate stima il costo di conseguenza.
**Tempo di attività macchina virtuale** | Durata di esecuzione delle VM. Le stime dei costi verranno definite di conseguenza.
**Piano tariffario** | [Piano tariffario (Basic o Standard)](../virtual-machines/windows/sizes-general.md) per le VM di Azure di destinazione. Se si pianifica la migrazione di un ambiente di produzione, ad esempio, si può prendere in considerazione il livello Standard, che offre VM con bassa latenza ma ha un costo superiore. D'altra parte, in un ambiente di testing si potrebbe usare il livello Basic, caratterizzato da latenza più elevata e costi inferiori. Per impostazione predefinita viene usato il piano [Standard](../virtual-machines/windows/sizes-general.md).
**Cronologia delle prestazioni** | Per impostazione predefinita, Azure Migrate valuta le prestazioni dei computer locali usando la cronologia delle prestazioni per l'ultimo giorno, con un valore percentile del 95%. 
**Serie VM** | Serie di VM usata per le stime delle dimensioni. Se si ha un ambiente di produzione di cui non si intende eseguire la migrazione a VM serie A in Azure, ad esempio, si può escludere la serie A dall'elenco o dalle serie. Il dimensionamento sarà basato solo sulla serie selezionata.   
**Fattore di comfort** | Durante la valutazione, Azure Migrate considera un buffer (fattore di comfort), che viene applicato ai dati sull'utilizzo delle VM (CPU, memoria, disco e rete). Il fattore di comfort tiene conto di aspetti come utilizzo stagionale, breve cronologia delle prestazioni e probabile aumento dell'utilizzo futuro.<br/><br/> Da una VM con 10 core e un utilizzo del 20%, ad esempio, si ottiene normalmente una VM con 2 core. Con un fattore di comfort pari a 2.0x, invece, il risultato è una VM con 4 core. L'impostazione predefinita del fattore di comfort è 1.3x.


## <a name="how-does-azure-migrate-work"></a>Funzionamento di Azure Migrate

1.  Creare un progetto Azure Migrate.
2.  Azure Migrate usa una VM locale denominata appliance dell'agente di raccolta per individuare informazioni sui computer locali. Per creare l'appliance, scaricare un file di installazione in formato Open Virtualization Appliance (con estensione ova) e importarlo come macchina virtuale nel server vCenter locale.
3. Connettersi alla VM dal server vCenter e specificare una nuova password durante la connessione.
4. Eseguire l'agente di raccolta nella VM per avviare l'individuazione.
5. L'agente di raccolta raccoglie i metadati delle VM usando cmdlet di VMware PowerCLI. L'individuazione è senza agente e non comporta installazioni nelle VM o negli host VMware. I metadati raccolti includono informazioni sulle VM come core, memoria, dischi, dimensioni dei dischi e schede di rete. Vengono anche raccolti dati sulle prestazioni delle VM, tra cui utilizzo di CPU e memoria, operazioni di I/O al secondo e velocità effettiva (in MBps) dei dischi e output di rete (in MBps).
5.  I metadati vengono inseriti nel progetto Azure Migrate e possono essere visualizzati nel portale di Azure.
6.  Ai fini della valutazione, riunire le macchine virtuali individuate in gruppi. È ad esempio possibile raggruppare le macchine virtuali che eseguono la stessa applicazione. Per un raggruppamento più preciso, è anche possibile usare la visualizzazione delle dipendenze per verificare le dipendenze per un computer specifico o per tutti i computer di un gruppo e ottimizzare il gruppo.
7.  Dopo aver definito un gruppo, creare una valutazione per tale gruppo.
8.  Dopo che è stata completata, la valutazione può essere visualizzata nel portale o scaricata in formato Excel.

  ![Architettura di Azure Migrate](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>Requisiti relativi alle porte

La tabella offre un riepilogo delle porte necessarie per le comunicazioni di Azure Migrate.

Componente | Comunica con |  Dettagli
--- | --- |--- 
Agente di raccolta  | Servizio Azure Migrate | L'agente di raccolta si connette al servizio sulla porta SSL 443.
Agente di raccolta | Server vCenter | Per impostazione predefinita, l'agente di raccolta si connette al server vCenter sulla porta 443. Se il server è in ascolto su una porta diversa, configurarla come porta in uscita nella VM dell'agente di raccolta. 
VM locale | Area di lavoro di Log Analytics | [TCP 443] | [Microsoft Monitoring Agent (MMA)](../log-analytics/log-analytics-windows-agent.md) usa la porta TCP 443 per la connessione a Log Analytics. Questa porta è necessaria solo se si usa la visualizzazione delle dipendenze, per cui è necessario l'agente MMA. 


## <a name="what-happens-after-assessment"></a>Operazioni successive alla valutazione

Dopo aver valutato i computer locali, è possibile usare alcuni strumenti per eseguire la migrazione.

- **Azure Site Recovery**: è possibile usare Azure Site Recovery per eseguire la migrazione ad Azure. A tale scopo, [preparare i componenti di Azure](../site-recovery/tutorial-prepare-azure.md) necessari, che includono un account di archiviazione e una rete virtuale. In locale, [preparare l'ambiente VMware](../site-recovery/vmware-azure-tutorial-prepare-on-premises.md). Dopo aver preparato tutto, configurare e abilitare la replica in Azure ed eseguire la migrazione delle VM. [Altre informazioni](../site-recovery/vmware-azure-tutorial.md).
- **Migrazione del database di Azure**: se i computer locali eseguono un database come SQL Server, MySQL oppure Oracle, è possibile eseguirne la migrazione ad Azure usando [Servizio Migrazione del database di Azure](../dms/dms-overview.md). 


## <a name="next-steps"></a>Passaggi successivi

- [Completare l'esercitazione](tutorial-assessment-vmware.md) per creare una valutazione per una VM VMware locale.
- [Vedere le domande frequenti](resources-faq.md) su Azure Migrate.
