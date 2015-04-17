<properties 
   pageTitle="Uso del connettore SharePoint nell'app per la logica" 
   description="Uso del connettore SharePoint nell'app per la logica" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="vagarw"/>

# Uso del connettore SharePoint nell'app per la logica

Le app per la logica possono eseguire un'attivazione in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso. Il connettore Microsoft SharePoint consente di connettersi a Microsoft SharePoint Server o SharePoint Online e gestire documenti ed elementi elenco. È possibile eseguire varie azioni, ad esempio creare, aggiornare, ottenere ed eliminare documenti ed elementi elenco. Nel caso di un ambiente SharePoint Server locale, è possibile fornire la stringa di connessione per il bus di servizio come parte della configurazione del connettore e installare l'agente listener locale per la connessione al server.

Il connettore SharePoint Online e l'app della raccolta SharePoint Server Connector forniscono trigger e azioni come meccanismi per interagire con SharePoint.

## Creazione di un connettore SharePoint Online per l'app per la logica

Per usare il connettore SharePoint Online, prima è necessario creare un'istanza dell'app per le API SharePoint Online Connector. Attenersi alla procedura riportata di seguito:

1. Aprire Azure Marketplace con l'opzione + NUOVO in basso a destra nel portale di Azure.

2. Andare a "Web e dispositivi mobili > App per le API" e cercare "SharePoint Online Connector".

3. Configurare il connettore SharePoint Online e fare clic su Crea. Di seguito sono elencati i parametri che è necessario fornire per creare il connettore:

	<table>
	  <tr>
	    <td><b>Name</b></td>
	    <td><b>Required</b></td>
	    <td><b>Description</b></td>
	  </tr>
	  <tr>
	    <td>Site URL</td>
	    <td>Yes</td>
	    <td>Specify the complete URL of the SharePoint web site. Ex: https://microsoft.sharepoint.com/teams/wabstest </td>
	  </tr>
	  <tr>
	    <td>Document Library / List Relative URLs</td>
	    <td>Yes</td>
	    <td>Specify the document libraries/lists URLs, relative to the SharePoint site URL, that are allowed to be modified by the connector. Ex: Lists/Task, Shared Documents'.</td>
	  </tr>
	</table>
	![][1]


4. A questo punto è possibile creare un'app per la logica nello stesso gruppo di risorse per usare il connettore SharePoint Online.

## Creazione di un connettore SharePoint Server per l'app per la logica

Per usare il connettore SharePoint Server, prima è necessario creare un'istanza dell'app per le API SharePoint Server Connector. Attenersi alla procedura riportata di seguito:

1. Aprire Azure Marketplace con l'opzione + NUOVO in basso a destra nel portale di Azure.

2. Andare a "Web e dispositivi mobili > App per le API" e cercare "SharePoint Server Connector".

