---
title: Gestione dei dati di Automazione di Azure
description: Questo articolo contiene più argomenti per la gestione di un ambiente di Automazione di Azure.  Include attualmente conservazione dei dati e backup del ripristino di emergenza di Automazione di Azure in Automazione di Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: f917e9c64a932d75fd0f6b14c9e0f35808467355
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984658"
---
# <a name="managing-azure-automation-data"></a>Gestione dei dati di Automazione di Azure

Questo articolo contiene più argomenti per la gestione dei dati in un ambiente di Automazione di Azure.This article contains multiple topics for managing data in an Azure Automation environment.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="data-retention"></a>Conservazione dei dati

Quando si elimina una risorsa in Automazione di Azure, questa viene conservata per un certo numero di giorni a scopo di controllo prima della rimozione permanente. Non è possibile visualizzare o utilizzare la risorsa durante questo periodo di tempo. Questo criterio si applica anche alle risorse che appartengono a un account di Automazione eliminato.

La tabella seguente riepiloga i criteri di conservazione per diverse risorse.

| Data | Policy |
|:--- |:--- |
| Account |Un account viene rimosso definitivamente 30 giorni dopo l'eliminazione da parte di un utente. |
| Asset |Una risorsa viene rimossa definitivamente 30 giorni dopo che un utente lo elimina o 30 giorni dopo che un utente ha eliminato un account che contiene la risorsa. |
| Nodi DSC |Un nodo DSC viene rimosso definitivamente 30 giorni dopo l'annullamento della registrazione da un account di automazione tramite il portale di Azure o il cmdlet [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) in Windows PowerShell. Un nodo viene inoltre rimosso definitivamente 30 giorni dopo che un utente elimina l'account che contiene il nodo. |
| Processi |Un processo viene eliminato e rimosso definitivamente 30 giorni dopo la modifica, ad esempio, dopo il completamento del processo, viene arrestato o viene sospeso. |
| Moduli |Un modulo viene rimosso definitivamente 30 giorni dopo che un utente lo elimina o 30 giorni dopo che un utente elimina l'account che contiene il modulo. |
| Configurazioni di nodo/File MOF |Una configurazione del nodo precedente viene rimossa definitivamente 30 giorni dopo la generazione di una nuova configurazione del nodo. |
| Report sul nodo |Un report del nodo viene rimosso definitivamente 90 giorni dopo la generazione di un nuovo report per tale nodo. |
| Runbook |Un runbook viene rimosso definitivamente 30 giorni dopo che un utente ha eliminato la risorsa o 30 giorni dopo che un utente ha eliminato l'account che contiene la risorsa. |

I criteri di conservazione si applicano a tutti gli utenti e attualmente non possono essere personalizzati. Tuttavia, se è necessario mantenere i dati per un periodo più lungo, è possibile inoltrare i dati dei processi di Automazione di [Azure ai log](automation-manage-send-joblogs-log-analytics.md)di Monitoraggio di Azure.However, if you need to keep data for a longer period, you can forward Azure Automation job data to Azure Monitor logs .

## <a name="data-backup"></a>Backup dei dati

Quando si elimina un account di automazione in Azure, tutti gli oggetti nell'account vengono eliminati. Gli oggetti includono runbook, moduli, configurazioni, impostazioni, processi e risorse. Non possono essere recuperati dopo l'eliminazione dell'account. È possibile utilizzare le seguenti informazioni per eseguire il backup del contenuto dell'account Automation prima di eliminarlo.

### <a name="runbooks"></a>Runbook

È possibile esportare i runbook in file di script usando il portale di Azure o il cmdlet [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) in Windows PowerShell. È possibile importare questi file di script in un altro account di automazione, come descritto in [Gestire i runbook in Automazione di Azure.You](manage-runbooks.md)can import these script files into another Automation account, as discussed in Manage runbooks in Azure Automation .

### <a name="integration-modules"></a>Moduli di integrazione

Non è possibile esportare i moduli di integrazione da Automazione di Azure.You can't export integration modules from Azure Automation. È necessario renderli disponibili all'esterno dell'account di automazione.

### <a name="assets"></a>Asset

Non è possibile esportare gli asset di Automazione di Azure: certificati, connessioni, credenziali, pianificazioni e variabili. È invece possibile usare il portale di Azure e i cmdlet di Azure per annotare i dettagli di questi asset. Usa quindi questi dettagli per creare le risorse usate dai runbook importati in un altro account di Automazione.Then use these details to create any assets that are used by runbooks that you import into another Automation account.

Non è possibile recuperare i valori per le variabili crittografate o i campi password delle credenziali utilizzando i cmdlet. Se non conosci questi valori, puoi recuperarli in un runbook. Per il recupero di valori variabili, vedere [Asset variabili in Automazione di Azure.For](shared-resources/variables.md)retrieving variable values, see Variable assets in Azure Automation. Per altre informazioni sul recupero dei valori delle credenziali, vedere Asset delle credenziali in Automazione di Azure.To find out more about retrieving credential values, see [Credential assets in Azure Automation.](shared-resources/credentials.md)

 ### <a name="dsc-configurations"></a>Configurazioni DSC

È possibile esportare le configurazioni DSC in file di script usando il portale di Azure o il cmdlet Export-AzAutomationDscConfiguration in Windows PowerShell.You can export your DSC configurations to script files using either the Azure portal or the [Export-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) cmdlet in Windows PowerShell. È possibile importare e utilizzare queste configurazioni in un altro account di automazione.

## <a name="geo-replication-in-azure-automation"></a>Replica geografica in Automazione di Azure

La replica geografica è standard negli account di Automazione di Azure.Geo-replication is standard in Azure Automation accounts. Puoi scegliere un'area geografica principale durante la configurazione dell'account. Il servizio di replica geografica dell'automazione interna assegna automaticamente un'area secondaria all'account. Il servizio esegue quindi il backup continuo dei dati dell'account dall'area primaria all'area secondaria. L'elenco completo delle aree primarie e secondarie è disponibile in Continuità aziendale e ripristino di [emergenza (BCDR): Aree associate di Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

Il backup creato dal servizio di replica geografica di automazione è una copia completa di asset, configurazioni e simili di Automazione. Questo backup può essere utilizzato se l'area primaria si arresta e perde dati. Nell'improbabile caso in cui i dati per un'area primaria vengano persi, Microsoft tenta di ripristinarla. Se l'azienda non riesce a ripristinare i dati primari, usa il failover automatico e informa la situazione tramite la sottoscrizione di Azure.If the company can't recover the primary data, it uses automatic failover and informs you of the situation through your Azure subscription. 

Il servizio di replica geografica di automazione non è accessibile direttamente ai clienti esterni in caso di errore a livello di area. Se si desidera mantenere la configurazione di automazione e i runbook durante gli errori regionali:

1. Selezionare un'area secondaria da associare all'area geografica dell'account di automazione principale.

2. Creare un account di automazione nell'area secondaria.

3. Nell'account principale esportare i runbook come file di script.

4. Importa i runbook nel tuo account di automazione nell'area secondaria.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sugli asset sicuri in Automazione di Azure, vedere [Crittografare gli asset protetti in Automazione](automation-secure-asset-encryption.md)di Azure.To learn more about secure assets in Azure Automation, see Encrypt secure assets in Azure Automation.

* Per ulteriori informazioni sulla replica geografica, vedere [Creazione e utilizzo della replica geografica attiva](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication).