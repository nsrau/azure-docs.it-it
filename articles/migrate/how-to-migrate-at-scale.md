---
title: Automatizzare la migrazione di un numero elevato di macchine virtuali in Azure | Microsoft Docs
description: Viene descritto come usare gli script per eseguire la migrazione di un numero elevato di macchine virtuali con Azure Site Recovery
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 02/07/2019
ms.author: snehaa
ms.openlocfilehash: c0fc4fa0bdd58b8ecdf4f26051d60324118c4b21
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55896589"
---
# <a name="scale-migration-of-vms-using-azure-site-recovery"></a>Migrazione su vasta scala di macchine virtuali con Azure Site Recovery

Questo articolo aiuta a comprendere il processo di uso degli script per eseguire la migrazione di un numero elevato di macchine virtuali con Azure Site Recovery. Questi script sono disponibili per il download nel repository di [esempi di Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples) in GitHub. Gli script possono essere usati per la migrazione di server fisici e macchine virtuali GCP, VMware e AWS in Azure. È anche possibile usare questi script per eseguire la migrazione di macchine virtuali Hyper-V se si esegue la migrazione delle macchine virtuali come server fisici. Gli script sfruttano i cmdlet di PowerShell per Azure Site Recovery documentati [qui](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell).

## <a name="current-limitations"></a>Limitazioni correnti:
- Gli script attualmente supportano la migrazione solo ai dischi non gestiti
- È supportata la migrazione solo ai dischi Standard
- È possibile specificare l'indirizzo IP statico solo per la scheda di interfaccia di rete primaria della macchina virtuale di destinazione
- Gli script non accettano gli input correlati al Vantaggio Azure Hybrid ed è necessario aggiornare manualmente le proprietà della macchina virtuale replicata nel portale

## <a name="how-does-it-work"></a>Come funziona?

### <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, è necessario seguire questi passaggi:
- Assicurarsi di creare l'Insieme di credenziali di Site Recovery nella sottoscrizione di Azure
- Assicurarsi che il server di configurazione e il server di elaborazione siano installati nell'ambiente di origine e che l'insieme di credenziali sia in grado di individuare l'ambiente
- Assicurarsi di creare criteri di replica e associarli al server di configurazione
- Assicurarsi di aggiungere l'account amministratore della macchina virtuale nel server di configurazione (che si userà per la replica delle macchine virtuali locali)
- Assicurarsi di creare gli elementi di destinazione in Azure
    - Gruppo di risorse di destinazione
    - Account di archiviazione di destinazione (e relativo gruppo di risorse)
    - Rete virtuale di destinazione per il failover (e relativo gruppo di risorse)
    - Subnet di destinazione
    - Rete virtuale di destinazione per il failover di test (e relativo gruppo di risorse)
    - Set di disponibilità (se necessario)
    - Gruppo di sicurezza di rete di destinazione e relativo gruppo di risorse
- Assicurarsi di aver definito le proprietà della macchina virtuale di destinazione
    - Nome della macchina virtuale di destinazione
    - Dimensioni della macchina virtuale di destinazione in Azure (che possono essere stabilite tramite una valutazione di Azure Migrate)
    - Indirizzo IP privato della scheda di interfaccia di rete primaria nella macchina virtuale
- Scaricare gli script dal repository di [esempi di Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples) in GitHub

### <a name="csv-input-file"></a>File di input CSV
Una volta completati tutti i prerequisiti, è necessario creare un file CSV contenente i dati per ogni macchina di origine di cui si vuole eseguire la migrazione. Il file CSV di input deve avere una riga di intestazione con i dettagli di input e una riga con i dettagli per ogni computer di cui si deve eseguire la migrazione. Tutti gli script sono progettati per funzionare nello stesso file CSV. Un modello CSV di esempio è disponibile nella cartella degli script come riferimento.

### <a name="script-execution"></a>Esecuzione degli script
Una volta pronto il file CSV, è possibile seguire la procedura illustrata di seguito per eseguire la migrazione delle macchine virtuali locali:

**N.ro passaggio** | **Nome script** | **Descrizione**
--- | --- | ---
1 | asr_startmigration.ps1 | Abilitare la replica per tutte le macchine virtuali elencate nel file CSV. Lo script crea un output CSV con i dettagli del processo per ogni macchina virtuale.
2 | asr_replicationstatus.ps1 | Controllare lo stato della replica. Lo script crea un file CSV con lo stato per ogni macchina virtuale.
3 | asr_updateproperties.ps1 | Una volta replicate/protette le macchine virtuali, usare questo script per aggiornare le proprietà di destinazione della macchina virtuale (proprietà di calcolo e rete).
4 | asr_propertiescheck.ps1 | Verificare se le proprietà vengono aggiornate in modo appropriato.
5 | asr_testmigration.ps1 |  Avviare il failover di test delle macchine virtuali elencate nel file CSV. Lo script crea un output CSV con i dettagli del processo per ogni macchina virtuale.
6 | asr_cleanuptestmigration.ps1 | Una volta convalidate manualmente le macchine virtuali che sono state sottoposte al failover di test, è possibile usare questo script per eseguire la pulizia delle macchine virtuali usate per il failover di test.
7 | asr_migration.ps1 | Eseguire un failover non pianificato per le macchine virtuali elencate nel file CSV. Lo script crea un output CSV con i dettagli del processo per ogni macchina virtuale. Lo script non arresta le macchine virtuali locali prima di attivare il failover, pertanto per la coerenza dell'applicazione è consigliabile arrestare manualmente le macchine virtuali prima di eseguire lo script.
8 | asr_completemigration.ps1 | Eseguire l'operazione di commit nelle macchine virtuali ed eliminare le entità di Azure Site Recovery.
9 | asr_postmigration.ps1 | Se si prevede di assegnare gruppi di sicurezza di rete alle schede di interfaccia di rete dopo il failover, è possibile usare questo script a tale scopo. Lo script assegna un gruppo di sicurezza di rete a ogni scheda di interfaccia di rete nella macchina virtuale di destinazione.

## <a name="next-steps"></a>Passaggi successivi

[Leggere altre informazioni](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) sulla migrazione di server in Azure con Azure Site Recovery