3. Configurare il connettore SharePoint Server e fare clic su Crea. Di seguito sono elencati i parametri che è necessario fornire per creare il connettore:

	<table>
	  <tr>
	    <td><b>Nome</b></td>
	    <td><b>Obbligatorio</b></td>
	    <td><b>Descrizione</b></td>
	  </tr>
	  <tr>
	    <td>URL sito</td>
	    <td>Sì</td>
	    <td>Specificare l'URL completo del sito Web di SharePoint. Esempio:: https://microsoft.sharepoint.com/teams/wabstest </td>
	  </tr>
	  <tr>
	    <td>Modalità di autenticazione</td>
	    <td>Sì</td>
	    <td>Specificare la modalità di autenticazione per la connessione al sito di SharePoint. I valori consentiti sono:<br><br>
			Default<br>
			OAuth2<br>
			WindowsAuthentication<br>
			FormBasedAuthentication.<br><br>
	
	Scegliendo il valore Default vengono usate le credenziali predefinite per l'esecuzione del microservizio SharePoint e non è necessario inserire nome utente e password. I campi Nome utente e Password sono obbligatori per altri tipi di autenticazione. <br><br>Nota: l'autenticazione anonima non è supportata.</td>
	  </tr>
	  <tr>
	    <td>Nome utente</td>
	    <td>No</td>
	    <td>Specificare un nome utente valido per la connessione al sito di SharePoint, se la modalità di autenticazione non è Default/OAuth2.</td>
	  </tr>
	  <tr>
	    <td>Password</td>
	    <td>No</td>
	    <td>Specificare una password valida per la connessione al sito di SharePoint, se la modalità di autenticazione non è Default/OAuth2.</td>
	  </tr>
	  <tr>
	    <td>URL relativi raccolta/elenco documenti</td>
	    <td>Sì</td>
	    <td>Specificare gli URL degli elenchi o delle raccolte di documenti, relativi all'URL del sito di SharePoint, che possono essere modificati dal connettore. Esempio: Elenchi/attività, documenti condivisi.</td>
	  </tr>
	  <tr>
	    <td>Stringa di connessione bus di servizio</td>
	    <td>No</td>
	    <td>Deve trattarsi di una stringa di connessione valida allo spazio dei nomi del bus di servizio.<br><br>
	
	È necessario installare un agente listener in un server che può accedere a SharePoint Server. <br>Per installare l'agente, passare alla pagina di riepilogo dell'app per le API e fare clic su 'Hybrid Connection'.</td>
	  </tr>
	</table>


	![][2]

4. A questo punto è possibile creare un'app per la logica nello stesso gruppo di risorse per usare il connettore SharePoint Server.
5. È necessario installare un agente listener in un server che può accedere a SharePoint Server. Per installare l'agente, passare alla pagina di riepilogo dell'app per le API e fare clic su 'Hybrid Connection'

## Uso del connettore SharePoint nell'app per la logica

Una volta creata l'app per le API, è possibile usare il connettore SharePoint come trigger o come azione per l'app per la logica. A questo scopo, è necessario:

1. Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse del connettore SharePoint.

2. Aprire "Trigger e azioni" per aprire la finestra di progettazione delle app per la logica e configurare il flusso. Il connettore SharePoint apparirà nella sezione "Usati di recente" nella raccolta sulla destra. Selezionarlo.

3. Se il connettore SharePoint viene selezionato all'avvio dell'app per la logica agisce come trigger, in caso contrario è possibile eseguire azioni sull'account SharePoint con il connettore. 

4. Se si usa il connettore SharePoint Online o se l'autenticazione scelta per il connettore SharePoint Server è OAuth2, sarà necessario autenticare e autorizzare le app per la logica per l'esecuzione di operazioni per conto dell'utente. Per avviare l'autorizzazione, fare clic su Autorizza nel connettore SharePoint. 

	![][3]

5. Facendo clic su Autorizza verrà visualizzata la finestra di dialogo di autenticazione di SharePoint. Specificare i dettagli di accesso dell'account di SharePoint su cui eseguire le operazioni. 

	![][4]
6. Concedere alle app per la logica l'accesso all'account utente per eseguire l'operazione per conto dell'utente. 

	![][5]

7. Se il connettore SharePoint viene configurato come trigger, vengono mostrati i trigger. In caso contrario, viene visualizzato l'elenco delle azioni ed è possibile scegliere l'operazione appropriata da eseguire.  

	![][6]

	<b>URL relativo configurato per la raccolta documenti</b><br><br>

	![][7]

	<b>URL relativo configurato per l'elenco documenti</b>

	<b>Nota:</b> per i trigger di seguito si presuppone che l'utente abbia specificato 'Shared Documents, Lists/Task' nelle Impostazioni pacchetto del connettore, dove 'Shared Documents' è una raccolta documenti e 'Lists/Task' è un elenco. 

##  Trigger
Usare i trigger se si vuole avviare un'app per la logica 

### 1.	Nuovo documento in documenti condivisi (JSON)
Questo trigger viene attivato quando un nuovo documento è disponibile in 'Shared Documents'. 

