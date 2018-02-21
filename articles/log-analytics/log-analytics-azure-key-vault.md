---
title: Soluzione Insieme di credenziali delle chiavi di Azure in Log Analytics | Documentazione Microsoft
description: "È possibile usare la soluzione Insieme di credenziali delle chiavi di Azure in Log Analytics per esaminare i log dell'Insieme di credenziali delle chiavi di Azure."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: richrund
ms.openlocfilehash: 651586e0846ffb22a23e64b73c2cc614980d9b92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-key-vault-analytics-solution-in-log-analytics"></a>Soluzione di Azure Key Vault Analytics in Log Analytics

![Simbolo di Key Vault](./media/log-analytics-azure-keyvault/key-vault-analytics-symbol.png)

È possibile usare la soluzione Insieme di credenziali delle chiavi di Azure in Log Analytics per esaminare i log AuditEvent dell'Insieme di credenziali delle chiavi di Azure.

Per usare la soluzione, è necessario abilitare la registrazione diagnostica di Azure Key Vault e indirizzare la diagnostica a un'area di lavoro di Log Analytics. Non è necessario inserire i log nell'Archiviazione BLOB di Azure.

> [!NOTE]
> Nel gennaio 2017, il metodo supportato per l'invio dei log da Key Vault a Log Analytics è cambiato. Se per la soluzione Key Vault in uso è indicato *(obsoleto)* nel titolo, fare riferimento alla sezione [Migrazione dalla soluzione Key Vault precedente](#migrating-from-the-old-key-vault-solution) per i passaggi da seguire.
>
>

## <a name="install-and-configure-the-solution"></a>Installare e configurare la soluzione
Usare le istruzioni seguenti per installare e configurare la soluzione Insieme di credenziali delle chiavi di Azure:

