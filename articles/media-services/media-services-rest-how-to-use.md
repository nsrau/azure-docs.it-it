<properties 
	pageTitle="Informazioni generali sull'API REST di Servizi multimediali - Azure" 
	description="Informazioni generali sull'API REST di Servizi multimediali" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="juliako"/>


# Informazioni generali sull'API REST di Servizi multimediali 

Servizi multimediali di Microsoft Azure è un servizio che accetta richieste HTTP basate su OData e può rispondere in formato JSON dettagliato o atom+pub. Poiché Servizi multimediali è conforme alle linee guida di progettazione di Azure, esiste un set di intestazioni HTTP obbligatorie che devono essere usate da ogni client per la connessione a Servizi multimediali, oltre a un set di intestazioni facoltative. Le seguenti sezioni descrivono le intestazioni e i verbi HTTP che è possibile usare quando si creano richieste e si ricevono risposte da Servizi multimediali.


## Intestazioni delle richieste HTTP standard supportate da Servizi multimediali

Per ogni chiamata effettuata in Servizi multimediali, è necessario includere nella richiesta un set di intestazioni obbligatorie ed eventualmente un set di intestazioni facoltative. Nella seguente tabella sono elencate le intestazioni obbligatorie:


<table border="1"> <tr><th>Intestazione</th><th>Tipo</th><th>Valore</th></tr> <tr><td>Autorizzazione</td><td>Bearer</td><td>Bearer è l’unico meccanismo di autorizzazione accettato. Il valore deve includere anche il token di accesso fornito da ACS.</td></tr> <tr><td>x-ms-version</td><td>Decimale</td><td>2.11</td></tr> <tr><td>DataServiceVersion</td><td>Decimale</td><td>3.0</td></tr> <tr><td>MaxDataServiceVersion</td><td>Decimale</td><td>3.0</td></tr> </table><br/>


>[AZURE.NOTE]Poiché Servizi multimediali usa OData per esporre l'archivio di metadati di asset sottostante tramite le API REST, le intestazioni DataServiceVersion e MaxDataServiceVersion devono essere incluse in tutte le richieste. Se non vengono incluse, Servizi multimediali suppone che il valore di DataServiceVersion in uso sia 3.0.

Nella seguente tabella è riportato un set di intestazioni facoltative:

<table border="1"> <tr><th>Intestazione</th><th>Tipo</th><th>Valore</th></tr> <tr><td>Data</td><td>Data RFC 1123</td><td>Timestamp della richiesta</td></tr> <tr><td>Accept</td><td>Tipo di contenuto</td><td>Tipo di contenuto richiesto per la risposta, ad esempio: <ul><li>application/json;odata = verbose</li><li>application/atom+xml</li></ul></br> Nelle risposte può essere presente un tipo di contenuto diverso, ad esempio di recupero BLOB. In questo caso, una risposta corretta deve contenere il flusso BLOB come payload.</td></tr> <tr><td>Codifica Accept</td><td>Gzip, deflate</td><td>Codifica GZIP e DEFLATE, se applicabile. Nota: in caso di risorse di grandi dimensioni, Servizi multimediali può ignorare questa intestazione e restituire dati non compressi. </td></tr> <tr><td>Accept-Language</td><td>"it", "es" e così via.</td><td>Specifica la lingua preferita per la risposta.</td></tr> <tr><td>Accept-Charset</td><td>Tipo di set di caratteri, ad esempio “UTF-8”</td><td>L’impostazione predefinita è UTF-8.</td></tr> <tr><td>X-HTTP-Method</td><td>Metodo HTTP</td><td>Consente ai client o ai firewall che non supportano metodi HTTP come PUT o DELETE di usarli, con tunneling tramite una chiamata GET.</td></tr> <tr><td>Content-Type</td><td>Tipo di contenuto</td><td>Tipo di contenuto del corpo delle richieste PUT o POST.</td></tr> <tr><td>client-request-id</td><td>Stringa</td><td>Valore definito dal chiamante che identifica la richiesta fornita. Se specificato, questo valore viene incluso nel messaggio di risposta per consentire il mapping della richiesta. <br/><br/> <b>Importante</b><br/> Le dimensioni di questi valori dovrebbero essere limitate a 2096 b (2 k).</td></tr> </table><br/>


## Intestazioni delle risposte HTTP standard supportate da Servizi multimediali

Nella seguente tabella è riportato un set di intestazioni che possono essere restituite a seconda della risorsa richiesta e dell'azione che si intende eseguire.


<table border="1"> <tr><th>Intestazione</th><th>Tipo</th><th>Valore</th></tr> <tr><td>request-id</td><td>Stringa</td><td>Identificatore univoco per l'operazione corrente, generato dal servizio.</td></tr> <tr><td>client-request-id</td><td>Stringa</td><td>Identificatore specificato dal chiamante nella richiesta originale, se presente.</td></tr> <tr><td>Data</td><td>Data RFC 1123</td><td>Data di elaborazione della richiesta.</td></tr> <tr><td>Content-Type</td><td>Variabile</td><td>Tipo di contenuto del corpo della risposta.</td></tr> <tr><td>Content-Encoding</td><td>Variabile</td><td>Gzip o deflate, a seconda delle esigenze.</td></tr> </table><br/>

## Verbi HTTP standard supportati da Servizi multimediali

Nella seguente tabella è riportato l'elenco completo dei verbi HTTP che è possibile usare per la creazione di richieste HTTP:


<table border="1"> <tr><th>Verbo</th><th>Descrizione</th></tr> <tr><td>GET</td><td>Restituisce il valore corrente di un oggetto.</td></tr> <tr><td>POST</td><td>Crea un oggetto in base ai dati forniti o invia un comando.</td></tr> <tr><td>PUT</td><td>Sostituisce un oggetto o ne crea uno nuovo con nome, se applicabile.</td></tr> <tr><td>DELETE</td><td>Elimina un oggetto.</td></tr> <tr><td>MERGE</td><td>Aggiorna un oggetto esistente con le modifiche alle proprietà denominate.</td></tr> <tr><td>HEAD</td><td>Restituisce i metadati di un oggetto per una risposta GET.</td></tr> </table><br/>

## Individuazione del modello di Servizi multimediali

Per rendere più facilmente individuabili le entità di Servizi multimediali, è possibile usare l'operazione $metadata, che consente di recuperare tutti i tipi validi di entità, le proprietà di entità, le associazioni, le funzioni, le azioni e così via. Il seguente esempio illustra come costruire l'URI: https://media.windows.net/API/$metadata.

Aggiungere "?api-version=2.x" alla fine dell'URI se si desidera visualizzare i metadati in un browser o se nella richiesta non è stata inclusa l'intestazione x-ms-version.


<!-- Anchors. -->


<!-- URLs. -->
  [Management Portal]: http://manage.windowsazure.com/



 

<!---HONumber=July15_HO4-->