**Input:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obbligatorio</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>Nome visualizzazione</td>
    <td>No</td>
    <td>Specificare una visualizzazione valida usata per filtrare i documenti da prelevare. Esempio: 'Approved Orders'. Per elaborare tutti i documenti esistenti, lasciare vuoto questo campo. </td>
  </tr>
  <tr>
    <td>Percorso archiviazione</td>
    <td>No</td>
    <td>Specificare un URL di cartella valido, relativo al sito di SharePoint, in cui archiviare i documenti elaborati. </td>
  </tr>
  <tr>
    <td>Sovrascrivi in archivio</td>
    <td>No</td>
    <td>Selezionare questa opzione per sovrascrivere un file nel percorso di archiviazione, se esiste già. </td>
  </tr>
  <tr>
    <td>Query Caml</td>
    <td>No, avanzate</td>
    <td>Specificare una query Caml valida per filtrare i documenti. Esempio: <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>

**Output:**
<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>Nome  </td>
    <td>Nome del documento.</td>
  </tr>
  <tr>
    <td>Contenuto</td>
    <td>Contenuto del documento.</td>
  </tr>
  <tr>
    <td>Codifica trasferimento contenuto</td>
    <td>Codifica di trasferimento contenuto del messaggio. ("none"|"base64")</
  </tr>
</table>


Nota: Tutte le colonne dell'elemento documento vengono riportate nelle proprietà di output 'Advanced'.


###2. Nuovo elemento in attività (JSON)
Questo trigger viene attivato quando viene aggiunto un nuovo elemento all'elenco 'Tasks'.

**Input:**
<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obbligatorio</b></td>
    <td><b>Descrizione</b></td>
 </tr>
  <tr>
    <td>Nome visualizzazione</td>
    <td>No</td>
    <td>Specificare una visualizzazione valida usata per filtrare gli elementi dell'elenco. Esempio: 'Approved Orders'. Per elaborare tutti i nuovi elementi, lasciare vuoto questo campo. </td>
  </tr>
  <tr>
    <td>Percorso archiviazione</td>
    <td>No</td>
    <td>Specificare un URL di cartella valido, relativo al sito di SharePoint, in cui archiviare gli elementi elenco elaborati. </td>
  </tr>
  <tr>
    <td>Query Caml</td>
    <td>No, avanzate</td>
    <td>Specificare una query Caml valida per filtrare gli elementi elenco. Esempio: <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**Output:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>Le colonne nell'elenco vengono popolate in modo dinamico e mostrate nei parametri di output.</td>
    <td> </td>
  </tr>

</table>


###3. Nuovo documento in documenti condivisi (XML)

Questo trigger viene attivato quando un nuovo documento è disponibile in 'Shared Documents'. Il nuovo documento viene restituito come messaggio XML.

**Input:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obbligatorio</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>Nome visualizzazione</td>
    <td>No</td>
    <td>Specificare una visualizzazione valida usata per filtrare i documenti da prelevare. Esempio: 'Approved Orders'. Per elaborare tutti i documenti esistenti, lasciare vuoto questo campo. </td>
  </tr>
  <tr>
    <td>Percorso archiviazione</td>
    <td>No</td>
    <td>Specificare un URL di cartella valido, relativo al sito di SharePoint, in cui archiviare i documenti elaborati. </td>
  </tr>
  <tr>
    <td>Sovrascrivi in archivio</td>
    <td>No</td>
    <td>Selezionare questa opzione per sovrascrivere un file nel percorso di archiviazione, se esiste già. </td>
  </tr>
  <tr>
    <td>Query Caml</td>
    <td>No, avanzate</td>
    <td>Specificare una query Caml valida per filtrare i documenti. Esempio: <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>

**Output:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>Contenuto</td>
    <td>Contenuto del documento.</td>
  </tr>
  <tr>
    <td>Codifica trasferimento contenuto</td>
    <td>Codifica di trasferimento contenuto del messaggio. ("none"|"base64")</td>
  </tr>
</table>


###4. Nuovo elemento in attività (XML)

Questo trigger viene attivato quando viene aggiunto un nuovo elemento all'elenco 'Tasks'. Il nuovo elemento elenco viene restituito come messaggio XML.

