---
title: Aggiornare un insieme di credenziali di Site Recovery a un insieme di credenziali di Servizi di ripristino
description: Informazioni su come aggiornare un insieme di credenziali di Site Recovery a un insieme di credenziali di Servizi di ripristino
ddocumentationcenter: 
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
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 523cab85b195d85007bd85c45dbe3645f7a00ab1
ms.contentlocale: it-it
ms.lasthandoff: 08/10/2017

---
# <a name="upgrade-site-recovery-vaults-to-azure-resource-manager-based-recovery-services-vaults"></a>Aggiornare insiemi di credenziali di Site Recovery a insiemi di credenziali di Recovery Services basati su Azure Resource Manager

Questo articolo descrive come aggiornare "insiemi di credenziali di Site Recovery" a "insiemi di credenziali di Recovery Services" basati su Azure Resource Manager senza alcun impatto sulla replica in corso. Altre informazioni sulle funzionalità e i vantaggi di Azure Resource Manager sono disponibili [qui](../azure-resource-manager/resource-group-overview.md).

## <a name="introduction"></a>Introduzione
L'insieme di credenziali di Recovery Services è una risorsa di Azure Resource Manager che consente di gestire le esigenze di backup e ripristino di emergenza in modo nativo nel cloud. È un insieme di credenziali unificato che può essere usato nel nuovo portale di Azure e sostituisce i classici insiemi di credenziali di backup e Site Recovery.

Gli insiemi di credenziali di Recovery Services danno luogo a una nuova gamma di funzionalità, elencate di seguito.

-   Supporto di Azure Resource Manager: è possibile proteggere ed eseguire il failover delle macchine virtuali e delle macchine fisiche nello stack di Azure Resource Manager.

-   Esclusione del disco: se si hanno file temporanei o dati ad alta varianza per i quali non si vuole usare la larghezza di banda disponibile, è possibile escludere volumi dalla replica. Questa funzionalità è attualmente abilitata negli scenari "Da VMware ad Azure" e 'Da Hyper-V ad Azure" e verrà presto estesa anche ad altri scenari.

- Supporto per l'archiviazione Premium e con ridondanza locale: è possibile ora proteggere i server in account di archiviazione Premium che consentono agli utenti di proteggere le applicazioni con valori di IOPS più elevati. Questa funzionalità è attualmente abilitata nello scenario "Da VMware ad Azure".

-   Attività iniziali semplificate: le attività iniziali sono state personalizzate per garantire una maggiore semplicità di configurazione del processo di ripristino di emergenza.

- Possibilità di gestire Backup e Site Recovery dallo stesso insieme di credenziali: è possibile ora proteggere i server per il ripristino di emergenza o il backup dallo stesso insieme di credenziali, riducendo in modo significativo il sovraccarico di gestione.

Per altre informazioni sui miglioramenti in termini di funzionalità ed esperienza d'uso, consultare questo [blog](https://azure.microsoft.com/blog/azure-site-recovery-now-available-in-a-new-experience-with-support-for-arm-and-csp/).

## <a name="salient-features"></a>Funzionalità principali

- **Nessun impatto sulla replica in corso**: le repliche in corso continuano senza interruzioni durante e in seguito all'aggiornamento.

- **Nessun costo aggiuntivo**: disponibilità di un'intera serie di nuove funzionalità senza costi aggiuntivi

- **Nessuna perdita di dati**: trattandosi di un aggiornamento e non di una migrazione, le informazioni esistenti sulla replica (punti di ripristino, impostazioni di replica e così via) rimangono invariate durante e in seguito all'aggiornamento.


## <a name="what-happens-during-the-upgrade"></a>Cosa accade durante l'aggiornamento?

Durante l'aggiornamento non sono consentite operazioni come la registrazione di un nuovo server, l'abilitazione della replica per una macchina virtuale e così via. Qualsiasi operazione che implica la lettura o la scrittura di dati nell'insieme di credenziali, come la replica in corso di elementi protetti nell'insieme di credenziali, continua invece senza interruzioni.

## <a name="changes-to-your-automation-and-tooling-after-vault-upgrade"></a>Modifiche all'automazione e agli strumenti dopo l'aggiornamento dell'insieme di credenziali
Nell'ambito del processo di aggiornamento del tipo di insieme di credenziali dal modello di distribuzione classica al modello di distribuzione di Resource Manager, è necessario aggiornare l'automazione o gli strumenti esistenti per accertarsi che continuino a funzionare anche dopo l'aggiornamento.

## <a name="preparing-your-environment-for-vault-upgrade"></a>Preparazione dell'ambiente per l'aggiornamento dell'insieme di credenziali

