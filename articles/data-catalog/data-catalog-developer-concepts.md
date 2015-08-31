<properties
   pageTitle="Concetti per sviluppatori del Catalogo dati di Azure"
   description="Concetti per sviluppatori del Catalogo dati di Azure: catalogo, utenti, risorse, crowdsourcing."
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
   ms.date="07/13/2015"
   ms.author="derrickv"/>

# Concetti per sviluppatori del Catalogo dati di Azure

**Catalogo dati di Microsoft Azure** è un servizio cloud completamente gestito che offre funzionalità di individuazione dell’origine dati e di crowdsourcing dei metadati dell'origine dati. Gli sviluppatori possono utilizzare il servizio tramite le API REST. La comprensione dei concetti implementati nel servizio è importante per gli sviluppatori al fine di una perfetta integrazione con il **Catalogo dati di Azure**.

## Concetti chiave

Il modello concettuale del **Catalogo dati di Azure** è basato su quattro concetti chiave: il **catalogo**, gli **utenti**, gli **asset** e le **annotazioni**.

![concetto][1]

*Figura 1. Modello concettuale semplificato del Catalogo dati di Azure*

### Catalogo

Il **catalogo** è il contenitore di primo livello per tutti i metadati archiviati da un'organizzazione. Per ogni account Azure è consentito un solo **catalogo**. I cataloghi sono legati a una sottoscrizione di Azure, ma solo un **catalogo** può essere creato per un determinato account Azure, anche se un account può avere più sottoscrizioni.

Un catalogo contiene **utenti** e **asset**.

### Utenti

Gli utenti sono entità di sicurezza che dispongono delle autorizzazioni per eseguire azioni nel catalogo (ricercare nel catalogo, aggiungere, modificare o rimuovere elementi e così via).

Diversi sono i ruoli disponibili che un utente può avere. Per ulteriori informazioni sui ruoli, vedere la sezione Ruoli e autorizzazione.

È possibile aggiungere solo singoli utenti (non gruppi di sicurezza).

Il Catalogo dati di Azure utilizza Azure Active Directory per la gestione delle identità e degli accessi. Ogni utente del catalogo deve essere un membro di Active Directory per l'account.

### Asset

Il **catalogo** contiene gli asset di dati. Gli **asset** rappresentano l'unità di granularità gestita dal catalogo.

La granularità di un asset varia a seconda dell'origine dati. Per Database Oracle o SQL Server un asset può essere una tabella o una vista. Per SQL Server Analysis Services un asset può essere una misura, una dimensione o un indicatore di prestazioni chiave (KPI). Per SQL Server Reporting Services un asset è un report.

L’**asset** è l'elemento che si aggiunge o si rimuove da un catalogo. È l'unità di risultato ottenuto dalla **ricerca**.

L’**asset** è composto dal relativo nome, percorso e tipo, nonché dalle annotazioni che lo descrivono ulteriormente.

### Annotazioni

Le annotazioni sono elementi che rappresentano i metadati relativi agli asset.

Esempi di annotazione sono la descrizione, i tag, lo schema, la documentazione e così via. Un elenco completo dei tipi di asset e dei tipi di annotazione è disponibile nella sezione del modello a oggetti asset.

## Annotazioni crowdsourcing e prospettiva dell'utente (molteplicità di opinione)

Un aspetto fondamentale del Catalogo dati di Azure è il supporto crowdsourcing dei metadati nel sistema. A differenza dell’approccio wiki, dove è presente una sola opinione e l'ultima scrittura prevale, il modello Catalogo dati di Azure consente a più opinioni di convivere affiancate nel sistema.

Questo approccio riflette il mondo reale dei dati aziendali in cui diversi utenti possono avere diverse prospettive su un determinato asset:

-	Un amministratore del database può fornire informazioni sui contratti di servizio o la finestra di elaborazione disponibile per le operazioni ETL in blocco
-	Un amministratore dei dati può fornire informazioni sui processi aziendali a cui si applica l'asset o le classificazioni a cui è stato applicato dall’azienda
-	Un analista finanziario può fornire informazioni sull'utilizzo dei dati durante le attività di reporting di fine periodo

