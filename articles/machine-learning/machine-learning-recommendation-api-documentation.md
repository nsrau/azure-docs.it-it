<properties 
	pageTitle="Documentazione relativa all'API Recommendations di Machine Learning | Microsoft Azure" 
	description="Documentazione relativa all'API Recommendations di Azure Machine Learning per un motore di raccomandazione disponibile in Microsoft Azure Marketplace." 
	services="machine-learning" 
	documentationCenter="" 
	authors="AharonGumnik" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/10/2015" 
	ms.author="LuisCa"/>

#Documentazione relativa all'API Recommendations di Azure Machine Learning

Questo documento illustra le API Recommendations di Microsoft Azure Machine Learning.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##1\. Panoramica generale
Questo è un documento di riferimento dell'API. È consigliabile iniziare con il documento "Guida introduttiva per l'API Recommendations di Machine Learning".

L'API Recommendations di Azure Machine Learning può essere suddivisa nei seguenti gruppi logici:

- <ins>Limitazioni</ins>: limitazioni dell'API Recommendations.
- <ins>Informazioni generali</ins>: informazioni su autenticazione, URI del servizio e controllo delle versioni.
- <ins>Modello Basic</ins>: API che consentono di eseguire operazioni di base sul modello (ad esempio, creare, aggiornare ed eliminare un modello).
- <ins>Modello Advanced</ins>: API che consentono di eseguire analisi avanzate sui dati del modello.
- <ins>Modello Business Rules</ins>: API che consentono di gestire regole di business sui risultati delle raccomandazioni relative al modello.
- <ins>Catalog</ins>: API che consentono di eseguire operazioni di base sul catalogo di un modello. Un catalogo contiene informazioni sui metadati relativi agli elementi dei dati di utilizzo.
- <ins>Feature</ins>: API che consentono di ottenere approfondimenti sull'elemento nel catalogo e su come usare queste informazioni per creare raccomandazioni migliori.
- <ins>Usage Data</ins>: API che consentono di eseguire operazioni di base sui dati di utilizzo del modello. Nel form di base i dati di utilizzo sono costituiti da righe che includono coppie di &#60;userId&#62;,&#60;itemId&#62;.
- <ins>Build</ins>: API che consentono di attivare la compilazione di un modello e di eseguire operazioni di base associate alla compilazione. È possibile attivare la compilazione di un modello solo se sono disponibili dati di utilizzo rilevanti.
- <ins>Recommendation</ins>: API che consentono di usare raccomandazioni al termine della compilazione di un modello.
- <ins>User Data</ins>: API che consentono di recuperare informazioni su dati di utilizzo utente.
- <ins>Notifications</ins>: API che consentono di ricevere notifiche per i problemi correlati alle operazioni dell'API. Ad esempio, se i dati di utilizzo vengono segnalati mediante acquisizione dei dati e la maggior parte degli eventi di elaborazione non riesce, viene generata una notifica di errore.

##2\. Limitazioni

- Il numero massimo di modelli per ogni sottoscrizione è 10.
- Il numero massimo di elementi che possono essere inclusi nel catalogo è 100.000.
- Il numero massimo di punti di utilizzo mantenuti è ~5.000.000. I meno recenti saranno eliminati se ne vengono caricati o segnalati di nuovi.
- Le dimensioni massime dei dati che possono essere inviati in POST (ad esempio, importazione dei dati del catalogo o dei dati di utilizzo) è di 200 MB.
- Il numero di transazioni al secondo per la compilazione di un modello di raccomandazione non attivo è di ~2 TPS. Un modello di raccomandazione attivo può includere un massimo di 20 TPS.

##3\. API: informazioni generali

###3\.1. Autenticazione
Seguire le linee guida di Microsoft Azure Marketplace relative all'autenticazione. Marketplace supporta il metodo di autenticazione di base o OAuth.

###3\.2. URI del servizio
L'URI radice del servizio per ogni API Recommendations di Azure Machine Learning è disponibile [qui](https://api.datamarket.azure.com/amla/recommendations/v3/).

L'URI del servizio completo è espresso usando elementi della specifica OData.

###3\.3. Versione dell'API
Ogni chiamata API terminerà con un parametro di query denominato apiVersion che dovrà essere impostato su 1.0.

###3\.4. Gli ID fanno distinzione tra maiuscole e minuscole
Gli ID restituiti da una delle API fanno distinzione tra maiuscole e minuscole e devono essere usati esattamente come sono, quando vengono passati come parametri nelle chiamate API successive. Ad esempio, per gli ID dei modelli e del catalogo viene fatta distinzione tra maiuscole e minuscole.

##4\. Qualità delle raccomandazioni ed elementi ignoti

###4\.1. Qualità delle raccomandazioni

La creazione di un modello di raccomandazione è in genere sufficiente per consentire al sistema di fornire raccomandazioni. La qualità delle raccomandazioni varia tuttavia in base ai dati di utilizzo elaborati e alla copertura del catalogo. Ad esempio, se sono disponibili molti elementi ignoti, ovvero elementi senza utilizzo significativo, il sistema avrà difficoltà a fornire una raccomandazione per tale elemento o a usarlo come elemento raccomandato. Per risolvere il problema degli elementi ignori, il sistema consente l'uso di metadati degli elementi per migliorare la qualità delle raccomandazioni. Questi metadati sono definiti funzionalità. L'autore di un libro o l'attore di un film è un esempio tipico di funzionalità. Le funzionalità vengono fornire tramite il catalogo sotto forma di stringhe chiave/valore. Per il formato completo del file di catalogo, vedere la sezione [Importare i dati del catalogo](#81-import-catalog-data).

###4\.2. Compilazione della classifica

Le funzionalità possono migliorare il modello di raccomandazione, ma ciò richiede l'uso di funzionalità significative. A questo scopo è stata introdotta una nuova compilazione per la definizione della classifica, che consente di classificare l'utilità delle funzionalità. Una funzionalità significativa presenta un punteggio di classificazione minimo pari a 2. Dopo avere individuato le funzionalità significative, attivare una compilazione di raccomandazione con l'elenco, o sottoelenco, delle funzionalità significative. È possibile usare queste funzionalità per il miglioramento sia degli elementi noti che di quelli ignoti. Per usarle per gli elementi noti è necessario configurare il parametro di compilazione `UseFeatureInModel`. Per usarle per gli elementi ignoti è necessario abilitare il parametro di compilazione `AllowColdItemPlacement`. Nota: non è possibile abilitare `AllowColdItemPlacement` se non si abilita anche `UseFeatureInModel`.

###4\.3. Motivazione delle raccomandazioni

La motivazione delle raccomandazioni è un altro aspetto dell'utilizzo delle funzionalità. In effetti, il motore di raccomandazioni di Azure Machine Learning è in grado di usare le funzionalità per fornire una spiegazione (o motivazione) della raccomandazione, favorendo un livello di confidenza maggiore nell'elemento raccomandato da parte del consumer di raccomandazioni. Per abilitare le motivazioni, è necessario configurare i parametri `AllowFeatureCorrelation` e `ReasoningFeatureList` prima di richiedere una compilazione di raccomandazioni.


##5\. Modello Basic

###5\.1. Creare il modello
Crea una richiesta di tipo "crea modello".

| Metodo HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/CreateModel?modelName=%27<model_name>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/CreateModel?modelName=%27MyFirstModel%27&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
|	modelName |	Sono consentiti solo lettere (A-Z, a-z), numeri (0-9), trattini (-) e caratteri di sottolineatura (\_).<br>Lunghezza massima: 20 caratteri | 
| apiVersion | 1.0 | 
||| 
| Corpo della richiesta | NESSUNO |


**Risposta**:

Codice stato HTTP: 200

- `feed/entry/content/properties/id`: contiene l'ID modello. **Nota**: l'ID modello fa distinzione tra maiuscole e minuscole.

XML OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Create A New Model</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-05T06:35:21Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">a658c626-2baa-43a7-ac98-f6ee26120a12</d:Id>
        <d:Name m:type="Edm.String">MyFirstModel</d:Name>
        <d:Date m:type="Edm.String">10/5/2014 6:35:19 AM</d:Date>
        <d:Status m:type="Edm.String">Created</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:UserName>
        <d:Description m:type="Edm.String"></d:Description>
      </m:properties>
    </content>
	  </entry>
	</feed>

###5\.2. Ottenere il modello
Crea una richiesta di tipo "ottieni modello":

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>Esempio:<br>`<rootURI>/GetModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
|	id |	Identificatore univoco del modello (con distinzione tra maiuscole e minuscole).| |
|	apiVersion | 1\.0 |
||| 
| Corpo della richiesta | NESSUNO |

**Risposta**:

Codice stato HTTP: 200

I dati del modello sono disponibili negli elementi seguenti:

- `feed/entry/content/properties/Id`: ID univoco del modello.
- `feed/entry/content/properties/Name`: nome del modello.
- `feed/entry/content/properties/Date`: data di creazione del modello.
- `feed/entry/content/properties/Status`: stato del modello. Uno dei seguenti:
    - Created: il modello viene creato e non contiene dati del catalogo e di utilizzo.
	- ReadyForBuild: il modello viene creato e contiene dati del catalogo e di utilizzo.
- `feed/entry/content/properties/HasActiveBuild`: indica se il modello è stato creato correttamente.
- `feed/entry/content/properties/BuildId`: ID compilazione attiva del modello.
- `feed/entry/content/properties/Mpr`: classificazione percentile media (MPR, Mean Percentile Ranking) del modello. Vedere ModelInsight per altre informazioni.
- `feed/entry/content/properties/UserName`: nome utente interno del modello.

XML OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Get A List Of All Models</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-28T14:35:51Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
        <d:Name m:type="Edm.String">vah-11111</d:Name>
        <d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
        <d:Status m:type="Edm.String">ReadyForBuild</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
		<d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
        <d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
        <d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
        <d:Description m:type="Edm.String">short description</d:Description>
        <d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
      </m:properties>
    </content>
	  </entry>
	</feed>

###5\.3. Ottenere tutti i modelli
Recupera tutti i modelli dell'utente corrente.

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetAllModels?apiVersion=%271.0%27`<br>Esempio:<br>`<rootURI>/GetAllModels?apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
|	apiVersion | 1\.0 |
||| 
| Corpo della richiesta | NESSUNO |

**Risposta**:

Codice stato HTTP: 200

- `feed/entry/content/properties/Id`: ID univoco del modello.
- `feed/entry/content/properties/Name`: nome del modello.
- `feed/entry/content/properties/Date`: data di creazione del modello.
- `feed/entry/content/properties/Status`: stato del modello. Uno dei seguenti:
  - Created: il modello viene creato e non contiene dati del catalogo e di utilizzo.
  - ReadyForBuild: il modello viene creato e contiene dati del catalogo e di utilizzo.
- `feed/entry/content/properties/HasActiveBuild`: indica se il modello è stato creato correttamente.
- `feed/entry/content/properties/BuildId`: ID compilazione attiva del modello.
- `feed/entry/content/properties/Mpr`:MPR del modello. Vedere ModelInsight per altre informazioni.
- `feed/entry/content/properties/UserName`: nome utente interno del modello.
- `feed/entry/content/properties/UsageFileNames`: elenco dei file di dati di utilizzo del modello, separati da virgole.
- `feed/entry/content/properties/CatalogId`: ID catalogo del modello.
- `feed/entry/content/properties/Description`: descrizione del modello.
- `feed/entry/content/properties/CatalogFileName`: nome del file di catalogo del modello.

XML OData


    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get A List Of All Models</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-28T14:35:51Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
					<d:Name m:type="Edm.String">vah-11111</d:Name>
					<d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
					<d:Status m:type="Edm.String">ReadyForBuild</d:Status>
					<d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
					<d:BuildId m:type="Edm.String">-1</d:BuildId>
					<d:Mpr m:type="Edm.String">0</d:Mpr>
					<d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
					<d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
					<d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
					<d:Description m:type="Edm.String">short description</d:Description>
					<d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
				</m:properties>
			</content>
		</entry>
	</feed>

###5\.4. Aggiornare il modello

È possibile aggiornare la descrizione del modello o l'ID compilazione attiva.<br> <ins>ID compilazione attiva</ins>: ogni compilazione per ogni modello ha un ID compilazione. Con il termine ID compilazione attiva si identifica la prima compilazione riuscita di ogni nuovo modello. Se dopo avere ottenuto un ID compilazione attiva si eseguono altre compilazioni per lo stesso modello, è necessario impostarlo in modo esplicito come ID compilazione predefinito. Quando si usano raccomandazioni, se non si specifica l'ID compilazione da usare, verrà applicato automaticamente quello predefinito.<br> Dopo avere implementato un modello di raccomandazione nell'ambiente di produzione, questo meccanismo consente di compilare nuovi modelli e testarli prima di alzarli di livello e passarli in produzione.


| Metodo HTTP | URI |
|:--------|:--------|
|PUT |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br>Esempio:<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
|	id | Identificatore univoco del modello (con distinzione tra maiuscole e minuscole).| |
|	apiVersion | 1\.0 |
||| 
| Corpo della richiesta | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`<Description>New Description</Description>`<br>`<ActiveBuildId>-1</ActiveBuildId>`<br>` </ModelUpdateParams>`<br><br>Si noti che i tag XML Description e ActiveBuildId sono facoltativi. Se non si vuole impostare Description o ActiveBuildId, rimuovere tutto il tag.|

**Risposta**:

Codice stato HTTP: 200

###5\.5. Eliminare il modello
Elimina un modello esistente in base all'ID.

| Metodo HTTP | URI |
|:--------|:--------|
|DELETE |`<rootURI>/DeleteModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>Esempio:<br>`<rootURI>/DeleteModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
|	id |	Identificatore univoco del modello (con distinzione tra maiuscole e minuscole).| |
|	apiVersion | 1\.0 |
||| 
| Corpo della richiesta | NESSUNO |

**Risposta**:

Codice stato HTTP: 200

XML OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Delete Model by Id</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-28T10:39:33Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">DeleteModelByIdEntity</title>
    <updated>2014-10-28T10:39:33Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
		<d:string m:type="Edm.String"></d:string>
      </m:properties>
    </content>
	  </entry>
	</feed>

##6\. Modello Advanced

###6\.1 Modello Data Insight
Restituisce informazioni statistiche sui dati di utilizzo con cui è stato compilato questo modello.

Disponibile solo per la compilazione di raccomandazioni.

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetDataInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>Esempio:<br>`<rootURI>/GetDataInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
|	modelId |	Identificatore univoco del modello. |
|	apiVersion | 1\.0 |
||| 
| Corpo della richiesta | NESSUNO |

