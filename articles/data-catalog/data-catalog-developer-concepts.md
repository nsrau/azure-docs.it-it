---
title: Concetti per sviluppatori di Data Catalog | Microsoft Docs
description: Introduzione ai concetti chiave nel modello concettuale del Catalogo dati di Azure, come esposto tramite l&quot;API REST del Catalogo.
services: data-catalog
documentationcenter: 
author: spelluru
manager: jhubbard
editor: 
tags: 
ms.assetid: 89de9137-a0a4-40d1-9f8d-625acad31619
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 10/11/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e05dc5695b08cdd82107bb3ed473c5d7b299f1e2


---
# <a name="azure-data-catalog-developer-concepts"></a>Concetti per sviluppatori del Catalogo dati di Azure
**Catalogo dati di Microsoft Azure** è un servizio cloud completamente gestito che offre funzionalità di individuazione dell’origine dati e di crowdsourcing dei metadati dell'origine dati. Gli sviluppatori possono usare il servizio tramite le API REST. La comprensione dei concetti implementati nel servizio è importante per gli sviluppatori al fine di una perfetta integrazione con il **Catalogo dati di Azure**.

## <a name="key-concepts"></a>Concetti chiave
Il modello concettuale di **Azure Data Catalog** è basato su quattro concetti chiave: il **catalogo**, gli **utenti**, gli **asset** e le **annotazioni**.

![concetto][1]

*Figura 1. Modello concettuale semplificato del Catalogo dati di Azure*

### <a name="catalog"></a>Catalogo
Il **catalogo** è il contenitore di livello principale per tutti i metadati archiviati da un'organizzazione. Per ogni account Azure è consentito un solo **catalogo**. I cataloghi sono legati a una sottoscrizione di Azure. Anche se un account può avere più sottoscrizioni, per un determinato account Azure è possibile creare un solo **catalogo**.

Un catalogo contiene **utenti** e **asset**.

### <a name="users"></a>Utenti
Gli utenti sono entità di sicurezza autorizzati a eseguire azioni nel catalogo, ad esempio a fare ricerche nel catalogo, aggiungere, modificare o rimuovere elementi e così via.

Diversi sono i ruoli disponibili che un utente può avere. Per altre informazioni sui ruoli, vedere la sezione Ruoli e autorizzazione.

È possibile aggiungere singoli utenti e gruppi di sicurezza.

Il Catalogo dati di Azure usa Azure Active Directory per la gestione delle identità e degli accessi. Ogni utente del catalogo deve essere un membro di Active Directory per l'account.

### <a name="assets"></a>asset
Il **catalogo** contiene gli asset di dati. **asset** rappresentano l'unità di granularità gestita dal catalogo.

La granularità di un asset varia a seconda dell'origine dati. Per database Oracle o SQL Server un asset può essere una tabella o una vista. Per SQL Server Analysis Services un asset può essere una misura, una dimensione o un indicatore di prestazioni chiave (KPI). Per SQL Server Reporting Services un asset è un report.

L’ **asset** è l'elemento che si aggiunge o si rimuove da un catalogo. È l'unità di risultato ottenuto dalla **ricerca**.

L' **asset** è composto dal relativo nome, dal percorso, dal tipo e dalle annotazioni che lo descrivono ulteriormente.

### <a name="annotations"></a>annotazioni
Le annotazioni sono elementi che rappresentano i metadati relativi agli asset.

Descrizioni, tag, schemi, documentazione e così via sono esempi di annotazioni. Un elenco completo dei tipi di annotazione e di asset è disponibile nella sezione Modello a oggetti asset.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Annotazioni crowdsourcing e prospettiva dell'utente (molteplicità di opinione)
Un aspetto fondamentale del Catalogo dati di Azure è il supporto crowdsourcing dei metadati nel sistema. A differenza dell’approccio wiki, dove è presente una sola opinione e l'ultima scrittura prevale, il modello Catalogo dati di Azure consente a più opinioni di convivere affiancate nel sistema.

Questo approccio riflette il mondo reale dei dati aziendali in cui diversi utenti possono avere diverse prospettive su un determinato asset:

* Un amministratore del database può fornire informazioni sui contratti di servizio o la finestra di elaborazione disponibile per le operazioni ETL in blocco
* Un amministratore dei dati può fornire informazioni sui processi aziendali a cui si applica l'asset o le classificazioni a cui è stato applicato dall’azienda
* Un analista finanziario può fornire informazioni sull'uso dei dati durante le attività di reporting di fine periodo