Per supportare questo esempio, ogni utente (l'amministratore del database, l'amministratore dei dati e l’analista) può aggiungere una descrizione a una singola tabella che è stata registrata nel catalogo. Tutte le descrizioni vengono gestite nel sistema e nel portale del Catalogo dati di Azure vengono visualizzate tutte le descrizioni.

Questo modello viene applicato alla maggior parte degli elementi del modello a oggetti. Questo è il motivo per i tipi di payload JSON sono spesso matrici per le proprietà di cui è possibile prevedere un singleton.

Ad esempio, sotto la radice dell'asset è disponibile una matrice di oggetti descrizione. La proprietà della matrice è denominata "descriptions". Un oggetto descrizione ha tre proprietà: description, tags e friendlyName. Il modello è che un utente che digita una o più di queste proprietà ottiene un oggetto descrizione creato per i valori forniti dall'utente.

L'esperienza utente può quindi scegliere come visualizzare la combinazione. Esistono tre diversi modelli per la visualizzazione.

-	Il modello più semplice è "Mostra tutto". In questo modello vengono visualizzati tutti gli oggetti in una sorta di visualizzazione elenco. Ciò è quanto l’esperienza utente esegue nel portale del Catalogo dati di Azure per la descrizione.
-	Un altro modello è "Merge". In questo modello, tutti i valori dagli utenti diversi vengono uniti e i duplicati vengono rimossi. Esempi di questo modello nell’esperienza utente nel portale del Catalogo dati di Azure sono le proprietà di tag ed esperti.
-	Un terzo modello è "ultima scrittura prevale". In questo modello viene visualizzato solo il valore digitato più di recente. friendlyName è un esempio di questo modello.

## Modello a oggetti asset

Come descritto nella sezione dei concetti chiave, il modello a oggetti del **Catalogo dati di Azure** include elementi che possono essere asset o annotazioni. Gli elementi dispongono di proprietà che possono essere obbligatorie o facoltativie. Alcune proprietà si applicano a tutti gli elementi. Alcune proprietà si applicano a tutti gli asset. Alcune proprietà si applicano solo a tipi di asset specifici.

### Proprietà comuni

Queste proprietà si applicano a tutti i tipi di asset radice e a tutti i tipi di annotazione.

> [AZURE.NOTE]Le proprietà i cui nomi iniziano con un doppio carattere di sottolineatura sono tipi di sistema.

<table><tr><td><b>Nome proprietà</b></td><td><b>Tipo di dati</b></td><td><b>Commenti</b></td></tr><tr><td>modifiedTime</td><td>DateTime</td><td>Data e ora dell'ultima modifica apportata alla radice. Viene impostato dal client. Il server non gestisce questo valore.</td></tr><tr><td>__id</td><td>Guid</td><td>ID dell'elemento (sola lettura). Questo ID è univoco per l'asset. La chiave per l'elemento è quindi  __Id, __id della radice. Questa tupla è univoca all'interno di una directory.</td></tr><tr><td>__typeId</td><td>Guid</td><td>Tipo di asset (sola lettura).</td></tr><tr><td>__creatorId</td><td>Stringa</td><td>Stringa utilizzata dall'autore dell'asset per identificare in modo univoco l'asset. </td></tr></table>

### Proprietà radice comuni

Queste proprietà si applicano a tutti i tipi di asset radice.

