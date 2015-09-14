<properties
   pageTitle="Uso del connettore SharePoint nelle app per la logica | Microsoft Azure App Service"
	description="Come creare e configurare l'app per le API o il connettore SharePoint e usarlo in un'app per la logica in Azure App Service"
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
	ms.date="08/23/2015"
	ms.author="sameerch"/>

# Uso del connettore SharePoint e aggiunta all'app per la logica
Connettersi a SharePoint Server o SharePoint Online per gestire documenti ed elementi elenco. È possibile eseguire varie azioni, ad esempio creare, aggiornare, ottenere ed eliminare documenti ed elementi elenco. Se si usa un ambiente SharePoint Server locale, immettere la stringa di connessione per il bus di servizio come parte della configurazione del connettore e installare l'agente listener locale per la connessione al server.

L'app della raccolta di connettori SharePoint Online e SharePoint Server forniscono trigger e azioni come meccanismi per interagire con SharePoint.

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso. È possibile aggiungere il connettore SharePoint al flusso di lavoro aziendale ed elaborare i dati come parte di questo flusso di lavoro nell'ambito di un'app per la logica.

## Creare un connettore SharePoint Online

È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Selezionare **App per le API** e cercare "SharePoint Online Connector".
3. Immettere il nome, il piano di servizio app e altre proprietà.
4. Immettere le impostazioni pacchetto seguenti:

	Nome | Obbligatorio | Descrizione
--- | --- | ---
Site URL | Sì | Immettere l'URL completo del sito Web di SharePoint. Ad esempio, immettere: *https://microsoft.sharepoint.com/teams/wabstest*.
URL relativi raccolta / elenco documenti | Sì | Specificare gli URL degli elenchi o delle raccolte documenti, relativi all/URL del sito di SharePoint, che possono essere modificati dal connettore. Ad esempio, immettere: *Elenchi/attività, Documenti condivisi*.

5. Al termine, l'aspetto di Impostazioni pacchetto è simile al seguente:  
 ![][1]

A questo punto è possibile creare un'app per la logica nello stesso gruppo di risorse per usare il connettore SharePoint Online.

## Creazione di SharePoint Server Connector

È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Selezionare **App per le API** e cercare "SharePoint Server Connector".
3. Immettere il nome, il piano di servizio app e altre proprietà.
4. Immettere le impostazioni pacchetto seguenti:

	Nome | Obbligatorio | Descrizione