**Input:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obbligatorio</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>Nome visualizzazione</td>
    <td>No</td>
    <td>Specificare una visualizzazione valida usata per filtrare gli elementi dell'elenco. Esempio: 'Approved Orders'. Per elaborare tutti i nuovi elementi, lasciare vuoto questo campo. </td>
  </tr>
  <tr>
    <td>Percorso archiviazione</td>
    <td>No</td>
    <td>Specificare un URL di cartella valido, relativo al sito di SharePoint, in cui archiviare gli elementi elenco elaborati. </td>
  </tr>
  <tr>
    <td>Query Caml</td>
    <td>No, avanzate</td>
    <td>Specificare una query Caml valida per filtrare gli elementi elenco. Esempio: <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**Output:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>Contenuto</td>
    <td>Contenuto del documento.</td>
  </tr>
  <tr>
    <td>Codifica trasferimento contenuto</td>
    <td>Codifica di trasferimento contenuto del messaggio. ("none"|"base64")</td>
  </tr>
</table>


##  Azioni
Per le azioni di seguito si presuppone che l'utente abbia specificato 'Shared Documents, Lists/Task' nelle Impostazioni pacchetto del connettore, dove 'Shared Documents' è una raccolta documenti e 'Lists/Task' è un elenco. 

###1. Carica in documenti condivisi (JSON)

Questa azione carica un nuovo documento in 'Shared Documents'. L'input è un oggetto JSON fortemente tipizzato con tutti i campi di colonna della raccolta documenti.

**Input:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obbligatorio</b></td>
    <td><b>Descrizione</b></td>
 </tr>
  <tr>
    <td>Nome</td>
    <td>Sì</td>
    <td>Nome del documento.</td>
  </tr>
  <tr>
    <td>Contenuto</td>
    <td>Sì</td>
    <td>Contenuto del documento.</td>
  </tr>
  <tr>
    <td>Codifica trasferimento contenuto</td>
    <td>Sì</td>
    <td>Codifica di trasferimento contenuto del messaggio. ("none"|"base64")</td>
  </tr>
  <tr>
    <td>Forza sovrascrittura</td>
    <td>Sì</td>
    <td>Se è impostato su TRUE ed esiste già un documento con il nome specificato, verrà sovrascritto.</td>
  </tr>
  <tr>
    <td>Parametro obbligatorio 1*</td>
    <td>Sì</td>
    <td>Uno dei parametri obbligatori per aggiungere un documento nella raccolta documenti.</td>
  </tr>
  <tr>
    <td>Parametro obbligatorio 2*</td>
    <td>Sì</td>
    <td>Uno dei parametri obbligatori per aggiungere un documento nella raccolta documenti.</td>
  </tr>
  <tr>
    <td>Parametro facoltativo 1*</td>
    <td>No. Avanzate</td>
    <td>Uno dei parametri facoltativi per aggiungere un documento nella raccolta documenti.</td>
  </tr>
  <tr>
    <td>Parametro facoltativo 2*</td>
    <td>No. Avanzate</td>
    <td>Uno dei parametri facoltativi per aggiungere un documento nella raccolta documenti.</td>
  </tr>
</table>

<b>Nota:</b> tutti i parametri della raccolta documenti vengono popolati in modo dinamico. I parametri obbligatori sono visibili, mentre i parametri facoltativi sono nella sezione delle proprietà avanzate.


**Output:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>ID elemento</td>
    <td>ID del documento aggiunto nella raccolta documenti.</td>
  </tr>
  <tr>
    <td>Status</td>
    <td>Un caricamento corretto del documento restituisce il codice di stato 200 (OK).</td>
  </tr>
</table>


 

###2. Recupera da documenti condivisi (JSON)
Questa azione recupera il documento dalla raccolta documenti in base all'URL relativo (struttura di cartelle) del documento.


**Input:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obbligatorio</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>URI relativo documento</td>
    <td>No</td>
    <td>Specificare l'URL del documento, relativo a 'Shared Documents'. Esempio: specpersonali1,cartellapersonale/ordini</td>
  </tr>
