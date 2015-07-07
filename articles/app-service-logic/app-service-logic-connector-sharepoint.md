<properties 
   pageTitle="Creare un connettore SharePoint da usare nell'app per la logica" 
   description="Creare un connettore SharePoint; Uso del connettore SharePoint nell'app per la logica" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="06/17/2015"
   ms.author="vagarw"/>

# Uso del connettore SharePoint nell'app per la logica

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso. Il connettore Microsoft SharePoint consente di connettersi a Microsoft SharePoint Server o SharePoint Online e gestire documenti ed elementi elenco. È possibile eseguire varie azioni, ad esempio creare, aggiornare, ottenere ed eliminare documenti ed elementi elenco. Nel caso di un ambiente SharePoint Server locale, è possibile fornire la stringa di connessione per il bus di servizio come parte della configurazione del connettore e installare l'agente listener locale per la connessione al server.

Il connettore SharePoint Online e l'app della raccolta SharePoint Server Connector forniscono trigger e azioni come meccanismi per interagire con SharePoint.

## Creare un connettore SharePoint Online

È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella schermata iniziale di Azure selezionare **Marketplace**.
2. Selezionare **App per le API** e cercare "Connettore SharePoint Online".
3. Immettere il Nome, il Piano di servizio app e altre proprietà.
4. Immettere le impostazioni pacchetto seguenti:

	Nome | Obbligatorio | Descrizione
--- | --- | ---
Site URL | Sì | Immettere l'URL completo del sito Web di SharePoint. Ad esempio, immettere: *https://microsoft.sharepoint.com/teams/wabstest*. URL relativi raccolta/elenco documenti | Sì | Specificare gli URL degli elenchi o delle raccolte di documenti, relativi all'URL del sito di SharePoint, che possono essere modificati dal connettore. Ad esempio, immettere: *Elenchi/attività, Documenti condivisi*.

5. Al termine, l'aspetto di Impostazioni pacchetto dovrebbe essere simile al seguente: <br/> ![][1]

A questo punto è possibile creare un'app per la logica nello stesso gruppo di risorse per usare il connettore SharePoint Online.

## Creare un connettore di SharePoint Server

È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella schermata iniziale di Azure selezionare **Marketplace**.
2. Selezionare **App per le API** e cercare "Connettore SharePoint Server".
3. Immettere il Nome, il Piano di servizio app e altre proprietà.
4. Immettere le impostazioni pacchetto seguenti:

	Nome | Obbligatorio | Descrizione