Per supportare questo esempio, ogni utente (l'amministratore del database, l'amministratore dei dati e l’analista) può aggiungere una descrizione a una singola tabella che è stata registrata nel catalogo. Tutte le descrizioni vengono gestite nel sistema e nel portale del Catalogo dati di Azure vengono visualizzate tutte le descrizioni.

Questo modello viene applicato alla maggior parte degli elementi del modello a oggetti. I tipi di oggetto nel payload JSON, quindi, sono spesso matrici per le proprietà in cui è possibile prevedere un singleton.

Ad esempio, sotto la radice dell'asset è disponibile una matrice di oggetti descrizione. La proprietà della matrice è denominata "descriptions". Un oggetto descrizione ha una proprietà, description. In base al modello, ogni utente che digita una descrizione ottiene un oggetto descrizione creato per il valore fornito dall'utente.

L'esperienza utente può quindi scegliere come visualizzare la combinazione. Esistono tre diversi modelli per la visualizzazione.

* Il modello più semplice è "Mostra tutto". In questo modello tutti gli oggetti vengono visualizzati in una visualizzazione elenco. Il portale di Azure Data Catalog fa uso di questo modello per la descrizione.
* Un altro modello è "Merge". In questo modello tutti i valori dai diversi utenti vengono uniti e i duplicati vengono rimossi. Esempi di questo modello nell’esperienza utente nel portale del Catalogo dati di Azure sono le proprietà di tag ed esperti.
* Un terzo modello è "ultima scrittura prevale". In questo modello viene visualizzato solo il valore digitato più di recente. friendlyName è un esempio di questo modello.

## <a name="asset-object-model"></a>Modello a oggetti asset
Come descritto nella sezione dei concetti chiave, il modello a oggetti del **Catalogo dati di Azure** include elementi che possono essere asset o annotazioni. Gli elementi dispongono di proprietà che possono essere obbligatorie o facoltative. Alcune proprietà si applicano a tutti gli elementi. Alcune proprietà si applicano a tutti gli asset. Alcune proprietà si applicano solo a tipi di asset specifici.

### <a name="system-properties"></a>Proprietà di sistema
<table><tr><td><b>Nome proprietà</b></td><td><b>Tipo di dati</b></td><td><b>Commenti</b></td></tr><tr><td>timestamp</td><td>DateTime</td><td>Data e ora dell'ultima modifica apportata all'elemento. Questo campo viene generato dal server quando viene inserito un elemento e ogni volta che viene aggiornato un elemento. Il valore di questa proprietà viene ignorato durante l'input di operazioni di pubblicazione.</td></tr><tr><td>id</td><td>Uri</td><td>URL assoluto dell'elemento (sola lettura). Si tratta dell'URI indirizzabile univoco per l'elemento.  Il valore di questa proprietà viene ignorato durante l'input di operazioni di pubblicazione.</td></tr><tr><td>type</td><td>Stringa</td><td>Tipo di asset (sola lettura).</td></tr><tr><td>etag</td><td>String</td><td>Stringa che corrisponde alla versione dell'elemento che è possibile usare per il controllo della concorrenza ottimistica quando si eseguono operazioni che aggiornano gli elementi nel catalogo. È possibile usare "*" per cercare corrispondenze per qualsiasi valore.</td></tr></table>

### <a name="common-properties"></a>Proprietà comuni
Queste proprietà si applicano a tutti i tipi di asset radice e a tutti i tipi di annotazione.

<table>
<tr><td><b>Nome proprietà</b></td><td><b>Tipo di dati</b></td><td><b>Commenti</b></td></tr>
<tr><td>fromSourceSystem</td><td>Boolean</td><td>Indica se i dati dell'elemento sono derivati da un sistema di origine, ad esempio un database SQL Server o un database Oracle, o creati da un utente.</td></tr>
</table>

### <a name="common-root-properties"></a>Proprietà radice comuni
<p>
Queste proprietà si applicano a tutti i tipi di asset radice.

<table><tr><td><b>Nome proprietà</b></td><td><b>Tipo di dati</b></td><td><b>Commenti</b></td></tr><tr><td>name</td><td>String</td><td>Nome derivato dalle informazioni del percorso di origine dati.</td></tr><tr><td>dsl</td><td>DataSourceLocation</td><td>Descrive in modo univoco l'origine dati ed è uno degli identificatori per l’asset. Vedere la sezione relativa all’identità doppia.  La struttura del percorso dell'origine dati varia in base al tipo di protocollo e di origine.</td></tr><tr><td>dataSource</td><td>DataSourceInfo</td><td>Ulteriori dettagli sul tipo di asset.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Descrive l'utente che ha registrato l’asset più di recente.  Contiene l'ID univoco per l'utente (upn) e un nome visualizzato (lastName e firstName).</td></tr><tr><td>containerId</td><td>String</td><td>ID dell'asset di contenitore per l'origine dati. Questa proprietà non è supportata per il tipo di contenitore.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Proprietà comuni di annotazione non singleton
Queste proprietà si applicano a tutti i tipi di annotazione non singleton, ad esempio le annotazioni di cui sono consentite più occorrenze per ogni asset.