<table><tr><td><b>Nome proprietà</b></td><td><b>Tipo di dati</b></td><td><b>Commenti</b></td></tr><tr><td>name</td><td>Stringa</td><td>Nome derivato dalle informazioni del percorso di origine dati.</td></tr><tr><td>dsl</td><td>Percorso dell'origine dati</td><td>Descrive in modo univoco l'origine dati ed è uno degli identificatori per l’asset. Vedere la sezione relativa all’identità doppia. La struttura del percorso dell’origine dati varia in base al tipo di origine.</td></tr><tr><td>dataSource</td><td>DataSourceInfo</td><td>Ulteriori dettagli sul tipo di asset.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Descrive l'utente che ha registrato l’asset più di recente. Contiene l'ID univoco per l'utente (upn) e un nome visualizzato (lastName e firstName).</td></tr><tr><td>lastRegisteredTime</td><td>dateTime</td><td>Data e ora dell'ultima registrazione dell’asset nel catalogo.</td></tr></table>

### Tipi di asset radice

I tipi di asset radice rappresentano i diversi tipi di asset di dati che possono essere registrati nel catalogo.

<table><tr><td><b>Tipo di asset</b></td><td><b>Proprietà aggiuntive</b></td><td><b>Tipo di dati</b></td><td><b>Commenti</b></td></tr><tr><td>Tabella</td><td></td><td></td><td>Tabella che rappresenta i dati tabulari. Può trattarsi di una tabella SQL, vista SQL, tabella tabulare di Analysis Services, dimensione multidimensionale di Analysis Services, tabella Oracle e così via.   </td></tr><tr><td>Measure</td><td></td><td></td><td>Tipo che rappresenta una misura di Analysis Services.</td></tr><tr><td></td><td>Measure</td><td>Colonna</td><td>Metadati che descrivono la misura.</td></tr><tr><td></td><td>isCalculated </td><td>Boolean</td><td>Specifica se la misura viene calcolata o meno.</td></tr><tr><td></td><td>measureGroup</td><td>Stringa</td><td>Contenitore fisico per la misura</td></tr><tr><td>KPI</td><td></td><td></td><td>Tipo che rappresenta un indicatore di prestazioni chiave di Analysis Services.</td></tr><tr><td></td><td>goalExpression</td><td>Stringa</td><td>Espressione numerica MDX o calcolo che restituisce il valore di destinazione dell'indicatore KPI.</td></tr><tr><td></td><td>valueExpression</td><td>Stringa</td><td>Espressione numerica MDX che restituisce il valore effettivo dell'indicatore KPI.</td></tr><tr><td></td><td>statusExpression</td><td>Stringa</td><td>Espressione MDX che rappresenta lo stato dell'indicatore KPI in un punto specifico nel tempo.</td></tr><tr><td></td><td>trendExpression</td><td>Stringa</td><td>Espressione MDX che restituisce il valore dell'indicatore KPI nel tempo. La tendenza può essere un qualsiasi criterio basato sul tempo utile in un contesto aziendale specifico.</td></tr><tr><td></td><td>measureGroup</td><td>Stringa</td><td>Contenitore fisico per la misura.</td></tr><tr><td>Report</td><td></td><td></td><td>Tipo che rappresenta un report di SQL Server Reporting Services. </td></tr><tr><td></td><td>CreatedBy</td><td>Stringa</td><td></td></tr><tr><td></td><td>CreatedDate</td><td>Stringa</td><td></td></tr></table>

### Tipi di annotazione

I tipi di annotazione sono tipi di metadati che possono essere assegnati ad altri tipi all'interno del catalogo.

