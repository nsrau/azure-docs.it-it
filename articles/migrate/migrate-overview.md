---
title: Informazioni su Azure Migrate | Microsoft Docs
description: Panoramica del servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 04/04/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: bb44600a799cfcf447d408fafd0bbde81e7a1ef8
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807352"
---
# <a name="about-azure-migrate"></a>Informazioni su Azure Migrate

Il servizio Azure Migrate valuta i carichi di lavoro locali per la migrazione ad Azure. Il servizio valuta l'idoneità per la migrazione dei computer locali, esegue il dimensionamento in base alle prestazioni e offre stime dei costi per l'esecuzione dei computer locali in Azure. È il servizio ideale se si stanno prendendo in considerazione migrazioni in modalità lift-and-shift o si affrontano le prime fasi di valutazione della migrazione. Al termine della valutazione, è possibile usare servizi come [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) e [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview), per eseguire la migrazione dei computer in Azure.

## <a name="why-use-azure-migrate"></a>Vantaggi di Azure Migrate

Azure Migrate consente di eseguire le operazioni seguenti.

- **Valutare l'idoneità per Azure**: valutare se i computer locali sono idonei per l'esecuzione in Azure.
- **Informazioni sulle dimensioni consigliate**: ottenere informazioni sulle dimensioni consigliate per le macchine virtuali di Azure in base alla cronologia delle prestazioni delle macchine virtuali locali.
- **Stimare i costi mensili**: ottenere una stima dei costi per l'esecuzione dei computer locali in Azure.  
- **Eseguire la migrazione in tutta tranquillità**: visualizzare le dipendenze dei computer locali per creare gruppi di computer di cui eseguire la valutazione e la migrazione insieme.

## <a name="current-limitations"></a>Limitazioni correnti

