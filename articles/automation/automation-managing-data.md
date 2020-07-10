---
title: Sicurezza dei dati di automazione di Azure
description: Questo articolo illustra in che modo automazione di Azure protegge la privacy e protegge i dati.
services: automation
ms.subservice: shared-capabilities
ms.date: 06/03/2020
ms.topic: conceptual
ms.openlocfilehash: cc96f2dd81b618b4170acd4b415a09248adbb7d5
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186164"
---
# <a name="management-of-azure-automation-data"></a>Gestione dei dati di Automazione di Azure

Questo articolo contiene diversi argomenti che illustrano il modo in cui i dati vengono protetti e protetti in un ambiente di automazione di Azure.

## <a name="tls-12-enforcement-for-azure-automation"></a>Imposizione di TLS 1,2 per automazione di Azure

Per garantire la sicurezza dei dati in transito in automazione di Azure, si consiglia di configurare l'uso di Transport Layer Security (TLS) 1,2. Di seguito è riportato un elenco di metodi o client che comunicano tramite HTTPS al servizio di automazione:

* Chiamate webhook

* Ruoli di lavoro ibridi per Runbook, che includono computer gestiti da Gestione aggiornamenti e Rilevamento modifiche e inventario.

* Nodi DSC

Le versioni precedenti di TLS/Secure Sockets Layer (SSL) sono state considerate vulnerabili. Nonostante siano ancora attualmente in uso per questioni di compatibilità con le versioni precedenti, **non sono consigliate**. A partire da settembre 2020, si inizia a applicare TLS 1,2 e versioni successive del protocollo di crittografia.

Non è consigliabile impostare in modo esplicito l'agente perché usi solo il protocollo TLS 1.2, a meno che non sia assolutamente necessario. Questa scelta potrebbe causare l'interruzione delle funzionalità di sicurezza a livello di piattaforma che consentono di rilevare automaticamente e sfruttare i vantaggi di protocolli più recenti e più sicuri quando saranno disponibili, ad esempio TLS 1.3.

Per informazioni sul supporto di TLS 1,2 con l'agente di Log Analytics per Windows e Linux, che è una dipendenza per il ruolo di lavoro ibrido per Runbook, vedere [Panoramica di log Analytics Agent-TLS 1,2](..//azure-monitor/platform/log-analytics-agent.md#tls-12-protocol). 

### <a name="platform-specific-guidance"></a>Indicazioni specifiche in base alla piattaforma