<table><tr><td><b>Tipo di annotazione</b></td><td><b>Proprietà aggiuntive</b></td><td><b>Tipo di dati</b></td><td><b>Commenti</b></td></tr><tr><td>Descrizione</td><td></td><td></td><td>Ogni utente del sistema può aggiungere i tag e la descrizione. Solo tale utente può modificare l'oggetto descrizione. Gli amministratori e i proprietari di asset possono eliminare l'oggetto descrizione ma non modificarlo. Il sistema mantiene separati questi elementi. Pertanto è disponibile una matrice di descrizioni in ogni asset (una per ogni utente che ha contribuito con le proprie conoscenze sull'asset, oltre a probabilmente una contenente le informazioni derivate dall'origine dati).</td></tr><tr><td></td><td>friendlyName</td><td>stringa</td><td>Nome descrittivo che può essere utilizzato al posto del nome derivato dall'origine dati. Ciò è utile per la visualizzazione e per la ricerca.</td></tr><tr><td></td><td>tags</td><td>string[]</td><td>Matrice di tag per l'asset.</td></tr><tr><td></td><td>description</td><td>stringa</td><td>Breve descrizione (2-3 righe) dell'asset.</td></tr><tr><td>Schema</td><td></td><td></td><td>Schema che descrive la struttura dei dati. Elenca i nomi e i tipi di attributo (ad esempio colonna, attributo, campo, ecc.) e altri metadati. Queste informazioni sono derivate dall'origine dati. In genere è presente un solo elemento di schema in un asset.</td></tr><tr><td></td><td>columns</td><td>Column[]</td><td>Matrice di oggetti colonna. Descrivono la colonna con le informazioni derivate dall'origine dati.</td></tr><tr><td>SchemaDescription</td><td></td><td></td><td>Contiene una descrizione e un set di tag per ogni attributo definito nello schema. Ogni utente del sistema può aggiungere i tag e la descrizione. Solo tale utente può modificare l'oggetto descrizione. Gli amministratori e i proprietari di asset possono eliminare l'oggetto SchemaDescription ma non modificarlo. Il sistema mantiene separati questi elementi. Pertanto è disponibile una matrice di oggetti SchemaDescription in ogni asset (una per ogni utente che ha contribuito con le proprie conoscenze sugli attributi, oltre a probabilmente una contenente le informazioni derivate dall'origine dati). SchemaAttributes è associato in modo generico allo schema e quindi potrebbe non essere sincronizzato, ossia SchemaDescription potrebbe descrivere colonne che non sono più presenti nello schema o non riuscire a creare il riferimento a una nuova colonna è stato aggiunta di recente. Spetta al writer tenere gli elementi sincronizzati. L'origine dati potrebbe avere anche le informazioni di descrizione. Si tratta di un oggetto schemaDescription aggiuntivo che verrebbe creato quando si esegue lo strumento.</td></tr><tr><td></td><td>columnDescriptions</td><td>ColumnDescription[]</td><td>Matrice di ColumnDescription che descrivono le colonne nello schema. </td></tr><tr><td>Esperto</td><td></td><td></td><td>Contiene l’elenco degli utenti considerati esperti nel set di dati. Le opinioni degli esperti (ad esempio le descrizioni) verranno propagate all'inizio dell'esperienza utente quando si elencano le descrizioni. Ogni utente può specificare il proprio elenco di esperti. Solo tale utente può modificare l'oggetto esperti. Gli amministratori e i proprietari di asset possono eliminare l'oggetto Experts ma non modificarlo.</td></tr><tr><td></td><td>esperti</td><td>string[]</td><td>Matrice di indirizzi e-mail.</td></tr><tr><td>Anteprima</td><td></td><td></td><td>L'anteprima contiene uno snapshot delle prime 20 righe di dati per l'asset. L’anteprima ha senso solo per alcuni tipi di asset (ad esempio per la tabella ma non per la misura).</td></tr><tr><td></td><td>preview</td><td>object[]</td><td>Matrice di oggetti che rappresentano una colonna. Ogni oggetto ha un mapping di proprietà a una colonna con un valore della colonna per la riga.</td></tr>
<tr><td>AccessInstruction</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>stringa</td><td>Il tipo mime del contenuto.</td></tr>
<tr><td></td><td>content</td><td>stringa</td><td>Istruzioni su come ottenere l'accesso a questa risorsa di dati. Potrebbe trattarsi di un URL, un indirizzo di posta elettronica o un set di istruzioni.</td></tr>

</table>

### Tipi comuni

I tipi comuni possono essere utilizzati come tipi per proprietà, ma non sono elementi.

