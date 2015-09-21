<properties 
	pageTitle="Informazioni generali sull'API REST di Servizi multimediali | Microsoft Azure" 
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
	ms.date="09/07/2015"
	ms.author="juliako"/>


# Informazioni generali sull'API REST di Servizi multimediali 

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Servizi multimediali di Microsoft Azure è un servizio che accetta richieste HTTP basate su OData e può rispondere in formato JSON dettagliato o atom+pub. Poiché Servizi multimediali è conforme alle linee guida di progettazione di Azure, esiste un set di intestazioni HTTP obbligatorie che devono essere usate da ogni client per la connessione a Servizi multimediali, oltre a un set di intestazioni facoltative. Le seguenti sezioni descrivono le intestazioni e i verbi HTTP che è possibile usare quando si creano richieste e si ricevono risposte da Servizi multimediali.


## Intestazioni delle richieste HTTP standard supportate da Servizi multimediali

Per ogni chiamata effettuata in Servizi multimediali, è necessario includere nella richiesta un set di intestazioni obbligatorie ed eventualmente un set di intestazioni facoltative. Nella seguente tabella sono elencate le intestazioni obbligatorie:


Intestazione|Tipo|Valore
---|---|---
Autorizzazione|Bearer|Bearer è l'unico meccanismo di autorizzazione accettato. Il valore deve includere anche il token di accesso fornito da ACS.
x-ms-version|Decimale|2\.11
DataServiceVersion|Decimale|3\.0
MaxDataServiceVersion|Decimale|3\.0



>[AZURE.NOTE]Poiché Servizi multimediali usa OData per esporre l'archivio di metadati di asset sottostante tramite le API REST, le intestazioni DataServiceVersion e MaxDataServiceVersion devono essere incluse in tutte le richieste. Se non vengono incluse, Servizi multimediali suppone che il valore di DataServiceVersion in uso sia 3.0.

Nella seguente tabella è riportato un set di intestazioni facoltative:

Intestazione|Tipo|Valore
---|---|---
Date|Data RFC 1123|Timestamp della richiesta.
Accept|Tipo di contenuto|Il tipo di contenuto richiesto per la risposta, ad esempio: <p> -application/json;odata=verbose<p> - application/atom+xml<p> Nelle risposte può essere presente un tipo di contenuto diverso, ad esempio di recupero BLOB. In questo caso, una risposta corretta deve contenere il flusso BLOB come payload.
Accept-Encoding|Gzip, deflate|Codifica GZIP e DEFLATE, se applicabile. Nota: in caso di risorse di grandi dimensioni, Servizi multimediali può ignorare questa intestazione e restituire dati non compressi.
Accept-Language|"en", "es" e così via.|Lingua preferita per la risposta.
Accept-Charset|Tipo di set di caratteri, ad esempio "UTF-8"|L'impostazione predefinita è UTF-8.
X-HTTP-Method|Metodo HTTP|Consente ai client o ai firewall che non supportano metodi HTTP come PUT o DELETE di usarli, con tunneling tramite una chiamata GET.
Content-Type|Tipo di contenuto|Tipo di contenuto del corpo delle richieste PUT o POST.
client-request-id|Stringa|Valore definito dal chiamante che identifica la richiesta fornita. Se specificato, questo valore verrà incluso nel messaggio di risposta come un modo per eseguire il mapping della richiesta.<p><p>**Importante**<p>I valori devono essere impostati su 2096b (2k).

## Intestazioni delle risposte HTTP standard supportate da Servizi multimediali

Nella seguente tabella è riportato un set di intestazioni che possono essere restituite a seconda della risorsa richiesta e dell'azione che si intende eseguire.


Intestazione|Tipo|Valore
---|---|---
request-id|Stringa|Identificatore univoco per l'operazione corrente, generato dal servizio.
client-request-id|Stringa|Identificatore specificato dal chiamante nella richiesta originale, se presente.
Date|Data RFC 1123|Data di elaborazione della richiesta.
Content-Type|Variabile|Tipo di contenuto del corpo della risposta.
Content-Encoding|Variabile|Gzip o deflate, a seconda delle esigenze.


## Verbi HTTP standard supportati da Servizi multimediali

Nella seguente tabella è riportato l'elenco completo dei verbi HTTP che è possibile usare per la creazione di richieste HTTP:


Verbo|Descrizione
---|---
GET|Restituisce il valore corrente di un oggetto.
POST|Crea un oggetto in base ai dati forniti o invia un comando.
PUT|Sostituisce un oggetto o ne crea uno nuovo con nome, se applicabile.
DELETE|Elimina un oggetto.
MERGE|Aggiorna un oggetto esistente con le modifiche alle proprietà denominate.
HEAD|Restituisce i metadati di un oggetto per una risposta GET.


## Individuazione del modello di Servizi multimediali

Per rendere più facilmente individuabili le entità di Servizi multimediali, è possibile usare l'operazione $metadata, che consente di recuperare tutti i tipi validi di entità, le proprietà di entità, le associazioni, le funzioni, le azioni e così via. Il seguente esempio illustra come costruire l'URI: https://media.windows.net/API/$metadata.

Aggiungere "?api-version=2.x" alla fine dell'URI se si desidera visualizzare i metadati in un browser o se nella richiesta non è stata inclusa l'intestazione x-ms-version.




##Percorsi di apprendimento di Media Services

È possibile visualizzare i percorsi di apprendimento AMS qui:

- [Flusso di lavoro AMS Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Flusso di lavoro AMS Streaming su richiesta](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)


<!-- Anchors. -->


<!-- URLs. -->
  [Management Portal]: http://manage.windowsazure.com/



 

<!---HONumber=Sept15_HO2-->