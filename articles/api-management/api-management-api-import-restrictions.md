---
title: Restrizioni e dettagli del supporto dei formati API
titleSuffix: Azure API Management
description: I dettagli dei problemi noti e delle restrizioni sui formati Open API, WSDL e WADL supportano in Gestione API di Azure.Details of known issues and restrictions on Open API, WSDL, and WADL formats support in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/02/2020
ms.author: apimpm
ms.openlocfilehash: 61d43addfdf9008cb7aa8a073dcf3bb702cb55f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513372"
---
# <a name="api-import-restrictions-and-known-issues"></a>Problemi noti e limitazioni dell'importazione dell'API

## <a name="about-this-list"></a>Informazioni sull'elenco

Quando si importa un'API, è possibile incontrare alcune restrizioni o identificare i problemi che devono essere corretti prima di poter eseguire correttamente l'importazione. In questo articolo vengono documentate queste limitazioni, organizzate in base al formato di importazione dell'API. Viene inoltre descritto il funzionamento dell'esportazione OpenAPI.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>Limitazioni dell'importazione OpenAPI/Swagger

Se vengono restituiti errori durante l'importazione del documento OpenAPI, verificare che sia stato prima convalidato. A tale scopo, usare la finestra di progettazione nel portale di Azure (Progettazione - Front End - Editor della specifica OpenAPI) o usare uno strumento di terze parti come <a href="https://editor.swagger.io">Swagger Editor</a>.

### <a name="general"></a><a name="open-api-general"> </a>Informazioni generali

-   I parametri obbligatori per percorso e query devono avere nomi univoci. In OpenAPI un nome di parametro deve essere univoco solo all'interno di una determinata posizione, ad esempio percorso, query, intestazione. In Gestione API, invece, le operazioni possono essere discriminate in base a parametri sia di percorso che di query, cosa che non è possibile in OpenAPI. Per questo motivo è necessario che i nomi dei parametri siano univoci all'interno dell'intero modello di URL.
-   `\$ref`i puntatori non possono fare riferimento a file esterni.
-   `x-ms-paths`e `x-servers` sono le uniche estensioni supportate.
-   Le estensioni personalizzate vengono ignorate in fase di importazione e non vengono salvate o mantenute per l'esportazione.
-   `Recursion`- Gestione API non supporta le definizioni definite in modo ricorsivo (ad esempio, gli schemi che fanno riferimento a se stessi).
-   L'URL del file di origine, se disponibile, viene applicato agli URL di server relativi.
-   Le definizioni di sicurezza vengono ignorate.
-   Le definizioni dello schema inline per le operazioni API non sono supportate. Le definizioni dello schema sono definite nell'ambito dell'API e sono disponibili riferimenti negli ambiti di richiesta o risposta delle operazioni API.
-   Un parametro URL definito deve far parte del modello di URL.
-   `Produces`Parola chiave, che descrive i tipi MIME restituiti da un'API, non è supportata. 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI versione 2

-   È supportato solo il formato JSON.

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI versione 3

-   Se `servers` ne vengono specificati molti, Gestione API tenterà di selezionare il primo URL HTTP. Se non vi sono URL HTTPs, selezionerà il primo URL HTTP. Se non vi sono nemmeno URL HTTP, l'URL del server sarà vuoto.
-   `Examples`non è supportato, ma `example` è.

## <a name="openapi-import-update-and-export-mechanisms"></a>Meccanismi di importazione, aggiornamento ed esportazione OpenAPI

### <a name="add-new-api-via-openapi-import"></a>Aggiungere una nuova API tramite l'importazione OpenAPIAdd new API via OpenAPI import

Per ogni operazione trovata nel documento OpenAPI, verrà creata una nuova operazione `operationId` `summary` con il nome della risorsa di Azure e il nome visualizzato impostati rispettivamente su e rispettivamente. `operationId`valore viene normalizzato seguendo le regole descritte di seguito. `summary`valore viene importato così com'è e la sua lunghezza è limitata a 300 caratteri.

Se `operationId` non viene specificato, ovvero non `null`presente, o vuoto, il valore del nome della risorsa di `get-foo`Azure verrà generato combinando il metodo HTTP e il modello di percorso, ad esempio .