</table>


**Output:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>Contenuto</td>
    <td>Contenuto del documento</td>
  </tr>
  <tr>
    <td>Codifica trasferimento contenuto</td>
    <td>Codifica di trasferimento contenuto del messaggio. ("none"|"base64")</td>
  </tr>
  <tr>
    <td>Status</td>
    <td>Un'esecuzione corretta dell'azione restituisce il codice di stato 200 (OK).</td>
  </tr>
  <tr>
    <td>Parametro 1*</td>
    <td>Uno dei parametri di un documento nella raccolta documenti.</td>
  </tr>
  <tr>
    <td>Parametro 2*</td>
    <td>Uno dei parametri di un documento nella raccolta documenti.</td>
  </tr>
</table>

<b>Nota:</b> tutti i parametri della raccolta documenti vengono popolati in modo dinamico e si trovano nella sezione delle proprietà avanzate.

 

###3. Elimina da documenti condivisi

Questa azione elimina il documento dalla raccolta documenti in base all'URL relativo (struttura di cartelle) del documento.

**Input:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obbligatorio</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>URI relativo documento</td>
    <td>No</td>
    <td>Specificare l'URL del documento, relativo a 'Shared Documents'. Esempio: specpersonali1,cartellapersonale/ordini</td>
  </tr>
</table>


**Output:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>Status</td>
    <td>Un'esecuzione corretta dell'azione restituisce il codice di stato 200 (OK).</td>
  </tr>
</table>


###4. Inserisci in attività (JSON)

Questa azione aggiunge un elemento nell'elenco elementi.

**Input:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obbligatorio</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>Parametro obbligatorio 1*</td>
    <td>Sì</td>
    <td>Uno dei parametri obbligatori per aggiungere un documento nell'elenco.</td>
  </tr>
  <tr>
    <td>Parametro obbligatorio 2*</td>
    <td>Sì</td>
    <td>Uno dei parametri obbligatori per aggiungere un documento nell'elenco.</td>
  </tr>
  <tr>
    <td>Parametro facoltativo 1*</td>
    <td>No. Avanzate</td>
    <td>Uno dei parametri obbligatori per aggiungere un documento nell'elenco.</td>
  </tr>
  <tr>
    <td>Parametro facoltativo 2*</td>
    <td>No. Avanzate</td>
    <td>Uno dei parametri obbligatori per aggiungere un documento nell'elenco.</td>
  </tr>
</table>


<b>Nota:</b> tutti i parametri in 'List' vengono popolati in modo dinamico. I parametri obbligatori sono visibili, mentre i parametri facoltativi sono nella sezione delle proprietà avanzate.

 
**Output:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>ID elemento</td>
    <td>ID dell'elemento elenco aggiunto.</td>
  </tr>
  <tr>
    <td>Status</td>
    <td>Un corretto inserimento dell'elemento elenco restituisce il codice di stato 200 (OK).</td>
  </tr>
</table>


###5. Aggiorna attività (JSON)

Questa azione aggiorna un elemento nell'elenco elementi.

**Input:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obbligatorio</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>ID elemento</td>
    <td>Sì</td>
    <td>ID dell'elemento elenco.</td>
  </tr>
  <tr>
    <td>Parametro obbligatorio 1*</td>
    <td>No</td>
    <td>Uno dei parametri obbligatori per aggiungere un documento nell'elenco.</td>
  </tr>
  <tr>
    <td>Parametro obbligatorio 2*</td>
    <td>No</td>
    <td>Uno dei parametri obbligatori per aggiungere un documento nell'elenco.</td>
  </tr>
  <tr>
    <td>Parametro facoltativo 1*</td>
    <td>No. Avanzate</td>
    <td>Uno dei parametri obbligatori per aggiungere un documento nell'elenco.</td>
  </tr>
  <tr>
    <td>Parametro facoltativo 2*</td>
    <td>No. Avanzate</td>
    <td>Uno dei parametri obbligatori per aggiungere un documento nell'elenco.</td>
  </tr>
</table>

