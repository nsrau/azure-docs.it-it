---
title: Codici di errore di Servizi multimediali di Azure | Microsoft Docs
description: Questo argomento fornisce una panoramica dei codici di errore di Servizi multimediali di Azure.
author: Juliako
manager: SyntaxC4
editor: 
services: media-services
documentationcenter: 
ms.assetid: d3a62a64-7608-4b17-8667-479b26ba0d6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 668ba10474fc1b01d5ebccbff1f50fe09ade5f30
ms.contentlocale: it-it
ms.lasthandoff: 08/01/2017

---
# <a name="azure-media-services-error-codes"></a>Codici di errore di Servizi multimediali di Azure
Quando si usa Servizi multimediali di Microsoft Azure, è possibile ricevere codici di errore HTTP dal servizio a seconda del problema, ad esempio scadenza dei token di autenticazione o azioni non supportate in Servizi multimediali. Di seguito è riportato un elenco di **codici di errore HTTP** che possono essere restituiti da Servizi multimediali e le cause che li possono generare.  

## <a name="400-bad-request"></a>400 - Richiesta non valida
La richiesta contiene informazioni non valide e viene rifiutata a causa di uno dei motivi seguenti:

* Viene specificata una versione API non supportata. Per la versione più aggiornata, vedere [Setup for Media Services REST API Development](media-services-rest-how-to-use.md) (Configurazione della distribuzione dell'API REST di Servizi multimediali).
* Non viene specificata la versione dell'API di Servizi multimediali. Per informazioni su come specificare la versione dell'API, vedere [Informazioni di riferimento sull'API REST di Servizi multimediali](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).
  
  > [!NOTE]
  > Se si usano gli SDK .NET o Java per connettersi a Servizi multimediali, la versione dell'API viene specificata automaticamente ogni volta che si tenta di eseguire un'azione su Servizi multimediali.
  > 
  > 
* Viene specificata una proprietà non definita. Il nome della proprietà è nel messaggio di errore. Solo le proprietà che fanno parte di una determinata entità possono essere specificate. Per un elenco delle entità e delle relative proprietà, vedere [Azure Media Services REST API Reference](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) (Informazioni di riferimento relative all'API REST di Servizi multimediali di Azure).
* Viene specificato un valore di proprietà non valido. Il nome della proprietà è nel messaggio di errore. Per i tipi di proprietà validi e i relativi valori, fare clic sul collegamento precedente.
* Il valore di una proprietà risulta mancante ed è obbligatorio.
* Parte dell'URL specificato contiene un valore non valido.
* È stato effettuato un tentativo di aggiornamento di una proprietà WriteOnce.
* È stato effettuato un tentativo di creazione di un'istanza di Job che dispone di un'istanza di Asset di input con un'istanza di AssetFile primaria che non è stata specificata o non può essere determinata.
* È stato effettuato un tentativo di aggiornamento di un localizzatore di firma di accesso condiviso. I localizzatori di firma di accesso condiviso possono solo essere creati o eliminati. I localizzatori di flusso possono essere aggiornati. Per altre informazioni, vedere [Locators](https://docs.microsoft.com/rest/api/media/operations/locator) (Localizzatori).
* È stata inviata un'operazione o una query non supportata.

## <a name="401-unauthorized"></a>401 - Non autorizzato
La richiesta non può essere autenticata (prima di poterla autorizzare) a causa di uno dei motivi seguenti:

* L'intestazione di autenticazione risulta mancante.
* Il valore dell'intestazione di autenticazione non è valido.
  * Il token è scaduto. 
  * Il token contiene una firma non valida.

## <a name="403-forbidden"></a>403 - Accesso negato
La richiesta non è consentita per uno dei motivi seguenti:

* Non è possibile trovare l'account di Servizi multimediali o è stato eliminato.
* L'account di Servizi multimediali è disattivato e il tipo di richiesta non è HTTP GET. Le operazioni del servizio restituiranno una risposta 403.
* Il token di autenticazione non contiene informazioni sulle credenziali dell'utente: nome account e/o ID sottoscrizione. È possibile trovare queste informazioni nell'estensione dell'interfaccia utente di Servizi multimediali per l'account di Servizi multimediali nel portale di gestione di Azure.
* Non è possibile accedere alla risorsa.
  
  * È stato effettuato un tentativo di uso di un'istanza di MediaProcessor non disponibile per l'account di Servizi multimediali.
  * È stato effettuato un tentativo di aggiornamento di un'istanza di JobTemplate definita da Servizi multimediali.
  * È stato effettuato un tentativo di sovrascrittura di altre istanze di Locator dell'account di Servizi multimediali.
  * È stato effettuato un tentativo di sovrascrittura di altre istanze di ContentKey dell'account di Servizi multimediali.
* Non è stato possibile creare la risorsa a causa di una quota del servizio raggiunta per l'account di Servizi multimediali. Per altre informazioni sulle quote del servizio, vedere [Quote e limitazioni](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 - Non trovato
La richiesta non è consentita su una risorsa per uno dei motivi seguenti:

* È stato effettuato un tentativo di aggiornamento di un'entità che non esiste.
* È stato effettuato un tentativo di eliminazione di un'entità che non esiste.
* È stato effettuato un tentativo di creazione di un'entità che si collega a un'entità che non esiste.
* È stato effettuato un tentativo di ottenere un'entità che non esiste.
* È stato effettuato un tentativo di specificare un account di archiviazione non associato all'account di Servizi multimediali.  

## <a name="409-conflict"></a>409 - Conflitto
La richiesta non è consentita per uno dei motivi seguenti:

* Una o più istanze di AssetFile hanno il nome specificato nell'istanza di Asset.
* È stato effettuato un tentativo di creazione di una seconda istanza primaria di AssetFile nell'istanza di Asset.
* È stato effettuato un tentativo di creazione di un'istanza di ContentKey con l'ID specificato già in uso.
* È stato effettuato un tentativo di creazione di un'istanza di Locator con l'ID specificato già in uso.
* Più istanze di IngestManifestFile hanno il nome specificato nell'istanza di IngestManifest.
* È stato effettuato un tentativo di collegamento di una seconda crittografia di archiviazione ContentKey all'istanza di Asset crittografata nell'archiviazione.
* È stato effettuato un tentativo di collegamento della stessa istanza di ContentKey all'istanza di Asset.
* È stato effettuato un tentativo di creazione di un localizzatore in un'istanza di Asset il cui contenitore di archiviazione risulta mancante o non è più associato all'istanza di Asset.
* È stato effettuato un tentativo di creazione di un localizzatore in un'istanza di Asset che ha già cinque localizzatori in uso. Archiviazione di Azure applica il limite di cinque criteri di accesso condiviso in un contenitore di archiviazione.
* Il collegamento di account di archiviazione di un'istanza di Asset a un'istanza di IngestManifestAsset non corrisponde all'account di archiviazione usato dall'istanza di IngestManifest.  

## <a name="500-internal-server-error"></a>500 - Errore interno del server
Durante l'elaborazione della richiesta, in Servizi multimediali viene generato un errore che impedisce di continuare l'elaborazione. Il problema può dipendere da una delle cause seguenti:

* La creazione di un'istanza di Asset o Job ha esito negativo a causa della temporanea mancanza di disponibilità di informazioni sulle quote di servizio dell'account di Servizi multimediali.
* La creazione di un contenitore di archiviazione BLOB di Asset o IngestManifest ha esito negativo perché le informazioni sull'account di archiviazione dell'account sono temporaneamente non disponibili.
* Un altro errore imprevisto.

## <a name="503-service-unavailable"></a>503 - Servizio non disponibile
Il server non è attualmente in grado di ricevere richieste. Questo errore può essere causato da un numero eccessivo di richieste al servizio. Il meccanismo di limitazione delle richieste di Servizi multimediali limita l'uso delle risorse per le applicazioni che inviano un numero elevato di richieste al servizio.

> [!NOTE]
> Controllare il messaggio di errore e la stringa del codice di errore per ottenere informazioni più dettagliate sul motivo per cui è stato generato un errore 503. Questo errore non indica necessariamente la limitazione delle richieste.
> 
> 

Possibili descrizioni dello stato sono:

* "Il server è occupato. Esecuzioni precedenti di questo tipo di richiesta hanno impiegato più di {0} secondi."
* "Il server è occupato. Più di {0} richieste al secondo possono essere limitate."
* "Il server è occupato. Più di {0} richieste in {1} secondi possono essere limitate."

Per gestire questo errore, è consigliabile usare la logica di ripetizione dei tentativi con interruzione temporanea esponenziale. Ciò significa usare attese progressivamente più lunghe tra le ripetizioni dei tentativi per le risposte di errore consecutive.  Per altre informazioni, vedere [Transient Fault Handling Application Block](https://msdn.microsoft.com/library/hh680905.aspx) (Blocco di applicazioni per la gestione degli errori temporanei).

> [!NOTE]
> Se si usa [Azure Media Services SDK for .Net](https://github.com/Azure/azure-sdk-for-media-services/tree/master), la logica di ripetizione dei tentativi per l'errore 503 è stata implementata dall'SDK.  
> 
> 

## <a name="see-also"></a>Vedere anche
[Codici di errore di gestione di Servizi multimediali](http://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