Se `summary` non è specificato (ovvero, `null`non è `display name` presente, o `operationId`vuoto), il valore verrà impostato su . Se `operationId` non viene specificato, il valore del nome visualizzato verrà generato `Get - /foo`combinando il metodo HTTP e il modello di percorso, ad esempio .

### <a name="update-an-existing-api-via-openapi-import"></a>Aggiornare un'API esistente tramite l'importazione OpenAPIUpdate an existing API via OpenAPI import

Durante l'importazione l'API esistente viene modificato in modo da corrispondere all'API descritta nel documento OpenAPI. Ogni operazione nel documento OpenAPI viene confrontata `operationId` con l'operazione esistente confrontandone il valore con il nome di risorsa di Azure dell'operazione esistente.

Se viene trovata una corrispondenza, le proprietà dell'operazione esistente verranno aggiornate "sul posto".

Se non viene trovata una corrispondenza, verrà creata una nuova operazione utilizzando le regole descritte nella sezione precedente. Per ogni nuova operazione, l'importazione tenterà di copiare i criteri da un'operazione esistente con lo stesso metodo HTTP e lo stesso modello di percorso.

Tutte le operazioni esistenti non corrispondenti verranno eliminate.

Per rendere l'importazione più prevedibile, segui queste linee guida:

- Assicurarsi di `operationId` specificare la proprietà per ogni operazione.
- Evitare `operationId` di modificare dopo l'importazione iniziale.
- Non `operationId` modificare mai e il metodo HTTP o il modello di percorso contemporaneamente.

### <a name="export-api-as-openapi"></a>Api di esportazione come OpenAPIExport API as OpenAPI

Per ogni operazione, il nome della risorsa `operationId`di Azure verrà esportato `summary`come nome visualizzato e come nome visualizzato come file .
Regole di normalizzazione per operationIdNormalization rules for operationId

- Consente di convertire la stringa in caratteri minuscoli.
- Sostituire ogni sequenza di caratteri non alfanumerici `GET-/foo/{bar}?buzz={quix}` con un `get-foo-bar-buzz-quix-`singolo trattino, ad esempio, verrà trasformata in .
- I trattini di taglio su `get-foo-bar-buzz-quix-` entrambi i lati, ad esempio,`get-foo-bar-buzz-quix`
- Tronca per contenere 76 caratteri, quattro caratteri inferiori al limite massimo per un nome di risorsa.
- Utilizzare i quattro caratteri rimanenti per un suffisso `-1, -2, ..., -999`di deduplicazione, se necessario, sotto forma di .


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

I file WSDL vengono utilizzati per creare API SOAP pass-through e SOAP-to-REST.

-   **Associazioni SOAP**: solo le associazioni SOAP con stile di codifica "document" e "literal" sono supportate. Non è disponibile il supporto per lo stile di codifica SOAP "rpc".
-   **WSDL:Import**: questo attributo non è supportato. I clienti devono unire le importazioni in un solo documento.
-   **Messaggi con più parti**: questo tipo di messaggi non è supportato.
-   **wsHttpBinding di WCF**: i servizi SOAP creati con Windows Communication Foundation devono usare basicHttpBinding poiché wsHttpBinding non è supportato.
-   **MTOM**: i servizi che usano MTOM <em>potrebbero</em> funzionare. Al momento il supporto ufficiale non è previsto.
-   **Ricorsione**: i tipi definiti in modo ricorsivo, che, ad esempio, fanno riferimento a una matrice di se stessi, non sono supportati da APIM.
-   **Più spazi dei nomi**: possono essere usati più spazi dei nomi in uno schema, ma solo lo spazio dei nomi di destinazione può essere usato per definire le parti del messaggio. Gli spazi dei nomi diversi dalla destinazione, utilizzati per definire altri elementi di input o output, non vengono mantenuti. Sebbene un documento WSDL di questo tipo possa essere importato, in caso di esportazione tutte le parti del messaggio avranno lo spazio dei nomi di destinazione del documento WSDL.
-   **Matrici:** la trasformazione DA SOAP a REST supporta solo le matrici di cui è stato eseguito il wrapping illustrato nell'esempio seguente:

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"></a><a name="wadl"> </a>WADL

Attualmente non sono noti problemi di importazione del formato WADL.
