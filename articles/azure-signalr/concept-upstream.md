---
title: Impostazioni upstream nel servizio Azure SignalR
description: Ottenere un'introduzione alle impostazioni upstream e ai protocolli dei messaggi upstream.
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: 1d51f5e8d2fac1e2b180a608c840d0a322e76271
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143249"
---
# <a name="upstream-settings"></a>Impostazioni upstream

Upstream è una funzionalità di anteprima che consente al servizio Azure SignalR di inviare messaggi ed eventi di connessione a un set di endpoint in modalità senza server. È possibile usare upstream per richiamare un metodo Hub da client in modalità senza server e consentire agli endpoint di ricevere notifiche quando le connessioni client vengono connesse o disconnesse.

> [!NOTE]
> Solo la modalità senza server può configurare le impostazioni upstream.

## <a name="details-of-upstream-settings"></a>Dettagli delle impostazioni upstream

Le impostazioni upstream sono costituite da un elenco di elementi sensibili agli ordini. Ogni elemento è costituito da:

* Modello URL, che specifica il punto in cui i messaggi vengono inviati a.
* Set di regole.
* Configurazioni di autenticazione. 

Quando si verifica l'evento specificato, le regole di un elemento vengono controllate una alla volta in ordine. I messaggi verranno inviati all'URL upstream del primo elemento corrispondente.

### <a name="url-template-settings"></a>Impostazioni modello URL

È possibile parametrizzare l'URL per supportare i vari modelli. Sono disponibili tre parametri predefiniti:

|Parametro predefinito|Descrizione|
|---------|---------|
|Hub| Un hub è un concetto di servizio Azure SignalR. Un hub è un'unità di isolamento. L'ambito degli utenti e del recapito dei messaggi è vincolato a un hub.|
|Categoria| Una categoria può essere uno dei valori seguenti: <ul><li>**connessioni**: eventi di durata della connessione. Viene generato quando una connessione client è connessa o disconnessa. Include eventi connessi e disconnessi.</li><li>**messaggi**: generato quando i client richiamano un metodo dell'hub. Include tutti gli altri eventi, ad eccezione di quelli nella categoria **connessioni** .</li></ul>|
|evento| Per la categoria **messages** , un evento è la destinazione in un [messaggio di chiamata](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) che i client inviano. Per la categoria **Connections** vengono utilizzati solo *Connected* e *disconnected* .|

Questi parametri predefiniti possono essere usati nel modello di URL. Quando si valuta l'URL upstream, i parametri verranno sostituiti con un valore specificato. Ad esempio: 
```
http://host.com/{hub}/api/{category}/{event}
```
Quando una connessione client nell'hub "chat" è connessa, viene inviato un messaggio all'URL seguente:
```
http://host.com/chat/api/connections/connected
```
Quando un client nell'hub "chat" richiama il metodo dell'hub `broadcast` , viene inviato un messaggio all'URL seguente:
```
http://host.com/chat/api/messages/broadcast
```

### <a name="rule-settings"></a>Impostazioni regola

È possibile impostare separatamente le regole per le regole dell' *Hub*, *le regole di categoria*e *le regole degli eventi* . La regola di corrispondenza supporta tre formati. Prendere le regole degli eventi come esempio:
- Usare un asterisco (*) per trovare la corrispondenza con qualsiasi evento.
- Usare una virgola (,) per unire più eventi. Ad esempio, `connected, disconnected` corrisponde agli eventi connessi e disconnessi.
- Usare il nome completo dell'evento per trovare la corrispondenza con l'evento. Ad esempio, `connected` corrisponde all'evento connesso.

> [!NOTE]
> Se si usa funzioni di Azure e il [trigger SignalR](../azure-functions/functions-bindings-signalr-service-trigger.md), il trigger SignalR esporrà un singolo endpoint nel formato seguente: `https://<APP_NAME>.azurewebsites.net/runtime/webhooks/signalr?code=<API_KEY>` .
> È possibile configurare solo il modello di URL in questo URL.

### <a name="authentication-settings"></a>Authentication settings

È possibile configurare l'autenticazione per ogni elemento dell'impostazione upstream separatamente. Quando si configura l'autenticazione di, un token viene impostato nell' `Authentication` intestazione del messaggio upstream. Attualmente, il servizio Azure SignalR supporta i tipi di autenticazione seguenti:
- `None`
- `ManagedIdentity`

Quando si seleziona `ManagedIdentity` , è necessario abilitare in anticipo un'identità gestita nel servizio Azure SignalR e, facoltativamente, specificare una risorsa. Per informazioni dettagliate, vedere [identità gestite per il servizio Azure SignalR](howto-use-managed-identity.md) .