<table><tr><td><b>Tipo comune</b></td><td><b>Proprietà</b></td><td><b>Tipo di dati</b></td><td><b>Commenti</b></td></tr><tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr><tr><td></td><td>sourceType</td><td>stringa</td><td>Descrive il tipo di origine dati, ad esempio SQL Server, Database Oracle e così via.  </td></tr><tr><td></td><td>objectType</td><td>stringa</td><td>Descrive il tipo di oggetto nell'origine dati, ad esempio tabella, vista per SQL Server.</td></tr><tr><td></td><td>formatType</td><td>stringa</td><td>Descrive la struttura dei dati. I valori correnti sono strutturati o non strutturati.</td></tr><tr><td>SecurityPrincipal</td><td></td><td></td><td></td></tr><tr><td></td><td>upn</td><td>stringa</td><td>Indirizzo e-mail univoco dell'utente.</td></tr><tr><td></td><td>firstName</td><td>stringa</td><td>Nome dell'utente (per scopi di visualizzazione).</td></tr><tr><td></td><td>lastName</td><td>stringa</td><td>Cognome dell'utente (per scopi di visualizzazione).</td></tr><tr><td>Colonna</td><td></td><td></td><td></td></tr><tr><td></td><td>name</td><td>stringa</td><td>Nome della colonna o dell'attributo.</td></tr><tr><td></td><td>type</td><td>stringa</td><td>Tipo di dati della colonna o dell'attributo. I tipi consentiti dipendono dal sourceType dei dati dell'asset. È supportato un solo subset di tipi.</td></tr><tr><td></td><td>maxLength</td><td>int</td><td>Lunghezza massima consentita per la colonna o l’attributo. Derivata dall'origine dati. Valida solo per alcuni tipi di origine.</td></tr><tr><td></td><td>Precision</td><td>byte</td><td>Precisione della colonna o dell’attributo. Derivata dall'origine dati. Valida solo per alcuni tipi di origine.</td></tr><tr><td></td><td>isNullable</td><td>Boolean</td><td>Verifica se la colonna può avere un valore null o meno. Derivata dall'origine dati. Valida solo per alcuni tipi di origine.</td></tr><tr><td></td><td>expression</td><td>stringa</td><td>Se il valore è una colonna calcolata, questo campo contiene l'espressione che esprime il valore. Derivata dall'origine dati. Valida solo per alcuni tipi di origine.</td></tr><tr><td></td><td>defaultValue</td><td>oggetto</td><td>Valore predefinito inserito se non è specificato nell'istruzione insert per l'oggetto. Derivata dall'origine dati. Valida solo per alcuni tipi di origine.</td></tr><tr><td>ColumnDescription</td><td></td><td></td><td></td></tr><tr><td></td><td>tags</td><td>string[]</td><td>Matrice di tag che descrivono la colonna.</td></tr><tr><td></td><td>description</td><td>stringa</td><td>Descrizione della colonna.</td></tr><tr><td></td><td>columnName</td><td>stringa</td><td>Nome della colonna a cui fanno riferimento queste informazioni.</td></tr>
</table>

## Ruoli e autorizzazione

Catalogo dati di Microsoft Azure offre funzionalità di autorizzazione per le operazioni CRUD su asset e annotazioni.

## Concetti chiave

Catalogo dati di Azure utilizza due meccanismi di autorizzazione:

- Autorizzazione basata sui ruoli
- Autorizzazione basata sulle autorizzazioni

### Ruoli

Sono disponibili 3 ruoli: **amministratore**, **proprietario** e **collaboratore**. Ogni ruolo ha un ambito e dei diritti che sono riepilogati nella tabella seguente.

<table><tr><td><b>Ruolo</b></td><td><b>Ambito</b></td><td><b>Diritti</b></td></tr><tr><td>Amministratore</td><td>Catalogo (ad esempio tutti gli asset o le annotazioni del catalogo)</td><td>Read Delete ViewRoles ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Proprietario</td><td>Ogni asset (noto anche come elemento radice)</td><td>Read Delete ViewRoles ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Collaboratore</td><td>Ogni singolo asset e annotazione</td><td>Read Update Delete ViewRoles Nota: tutti i diritti vengono revocati se il diritto Read sull'elemento viene revocato dall'autore</td></tr></table>