1.  Installare/aggiornare PowerShell alla versione 5 o successiva usando questo [collegamento](https://www.microsoft.com/download/details.aspx?id=50395)

2.  Installare il file MSI di Azure PowerShell più recente da [qui](https://github.com/Azure/azure-powershell/releases)

3.  [Scaricare](https://aka.ms/vaultupgradescript) lo script per l'aggiornamento dell'insieme di credenziali

## <a name="prerequisites-for-upgrade"></a>Prerequisiti per l'aggiornamento
Per eseguire l'aggiornamento da un insieme di credenziali di Site Recovery a un insieme di credenziali di Recovery Services basato su Azure Resource Manager, è necessario soddisfare i prerequisiti seguenti.

- Versione minima dell'agente: l'aggiornamento richiede che la versione del provider di Azure Site Recovery installata nel server sia almeno 5.1.1700.0.

- Configurazione supportata: l'insieme di credenziali non deve essere configurato con SAN e gruppi di disponibilità SQL AlwaysOn. Sono supportate tutte le altre configurazioni.

>[!NOTE]
> In seguito all'aggiornamento, il mapping dell'archiviazione può essere gestito solo tramite PowerShell.

- Scenario di distribuzione supportato: l'insieme di credenziali non deve trovarsi nel modello di distribuzione legacy "Da VMware ad Azure".  Prima di procedere, è necessario passare al modello di distribuzione avanzata.

- Nessun processo attivo avviato dall'utente che coinvolga operazioni del piano di gestione: poiché durante l'aggiornamento l'accesso al piano di gestione è limitato, è necessario completare prima tutte le azioni del piano di gestione e quindi attivare l'aggiornamento. Questo non include la replica in corso.

## <a name="frequently-asked-questions"></a>Domande frequenti

- L'aggiornamento influisce sulla replica in corso?

  No. Durante e dopo l'aggiornamento, la replica in corso continua ininterrottamente.

- Che cosa accade alle impostazioni di rete: VPN da sito a sito, impostazioni IP e così via?

  L'aggiornamento non incide sulle impostazioni di rete e tutte le connessioni da Azure a locale rimangono inalterate.
- Che cosa accade agli insiemi di credenziali se non si prevede un aggiornamento a breve?

  Il supporto per l'insieme di credenziali di Site Recovery nella versione precedente del portale di Azure verrà interrotto a partire dal mese di settembre. È quindi consigliabile usare la funzionalità di aggiornamento per passare al nuovo portale.

- Qual è l'impatto di questo piano di migrazione per gli strumenti esistenti?  

  L'aggiornamento degli strumenti al modello di distribuzione di Resource Manager su cui si basano gli insiemi di credenziali di Recovery Services è una delle modifiche più importanti da tenere in considerazione per i piani di aggiornamento.

- Quale durata avrà il tempo di inattività del piano di gestione?

  L'aggiornamento richiede circa 15-30 minuti, fino a un massimo di un'ora.

- È possibile eseguire il ripristino dello stato precedente dopo l'aggiornamento?

  No. Il ripristino dello stato precedente non è supportato dopo il completamento dell'aggiornamento delle risorse.

- È possibile convalidare la sottoscrizione o le risorse per verificare che siano idonee all'aggiornamento?

  Sì. Nell'opzione di aggiornamento supportata dalla piattaforma, il primo passaggio prevede di verificare che le risorse siano idonee all'aggiornamento. In caso di esito negativo dell'operazione di convalida, si riceveranno appositi errori/avvisi.

- Come è possibile segnalare un problema in fase di aggiornamento?

  In caso di errore durante l'aggiornamento, annotare il codice OperationId elencato nel messaggio di errore. Il supporto tecnico Microsoft si impegnerà per risolvere il problema. È possibile contattare il supporto tecnico indicando l'ID della sottoscrizione, il nome dell'insieme di credenziali e l'ID dell'operazione. Il problema verrà risolto appena possibile. Non ripetere l'operazione a meno che non venga esplicitamente richiesto da Microsoft.

## <a name="how-to-run-the-script"></a>Come eseguire lo script?

Al prompt di PowerShell, eseguire il comando seguente:

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionID <subscriptionID>  -VaultName <vaultname> -Location <location> -ResourceType HyperVRecoveryManagerVault -TargetResourceGroupName <rgname>

- SubscriptionID: ID della sottoscrizione associato all'insieme di credenziali che si sta aggiornando
- VaultName: nome dell'insieme di credenziali che si sta aggiornando
- Location: percorso dell'insieme di credenziali che si sta aggiornando
- ResourceType: HyperVRecoveryManagerVault per gli insiemi di credenziali di Site Recovery
- TargetResourceGroupName: gruppo di risorse in cui si vuole inserire l'insieme di credenziali aggiornato. TargetResourceGroupName può essere un gruppo di risorse esistente in Azure Resource Manager o uno nuovo. Nel caso in cui il valore TargetResourceGroupName specificato non esista, viene creato nel corso del processo di aggiornamento nello stesso percorso dell'insieme di credenziali. Per altre informazioni sui gruppi di risorse, fare clic [qui](../azure-resource-manager/resource-group-overview.md#resource-groups):

>[!NOTE]
>I nomi dei gruppi di risorse hanno dei vincoli. In caso di mancata conformità a questi vincoli, è possibile che si verifichino problemi durante l'aggiornamento dell'insieme di credenziali.

Esempio:

    .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionId 1234-54123-354354-56416-8645 -VaultName gen2dr -Location "north europe" -ResourceType hypervrecoverymanagervault -TargetResourceGroupName abc


In alternativa, è possibile eseguire lo script seguente; verrà richiesto di specificare gli input per tutti i parametri obbligatori.

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1
    cmdlet RecoveryServicesVaultUpgrade-1.0.0.ps1 at command pipeline position 1

    Supply values for the following parameters:
    SubscriptionId:
    VaultName:
    Location:
    ResourceType:
    TargetResourceGroupName:

1.  Lo script di PowerShell richiederà di immettere le credenziali personali. È necessario immettere le credenziali due volte: la prima per l'account di Azure Service Manager, la seconda per l'account di Azure Resource Manager.

2.  Dopo aver immesso le credenziali, lo script esegue un controllo dei prerequisiti per determinare che la configurazione dell'infrastruttura soddisfi i prerequisiti indicati nella sezione precedente di questo documento.

3.  Al termine del controllo dei prerequisiti, viene richiesto di confermare la volontà di proseguire l'aggiornamento dell'insieme di credenziali. In caso di conferma, il processo di aggiornamento avvia l'aggiornamento dell'insieme di credenziali. L'aggiornamento viene completato in circa 15-30 minuti.

4.  Al termine del processo di aggiornamento, è possibile accedere all'insieme di credenziali aggiornato nel nuovo portale di Azure.

## <a name="management-experience-post-upgrade"></a>Esperienza di gestione post-aggiornamento

### <a name="how-to-replicate-using-azure-site-recovery-in-the-recovery-services-vault"></a>Come eseguire la replica usando Azure Site Recovery nell'insieme di credenziali di Recovery Services

- È ora possibile proteggere le macchine virtuali di Azure da un'area a un'altra. Per altre informazioni, vedere la documentazione [qui](site-recovery-azure-to-azure.md).

- Per altre informazioni sulla replica di macchine virtuali VMware in Azure, vedere la documentazione [qui](vmware-walkthrough-overview.md).

- Per altre informazioni sulla replica di macchine virtuali Hyper-V (senza VMM) in Azure, vedere la documentazione [qui](hyper-v-site-walkthrough-overview.md).

- Per altre informazioni sulla replica di macchine virtuali Hyper-V (con VMM) in Azure, vedere la documentazione [qui](vmm-to-azure-walkthrough-overview.md).

- Per altre informazioni sulla replica di macchine virtuali Hyper-V (con VMM) in un sito secondario, vedere la documentazione [qui](site-recovery-vmm-to-vmm.md).

- Per altre informazioni sulla replica di macchine virtuali VMware in un sito secondario, vedere la documentazione [qui](site-recovery-vmware-to-vmware.md).

### <a name="how-to-view-your-replicated-items"></a>Come visualizzare gli elementi replicati

La schermata seguente mostra la pagina del dashboard dell'insieme di credenziali di Recovery Services in cui vengono visualizzate le entità principali relative all'insieme di credenziali. Fare clic su **Site Recovery** -> **Elementi replicati** per visualizzare l'elenco delle entità protette nell'insieme di credenziali.


![Elementi replicati](./media/upgrade-site-recovery-vaults/replicateditems.png)

La schermata seguente mostra il flusso di lavoro necessario per visualizzare gli oggetti replicati e come avviare un failover.

![Elementi replicati](./media/upgrade-site-recovery-vaults/failover.png)

### <a name="how-to-view-your-replication-settings"></a>Come visualizzare le impostazioni di replica

Nell'insieme di credenziali di Site Recovery, ogni gruppo di protezione è configurato con impostazioni di replica (Frequenza di copia, Conservazione del punto di ripristino, Frequenza degli snapshot coerenti con l'applicazione e così via). Nell'insieme di credenziali di Recovery Services, queste impostazioni sono configurate come criteri di replica. Il nome dei criteri corrisponde al nome del gruppo protezione o a "primarycloud_Policy".

Per altre informazioni sui criteri di replica, vedere [qui](site-recovery-setup-replication-settings-vmware.md)

