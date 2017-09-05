---
title: Aggiornare un insieme di credenziali di Site Recovery a un insieme di credenziali di Servizi di ripristino di Azure
description: Informazioni su come aggiornare un insieme di credenziali di Site Recovery a un insieme di credenziali di Servizi di ripristino
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/31/2017
ms.author: rajani-janaki-ram
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: fdb33ea0d08353b491f2934fcf885fcb6910b9a2
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---
# <a name="upgrade-a-site-recovery-vault-to-an-azure-resource-manager-based-recovery-services-vault"></a>Aggiornare un insieme di credenziali di Site Recovery a un insieme di credenziali di Servizi di ripristino basato su Azure Resource Manager

Questo articolo descrive come aggiornare gli insiemi di credenziali di Azure Site Recovery agli insiemi di credenziali di Servizi di ripristino basati su Azure Resource Manager senza alcun impatto sulla replica in corso. Per altre informazioni sulle funzionalità e sui vantaggi di Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="introduction"></a>Introduzione
Un insieme di credenziali di Servizi di ripristino è una risorsa di Azure Resource Manager che consente di gestire il backup e il ripristino di emergenza in modo nativo nel cloud. È un insieme di credenziali unificato che è possibile usare nel nuovo portale di Azure e sostituisce i classici insiemi di credenziali di backup e Site Recovery.

Gli insiemi di credenziali di Servizi di ripristino offrono una nuova gamma di funzionalità, elencate di seguito:

* Supporto di Azure Resource Manager: è possibile proteggere ed eseguire il failover delle macchine virtuali e dei computer fisici in uno stack di Azure Resource Manager.

* Esclusione del disco: se si hanno file temporanei o dati ad alta varianza per i quali non si vuole usare tutta la larghezza di banda, è possibile escludere volumi dalla replica. Questa funzionalità è attualmente abilitata negli scenari *da VMware ad Azure* e *da Hyper-V ad Azure* ed è estesa anche ad altri scenari.

* Supporto per l'archiviazione Premium e con ridondanza locale: è possibile ora proteggere i server in account di archiviazione Premium che consentono agli utenti di proteggere le applicazioni con valori di inoput/output al secondo più elevati. Questa funzionalità è attualmente abilitata nello scenario *da VMware ad Azure*.

* Attività iniziali semplificate: le attività iniziali sono state progettate per facilitare la configurazione del ripristino di emergenza.

* Gestione di Backup e Site Recovery dallo stesso insieme di credenziali: è possibile ora proteggere i server per il ripristino di emergenza o il backup dallo stesso insieme di credenziali, riducendo in modo significativo il sovraccarico di gestione.

Per altre informazioni sull'esperienza e sulle funzionalità aggiornate, vedere il blog [Storage, Backup, and Recovery](https://azure.microsoft.com/blog/azure-site-recovery-now-available-in-a-new-experience-with-support-for-arm-and-csp/) (Archiviazione, backup e ripristino).

## <a name="salient-features"></a>Funzionalità principali

* Nessun impatto sulla replica in corso: le repliche in corso continuano senza interruzioni durante e in seguito all'aggiornamento.

* Nessun costo aggiuntivo: è possibile ottenere un intero set di funzionalità aggiornate senza costi aggiuntivi.

* Nessuna perdita di dati: trattandosi di un processo di aggiornamento e non di migrazione, i punti di ripristino e le impostazioni esistenti della replica rimangono invariate durante e in seguito all'aggiornamento.


## <a name="what-happens-during-the-vault-upgrade"></a>Che cosa accade durante l'aggiornamento dell'insieme di credenziali

Durante l'aggiornamento, non è possibile eseguire operazioni come la registrazione di un nuovo server o l'abilitazione della replica per una macchina virtuale. Le operazioni che implicano la lettura o la scrittura di dati nell'insieme di credenziali, come la replica in corso di elementi protetti nell'insieme di credenziali, continuano invece senza interruzioni.

### <a name="changes-to-automation-and-tooling-after-the-upgrade"></a>Modifiche all'automazione e agli strumenti dopo l'aggiornamento
Quando si aggiorna il tipo di insieme di credenziali dal modello di distribuzione classica al modello di distribuzione Resource Manager, aggiornare l'automazione o gli strumenti esistenti per accertarsi che continuino a funzionare anche dopo l'aggiornamento.

### <a name="prepare-your-environment-for-the-upgrade"></a>Preparare l'ambiente per l'aggiornamento