> [AZURE.NOTE]I diritti **Read**, **Update**, **Delete**, **ViewRoles** sono applicabili a qualsiasi elemento (asset o annotazione) mentre **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility**, **ViewPermissions** sono applicabili solo all’asset radice.
>
>Il diritto **Delete** si applica a un elemento, nonché agli eventuali elementi secondari o al singolo elemento sottostante. Ad esempio, l'eliminazione di un asset comporta l’eliminazione delle annotazioni dell’asset.

### Autorizzazioni

L'autorizzazione è come un elenco di voci di controllo di accesso. Ogni voce di controllo di accesso assegna un set di diritti a un'entità di sicurezza. Le autorizzazioni possono essere specificate solo su un asset (ad esempio, un elemento radice) ed essere applicato all’asset e relativi elementi secondari.

Durante l’anteprima del **Catalogo dati di Azure** solo il diritto **Read** è supportato nell'elenco delle autorizzazioni per consentire allo scenario di limitare la visibilità a un asset.

Per impostazione predefinita tutti gli utenti autenticati hanno il diritto **Read** per qualsiasi elemento del catalogo, a meno che la visibilità non sia limitata al set di entità nelle autorizzazioni.

## API REST

Le richieste di elemento di visualizzazione **PUT** e **POST** possono essere utilizzate per controllare i ruoli e le autorizzazioni: oltre alle due proprietà di sistema di payload dell’elemento è possibile specificare **\_ruoli** e **\_autorizzazioni**.

> [AZURE.NOTE]
>
> **__**\_autorizzazioni** si applica solo a un elemento radice.
>
> Il ruolo **Proprietario** si applica solo a un elemento radice.
>
> Per impostazione predefinita quando viene creato un elemento nel catalogo, il relativo**Collaboratore** è impostato sull'utente attualmente autenticato. Se l'elemento deve essere aggiornabile da tutti, **Collaboratore**deve essere impostato sull’entità di sicurezza speciale <Everyone> nella proprietà **\_ruoli** quando è il primo elemento pubblicato (vedere l'esempio riportato di seguito). **Collaboratore** non può essere modificato e rimane invariato durante la durata di un elemento (ad esempio neanche l’**Amministratore** o il **Proprietario** hanno il diritto di modificare il **Collaboratore**). L'unico valore supportato per l'impostazione esplicita di **Collaboratore** è <Everyone>: ad esempio, **Collaboratore** può essere solo un utente che ha creato un elemento o <Everyone>.

###esempi
**Impostare collaboratore<Everyone>durante la pubblicazione di un elemento.** Entità di sicurezza speciale <Everyone> ha come objectId "00000000-0000-0000-0000-000000000201". **POST**https://123154bb...6aad6370ee14.datacatalog.azure.com/default/views/tables/?api-version=2015-07.1.0-Preview**Corpo**

	{
	    "__roles": [
	        {
	            "role": "Contributor",
	            "members": [
	                {
	                    "objectId": "00000000-0000-0000-0000-000000000201"
	                }
	            ]
	        }
	    ],
	    … other table properties
	}

**Assegnare i proprietari e limitare la visibilità di un elemento radice esistente****PUT**https://123154bb...6aad6370ee14.datacatalog.azure.com/default/views/tables/042297b0...1be45ecd462a?api-version=2015-07.1.0-Preview

	{
	    "__roles": [
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
	    "__permissions": [
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

> [AZURE.NOTE]In PUT non è necessario specificare un payload dell'elemento nel corpo: PUT consente di aggiornare solo i ruoli e/o le autorizzazioni.

<!--Image references-->
[1]: ./media/data-catalog-developer-concepts/concept.png

<!---HONumber=August15_HO8-->