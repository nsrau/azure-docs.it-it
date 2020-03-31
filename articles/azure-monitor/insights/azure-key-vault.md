---
title: Soluzione di Archiviazione chiave di Azure in Monitoraggio di Azure Documenti Microsoft
description: È possibile usare la soluzione Archiviazione chiave di Azure in Monitoraggio di Azure per esaminare i log dell'insieme di credenziali delle chiavi di Azure.You can use the Azure Key Vault solution in Azure Monitor to review Azure Key Vault logs.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/27/2019
ms.openlocfilehash: 7a2becf8cb43568383c324bb9f4f5b2e7b844268
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667144"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Azure Key Vault Analytics solution in Azure Monitor

![Simbolo di Key Vault](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

È possibile usare la soluzione Archiviazione delle chiavi di Azure in Monitoraggio di Azure per esaminare i log AuditEvent dell'insieme di credenziali delle chiavi di Azure.You can use the Azure Key Vault solution in Azure Monitor to review Azure Key Vault AuditEvent logs.

Per usare la soluzione, è necessario abilitare la registrazione diagnostica di Azure Key Vault e indirizzare la diagnostica a un'area di lavoro Log Analytics. Non è necessario inserire i log nell'Archiviazione BLOB di Azure.

> [!NOTE]
> Nel gennaio 2017, il metodo supportato per l'invio dei log da Key Vault a Log Analytics è cambiato. Se per la soluzione Key Vault in uso è indicato *(obsoleto)* nel titolo, fare riferimento alla sezione [Migrazione dalla soluzione Key Vault precedente](#migrating-from-the-old-key-vault-solution) per i passaggi da seguire.
>
>

## <a name="install-and-configure-the-solution"></a>Installare e configurare la soluzione
Usare le istruzioni seguenti per installare e configurare la soluzione Insieme di credenziali delle chiavi di Azure:

1. Usare il processo descritto in [Aggiungere soluzioni di Monitoraggio di Azure dalla raccolta soluzioni](../../azure-monitor/insights/solutions.md) per aggiungere la soluzione di archiviazione delle chiavi di Azure all'area di lavoro log Analytics.Use the process described in Add Azure Monitor solutions from the Solutions Gallery to add the Azure Key Vault solution to your Log Analytics workspace.
2. Abilitare la registrazione diagnostica per le risorse dell'insieme di credenziali delle chiavi da monitorare, usando il portale o [PowerShellEnable](#enable-key-vault-diagnostics-using-powershell) diagnostics logging for the Key Vault resources to monitor, using either the [portal](#enable-key-vault-diagnostics-in-the-portal) or PowerShell

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Abilitare la diagnostica di Key Vault nel portale

1. Nel portale di Azure passare alla risorsa Key Vault da monitorare
2. Selezionare *Impostazioni di diagnostica* per aprire la pagina seguente

   ![Immagine del riquadro Insieme di credenziali delle chiavi di Azure](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Fare clic su *Attiva diagnostica* per aprire la pagina seguente

   ![Immagine del riquadro Insieme di credenziali delle chiavi di Azure](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Assegnare un nome all'impostazione di diagnostica.
5. Selezionare la casella di controllo *Send to Log Analytics* (Invia a Log Analytics)
6. Selezionare un'area di lavoro Log Analytics esistente o creare una
7. Per abilitare i log *AuditEvent*, fare clic sulla casella di controllo in Log
8. Fare clic su Salva per abilitare la registrazione della diagnostica nell'area di lavoro di Log Analytics.Click *Save* to enable the logging of diagnostics to Log Analytics workspace.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Abilitare la diagnostica di Key Vault utilizzando PowerShell
Lo script di PowerShell seguente fornisce `Set-AzDiagnosticSetting` un esempio di come usare per abilitare la registrazione delle risorse per l'insieme di credenziali delle chiavi:The following PowerShell script provides an example of how to use to enable resource logging for Key Vault:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Esaminare i dettagli della raccolta di dati dell'Insieme di credenziali delle chiavi di Azure
La soluzione Azure Key Vault raccoglie i log di diagnostica direttamente da Key Vault.
Non è necessario inserire i log in Archiviazione BLOB di Azure né è necessario alcun agente per la raccolta dati.

La tabella seguente illustra i metodi di raccolta dei dati e altri dettagli sulla modalità di raccolta dei dati per l'Insieme di credenziali delle chiavi di Azure.

| Piattaforma | Agente diretto | Agente di Systems Center Operations Manager | Azure | È necessario Operations Manager? | Dati dell'agente Operations Manager inviati con il gruppo di gestione | Frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | all'arrivo |

## <a name="use-azure-key-vault"></a>Usare l'Insieme di credenziali delle chiavi di Azure
Dopo aver [installato la soluzione,](https://azuremarketplace.microsoft.com/en-usrketplace/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview)visualizzare i dati dell'insieme di credenziali delle chiavi facendo clic sul riquadro **Analisi dell'insieme** di credenziali delle chiavi nella pagina **Panoramica** di Monitoraggio di Azure. Aprire questa pagina dal menu **Monitoraggio di Azure** facendo clic su **Altro** sotto la sezione **Informazioni dettagliate**. 

![Immagine del riquadro Insieme di credenziali delle chiavi di Azure](media/azure-key-vault/log-analytics-keyvault-tile.png)

Dopo aver fatto clic sul riquadro **Analisi Vault chiave,** puoi visualizzare i riepiloghi dei log e quindi eseguire il drill-in dei dettagli per le categorie seguenti:

* Volume di tutte le operazioni dell'insieme di credenziali delle chiavi nel tempo
* Volumi di operazioni non riuscite nel tempo
* Latenza operativa media per operazione
* Qualità del servizio per operazioni con numero delle operazioni che richiedono più di 1000 ms ed elenco di operazioni che richiedono più di 1000 ms

![Immagine del dashboard dell'Insieme di credenziali delle chiavi di Azure](media/azure-key-vault/log-analytics-keyvault01.png)

![Immagine del dashboard dell'Insieme di credenziali delle chiavi di Azure](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Per visualizzare i dettagli per un'operazione
1. Nella pagina **Panoramica** fare clic sul riquadro **Analisi vault chiave.**
2. Nel dashboard **Insieme di credenziali delle chiavi di Azure** esaminare le informazioni di riepilogo in uno dei pannelli, quindi fare clic su un pannello per visualizzare le informazioni dettagliate corrispondenti nella pagina di ricerca di log.

    In una pagina di ricerca di log qualsiasi è possibile visualizzare i risultati in base all'ora, ai dettagli e alla cronologia di ricerca. È anche possibile filtrare per facet in modo da limitare i risultati.

## <a name="azure-monitor-log-records"></a>Record di log di Monitoraggio di Azure
La soluzione Insieme di credenziali delle chiavi di Azure analizza i record con tipo **KeyVaults**, raccolti dai [log AuditEvent](../../key-vault/key-vault-logging.md) in Diagnostica di Azure.  Le proprietà per questi record sono disponibili nella tabella seguente:  

| Proprietà | Descrizione |
|:--- |:--- |
| `Type` |*AzureDiagnostics* |
| `SourceSystem` |*Azure* |
| `CallerIpAddress` |Indirizzo IP del client che ha eseguito la richiesta |
| `Category` | *AuditEvent* |
| `CorrelationId` |GUID facoltativo che il client può passare per correlare i log sul lato client con quelli sul lato servizio (insieme di credenziali delle chiavi). |
| `DurationMs` |Tempo impiegato per soddisfare la richiesta API REST, in millisecondi. Il tempo non include la latenza di rete, quindi il tempo misurato sul lato client potrebbe non corrispondere a questo valore. |
| `httpStatusCode_d` |Codice di stato HTTP restituito dalla richiesta (ad esempio *200*) |
| `id_s` |ID univoco della richiesta |
| `identity_claim_appid_g` | GUID per l'ID applicazione |
| `OperationName` |Nome dell'operazione, come illustrato in [Registrazione dell'Insieme di credenziali delle chiavi di Azure](../../key-vault/key-vault-logging.md) |
| `OperationVersion` |Versione dell'API REST richiesta dal client (ad esempio *2015-06-01*) |
| `requestUri_s` |URI della richiesta |
| `Resource` |Nome dell'insieme di credenziali delle chiavi |
| `ResourceGroup` |Gruppo di risorse dell'insieme di credenziali delle chiavi |
| `ResourceId` |ID della risorsa Gestione risorse di Azure. Per i log di Key Vault, questo è l'ID della risorsa Key Vault. |
| `ResourceProvider` |*Microsoft. KEYVAULT* |
| `ResourceType` | *VAULTS* |
| `ResultSignature` |Stato HTTP (ad esempio *OK*) |
| `ResultType` |Risultato della richiesta dell'API REST (ad esempio *Operazione completata*) |
| `SubscriptionId` |ID sottoscrizione di Azure della sottoscrizione che include l'insieme di credenziali delle chiavi |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migrazione dalla soluzione Key Vault precedente
Nel gennaio 2017, il metodo supportato per l'invio dei log da Key Vault a Log Analytics è cambiato. In questo modo si otterranno i vantaggi seguenti:
+ I log vengono scritti direttamente in un'area di lavoro di Log Analytics senza la necessità di usare un account di archiviazioneLogs are written directly to a Log Analytics workspace without the need to use a storage account
+ Minore latenza dal momento in cui i log vengono generati essendo immediatamente disponibili in Log Analytics
+ Meno passaggi di configurazione
+ Un formato comune per tutti i tipi di diagnostica di Azure

Per utilizzare la soluzione aggiornata:

1. [Configurare l'invio della diagnostica direttamente a un'area di lavoro di Log Analytics da Key Vault](#enable-key-vault-diagnostics-in-the-portal)  
2. Abilitare la soluzione Dell'insieme di chiavi di Azure usando il processo descritto in [Aggiungere soluzioni di Monitoraggio di Azure dalla raccolta soluzioniEnable](../../azure-monitor/insights/solutions.md) the Azure Key Vault solution by using the process described in Add Azure Monitor solutions from the Solutions Gallery
3. Aggiornare tutte le query salvate, i dashboard o gli avvisi per utilizzare il nuovo tipo di dati
   + Il tipo è cambiato da KeyVaults a AzureDiagnostics. È possibile utilizzare ResourceType per filtrare i log di Key Vault.
   + Invece di: `KeyVaults`, utilizzare i campi `AzureDiagnostics | where ResourceType'=="VAULTS"`:
   + I nomi dei campi distinguono tra maiuscole e minuscole
   + Per ogni campo con suffisso \_s, \_d o \_g nel nome, modificare il primo carattere in lettere minuscole
   + Per ogni campo con suffisso \_o nel nome, i dati sono suddivisi in singoli campi in base ai nomi dei campi nidificati. Ad esempio, il nome UPN del chiamante viene archiviato in un campo `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   + Il campo CallerIpAddress viene modificato in CallerIPAddress
   + Il campo RemoteIPCountry non è più presente
4. Rimuovere la soluzione *Key Vault Analytics (obsoleta)*. Se si utilizza PowerShell, usare `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

I dati raccolti prima della modifica non sono visibili nella nuova soluzione. È possibile continuare a eseguire query per questi dati utilizzando i nomi di campo e il tipo vecchi.

## <a name="troubleshooting"></a>Risoluzione dei problemi
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Passaggi successivi
* Usare [Query di log in Monitoraggio di Azure](../../azure-monitor/log-query/log-query-overview.md) per visualizzare i dati dettagliati dell'insieme di credenziali delle chiavi di Azure.Use Log queries in Azure Monitor to view detailed Azure Key Vault data.