<table>
<tr><td><b>Nome proprietà</b></td><td><b>Tipo di dati</b></td><td><b>Commenti</b></td></tr>
<tr><td>key</td><td>String</td><td>Chiave specificata dall'utente che identifica in modo univoco l'annotazione nella raccolta corrente. La lunghezza della chiave non può superare i 256 caratteri.</td></tr>
</table>

### <a name="root-asset-types"></a>Tipi di asset radice
I tipi di asset radice rappresentano i diversi tipi di asset di dati che possono essere registrati nel catalogo. Per ogni tipo radice è disponibile una vista che descrive asset e annotazioni inclusi nella vista. Il nome della vista deve essere usato nel segmento dell'URL corrispondente {vista_nome} quando un asset viene pubblicato tramite l'API REST.

<table><tr><td><b>Tipo di asset (nome vista)</b></td><td><b>Proprietà aggiuntive</b></td><td><b>Tipo di dati</b></td><td><b>Annotazioni consentite</b></td><td><b>Commenti</b></td></tr><tr><td>Tabella ("tabelle")</td><td></td><td></td><td>Descrizione<p>FriendlyName<p>Tag<p>Schema<p>ColumnDescription<p>ColumnTag<p> Esperto<p>Preview<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>Documentazione<p></td><td>Tabella che rappresenta i dati tabulari.  Ad esempio: tabella SQL, vista SQL, tabella tabulare di Analysis Services, dimensione multidimensionale di Analysis Services, tabella Oracle e così via.   </td></tr><tr><td>Misura ("misure")</td><td></td><td></td><td>Descrizione<p>FriendlyName<p>Tag<p>Esperto<p>AccessInstruction<p>Documentazione<p></td><td>Tipo che rappresenta una misura di Analysis Services.</td></tr><tr><td></td><td>Misura</td><td>Colonna</td><td></td><td>Metadati che descrivono la misura.</td></tr><tr><td></td><td>isCalculated </td><td>Boolean</td><td></td><td>Specifica se la misura viene calcolata o meno.</td></tr><tr><td></td><td>measureGroup</td><td>String</td><td></td><td>Contenitore fisico per la misura.</td></tr><td>Indicatore KPI ("indicatori KPI")</td><td></td><td></td><td>Descrizione<p>FriendlyName<p>Tag<p>Esperto<p>AccessInstruction<p>Documentazione</td><td></td></tr><tr><td></td><td>measureGroup</td><td>String</td><td></td><td>Contenitore fisico per la misura.</td></tr><tr><td></td><td>goalExpression</td><td>String</td><td></td><td>Espressione numerica MDX o calcolo che restituisce il valore di destinazione dell'indicatore KPI.</td></tr><tr><td></td><td>valueExpression</td><td>String</td><td></td><td>Espressione numerica MDX che restituisce il valore effettivo dell'indicatore KPI.</td></tr><tr><td></td><td>statusExpression</td><td>String</td><td></td><td>Espressione MDX che rappresenta lo stato dell'indicatore KPI in un punto specifico nel tempo.</td></tr><tr><td></td><td>trendExpression</td><td>String</td><td></td><td>Espressione MDX che restituisce il valore dell'indicatore KPI nel tempo. La tendenza può essere un qualsiasi criterio basato sul tempo utile in un contesto aziendale specifico.</td>
<tr><td>Report ("report")</td><td></td><td></td><td>Descrizione<p>FriendlyName<p>Tag<p>Esperto<p>AccessInstruction<p>Documentazione<p></td><td>Tipo che rappresenta un report di SQL Server Reporting Services. </td></tr><tr><td></td><td>assetCreatedDate</td><td>Stringa</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>Stringa</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>Stringa</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>Stringa</td><td></td><td></td></tr><tr><td>Contenitore ("contenitori")</td><td></td><td></td><td>Descrizione<p>FriendlyName<p>Tag<p>Esperto<p>AccessInstruction<p>Documentazione<p></td><td>Questo tipo rappresenta un contenitore di altri asset, ad esempio un database SQL, un contenitore di BLOB di Azure o un modello di Analysis Services.</td></tr></table>

### <a name="annotation-types"></a>Tipi di annotazione
I tipi di annotazione sono tipi di metadati che possono essere assegnati ad altri tipi all'interno del catalogo.

<table>
<tr><td><b>Tipo di annotazione (nome vista annidata)</b></td><td><b>Proprietà aggiuntive</b></td><td><b>Tipo di dati</b></td><td><b>Commenti</b></td></tr>

<tr><td>Descrizione ("descrizioni")</td><td></td><td></td><td>Questa proprietà contiene una descrizione per un asset. Ogni utente del sistema può aggiungere una descrizione.  Solo tale utente può modificare l'oggetto descrizione.  Gli amministratori e i proprietari di asset possono eliminare l'oggetto Description ma non modificarlo. Il sistema gestisce le descrizioni degli utenti separatamente.  Pertanto è disponibile una matrice di descrizioni in ogni asset (una per ogni utente che ha contribuito con le proprie conoscenze sull'asset, oltre a probabilmente una contenente le informazioni derivate dall'origine dati).</td></tr>
<tr><td></td><td>description</td><td>string</td><td>Breve descrizione (2-3 righe) dell'asset</td></tr>

<tr><td>Tag ("tag")</td><td></td><td></td><td>Questa proprietà definisce un tag per un asset. Ogni utente del sistema può aggiungere più tag per un asset.  Solo l'utente che ha creato gli oggetti Tag può modificarli.  Gli amministratori e i proprietari di asset possono eliminare l'oggetto Tag ma non modificarlo. Il sistema gestisce i tag degli utenti separatamente.  Di conseguenza, è disponibile una matrice di oggetti Tag in ogni asset.</td></tr>
<tr><td></td><td>tag</td><td>string</td><td>Tag che descrive l'asset.</td></tr>

<tr><td>FriendlyName ("friendlyName")</td><td></td><td></td><td>Questa proprietà contiene un nome descrittivo per un asset. FriendlyName è un'annotazione singleton. È possibile aggiungere una sola annotazione FriendlyName a un asset.  Solo l'utente che ha creato l'oggetto FriendlyName può modificarlo. Gli amministratori e i proprietari di asset possono eliminare l'oggetto FriendlyName ma non modificarlo. Il sistema gestisce i nomi descrittivi degli utenti separatamente.</td></tr>
<tr><td></td><td>friendlyName</td><td>string</td><td>Nome descrittivo dell'asset.</td></tr>

<tr><td>Schema ("schema")</td><td></td><td></td><td>Schema che descrive la struttura dei dati.  Elenca i nomi di attributo, come colonna, attributo, campo e così via, i tipi e altri metadati.  Queste informazioni sono derivate dall'origine dati.  Lo schema è un'annotazione singleton. È possibile aggiungere un solo schema a un asset.</td></tr>
<tr><td></td><td>columns</td><td>Column[]</td><td>Matrice di oggetti colonna. Descrivono la colonna con le informazioni derivate dall'origine dati.</td></tr>

<tr><td>ColumnDescription ("columnDescriptions")</td><td></td><td></td><td>Questa proprietà contiene una descrizione per una colonna.  Tutti gli utenti del sistema possono aggiungere descrizioni per più colonne, con un massimo di una descrizione per ogni colonna. Solo l'utente che ha creato gli oggetti ColumnDescription può modificarli.  Gli amministratori e i proprietari di asset possono eliminare l'oggetto ColumnDescription ma non modificarlo. Il sistema gestisce le descrizioni delle colonne dell'utente separatamente.  Di conseguenza, è disponibile una matrice di oggetti ColumnDescription in ogni asset (una per colonna per ogni utente che ha contribuito con le proprie conoscenze sulla colonna). Inoltre, è possibile che ce ne sia un'altra che contiene le informazioni derivate dall'origine dati.  ColumnDescription è associato in modo generico allo schema, quindi può non essere sincronizzato. ColumnDescription potrebbe descrivere una colonna non più presente nello schema.  La sincronizzazione tra descrizione e schema spetta al writer.  Le informazioni sulla descrizione delle colonne eventualmente presenti nell'origine dati sono oggetti ColumnDescription aggiuntivi che vengono creati durante l'esecuzione dello strumento.</td></tr>
<tr><td></td><td>columnName</td><td>Stringa</td><td>Nome della colonna a cui fa riferimento questa descrizione.</td></tr>
<tr><td></td><td>description</td><td>Stringa</td><td>Breve descrizione (2-3 righe) della colonna.</td></tr>

<tr><td>ColumnTag ("columnTags")</td><td></td><td></td><td>Questa proprietà contiene un tag per una colonna. Ogni utente del sistema può aggiungere più tag a una determinata colonna e può aggiungere tag a più colonne. Solo l'utente che ha creato gli oggetti ColumnTag può modificarli. Gli amministratori e i proprietari di asset possono eliminare l'oggetto ColumnTag ma non modificarlo. Il sistema gestisce i tag delle colonne dell'utente separatamente.  Di conseguenza, è disponibile una matrice di oggetti ColumnTag in ogni asset.  ColumnTag è associato in modo generico allo schema, quindi può non essere sincronizzato. ColumnTag potrebbe descrivere una colonna non più presente nello schema.  La sincronizzazione tra tag delle colonne e schema spetta al writer.</td></tr>
<tr><td></td><td>columnName</td><td>Stringa</td><td>Nome della colonna a cui fa riferimento questo tag.</td></tr>
<tr><td></td><td>tag</td><td>Stringa</td><td>Tag che descrive la colonna.</td></tr>

<tr><td>Esperto ("esperti")</td><td></td><td></td><td>Questa proprietà contiene un utente che viene considerato un esperto nel set di dati. Le opinioni degli esperti, ad esempio le descrizioni, vengono visualizzate all'inizio dell'esperienza utente nell'elenco delle descrizioni. Ogni utente può specificare i propri esperti. Solo tale utente può modificare l'oggetto esperti. Gli amministratori e i proprietari di asset possono eliminare l'oggetto Expert ma non modificarlo.</td></tr>
<tr><td></td><td>esperto</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>Anteprima ("anteprime")</td><td></td><td></td><td>L'anteprima contiene uno snapshot delle prime 20 righe di dati per l'asset. L'anteprima risulta utile solo per alcuni tipi di asset, ad esempio per la tabella ma non per la misura.</td></tr>
<tr><td></td><td>preview</td><td>object[]</td><td>Matrice di oggetti che rappresentano una colonna.  Ogni oggetto ha un mapping di proprietà a una colonna con un valore della colonna per la riga.</td></tr>

<tr><td>AccessInstruction ("accessInstructions")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>string</td><td>Il tipo mime del contenuto.</td></tr>
<tr><td></td><td>content</td><td>string</td><td>Istruzioni su come ottenere l'accesso a questa risorsa di dati. Il contenuto può essere un URL, un indirizzo di posta elettronica o un set di istruzioni.</td></tr>

<tr><td>TableDataProfile ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>Il numero di righe nel set di dati</td></tr>
<tr><td></td><td>size</td><td>long</td><td>La dimensione in byte del set di dati.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>string</td><td>L’ora dell'ultima modifica apportata allo schema</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>stringa</td><td>Ora dell'ultima modifica al set di dati, con aggiunta, modifica o eliminazione di dati</td></tr>

<tr><td>ColumnsDataProfile ("columnsDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columns</td></td><td>ColumnDataProfile[]</td><td>Matrice di profili dati della colonna.</td></tr>

<tr><td>ColumnDataClassification ("columnDataClassifications")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>Nome della colonna a cui fa riferimento questa classificazione.</td></tr>
<tr><td></td><td>classificazione</td><td>String</td><td>La classificazione dei dati in questa colonna.</td></tr>

<tr><td>Documentazione ("documentazione")</td><td></td><td></td><td>Un determinato asset può avere solo una documentazione associata.</td></tr>
<tr><td></td><td>mimeType</td><td>string</td><td>Il tipo mime del contenuto.</td></tr>
<tr><td></td><td>content</td><td>string</td><td>Il contenuto della documentazione.</td></tr>

</table>

### <a name="common-types"></a>Tipi comuni
I tipi comuni possono essere usati come tipi per proprietà, ma non sono elementi.

<table>
<tr><td><b>Tipo comune</b></td><td><b>Proprietà</b></td><td><b>Tipo di dati</b></td><td><b>Commenti</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>sourceType</td><td>string</td><td>Descrive il tipo di origine dati.  Ad esempio: SQL Server, database Oracle e così via.  </td></tr>
<tr><td></td><td>objectType</td><td>stringa</td><td>Descrive il tipo di oggetto nell'origine dati. Ad esempio: tabella, vista per SQL Server.</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>protocol</td><td>string</td><td>Obbligatorio. Descrive un protocollo usato per comunicare con l'origine dati. Ad esempio: "tds" per SQL Server, "oracle" per Oracle e così via. Per l'elenco dei protocolli attualmente supportati, vedere la [Specifica di riferimento per l'origine dati: struttura DSL](data-catalog-dsr.md).</td></tr>
<tr><td></td><td>Address</td><td>Dizionario<string, object></td><td>Obbligatorio. Si tratta di un set di dati specifico per il protocollo usato per identificare l'origine dati a cui si fa riferimento. Dati indirizzo specifici per un determinato protocollo, vale a dire privi di significato se il protocollo non è noto.</td></tr>
<tr><td></td><td>authentication</td><td>stringa</td><td>Facoltativo. Schema di autenticazione usato per comunicare con l'origine dati. Ad esempio, Windows, OAuth e così via.</td></tr>
<tr><td></td><td>connectionProperties</td><td>Dizionario<string, object></td><td>Facoltativo. Altre informazioni su come connettersi a un'origine dati.</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>Il back-end non esegue alcuna convalida delle proprietà fornite in base ad AAD durante la pubblicazione.</td></tr>
<tr><td></td><td>upn</td><td>string</td><td>Indirizzo e-mail univoco dell'utente. Deve essere specificato se objectId non viene fornito o se è nel contesto della proprietà "lastRegisteredBy". In caso contrario, è facoltativo.</td></tr>
<tr><td></td><td>objectId</td><td>Guid</td><td>Identità AAD del gruppo di utenti o di sicurezza. Facoltativo. Deve essere specificato se upn non viene fornito. In caso contrario, è facoltativo.</td></tr>
<tr><td></td><td>firstName</td><td>string</td><td>Nome dell'utente (per scopi di visualizzazione). Facoltativo. Valido solo nel contesto della proprietà "lastRegisteredBy". Non può essere specificato quando si fornisce l'entità di sicurezza per "ruoli", "autorizzazioni" ed "esperti".</td></tr>
<tr><td></td><td>lastName</td><td>string</td><td>Cognome dell'utente (per scopi di visualizzazione). Facoltativo. Valido solo nel contesto della proprietà "lastRegisteredBy". Non può essere specificato quando si fornisce l'entità di sicurezza per "ruoli", "autorizzazioni" ed "esperti".</td></tr>

<tr><td>Colonna</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>string</td><td>Nome della colonna o dell'attributo.</td></tr>
<tr><td></td><td>type</td><td>string</td><td>Tipo di dati della colonna o dell'attributo. I tipi consentiti dipendono dal sourceType dei dati dell'asset.  È supportato un solo subset di tipi.</td></tr>
<tr><td></td><td>maxLength</td><td>int</td><td>Lunghezza massima consentita per la colonna o l’attributo. Derivata dall'origine dati. Valida solo per alcuni tipi di origine.</td></tr>
<tr><td></td><td>precision</td><td>byte</td><td>Precisione della colonna o dell’attributo. Derivata dall'origine dati. Valida solo per alcuni tipi di origine.</td></tr>
<tr><td></td><td>isNullable</td><td>Boolean</td><td>Verifica se la colonna può avere un valore null o meno. Derivata dall'origine dati. Valida solo per alcuni tipi di origine.</td></tr>
<tr><td></td><td>expression</td><td>stringa</td><td>Se il valore è una colonna calcolata, questo campo contiene l'espressione che esprime il valore. Derivata dall'origine dati. Valida solo per alcuni tipi di origine.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>string</td><td>Il nome della colonna</td></tr>
<tr><td></td><td>type </td><td>string</td><td>Il tipo della colonna</td></tr>
<tr><td></td><td>min </td><td>string</td><td>Il valore minimo nel set di dati</td></tr>
<tr><td></td><td>max </td><td>string</td><td>Il valore massimo nel set di dati</td></tr>
<tr><td></td><td>avg </td><td>double</td><td>Il valore medio del set di dati</td></tr>
<tr><td></td><td>stdev </td><td>double</td><td>La deviazione standard per il set di dati</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>Il numero di valori null nel set di dati</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>Il numero di valori distinct nel set di dati</td></tr>


</table>

## <a name="asset-identity"></a>Identità di asset
Azure Data Catalog usa le proprietà Identity e "protocol" disponibili nel contenitore della proprietà "address", nella proprietà DataSourceLocation "dsl", per generare l'identità dell'asset usata per trovare l'asset nel catalogo.
Ad esempio, le proprietà Identity del protocollo "tds" sono "server", "database", "schema" e "object". Per generare l'identità dell'asset tabella di SQL Server vengono usate combinazioni delle proprietà Identity e "protocol".
Azure Data Catalog offre vari protocolli di origine dati predefiniti, che sono elencati nella [specifica di riferimento per l'origine dati: struttura DSL](data-catalog-dsr.md).
Il set di protocolli supportati può essere esteso a livello di codice. Vedere in proposito il riferimento all'API REST di Data Catalog. Gli amministratori di Data Catalog possono registrare protocolli di origine dati personalizzati. La tabella seguente illustra le proprietà necessarie per registrare un protocollo personalizzato.

### <a name="custom-data-source-protocol-specification"></a>Specifica del protocollo di origine dati personalizzato
<table>
<tr><td><b>Tipo</b></td><td><b>Proprietà</b></td><td><b>Tipo di dati</b></td><td><b>Commenti</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>namespace</td><td>string</td><td>Spazio dei nomi del protocollo. Lo spazio dei nomi deve avere una lunghezza compresa tra 1 e 255 caratteri e contenere una o più parti non vuote separate da un punto (.). Ogni parte deve avere una lunghezza compresa tra 1 e 255 caratteri, iniziare con una lettera e contenere solo lettere e numeri.</td></tr>
<tr><td></td><td>name</td><td>string</td><td>Nome del protocollo. Lo spazio dei nomi deve avere una lunghezza compresa tra 1 e 255 caratteri, iniziare con una lettera e contenere solo lettere, numeri e trattini (-).</td></tr>
<tr><td></td><td>identityProperties</td><td>DataSourceProtocolIdentityProperty[]</td><td>Elenco di proprietà Identity, deve contenere almeno una proprietà, ma non oltre 20. Ad esempio: "server", "database", "schema", "object" sono proprietà Identity del protocollo "tds".</td></tr>
<tr><td></td><td>identitySets</td><td>DataSourceProtocolIdentitySet[]</td><td>Elenco di set di identità. Definisce i set di proprietà Identity che rappresentano l'identità valida dell'asset. Deve contenere almeno un set, ma non oltre 20. Ad esempio: {"server", "database", "schema" e "object"} è un set di identità del protocollo "tds", che definisce l'identità dell'asset Tabella di SQL Server.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>string</td><td>Nome della proprietà. Il nome della proprietà deve avere una lunghezza compresa tra 1 e 100 caratteri, iniziare con una lettera e può contenere solo lettere e numeri.</td></tr>
<tr><td></td><td>type</td><td>string</td><td>Tipo della proprietà. Valori supportati: "bool", boolean", "byte", "guid", "int", "integer", "long", "string", "url"</td></tr>
<tr><td></td><td>ignoreCase</td><td>bool</td><td>Indica se deve essere ignorato il caso quando si usa il valore della proprietà. Può essere specificato solo per le proprietà di tipo "string". Il valore predefinito è False.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>bool[]</td><td>Indica se deve essere ignorato il caso per ogni segmento del percorso URL. Può essere specificato solo per le proprietà di tipo "url". Il valore predefinito è [false].</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>stringa</td><td>Nome del set di identità.</td></tr>
<tr><td></td><td>properties</td><td>string[]</td><td>Elenco delle proprietà Identity incluse in questo set. Non può contenere duplicati. Ogni proprietà a cui fa riferimento il set di identità deve essere definita nell'elenco di "identityProperties" del protocollo.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Ruoli e autorizzazione
Catalogo dati di Microsoft Azure offre funzionalità di autorizzazione per le operazioni CRUD su asset e annotazioni.

## <a name="key-concepts"></a>Concetti chiave
Catalogo dati di Azure usa due meccanismi di autorizzazione:

* Autorizzazione basata sui ruoli
* Autorizzazione basata sulle autorizzazioni

### <a name="roles"></a>Ruoli
Sono disponibili tre ruoli: **Amministratore**, **Proprietario** e **Collaboratore**.  Ogni ruolo ha un ambito e dei diritti che sono riepilogati nella tabella seguente.

<table><tr><td><b>Ruolo</b></td><td><b>Scope</b></td><td><b>Diritti</b></td></tr><tr><td>Amministratore</td><td>Catalogo (ad esempio tutti gli asset o le annotazioni del catalogo)</td><td>Read Delete ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Proprietario</td><td>Ogni asset (elemento radice)</td><td>Read Delete ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Collaboratore</td><td>Ogni singolo asset e annotazione</td><td>Read Update Delete ViewRoles Nota: tutti i diritti vengono revocati se il diritto Read sull'elemento viene revocato dall'autore</td></tr></table>

> [!NOTE]
> I diritti **Read**, **Update**, **Delete** e **ViewRoles** sono applicabili a qualsiasi elemento (asset o annotazione) mentre **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility** e **ViewPermissions** sono applicabili solo all'asset radice.
> 
> **Delete** si applica a un elemento e agli eventuali elementi secondari o al singolo elemento sottostante. Ad esempio, l'eliminazione di un asset comporta l'eliminazione delle annotazioni dell'asset.
> 
> 

### <a name="permissions"></a>Autorizzazioni
L'autorizzazione è come un elenco di voci di controllo di accesso. Ogni voce di controllo di accesso assegna un set di diritti a un'entità di sicurezza. Le autorizzazioni possono essere specificate solo su un asset, vale a dire un elemento radice, e si applicano all'asset e ai relativi elementi secondari.

Durante l'anteprima di **Azure Data Catalog** solo il diritto **Read** è supportato nell'elenco delle autorizzazioni per consentire allo scenario di limitare la visibilità a un asset.

Per impostazione predefinita tutti gli utenti autenticati hanno il diritto **Read** per qualsiasi elemento del catalogo, a meno che la visibilità non sia limitata al set di entità nelle autorizzazioni.

## <a name="rest-api"></a>API REST
Le richieste dell'elemento di visualizzazione **PUT** e **POST** possono essere usate per controllare i ruoli e le autorizzazioni: oltre al payload dell'elemento, è possibile specificare due proprietà di sistema, **ruoli** e **autorizzazioni**.

> [!NOTE]
> **permissions** si applicano solo a un elemento radice.
> 
> **Proprietario** si applica solo a un elemento radice.
> 
> Per impostazione predefinita quando viene creato un elemento nel catalogo, il relativo **Collaboratore** è impostato sull'utente attualmente autenticato. Se l'elemento deve poter essere aggiornato da tutti, è necessario che **Collaboratore** sia impostato sull'entità di sicurezza speciale &lt;Tutti&gt; nella proprietà **ruoli** quando l'elemento viene pubblicato per la prima volta. Vedere l'esempio riportato di seguito. **Collaboratore** non può essere modificato e rimane invariato per la durata di un elemento. Neanche **Amministratore** o **Proprietario** hanno il diritto di modificare il ruolo **Collaboratore**. L'unico valore supportato per l'impostazione esplicita di **Collaboratore** è &lt;Tutti&gt;: ad esempio, **Collaboratore** può essere solo un utente che ha creato un elemento o &lt;Tutti&gt;.
> 
> 

### <a name="examples"></a>esempi
**Impostare collaboratore su &lt;Tutti&gt; durante la pubblicazione di un elemento.**
L'entità di sicurezza speciale &lt;Tutti&gt; ha come objectId "00000000-0000-0000-0000-000000000201".
  **POST**: https://api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [!NOTE]
> Alcune implementazioni di client HTTP possono ripetere automaticamente l'invio di richieste in caso di risposta 302 dal server, eliminando in genere le intestazioni di autorizzazione dalla richiesta. Dato che l'intestazione dell'autorizzazione è obbligatoria per l'invio di richieste ad Azure Data Catalog, è necessario assicurarsi che sia ancora disponibile quando si invia di nuovo una richiesta a un percorso di reindirizzamento specificato da Azure Data Catalog. L'esempio di codice seguente offre una dimostrazione usando l'oggetto .NET HttpWebRequest.
> 
> 

**Corpo**

    {
        "roles": [
            {
                "role": "Contributor",
                "members": [
                    {
                        "objectId": "00000000-0000-0000-0000-000000000201"
                    }
                ]
            }
        ]
    }

  **Assegnare i proprietari e limitare la visibilità di un elemento radice esistente**: **PUT** https://api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

    {
        "roles": [
            {
                "role": "Owner",
                "members": [
                    {
                        "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
                        "upn": "user1@contoso.com"
                    },
                    {
                        "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
                        "upn": "user2@contoso.com"
                    }
                ]
            }
        ],
        "permissions": [
            {
                "principal": {
                    "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
                    "upn": "user3@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            },
            {
                "principal": {
                    "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
                    "upn": "user4@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            }
        ]
    }

> [!NOTE]
> In PUT non è necessario specificare un payload dell'elemento nel corpo: PUT consente di aggiornare solo i ruoli e/o le autorizzazioni.
> 
> 

<!--Image references-->
[1]: ./media/data-catalog-developer-concepts/concept2.png



<!--HONumber=Nov16_HO3-->