**Risposta**:

Codice stato HTTP: 200

I dati vengono restituiti come una raccolta di proprietà.

- `feed/entry/id/content/properties/key`: contiene il nome della proprietà.
- `feed/entry/id/content/properties/value`: contiene il valore della proprietà.

La tabella seguente illustra il valore rappresentato da ogni chiave.

|Chiave|Descrizione|
|:-----|:----|
| AvgItemLength | Numero medio di utenti distinti per elemento. |
| AvgUserLength | Numero medio di elementi distinti per utente. |
| DensificationNumberOfItems | Numero di elementi dopo l'eliminazione degli elementi che non possono essere modellati. |
| DensificationNumberOfUsers | Numero di punti di utilizzo dopo l'eliminazione degli utenti e degli elementi che non possono essere modellati. |
| DensificationNumberOfRecords | Numero di punti di utilizzo dopo l'eliminazione degli utenti e degli elementi che non possono essere modellati. |
| MaxItemLength | Numero di utenti distinti per l'elemento più comune. |
| MaxUserLength | Numero massimo di elementi distinti per un utente. |
| MinItemLength | Numero massimo di utenti distinti per un elemento. |
| MinUserLength | Numero minimo di elementi distinti per un utente. |
| RawNumberOfItems | Numero di elementi nei file di utilizzo. |
| RawNumberOfUsers | Numero di punti di utilizzo prima di qualsiasi eliminazione. |
| RawNumberOfRecords | Numero di punti di utilizzo prima di qualsiasi eliminazione. |
| SamplingNumberOfItems | N/D |
| SamplingNumberOfRecords | N/D |
| SamplingNumberOfUsers | N/D |

XML OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get data insight statistics</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
	<entry>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgItemLength</d:Key>
        <d:Value m:type="Edm.String">18.936</d:Value>
      </m:properties>
    </content>
	</entry>
	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgUserLength</d:Key>
        <d:Value m:type="Edm.String">51.879</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfItems</d:Key>
        <d:Value m:type="Edm.String">2,000</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
	<title type="text">GetDataInsightStatisticsEntity</title>
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfRecords</d:Key>
        <d:Value m:type="Edm.String">37,872</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
	<title type="text">GetDataInsightStatisticsEntity</title>
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
	<content type="application/xml">
		<m:properties>
			<d:Key m:type="Edm.String">DensificationNumberOfUsers</d:Key>
			<d:Value m:type="Edm.String">730</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
    	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MaxItemLength</d:Key>
				<d:Value m:type="Edm.String">4,704</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
    	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    	<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MaxUserLength</d:Key>
				<d:Value m:type="Edm.String">190</d:Value>
			</m:properties>
    	</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MinItemLength</d:Key>
				<d:Value m:type="Edm.String">8</d:Value>
			</m:properties>
    	</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MinUserLength</d:Key>
				<d:Value m:type="Edm.String">20</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfItems</d:Key>
				<d:Value m:type="Edm.String">2,000</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfRecords</d:Key>
				<d:Value m:type="Edm.String">72,022</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfUsers</d:Key>
				<d:Value m:type="Edm.String">9,044</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfItems</d:Key>
				<d:Value m:type="Edm.String">2,000</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfRecords</d:Key>
				<d:Value m:type="Edm.String">72,022</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfUsers</d:Key>
				<d:Value m:type="Edm.String">9,044</d:Value>
			</m:properties>
		</content>
    </entry>
    </feed>

###6\.2. Modello Insight
Restituisce informazioni dettagliate sul modello nella compilazione attiva o, se indicata, in una compilazione specifica.

Disponibile solo per la compilazione di raccomandazioni.

| Metodo HTTP | URI |
|:--------|:--------|
|GET |Con l'ID compilazione attiva:<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/GetModelInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>Con l'ID compilazione specifica:<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
|	modelId |	Identificatore univoco del modello. |
|	buildId |	Facoltativo: numero che identifica una compilazione completata. |
|	apiVersion | 1\.0 |
||| 
| Corpo della richiesta | NESSUNO |

**Risposta**:

Codice stato HTTP: 200

I dati vengono restituiti come una raccolta di proprietà.

- `feed/entry/id/content/properties/key`
- `feed/entry/id/content/properties/value`


La tabella seguente illustra il valore rappresentato da ogni chiave.

| Chiave | Descrizione |
|:---- |:----|
| CatalogCoverage | La parte del catalogo che è possibile modellare con modelli di utilizzo. Il resto degli elementi richiederà funzionalità basate sul contenuto. |
| Mpr | Classificazione percentile media del modello. È preferibile un valore basso. |
| NumberOfDimensions | Numero di dimensioni usate dall'algoritmo di fattorizzazione di matrice. |


XML OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get model insight statistics</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-27T14:27:11Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">CatalogCoverage</d:Key>
				<d:Value m:type="Edm.String">1.000</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">Mpr</d:Key>
				<d:Value m:type="Edm.String">0.367</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">NumberOfDimensions</d:Key>
				<d:Value m:type="Edm.String">20</d:Value>
			</m:properties>
		</content>
	</entry>
	</feed>

###6\.3. Ottenere un esempio del modello
Ottiene un esempio del modello di raccomandazione.

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelSample?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>Esempio:<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>Con l'ID compilazione specifica:<br>`<rootURI>/GetModelSample?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`<br>Esempio:<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&buildId=%271500068%27&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
|	modelId |	Identificatore univoco del modello. |
|	apiVersion | 1\.0 |
||| 
| Corpo della richiesta | NESSUNO |

**Risposta**:

Codice stato HTTP: 200

XML OData

La risposta viene restituita in un formato di testo non elaborato:

<pre>
Level 1
---------------
655fc955-a5a3-4a26-9723-3090859cb27b, Prey: A Novel
	655fc955-a5a3-4a26-9723-3090859cb27b, Prey: A Novel Rating: 0.5215
	3f471802-f84f-44a0-99c8-6d2e7418eec1, Black Hawk Down: A Story of Modern War Rating: 0.5151
	07b10e28-9e7c-4032-90b7-10acab7f2460, Cryptonomicon Rating: 0.5148
	6afc18e4-8c2a-43d1-9021-57543d6b11d8, Imajica Rating: 0.5146
	e4cc5e69-3567-43ab-b00f-f0d8d0506870, Hit List Rating: 0.514
56b61441-0eed-46cc-a8f6-112775b81892, Life and Death in Shanghai
	56b61441-0eed-46cc-a8f6-112775b81892, Life and Death in Shanghai Rating: 0.5218
	53156702-cc0c-443d-b718-6fb74b2491d3, Son of \ Rating: 0.5212
	fb8cf7a6-8719-46ee-97d4-92f931d77a3a, Smoke and Mirrors: Short Fictions and Illusions Rating: 0.5188
	8f5fe006-79e4-4679-816b-950989d1db4b, A Place I've Never Been (Contemporary American Fiction) Rating: 0.5156
	d8db4583-cc0f-49ce-bc95-b7fa3491623f, Happiness: A Novel Rating: 0.5156
50471eec-9aeb-4900-84d7-21567ab18546, If the Buddha Dated: A Handbook for Finding Love on a Spiritual Path
	cfe922a1-7ca0-4f8d-ad9d-b7cc87bfe0ef, Divine Secrets of the Ya-Ya Sisterhood: A Novel Rating: 0.5266
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Rating: 0.5252
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven Rating: 0.5244
	e2cbf7ad-0636-4117-8b30-298da6df7077, Animal Dreams Rating: 0.5227
	6c818fd3-5a09-417d-9ab4-7ffe090f0fef, Confessions of an Ugly Stepsister: A Novel Rating: 0.5222
