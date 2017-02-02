---
title: Soluzione Insieme di credenziali delle chiavi di Azure in Log Analytics | Documentazione Microsoft
description: "È possibile usare la soluzione Insieme di credenziali delle chiavi di Azure in Log Analytics per esaminare i log dell&quot;Insieme di credenziali delle chiavi di Azure."
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
ms.date: 12/01/2016
ms.author: richrund
translationtype: Human Translation
ms.sourcegitcommit: 5aae95a78e604acc5f0189d5df62620d65d29857
ms.openlocfilehash: bb9ece6382c22f4c1b7905048647434fc7cee98a


---
# <a name="azure-key-vault-analytics-preview-solution-in-log-analytics"></a>Soluzione di Azure Key Vault Analytics (anteprima) in Log Analytics

È possibile usare la soluzione Insieme di credenziali delle chiavi di Azure in Log Analytics per esaminare i log AuditEvent dell'Insieme di credenziali delle chiavi di Azure.

> [!NOTE]
> Azure Key Vault Analytics è una [soluzione di anteprima](log-analytics-add-solutions.md#preview-management-solutions-and-features).
> 
> 

Per usare la soluzione, è necessario abilitare la registrazione diagnostica di Azure Key Vault e indirizzare la diagnostica a un'area di lavoro di Log Analytics. Non è necessario inserire i log nell'Archiviazione BLOB di Azure.

## <a name="install-and-configure-the-solution"></a>Installare e configurare la soluzione
Usare le istruzioni seguenti per installare e configurare la soluzione Insieme di credenziali delle chiavi di Azure:

1. Usare `Set-AzureRmDiagnosticSetting` per abilitare la registrazione diagnostica per le risorse di Key Vault da monitorare. 
2. Abilitare la soluzione Insieme di credenziali delle chiavi di Azure seguendo la procedura illustrata in [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md). 

Lo script PowerShell seguente contiene un esempio su come abilitare la registrazione diagnostica per Key Vault:
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
| Azure |![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![Sì](./media/log-analytics-azure-keyvault/oms-bullet-green.png) |![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png) | all'arrivo |

## <a name="use-azure-key-vault"></a>Usare l'Insieme di credenziali delle chiavi di Azure
Dopo l'installazione della soluzione, è possibile visualizzare il riepilogo degli stati delle richieste nel tempo per gli insiemi di credenziali delle chiavi monitorati, usando il riquadro **Insieme di credenziali delle chiavi di Azure** nella pagina **Panoramica** di Log Analytics.

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

| Proprietà | Descrizione |
|:--- |:--- |
| Tipo |*AzureDiagnostics* |
| SourceSystem |*Azzurro* |
| CallerIpAddress |Indirizzo IP del client che ha eseguito la richiesta |
| Categoria | *AuditEvent* |
| CorrelationId |GUID facoltativo che il client può passare per correlare i log sul lato client con quelli sul lato servizio (insieme di credenziali delle chiavi). |
| DurationMs |Tempo impiegato per soddisfare la richiesta API REST, in millisecondi. Non include la latenza di rete, quindi il tempo misurato sul lato client potrebbe non corrispondere a questo valore. |
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

## <a name="next-steps"></a>Passaggi successivi
* Usare le [Ricerche nei log in Log Analytics](log-analytics-log-searches.md) per visualizzare i dati dettagliati per l'Insieme di credenziali delle chiavi di Azure.




<!--HONumber=Dec16_HO1-->