* [Installare PowerShell o aggiornarlo alla versione 5 o successiva](https://www.microsoft.com/download/details.aspx?id=50395)
* [Installare la versione più recente del file MSI di Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Scaricare lo script di aggiornamento dell'insieme di credenziali di Servizi di ripristino](https://aka.ms/vaultupgradescript)

### <a name="prerequisites"></a>Prerequisiti
Per eseguire l'aggiornamento da insiemi di credenziali di Site Recovery a insiemi di credenziali di Servizi di ripristino basati su Azure Resource Manager, è necessario soddisfare i requisiti seguenti:

* Versione minima dell'agente: la versione del provider di Azure Site Recovery installata nel server deve essere 5.1.1700.0 o successiva.

* Configurazione supportata: non è possibile configurare l'insieme di credenziali con la rete di archiviazione (SAN) o i gruppi di disponibilità AlwaysOn di SQL Server. Sono supportate tutte le altre configurazioni.

    >[!NOTE]
    >Dopo l'aggiornamento, è possibile gestire il mapping dell'archiviazione solo tramite PowerShell.

* Scenario di distribuzione supportato: l'insieme di credenziali non deve essere il modello di distribuzione legacy *da VMware ad Azure*. Prima di continuare, passare al modello di distribuzione avanzata.

* Nessun processo attivo avviato dall'utente che coinvolga operazioni del piano di gestione: poiché durante l'aggiornamento l'accesso al piano di gestione è limitato, completare tutte le azioni del piano di gestione prima di attivare l'aggiornamento. Questo processo non include la replica in corso.

## <a name="frequently-asked-questions"></a>Domande frequenti

**L'aggiornamento influisce sulla replica in corso?**

No. Durante e dopo l'aggiornamento, la replica in corso continua ininterrottamente.

**Che cosa accade alle impostazioni di rete, ad esempio alle impostazioni IP e VPN da sito a sito?**

L'aggiornamento non influisce sulle impostazioni di rete. Tutte le connessioni da Azure a locale rimangono inalterate.

**Che cosa accade agli insiemi di credenziali se non si prevede un aggiornamento a breve?**

Il supporto per l'insieme di credenziali di Site Recovery nella versione precedente del portale di Azure sarà deprecato a partire dal mese di settembre 2017. È consigliabile usare la funzionalità di aggiornamento per passare al nuovo portale.

**Qual è l'impatto di questo piano di migrazione per gli strumenti esistenti?**  

L'aggiornamento degli strumenti al modello di distribuzione Resource Manager è una delle modifiche più importanti da tenere in considerazione per i piani di aggiornamento. Gli insiemi di credenziali di Servizi di ripristino si basano sul modello di distribuzione Resource Manager. 

**Quale durata ha il tempo di inattività del piano di gestione?**

L'aggiornamento richiede in genere da 15 a 30 minuti circa, fino a un massimo di un'ora.

**È possibile eseguire il ripristino dello stato precedente dopo l'aggiornamento?**

No. Il ripristino dello stato precedente non è supportato dopo il completamento dell'aggiornamento delle risorse.

**È possibile convalidare la sottoscrizione o le risorse per verificare se possono essere aggiornate?**

Sì. Nell'opzione di aggiornamento supportata dalla piattaforma, il primo passaggio prevede di verificare che le risorse siano idonee per un aggiornamento. In caso di esito negativo dell'operazione di convalida, si riceveranno appositi messaggi di errore o avvisi.

**Come è possibile segnalare un problema in fase di aggiornamento?**

Se si verifica un errore durante l'aggiornamento, prendere nota dell'ID dell'operazione elencato nell'errore. Il supporto tecnico Microsoft si impegnerà per risolvere il problema. È anche possibile contattare il supporto tecnico indicando l'ID della sottoscrizione, il nome dell'insieme di credenziali e l'ID dell'operazione. Il supporto tecnico si impegnerà per cercare di risolvere il problema il prima possibile. Non ripetere l'operazione a meno che non venga esplicitamente richiesto da Microsoft.

## <a name="run-the-script"></a>Esecuzione dello script

In PowerShell eseguire questo comando:

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionID <subscriptionID>  -VaultName <vaultname> -Location <location> -ResourceType HyperVRecoveryManagerVault -TargetResourceGroupName <rgname>

* SubscriptionID: ID della sottoscrizione associato all'insieme di credenziali che si sta aggiornando.

* VaultName: nome dell'insieme di credenziali che si sta aggiornando.

* Location: posizione dell'insieme di credenziali che si sta aggiornando.

* ResourceType: HyperVRecoveryManagerVault per gli insiemi di credenziali di Site Recovery.

* TargetResourceGroupName: gruppo di risorse in cui si vuole inserire l'insieme di credenziali aggiornato. TargetResourceGroupName può essere un gruppo di risorse esistente in Azure Resource Manager o uno nuovo. Se il valore TargetResourceGroupName specificato non esiste, viene creato nel corso del processo di aggiornamento nello stesso percorso dell'insieme di credenziali. Per altre informazioni, vedere la sezione "Gruppi di risorse" di [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).

    >[!NOTE]
    >La denominazione del gruppo di risorse è soggetta a determinati vincoli. Per evitare l'errore di aggiornamento dell'insieme di credenziali, assicurarsi di rispettare la convenzione di denominazione,
    >
    >ad esempio:
    >
    >.\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionId 1234-54123-354354-56416-8645 -VaultName gen2dr -Location "north europe" -ResourceType hypervrecoverymanagervault -TargetResourceGroupName abc

In alternativa, è possibile usare lo script seguente. Immettere i valori per i parametri obbligatori.

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1
    cmdlet RecoveryServicesVaultUpgrade-1.0.0.ps1 at command pipeline position 1

    Supply values for the following parameters:
    SubscriptionId:
    VaultName:
    Location:
    ResourceType:
    TargetResourceGroupName:

1. Lo script di PowerShell richiede di immettere le credenziali. Immetterle due volte, la prima per l'account del modello di distribuzione classica, la seconda per l'account di Azure Resource Manager.

2. Dopo avere immesso le credenziali, lo script esegue un controllo per determinare se la configurazione dell'infrastruttura soddisfa i requisiti descritti in prima.

3. Dopo che i prerequisiti sono stati controllati e confermati, viene chiesto di continuare con l'aggiornamento dell'insieme di credenziali. Il processo di aggiornamento avvia l'aggiornamento dell'insieme di credenziali. L'aggiornamento viene completato in 15-30 minuti.

4. Al termine del processo di aggiornamento, è possibile accedere all'insieme di credenziali aggiornato nel nuovo portale di Azure.

## <a name="post-upgrade-vault-management"></a>Gestione dell'insieme di credenziali successiva all'aggiornamento

### <a name="replicate-by-using-azure-site-recovery-in-the-recovery-services-vault"></a>Eseguire la replica usando Azure Site Recovery nell'insieme di credenziali di Servizi di ripristino

* È ora possibile proteggere le macchine virtuali di Azure da un'area a un'altra. Per altre informazioni, vedere [Eseguire la replica delle VM di Azure tra le aree con Azure Site Recovery](site-recovery-azure-to-azure.md).

* Per altre informazioni sulla replica di macchine virtuali VMware in Azure, vedere [Replicare VM VMware in Azure con Site Recovery](vmware-walkthrough-overview.md).

* Per altre informazioni sulla replica di macchine virtuali Hyper-V (senza VMM) in Azure, vedere [Eseguire la replica di macchine virtuali Hyper-V (senza VMM) in Azure](hyper-v-site-walkthrough-overview.md).

* Per altre informazioni sulla replica di macchine virtuali Hyper-V (con VMM) in Azure, vedere [Eseguire la replica di macchine virtuali Hyper-V nei cloud VMM in Azure tramite Site Recovery nel portale di Azure](vmm-to-azure-walkthrough-overview.md).

* Per altre informazioni sulla replica di macchine virtuali Hyper-V (con VMM) in un sito secondario, vedere [Eseguire la replica di macchine virtuali Hyper-V in cloud VMM in un sito VMM secondario usando il portale di Azure](site-recovery-vmm-to-vmm.md).

* Per altre informazioni sulla replica di macchine virtuali VMware in un sito secondario, vedere [Eseguire la replica di macchine virtuali VMware locali o server fisici in un sito secondario nel portale di Azure classico](site-recovery-vmware-to-vmware.md).

### <a name="view-your-replicated-items"></a>Visualizzare gli elementi replicati

L'immagine seguente mostra la pagina del dashboard dell'insieme di credenziali di Servizi di ripristino in cui vengono visualizzate le entità principali relative all'insieme di credenziali. Per visualizzare un elenco delle entità protette nell'insieme di credenziali, selezionare **Site Recovery** > **Elementi replicati**.


![Elementi replicati](./media/upgrade-site-recovery-vaults/replicateditems.png)

L'immagine seguente illustra il flusso di lavoro per visualizzare gli elementi replicati e il comando **Failover** per avviare un failover.

![Elementi replicati](./media/upgrade-site-recovery-vaults/failover.png)

### <a name="view-your-replication-settings"></a>Visualizzare le impostazioni di replica

Nell'insieme di credenziali di Site Recovery ogni gruppo protezione dati è configurato con frequenza di copia, conservazione del punto di ripristino, frequenza degli snapshot coerenti con l'applicazione e altre impostazioni di replica. Nell'insieme di credenziali di Servizi di ripristino queste impostazioni sono configurate come criteri di replica. Il nome dei criteri corrisponde al nome del gruppo protezione dati o a *primarycloud_Policy*.

Per altre informazioni sui criteri di replica, vedere [Gestire i criteri di replica per VMware in Azure](site-recovery-setup-replication-settings-vmware.md).