1. Abilitare la soluzione Azure Key Vault da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview) o seguendo la procedura illustrata in [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).
2. Abilitare la registrazione diagnostica per le risorse di Key Vault da monitorare, usando il [portale](#enable-key-vault-diagnostics-in-the-portal) o [PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Abilitare la diagnostica di Key Vault nel portale

1. Nel portale di Azure passare alla risorsa Key Vault da monitorare
2. Selezionare *Log di diagnostica* per aprire la pagina seguente

   ![Immagine del riquadro Insieme di credenziali delle chiavi di Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault-enable-diagnostics01.png)
3. Fare clic su *Attiva diagnostica* per aprire la pagina seguente

   ![Immagine del riquadro Insieme di credenziali delle chiavi di Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault-enable-diagnostics02.png)
4. Per attivare la diagnostica, fare clic su *Attivato* in *Stato*
5. Selezionare la casella di controllo *Send to Log Analytics* (Invia a Log Analytics)
6. Selezionare un'area di lavoro Log Analytics esistente o creare una
7. Per abilitare i log *AuditEvent*, fare clic sulla casella di controllo in Log
8. Fare clic su *Salva* per abilitare la registrazione della diagnostica per Log Analytics

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Abilitare la diagnostica di Key Vault utilizzando PowerShell
Il seguente script PowerShell contiene un esempio su come utilizzare `Set-AzureRmDiagnosticSetting` per abilitare la registrazione della diagnostica per Key Vault:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Esaminare i dettagli della raccolta di dati dell'Insieme di credenziali delle chiavi di Azure
La soluzione Azure Key Vault raccoglie i log di diagnostica direttamente da Key Vault.
Non è necessario inserire i log in Archiviazione BLOB di Azure né è necessario alcun agente per la raccolta dati.

La tabella seguente illustra i metodi di raccolta dei dati e altri dettagli sulla modalità di raccolta dei dati per l'Insieme di credenziali delle chiavi di Azure.

| Piattaforma | Agente diretto | Agente di Systems Center Operations Manager | Azure | È necessario Operations Manager? | Dati dell'agente Operations Manager inviati con il gruppo di gestione | Frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | all'arrivo |

## <a name="use-azure-key-vault"></a>Usare l'Insieme di credenziali delle chiavi di Azure
Dopo aver [installato la soluzione](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview), è possibile visualizzare i dati Key Vault facendo clic sul riquadro **Azure Key Vault** dalla pagina **Panoramica** di Log Analytics.

![Immagine del riquadro Insieme di credenziali delle chiavi di Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

Dopo avere selezionato il riquadro **Panoramica**, è possibile visualizzare i riepiloghi dei log e quindi analizzare i dettagli per le categorie seguenti:

* Volume di tutte le operazioni dell'insieme di credenziali delle chiavi nel tempo
* Volumi di operazioni non riuscite nel tempo
* Latenza operativa media per operazione
* Qualità del servizio per operazioni con numero delle operazioni che richiedono più di 1000 ms ed elenco di operazioni che richiedono più di 1000 ms

![Immagine del dashboard dell'Insieme di credenziali delle chiavi di Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![Immagine del dashboard dell'Insieme di credenziali delle chiavi di Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Per visualizzare i dettagli per un'operazione
1. Nella pagina **Panoramica** fare clic sul riquadro **Insieme di credenziali delle chiavi di Azure**.
2. Nel dashboard **Insieme di credenziali delle chiavi di Azure** esaminare le informazioni di riepilogo in uno dei pannelli, quindi fare clic su un pannello per visualizzare le informazioni dettagliate corrispondenti nella pagina di ricerca di log.

    In una pagina di ricerca di log qualsiasi è possibile visualizzare i risultati in base all'ora, ai dettagli e alla cronologia di ricerca. È anche possibile filtrare per facet in modo da limitare i risultati.

## <a name="log-analytics-records"></a>Record di Log Analytics
La soluzione Insieme di credenziali delle chiavi di Azure analizza i record con tipo **KeyVaults**, raccolti dai [log AuditEvent](../key-vault/key-vault-logging.md) in Diagnostica di Azure.  Le proprietà per questi record sono disponibili nella tabella seguente:  

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| type |*AzureDiagnostics* |
| SourceSystem |*Azure* |
| CallerIpAddress |Indirizzo IP del client che ha eseguito la richiesta |
| Categoria | *AuditEvent* |
| CorrelationId |GUID facoltativo che il client può passare per correlare i log sul lato client con quelli sul lato servizio (insieme di credenziali delle chiavi). |
| DurationMs |Tempo impiegato per soddisfare la richiesta API REST, in millisecondi. Il tempo non include la latenza di rete, quindi il tempo misurato sul lato client potrebbe non corrispondere a questo valore. |
| httpStatusCode_d |Codice di stato HTTP restituito dalla richiesta (ad esempio *200*) |
| id_s |ID univoco della richiesta |
| identity_claim_appid_g | GUID per l'ID applicazione |
| OperationName |Nome dell'operazione, come illustrato in [Registrazione dell'Insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-logging.md) |
| OperationVersion |Versione dell'API REST richiesta dal client (ad esempio *2015-06-01*) |
| requestUri_s |URI della richiesta |
| Risorsa |Nome dell'insieme di credenziali delle chiavi |
| ResourceGroup |Gruppo di risorse dell'insieme di credenziali delle chiavi |
| ResourceId |ID della risorsa Gestione risorse di Azure. Per i log di Key Vault, questo è l'ID della risorsa Key Vault. |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResourceType | *VAULTS* |
| ResultSignature |Stato HTTP (ad esempio *OK*) |
| ResultType |Risultato della richiesta dell'API REST (ad esempio *Operazione completata*) |
| SubscriptionId |ID sottoscrizione di Azure della sottoscrizione che include l'insieme di credenziali delle chiavi |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migrazione dalla soluzione Key Vault precedente
Nel gennaio 2017, il metodo supportato per l'invio dei log da Key Vault a Log Analytics è cambiato. In questo modo si otterranno i vantaggi seguenti:
+ I log vengono scritti direttamente in Log Analytics senza la necessità di utilizzare un account di archiviazione
+ Minore latenza dal momento in cui i log vengono generati essendo immediatamente disponibili in Log Analytics
+ Meno passaggi di configurazione
+ Un formato comune per tutti i tipi di diagnostica di Azure

Per utilizzare la soluzione aggiornata:

1. [Configurare la diagnostica in modo che venga inviata direttamente a Log Analytics da Key Vault](#enable-key-vault-diagnostics-in-the-portal)  
2. Abilitare la soluzione Azure Key Vault seguendo la procedura illustrata in [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md)
3. Aggiornare tutte le query salvate, i dashboard o gli avvisi per utilizzare il nuovo tipo di dati
  + Il tipo è cambiato da KeyVaults a AzureDiagnostics. È possibile utilizzare ResourceType per filtrare i log di Key Vault.
  - Invece di: `Type=KeyVaults`, utilizzare i campi `Type=AzureDiagnostics ResourceType=VAULTS`:
  + I nomi dei campi distinguono tra maiuscole e minuscole
  - Per ogni campo con suffisso \_s, \_d o \_g nel nome, modificare il primo carattere in lettere minuscole
  - Per ogni campo con suffisso \_o nel nome, i dati sono suddivisi in singoli campi in base ai nomi dei campi nidificati. Ad esempio, il nome UPN del chiamante viene archiviato in un campo `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   - Il campo CallerIpAddress viene modificato in CallerIPAddress
   - Il campo RemoteIPCountry non è più presente
4. Rimuovere la soluzione *Key Vault Analytics (obsoleta)*. Se si utilizza PowerShell, usare `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

I dati raccolti prima della modifica non sono visibili nella nuova soluzione. È possibile continuare a eseguire query per questi dati utilizzando i nomi di campo e il tipo vecchi.

## <a name="troubleshooting"></a>risoluzione dei problemi
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Passaggi successivi
* Usare le [Ricerche nei log in Log Analytics](log-analytics-log-searches.md) per visualizzare i dati dettagliati per l'Insieme di credenziali delle chiavi di Azure.