|Piattaforma/linguaggio | Supporto | Altre informazioni |
| --- | --- | --- |
|Linux | Le distribuzioni Linux si basano generalmente su [OpenSSL](https://www.openssl.org) per supportare TLS 1.2.  | Controllare nel [log delle modifiche di OpenSSL](https://www.openssl.org/news/changelog.html) per assicurarsi che la versione di OpenSSL sia supportata.|
| Windows 8.0 - 10 | Supportato e abilitato per impostazione predefinita. | Assicurarsi che le [impostazioni predefinite](/windows-server/security/tls/tls-registry-settings) siano ancora in uso.  |
| Windows Server 2012 - 2016 | Supportato e abilitato per impostazione predefinita. | Per verificare che le [impostazioni predefinite](/windows-server/security/tls/tls-registry-settings) siano ancora utilizzate |
| Windows 7 SP1 e Windows Server 2008 R2 SP1 | Supportato ma non abilitato per impostazione predefinita. | Vedere la pagina [Transport Layer Security (TLS) registry settings](/windows-server/security/tls/tls-registry-settings) (Impostazioni del Registro di sistema di Transport Layer Security (TLS)) per informazioni dettagliate su come eseguire l'abilitazione.  |

## <a name="data-retention"></a>Conservazione dei dati

Quando si elimina una risorsa in Automazione di Azure, viene conservata per alcuni giorni per finalità di controllo, prima della rimozione permanente. In tale periodo non sarà possibile visualizzare o usare la risorsa. Questi criteri sono applicabili anche alle risorse appartenenti a un account di Automazione eliminato.

La tabella seguente riepiloga i criteri di conservazione per diverse risorse.

| Data | Policy |
|:--- |:--- |
| Account |Un account viene rimosso definitivamente 30 giorni dopo l'eliminazione da parte di un utente. |
| Asset |Un asset viene rimosso definitivamente 30 giorni dopo l'eliminazione da parte di un utente o 30 giorni dopo l'eliminazione di un account che possiede l'asset. |
| Nodi DSC |Un nodo DSC viene rimosso in modo permanente 30 giorni dopo l'annullamento della registrazione dall'account di Automazione tramite il portale di Azure o il cmdlet [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) in Windows PowerShell. I nodi vengono rimossi in modo permanente anche 30 giorni dopo aver eliminato l'account che possiede il nodo. |
| Processi |Un processo viene eliminato e rimosso definitivamente 30 giorni dopo la modifica, ad esempio dopo il completamento, l'arresto o la sospensione del processo. |
| Moduli |Un modulo viene rimosso definitivamente 30 giorni dopo l'eliminazione da parte di un utente o 30 giorni dopo l'eliminazione di un account che possiede il modulo. |
| Configurazioni di nodo/File MOF |Una configurazione di nodo precedente verrà rimossa definitivamente 30 giorni dopo che viene generata una nuova configurazione di nodo. |
| Report sul nodo |Un report sul nodo viene rimosso definitivamente 90 giorni dopo la generazione di un nuovo report per quel nodo. |
| Runbook |Un runbook viene rimosso definitivamente 30 giorni dopo l'eliminazione della risorsa da parte di un utente o 30 giorni dopo l'eliminazione dell'account che possiede la risorsa. |

I criteri di conservazione sono applicabili a tutti gli utenti e non è attualmente possibile personalizzarli. Se tuttavia è necessario conservare i dati per un periodo di tempo più lungo, è possibile [inviare i dati dei processi di Automazione di Azure ai log di Monitoraggio di Azure](automation-manage-send-joblogs-log-analytics.md).

## <a name="data-backup"></a>Backup dei dati

Quando si elimina un account di automazione in Azure, vengono eliminati tutti gli oggetti presenti nell'account. Gli oggetti includono runbook, moduli, configurazioni, impostazioni, processi e asset. Non sarà possibile ripristinare gli oggetti dopo l'eliminazione dell'account. È possibile usare le informazioni seguenti per eseguire il backup dei contenuti dell'account di Automazione prima di eliminarlo.

### <a name="runbooks"></a>Runbook

È possibile esportare i runbook in file di script usando il portale di Azure o il cmdlet [Get-AzureAutomationRunbookDefinition](/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) in Windows PowerShell. È possibile importare questi file di script in un altro account di Automazione, come illustrato in [Gestire runbook in Automazione di Azure](manage-runbooks.md).

### <a name="integration-modules"></a>Moduli di integrazione

Non è possibile esportare i moduli di integrazione da Automazione di Azure. È necessario renderli disponibili al di fuori dell'account di Automazione.

### <a name="assets"></a>Asset

Non è possibile esportare gli asset di Automazione di Azure: certificati, connessioni, credenziali, pianificazioni e variabili. È invece possibile usare il portale di Azure e i cmdlet di Azure per prendere nota dei dettagli di questi asset. Usare quindi questi dettagli per creare eventuali asset usati dai runbook importati in un altro account di Automazione.

Non è possibile recuperare il valore delle variabili crittografate o i campi password delle credenziali mediante i cmdlet. Se non si conoscono questi valori, è possibile recuperarli in un runbook. Per recuperare i valori delle variabili, vedere [Asset di tipo variabile in Automazione di Azure](shared-resources/variables.md). Per altre informazioni sul recupero dei valori delle credenziali, vedere [Asset credenziali in Automazione di Azure](shared-resources/credentials.md).

### <a name="dsc-configurations"></a>Configurazioni DSC

È possibile esportare le configurazioni DSC in file di script tramite il portale di Azure o il cmdlet [Export-AzAutomationDscConfiguration](/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0) in Windows PowerShell. È possibile importare e usare queste configurazioni in un altro account di Automazione.

## <a name="geo-replication-in-azure-automation"></a>Replica geografica in Automazione di Azure

La replica geografica è standard negli account di Automazione di Azure. Quando si configura l'account, è possibile scegliere un'area primaria. Il servizio interno di replica geografica di Automazione assegna automaticamente un'area secondaria all'account. Il servizio esegue quindi il backup continuo dei dati dell'account dall'area primaria all'area secondaria. L'elenco completo delle aree primarie e secondarie è disponibile in [Continuità aziendale e ripristino di emergenza (BCDR): aree geografiche abbinate di Azure](../best-practices-availability-paired-regions.md).

Il backup creato dal servizio di replica geografica di Automazione è una copia completa degli asset di automazione, delle configurazioni e di elementi simili. Questo backup può essere usato se l'area primaria diventa inattiva e perde i dati. Nell'eventualità improbabile che vengano persi dei dati di un'area primaria, Microsoft tenta di ripristinarli. Se l'azienda non riesce a ripristinare i dati primari, viene usato il failover automatico e viene segnalata la situazione tramite la sottoscrizione di Azure.

Il servizio di replica geografica di Automazione non è accessibile direttamente ai clienti esterni se si verifica un errore a livello di area. Se si vogliono mantenere la configurazione e i runbook di Automazione durante gli errori a livello di area:

1. Selezionare un'area secondaria da associare all'area geografica dell'account di Automazione primario.

2. Creare un account di Automazione nell'area secondaria.

3. Nell'account primario esportare i runbook come file di script.

4. Importare i runbook nell'account di Automazione dell'area secondaria.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sugli asset sicuri in Automazione di Azure, vedere [Crittografia di asset sicuri in Automazione di Azure](automation-secure-asset-encryption.md).

* Per altre informazioni sulla replica geografica, vedere [Creazione e uso della replica geografica attiva](../azure-sql/database/active-geo-replication-overview.md).