--- | --- | ---
Site URL | Sì | Immettere l'URL completo del sito Web di SharePoint. Ad esempio, immettere: *https://microsoft.sharepoint.com/teams/wabstest*. Modalità di autenticazione | Sì | Specificare la modalità di autenticazione per la connessione al sito di SharePoint. Le opzioni includono:<ul><li>Default</li><li>WindowsAuthentication</li><li>FormBasedAuthentication</li></ul><br/>
<br/>
Se si sceglie Default, verranno usate le credenziali di esecuzione del connettore SharePoint. Il nome utente e la password non sono richiesti. User Name e Password sono richiesti per altri tipi di autenticazione.<br/>
<br/>
** Nota** L'autenticazione anonima non è supportata.
User Name | No | Immettere un nome utente valido per la connessione al sito di SharePoint, se la modalità di autenticazione non è Default.
Password | No | Immettere una password valida per la connessione al sito di SharePoint, se la modalità di autenticazione non è Default.
URL relativi raccolta/elenco documenti | Sì | Immettere gli URL degli elenchi o delle raccolte documenti, relativi all'URL del sito di SharePoint, che possono essere modificati dal connettore. Ad esempio, immettere: *Elenchi/attività, Documenti condivisi*.
Service Bus Connection String | No | Se ci si connette in locale, immettere la stringa di connessione di inoltro del bus di servizio.<br/>
<br/>
[Uso della gestione connessione ibrida](app-service-logic-hybrid-connection-manager.md)<br/>
[Prezzi del bus di servizio](http://azure.microsoft.com/pricing/details/service-bus/)

5. Al termine, l'aspetto di Impostazioni pacchetto è simile al seguente:
 ![][2]

A questo punto è possibile creare un'app per la logica nello stesso gruppo di risorse per usare il connettore SharePoint Server.


## Usare il connettore SharePoint nell'app per la logica

Dopo aver creato l'app per le API, è possibile usare il connettore SharePoint come trigger o azione per l'app per la logica. A questo scopo, è necessario:

1. Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse del connettore SharePoint.

2. Aprire **Triggers and Actions** per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso di lavoro. Il connettore SharePoint viene visualizzato nella sezione "Recently used" a destra nella raccolta. Selezionarlo.

3. Se viene selezionato all'avvio dell'app per la logica, il connettore SharePoint agisce come trigger, in caso contrario sarebbe possibile eseguire azioni sull'account SharePoint con il connettore.

4. Quando si usa il connettore SharePoint Online, per eseguire operazioni per proprio conto è necessario autenticare e autorizzare le app per la logica. Per avviare l'autorizzazione, fare clic su **Authorize** nel connettore SharePoint:
 ![][3]

5. Facendo clic su Authorize, viene visualizzata la finestra di dialogo di autenticazione di SharePoint. Immettere i dettagli di accesso dell'account di SharePoint su cui eseguire le operazioni:
 ![][4]

6. Concedere alle app per la logica l'accesso all'account utente per eseguire l'operazione per proprio conto:
 ![][5]

7. Se il connettore SharePoint viene configurato come trigger, vengono mostrati i trigger. In caso contrario, viene visualizzato un elenco di azioni ed è possibile scegliere l'operazione appropriata da eseguire:<br/>
 ![][6] <br/>
 **URL relativo configurato per la libreria di documenti** <br/>
 ![][7] <br/>
 **URL relativo configurato per l'elenco di documenti**

> [AZURE.NOTE]Per i trigger seguenti si presuppone che l'utente abbia immesso "Documenti condivisi, Elenchi/attività" nelle impostazioni pacchetto del connettore, dove "Documenti condivisi" è una raccolta documenti ed "Elenchi/attività" è un elenco.

##  Trigger
Usare i trigger se si vuole avviare un'app per la logica.

> [AZURE.NOTE]I trigger eliminano i file dopo averli letti. Per mantenere i file, specificare un valore per il percorso di archiviazione.

### 1\. Nuovo documento in documenti condivisi (JSON)
Questo trigger viene attivato quando un nuovo documento è disponibile in 'Documenti condivisi'.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
Nome visualizzazione | No | Immettere una visualizzazione valida usata per filtrare i documenti da selezionare. Ad esempio, immettere: "Approved Orders". Per elaborare tutti i documenti esistenti, lasciare vuoto questo campo.
Percorso archiviazione | No | Immettere un URL di cartella valido, relativo al sito di SharePoint, in cui archiviare i documenti elaborati.
Sovrascrivi in archivio | No | Selezionare questa opzione per sovrascrivere un file nel percorso di archiviazione, se esiste già.
Query Caml | No, avanzate | Immettere una query Caml valida per filtrare i documenti. Ad esempio, immettere: `<Where><Geq><FieldRef Name='ID'/>
<Value Type='Number'>10</Value></Geq></Where>`

#### Output

Nome | Descrizione
--- | ---
Nome | Nome del documento.
Contenuto | Contenuto del documento.
ContentTransferEncoding | Codifica di trasferimento contenuto del messaggio ("none" o "base64").

**Nota** Tutte le colonne dell'elemento documento vengono riportate nelle proprietà di output Avanzate.


### 2\. Nuovo elemento in attività (JSON)
Questo trigger viene attivato quando viene aggiunto un nuovo elemento all'elenco 'Attività'.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
Nome visualizzazione | No | Immettere una visualizzazione valida usata per filtrare gli elementi dell'elenco. Ad esempio, immettere: "Approved Orders". Per elaborare tutti i nuovi elementi, lasciare vuoto questo campo.
Percorso archiviazione | No | Immettere un URL di cartella valido, relativo al sito di SharePoint, in cui archiviare gli elementi elenco elaborati.
Query Caml | No, avanzate | Immettere una query Caml valida per filtrare i documenti. Ad esempio, immettere: `<Where><Geq><FieldRef Name='ID'/>
<Value Type='Number'>10</Value></Geq></Where>`

#### Output

Nome | Descrizione
--- | ---
Le colonne nell'elenco vengono popolate in modo dinamico e mostrate nei parametri di output. | & nbsp;


### 3\. Nuovo documento in documenti condivisi (XML)

Questo trigger viene attivato quando un nuovo documento è disponibile in 'Documenti condivisi'. Il nuovo documento viene restituito come messaggio XML.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
Nome visualizzazione | No | Immettere una visualizzazione valida usata per filtrare i documenti da selezionare. Ad esempio, immettere: "Approved Orders". Per elaborare tutti i documenti esistenti, lasciare vuoto questo campo.
Percorso archiviazione | No | Specificare un URL di cartella valido, relativo al sito di SharePoint, in cui archiviare i documenti di elenco elaborati.
Sovrascrivi in archivio | No | Selezionare questa opzione per sovrascrivere un file nel percorso di archiviazione, se esiste già.
Query Caml | No, avanzate | Immettere una query Caml valida per filtrare i documenti. Ad esempio, immettere: `<Where><Geq><FieldRef Name='ID'/>
<Value Type='Number'>10</Value></Geq></Where>`

#### Output

Nome | Descrizione
--- | ---
Contenuto | Contenuto del documento.
ContentTransferEncoding | Codifica di trasferimento contenuto del messaggio ("none" o "base64").


### 4\. Nuovo elemento in attività (XML)

Questo trigger viene attivato quando viene aggiunto un nuovo elemento all'elenco 'Attività'. Il nuovo elemento elenco viene restituito come messaggio XML.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
Nome visualizzazione | No | Immettere una visualizzazione valida usata per filtrare gli elementi dell'elenco. Ad esempio: 'Approved Orders'. Per elaborare tutti i nuovi elementi, lasciare vuoto questo campo.
Percorso archiviazione | No | Immettere un URL di cartella valido, relativo al sito di SharePoint, in cui archiviare gli elementi elenco elaborati.
Query Caml | No, avanzate | Immettere una query Caml valida per filtrare gli elementi elenco. Ad esempio, immettere: `<Where><Geq><FieldRef Name='ID'/>
<Value Type='Number'>10</Value></Geq></Where>`

#### Output

Nome | Descrizione
--- | ---
Contenuto | Contenuto del documento.
ContentTransferEncoding | Codifica di trasferimento contenuto del messaggio ("none" o "base64").


##  Azioni
Per le azioni seguenti si presuppone che l'utente abbia immesso "Documenti condivisi, Elenchi/attività" nelle impostazioni pacchetto del connettore, dove "Documenti condivisi" è una raccolta documenti ed "Elenchi/attività" è un elenco.

### 1\. Carica in documenti condivisi (JSON)

Questa azione carica un nuovo documento in 'Documenti condivisi'. L'input è un oggetto JSON fortemente tipizzato con tutti i campi di colonna della raccolta documenti.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
Nome | Sì | Nome del documento.
Contenuto | Sì | Contenuto del documento.
ContentTransferEncoding | Sì | Codifica di trasferimento contenuto del messaggio ("none" o "base64").
Forza sovrascrittura | Sì | Se è impostato su TRUE ed esiste già un documento con il nome specificato, verrà sovrascritto.
ReqParam1* | Sì | Uno dei parametri obbligatori per aggiungere un documento nella raccolta documenti.
ReqParam2* | Sì | Uno dei parametri obbligatori per aggiungere un documento nella raccolta documenti.
OptionalParam1* | No. Avanzate | Uno dei parametri facoltativi per aggiungere un documento nella raccolta documenti.
OptionalParam2* | No. Avanzate | Uno dei parametri facoltativi per aggiungere un documento nella raccolta documenti.

**Nota** Tutti i parametri della raccolta documenti vengono popolati in modo dinamico. I parametri obbligatori sono visibili e i parametri facoltativi si trovano nella sezione Avanzate.

#### Output

Nome | Descrizione
--- | ---
ItemId | ID del documento aggiunto nella raccolta documenti.
Stato | Un caricamento corretto del documento restituisce il codice di stato 200 (OK).


 

### 2\. Recupera da documenti condivisi (JSON)
Questa azione recupera il documento dalla raccolta documenti in base all'URL relativo (struttura di cartelle) del documento.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
URI relativo documento | No | Immettere l'URL del documento, relativo a 'Documenti condivisi'. Ad esempio, immettere: *myspec1, myfolder/orders*.

#### Output

Nome | Descrizione
--- | ---
Contenuto | Contenuto del documento
ContentTransferEncoding | Codifica di trasferimento contenuto del messaggio ("none" o "base64").
Stato | Un'esecuzione corretta dell'azione restituisce il codice di stato 200 (OK).
Param1* | Uno dei parametri di un documento nella raccolta documenti.
Param2* | Uno dei parametri di un documento nella raccolta documenti.

**Nota** Tutti i parametri della raccolta documenti vengono popolati in modo dinamico. Si trovano nella sezione Avanzate.

 

### 3\. Elimina da documenti condivisi

Questa azione elimina il documento dalla raccolta documenti in base all'URL relativo (struttura di cartelle) del documento.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
URI relativo documento | No | Immettere l'URL del documento, relativo a 'Documenti condivisi'. Ad esempio, immettere: *myspec1, myfolder/orders*.

#### Output

Nome | Descrizione
--- | ---
Stato | Un'esecuzione corretta dell'azione restituisce il codice di stato 200 (OK).


### 4\. Inserisci in attività (JSON)

Questa azione aggiunge un elemento nell'elenco elementi.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
ReqParam1* | Sì | Uno dei parametri obbligatori per aggiungere un documento nell'elenco.
ReqParam2* | Sì | Uno dei parametri obbligatori per aggiungere un documento nell'elenco.
OptionalParam1* | No. Avanzate | Uno dei parametri obbligatori per aggiungere un documento nell'elenco.
OptionalParam2* | No. Avanzate | Uno dei parametri obbligatori per aggiungere un documento nell'elenco.

**Nota** Tutti i parametri dell'elenco vengono popolati in modo dinamico. I parametri obbligatori sono visibili e i parametri facoltativi si trovano nella sezione Avanzate.

#### Output

Nome | Descrizione
--- | ---
ItemId | ID dell'elemento elenco aggiunto.
Stato | Un corretto inserimento dell'elemento elenco restituisce il codice di stato 200 (OK).


### 5\. Aggiorna attività (JSON)

Questa azione aggiorna un elemento nell'elenco elementi.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
ItemId | Sì | ID dell'elemento elenco.
ReqParam1* | No | Uno dei parametri obbligatori per aggiungere un documento nell'elenco.
ReqParam2* | No | Uno dei parametri obbligatori per aggiungere un documento nell'elenco.
OptionalParam1* | No. Avanzate | Uno dei parametri obbligatori per aggiungere un documento nell'elenco.
OptionalParam2* | No. Avanzate | Uno dei parametri obbligatori per aggiungere un documento nell'elenco.

**Nota** Tutti i parametri dell'elenco vengono popolati in modo dinamico. I parametri obbligatori sono visibili e i parametri facoltativi si trovano nella sezione Avanzate.

#### Output

Nome | Descrizione
--- | ---
Stato | Un aggiornamento corretto dell'elemento elenco restituisce il codice di stato 200 (OK).


### 6\. Recupera elemento da attività (JSON)

Questa azione recupera un elemento dall'elenco elementi.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
ItemId | Sì | ID dell'elemento elenco.

#### Output

Nome | Descrizione
--- | ---
Colonna 1* | Uno dei parametri nell'elenco.
Colonna 2* | Uno dei parametri nell'elenco.
Stato | Un'esecuzione corretta dell'azione restituisce il codice di stato 200 (OK).

**Nota** Le colonne nell'elenco vengono popolate in modo dinamico e mostrate nei parametri di output.


### 7\. Elimina elemento da attività

Questa azione elimina un elemento dall'elenco elementi.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
ItemId | Sì | ID dell'elemento elenco.

#### Output

Nome | Descrizione
--- | ---
Stato | Un'eliminazione corretta dell'elemento elenco restituisce il codice di stato 200 (OK).


### 8\. Elenca documenti condivisi (JSON)

Questa azione elenca tutti i documenti in una raccolta documenti. È possibile usare una visualizzazione o una query Caml per filtrare i documenti.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
Nome visualizzazione | No | Immettere una visualizzazione valida usata per filtrare i documenti da selezionare. Ad esempio, immettere: "Approved Orders". Per elaborare tutti i documenti esistenti, lasciare vuoto questo campo.
Query Caml | No | Immettere una query Caml valida per filtrare i documenti. Ad esempio, immettere: `<Where><Geq><FieldRef Name='ID'/>
<Value Type='Number'>10</Value></Geq></Where>`

#### Output

Nome | Descrizione
--- | ---
Documenti | Matrice di tutti i documenti. Ogni documento contiene i campi seguenti: <ul><li>Documenti </li><li>Nome</li><li>ID elemento</li><li>URL completo elemento</li><li>Avanzate</li><li>URL modifica elemento</li><li>Nome elenco</li><li>URL completo elenco</li></ul>
Stato | Un corretto inserimento dell'elemento elenco restituisce il codice di stato 200 (OK).


### 9\. Carica in documenti condivisi (XML)

Questa azione carica un nuovo documento in 'Documenti condivisi'. Il documento di input deve essere un payload XML. La risposta dell'azione sarà un payload XML.
 
#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
Nome | Sì | Nome del documento.
Contenuto | Sì | Contenuto del documento.
ContentTransferEncoding | Sì | Codifica di trasferimento contenuto del messaggio ("none" o "base64").
Forza sovrascrittura | Sì | Se è impostato su TRUE ed esiste già un documento con il nome specificato, viene sovrascritto.
 
#### Output

Nome | Descrizione
--- | ---
XML di output | Risposta dell'azione di caricamento in formato XML.
Stato | Un caricamento corretto del documento restituisce il codice di stato 200 (OK).

### 10\. Recupera da documenti condivisi (XML)

Questa azione recupera il documento dalla raccolta documenti in base all'URL relativo (struttura di cartelle) del documento.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
URI relativo documento | No | Immettere l'URL del documento, relativo a 'Documenti condivisi'. Ad esempio, immettere: *myspec1, myfolder/orders*.
Tipo file | Sì | Specificare se il file è un file binario o un file di testo.

#### Output

Nome | Descrizione
--- | ---
XML di output | Contenuto del documento
ContentTransferEncoding | Codifica di trasferimento contenuto del messaggio ("none" o "base64").
Stato | Un'esecuzione corretta dell'azione restituisce il codice di stato 200 (OK).

### 11\. Inserisci in attività (XML)

Questa azione aggiunge un elemento nell'elenco elementi. L'input previsto è un payload XML.

#### Input

Nome | Obbligatorio | Descrizione
--- | --- | ---
XML di input | Sì | Il messaggio XML che contiene i valori dei campi dell'elemento elenco da inserire. È possibile usare l'app per le API Transform per generare il messaggio XML.

**Nota** Tutti i parametri dell'elenco vengono popolati in modo dinamico. I parametri obbligatori sono visibili e i parametri facoltativi si trovano nella sezione Avanzate.

#### Output

Nome | Descrizione
--- | ---
ItemId | ID dell'elemento elenco aggiunto.
Stato | Un corretto inserimento dell'elemento elenco restituisce il codice di stato 200 (OK).


### 12\. Aggiorna attività (XML)

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


### 13\. Recupera elemento da attività (XML)

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

Il servizio app usa Gestione connessione ibrida per connettersi in modo sicuro al sistema locale. Se il connettore usa SharePoint Server in locale, è richiesta la Gestione connessione ibrida.

Vedere l'articolo relativo all'[uso di Gestione connessione ibrida](app-service-logic-hybrid-connection-manager.md).

## Più vantaggi con il connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere l'articolo relativo alla [gestione e al monitoraggio delle app per le API e del connettore](app-service-api-manage-in-portal.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-sharepoint/image_0.png
[2]: ./media/app-service-logic-connector-sharepoint/image_1.png
[3]: ./media/app-service-logic-connector-sharepoint/image_2.png
[4]: ./media/app-service-logic-connector-sharepoint/image_3.png
[5]: ./media/app-service-logic-connector-sharepoint/image_4.jpg
[6]: ./media/app-service-logic-connector-sharepoint/image_5.png
[7]: ./media/app-service-logic-connector-sharepoint/image_6.png

<!----HONumber=August15_HO9-->