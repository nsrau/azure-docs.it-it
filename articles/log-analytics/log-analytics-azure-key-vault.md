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
ms.date: 07/12/2016
ms.author: richrund
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 90ef2d32a00744decdb5a50ae1f707820e87f513


---
# <a name="azure-key-vault-preview-solution-in-log-analytics"></a>Soluzione Insieme di credenziali delle chiavi di Azure (anteprima) in Log Analytics
> [!NOTE]
> Si tratta di una [soluzione disponibile in anteprima](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).
> 
> 

È possibile usare la soluzione Insieme di credenziali delle chiavi di Azure in Log Analytics per esaminare i log AuditEvent dell'Insieme di credenziali delle chiavi di Azure.

È possibile abilitare la registrazione degli eventi di controllo per l'Insieme di credenziali delle chiavi di Azure. Questi log vengono scritti nell'archivio BLOB di Azure, in cui possono essere quindi indicizzati da Log Analytics per la ricerca e l'analisi.

## <a name="install-and-configure-the-solution"></a>Installare e configurare la soluzione
Usare le istruzioni seguenti per installare e configurare la soluzione Insieme di credenziali delle chiavi di Azure:

1. Abilitare la [registrazione diagnostica per le credenziali dell'insieme di credenziali delle chiavi](../key-vault/key-vault-logging.md) da monitorare
2. Configurare Log Analytics per la lettura dei log dall'archivio BLOB usando il processo illustrato in [File JSON nell'archivio BLOB](log-analytics-azure-storage-json.md).
3. Abilitare la soluzione Insieme di credenziali delle chiavi di Azure seguendo la procedura illustrata in [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).  

## <a name="review-azure-key-vault-data-collection-details"></a>Esaminare i dettagli della raccolta di dati dell'Insieme di credenziali delle chiavi di Azure
La soluzione Insieme di credenziali delle chiavi di Azure raccoglie i log di diagnostica dall'archivio BLOB di Azure per l'Insieme di credenziali delle chiavi di Azure.
Non sono necessari agenti per la raccolta di dati.

La tabella seguente illustra i metodi di raccolta dei dati e altri dettagli sulla modalità di raccolta dei dati per l'Insieme di credenziali delle chiavi di Azure.

| Piattaforma | Agente diretto | Agente di Systems Center Operations Manager (SCOM) | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | Frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![Sì](./media/log-analytics-azure-keyvault/oms-bullet-green.png) |![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |10 minuti |

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
La soluzione Insieme di credenziali delle chiavi di Azure analizza i record con tipo **KeyVaults**, raccolti dai [log AuditEvent](../key-vault/key-vault-logging.md) in Diagnostica di Azure.  Le proprietà per questi record sono disponibili nella tabella seguente.  

| Proprietà | Descrizione |
|:--- |:--- |
| Tipo |*KeyVaults* |
| SourceSystem |*AzureStorage* |
| CallerIpAddress |Indirizzo IP del client che ha eseguito la richiesta |
| Categoria |Per i log dell'insieme di credenziali delle chiavi, AuditEvent è il solo valore disponibile. |
| CorrelationId |GUID facoltativo che il client può passare per correlare i log sul lato client con quelli sul lato servizio (insieme di credenziali delle chiavi). |
| DurationMs |Tempo impiegato per soddisfare la richiesta API REST, in millisecondi. Non include la latenza di rete, quindi il tempo misurato sul lato client potrebbe non corrispondere a questo valore. |
| HttpStatusCode_d |Codice di stato HTTP restituito dalla richiesta |
| Id_s |ID univoco della richiesta |
| Identity_o |Identità del token presentato al momento dell'esecuzione della richiesta API REST. In genere si tratta di un "utente", una "entità servizio" o una combinazione "utente+appId" come nel caso di una richiesta generata da un cmdlet di Azure PowerShell. |
| OperationName |Nome dell'operazione, come illustrato in [Registrazione dell'Insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-logging.md) |
| OperationVersion |Versione dell'API REST richiesta dal client |
| RemoteIPLatitude |Latitudine del client che ha eseguito la richiesta |
| RemoteIPLongitude |Longitudine del client che ha eseguito la richiesta |
| RemoteIPCountry |Paese del client che ha eseguito la richiesta |
| RequestUri_s |URI della richiesta |
| Risorsa |Nome dell'insieme di credenziali delle chiavi |
| ResourceGroup |Gruppo di risorse dell'insieme di credenziali delle chiavi |
| ResourceId |ID della risorsa Gestione risorse di Azure. Per i log dell'insieme di credenziali delle chiavi questo è sempre l'ID della risorsa insieme di credenziali delle chiavi. |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResultSignature |Stato HTTP |
| ResultType |Risultato della richiesta API REST |
| SubscriptionId |ID sottoscrizione di Azure della sottoscrizione che include l'insieme di credenziali delle chiavi |

## <a name="next-steps"></a>Passaggi successivi
* Usare le [Ricerche nei log in Log Analytics](log-analytics-log-searches.md) per visualizzare i dati dettagliati per l'Insieme di credenziali delle chiavi di Azure.




<!--HONumber=Nov16_HO3-->


