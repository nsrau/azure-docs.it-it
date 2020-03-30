---
title: Data ingestion properties for Azure Data Explorer
description: Informazioni sulle varie proprietà di inserimento dati supportate da Azure Data Explorer.Learn about the various data ingestion properties supported by Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 48bce1bf03a0a4c8d81fff7ae54e0b22261b70f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109576"
---
# <a name="azure-data-explorer-data-ingestion-properties"></a>Proprietà di inserimento dati di Azure Data ExplorerAzure Data Explorer data ingestion properties 

Data ingestion is the process by which data is added to a table and is made available for query in Azure Data Explorer. Aggiungere proprietà al comando di inserimento dopo la `with` parola chiave.

## <a name="ingestion-properties"></a>Proprietà di inserimento

Nella tabella seguente sono elencate le proprietà supportate da Azure Data Explorer, le vengono descritte e vengono forniti esempi:The following table lists the properties supported by Azure Data Explorer, describes, and provides examples: 

|Proprietà              |Descrizione                                              |Esempio                                             |
|----------------------|---------------------------------------------------------|----------------------------------------------------|
|`ingestionMapping`    |valore stringa che indica come eseguire il mapping dei dati dal file di origine alle colonne effettive della tabella. Definire `format` il valore con il tipo di mapping pertinente. Vedere [Mapping dei dati](/azure/kusto/management/mappings).|`with (format="json", ingestionMapping = "[{\"column\":\"rownumber\", \"Properties\":{\"Path\":\"$.RowNumber\"}}, {\"column\":\"rowguid\", \"Properties\":{\"Path\":\"$.RowGuid\"}}]")`<br>(deprecato: `avroMapping`, `csvMapping`, `jsonMapping`) |
|`ingestionMappingReference`|valore stringa che indica come eseguire il mapping dei dati dal file di origine alle colonne effettive della tabella tramite un oggetto criteri di mapping denominato. Definire `format` il valore con il tipo di mapping pertinente. Vedere [Mapping dei dati](/azure/kusto/management/mappings).|`with (format="csv", ingestionMappingReference = "Mapping1")`<br>(deprecato: `avroMappingReference`, `csvMappingReference`, `jsonMappingReference`)|
|`creationTime` |Valore datetime (formattato come stringa ISO8601) da utilizzare al momento della creazione degli extent di dati ingeriti. Se non è specificato, viene usato il valore corrente (`now()`). L'override dell'impostazione predefinita è utile quando si ingeriscono dati meno recenti, in modo che i criteri di conservazione vengano applicati correttamente.|`with (creationTime="2017-02-13T11:09:36.7992775Z")`|
|`extend_schema`|valore booleano che, se specificato, indica al comando di estendere lo schema della tabella (il valore predefinito è `false`). Questa opzione è valida solo per i comandi `.append` e `.set-or-append`. Le uniche estensioni dello schema consentite includono colonne aggiuntive alla fine della tabella.|Se lo schema `(a:string, b:int)`della tabella originale è `(a:string, b:int, c:datetime, d:string)`, `(a:string, c:datetime)` un'estensione di schema valida sarebbe , ma non sarebbe valida|
|`folder` |Per i comandi [di inserimento da query,](/azure/kusto/management/data-ingestion/ingest-from-query) la cartella da assegnare alla tabella. Se la tabella esiste già, questa proprietà sostituirà la cartella della tabella.|`with (folder="Tables/Temporary")`|
|`format` |Il formato dati (vedere [formati di dati supportati](ingestion-supported-formats.md)).|`with (format="csv")`|
|`ingestIfNotExists`|valore stringa che, se specificato, impedisce il completamento dell'inserimento se la tabella contiene già dati contrassegnati con un tag `ingest-by:` con lo stesso valore. In questo modo si garantisce un inserimento dati idempotente. Per ulteriori informazioni, consultate [tag ingest-by:](/azure/kusto/management/extents-overview#ingest-by-extent-tags).|Le `with (ingestIfNotExists='["Part0001"]', tags='["ingest-by:Part0001"]')` proprietà indicano che `ingest-by:Part0001` se i dati con il tag esistono già, non completare l'inserimento corrente. Se non esiste già, questa nuova inserimento deve avere questo set di tag (nel caso in cui un'inserimento futuro tenti di eseguire nuovamente l'inserimento degli stessi dati).|
|`ignoreFirstRecord` |valore booleano che, se impostato su `true`, indica che l'inserimento deve ignorare il primo record di ogni file. Questa proprietà è utile `CSV`per i file in formati simili, se il primo record nel file sono i nomi delle colonne. Per impostazione predefinita, `false` si presuppone che.|`with (ignoreFirstRecord=false)`|
|`persistDetails` |valore booleano che, se specificato, indica che il comando deve rendere permanente i risultati dettagliati (anche in caso di esito positivo) in modo che il comando [.show operation details](/azure/kusto/management/operations#show-operation-details) possa recuperarli. L'impostazione predefinita è `false`.|`with (persistDetails=true)`|
|`policy_ingestiontime`|valore booleano che, se specificato, indica se abilitare i [criteri del tempo di inserimento](/azure/kusto/management/ingestiontimepolicy) su una tabella creata da questo comando. Il valore predefinito è `true`.|`with (policy_ingestiontime=false)`|
|`recreate_schema` |valore booleano che, se specificato, indica se il comando può ricreare lo schema della tabella. Questa proprietà si `.set-or-replace` applica solo al comando. Questa proprietà ha la `extend_schema` precedenza sulla proprietà se sono impostati entrambi.|`with (recreate_schema=true)`|
|`tags`|Elenco di [tag](/azure/kusto/management/extents-overview#extent-tagging) da associare ai dati ingeriti, formattati come stringa JSON |`with (tags="['Tag1', 'Tag2']")`|
|`validationPolicy`|una stringa JSON che indica le convalide da eseguire durante l'inserimento. Per una spiegazione delle diverse opzioni, vedere [Inserimento dati.](/azure/kusto/management/data-ingestion/)| `with (validationPolicy='{"ValidationOptions":1, "ValidationImplications":1}')`(questo è in realtà il criterio predefinito)|
|`zipPattern`|Utilizzare questa proprietà quando si ingeriscono dati dall'archivio che dispone di un archivio zip. Si tratta di un valore stringa che indica l'espressione regolare da utilizzare quando si selezionano i file nell'archivio zip da ingerire.  Tutti gli altri file nell'archivio verranno ignorati.|`with (zipPattern="*.csv")`|

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni [sull'inserimento di dati](/azure/data-explorer/ingest-data-overview)
* Ulteriori informazioni sui [formati di dati supportati](ingestion-supported-formats.md)
