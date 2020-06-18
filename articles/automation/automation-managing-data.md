---
title: Gestione dei dati di Automazione di Azure
description: Questo articolo descrive i concetti relativi alla gestione dei dati in Automazione di Azure, inclusi la conservazione dei dati e il backup.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: de60ef31a39a698f9a797a5836546f9b75b67594
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835207"
---
# <a name="management-of-azure-automation-data"></a>Gestione dei dati di Automazione di Azure

Questo articolo contiene diversi argomenti per la gestione dei dati in un ambiente di Automazione di Azure.

## <a name="data-retention"></a>Conservazione dei dati

Quando si elimina una risorsa in Automazione di Azure, viene conservata per alcuni giorni per finalità di controllo, prima della rimozione permanente. In tale periodo non sarà possibile visualizzare o usare la risorsa. Questi criteri sono applicabili anche alle risorse appartenenti a un account di Automazione eliminato.

La tabella seguente riepiloga i criteri di conservazione per diverse risorse.

| Data | Policy |
|:--- |:--- |
| Account |Un account viene rimosso definitivamente 30 giorni dopo l'eliminazione da parte di un utente. |
| Asset |Un asset viene rimosso definitivamente 30 giorni dopo l'eliminazione da parte di un utente o 30 giorni dopo l'eliminazione di un account che possiede l'asset. |
| Nodi DSC |Un nodo DSC viene rimosso in modo permanente 30 giorni dopo l'annullamento della registrazione dall'account di Automazione tramite il portale di Azure o il cmdlet [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) in Windows PowerShell. I nodi vengono rimossi in modo permanente anche 30 giorni dopo aver eliminato l'account che possiede il nodo. |
| Processi |Un processo viene eliminato e rimosso definitivamente 30 giorni dopo la modifica, ad esempio dopo il completamento, l'arresto o la sospensione del processo. |
| Moduli |Un modulo viene rimosso definitivamente 30 giorni dopo l'eliminazione da parte di un utente o 30 giorni dopo l'eliminazione di un account che possiede il modulo. |
| Configurazioni di nodo/File MOF |Una configurazione di nodo precedente verrà rimossa definitivamente 30 giorni dopo che viene generata una nuova configurazione di nodo. |
| Report sul nodo |Un report sul nodo viene rimosso definitivamente 90 giorni dopo la generazione di un nuovo report per quel nodo. |
| Runbook |Un runbook viene rimosso definitivamente 30 giorni dopo l'eliminazione della risorsa da parte di un utente o 30 giorni dopo l'eliminazione dell'account che possiede la risorsa. |

I criteri di conservazione sono applicabili a tutti gli utenti e non è attualmente possibile personalizzarli. Se tuttavia è necessario conservare i dati per un periodo di tempo più lungo, è possibile [inviare i dati dei processi di Automazione di Azure ai log di Monitoraggio di Azure](automation-manage-send-joblogs-log-analytics.md).

## <a name="data-backup"></a>Backup dei dati

Quando si elimina un account di automazione in Azure, vengono eliminati tutti gli oggetti presenti nell'account. Gli oggetti includono runbook, moduli, configurazioni, impostazioni, processi e asset. Non sarà possibile ripristinare gli oggetti dopo l'eliminazione dell'account. È possibile usare le informazioni seguenti per eseguire il backup dei contenuti dell'account di Automazione prima di eliminarlo.

### <a name="runbooks"></a>Runbook

È possibile esportare i runbook in file di script usando il portale di Azure o il cmdlet [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) in Windows PowerShell. È possibile importare questi file di script in un altro account di Automazione, come illustrato in [Gestire runbook in Automazione di Azure](manage-runbooks.md).

### <a name="integration-modules"></a>Moduli di integrazione

Non è possibile esportare i moduli di integrazione da Automazione di Azure. È necessario renderli disponibili al di fuori dell'account di Automazione.

### <a name="assets"></a>Asset

Non è possibile esportare gli asset di Automazione di Azure: certificati, connessioni, credenziali, pianificazioni e variabili. È invece possibile usare il portale di Azure e i cmdlet di Azure per prendere nota dei dettagli di questi asset. Usare quindi questi dettagli per creare eventuali asset usati dai runbook importati in un altro account di Automazione.

Non è possibile recuperare il valore delle variabili crittografate o i campi password delle credenziali mediante i cmdlet. Se non si conoscono questi valori, è possibile recuperarli in un runbook. Per recuperare i valori delle variabili, vedere [Asset di tipo variabile in Automazione di Azure](shared-resources/variables.md). Per altre informazioni sul recupero dei valori delle credenziali, vedere [Asset credenziali in Automazione di Azure](shared-resources/credentials.md).

 ### <a name="dsc-configurations"></a>Configurazioni DSC

È possibile esportare le configurazioni DSC in file di script tramite il portale di Azure o il cmdlet [Export-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) in Windows PowerShell. È possibile importare e usare queste configurazioni in un altro account di Automazione.

## <a name="geo-replication-in-azure-automation"></a>Replica geografica in Automazione di Azure

La replica geografica è standard negli account di Automazione di Azure. Quando si configura l'account, è possibile scegliere un'area primaria. Il servizio interno di replica geografica di Automazione assegna automaticamente un'area secondaria all'account. Il servizio esegue quindi il backup continuo dei dati dell'account dall'area primaria all'area secondaria. L'elenco completo delle aree primarie e secondarie è disponibile in [Continuità aziendale e ripristino di emergenza (BCDR): aree geografiche abbinate di Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

Il backup creato dal servizio di replica geografica di Automazione è una copia completa degli asset di automazione, delle configurazioni e di elementi simili. Questo backup può essere usato se l'area primaria diventa inattiva e perde i dati. Nell'eventualità improbabile che vengano persi dei dati di un'area primaria, Microsoft tenta di ripristinarli. Se l'azienda non riesce a ripristinare i dati primari, viene usato il failover automatico e viene segnalata la situazione tramite la sottoscrizione di Azure. 

Il servizio di replica geografica di Automazione non è accessibile direttamente ai clienti esterni se si verifica un errore a livello di area. Se si vogliono mantenere la configurazione e i runbook di Automazione durante gli errori a livello di area:

1. Selezionare un'area secondaria da associare all'area geografica dell'account di Automazione primario.

2. Creare un account di Automazione nell'area secondaria.

3. Nell'account primario esportare i runbook come file di script.

4. Importare i runbook nell'account di Automazione dell'area secondaria.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sugli asset sicuri in Automazione di Azure, vedere [Crittografia di asset sicuri in Automazione di Azure](automation-secure-asset-encryption.md).
* Per altre informazioni sulla replica geografica, vedere [Creazione e uso della replica geografica attiva](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication).