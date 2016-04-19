<properties
   pageTitle="Concetti per sviluppatori di Azure Data Catalog | Microsoft Azure"
   description="Questo articolo fornisce un'introduzione ai concetti chiave nel modello concettuale di Azure Data Catalog, come esposto con l'API REST di Azure Data Catalog."
   services="data-catalog"
   documentationCenter=""
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags 
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# Concetti per sviluppatori del Catalogo dati di Azure

**Catalogo dati di Microsoft Azure** è un servizio cloud completamente gestito che offre funzionalità di individuazione dell’origine dati e di crowdsourcing dei metadati dell'origine dati. Gli sviluppatori possono usare il servizio tramite le API REST. La comprensione dei concetti implementati nel servizio è importante per gli sviluppatori al fine di una perfetta integrazione con il **Catalogo dati di Azure**.

## Concetti chiave

Il modello concettuale del **Catalogo dati di Azure** è basato su quattro concetti chiave: il **catalogo**, gli **utenti**, gli **asset** e le **annotazioni**.

![](media/data-catalog-developer-concepts/concept2.png)

*Figura 1. Modello concettuale semplificato del Catalogo dati di Azure*

### Catalogo

Il **catalogo** è il contenitore di primo livello per tutti i metadati archiviati da un'organizzazione. Per ogni account Azure è consentito un solo **catalogo**. I cataloghi sono legati a una sottoscrizione di Azure, ma solo un **catalogo** può essere creato per un determinato account Azure, anche se un account può avere più sottoscrizioni.

Un catalogo contiene **utenti** e **asset**.

### Utenti

Gli utenti sono entità di sicurezza che dispongono delle autorizzazioni per eseguire azioni nel catalogo (ricercare nel catalogo, aggiungere, modificare o rimuovere elementi e così via).

Diversi sono i ruoli disponibili che un utente può avere. Per altre informazioni sui ruoli, vedere la sezione Ruoli e autorizzazione.

È possibile aggiungere singoli utenti e gruppi di sicurezza.

Il Catalogo dati di Azure usa Azure Active Directory per la gestione delle identità e degli accessi. Ogni utente del catalogo deve essere un membro di Active Directory per l'account.

### Asset

Il **catalogo** contiene gli asset di dati. Gli **asset** rappresentano l'unità di granularità gestita dal catalogo.

La granularità di un asset varia a seconda dell'origine dati. Per Database Oracle o SQL Server un asset può essere una tabella o una vista. Per SQL Server Analysis Services un asset può essere una misura, una dimensione o un indicatore di prestazioni chiave (KPI). Per SQL Server Reporting Services un asset è un report.

L’**asset** è l'elemento che si aggiunge o si rimuove da un catalogo. È l'unità di risultato ottenuto dalla **ricerca**.

L’**asset** è composto dal relativo nome, percorso e tipo, nonché dalle annotazioni che lo descrivono ulteriormente.

### Annotazioni

Le annotazioni sono elementi che rappresentano i metadati relativi agli asset.

Tra gli esempi di annotazioni ci sono descrizioni, tag, schemi e documentazione. Un elenco completo dei tipi di annotazione e di asset è disponibile nella sezione seguente [Modello a oggetti asset](#asset-model).

## Annotazioni crowdsourcing e prospettiva dell'utente (molteplicità di opinione)

Un aspetto fondamentale del Catalogo dati di Azure è il supporto crowdsourcing dei metadati nel sistema. A differenza dell’approccio wiki, dove è presente una sola opinione e l'ultima scrittura prevale, il modello Catalogo dati di Azure consente a più opinioni di convivere affiancate nel sistema.

Questo approccio riflette il mondo reale dei dati aziendali in cui diversi utenti possono avere diverse prospettive su un determinato asset:

-	Un amministratore del database può fornire informazioni sui contratti di servizio o la finestra di elaborazione disponibile per le operazioni ETL in blocco
-	Un amministratore dei dati può fornire informazioni sui processi aziendali a cui si applica l'asset o le classificazioni a cui è stato applicato dall’azienda
-	Un analista finanziario può fornire informazioni sull'uso dei dati durante le attività di reporting di fine periodo

Per supportare questo esempio, ogni utente (l'amministratore del database, l'amministratore dei dati e l’analista) può aggiungere una descrizione a una singola tabella che è stata registrata nel catalogo. Tutte le descrizioni vengono gestite nel sistema e nel portale del Catalogo dati di Azure vengono visualizzate tutte le descrizioni.

Questo modello viene applicato alla maggior parte degli elementi del modello a oggetti. Questo è il motivo per i tipi di payload JSON sono spesso matrici per le proprietà di cui è possibile prevedere un singleton.

Ad esempio, sotto la radice dell'asset è disponibile una matrice di oggetti descrizione. La proprietà della matrice è denominata "descriptions". Un oggetto descrizione ha una proprietà, description. In base al modello, ogni utente che digita una descrizione ottiene un oggetto descrizione creato per il valore fornito dall'utente.

L'esperienza utente può quindi scegliere come visualizzare la combinazione. Esistono tre diversi modelli per la visualizzazione.

-	Il modello più semplice è "Mostra tutto". In questo modello vengono visualizzati tutti gli oggetti in una sorta di visualizzazione elenco. Ciò è quanto l’esperienza utente esegue nel portale del Catalogo dati di Azure per la descrizione.
-	Un altro modello è "Merge". In questo modello tutti i valori dai diversi utenti vengono uniti e i duplicati vengono rimossi. Esempi di questo modello nell’esperienza utente nel portale del Catalogo dati di Azure sono le proprietà di tag ed esperti.
-	Un terzo modello è "ultima scrittura prevale". In questo modello viene visualizzato solo il valore digitato più di recente. friendlyName è un esempio di questo modello.

<a name="asset-model"/>
## Modello a oggetti asset

Come descritto nella sezione dei concetti chiave, il modello a oggetti del **Catalogo dati di Azure** include elementi che possono essere asset o annotazioni. Gli elementi dispongono di proprietà che possono essere obbligatorie o facoltative. Alcune proprietà si applicano a tutti gli elementi. Alcune proprietà si applicano a tutti gli asset. Alcune proprietà si applicano solo a tipi di asset specifici.

### Proprietà di sistema

<table><tr><td><b>Nome proprietà</b></td><td><b>Tipo di dati</b></td><td><b>Commenti</b></td></tr><tr><td>timestamp</td><td>DateTime</td><td>Data e ora dell'ultima modifica apportata all'elemento. Questo valore viene generato dal server quando viene inserito e ogni volta che viene aggiornato un elemento. Il valore di questa proprietà viene ignorato durante l'input di operazioni di pubblicazione.</td></tr><tr><td>id</td><td>Uri</td><td>URL assoluto dell'elemento (sola lettura). Si tratta dell'URI indirizzabile univoco per l'elemento. Il valore di questa proprietà viene ignorato durante l'input di operazioni di pubblicazione.</td></tr><tr><td>type</td><td>Stringa</td><td>Tipo di asset (sola lettura).</td></tr><tr><td>etag</td><td>Stringa</td><td>Stringa che corrisponde alla versione dell'elemento che è possibile usare per il controllo della concorrenza ottimistica quando si eseguono operazioni che aggiornano gli elementi nel catalogo. È possibile usare "*" per cercare corrispondenze per qualsiasi valore.</td></tr></table>

### Proprietà comuni

Queste proprietà si applicano a tutti i tipi di asset radice e a tutti i tipi di annotazione.

<table>
<tr><td><b>Nome proprietà</b></td><td><b>Tipo di dati</b></td><td><b>Commenti</b></td></tr>
<tr><td>fromSourceSystem</td><td>Boolean</td><td>Indica se i dati dell'elemento sono derivati da un sistema di origine, ad esempio un database SQL Server o un database Oracle, o creati da un utente.</td></tr>
</table>

### Proprietà radice comuni
<p>
Queste proprietà si applicano a tutti i tipi di asset radice.
<table><tr><td><b>Nome proprietà</b></td><td><b>Tipo di dati</b></td><td><b>Commenti</b></td></tr><tr><td>name</td><td>String</td><td>Nome derivato dalle informazioni del percorso di origine dati.</td></tr><tr><td>dsl</td><td>Percorso dell'origine dati</td><td>Descrive in modo univoco l'origine dati ed è uno degli identificatori per l’asset. Vedere la sezione relativa all’identità doppia. La struttura del percorso dell’origine dati varia in base al tipo di origine.</td></tr><tr><td>dataSource</td><td>DataSourceInfo</td><td>Ulteriori dettagli sul tipo di asset.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Descrive l'utente che ha registrato l’asset più di recente. Contiene l'ID univoco per l'utente (upn) e un nome visualizzato (lastName e firstName).</td></tr><tr><td>containerId</td><td>String</td><td>ID dell'asset di contenitore per l'origine dati. Questa proprietà non è supportata per il tipo di contenitore.</td></tr></table>

### Proprietà comuni di annotazione non singleton

Queste proprietà si applicano a tutti i tipi di annotazione non singleton, ad esempio le annotazioni di cui sono consentite più occorrenze per ogni asset.

<table>
<tr><td><b>Nome proprietà</b></td><td><b>Tipo di dati</b></td><td><b>Commenti</b></td></tr>
<tr><td>key</td><td>Stringa</td><td>Chiave specificata dall'utente che identifica in modo univoco l'annotazione nella raccolta corrente. La lunghezza della chiave non può superare i 256 caratteri.</td></tr>
</table>

### Tipi di asset radice

I tipi di asset radice rappresentano i diversi tipi di asset di dati che possono essere registrati nel catalogo.

<table><tr><td><b>Tipo di asset</b></td><td><b>Proprietà aggiuntive</b></td><td><b>Tipo di dati</b></td><td><b>Annotazioni consentite</b></td><td><b>Commenti</b></td></tr><tr><td>Tabella</td><td></td><td></td><td>Descrizione<p>FriendlyName<p>Tag<p>Schema<p>ColumnDescription<p>ColumnTag<p> Esperto<p>Preview<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>Documentazione<p></td><td>Tabella che rappresenta i dati tabulari. Può trattarsi di una tabella SQL, vista SQL, tabella tabulare di Analysis Services, dimensione multidimensionale di Analysis Services, tabella Oracle e così via.   </td></tr><tr><td>Measure</td><td></td><td></td><td>Descrizione<p>FriendlyName<p>Tag<p>Esperto<p>AccessInstruction<p>Documentazione<p></td><td>Tipo che rappresenta una misura di Analysis Services.</td></tr><tr><td></td><td>Misura</td><td>Colonna</td><td></td><td>Metadati che descrivono la misura.</td></tr><tr><td></td><td>isCalculated </td><td>Boolean</td><td></td><td>Specifica se la misura viene calcolata o meno.</td></tr><tr><td></td><td>measureGroup</td><td>String</td><td></td><td>Contenitore fisico per la misura.</td></tr><td>KPI</td><td></td><td></td><td>Descrizione<p>FriendlyName<p>Tag<p>Esperto<p>AccessInstruction<p>Documentazione</td><td></td></tr><tr><td></td><td>measureGroup</td><td>String</td><td></td><td>Contenitore fisico per la misura.</td></tr><tr><td></td><td>goalExpression</td><td>String</td><td></td><td>Espressione numerica MDX o calcolo che restituisce il valore di destinazione dell'indicatore KPI.</td></tr><tr><td></td><td>valueExpression</td><td>String</td><td></td><td>Espressione numerica MDX che restituisce il valore effettivo dell'indicatore KPI.</td></tr><tr><td></td><td>statusExpression</td><td>String</td><td></td><td>Espressione MDX che rappresenta lo stato dell'indicatore KPI in un punto specifico nel tempo.</td></tr><tr><td></td><td>trendExpression</td><td>String</td><td></td><td>Espressione MDX che restituisce il valore dell'indicatore KPI nel tempo. La tendenza può essere un qualsiasi criterio basato sul tempo utile in un contesto aziendale specifico.</td>
<tr><td>Report</td><td></td><td></td><td>Descrizione<p>FriendlyName<p>Tag<p>Esperto<p>AccessInstruction<p>Documentazione<p></td><td>Tipo che rappresenta un report di SQL Server Reporting Services. </td></tr><tr><td></td><td>assetCreatedDate</td><td>Stringa</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>Stringa</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>Stringa</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>Stringa</td><td></td><td></td></tr><tr><td>Contenitore</td><td></td><td></td><td>Descrizione<p>FriendlyName<p>Tag<p>Esperto<p>AccessInstruction<p>Documentazione<p></td><td>Questo tipo rappresenta un contenitore di altri asset, ad esempio un database SQL, un contenitore di BLOB di Azure o un modello di Analysis Services.</td></tr></table>

### Tipi di annotazione

I tipi di annotazione sono tipi di metadati che possono essere assegnati ad altri tipi all'interno del catalogo.

<table>
<tr><td><b>Tipo di annotazione</b></td><td><b>Proprietà aggiuntive</b></td><td><b>Tipo di dati</b></td><td><b>Commenti</b></td></tr>

<tr><td>Descrizione</td><td></td><td></td><td>Contiene una descrizione per un asset. Ogni utente del sistema può aggiungere una descrizione. Solo tale utente può modificare l'oggetto descrizione. Gli amministratori e i proprietari di asset possono eliminare l'oggetto Description ma non modificarlo. Il sistema mantiene separati questi elementi. Pertanto è disponibile una matrice di descrizioni in ogni asset (una per ogni utente che ha contribuito con le proprie conoscenze sull'asset, oltre a probabilmente una contenente le informazioni derivate dall'origine dati).</td></tr>
<tr><td></td><td>description</td><td>string</td><td>Breve descrizione (2-3 righe) dell'asset</td></tr>

<tr><td>Tag</td><td></td><td></td><td>Contiene un tag per un asset. Ogni utente del sistema può aggiungere più tag per un asset. Solo l'utente che ha creato gli oggetti Tag può modificarli. Gli amministratori e i proprietari di asset possono eliminare l'oggetto Tag ma non modificarlo. Il sistema mantiene separati questi elementi. Di conseguenza, è disponibile una matrice di oggetti Tag in ogni asset.</td></tr>
<tr><td></td><td>tag</td><td>string</td><td>Tag che descrive l'asset.</td></tr>

<tr><td>FriendlyName</td><td></td><td></td><td>Contiene un nome descrittivo per un asset. FriendlyName è un'annotazione sigleton. È possibile aggiungere una sola annotazione FriendlyName a un asset. Solo l'utente che ha creato l'oggetto FriendlyName può modificarlo. Gli amministratori e i proprietari di asset possono eliminare l'oggetto FriendlyName ma non modificarlo. Il sistema mantiene separati questi elementi.</td></tr>
<tr><td></td><td>friendlyName</td><td>string</td><td>Nome descrittivo dell'asset.</td></tr>

<tr><td>Schema</td><td></td><td></td><td>Schema che descrive la struttura dei dati. Elenca i nomi e i tipi di attributo (ad esempio colonna, attributo, campo, ecc.) e altri metadati. Queste informazioni sono derivate dall'origine dati. Schema è un'annotazione singleton. È possibile aggiungere una sola annotazione Schema a un asset.</td></tr>
<tr><td></td><td>columns</td><td>Column[]</td><td>Matrice di oggetti colonna. Descrivono la colonna con le informazioni derivate dall'origine dati.</td></tr>

<tr><td>ColumnDescription</td><td></td><td></td><td>Contiene una descrizione per una colonna. Tutti gli utenti del sistema possono aggiungere descrizioni per più colonne, con un massimo di una descrizione per ogni colonna. Solo l'utente che ha creato gli oggetti ColumnDescription può modificarli. Gli amministratori e i proprietari di asset possono eliminare l'oggetto ColumnDescription ma non modificarlo. Il sistema mantiene separati questi elementi. Di conseguenza, è disponibile una matrice di oggetti ColumnDescription in ogni asset (una per colonna per ogni utente che ha contribuito con le proprie conoscenze sulla colonna). Inoltre, è possibile che ce ne sia un'altra che contiene le informazioni derivate dall'origine dati. ColumnDescription è associato in modo generico allo schema, quindi può non essere sincronizzato, ad esempio ColumnDescription potrebbe descrivere una colonna non più presente nello schema. Spetta al writer tenere gli elementi sincronizzati. Anche l'origine dati potrebbe avere informazioni di descrizione della colonna. Si tratta di altri oggetti ColumnDescription che vengono eventualmente creati quando si esegue lo strumento.</td></tr>
<tr><td></td><td>columnName</td><td>Stringa</td><td>Nome della colonna a cui fa riferimento questa descrizione.</td></tr>
<tr><td></td><td>description</td><td>Stringa</td><td>Breve descrizione (2-3 righe) della colonna.</td></tr>

<tr><td>ColumnTag</td><td></td><td></td><td>Contiene un tag per una colonna. Ogni utente del sistema può aggiungere più tag a una determinata colonna e può aggiungere tag a più colonne. Solo l'utente che ha creato gli oggetti ColumnTag può modificarli. Gli amministratori e i proprietari di asset possono eliminare l'oggetto ColumnTag ma non modificarlo. Il sistema mantiene separati questi elementi. Di conseguenza, è disponibile una matrice di oggetti ColumnTag in ogni asset. ColumnTag è associato in modo generico allo schema, quindi può non essere sincronizzato, ad esempio ColumnTag potrebbe descrivere una colonna non più presente nello schema. Spetta al writer tenere gli elementi sincronizzati.</td></tr>
<tr><td></td><td>columnName</td><td>Stringa</td><td>Nome della colonna a cui fa riferimento questo tag.</td></tr>
<tr><td></td><td>tag</td><td>Stringa</td><td>Tag che descrive la colonna.</td></tr>

<tr><td>Esperto</td><td></td><td></td><td>Contiene un utente che viene considerato un esperto nel set di dati. Le opinioni degli esperti (ad esempio le descrizioni) verranno propagate all'inizio dell'esperienza utente quando si elencano le descrizioni. Ogni utente può specificare i propri esperti. Solo tale utente può modificare l'oggetto esperti. Gli amministratori e i proprietari di asset possono eliminare l'oggetto Expert ma non modificarlo.</td></tr>
<tr><td></td><td>esperto</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>Preview</td><td></td><td></td><td>L'anteprima contiene uno snapshot delle prime 20 righe di dati per l'asset. L’anteprima ha senso solo per alcuni tipi di asset (ad esempio per la tabella ma non per la misura).</td></tr>
<tr><td></td><td>preview</td><td>object[]</td><td>Matrice di oggetti che rappresentano una colonna. Ogni oggetto ha un mapping di proprietà a una colonna con un valore della colonna per la riga.</td></tr>

<tr><td>AccessInstruction</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>string</td><td>Il tipo mime del contenuto.</td></tr>
<tr><td></td><td>content</td><td>string</td><td>Istruzioni su come ottenere l'accesso a questa risorsa di dati. Potrebbe trattarsi di un URL, un indirizzo di posta elettronica o un set di istruzioni.</td></tr>

<tr><td>TableDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>Il numero di righe nel set di dati</td></tr>
<tr><td></td><td>size</td><td>long</td><td>La dimensione in byte del set di dati.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>string</td><td>L’ora dell'ultima modifica apportata allo schema</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>string</td><td>L’ora dell'ultima modifica al set di dati (dati aggiunti, modificati o eliminati)</td></tr>

<tr><td>ColumnsDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columns</td></td><td>ColumnDataProfile[]</td><td>Matrice di profili dati della colonna.</td></tr>

<tr><td>Documentazione</td><td></td><td></td><td>Un determinato asset può avere solo una documentazione associata.</td></tr>
<tr><td></td><td>mimeType</td><td>string</td><td>Il tipo mime del contenuto.</td></tr>
<tr><td></td><td>content</td><td>string</td><td>Il contenuto della documentazione.</td></tr>

</table>

### Tipi comuni

I tipi comuni possono essere usati come tipi per proprietà, ma non sono elementi.
<table>
<tr><td><b>Tipo comune</b></td><td><b>Proprietà</b></td><td><b>Tipo di dati</b></td><td><b>Commenti</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>sourceType</td><td>string</td><td>Descrive il tipo di origine dati, ad esempio SQL Server, Database Oracle e così via.  </td></tr>
<tr><td></td><td>objectType</td><td>string</td><td>Descrive il tipo di oggetto nell'origine dati, ad esempio tabella, vista per SQL Server.</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>Si noti che il back-end non esegue alcuna convalida delle proprietà fornite in base ad AAD durante la pubblicazione.</td></tr>
<tr><td></td><td>upn</td><td>string</td><td>Indirizzo e-mail univoco dell'utente. Deve essere specificato se objectId non viene fornito o se è nel contesto della proprietà "lastRegisteredBy". In caso contrario, è facoltativo.</td></tr>
<tr><td></td><td>objectId</td><td>Guid</td><td>Identità AAD del gruppo di utenti o di sicurezza. Facoltativo. Deve essere specificato se upn non viene fornito. In caso contrario, è facoltativo.</td></tr>
<tr><td></td><td>firstName</td><td>string</td><td>Nome dell'utente (per scopi di visualizzazione). Facoltativo. Valido solo nel contesto della proprietà "lastRegisteredBy". Non può essere specificato quando si fornisce l'entità di sicurezza per "ruoli", "autorizzazioni" ed "esperti".</td></tr>
<tr><td></td><td>lastName</td><td>string</td><td>Cognome dell'utente (per scopi di visualizzazione). Facoltativo. Valido solo nel contesto della proprietà "lastRegisteredBy". Non può essere specificato quando si fornisce l'entità di sicurezza per "ruoli", "autorizzazioni" ed "esperti".</td></tr>

<tr><td>Colonna</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>string</td><td>Nome della colonna o dell'attributo.</td></tr>
<tr><td></td><td>type</td><td>string</td><td>Tipo di dati della colonna o dell'attributo. I tipi consentiti dipendono dal sourceType dei dati dell'asset. È supportato un solo subset di tipi.</td></tr>
<tr><td></td><td>maxLength</td><td>int</td><td>Lunghezza massima consentita per la colonna o l’attributo. Derivata dall'origine dati. Valida solo per alcuni tipi di origine.</td></tr>
<tr><td></td><td>precision</td><td>byte</td><td>Precisione della colonna o dell’attributo. Derivata dall'origine dati. Valida solo per alcuni tipi di origine.</td></tr>
<tr><td></td><td>isNullable</td><td>Boolean</td><td>Verifica se la colonna può avere un valore null o meno. Derivata dall'origine dati. Valida solo per alcuni tipi di origine.</td></tr>
<tr><td></td><td>expression</td><td>string</td><td>Se il valore è una colonna calcolata, questo campo contiene l'espressione che esprime il valore. Derivata dall'origine dati. Valida solo per alcuni tipi di origine.</td></tr>

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

## Ruoli e autorizzazione

Catalogo dati di Microsoft Azure offre funzionalità di autorizzazione per le operazioni CRUD su asset e annotazioni.

## Concetti chiave

Catalogo dati di Azure usa due meccanismi di autorizzazione:

- Autorizzazione basata sui ruoli
- Autorizzazione basata sulle autorizzazioni

### Ruoli

Sono disponibili 3 ruoli: **amministratore**, **proprietario** e **collaboratore**. Ogni ruolo ha un ambito e dei diritti che sono riepilogati nella tabella seguente.

<table><tr><td><b>Ruolo</b></td><td><b>Ambito</b></td><td><b>Diritti</b></td></tr><tr><td>Amministratore</td><td>Catalogo (ad esempio tutti gli asset o le annotazioni del catalogo)</td><td>Read Delete ViewRoles ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Proprietario</td><td>Ogni asset (noto anche come elemento radice)</td><td>Read Delete ViewRoles ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Collaboratore</td><td>Ogni singolo asset e annotazione</td><td>Read Update Delete ViewRoles Nota: tutti i diritti vengono revocati se il diritto Read sull'elemento viene revocato dall'autore</td></tr></table>

> [AZURE.NOTE] I diritti **Read**, **Update**, **Delete**, **ViewRoles** sono applicabili a qualsiasi elemento (asset o annotazione) mentre **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility**, **ViewPermissions** sono applicabili solo all’asset radice.
>
>Il diritto **Delete** si applica a un elemento, nonché agli eventuali elementi secondari o al singolo elemento sottostante. Ad esempio, l'eliminazione di un asset comporta l’eliminazione delle annotazioni dell’asset.

### Autorizzazioni

L'autorizzazione è come un elenco di voci di controllo di accesso. Ogni voce di controllo di accesso assegna un set di diritti a un'entità di sicurezza. Le autorizzazioni possono essere specificate solo su un asset (ad esempio, un elemento radice) ed essere applicato all’asset e relativi elementi secondari.

Durante l’anteprima del **Catalogo dati di Azure** solo il diritto **Read** è supportato nell'elenco delle autorizzazioni per consentire allo scenario di limitare la visibilità a un asset.

Per impostazione predefinita tutti gli utenti autenticati hanno il diritto **Read** per qualsiasi elemento del catalogo, a meno che la visibilità non sia limitata al set di entità nelle autorizzazioni.

## API REST

Le richieste dell'elemento di visualizzazione **PUT** e **POST** possono essere usate per controllare i ruoli e le autorizzazioni: oltre al payload dell'elemento, è possibile specificare due proprietà di sistema, **ruoli** e **autorizzazioni**.

> [AZURE.NOTE]
>
> **autorizzazioni** si applica solo a un elemento radice.
>
> Il ruolo **Proprietario** si applica solo a un elemento radice.
>
> Per impostazione predefinita quando viene creato un elemento nel catalogo, il relativo**Collaboratore** è impostato sull'utente attualmente autenticato. Se l'elemento deve poter essere aggiornato da tutti, è necessario che **Collaboratore** sia impostato sull'entità di sicurezza speciale <Everyone> nella proprietà **ruoli** quando l'elemento viene pubblicato (vedere l'esempio riportato di seguito). **Collaboratore** non può essere modificato e rimane invariato durante la durata di un elemento (ad esempio neanche l’**Amministratore** o il **Proprietario** hanno il diritto di modificare il **Collaboratore**). L'unico valore supportato per l'impostazione esplicita di **Collaboratore** è <Everyone>: ad esempio, **Collaboratore** può essere solo un utente che ha creato un elemento o <Everyone>.

###esempi
**Impostare collaboratore<Everyone>durante la pubblicazione di un elemento.** Entità di sicurezza speciale <Everyone> ha come objectId "00000000-0000-0000-0000-000000000201". **POST** https://api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [AZURE.NOTE] Alcune implementazioni di client HTTP possono ripetere automaticamente l'invio di richieste in caso di risposta 302 dal server, eliminando in genere le **intestazioni Authorization** dalla richiesta. Poiché l'intestazione Authorization è obbligatoria per inviare richieste al servizio ADC, è necessario assicurarsi che l'intestazione Authorization sia ancora disponibile quando si invia di nuovo una richiesta a un percorso di reindirizzamento specificato dal servizio ADC. Il codice seguente illustra questo concetto usando l'oggetto .NET HttpWebRequest.

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

  **Assegnare i proprietari e limitare la visibilità di un elemento radice esistente** **PUT** https://api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

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

  > [AZURE.NOTE] In PUT non è necessario specificare un payload dell'elemento nel corpo: PUT consente di aggiornare solo i ruoli e/o le autorizzazioni.

<!----HONumber=AcomDC_0406_2016-->