<b>Nota:</b> tutti i parametri in 'List' vengono popolati in modo dinamico. I parametri obbligatori sono visibili, mentre i parametri facoltativi sono nella sezione delle proprietà avanzate.


**Output:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>Status  </td>
    <td>Un aggiornamento corretto dell'elemento elenco restituisce il codice di stato 200 (OK).</td>
  </tr>
</table>


###6. Recupera elemento da attività (JSON)

Questa azione recupera un elemento dall'elenco elementi.


**Input:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obbligatorio</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>ID elemento</td>
    <td>Sì</td>
    <td>ID dell'elemento elenco.</td>
  </tr>
</table>


**Output:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>Colonna 1*</td>
    <td>Uno dei parametri nell'elenco.</td>
  </tr>
  <tr>
    <td>Colonna 2*</td>
    <td>Uno dei parametri nell'elenco.</td>
  </tr>
  <tr>
    <td>Status</td>
    <td>Un'esecuzione corretta dell'azione restituisce il codice di stato 200 (OK).</td>
  </tr>
</table>

<b>Nota:</b> le colonne nell'elenco vengono popolate in modo dinamico e mostrate nei parametri di output.


###7. Elimina elemento da attività

Questa azione elimina un elemento dall'elenco elementi.

 
**Input:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obbligatorio</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>ID elemento</td>
    <td>Sì</td>
    <td>ID dell'elemento elenco.</td>
  </tr>
</table>


**Output:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>Status  </td>
    <td>Un'eliminazione corretta dell'elemento elenco restituisce il codice di stato 200 (OK).</td>
  </tr>
</table>


###8. Elenca documenti condivisi (JSON)

Questa azione elenca tutti i documenti in una raccolta documenti. È possibile usare una visualizzazione o una query Caml per filtrare i documenti.  

 
**Input:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obbligatorio</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>Nome visualizzazione</td>
    <td>No</td>
    <td>Specificare una visualizzazione valida usata per filtrare i documenti da prelevare. Esempio: 'Approved Orders'. Per elaborare tutti i documenti esistenti, lasciare vuoto questo campo. </td>
  </tr>
  <tr>
    <td>Query Caml</td>
    <td>No</td>
    <td>Specificare una query Caml valida per filtrare i documenti. Esempio: <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**Output:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>Documenti</td>
    <td>Matrice di tutti i documenti. Ogni documento dispone dei campi di seguito. <br><br>
	Documenti []<br>
	Nome<br>
	Item Id<br>
	URL completo elemento<br>
	Avanzate<br>
	URL modifica elemento<br>
	Nome elenco<br>
	URL completo elenco<br>
	</td>
  </tr>
  <tr>
    <td>Status  </td>
    <td>Un corretto inserimento dell'elemento elenco restituisce il codice di stato 200 (OK).</td>
  </tr>
</table>


###9. Carica in documenti condivisi (XML)

Questa azione carica un nuovo documento in 'Shared Documents'. Il documento di input deve essere un payload XML. La risposta dell'azione sarà un payload XML.
 

**Input:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obbligatorio</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>Nome</td>
    <td>Sì</td>
    <td>Nome del documento.</td>
  </tr>
  <tr>
    <td>Contenuto</td>
    <td>Sì</td>
    <td>Contenuto del documento.</td>
  </tr>
  <tr>
    <td>Codifica trasferimento contenuto</td>
    <td>Sì</td>
    <td>Codifica di trasferimento contenuto del messaggio. ("none"|"base64")</td>
  </tr>
  <tr>
    <td>Forza sovrascrittura</td>
    <td>Sì</td>
    <td>Se è impostato su TRUE ed esiste già un documento con il nome specificato, verrà sovrascritto.</td>
  </tr>
</table>
 

**Output:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>XML di output</td>
    <td>Risposta dell'azione di caricamento in formato XML.</td>
  </tr>
  <tr>
    <td>Status  </td>
    <td>Un caricamento corretto del documento restituisce il codice di stato 200 (OK).</td>
  </tr>
</table>

###10. Recupera da documenti condivisi (XML)