--- | --- | ---
Site URL | Sì | Immettere l'URL completo del sito Web di SharePoint. Ad esempio, immettere: *https://microsoft.sharepoint.com/teams/wabstest*. Modalità di autenticazione | Sì | Specificare la modalità di autenticazione per la connessione al sito di SharePoint. Le opzioni includono:<ul><li>Default</li><li>WindowsAuthentication</li><li>FormBasedAuthentication</li></ul><br/><br/>Se si sceglie Default, verranno usate le credenziali di esecuzione del connettore SharePoint; nome utente/password non sono richiesti. Nome utente e Password sono richiesti per altri tipi di autenticazione.<br/><br/>** Nota** L'autenticazione anonima non è supportata.
User Name | No | Immettere un nome utente valido per la connessione al sito di SharePoint, se la modalità di autenticazione non è Predefinito.
Password | No | Immettere una password valida per la connessione al sito di SharePoint, se la modalità di autenticazione non è Predefinito.
URL relativi raccolta/elenco documenti | Sì | Immettere gli URL degli elenchi o delle raccolte di documenti, relativi all'URL del sito di SharePoint, che possono essere modificati dal connettore. Ad esempio, immettere: *Elenchi/attività, Documenti condivisi*.
Service Bus Connection String | No | Se ci si connette in locale, immettere la stringa di connessione di inoltro del bus di servizio.<br/><br/>[Uso della gestione connessione ibrida](app-service-logic-hybrid-connection-manager.md)<br/>[Prezzi del bus di servizio](http://azure.microsoft.com/pricing/details/service-bus/)

5. Al termine, l'aspetto di Impostazioni pacchetto dovrebbe essere simile al seguente: <br/> ![][2]

A questo punto è possibile creare un'app per la logica nello stesso gruppo di risorse per usare il connettore SharePoint Server.


## Uso del connettore SharePoint nell'app per la logica

Una volta creata l'app per le API, è possibile usare il connettore SharePoint come trigger o come azione per l'app per la logica. A questo scopo, è necessario:

1. Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse del connettore SharePoint.

2. Aprire **Trigger e azioni** per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso. Il connettore SharePoint apparirà nella sezione "Usati di recente" nella raccolta sulla destra. Selezionarlo.

3. Se il connettore SharePoint viene selezionato all'avvio dell'app per la logica agisce come trigger, in caso contrario è possibile eseguire azioni sull'account SharePoint con il connettore.

4. Quando si usa il connettore SharePoint Online, per eseguire operazioni per conto dell'utente è necessario autenticare e autorizzare le app per la logica. Per avviare l'autorizzazione, fare clic su **Autorizza** nel connettore SharePoint: <br/> ![][3]

5. Facendo clic su Autorizza verrà visualizzata la finestra di dialogo di autenticazione di SharePoint. Immettere i dettagli di accesso dell'account di SharePoint su cui eseguire le operazioni: <br/> ![][4]

6. Concedere alle app per la logica l'accesso all'account utente per eseguire le operazioni per conto dell'utente: <br/> ![][5]

7. Se il connettore SharePoint viene configurato come trigger, vengono mostrati i trigger. In caso contrario, viene visualizzato l'elenco delle azioni ed è possibile scegliere l'operazione appropriata da eseguire:<br/> ![][6] <br/> **URL relativo configurato per la libreria di documenti** <br/> ![][7] <br/> **URL relativo configurato per l'elenco di documenti** <br/>

> [AZURE.NOTE]Per i trigger seguenti si presuppone che l'utente abbia immesso 'Documenti condivisi, Elenchi/attività' nelle impostazioni pacchetto del connettore, dove 'Documenti condivisi' è una raccolta documenti ed 'Elenchi/attività' è un elenco.

##  Trigger
Usare i trigger se si vuole avviare un'app per la logica.

> [AZURE.NOTE]I trigger eliminano i file dopo averli letti. Per mantenere i file, immettere un valore per il percorso dell'archivio.

### 1. Nuovo documento in documenti condivisi (JSON)
Questo trigger viene attivato quando un nuovo documento è disponibile in 'Documenti condivisi'.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
Nome visualizzazione | No | Immettere una visualizzazione valida usata per filtrare i documenti da prelevare. Ad esempio, immettere: 'Approved Orders'. Per elaborare tutti i documenti esistenti, lasciare vuoto questo campo. 
Percorso archiviazione | No | Immettere un URL di cartella valido, relativo al sito di SharePoint, in cui archiviare i documenti elaborati.
Sovrascrivi in archivio | No | Selezionare questa opzione per sovrascrivere un file nel percorso di archiviazione, se esiste già.
Query Caml | No, avanzate | Immettere una query Caml valida per filtrare i documenti. Ad esempio, immettere: `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Output

Nome | Descrizione
--- | --- 
Nome | Nome del documento.
Contenuto | Contenuto del documento.
Codifica trasferimento contenuto | Codifica di trasferimento contenuto del messaggio ("Nessuna"o "Base64").

**Nota**: tutte le colonne dell'elemento documento vengono riportate nelle proprietà di output avanzate.


### 2. Nuovo elemento in attività (JSON)
Questo trigger viene attivato quando viene aggiunto un nuovo elemento all'elenco 'Attività'.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
Nome visualizzazione | No | Immettere una visualizzazione valida usata per filtrare gli elementi dell'elenco. Ad esempio, immettere: 'Approved Orders'. Per elaborare tutti i nuovi elementi, lasciare vuoto questo campo. 
Percorso archiviazione | No | Immettere un URL di cartella valido, relativo al sito di SharePoint, in cui archiviare gli elementi elenco elaborati.
Query Caml | No, avanzate | Immettere una query Caml valida per filtrare i documenti. Ad esempio, immettere: `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Output

Nome | Descrizione
--- | --- 
Le colonne nell'elenco vengono popolate in modo dinamico e mostrate nei parametri di output. | & nbsp;


### 3. Nuovo documento in documenti condivisi (XML)

Questo trigger viene attivato quando un nuovo documento è disponibile in 'Documenti condivisi'. Il nuovo documento viene restituito come messaggio XML.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
Nome visualizzazione | No | Immettere una visualizzazione valida usata per filtrare i documenti da prelevare. Ad esempio, immettere: 'Approved Orders'. Per elaborare tutti i documenti esistenti, lasciare vuoto questo campo. 
Percorso archiviazione | No | Specificare un URL di cartella valido, relativo al sito di SharePoint, in cui archiviare i documenti elaborati.
Sovrascrivi in archivio | No | Selezionare questa opzione per sovrascrivere un file nel percorso di archiviazione, se esiste già.
Query Caml | No, avanzate | Immettere una query Caml valida per filtrare i documenti. Ad esempio, immettere: `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Output

Nome | Descrizione
--- | --- 
Contenuto | Contenuto del documento.
Codifica trasferimento contenuto | Codifica di trasferimento contenuto del messaggio ("Nessuna"o "Base64").


### 4. Nuovo elemento in attività (XML)

Questo trigger viene attivato quando viene aggiunto un nuovo elemento all'elenco 'Attività'. Il nuovo elemento elenco viene restituito come messaggio XML.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
Nome visualizzazione | No | Immettere una visualizzazione valida usata per filtrare gli elementi dell'elenco. Ad esempio: 'Approved Orders'. Per elaborare tutti i nuovi elementi, lasciare vuoto questo campo. 
Percorso archiviazione | No | Immettere un URL di cartella valido, relativo al sito di SharePoint, in cui archiviare gli elementi elenco elaborati.
Query Caml | No, avanzate | Immettere una query Caml valida per filtrare gli elementi elenco. Ad esempio, immettere: `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Output

Nome | Descrizione
--- | --- 
Contenuto | Contenuto del documento.
Codifica trasferimento contenuto | Codifica di trasferimento contenuto del messaggio ("Nessuna"o "Base64").


## Actions
Per le azioni seguenti si presuppone che l'utente abbia specificato 'Documenti condivisi, Elenchi/attività' nelle impostazioni pacchetto del connettore, dove 'Documenti condivisi' è una raccolta documenti ed 'Elenchi/attività' è un elenco.

### 1. Carica in documenti condivisi (JSON)

Questa azione carica un nuovo documento in 'Documenti condivisi'. L'input è un oggetto JSON fortemente tipizzato con tutti i campi di colonna della raccolta documenti.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
Nome | Sì | Nome del documento.
Contenuto | Sì | Contenuto del documento.
Codifica trasferimento contenuto | Sì | Codifica di trasferimento contenuto del messaggio ("Nessuna"o "Base64").
Forza sovrascrittura | Sì | Se è impostato su TRUE ed esiste già un documento con il nome specificato, verrà sovrascritto.
Parametro obbligatorio 1* | Sì | Uno dei parametri obbligatori per aggiungere un documento nella raccolta documenti.
Parametro obbligatorio 2* | Sì | Uno dei parametri obbligatori per aggiungere un documento nella raccolta documenti.
Parametro facoltativo 1* | No. Avanzate | Uno dei parametri facoltativi per aggiungere un documento nella raccolta documenti.
Parametro facoltativo 2* | No. Avanzate | Uno dei parametri facoltativi per aggiungere un documento nella raccolta documenti.

**Nota** Tutti i parametri della raccolta documenti vengono popolati in modo dinamico. I parametri obbligatori sono visibili e i parametri facoltativi si trovano nella sezione Avanzate.


#### Output

Nome | Descrizione
--- | --- 
ID elemento | ID del documento aggiunto nella raccolta documenti.
Stato | Un caricamento corretto del documento restituisce il codice di stato 200 (OK).
 

### 2. Recupera da documenti condivisi (JSON)
Questa azione recupera il documento dalla raccolta documenti in base all'URL relativo (struttura di cartelle) del documento.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
URI relativo documento | No | Immettere l'URL del documento, relativo a 'Documenti condivisi'. Ad esempio, immettere: *myspec1, myfolder/orders*.


#### Output

Nome | Descrizione
--- | --- 
Contenuto | Contenuto del documento 
Codifica trasferimento contenuto | Codifica di trasferimento contenuto del messaggio ("Nessuna" o "Base64").
Stato | Un'esecuzione corretta dell'azione restituisce il codice di stato 200 (OK).
Parametro 1* | Uno dei parametri di un documento nella raccolta documenti.
Parametro 2* | Uno dei parametri di un documento nella raccolta documenti.

**Nota** Tutti i parametri della raccolta documenti vengono popolati in modo dinamico. Si trovano nella sezione Avanzate.

 

### 3. Elimina da documenti condivisi

Questa azione elimina il documento dalla raccolta documenti in base all'URL relativo (struttura di cartelle) del documento.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
URI relativo documento | No | Immettere l'URL del documento, relativo a 'Documenti condivisi'. Ad esempio, immettere: *myspec1, myfolder/orders*.

#### Output

Nome | Descrizione
--- | --- 
Stato | Un'esecuzione corretta dell'azione restituisce il codice di stato 200 (OK).


### 4. Inserisci in attività (JSON)

Questa azione aggiunge un elemento nell'elenco elementi.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
Parametro obbligatorio 1* | Sì | Uno dei parametri obbligatori per aggiungere un documento nell'elenco.
Parametro obbligatorio 2* | Sì | Uno dei parametri obbligatori per aggiungere un documento nell'elenco.
Parametro facoltativo 1* | No. Avanzate | Uno dei parametri obbligatori per aggiungere un documento nell'elenco.
Parametro facoltativo 2* | No. Avanzate | Uno dei parametri obbligatori per aggiungere un documento nell'elenco.

**Nota** Tutti i parametri dell'elenco vengono popolati in modo dinamico. I parametri obbligatori sono visibili e i parametri facoltativi si trovano nella sezione Avanzate.

 
#### Output

Nome | Descrizione
--- | --- 
ID elemento | ID dell'elemento elenco aggiunto.
Stato | Un corretto inserimento dell'elemento elenco restituisce il codice di stato 200 (OK).


### 5. Aggiorna attività (JSON)

Questa azione aggiorna un elemento nell'elenco elementi.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
ID elemento | Sì | ID dell'elemento elenco.
Parametro obbligatorio 1* | No | Uno dei parametri obbligatori per aggiungere un documento nell'elenco.
Parametro obbligatorio 2* | No | Uno dei parametri obbligatori per aggiungere un documento nell'elenco.
Parametro facoltativo 1* | No. Avanzate | Uno dei parametri obbligatori per aggiungere un documento nell'elenco.
Parametro facoltativo 2* | No. Avanzate | Uno dei parametri obbligatori per aggiungere un documento nell'elenco.

**Nota** Tutti i parametri dell'elenco vengono popolati in modo dinamico. I parametri obbligatori sono visibili e i parametri facoltativi si trovano nella sezione Avanzate.


#### Output

Nome | Descrizione
--- | --- 
Stato | Un aggiornamento corretto dell'elemento elenco restituisce il codice di stato 200 (OK).


### 6. Recupera elemento da attività (JSON)

Questa azione recupera un elemento dall'elenco elementi.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
ID elemento | Sì | ID dell'elemento elenco.


#### Output

Nome | Descrizione
--- | --- 
Colonna 1* | Uno dei parametri nell'elenco.
Colonna 2* | Uno dei parametri nell'elenco.
Stato | Un'esecuzione corretta dell'azione restituisce il codice di stato 200 (OK).

**Nota** Le colonne nell'elenco vengono popolate in modo dinamico e mostrate nei parametri di output.


### 7. Elimina elemento da attività

Questa azione elimina un elemento dall'elenco elementi.

 
#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
ID elemento | Sì | ID dell'elemento elenco.


#### Output

Nome | Descrizione
--- | --- 
Stato | Un'eliminazione corretta dell'elemento elenco restituisce il codice di stato 200 (OK).


### 8. Elenca documenti condivisi (JSON)

Questa azione elenca tutti i documenti in una raccolta documenti. È possibile usare una visualizzazione o una query Caml per filtrare i documenti.

 
#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
Nome visualizzazione | No | Immettere una visualizzazione valida usata per filtrare i documenti da prelevare. Ad esempio, immettere: 'Approved Orders'. Per elaborare tutti i documenti esistenti, lasciare vuoto questo campo. 
Query Caml | No | Immettere una query Caml valida per filtrare i documenti. Ad esempio, immettere: `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Output

Nome | Descrizione
--- | --- 
Documenti | Matrice di tutti i documenti. Ogni documento contiene i campi seguenti: <ul><li>Documenti </li><li>Nome</li><li>ID elemento</li><li>URL completo elemento</li><li>Avanzate</li><li>URL modifica elemento</li><li>Nome elenco</li><li>URL completo elenco</li></ul>
Stato | Un corretto inserimento dell'elemento elenco restituisce il codice di stato 200 (OK).


### 9. Carica in documenti condivisi (XML)

Questa azione carica un nuovo documento in 'Documenti condivisi'. Il documento di input deve essere un payload XML. La risposta dell'azione sarà un payload XML.
 

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
Nome | Sì | Nome del documento.
Contenuto | Sì | Contenuto del documento.
Codifica trasferimento contenuto | Sì | Codifica di trasferimento contenuto del messaggio ("Nessuna" o "Base64").
Forza sovrascrittura | Sì | Se è impostato su TRUE ed esiste già un documento con il nome specificato, viene sovrascritto.
 

#### Output

Nome | Descrizione
--- | --- 
XML di output | Risposta dell'azione di caricamento in formato XML.
Stato | Un caricamento corretto del documento restituisce il codice di stato 200 (OK).

### 10. Recupera da documenti condivisi (XML)

Questa azione recupera il documento dalla raccolta documenti in base all'URL relativo (struttura di cartelle) del documento.

 
#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
URI relativo documento | No | Immettere l'URL del documento, relativo a 'Documenti condivisi'. Ad esempio, immettere: *myspec1, myfolder/orders*.
Tipo file | Sì | Immettere se il file è un file binario o un file di testo.

#### Output

Nome | Descrizione
--- | --- 
XML di output | Contenuto del documento
Codifica trasferimento contenuto | Codifica di trasferimento contenuto del messaggio ("Nessuna" o "Base64").
Stato | Un'esecuzione corretta dell'azione restituisce il codice di stato 200 (OK).

### 11. Inserisci in attività (XML)

Questa azione aggiunge un elemento nell'elenco elementi. L'input previsto è un payload XML.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
XML di input | Sì | Il messaggio XML che contiene i valori dei campi dell'elemento elenco da inserire. È possibile usare l'app per le API Transform per generare il messaggio XML.

**Nota** Tutti i parametri dell'elenco vengono popolati in modo dinamico. I parametri obbligatori sono visibili e i parametri facoltativi si trovano nella sezione Avanzate.

 
#### Output

Nome | Descrizione
--- | ---
ID elemento | ID dell'elemento elenco aggiunto.
Stato | Un corretto inserimento dell'elemento elenco restituisce il codice di stato 200 (OK).


### 12. Aggiorna attività (XML)

Questa azione aggiorna un elemento nell'elenco elementi. L'input previsto è un payload XML.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
ItemID | Sì | ID dell'elemento elenco.
XML di input | Sì | Il messaggio XML che contiene i valori dei campi dell'elemento elenco da inserire. È possibile usare l'app per le API Transform per generare il messaggio XML.

**Nota** Tutti i parametri dell'elenco vengono popolati in modo dinamico. I parametri obbligatori sono visibili e i parametri facoltativi si trovano nella sezione Avanzate.
 
#### Output

Nome | Descrizione
--- | ---
Stato | Un aggiornamento corretto dell'elemento elenco restituisce il codice di stato 200 (OK).


### 13. Recupera elemento da attività (XML)

Questa azione recupera un elemento dall'elenco elementi.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
ItemID | Sì | ID dell'elemento elenco.


#### Output

Nome | Descrizione
--- | ---
XML di output | Il messaggio XML che contiene i valori dei campi dell'elemento elenco selezionato.
Stato | Un'esecuzione corretta dell'azione restituisce il codice di stato 200 (OK).


## Configurazione ibrida (facoltativo)

> [AZURE.NOTE]Questo passaggio è obbligatorio solo se si usa SharePoint installato in locale e protetto da firewall.

Il servizio app usa Gestione connessione ibrida per connettersi in modo sicuro al sistema locale. Se il connettore usa un server SharePoint locale, è richiesta la Gestione connessione ibrida.

Vedere [Uso di Gestione connessione ibrida](app-service-logic-hybrid-connection-manager.md).

## Più vantaggi con il connettore
Dopo aver creato il connettore è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e il connettore](../app-service-api/app-service-api-manage-in-portal.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-sharepoint/image_0.png
[2]: ./media/app-service-logic-connector-sharepoint/image_1.png
[3]: ./media/app-service-logic-connector-sharepoint/image_2.png
[4]: ./media/app-service-logic-connector-sharepoint/image_3.png
[5]: ./media/app-service-logic-connector-sharepoint/image_4.jpg
[6]: ./media/app-service-logic-connector-sharepoint/image_5.png
[7]: ./media/app-service-logic-connector-sharepoint/image_6.png
 

<!---HONumber=62-->