5e97148f-defb-4d74-af2d-80f4763bf531, The Deep End of the Ocean (Oprah's Book Club)
	5e97148f-defb-4d74-af2d-80f4763bf531, The Deep End of the Ocean (Oprah's Book Club) Rating: 0.537
	5dcbac37-2946-4f2a-a0b3-bbe710f9409a, Up Island: A Novel Rating: 0.5277
	bc5b69db-733b-4346-adde-3927544258f7, Downtown Rating: 0.5275
	31fe5c63-3e5a-48d0-802b-d3b0f989a634, Have a Nice Day: A Tale of Blood and Sweatsocks Rating: 0.5252
	0adf981a-b65b-4c11-b36b-78aca2f948a2, The Perfect Storm: A True Story of Men Against the Sea Rating: 0.5238
68f97068-ae1a-4163-9e94-396b800b743d, Modoc: The True Story of the Greatest Elephant That Ever Lived
	68f97068-ae1a-4163-9e94-396b800b743d, Modoc: The True Story of the Greatest Elephant That Ever Lived Rating: 0.5379
	6724862e-e4e7-4022-9614-1468d8b902ff, Little House on the Prairie Rating: 0.5345
	cdedb837-1620-496d-94c4-6ccfed888320, Little House in the Big Woods Rating: 0.5325
	382164ba-406b-4187-b726-d7a54b9d790d, The Tao of Pooh Rating: 0.5309
	6a068d6a-bb74-4ba3-b3f2-a956c4f9d1b5, On the Banks of Plum Creek Rating: 0.5285
37ef8e74-e348-44e5-aabc-1d7f9efcb25b, Men Are from Mars Women Are from Venus: A Practical Guide for Improving Communication and Getting What You Want in Your Relationships
	37ef8e74-e348-44e5-aabc-1d7f9efcb25b, Men Are from Mars, Women Are from Venus: A Practical Guide for Improving Communication and Getting What You Want in Your Relationships Rating: 0.5397
	f2be16d4-5faf-4d32-ab83-7ba74d29261e, Politically Correct Bedtime Stories: Modern Tales for Our Life and Times Rating: 0.5207
	ef732c5c-334b-4d6b-ab82-7255eb7286d0, Honor Among Thieves Rating: 0.5195
	0b209b8c-7cdd-47fd-b940-05c7ff7c60fc, The Giving Tree Rating: 0.5194
	883b360f-8b42-407f-b977-2f44ad840877, Scary Stories to Tell in the Dark: Collected from American Folklore (Scary Stories) Rating: 0.5184
ff51b67e-fa8e-4c5e-8f4d-02a928de735d, Men at Work: The Craft of Baseball
	d008dae9-c73a-40a1-9a9b-96d5cf546f36, The Gulag Archipelago 1918-1956: An Experiment in Literary Investigation I-II Rating: 0.5416
	ff51b67e-fa8e-4c5e-8f4d-02a928de735d, Men at Work: The Craft of Baseball Rating: 0.5403
	49dec30e-0adb-411a-b186-48eaabf6f8bc, Fatherland Rating: 0.5394
	cc7964fd-d30f-478e-a425-93ddbdf094ed, Magic the Gathering: Arena Vol. 1 Rating: 0.5379
	8a1e9f36-97af-4614-bed9-24e3940a05f3, More Sniglets: Any Word That Doesn't Appear in the Dictionary but Should Rating: 0.5377
12a6d988-be21-4a09-8143-9d5f4261ba16, A Dream of Eagles
	07b10e28-9e7c-4032-90b7-10acab7f2460, Cryptonomicon Rating: 0.5417
	e4cc5e69-3567-43ab-b00f-f0d8d0506870, Hit List Rating: 0.5416
	1f1a34c4-9781-49f5-a3cc-acec3ae3c71d, The Family Rating: 0.5371
	56daeffe-7d48-43cd-8ef8-7dffd0c103d3, Kilo Class Rating: 0.5366
	b2fe511e-5cb9-4a56-b823-2801e63e6a96, Legal Tender Rating: 0.5366
df87525b-e435-4bd6-8701-4e60ad344e28, Finding Fish
	56d33036-dfda-46b9-8e2a-76cb03921bb0, The X-Files: Ground Zero Rating: 0.5417
	0780cde8-6529-4e1d-b6c6-082c1b80e596, Twelve Red Herrings Rating: 0.5416
	df87525b-e435-4bd6-8701-4e60ad344e28, Finding Fish Rating: 0.5408
	400fe331-2c35-490c-adbc-b28b4b73d56c, Shall We Tell the President? Rating: 0.5383
	f86ad7d0-5c03-42b3-aebf-13d44aec8b30, Shades of Grace Rating: 0.5358
de1f62a4-89e6-44d2-aaee-992a4bf093f1, The Map That Changed the World: William Smith and the Birth of Modern Geology
	de1f62a4-89e6-44d2-aaee-992a4bf093f1, The Map That Changed the World: William Smith and the Birth of Modern Geology Rating: 0.5422
	b303538f-e2c6-4a2c-b425-8d21e684fc3e, My Uncle Oswald Rating: 0.5385
	34b84627-48af-4a4c-96c4-b26fb3863f56, Midnight In the Garden of Good and Evil Rating: 0.5379
	306cbaa7-b1a8-4142-9d55-e11b5018a7a8, The Street Lawyer Rating: 0.5376
	e53b4baa-8c09-45c4-95c0-b6a26b98770b, Miss Smillas Feeling for Snow Rating: 0.5367

Level 2
---------------
352aaea1-6b12-454d-a3d5-46379d9e4eb2, The Sinister Pig (Hillerman Tony)
	352aaea1-6b12-454d-a3d5-46379d9e4eb2, The Sinister Pig (Hillerman Tony) Rating: 0.5425
	74c49398-bc10-4af5-a658-a996a1201254, Children of the Storm (Peters Elizabeth) Rating: 0.5387
	9ba80080-196e-43fd-8025-391d963f77e7, The Floating Girl Rating: 0.5372
	e68f81d5-7745-4cc7-b943-fedb8fcc2ced, Killer Smile (Scottoline Lisa) Rating: 0.5353
	b2fe511e-5cb9-4a56-b823-2801e63e6a96, Legal Tender Rating: 0.5332
c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, Lake Wobegon days
	0adf981a-b65b-4c11-b36b-78aca2f948a2, The Perfect Storm: A True Story of Men Against the Sea Rating: 0.5433
	c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, Lake Wobegon days Rating: 0.543
	a00ae6ad-4a7f-4211-9836-75ce8834eb11, Sniglets (Snig'lit: Any Word That Doesn't Appear in the Dictionary But Should) Rating: 0.5327
	6f6e192e-0d64-49ca-9b63-f09413ea1ee6, Politically Correct Holiday Stories: For an Enlightened Yuletide Season Rating: 0.5307
	798051a8-147d-4d46-b0dc-e836325029e6, AGE OF INNOCENCE (MOVIE TIE-IN) Rating: 0.5301
73f3e25a-e996-4162-9ed8-ff3d34075650, O Pioneers! (Penguin Twentieth-Century Classics)
	cba8163f-6536-436b-8130-47b4a43c827f, Trust No One (The Official Guide to the X-Files Vol. 2) Rating: 0.5434
	5708e4cb-2492-49c0-94a8-cc413eec5d89, Small Gods (Discworld Novels (Paperback)) Rating: 0.5406
	73f3e25a-e996-4162-9ed8-ff3d34075650, O Pioneers! (Penguin Twentieth-Century Classics) Rating: 0.5403
	d885b0bd-ae4b-452d-bdf2-faa90197dbc9, The Color of Magic Rating: 0.539
	b133a9c4-4784-4db3-b100-d0d6dffb94d2, The Truth Is Out There (The Official Guide to the X-Files Vol. 1) Rating: 0.5367
271700a5-854a-4d5a-8409-6b57a5ee4de4, Fluke: Or I Know Why the Winged Whale Sings
	271700a5-854a-4d5a-8409-6b57a5ee4de4, Fluke: Or I Know Why the Winged Whale Sings Rating: 0.5445
	2de1c354-90ff-47c5-a0db-1bad7d88ef94, The Salaryman's Wife (Children of Violence Series) Rating: 0.5329
	d279416e-19c0-43f8-9ec9-a585947879ca, Zen Attitude Rating: 0.5316
	c8f854d7-3de3-4b23-8217-f4f851670fd4, Revenge of the Cootie Girls: A Robin Hudson Mystery (Robin Hudson Mysteries (Paperback)) Rating: 0.5305
	8ef4751c-7074-409e-a3ac-d49b222fc864, Where the Wild Things Are Rating: 0.5289
9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, Their Eyes Were Watching God
	9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, Their Eyes Were Watching God Rating: 0.5446
	da45c4d5-aba1-413b-a9bd-50df98b1e1d2, The Bean Trees Rating: 0.5389
	65ecbdd1-131c-40c3-a3d6-d86ca281377a, The God of Small Things Rating: 0.5387
	c78743bf-7947-4a0c-8db7-8a3bfe69ba70, The Stone Diaries Rating: 0.5355
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven Rating: 0.5344
5f17d90a-2604-4fe8-8977-1a280b9098b1, One for the Money (Stephanie Plum Novels (Paperback))
	5f17d90a-2604-4fe8-8977-1a280b9098b1, One for the Money (Stephanie Plum Novels (Paperback)) Rating: 0.5446
	57169b2b-9a8a-486b-9aac-1ed98ce57168, Final Appeal Rating: 0.5332
	efcb1bc4-7278-4a8f-b491-befde02070d6, Moment of Truth Rating: 0.5329
	1efa91a2-993b-4c43-9f5c-3454fc12612d, Burn Factor Rating: 0.5309
	24c59962-458a-4ec8-b95d-d694e861919c, At Home in Mitford (The Mitford Years) Rating: 0.5303
4fd48c46-1a20-4c57-bc7f-a02ef123dc52, As Nature Made Him: The Boy Who Was Raised As a Girl
	4fd48c46-1a20-4c57-bc7f-a02ef123dc52, As Nature Made Him: The Boy Who Was Raised As a Girl Rating: 0.5449
	cd5f2c03-20cb-43be-a1fb-3b4233e63222, Pigs in Heaven Rating: 0.5329
	19985fdb-d07a-4a25-ae4a-97b9cb61e5d1, Love in the Time of Cholera (Penguin Great Books of the 20th Century) Rating: 0.5267
	15689d09-c711-4844-84d8-130a90237b26, Bel Canto Rating: 0.5245
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Rating: 0.5235
98df28ec-41e7-4fca-b77f-8b0d3109085d, Star Trek Memories
	f874b5a3-5d40-4436-94ff-0fa1c090ddf5, The Sun Also Rises (A Scribner classic) Rating: 0.5451
	98df28ec-41e7-4fca-b77f-8b0d3109085d, Star Trek Memories Rating: 0.5442
	0ce0014a-9a48-4013-a08a-7f2c11877930, H.M.S. Unseen Rating: 0.5421
	15316ca6-1e38-425f-893d-691944a47000, More Scary Stories To Tell In The Dark Rating: 0.5409
	329d5682-3dc3-4206-8aa2-eef4b1032258, Letters from the Earth Rating: 0.54
5b9445d5-c072-419c-8d49-6f669bb1b0a9, Daughter of Fortune: A Novel (Oprah's Book Club (Hardcover))
	5b9445d5-c072-419c-8d49-6f669bb1b0a9, Daughter of Fortune: A Novel (Oprah's Book Club (Hardcover)) Rating: 0.5462
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Rating: 0.5372
	604eb3bd-6026-4f51-bffd-9fb54f180400, Family Pictures: A Novel Rating: 0.5341
	8d06d01d-31cd-4678-b6b1-140a67987ce9, Songs in Ordinary Time (Oprah's Book Club (Paperback)) Rating: 0.5334
	da45c4d5-aba1-413b-a9bd-50df98b1e1d2, The Bean Trees Rating: 0.5319
d5358189-d70f-4e35-8add-34b83b4942b3, Pigs in Heaven
	d5358189-d70f-4e35-8add-34b83b4942b3, Pigs in Heaven Rating: 0.5491
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Rating: 0.5401
	c78743bf-7947-4a0c-8db7-8a3bfe69ba70, The Stone Diaries Rating: 0.5393
	8d06d01d-31cd-4678-b6b1-140a67987ce9, Songs in Ordinary Time (Oprah's Book Club (Paperback)) Rating: 0.5382
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven Rating: 0.5367

</pre>




##7\. Modello Business Rules
Questi sono i tipi di regole supportati: - <strong>BlockList</strong>: Blocklist consente di specificare un elenco di elementi che non dovranno essere restituiti nei risultati delle raccomandazioni.. - <strong>FeatureBlockList</strong>: FeatureBlockList consente di bloccare gli elementi in base ai valori delle relative funzionalità. - <strong>Upsale</strong>: Upsale consente di imporre gli elementi da restituire nei risultati delle raccomandazioni. - <strong>WhiteList</strong>: WhiteList consente di suggerire solo raccomandazioni da un elenco di elementi. - <strong>FeatureWhiteList</strong>: FeatureWhiteList consente di raccomandare solo elementi con valori di funzionalità specifici. - <strong>PerSeedBlockList</strong>: PerSeedBlockList consente di specificare un elenco, per tipo di elemento, degli elementi che non possono essere restituiti come risultati delle raccomandazioni.


###7\.1. Ottenere le regole del modello

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>Esempio:<br>`<rootURI>/GetModelRules?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
|	modelId |	Identificatore univoco del modello. |
|	apiVersion | 1\.0 |
||| 
| Corpo della richiesta | NESSUNO |

**Risposta**:

Codice stato HTTP: 200

- `feed/entry/content/properties/Id`: identificatore univoco della regola.
- `feed/entry/content/properties/Type`: tipo di regola.
- `feed/entry/content/properties/Parameter`: parametro della regola.

XML OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get a list of rules for a model</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T12:58:57Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetAListOfRulesForAModelEntity</title>
		<updated>2014-11-05T12:58:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000043</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1000"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetAListOfRulesForAModelEntity</title>
		<updated>2014-11-05T12:58:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000044</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1001"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	</feed>

###7\.2. Aggiungere una regola

| Metodo HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/AddRule?apiVersion=%271.0%27`|
|INTESTAZIONE |`"Content-Type", "text/xml"`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
|	apiVersion | 1\.0 |
||| 
| Corpo della richiesta | 
<ins>Ogni volta che si forniscono gli ID degli elementi per le regole di business, assicurarsi di utilizzare l'Id esterno dell'elemento (lo stesso Id utilizzato nel file di catalogo)</ins><br> 
<ins>Per l'aggiunta della regola BlockList:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>BlockList</Type><Value>{"ItemsToExclude":["2406E770-769C-4189-89DE-1C9283F93A96","3906E110-769C-4189-89DE-1C9283F98888"]}</Value></ApiFilter>`<br><br><ins>Per l'aggiunta della regola Upsale:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>Upsale</Type><Value>{"ItemsToUpsale":["2406E770-769C-4189-89DE-1C9283F93A96"]}</Value></ApiFilter>`<br><br><ins>Per l'aggiunta della regola WhiteList:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>WhiteList</Type><Value>{"ItemsToInclude":["2406E770-769C-4189-89DE-1C9283F93A96","1116E770-769C-4189-89DE-1C9283F88888"]}</Value></ApiFilter>`<br><br><ins>Per l'aggiunta della regola PerSeedBlockList:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>PerSeedBlockList</Type><Value>{"SeedItems":["9949"],"ItemsToExclude":["9862","8158","8244"]}</Value></ApiFilter>`|

**Risposta**:

Codice stato HTTP: 200

L'API restituisce la regola appena creata con i relativi dettagli. Le proprietà della regola possono essere recuperate dai percorsi seguenti:

- `feed/entry/content/properties/Id`: identificatore univoco della regola.
- `feed/entry/content/properties/Type`: tipo di regola, ovvero BlockList o Upsale.
- `feed/entry/content/properties/Parameter`: parametro della regola.

XML OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Add A Rule</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T11:13:28Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">AddARuleEntity</title>
		<updated>2014-11-05T11:13:28Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000041</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1002"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	</feed>

###7\.3. Eliminare una regola

| Metodo HTTP | URI |
|:--------|:--------|
|DELETE |`<rootURI>/DeleteRule?modelId=%27<model_id>%27&filterId=%27<filter_Id>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`DeleteRule?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&filterId=%271000011%27&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
|	modelId |	Identificatore univoco del modello. |
|	filterId |	Identificatore univoco del filtro. |
|	apiVersion | 1.0 |
||| 
| Corpo della richiesta | NESSUNO |

**Risposta**:

Codice stato HTTP: 200

###7\.4. Eliminare tutte le regole

| Metodo HTTP | URI |
|:--------|:--------|
|DELETE |`<rootURI>/DeleteAllRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`DeleteAllRules?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
|	modelId |	Identificatore univoco del modello. |
|	apiVersion | 1.0 |
||| 
| Corpo della richiesta | NESSUNO |

**Risposta**:

Codice stato HTTP: 200

##8\. Catalogo

###8\.1 Importare i dati del catalogo

Se si caricano diversi file del catalogo nello stesso modello con diverse chiamate, verranno inseriti solo i nuovi elementi del catalogo. Gli elementi esistenti manterranno i valori originali. Non è possibile aggiornare i dati del catalogo con questo metodo.

I dati del catalogo devono seguire il formato seguente:

- Senza funzionalità: `<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- Con funzionalità: `<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

Nota: le dimensioni massime del file sono pari a 200 MB.

** Dettagli relativi al formato **

| Nome | Obbligatorio | Tipo | Descrizione |
|:---|:---|:---|:---|
| Item Id |Sì | [A-z], [a-z], [0-9], [\_] &#40;carattere di sottolineatura&#41;, [-] &#40;trattino&#41;<br> Lunghezza massima: 50 | Identificatore univoco di un elemento. | 
| Item Name | Sì| Qualsiasi carattere alfanumerico<br> Lunghezza massima: 255 | Nome dell'elemento. | 
| Item Category | Sì | Qualsiasi carattere alfanumerico <br> Lunghezza massima: 255 | Categoria alla quale appartiene l'elemento (ad esempio, libri di cucina, letteratura e così via); può essere vuoto. | 
| Descrizione | No, a meno che siano presenti funzionalità (può essere vuoto) | Qualsiasi carattere alfanumerico <br> Lunghezza massima: 4000 | Descrizione dell'elemento. | 
| Elenco delle funzionalità | No | Qualsiasi carattere alfanumerico <br> Lunghezza massima: 4000 | Elenco con valori delimitati da virgole di nome funzionalità=valore funzionalità che è possibile usare per migliorare la raccomandazione relativa al modello. Vedere la sezione [Argomenti avanzati](#2-advanced-topics). |


| Metodo HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|
|INTESTAZIONE |`"Content-Type", "text/xml"`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
|	modelId |	Identificatore univoco del modello. |
| filename | Identificatore testuale del catalogo.<br>Sono consentiti solo lettere (A-Z, a-z), numeri (0-9), trattini (-) e caratteri di sottolineatura (\_).<br>Lunghezza massima: 50 | 
| apiVersion | 1.0 | 
||| 
| Corpo della richiesta | Esempio (con funzionalità):<br/>2406e770-769c-4189-89de-1c9283f93a96,Clara Callan,Book,the book description,author=Richard Wright,publisher=Harper Flamingo Canada,year=2001<br>21bf8088-b6c0-4509-870c-e1c7ac78304a,The Forgetting Room: A Fiction (Byzantium Book),Book,,author=Nick Bantock,publisher=Harpercollins,year=1997<br>3bb5cb44-d143-4bdd-a55c-443964bf4b23,Spadework,Book,,author=Timothy Findley, publisher=HarperFlamingo Canada, year=2001<br>552a1940-21e4-4399-82bb-594b46d7ed54,Restraint of Beasts,Book,the book description,author=Magnus Mills, publisher=Arcade Publishing, year=1998</pre> |


**Risposta**:

Codice stato HTTP: 200

L'API restituisce un report dell'importazione. - `feed\entry\content\properties\LineCount`: numero di righe accettate. - `feed\entry\content\properties\ErrorCount`: numero di righe non inserite a causa di un errore.

XML OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Import catalog file</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-05T06:58:04Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
	<entry>
   	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">ImportCatalogFileEntity2</title>
		<updated>2014-10-05T06:58:04Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:LineCount m:type="Edm.String">4</d:LineCount>
				<d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
			</m:properties>
		</content>
	</entry>
	</feed>

###8\.2. Ottenere il catalogo
Recupera tutti gli elementi del catalogo.

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetCatalog?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`GetCatalog?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
|	modelId |	Identificatore univoco del modello. |
|	apiVersion | 1.0 |
||| 
| Corpo della richiesta | NESSUNO |

**Risposta**:

Codice stato HTTP: 200

La risposta include una voce per ogni elemento del catalogo. Ogni voce include i dati seguenti:

- `feed/entry/content/properties/ExternalId`: ID esterno dell'elemento del catalogo, fornito dal cliente.
- `feed/entry/content/properties/InternalId`: ID interno dell'elemento del catalogo, generato dall'API Recommendations di Azure Machine Learning.
- `feed/entry/content/properties/Name`: nome dell'elemento del catalogo.
- `feed/entry/content/properties/Category`: categoria dell'elemento del catalogo.
- `feed/entry/content/properties/Description`: descrizione dell'elemento del catalogo.
- `feed/entry/content/properties/Metadata`: metadati dell'elemento del catalogo.


XML OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get All Catalog Items</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'" />
		<entry>
        	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">552A1940-21E4-4399-82BB-594B46D7ED54</d:ExternalId>
				<d:InternalId m:type="Edm.String">060db26a-e6a6-464e-bb52-436d2da82a50</d:InternalId>
				<d:Name m:type="Edm.String">Restraint of Beasts</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:ExternalId>
				<d:InternalId m:type="Edm.String">209d7bfe-2eb9-4455-92a3-7c867a41a74a</d:InternalId>
				<d:Name m:type="Edm.String">Clara Callan</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">3BB5CB44-D143-4BDD-A55C-443964BF4B23</d:ExternalId>
				<d:InternalId m:type="Edm.String">913ff79b-059b-4d4d-8042-6fa4cc1391dd</d:InternalId>
				<d:Name m:type="Edm.String">Spadework</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
    		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">21BF8088-B6C0-4509-870C-E1C7AC78304A</d:ExternalId>
				<d:InternalId m:type="Edm.String">ea65e4fa-768c-40b4-92c3-69d3e8178691</d:InternalId>
				<d:Name m:type="Edm.String">The Forgetting Room: A Fiction (Byzantium Book)</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	</feed>

###8\.3. Ottenere gli elementi del catalogo in base al token

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetCatalogItemsByToken?modelId=%27<modelId>%27&token=%27<token>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`GetCatalogItemsByToken?modelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&token=%27Cla%27&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
|	modelId |	Identificatore univoco del modello. |
|	token |	Token del nome dell'elemento del catalogo. Deve contenere almeno tre caratteri. |
|	apiVersion | 1\.0 |
||| 
| Corpo della richiesta | NESSUNO |

**Risposta**:

Codice stato HTTP: 200

La risposta include una voce per ogni elemento del catalogo. Ogni voce include i dati seguenti:

- `feed/entry/content/properties/ExternalId`: ID esterno dell'elemento del catalogo, fornito dal cliente.
- `feed/entry/content/properties/InternalId`: ID interno dell'elemento del catalogo, generato dall'API Recommendations di Azure Machine Learning.
- `feed/entry/content/properties/Name`: nome dell'elemento del catalogo.
- `feed/entry/content/properties/Category`: categoria dell'elemento del catalogo.
- `feed/entry/content/properties/Description`: descrizione dell'elemento del catalogo.
- `feed/entry/content/properties/Metadata`: metadati dell'elemento del catalogo.

XML OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get Catalog items that contain a token</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-29T11:48:19Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">CatalogItemsThatContainATokenEntity</title>
			<updated>2014-10-29T11:48:19Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    		<content type="application/xml">
      			<m:properties>
					<d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
        			<d:Name m:type="Edm.String">Clara Callan</d:Name>
        			<d:Rating m:type="Edm.Double">0</d:Rating>
        			<d:Reasoning m:type="Edm.String"></d:Reasoning>
        			<d:Metadata m:type="Edm.String"></d:Metadata>
        			<d:FormattedRating m:type="Edm.Double" m:null="true"></d:FormattedRating>
      			</m:properties>
			</content>
		</entry>
	</feed>

##9\. Dati di utilizzo
###9\.1. Importare i dati di utilizzo
####9\.1.1. Caricamento del file
Queste sezioni mostrano come caricare i dati di utilizzo tramite un file. È possibile chiamare l'API più volte con i dati di utilizzo. Tutti i dati di utilizzo verranno salvati per tutte le chiamate.

| Metodo HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
|	modelId |	Identificatore univoco del modello. |
| filename | Identificatore testuale del catalogo.<br>Sono consentiti solo lettere (A-Z, a-z), numeri (0-9), trattini (-) e caratteri di sottolineatura (_).<br>Lunghezza massima: 50 caratteri |
| apiVersion | 1.0 |
|||
| Corpo della richiesta | Dati di utilizzo. Formato:<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>Nome</th><th>Obbligatorio</th><th>Tipo</th><th>Descrizione</th></tr><tr><td>Id utente</td><td>Sì</td><td>[A-z], [a-z], [0-9], [_] & #40; Carattere di sottolineatura & #41; [-] & #40; Lineetta & #41;<br> La lunghezza massima consentita: 255 </td><td>Identificatore univoco dell'utente.</td></tr><tr><td>Id elemento</td><td>Sì</td><td>[A-z], [a-z], [0-9], [& #95;] & #40; Carattere di sottolineatura & #41; [-] & #40; Lineetta & #41;<br> La lunghezza massima consentita: 50</td><td>Identificatore univoco di un elemento.</td></tr><tr><td>Ora</td><td>No</td><td>Data nel formato: AAAA/MM/GGTHH:MM:SS (ad esempio 2013/06/20T10:00:00)</td><td>Ora dei dati.</td></tr><tr><td>Evento</td><td>No; se fornito, deve inserire anche la data</td><td>Uno dei seguenti:<br>• Fare clic su<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Purchase</td><td></td></tr></table><br>Dimensione massima del file: 200MB<br><br>Esempio:<br><pre>149452,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>6360,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>50321,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>71285,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>224450,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>236645,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>107951,1b3d95e2-84e4-414c-bb38-be9cf461c347</pre> |

**Risposta**:

Codice stato HTTP: 200

- `Feed\entry\content\properties\LineCount`: numero di righe accettate.
- `Feed\entry\content\properties\ErrorCount`: numero di righe non inserite a causa di un errore.
- `Feed\entry\content\properties\FileId`: identificatore del file.

XML OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Add bulk usage data (usage file)</subtitle>
  	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T07:21:44Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
  	</entry>
	</feed>


####9\.1.2. Uso dell'acquisizione dei dati
Questa sezione illustra come inviare eventi in tempo reale a Recommendations di Azure Machine Learning, in genere dal sito Web.

| Metodo HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27`|
|INTESTAZIONE |`"Content-Type", "text/xml"`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
|	apiVersion | 1\.0 |
|Corpo della richiesta| Immissione di dati evento per ogni evento da inviare. Per lo stesso utente o la stessa sessione del browser si dovrà inviare lo stesso ID nel campo SessionId. Vedere l'esempio di corpo dell'evento di seguito.|


- Esempio di evento "Click":

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
		<SessionId>11112222</SessionId>
		<EventData>
		<EventData>
		<Name>Click</Name>
		<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
		</EventData>
		</EventData>
		</Event>

- Esempio di evento "RecommendationClick":

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>RecommendationClick</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    	</EventData>
  		</EventData>
		</Event>

- Esempio di evento "AddShopCart":

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>AddShopCart</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    	</EventData>
  		</EventData>
		</Event>

- Esempio di evento "RemoveShopCart":

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
		  	<EventData>
      				<Name>RemoveShopCart</Name>
      				<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    			</EventData>
  		</EventData>
		</Event>

- Esempio di evento "Purchase":

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
		<SessionId>11112222</SessionId>
		<EventData>
			<EventData>
				<Name>Purchase</Name>
				<PurchaseItems>
					<PurchaseItem>
						<ItemId>ABBF8081-C5C0-4F09-9701-E1C7AC78304A</ItemId>
						<Count>1</Count>
					</PurchaseItem>
					<PurchaseItem>
						<ItemId>21BF8088-B6C0-4509-870C-11C0AC7F304B</ItemId>
						<Count>3</Count>
					</PurchaseItem>
				</PurchaseItems>
			</EventData>
		</EventData>
		</Event>
		
		
		



- Esempio di invio di due eventi, "Click" e "AddShopCart":

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>Click</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
      	<ItemName>itemName</ItemName>
      	<ItemDescription>item description</ItemDescription>
      	<ItemCategory>category</ItemCategory>
    	</EventData>
    	<EventData>
      	<Name>AddShopCart</Name>
      	<ItemId>552A1940-21E4-4399-82BB-594B46D7ED54</ItemId>
    	</EventData>
  		</EventData>
		</Event>

**Risposta**: Codice stato HTTP: 200

###9\.2. Elencare i file di dati di utilizzo del modello
Recupera i metadati di tutti i file di dati di utilizzo del modello.

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/ListModelUsageFiles?forModelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/ListModelUsageFiles?forModelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
|	forModelId |	Identificatore univoco del modello. |
|	apiVersion | 1.0 |
||| 
| Corpo della richiesta | NESSUNO |

**Risposta**:

Codice stato HTTP: 200

La risposta include una voce per ogni file di dati di utilizzo. Ogni voce include i dati seguenti:

- `feed\entry\content\properties\Id`: ID del file di dati di utilizzo.
- `feed\entry\content\properties\Length`: lunghezza del file di dati di utilizzo, in MB.
- `feed\entry\content\properties\DateModified`: data di creazione del file di dati di utilizzo.
- `feed\entry\content\properties\UseInModel`: indica se il file di dati di utilizzo viene usato nel modello.

XML OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get a list of model's usage files info</subtitle>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-30T09:40:25Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
			<updated>2014-10-30T09:40:25Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Id m:type="Edm.String">4c067b42-e975-4cb2-8c98-a6ab80ed6d63</d:Id>
					<d:Length m:type="Edm.Double">0</d:Length>
					<d:DateModified m:type="Edm.String">10/30/2014 9:19:57 AM</d:DateModified>
					<d:UseInModel m:type="Edm.String">true</d:UseInModel>
				</m:properties>
			</content>
		</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
		<updated>2014-10-30T09:40:25Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">3126d816-4e80-4248-8339-1ebbdb9d544d</d:Id>
				<d:Length m:type="Edm.Double">0.001</d:Length>
				<d:DateModified m:type="Edm.String">10/30/2014 9:21:44 AM</d:DateModified>
				<d:UseInModel m:type="Edm.String">true</d:UseInModel>
          	</m:properties>
		</content>
	</entry>
</feed>

###9\.3. Ottenere statistiche di utilizzo
Ottiene le statistiche di utilizzo.

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetUsageStatistics?modelId=%27<modelId>%27& startDate=%27<date>%27&endDate=%27<date>%27&eventTypes=%27<types>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/GetUsageStatistics?modelId=%271d20c34f-dca1-4eac-8e5d-f299e4e4ad66%27&startDate=%272014%2F10%2F17T00%3A00%3A00%27&endDate=%272014%2F11%2F16T00%3A00%3A00%27&eventTypes=%271%2C2%2C3%2C4%2C5%27&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
| modelId |	Identificatore univoco del modello. |
| startDate |	Data di inizio. Formato: aaaa/MM/ggTHH:mm:ss |
| endDate |	Data di fine. Formato: aaaa/MM/ggTHH:mm:ss |
| eventTypes |	Stringa con valori delimitati da virgole di tipi di evento specifici o Null per ottenere tutti gli eventi. |
| apiVersion | 1.0 |
||| 
| Corpo della richiesta | NESSUNO |

**Risposta**:

Codice stato HTTP: 200

Raccolta di elementi chiave/valore. Ognuno contiene la somma degli eventi per un tipo specifico di eventi raggruppati in base all'ora.

- `feed\entry[i]\content\properties\Key`: contiene la data e l'ora (raggruppate per ora) e il tipo di evento.
- `feed\entry[i]\content\properties\Value`: numero totale di eventi.

XML OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get usage statistics</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-18T11:39:16Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;Click</d:Event>
				<d:Value m:type="Edm.String">5</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;RecommendationClick</d:Event>
				<d:Value m:type="Edm.String">10</d:Value>
          	</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
        <content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/1/2014 8:00:00 AM;RemoveShopCart</d:Event>
            	<d:Value m:type="Edm.String">10</d:Value>
			</m:properties>
        </content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/5/2014 8:00:00 AM;RemoveShopCart</d:Event>
				<d:Value m:type="Edm.String">10</d:Value>
			</m:properties>
		</content>
	</entry>
	</feed>

###9\.4. Ottenere un esempio del file di dati di utilizzo
Recupera i primi 2 KB del contenuto del file di dati di utilizzo:

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetUsageFileSample?modelId=%27<modelId>%27& fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/GetUsageFileSample?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fileId=%274c067b42-e975-4cb2-8c98-a6ab80ed6d63%27&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
| modelId |	Identificatore univoco del modello. |
| fileId |	Identificatore univoco del file di dati di utilizzo del modello. |
| apiVersion | 1.0 |
||| 
| Corpo della richiesta | NESSUNO |

**Risposta**:

Codice stato HTTP: 200

La risposta viene restituita in un formato di testo non elaborato: <pre> 85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 </pre>


###9\.5. Ottenere il file di dati di utilizzo del modello
Recupera l'intero contenuto del file. di dati di utilizzo.

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelUsageFile?mid=%27<modelId>%27& fid=%27<fileId>%27&download=%27<download_value>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/GetModelUsageFile?mid=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fid=%273126d816-4e80-4248-8339-1ebbdb9d544d%27&download=%271%27&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
| mid |	Identificatore univoco del modello. |
| fid |	Identificatore univoco del file di dati di utilizzo del modello. |
| download | 1 |
| apiVersion | 1.0 |
||| 
| Corpo della richiesta | NESSUNO |

**Risposta**:

Codice stato HTTP: 200

La risposta viene restituita in un formato di testo non elaborato: <pre> 85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 244881,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 50547,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 213090,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 260655,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 72214,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189334,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 36326,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189336,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189334,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 260655,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 162100,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 54946,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 260965,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 102758,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 112602,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 163925,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 262998,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 144717,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 </pre>

###9\.6. Eliminare il file di dati di utilizzo
Elimina il file di dati di utilizzo del modello specificato.

| Metodo HTTP | URI |
|:--------|:--------|
|DELETE |`<rootURI>/DeleteUsageFile?modelId=%27<modelId>%27&fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/DeleteUsageFile?modelId=%270f86d698-d0f4-4406-a684-d13d22c47a73%27&fileId=%27f2e0b09d-be5c-46b2-9ac2-c7f622e5e1a5%27&apiVersion=%271.0%27`|

| Nome parametro |	Valori validi |
|:--------			|:--------								|
| modelId |	Identificatore univoco del modello. |
| fileId | Identificatore univoco del file da eliminare. |
| apiVersion | 1.0 |
||| 
| Corpo della richiesta | NESSUNO |

**Risposta**:

Codice stato HTTP: 200


###9\.7. Eliminare tutti i file di dati di utilizzo
Elimina tutti i file di dati di utilizzo del modello.

| Metodo HTTP | URI |
|:--------|:--------|
|DELETE |`<rootURI>/DeleteAllUsageFiles?modelId=%27<modelId>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/DeleteAllUsageFiles?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&apiVersion=%271.0%27`|

| Nome parametro |	Valori validi |
|:--------			|:--------								|
| modelId |	Identificatore univoco del modello. |
| apiVersion | 1.0 |
||| 
| Corpo della richiesta | NESSUNO |

**Risposta**:

Codice stato HTTP: 200

##10\. Funzionalità
Questa sezione illustra come recuperare informazioni sulle funzionalità, ad esempio le funzionalità importate e i relativi valori, la classificazione e la relativa data di allocazione. Le funzionalità vengono importate come parte dei dati del catalogo e quindi la relativa classificazione viene associata durante una compilazione della classifica. La classificazione delle funzionalità può cambiare in base al modello di dati di utilizzo e al tipo di elementi. Per la coerenza dei dati di utilizzo e degli elementi, è opportuno che le fluttuazioni siano limitate. La classificazione delle funzionalità è espressa mediante un numero non negativo. Il numero 0 significa che la funzionalità non è stata classificata, ad esempio nel caso il cui l'API venga richiamata prima che sia completata la prima compilazione della classifica. La data in cui è stata attribuita la classificazione è detta aggiornamento del punteggio.

###10\.1. Ottenere informazioni sulle funzionalità (per l'ultima compilazione della classifica)
Recupera le informazioni sulle funzionalità, inclusa la classificazione per l'ultima compilazione della classifica riuscita.

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&apiVersion=%271.0%27`

| Nome parametro |	Valori validi |
|:--------			|:--------								|
| modelId |	Identificatore univoco del modello. |
|samplingSize| Numero di valori da includere per ogni funzionalità, in base ai dati presenti nel catalogo. <br/>I valori possibili sono:<br> -1, tutti i campioni. <br>0, nessun campionamento. <br>N, restituisce N campioni per ogni nome di funzionalità.|
| apiVersion | 1.0 |
||| 
| Corpo della richiesta | NESSUNO |


**Risposta**:

Codice stato HTTP: 200

La risposta contiene un elenco di voci di informazioni sulle funzionalità. Ogni voce contiene:

- `feed/entry/content/m:properties/d:Name`: nome della funzionalità.
- `feed/entry/content/m:properties/d:RankUpdateDate`: data in cui la classificazione è stata allocata alla funzionalità, nota anche come funzionalità di aggiornamento del punteggio. Una data cronologica ("0001-01-01T00:00:00") indica che non è stata eseguita alcuna compilazione della classifica.
- `feed/entry/content/m:properties/d:Rank` classificazione delle funzionalità (mobile). Una classificazione pari ad almeno 2.0 è considerata una funzionalità valida.
- `feed/entry/content/m:properties/d:SampleValues`: elenco con valori delimitati da virgole fino alle dimensioni di campionamento richieste.

XML OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get the features of a model</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2015-01-08T13:15:02Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">ModelFeaturesEntity</title>
		<updated>2015-01-08T13:15:02Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Name m:type="Edm.String">Author</d:Name>
				<d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
				<d:Rank m:type="Edm.String">0</d:Rank>
				<d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">ModelFeaturesEntity</title>
		<updated>2015-01-08T13:15:02Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Name m:type="Edm.String">Publisher</d:Name>
				<d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
				<d:Rank m:type="Edm.String">0</d:Rank>
				<d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">ModelFeaturesEntity</title>
		<updated>2015-01-08T13:15:02Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1"/>
		<contenttype="application/xml">
		<m:properties>
			<d:Name m:type="Edm.String">Year</d:Name>
			<d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
			<d:Rank m:type="Edm.String">0</d:Rank>
			<d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
		</m:properties>
		</content>
	</entry>
</feed>


###10\.2. Ottenere informazioni sulle funzionalità (per la specifica compilazione della classifica)

Recupera le informazioni sulle funzionalità, inclusa la classificazione per una compilazione della classifica specifica.

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&rankBuildId=<rankBuildId>&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&rankBuildId=1000551&apiVersion=%271.0%27`

| Nome parametro |	Valori validi |
|:--------			|:--------			|
| modelId |	Identificatore univoco del modello. |
|samplingSize| Numero di valori da includere per ogni funzionalità, in base ai dati presenti nel catalogo.<br/> I valori possibili sono:<br> -1, tutti i campioni. <br>0, nessun campionamento. <br>N, restituisce N campioni per ogni nome di funzionalità.|
|rankBuildId| Identificatore univoco per la compilazione della classifica o -1 per l'ultima compilazione della classifica.|
| apiVersion | 1.0 |
||| 
| Corpo della richiesta | NESSUNO |


**Risposta**:

Codice stato HTTP: 200

La risposta contiene un elenco di voci di informazioni sulle funzionalità. Ogni voce contiene:

- `feed/entry/content/m:properties/d:Name`: nome della funzionalità.
- `feed/entry/content/m:properties/d:RankUpdateDate`: data in cui la classificazione è stata allocata alla funzionalità, nota anche come funzionalità di aggiornamento del punteggio. Una data cronologica ("0001-01-01T00:00:00") indica che non è stata eseguita alcuna compilazione della classifica.
- `feed/entry/content/m:properties/d:Rank` classificazione delle funzionalità (mobile). Una classificazione pari ad almeno 2.0 è considerata una funzionalità valida.
- `feed/entry/content/m:properties/d:SampleValues`: elenco con valori delimitati da virgole fino alle dimensioni di campionamento richieste.

OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get the features of a model</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2015-01-08T13:54:22Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">ModelFeaturesEntity</title>
			<updated>2015-01-08T13:54:22Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Name m:type="Edm.String">Author</d:Name>
					<d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
					<d:Rank m:type="Edm.String">3.38867426</d:Rank>
					<d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
			<title type="text">ModelFeaturesEntity</title>
			<updated>2015-01-08T13:54:22Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Name m:type="Edm.String">Publisher</d:Name>
					<d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
					<d:Rank m:type="Edm.String">1.67839336</d:Rank>
					<d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
			<title type="text">ModelFeaturesEntity</title>
			<updated>2015-01-08T13:54:22Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Name m:type="Edm.String">Year</d:Name>
					<d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
					<d:Rank m:type="Edm.String">1.12352145</d:Rank>
					<d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
				</m:properties>
			</content>
		</entry>
	</feed>


##11\. Compilazione

  Questa sezione descrive le diverse API correlate alle compilazioni. Esistono tre tipi di compilazione: una compilazione raccomandazione, una compilazione classifica e una compilazione FBT (Frequently Bought Together).

Lo scopo della compilazione raccomandazione è di generare un modello di raccomandazione usato per le stime. Le stime (per questo tipo di compilazione) possono essere di due tipi: * I2I, nota anche come Raccomandazioni da elemento a elemento: dato un elemento o un elenco di elementi, questa opzione stimerà un elenco di elementi che possono risultare molto interessanti. * U2I, nota anche come Raccomandazioni da utente a elemento: dato un ID utente (e facoltativamente un elenco di elementi), questa opzione stimerà un elenco di elementi che possono risultare molto interessanti per l'utente specificato (e la scelta di elementi aggiuntivi). Le indicazioni U2I si basano sulla cronologia di elementi di interesse per l'utente fino al momento in cui è stato compilato il modello.

Una compilazione della classifica è una compilazione tecnica che consente di ottenere informazioni sull'utilità delle proprie funzionalità. In genere, per ottenere il miglior risultato per un modello di raccomandazione che comporta l'uso di funzionalità, è consigliabile eseguire i passaggi seguenti: attivare una compilazione della classifica (a meno che il punteggio delle funzionalità non sia stabile) e attendere finché non si ottiene il punteggio delle funzionalità. Recuperare la classificazione delle funzionalità chiamando l'API [Get Features Info](#101-get-features-info-for-last-rank-build). Configurare una compilazione di raccomandazioni con i parametri seguenti: `useFeatureInModel`, impostato su True. `ModelingFeatureList`, impostato su un elenco con valori delimitati da virgole di funzionalità con un punteggio pari a 2.0 o superiore (in base alle classificazioni recuperate nel passaggio precedente). `AllowColdItemPlacement`, impostato su True. Facoltativamente è possibile impostare `EnableFeatureCorrelation` su True e `ReasoningFeatureList` sull'elenco di funzionalità da usare per le spiegazioni (in genere, lo stesso elenco di funzionalità usato nella creazione del modello o un sottoelenco). Attivare la compilazione di raccomandazioni con i parametri configurati.

Nota: se non si configurano parametri, ovvero se si richiama la compilazione di raccomandazioni senza parametri, oppure non si disabilita in modo esplicito l'utilizzo di funzionalità (ad esempio, `UseFeatureInModel` impostato su False), il sistema imposterà i parametri correlati alla funzionalità sui valori illustrati sopra, se esiste una compilazione della classifica.

Non esistono limitazioni all'esecuzione contemporanea di una compilazione della classifica e di una compilazione di raccomandazioni per lo stesso modello. Non è tuttavia possibile eseguire due compilazioni dello stesso tipo nello stesso modello in parallelo.

Una compilazione FBT (Frequently Bought Together) è un altro algoritmo di raccomandazione, detto a volte sistema di raccomandazione "conservativo", che risulta appropriato per i cataloghi non omogenei per natura (omogenei: libri, film, alcuni alimenti, moda; non omogenei: computer e dispositivi, interdominio, estremamente diversi).

Nota: se i file di dati di utilizzo caricati contengono il campo facoltativo "event type", per la creazione del modello FBT verranno usati solo eventi "Purchase". Se non viene specificato alcun tipo di evento, tutti gli eventi verranno considerati come acquisti.


####11\.1 Parametri della compilazione

Ogni tipo di compilazione può essere configurato con un set di parametri, come illustrato di seguito. Se non si configurano parametri, il sistema attribuirà automaticamente i valori ai parametri in base alle informazioni presenti nel momento in cui si attiva una compilazione.

#####11\.1.1. Concentrazione dei dati di utilizzo
Gli utenti o gli elementi con pochi punti di utilizzo possono contenere una quantità di dati non significativi maggiore delle informazioni. Il sistema tenta di stimare il numero minimo di punti di utilizzo per utente/elemento da usare in un modello. Questo numero sarà compreso nell'intervallo definito dai parametri ItemCutoffLowerBound e ItemCutoffUpperBound per gli elementi e nell'intervallo definito dai parametri UserCutOffLowerBound e UserCutoffUpperBound per gli utenti. L'effetto di concentrazione di elementi o utenti può essere ridotto impostando su zero almeno uno dei limiti corrispondenti.

#####11\.1.2. Parametri di compilazione della classifica

La tabella seguente illustra i parametri per una compilazione della classifica.

|Chiave|Descrizione|Tipo|Valore valido|
|:-----|:----|:----|:---|
|NumberOfModelIterations | Il numero di iterazioni eseguite dal modello viene riflesso dal tempo di calcolo complessivo e dall'accuratezza del modello. A un numero più alto corrisponderà una migliore accuratezza, ma il tempo di calcolo sarà maggiore.| Integer | 10-50 |
| NumberOfModelDimensions | Il numero di dimensioni è correlato al numero di "funzionalità" che il modello proverà a trovare nei dati. L'aumento del numero di dimensioni consentirà l'ottimizzazione dei risultati in cluster più piccoli. Troppe dimensioni impediranno tuttavia al modello di trovare correlazioni tra gli elementi. | Integer | 10-40 |
|ItemCutOffLowerBound| Definisce il limite minimo dell'elemento per la concentrazione. Vedere la sezione precedente relativa alla concentrazione dei dati di utilizzo. | Integer | 2 o più (0 disabilita la concentrazione) |
|ItemCutOffUpperBound| Definisce il limite massimo dell'elemento per la concentrazione. Vedere la sezione precedente relativa alla concentrazione dei dati di utilizzo. | Integer | 2 o più (0 disabilita la concentrazione) |
|UserCutOffLowerBound| Definisce il limite minimo dell'utente per la concentrazione. Vedere la sezione precedente relativa alla concentrazione dei dati di utilizzo. | Integer | 2 o più (0 disabilita la concentrazione) |
|UserCutOffUpperBound| Definisce il limite massimo dell'utente per la concentrazione. Vedere la sezione precedente relativa alla concentrazione dei dati di utilizzo. | Integer | 2 o più (0 disabilita la concentrazione) |

#####11\.1.3. Parametri della compilazione di raccomandazioni
La tabella seguente illustra i parametri per la compilazione di raccomandazioni.

|Chiave|Descrizione|Tipo|Valore valido|
|:-----|:----|:----|:---|
|NumberOfModelIterations | Il numero di iterazioni eseguite dal modello viene riflesso dal tempo di calcolo complessivo e dall'accuratezza del modello. A un numero più alto corrisponderà una migliore accuratezza, ma il tempo di calcolo sarà maggiore.| Integer | 10-50 |
| NumberOfModelDimensions | Il numero di dimensioni è correlato al numero di "funzionalità" che il modello proverà a trovare nei dati. L'aumento del numero di dimensioni consentirà l'ottimizzazione dei risultati in cluster più piccoli. Troppe dimensioni impediranno tuttavia al modello di trovare correlazioni tra gli elementi. | Integer | 10-40 |
|ItemCutOffLowerBound| Definisce il limite minimo dell'elemento per la concentrazione. Vedere la sezione precedente relativa alla concentrazione dei dati di utilizzo. | Integer | 2 o più (0 disabilita la concentrazione) |
|ItemCutOffUpperBound| Definisce il limite massimo dell'elemento per la concentrazione. Vedere la sezione precedente relativa alla concentrazione dei dati di utilizzo. | Integer | 2 o più (0 disabilita la concentrazione) |
|UserCutOffLowerBound| Definisce il limite minimo dell'utente per la concentrazione. Vedere la sezione precedente relativa alla concentrazione dei dati di utilizzo. | Integer | 2 o più (0 disabilita la concentrazione) |
|UserCutOffUpperBound| Definisce il limite massimo dell'utente per la concentrazione. Vedere la sezione precedente relativa alla concentrazione dei dati di utilizzo. | Integer | 2 o più (0 disabilita la concentrazione) |
| Descrizione | Descrizione della compilazione. | String | Qualsiasi testo, con un massimo di 512 caratteri |
| EnableModelingInsights | Consente di calcolare la metrica nel modello di raccomandazione. | Boolean | True/False |
| UseFeaturesInModel | Indica se le funzionalità possono essere usate in ordine per migliorare il modello di raccomandazione. | Boolean | True/False |
| ModelingFeatureList | Elenco con valori delimitati da virgole dei nomi di funzionalità da usare nella compilazione di raccomandazioni, allo scopo di migliorare la raccomandazione. | String | Nomi di funzionalità, con un massimo di 512 caratteri |
| AllowColdItemPlacement | Indica se la raccomandazione dovrà effettuare il push anche degli elementi ignoti in base alla somiglianza di funzionalità. | Boolean | True/False |
| EnableFeatureCorrelation | Indica se le funzionalità possono essere usate nella motivazione. | Boolean | True/False |
| ReasoningFeatureList | Elenco con valori delimitati da virgole dei nomi delle funzionalità da usare nelle frasi relative alla motivazione (ad esempio, le spiegazioni delle raccomandazioni). | String | Nomi di funzionalità, con un massimo di 512 caratteri |
| EnableU2I | Consente la raccomandazione personalizzata, nota anche come U2I (raccomandazioni da utente a elemento). | Boolean | True/False (valore predefinito true) |

#####11\.1.4. Parametri della compilazione FBT
La tabella seguente illustra i parametri per la compilazione di raccomandazioni.

|Chiave|Descrizione|Tipo|Valore valido (predefinito)|
|:-----|:----|:----|:---|
|FbtSupportThreshold | Indica il livello conservativo del modello. Numero di co-occorrenze di elementi da considerare per la creazione del modello.| Integer | 3-50 (6) |
|FbtMaxItemSetSize | Limita il numero di elementi in un set frequente.| Integer | 2-3 (2) |
|FbtMinimalScore | Punteggio minimo che un set frequente deve avere per essere incluso nei risultati restituiti. Più alto è il valore, migliori saranno i risultati.| Double | 0 e superiore (0) |
|FbtSimilarityFunction | Definisce la funzione di somiglianza da usare per la compilazione. L’accuratezza favorisce la serendipità, la co-occorrenza favorisce la prevedibilità e Jaccard è un interessante compromesso tra i due. | Stringa | cooccurrence, lift, jaccard (lift) |


###11\.2. Attivare una compilazione di raccomandazioni

  Per impostazione predefinita, questa API attiverà la compilazione di un modello di raccomandazione. Per attivare la compilazione della classifica (per assegnare un punteggio alle funzionalità), è necessario usare la variante dell'API di compilazione con il parametro del tipo di compilazione.


| Metodo HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`|
|INTESTAZIONE |`"Content-Type", "text/xml"` (Se si sta inviando il corpo della richiesta)|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
| modelId |	Identificatore univoco del modello. |
| userDescription | Identificatore testuale del catalogo. Tenere presente che, se si usano degli spazi, è necessario codificarli con il simbolo %20. Vedere l'esempio precedente.<br>Lunghezza massima: 50 |
| apiVersion | 1.0 |
||| 
| Corpo della richiesta | Se lasciato vuoto, la compilazione verrà eseguita con i parametri di compilazione predefiniti.<br><br>Per impostare i parametri di compilazione, inviarli come XML nel corpo, come nell'esempio seguente. Per una descrizione dei parametri, vedere la sezione "Parametri della compilazione".`<NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance><EnableModelingInsights>true</EnableModelingInsights><UseFeaturesInModel>false</UseFeaturesInModel><ModelingFeatureList>feature_name_1,feature_name_2,...</ModelingFeatureList><AllowColdItemPlacement>false</AllowColdItemPlacement><EnableFeatureCorrelation>false</EnableFeatureCorrelation><ReasoningFeatureList>feature_name_a,feature_name_b,...</ReasoningFeatureList></BuildParametersList>` |

**Risposta**:

Codice stato HTTP: 200

Questa è un'API asincrona. Come risposta si otterrà un ID compilazione. Per sapere quando termina l'esecuzione della compilazione, è necessario chiamare l'API "Get Builds Status of a Model" e individuare l'ID compilazione nella risposta. Tenere presente che una compilazione può richiedere minuti o ore, a seconda delle dimensioni dei dati.

Non è possibile usare raccomandazioni finché la compilazione non viene completata.

Stati di compilazione validi:

- Create: è stata creata una richiesta di compilazione.
- Queued: la richiesta di compilazione è stata inviata e accodata.
- Building: la compilazione è in corso.
- Success: la compilazione è stata completata.
- Error: la compilazione è terminata con un errore.
- Cancelled: la compilazione è stata annullata.
- Cancelling: è stata inviata una richiesta di annullamento per la compilazione.


È possibile trovare l'ID compilazione nel percorso seguente: `Feed\entry\content\properties\Id`

XML OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
  	</entry>
	</feed>

###11\.3. Attivare la compilazione (di raccomandazioni, della classifica o FBT)

| Metodo HTTP | URI |
|:--------|:--------|
|POST |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&buildType=%27<buildType>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&buildType=%27Ranking%27&apiVersion=%271.0%27`|
|INTESTAZIONE |`"Content-Type", "text/xml"` (Se si sta inviando il corpo della richiesta)|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
| modelId |	Identificatore univoco del modello. |
| userDescription | Identificatore testuale del catalogo. Tenere presente che, se si usano degli spazi, è necessario codificarli con il simbolo %20. Vedere l'esempio precedente.<br>Lunghezza massima: 50 |
| buildType | Tipo della compilazione da richiamare: <br/> - "Recommendation" per una compilazione di raccomandazioni <br> - "Ranking" per una compilazione della classifica <br/> - "Fbt" per una compilazione FBT
| apiVersion | 1\.0 |
||| 
| Corpo della richiesta | Se lasciato vuoto, la compilazione verrà eseguita con i parametri di compilazione predefiniti.<br><br>Per impostare i parametri di compilazione, inviarli come XML nel corpo, come nell'esempio seguente. (Per la descrizione e l'elenco completo dei parametri, vedere la sezione "Parametri della compilazione".)`<BuildParametersList><NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance></BuildParametersList>` |

**Risposta**:

Codice stato HTTP: 200

Questa è un'API asincrona. Come risposta si otterrà un ID compilazione. Per sapere quando termina l'esecuzione della compilazione, è necessario chiamare l'API "Get Builds Status of a Model" e individuare l'ID compilazione nella risposta. Tenere presente che una compilazione può richiedere minuti o ore, a seconda delle dimensioni dei dati.

Non è possibile usare raccomandazioni finché la compilazione non viene completata.

Stati di compilazione validi:

- Create: il modello è stato creato.
- Queued: la compilazione del modello è stata attivata ed è in coda.
- Building: la compilazione del modello è in corso.
- Success: la compilazione è stata completata.
- Error: la compilazione è terminata con un errore.
- Cancelled: la compilazione è stata annullata.
- Cancelling: è in corso l'annullamento della compilazione.

È possibile trovare l'ID compilazione nel percorso seguente: `Feed\entry\content\properties\Id`

XML OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
  	</entry>
	</feed>




###11\.4. Ottenere lo stato delle compilazioni di un modello
Recupera le compilazioni e il relativo stato per un modello specifico.

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`|


|	Nome parametro |	Valori validi |
|:--------			|:--------								|
|	modelId |	Identificatore univoco del modello. |
|	onlyLastBuild |	Indica se restituire l'intera cronologia di compilazioni del modello o solo lo stato della compilazione più recente. |
|	apiVersion |	1\.0 |


**Risposta**:

Codice stato HTTP: 200

La risposta include una voce per ogni compilazione. Ogni voce include i dati seguenti:

- `feed/entry/content/properties/UserName`: nome dell'utente.
- `feed/entry/content/properties/ModelName`: nome del modello.
- `feed/entry/content/properties/ModelId`: identificatore univoco del modello.
- `feed/entry/content/properties/IsDeployed`: indica se la compilazione viene distribuita (nota anche come compilazione attiva).
- `feed/entry/content/properties/BuildId`: identificatore univoco della compilazione.
- `feed/entry/content/properties/BuildType`: tipo della compilazione.
- `feed/entry/content/properties/Status`: stato della compilazione. Può essere uno dei seguenti: Error, Building, Queued, Cancelling, Cancelled, Success.
- `feed/entry/content/properties/StatusMessage`: messaggio di stato dettagliato (si applica solo a stati specifici).
- `feed/entry/content/properties/Progress`: stato di avanzamento della compilazione (%).
- `feed/entry/content/properties/StartTime`: ora di inizio della compilazione.
- `feed/entry/content/properties/EndTime`: ora di fine della compilazione.
- `feed/entry/content/properties/ExecutionTime`: durata della compilazione.
- `feed/entry/content/properties/ProgressStep`: dettagli relativi alla fase corrente di una compilazione in corso.

Stati di compilazione validi: - Created: la voce della richiesta di compilazione è stata creata. - Queued: la richiesta di compilazione è stata creata ed è in coda. - Building: il processo di compilazione è in corso. - Success: la compilazione è stata completata. - Error: la compilazione è terminata con un errore. - Cancelled: la compilazione è stata annullata. - Cancelling: è in corso l'annullamento della compilazione.

Valori validi per il tipo di compilazione: - Rank: compilazione della classifica. - Recommendation: compilazione di raccomandazioni.


XML OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get builds status of a model</subtitle>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-05T17:51:10Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
		<entry>
    		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildsStatusEntity</title>
			<updated>2014-11-05T17:51:10Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
					<d:ModelName m:type="Edm.String">ModelName</d:ModelName>
					<d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
					<d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
					<d:BuildId m:type="Edm.String">1000272</d:BuildId>
                    <d:BuildType m:type="Edm.String">Recommendation</d:BuildType>
					<d:Status m:type="Edm.String">Success</d:Status>
					<d:StatusMessage m:type="Edm.String"></d:StatusMessage>
					<d:Progress m:type="Edm.String">0</d:Progress>
					<d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
					<d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
					<d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
					<d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
					<d:ProgressStep m:type="Edm.String"></d:ProgressStep>
				</m:properties>
			</content>
		</entry>
	</feed>


###11\.5. Ottenere lo stato delle compilazioni
Recupera lo stato delle compilazioni di tutti i modelli di un utente.

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=<bool>&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=true&apiVersion=%271.0%27`|


|	Nome parametro |	Valori validi |
|:--------			|:--------								|
|	onlyLastBuild |	Indica se restituire l'intera cronologia di compilazioni del modello o solo lo stato della compilazione più recente. |
|	apiVersion |	1\.0 |


**Risposta**:

Codice stato HTTP: 200

La risposta include una voce per ogni compilazione. Ogni voce include i dati seguenti:

- `feed/entry/content/properties/UserName`: nome dell'utente.
- `feed/entry/content/properties/ModelName`: nome del modello.
- `feed/entry/content/properties/ModelId`: identificatore univoco del modello.
- `feed/entry/content/properties/IsDeployed`: indica se la compilazione viene distribuita.
- `feed/entry/content/properties/BuildId`: identificatore univoco della compilazione.
- `feed/entry/content/properties/BuildType`: tipo della compilazione.
- `feed/entry/content/properties/Status`: stato della compilazione. Può essere uno dei seguenti: Error, Building, Queued, Cancelled, Cancelling, Success.
- `feed/entry/content/properties/StatusMessage`: messaggio di stato dettagliato (si applica solo a stati specifici).
- `feed/entry/content/properties/Progress`: stato di avanzamento della compilazione (%).
- `feed/entry/content/properties/StartTime`: ora di inizio della compilazione.
- `feed/entry/content/properties/EndTime`: ora di fine della compilazione.
- `feed/entry/content/properties/ExecutionTime`: durata della compilazione.
- `feed/entry/content/properties/ProgressStep`: dettagli relativi alla fase corrente di una compilazione in corso.

Stati di compilazione validi: - Created: la voce della richiesta di compilazione è stata creata. - Queued: la richiesta di compilazione è stata creata ed è in coda. - Building: il processo di compilazione è in corso. - Success: la compilazione è stata completata. - Error: la compilazione è terminata con un errore. - Cancelled: la compilazione è stata annullata. - Cancelling: è in corso l'annullamento della compilazione.


Valori validi per il tipo di compilazione: - Rank: compilazione della classifica. - Recommendation: compilazione di raccomandazioni.


XML OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get builds status of a user</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-05T18:41:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildsStatusEntity</title>
			<updated>2014-11-05T18:41:21Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
					<d:ModelName m:type="Edm.String">ModelName</d:ModelName>
					<d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
					<d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
					<d:BuildId m:type="Edm.String">1000272</d:BuildId>
                    <d:BuildType m:type="Edm.String">Recommendation</d:BuildType>
					<d:Status m:type="Edm.String">Success</d:Status>
					<d:StatusMessage m:type="Edm.String"></d:StatusMessage>
					<d:Progress m:type="Edm.String">0</d:Progress>
					<d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
					<d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
					<d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
					<d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
					<d:ProgressStep m:type="Edm.String"></d:ProgressStep>
				</m:properties>
			</content>
		</entry>
	</feed>


###11\.6. Eliminare una compilazione
Elimina una compilazione.

NOTA: <br>Non è possibile eliminare una compilazione attiva. Per poterla eliminare, è necessario aggiornare il modello a una compilazione attiva diversa.<br>Non è possibile eliminare una compilazione in corso. È necessario annullare prima la compilazione chiamando <strong>Cancel Build</strong>.

| Metodo HTTP | URI |
|:--------|:--------|
|DELETE |`<rootURI>/DeleteBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/DeleteBuild?buildId=%271500068%27&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
| buildId | Identificatore univoco della compilazione. |
| apiVersion | 1\.0 |

**Risposta:**

Codice stato HTTP: 200

###11\.7. Annullare una compilazione
Annulla una compilazione nello stato di creazione.

| Metodo HTTP | URI |
|:--------|:--------|
|PUT |`<rootURI>/CancelBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/CancelBuild?buildId=%271500076%27&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
| buildId | Identificatore univoco della compilazione. |
| apiVersion | 1\.0 |

**Risposta:**

Codice stato HTTP: 200

###11\.8. Ottenere i parametri della compilazione
Recupera i parametri della compilazione.

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetBuildParameters?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/GetBuildParameters?buildId=%271000653%27&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
| buildId | Identificatore univoco della compilazione. |
| apiVersion | 1\.0 |

**Risposta:**

Codice stato HTTP: 200

L'API restituisce una raccolta di elementi chiave/valore. Ogni elemento rappresenta un parametro e il relativo valore: - `feed/entry/content/properties/Key`, nome del parametro di compilazione. - `feed/entry/content/properties/Value`, valore del parametro di compilazione.

La tabella seguente illustra il valore rappresentato da ogni chiave.

|Chiave|Descrizione|Tipo|Valore valido|
|:-----|:----|:----|:---|
|NumberOfModelIterations | Il numero di iterazioni eseguite dal modello viene riflesso dal tempo di calcolo complessivo e dall'accuratezza del modello. A un numero più alto corrisponderà una migliore accuratezza, ma il tempo di calcolo sarà maggiore.| Integer | 10-50 |
| NumberOfModelDimensions | Il numero di dimensioni è correlato al numero di "funzionalità" che il modello proverà a trovare nei dati. L'aumento del numero di dimensioni consentirà l'ottimizzazione dei risultati in cluster più piccoli. Troppe dimensioni impediranno tuttavia al modello di trovare correlazioni tra gli elementi. | Integer | 10-40 |
|ItemCutOffLowerBound| Definisce il limite minimo dell'elemento per la concentrazione. Vedere la sezione precedente relativa alla concentrazione dei dati di utilizzo. | Integer | 2 o più (0 disabilita la concentrazione) |
|ItemCutOffUpperBound| Definisce il limite massimo dell'elemento per la concentrazione. Vedere la sezione precedente relativa alla concentrazione dei dati di utilizzo. | Integer | 2 o più (0 disabilita la concentrazione) |
|UserCutOffLowerBound| Definisce il limite minimo dell'utente per la concentrazione. Vedere la sezione precedente relativa alla concentrazione dei dati di utilizzo. | Integer | 2 o più (0 disabilita la concentrazione) |
|UserCutOffUpperBound| Definisce il limite massimo dell'utente per la concentrazione. Vedere la sezione precedente relativa alla concentrazione dei dati di utilizzo. | Integer | 2 o più (0 disabilita la concentrazione) |
| Descrizione | Descrizione della compilazione. | String | Qualsiasi testo, con un massimo di 512 caratteri |
| EnableModelingInsights | Consente di calcolare la metrica nel modello di raccomandazione. | Boolean | True/False |
| UseFeaturesInModel | Indica se le funzionalità possono essere usate in ordine per migliorare il modello di raccomandazione. | Boolean | True/False |
| ModelingFeatureList | Elenco con valori delimitati da virgole dei nomi di funzionalità da usare nella compilazione di raccomandazioni, allo scopo di migliorare la raccomandazione. | String | Nomi di funzionalità, con un massimo di 512 caratteri |
| AllowColdItemPlacement | Indica se la raccomandazione dovrà effettuare il push anche degli elementi ignoti in base alla somiglianza di funzionalità. | Boolean | True/False |
| EnableFeatureCorrelation | Indica se le funzionalità possono essere usate nella motivazione. | Boolean | True/False |
| ReasoningFeatureList | Elenco con valori delimitati da virgole dei nomi delle funzionalità da usare nelle frasi relative alla motivazione (ad esempio, le spiegazioni delle raccomandazioni). | String | Nomi di funzionalità, con un massimo di 512 caratteri |


XML OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get build parameters</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2015-01-08T13:50:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UseFeaturesInModel</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">AllowColdItemPlacement</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">EnableFeatureCorrelation</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">EnableModelingInsights</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">NumberOfModelIterations</d:Key>
					<d:Value m:type="Edm.String">10</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
			<titletype="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">NumberOfModelDimensions</d:Key>
					<d:Value m:type="Edm.String">10</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<linkrel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ItemCutOffLowerBound</d:Key>
					<d:Value m:type="Edm.String">2</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ItemCutOffUpperBound</d:Key>
					<d:Value m:type="Edm.String">2147483647</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UserCutOffLowerBound</d:Key>
					<d:Value m:type="Edm.String">2</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UserCutOffUpperBound</d:Key>
					<d:Value m:type="Edm.String">2147483647</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ModelingFeatureList</d:Key>
					<d:Value m:type="Edm.String"/>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ReasoningFeatureList</d:Key>
					<d:Value m:type="Edm.String"/>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">Description</d:Key>
					<d:Value m:type="Edm.String">rankBuild</d:Value>
				</m:properties>
			</content>
		</entry>
	</feed>

##12\. Raccomandazione
###12\.1. Ottenere le raccomandazioni degli elementi (per la compilazione attiva)

Ottenere le raccomandazioni della compilazione attiva di tipo "Recommendation" o "Fbt" in base a un elenco di elementi seeds (input).

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
| modelId | Identificatore univoco del modello. |
| itemIds | Elenco con valori delimitati da virgole degli elementi per i quali aggiungere raccomandazioni. <br>Se la compilazione attiva è di tipo FBT, è possibile inviare un solo elemento. <br>Lunghezza massima: 1024 |
| numberOfResults | Numero di risultati richiesti. |
| includeMetatadata | Uso futuro, sempre false. |
| apiVersion | 1\.0 |

**Risposta:**

Codice stato HTTP: 200


La risposta include una voce per ogni elemento raccomandato. Per ogni voce sono presenti i dati seguenti: - `Feed\entry\content\properties\Id`, ID dell'elemento raccomandato. - `Feed\entry\content\properties\Name`, nome dell'elemento. - `Feed\entry\content\properties\Rating`, classificazione della raccomandazione; un numero più alto significa maggiore confidenza. - `Feed\entry\content\properties\Reasoning`, motivazione della raccomandazione (ad esempio, spiegazioni delle raccomandazioni).

La risposta di esempio seguente include 10 elementi raccomandati.

XML OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">159</d:Id>
        <d:Name m:type="Edm.String">159</d:Name>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '159'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">52</d:Id>
        <d:Name m:type="Edm.String">52</d:Name>
        <d:Rating m:type="Edm.Double">0.539588900748721</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '52'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">35</d:Id>
        <d:Name m:type="Edm.String">35</d:Name>
        <d:Rating m:type="Edm.Double">0.53842946443853</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '35'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">124</d:Id>
        <d:Name m:type="Edm.String">124</d:Name>
        <d:Rating m:type="Edm.Double">0.536712832792886</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '124'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">120</d:Id>
        <d:Name m:type="Edm.String">120</d:Name>
        <d:Rating m:type="Edm.Double">0.533673023762878</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '120'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">96</d:Id>
        <d:Name m:type="Edm.String">96</d:Name>
        <d:Rating m:type="Edm.Double">0.532544826370521</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '96'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">69</d:Id>
        <d:Name m:type="Edm.String">69</d:Name>
        <d:Rating m:type="Edm.Double">0.531678607847759</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '69'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">172</d:Id>
        <d:Name m:type="Edm.String">172</d:Name>
        <d:Rating m:type="Edm.Double">0.530957821375951</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '172'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">155</d:Id>
        <d:Name m:type="Edm.String">155</d:Name>
        <d:Rating m:type="Edm.Double">0.529093541481333</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '155'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">32</d:Id>
        <d:Name m:type="Edm.String">32</d:Name>
        <d:Rating m:type="Edm.Double">0.528917978168322</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '32'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
	</feed>

###12\.2. Ottenere le raccomandazioni degli elementi (di una compilazione specifica)

Ottiene raccomandazioni di una compilazione specifica di tipo "Recommendation" o "Fbt".

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=1234&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
| modelId | Identificatore univoco del modello. |
| itemIds | Elenco con valori delimitati da virgole degli elementi per i quali aggiungere raccomandazioni. <br>Se la compilazione attiva è di tipo FBT, è possibile inviare un solo elemento. <br>Lunghezza massima: 1024 |
| numberOfResults | Numero di risultati richiesti. |
| includeMetatadata | Uso futuro, sempre false.
| buildId | L'ID compilazione da usare per questa richiesta di raccomandazioni. |
| apiVersion | 1\.0 |

**Risposta:**

Codice stato HTTP: 200


La risposta include una voce per ogni elemento raccomandato. Per ogni voce sono presenti i dati seguenti: - `Feed\entry\content\properties\Id`, ID dell'elemento raccomandato. - `Feed\entry\content\properties\Name`, nome dell'elemento. - `Feed\entry\content\properties\Rating`, classificazione della raccomandazione; un numero più alto significa maggiore confidenza. - `Feed\entry\content\properties\Reasoning`, motivazione della raccomandazione (ad esempio, spiegazioni delle raccomandazioni).

Vedere un esempio di risposta nella sezione 12.1.

###12\.3. Ottenere le raccomandazioni FBT (per la compilazione attiva)

Ottiene le raccomandazioni della compilazione attiva di tipo "Fbt" in base a un elemento seed (input).

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=<double>&includeMetadata=false&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
| modelId | Identificatore univoco del modello. |
| itemId | Elemento per il quale aggiungere raccomandazioni. <br>Lunghezza massima: 1024 |
| numberOfResults | Numero di risultati richiesti. |
| minimalScore | Punteggio minimo che un set frequente deve avere per essere incluso nei risultati restituiti. |
| includeMetatadata | Uso futuro, sempre false. |
| apiVersion | 1\.0 |

**Risposta:**

Codice stato HTTP: 200


La risposta include una voce per ogni set di elementi consigliati (un set di elementi che in genere vengono acquistati con l'elemento del valore di inizializzazione/input). Ogni voce dispone dei dati seguenti: - `Feed\entry\content\properties\Id1`: ID elemento consigliato. - `Feed\entry\content\properties\Name1`: nome dell'elemento. - `Feed\entry\content\properties\Id2`: ID 2° elemento consigliato (facoltativo). - `Feed\entry\content\properties\Name2`: nome del 2° elemento (facoltativo). - `Feed\entry\content\properties\Rating`: valutazione della raccomandazione; più elevato è il numero, maggiore è la confidenza. - `Feed\entry\content\properties\Reasoning`: motivo della raccomandazione (ad esempio, spiegazioni per la raccomandazione).

La risposta di esempio seguente include tre set di elementi consigliati.

XML OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemId='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">159</d:Id1>
        <d:Name1 m:type="Edm.String">159</d:Name1>
		<d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '159'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">52</d:Id1>
        <d:Name1 m:type="Edm.String">52</d:Name1>
		<d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.533343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '52'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">35</d:Id1>
        <d:Name1 m:type="Edm.String">35</d:Name1>
		<d:Id2 m:type="Edm.String">102</d:Id2>
        <d:Name2 m:type="Edm.String">102</d:Name2>
        <d:Rating m:type="Edm.Double">0.523343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '35' and '102'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
	</feed>

###12\.4. Ottenere le raccomandazioni FBT (di una compilazione specifica)

Ottenere le raccomandazioni di una compilazione specifica di tipo "Fbt".

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=0.1&includeMetadata=false&buildId=1234&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
| modelId | Identificatore univoco del modello. |
| itemId | Elemento per il quale aggiungere raccomandazioni. <br>Lunghezza massima: 1024 |
| numberOfResults | Numero di risultati richiesti. |
| minimalScore | Punteggio minimo che un set frequente deve avere per essere incluso nei risultati restituiti. |
| includeMetatadata | Uso futuro, sempre false. |
| buildId | L'ID compilazione da usare per questa richiesta di raccomandazioni. |
| apiVersion | 1\.0 |

**Risposta:**

Codice stato HTTP: 200


La risposta include una voce per ogni set di elementi consigliati (un set di elementi che in genere vengono acquistati con l'elemento del valore di inizializzazione/input). Ogni voce dispone dei dati seguenti: - `Feed\entry\content\properties\Id1`: ID elemento consigliato. - `Feed\entry\content\properties\Name1`: nome dell'elemento. - `Feed\entry\content\properties\Id2`: ID 2° elemento consigliato (facoltativo). - `Feed\entry\content\properties\Name2`: nome del 2° elemento (facoltativo). - `Feed\entry\content\properties\Rating`: valutazione della raccomandazione; più elevato è il numero, maggiore è la confidenza. - `Feed\entry\content\properties\Reasoning`: motivo della raccomandazione (ad esempio, spiegazioni per la raccomandazione).

Vedere un esempio di risposta nella sezione 12.3.

###12\.5. Ottenere le raccomandazioni utente (per la compilazione attiva)

Ottenere le raccomandazioni utente di una compilazione di tipo "Recommendation" contrassegnata come compilazione attiva.

L'API restituirà un elenco di elementi stimati secondo la cronologia di utilizzo dell'utente.

Note: 1. Non esiste alcuna raccomandazione utente per la compilazione FBT. 2. Se la compilazione attiva è FBT, questo metodo restituisce un errore.

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
| modelId | Identificatore univoco del modello. |
| userId | Identificatore univoco dell'utente |
| numberOfResults | Numero di risultati richiesti. |
| includeMetatadata | Uso futuro, sempre false. |
| apiVersion | 1\.0 |

**Risposta:**

Codice stato HTTP: 200


La risposta include una voce per ogni elemento raccomandato. Per ogni voce sono presenti i dati seguenti: - `Feed\entry\content\properties\Id`, ID dell'elemento raccomandato. - `Feed\entry\content\properties\Name`, nome dell'elemento. - `Feed\entry\content\properties\Rating`, classificazione della raccomandazione; un numero più alto significa maggiore confidenza. - `Feed\entry\content\properties\Reasoning`, motivazione della raccomandazione (ad esempio, spiegazioni delle raccomandazioni).

Vedere un esempio di risposta nella sezione 12.1.

###12\.6. Ottenere le raccomandazioni utente con l'elenco di elementi (per la compilazione attiva)

Ottenere raccomandazioni utente di una compilazione di tipo "Recommendation" contrassegnata come compilazione attiva con un elenco aggiuntivo di elementi

L'API restituirà un elenco di elementi stimati secondo la cronologia di utilizzo dell'utente e gli elementi stimati aggiuntivi.

Note: 1. Non esiste alcuna raccomandazione utente per la compilazione FBT. 2. Se la compilazione attiva è FBT, questo metodo restituisce un errore.


| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>&itemsIds=%27<itemsIds>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemsIds=%271003%2C1000%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|	Nome parametro |	Valori validi |
|:--------			|:--------								|
| modelId | Identificatore univoco del modello. |
| userId | Identificatore univoco dell'utente |
| itemIds | Elenco con valori delimitati da virgole degli elementi per i quali aggiungere raccomandazioni. Lunghezza massima: 1024 |
| numberOfResults | Numero di risultati richiesti. |
| includeMetatadata | Uso futuro, sempre false. |
| apiVersion | 1\.0 |

**Risposta:**

Codice stato HTTP: 200


La risposta include una voce per ogni elemento raccomandato. Per ogni voce sono presenti i dati seguenti: - `Feed\entry\content\properties\Id`, ID dell'elemento raccomandato. - `Feed\entry\content\properties\Name`, nome dell'elemento. - `Feed\entry\content\properties\Rating`, classificazione della raccomandazione; un numero più alto significa maggiore confidenza. - `Feed\entry\content\properties\Reasoning`, motivazione della raccomandazione (ad esempio, spiegazioni delle raccomandazioni).

Vedere un esempio di risposta nella sezione 12.1.

###12\.7. Ottenere le raccomandazioni utente (di una compilazione specifica)

Ottenere le raccomandazioni utente di una compilazione specifica di tipo "Recommendation".

L'API restituirà un elenco di elementi stimati secondo la cronologia di utilizzo dell'utente (usato nella compilazione specifica).

Nota: non esiste alcuna raccomandazione utente per la compilazione FBT.

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27`|


|	Nome parametro |	Valori validi |
|:--------			|:--------								|
| modelId | Identificatore univoco del modello. |
| userId | Identificatore univoco dell'utente |
| numberOfResults | Numero di risultati richiesti. |
| includeMetatadata | Uso futuro, sempre false. |
| buildId | L'ID compilazione da usare per questa richiesta di raccomandazioni. |
| apiVersion | 1\.0 |

**Risposta:**

Codice stato HTTP: 200


La risposta include una voce per ogni elemento raccomandato. Per ogni voce sono presenti i dati seguenti: - `Feed\entry\content\properties\Id`, ID dell'elemento raccomandato. - `Feed\entry\content\properties\Name`, nome dell'elemento. - `Feed\entry\content\properties\Rating`, classificazione della raccomandazione; un numero più alto significa maggiore confidenza. - `Feed\entry\content\properties\Reasoning`, motivazione della raccomandazione (ad esempio, spiegazioni delle raccomandazioni).

Vedere un esempio di risposta nella sezione 12.1.


###12\.8. Ottenere le raccomandazioni utente con l'elenco di elementi (di una compilazione specifica)

Ottenere le raccomandazioni utente di una compilazione specifica di tipo "Recommendation" e l'elenco di elementi aggiuntivi.

L'API restituirà un elenco di elementi stimati secondo la cronologia di utilizzo dell'utente e l'elenco aggiuntivo di elementi.

Nota: non esiste alcuna raccomandazione utente per la compilazione FBT.

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&itemsIds=%27<itemsIds>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemsIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27`|



|	Nome parametro |	Valori validi |
|:--------			|:--------								|
| modelId | Identificatore univoco del modello. |
| userId | Identificatore univoco dell'utente |
| itemIds | Elenco con valori delimitati da virgole degli elementi per i quali aggiungere raccomandazioni. Lunghezza massima: 1024 |
| numberOfResults | Numero di risultati richiesti. |
| includeMetatadata | Uso futuro, sempre false. |
| buildId | L'ID compilazione da usare per questa richiesta di raccomandazioni. |
| apiVersion | 1\.0 |

**Risposta:**

Codice stato HTTP: 200


La risposta include una voce per ogni elemento raccomandato. Per ogni voce sono presenti i dati seguenti: - `Feed\entry\content\properties\Id`, ID dell'elemento raccomandato. - `Feed\entry\content\properties\Name`, nome dell'elemento. - `Feed\entry\content\properties\Rating`, classificazione della raccomandazione; un numero più alto significa maggiore confidenza. - `Feed\entry\content\properties\Reasoning`, motivazione della raccomandazione (ad esempio, spiegazioni delle raccomandazioni).

Vedere un esempio di risposta nella sezione 12.1.

##13\. Cronologia utilizzo utente
Una volta compilato un modello di raccomandazione, il sistema consentirà di recuperare la cronologia utente (elementi associati a un utente specifico) usata per la compilazione. Questa API consente di recuperare la cronologia utente

Nota: la cronologia utente è attualmente disponibile solo per le compilazioni raccomandazione.

###13\.1 Recuperare la cronologia utente
Recuperare l'elenco di elementi usati nella compilazione attiva o nella compilazione specificata per l'ID utente specificato.

| Metodo HTTP | URI |
|:--------|:--------|
|GET | Ottenere la cronologia utente per la compilazione attiva.<br/>`<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&apiVersion=%271.0%27`<br/><br/>Ottenere la cronologia utente per la compilazione specificata `<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&buildId=<int>&apiVersion=%271.0%27`<br/><br/>Esempio:`<rootURI>/GetUserHistory?modelId=%2727967136e8-f868-4258-9331-10d567f87fae%27&&userId=%27u_1013%27&apiVersion=%271.0%277`|


|	Nome parametro |	Valori validi |
|:--------			|:--------								|
| modelId | Identificatore univoco del modello.|
| userId | Identificatore univoco dell'utente.
| buildId | Parametro facoltativo, consente di indicare da quale compilazione deve essere recuperata la cronologia utente
| apiVersion | 1\.0 |


**Risposta:**

Codice stato HTTP: 200

La risposta include una voce per ogni elemento raccomandato. Ogni voce include i dati seguenti: - `Feed\entry\content\properties\Id`: ID elemento consigliato. - `Feed\entry\content\properties\Name`: nome dell'elemento. - `Feed\entry\content\properties\Rating`: N/D. - `Feed\entry\content\properties\Reasoning`: N/D.

XML OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get User History</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2015-05-26T15:32:47Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">CatalogItemsThatContainATokenEntity</title>
		<updated>2015-05-26T15:32:47Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
				<d:Name m:type="Edm.String">Clara Callan</d:Name>
				<d:Rating m:type="Edm.Double">0</d:Rating>
				<d:Reasoning m:type="Edm.String"/>
				<d:Metadata m:type="Edm.String"/>
				<d:FormattedRating m:type="Edm.Double" m:null="true"/>
			</m:properties>
		</content>
	</entry>
</feed>

##14\. Notifiche
L'API Recommendations di Azure Machine Learning crea notifiche quando nel sistema si verificano errori persistenti. Esistono tre tipi di notifica: 1. Build failure: questa notifica viene attivata per ogni errore di compilazione. 2. Data acquisition processing failure: questa notifica viene attivata quando, durante l'elaborazione degli eventi di utilizzo per ogni modello, si verificano più di 100 errori nell'arco di cinque minuti. 3. Recommendation consumption failure: questa notifica viene attivata quando, durante l'elaborazione delle richieste di raccomandazione per ogni modello, si verificano più di 100 errori nell'arco di cinque minuti.


###14\.1. Ottenere notifiche
Recupera tutte le notifiche relative a tutti i modelli o a un singolo modello.

| Metodo HTTP | URI |
|:--------|:--------|
|GET |`<rootURI>/GetNotifications?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Ottenere tutte le notifiche per tutti i modelli:<br>`<rootURI>/GetNotifications?apiVersion=%271.0%27`<br><br>Esempio per ottenere notifiche per un modello specifico:<br>`<rootURI>/GetNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%277`|


|	Nome parametro |	Valori validi |
|:--------			|:--------								|
| modelId | Parametro facoltativo. Se omesso, vengono restituite tutte le notifiche relative a tutti i modelli. <br>Valore valido: identificatore univoco del modello.|
| apiVersion | 1.0 |
||| 
| Corpo della richiesta | NESSUNO |

**Risposta:**

Codice stato HTTP: 200

XML OData

    The response includes one entry per notification. Each entry has the following data:
		* feed\entry\content\properties\UserName – Internal user name identification.
		* feed\entry\content\properties\ModelId – Model ID.
		* feed\entry\content\properties\Message – Notification message.
		* feed\entry\content\properties\DateCreated – Date that this notification was created in UTC format.
		* feed\entry\content\properties\NotificationType – Notification types. Values are BuildFailure, RecommendationFailure, and DataAquisitionFailure.

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get a list of Notifications for a user</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-04T13:24:23Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'" />
		<entry>
				<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfNotificationsForAUserEntity</title>
			<updated>2014-11-04T13:24:23Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:UserName m:type="Edm.String">515506bc-3693-4dce-a5e2-81cb3e8efb56@dm.com</d:UserName>
					<d:ModelId m:type="Edm.String">967136e8-f868-4258-9331-10d567f87fae</d:ModelId>
					<d:Message m:type="Edm.String">Build failed for user</d:Message>
					<d:DateCreated m:type="Edm.String">2014-11-04T13:23:14.383</d:DateCreated>
					<d:NotificationType m:type="Edm.String">BuildFailure</d:NotificationType>
				</m:properties>
			</content>
		</entry>
	</feed>

###14\.2. Eliminare le notifiche di modello
Elimina tutte le notifiche di lettura relative a un modello.

| Metodo HTTP | URI |
|:--------|:--------|
|DELETE |`<rootURI>/DeleteModelNotifications?modelId=%<model_id>%27&apiVersion=%271.0%27`<br><br>Esempio:<br>`<rootURI>/DeleteModelNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%27`|


|	Nome parametro |	Valori validi |
|:--------			|:--------								|
| modelId | Identificatore univoco del modello. |
| apiVersion | 1.0 |
||| 
| Corpo della richiesta | NESSUNO |

**Risposta**:

Codice stato HTTP: 200

###14\.3. Eliminare le notifiche utente
Elimina tutte le notifiche relative a tutti i modelli.

| Metodo HTTP | URI |
|:--------|:--------|
|DELETE |`<rootURI>/DeleteUserNotifications?apiVersion=%271.0%27`|


|	Nome parametro |	Valori validi |
|:--------			|:--------								|
| apiVersion | 1.0 |
||| 
| Corpo della richiesta | NESSUNO |

**Risposta**:

Codice stato HTTP: 200




##15\. Note legali
Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Web Internet, sono soggette a modifica senza preavviso.<br><br> Alcuni esempi usati in questo documento vengono forniti a scopo puramente illustrativo e sono fittizi. Nessuna associazione reale o connessione è intenzionale o può essere desunta.<br><br> Il presente documento non fornisce all'utente alcun diritto legale rispetto a qualsiasi proprietà intellettuale in qualsiasi prodotto Microsoft. È possibile copiare e usare il presente documento per scopi interni e di riferimento.<br><br> © 2015 Microsoft. Tutti i diritti sono riservati.
 

<!---HONumber=AcomDC_1217_2015-->