Questa azione recupera il documento dalla raccolta documenti in base all'URL relativo (struttura di cartelle) del documento.

 
**Input:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obbligatorio</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>URI relativo documento</td>
    <td>No</td>
    <td>Specificare l'URL del documento, relativo a 'Shared Documents'. Esempio: specpersonali1,cartellapersonale/ordini</td>
  </tr>
  <tr>
    <td>Tipo file</td>
    <td>Sì</td>
    <td>Specificare se il file è un file binario o un file di testo.</td>
  </tr>
</table>


**Output:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>XML di output</td>
    <td>Contenuto del documento</td>
  </tr>
  <tr>
    <td>Codifica trasferimento contenuto</td>
    <td>Codifica di trasferimento contenuto del messaggio. ("none"|"base64")</td>
  </tr>
  <tr>
    <td>Status</td>
    <td>Un'esecuzione corretta dell'azione restituisce il codice di stato 200 (OK).</td>
  </tr>
</table>

###11. Inserisci in attività (XML)

Questa azione aggiunge un elemento nell'elenco elementi. L'input previsto è un payload XML.

** Input:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obbligatorio</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>XML di input</td>
    <td>Sì</td>
    <td>Il messaggio XML che contiene i valori dei campi dell'elemento elenco da inserire. È possibile usare l'app per le API Transform per generare il messaggio XML.</td>
  </tr>
</table>
 
<b>Nota:</b> tutti i parametri in 'List' vengono popolati in modo dinamico. I parametri obbligatori sono visibili, mentre i parametri facoltativi sono nella sezione delle proprietà avanzate.

 
**Output:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>ID elemento</td>
    <td>ID dell'elemento elenco aggiunto.</td>
  </tr>
  <tr>
    <td>Status  </td>
    <td>Un corretto inserimento dell'elemento elenco restituisce il codice di stato 200 (OK).</td>
  </tr>
</table>


###12. Aggiorna attività (XML)

Questa azione aggiorna un elemento nell'elenco elementi. L'input previsto è un payload XML.


**Input:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obbligatorio</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>ID elemento</td>
    <td>Sì</td>
    <td>ID dell'elemento elenco.</td>
  </tr>
  <tr>
    <td>XML di input</td>
    <td>Sì</td>
    <td>Il messaggio XML che contiene i valori dei campi dell'elemento elenco da inserire. È possibile usare l'app per le API Transform per generare il messaggio XML.</td>
  </tr>
</table>

<b>Nota:</b> tutti i parametri in 'List' vengono popolati in modo dinamico. I parametri obbligatori sono visibili, mentre i parametri facoltativi sono nella sezione delle proprietà avanzate.

 
**Output:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>Status  </td>
    <td>Un aggiornamento corretto dell'elemento elenco restituisce il codice di stato 200 (OK).</td>
  </tr>
</table>


###13. Recupera elemento da attività (XML)

Questa azione recupera un elemento dall'elenco elementi.


**Input:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obbligatorio</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>ID elemento</td>
    <td>Sì</td>
    <td>ID dell'elemento elenco.</td>
  </tr>
</table>

**Output:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrizione</b></td>
  </tr>
  <tr>
    <td>XML di output</td>
    <td>Il messaggio XML che contiene i valori dei campi dell'elemento elenco selezionato. </td>
  </tr>
  <tr>
    <td>Status  </td>
    <td>Un'esecuzione corretta dell'azione restituisce il codice di stato 200 (OK).</td>
  </tr>
</table>


<!--Image references-->
[1]: ./media/app-service-logic-connector-sharepoint/image_0.png
[2]: ./media/app-service-logic-connector-sharepoint/image_1.png
[3]: ./media/app-service-logic-connector-sharepoint/image_2.png
[4]: ./media/app-service-logic-connector-sharepoint/image_3.png
[5]: ./media/app-service-logic-connector-sharepoint/image_4.jpg
[6]: ./media/app-service-logic-connector-sharepoint/image_5.png
[7]: ./media/app-service-logic-connector-sharepoint/image_6.png

<!--HONumber=49-->