## <a name="create-upstream-settings-via-the-azure-portal"></a>Creare le impostazioni upstream tramite il portale di Azure

1. Passare al servizio Azure SignalR.
2. Selezionare **Impostazioni** e passare alla **modalità servizio** a senza **server**. Verranno visualizzate le impostazioni upstream:

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="Impostazioni upstream":::

3. Aggiungere URL nel **modello URL upstream**. Impostazioni come le **regole dell'hub** mostreranno quindi il valore predefinito.
4. Per impostare le impostazioni per le **regole dell'hub**, le **regole degli eventi**, le regole di **categoria**e **l'autenticazione upstream**, selezionare il valore delle **regole dell'hub**. Viene visualizzata una pagina che consente di modificare le impostazioni:

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="Impostazioni upstream":::

5. Per impostare **l'autenticazione upstream**, assicurarsi che sia stata abilitata per prima un'identità gestita. Selezionare quindi **Usa identità gestita**. Secondo le esigenze, è possibile scegliere qualsiasi opzione in **ID risorsa di autenticazione**. Per informazioni dettagliate, vedere [identità gestite per il servizio Azure SignalR](howto-use-managed-identity.md) .

## <a name="create-upstream-settings-via-resource-manager-template"></a>Crea impostazioni upstream tramite Gestione risorse modello

Per creare le impostazioni upstream usando un [modello di Azure Resource Manager](../azure-resource-manager/templates/overview.md), impostare la `upstream` proprietà nella `properties` Proprietà. Il frammento di codice seguente mostra come impostare la `upstream` proprietà per la creazione e l'aggiornamento delle impostazioni upstream.

```JSON
{
  "properties": {
    "upstream": {
      "templates": [
        {
          "UrlTemplate": "http://host.com/{hub}/api/{category}/{event}",
          "EventPattern": "*",
          "HubPattern": "*",
          "CategoryPattern": "*",
          "Auth": {
            "Type": "ManagedIdentity",
            "ManagedIdentity": {
              "Resource": "<resource>"
            }
          }
        }
      ]
    }
  }
}
```

## <a name="serverless-protocols"></a>Protocolli senza server

Il servizio Azure SignalR invia messaggi agli endpoint che seguono i protocolli seguenti.

### <a name="method"></a>Metodo

POST

### <a name="request-header"></a>Intestazione della richiesta

|Nome |Descrizione|
|---------|---------|
|X-ASRS-Connection-ID |ID connessione per la connessione client.|
|X-ASRS-Hub |Hub a cui appartiene la connessione client.|
|X-ASRS-Category |Categoria a cui appartiene il messaggio.|
|X-ASRS-evento |Evento a cui appartiene il messaggio.|
|X-ASRS-Signature |Codice HMAC (hash-based Message Authentication Code) usato per la convalida. Per informazioni dettagliate, vedere [firma](#signature) .|
|X-ASRS-User-Claims |Gruppo di attestazioni della connessione client.|
|X-ASRS-User-ID |Identità utente del client che invia il messaggio.|
|X-ASRS-client-query |Query della richiesta quando i client si connettono al servizio.|
|Authentication |Token facoltativo quando si usa `ManagedIdentity` . |

### <a name="request-body"></a>Corpo della richiesta

#### <a name="connected"></a>Connesso

Content-Type: application/json

#### <a name="disconnected"></a>Disconnesso

Tipo di contenuto: `application/json`

|Nome  |Type  |Descrizione  |
|---------|---------|---------|
|Errore |string |Messaggio di errore di una connessione chiusa. Vuota quando le connessioni si chiudono senza errori.|

#### <a name="invocation-message"></a>Messaggio di chiamata

Content-Type: `application/json` o `application/x-msgpack`

|Nome  |Type  |Descrizione  |
|---------|---------|---------|
|InvocationId |string | Stringa facoltativa che rappresenta un messaggio di chiamata. Trovare i dettagli nelle [chiamate](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations).|
|Destinazione |string | Uguale all'evento e uguale a quello della destinazione in un messaggio di [chiamata](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding). |
|Argomenti |Matrice di oggetti |Matrice contenente gli argomenti da applicare al metodo a cui si fa riferimento in `Target` . |

### <a name="signature"></a>Firma

Il servizio calcolerà il codice SHA256 per il `X-ASRS-Connection-Id` valore usando la chiave di accesso primaria e la chiave di accesso secondaria come `HMAC` chiave. Il servizio lo imposterà nell' `X-ASRS-Signature` intestazione quando si effettuano richieste HTTP a upstream:
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>Passaggi successivi

- [Identità gestite per il servizio Azure SignalR](howto-use-managed-identity.md)
- [Sviluppo e configurazione di Funzioni di Azure e con il Servizio Azure SignalR](signalr-concept-serverless-development-config.md)