- È possibile valutare solo macchine virtuali (VM) VMware locali per la migrazione a Macchine virtuali di Azure. Le VM VMware devono essere gestite dal server vCenter (versione 5.5, 6.0, 6.5 o 6.7).
- Il supporto di produzione per Hyper-V è attualmente in fase di anteprima; se si è interessati a provarlo, iscriversi [qui.](https://aka.ms/migratefuture)
- Per la valutazione dei server fisici, è possibile usare gli [strumenti dei partner](https://azure.microsoft.com/migration/partners/).
- È possibile trovare fino a 1500 VM in una singola individuazione e in un singolo progetto. È disponibile una versione di anteprima che consente di individuare fino a 10.000 macchine virtuali VMware in un singolo progetto con una singola appliance. Se si è interessati a provarla, iscriversi [qui.](https://aka.ms/migratefuture)
- Se si vuole eseguire l'individuazione di un ambiente di maggiori dimensioni, è possibile suddividere l'individuazione e creare più progetti. [Altre informazioni](how-to-scale-assessment.md) Azure Migrate supporta fino a 20 progetti per ogni sottoscrizione.
- Azure Migrate supporta solo dischi gestiti per la valutazione della migrazione.
-  È possibile creare un progetto Azure Migrate nelle seguenti aree geografiche. Tuttavia, ciò non limita la possibilità di creare le valutazioni per gli altri percorsi di destinazione di Azure.

    **Area geografica** | **Posizione di archiviazione**
    --- | ---
    Azure Government | US Gov Virginia
    Asia | Asia sud-orientale o Asia orientale
    Europa | Europa settentrionale o Europa occidentale
    Stati Uniti | Stati Uniti orientali o Stati Uniti centro-occidentali

    L'area geografica associata al progetto di migrazione viene usata per l'archiviazione dei metadati individuati nell'ambiente locale. I metadati vengono archiviati in una delle aree in base all'area geografica specificata per il progetto di migrazione. Se si usa la visualizzazione delle dipendenze creando una nuova area di lavoro Log Analytics, questa viene creata nella stessa area del progetto.
- La funzionalità di visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.


## <a name="what-do-i-need-to-pay-for"></a>Costo del servizio

Vedere [altre informazioni](https://azure.microsoft.com/pricing/details/azure-migrate/) sui prezzi di Azure Migrate.


## <a name="whats-in-an-assessment"></a>Elementi inclusi nella valutazione

Le impostazioni di valutazione possono essere personalizzate in base alle esigenze specifiche. La tabella seguente offre un riepilogo delle proprietà della valutazione.

**Proprietà** | **Dettagli**
--- | ---
**Posizione di destinazione** | Area di Azure in cui si vuole eseguire la migrazione.<br/><br/>Azure Migrate supporta attualmente 33 aree come destinazioni della migrazione. [Verificare le aree](https://azure.microsoft.com/global-infrastructure/services/). Per impostazione predefinita, l'area di destinazione è impostata su Stati Uniti orientali.
**Tipo di archiviazione** | Il tipo di dischi gestiti da allocare per tutte le macchine virtuali che fanno parte della valutazione. Se come criterio di dimensionamento è impostato il *dimensionamento come in locale*, è possibile specificare il tipo di disco di destinazione come Premium (predefinito), SSD Standard o HDD Standard. Per il *dimensionamento basato sulle prestazioni*, insieme alle opzioni precedenti è possibile selezionare anche Automatico per assicurarsi che vengano selezionate automaticamente le dimensioni consigliate dei dischi in base ai dati delle prestazioni delle macchine virtuali. Se ad esempio si vuole ottenere un [contratto di servizio per singole istanze di macchina virtuale con tempo di attività pari al 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), può essere opportuno specificare il tipo di archiviazione Managed Disks Premium che garantisce che tutti i dischi nella valutazione vengano raccomandati come Managed Disks Premium. Si noti che Azure Migrate supporta solo dischi gestiti per la valutazione della migrazione.
**Istanze riservate** |  Eventuale disponibilità di [istanze riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/) in Azure. Azure Migrate stima il costo di conseguenza.
**Criterio di dimensionamento** | Il dimensionamento può essere basato sulla **cronologia delle prestazioni** delle macchine virtuali locali (impostazione predefinita) oppure può essere configurato **come in locale**, senza considerare la cronologia delle prestazioni.
**Cronologia delle prestazioni** | Per impostazione predefinita, Azure Migrate valuta le prestazioni dei computer locali usando la cronologia delle prestazioni per l'ultimo giorno, con un valore percentile del 95%.
**Fattore di comfort** | Durante la valutazione, Azure Migrate considera un buffer (fattore di comfort), che viene applicato ai dati sull'utilizzo delle VM (CPU, memoria, disco e rete). Il fattore di comfort tiene conto di aspetti come utilizzo stagionale, breve cronologia delle prestazioni e probabile aumento dell'utilizzo futuro.<br/><br/> Da una VM con 10 core e un utilizzo del 20%, ad esempio, si ottiene normalmente una VM con 2 core. Con un fattore di comfort pari a 2.0x, invece, il risultato è una VM con 4 core. L'impostazione predefinita del fattore di comfort è 1.3x.
**Serie macchina virtuale** | Serie di VM usata per le stime delle dimensioni. Se si ha un ambiente di produzione di cui non si intende eseguire la migrazione a VM serie A in Azure, ad esempio, si può escludere la serie A dall'elenco o dalle serie. Il dimensionamento sarà basato solo sulla serie selezionata.   
**Valuta** | Valuta di fatturazione. La valuta predefinita è il dollaro statunitense.
**Sconto (%)** | Qualsiasi sconto specifico della sottoscrizione ricevuto oltre all'offerta Azure. L'impostazione predefinita è 0%.
**Tempo di attività macchina virtuale** | Se non si prevede di eseguire ininterrottamente le macchine virtuali in Azure, è possibile specificare la durata (numero di giorni al mese e numero di ore al giorno) dell'esecuzione. Il costo verrà stimato di conseguenza. Il valore predefinito è di 31 giorni al mese e di 24 ore al giorno.
**Offerta Azure** | [Offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) sottoscritta. Azure Migrate stima il costo di conseguenza.
**Vantaggio Azure Hybrid** | Eventuale iscrizione a Software Assurance e idoneità per il [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/) con prezzi scontati.

## <a name="how-does-azure-migrate-work"></a>Funzionamento di Azure Migrate

1. Creare un progetto Azure Migrate.
2. Azure Migrate usa una VM locale denominata appliance dell'agente di raccolta per individuare informazioni sui computer locali. Per creare l'appliance, scaricare un file di installazione in formato Open Virtualization Appliance (con estensione ova) e importarlo come macchina virtuale nel server vCenter locale.
3. Connettersi alla VM dal server vCenter e specificare una nuova password durante la connessione.
4. Eseguire l'agente di raccolta nella VM per avviare l'individuazione.
5. L'agente di raccolta raccoglie i metadati delle VM usando cmdlet di VMware PowerCLI. L'individuazione è senza agente e non comporta installazioni nelle VM o negli host VMware. I metadati raccolti includono informazioni sulle VM come core, memoria, dischi, dimensioni dei dischi e schede di rete. Vengono anche raccolti dati sulle prestazioni delle VM, tra cui utilizzo di CPU e memoria, operazioni di I/O al secondo e velocità effettiva (in MBps) dei dischi e output di rete (in MBps).
5. I metadati vengono inseriti nel progetto Azure Migrate e possono essere visualizzati nel portale di Azure.
6. Ai fini della valutazione, riunire le macchine virtuali individuate in gruppi. È ad esempio possibile raggruppare le macchine virtuali che eseguono la stessa applicazione. Per un raggruppamento più preciso, è anche possibile usare la visualizzazione delle dipendenze per verificare le dipendenze per un computer specifico o per tutti i computer di un gruppo e ottimizzare il gruppo.
7. Dopo aver definito un gruppo, creare una valutazione per tale gruppo.
8. Dopo che è stata completata, la valutazione può essere visualizzata nel portale o scaricata in formato Excel.

   ![Architettura di Azure Migrate](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>Requisiti relativi alle porte

La tabella offre un riepilogo delle porte necessarie per le comunicazioni di Azure Migrate.

| Componente | Comunica con |  Dettagli |
| --- | --- |--- |
|Agente di raccolta  | Servizio Azure Migrate | L'agente di raccolta si connette al servizio sulla porta SSL 443.|
|Agente di raccolta | Server vCenter | Per impostazione predefinita, l'agente di raccolta si connette al server vCenter sulla porta 443. Se il server è in ascolto su una porta diversa, configurarla come porta in uscita nella VM dell'agente di raccolta.|
|VM locale | area di lavoro Log Analytics | [Microsoft Monitoring Agent (MMA)](../log-analytics/log-analytics-windows-agent.md) usa la porta TCP 443 per la connessione ai log di Monitoraggio di Azure. Questa porta è necessaria solo se si usa la visualizzazione delle dipendenze, per cui è necessario l'agente MMA.|


## <a name="what-happens-after-assessment"></a>Operazioni successive alla valutazione

Dopo aver valutato i computer locali, è possibile usare alcuni strumenti per eseguire la migrazione.

- **Azure Site Recovery**: è possibile usare Azure Site Recovery per eseguire la migrazione ad Azure. A tale scopo, [preparare i componenti di Azure](../site-recovery/tutorial-prepare-azure.md) necessari, che includono un account di archiviazione e una rete virtuale. In locale, [preparare l'ambiente VMware](../site-recovery/vmware-azure-tutorial-prepare-on-premises.md). Dopo aver preparato tutto, configurare e abilitare la replica in Azure ed eseguire la migrazione delle VM. [Altre informazioni](../site-recovery/vmware-azure-tutorial.md)
- **Migrazione del database di Azure**: se i computer locali eseguono un database come SQL Server, MySQL oppure Oracle, è possibile eseguirne la migrazione ad Azure usando [Servizio Migrazione del database di Azure](../dms/dms-overview.md).

## <a name="want-to-learn-more-from-community-experts"></a>Per saperne di più dagli esperti della community
Visitare il [forum su Azure Migrate su MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureMigrate&filter=alltypes&sort=lastpostdesc) o [Stack Overflow](https://stackoverflow.com/search?q=azure+migrate)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.  
In caso di domande o per assistenza, [creare una richiesta di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Se per la richiesta di supporto sono necessarie informazioni tecniche dettagliate, visitare la pagina dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/)     


## <a name="next-steps"></a>Passaggi successivi

- [Completare l'esercitazione](tutorial-assessment-vmware.md) per creare una valutazione per una VM VMware locale.
- [Vedere le domande frequenti](resources-faq.md) su Azure Migrate.