---
title: Criteri di integrazione Dapr di gestione API di Azure | Microsoft Docs
description: Informazioni sui criteri di gestione API di Azure per interagire con le estensioni di microservizi Dapr.
author: vladvino
ms.author: vlvinogr
ms.date: 10/23/2020
ms.topic: article
ms.service: api-management
ms.openlocfilehash: 2bf9c4d233cfad454d63da4dce30a38af80d24ab
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92558398"
---
# <a name="api-management-dapr-integration-policies"></a>Criteri di integrazione Dapr di gestione API

Questo argomento fornisce un riferimento per i criteri di gestione API di integrazione Dapr. Dapr è un runtime portatile per la creazione di applicazioni basate su microservizi con e senza stato con qualsiasi linguaggio o Framework. Codifica i modelli di microservizi comuni, ad esempio l'individuazione e la chiamata del servizio con la logica di ripetizione dei tentativi di compilazione, la pubblicazione e la sottoscrizione con semantica di recapito at-least-once o risorse di binding collegabili per semplificare la composizione usando servizi esterni. Passare a [dapr.io](https://dapr.io) per informazioni dettagliate e istruzioni su come iniziare a usare dapr. Per informazioni sull'aggiunta e sulla configurazione dei criteri, vedere [Criteri di Gestione API](api-management-howto-policies.md).

> [!CAUTION]
> I criteri a cui si fa riferimento in questo argomento sono disponibili in anteprima pubblica e sono soggetti a condizioni per l' [utilizzo supplementari per le anteprime Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> I criteri a cui viene fatto riferimento in questo argomento funzionano solo nella [versione self-hosted del gateway di gestione API con il](self-hosted-gateway-overview.md) supporto di Dapr abilitato.

## <a name="enable-dapr-support-in-the-self-hosted-gateway"></a>Abilitare il supporto di Dapr nel gateway self-hosted

Per abilitare il supporto di Dapr nel gateway self-hosted, aggiungere le [annotazioni Dapr](https://github.com/dapr/docs/blob/master/howto/configure-k8s/README.md) seguenti al [modello di distribuzione Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md) sostituendo "app-name" con un nome desiderato. Procedura dettagliata per la configurazione e l'uso di gestione API con Dapr è disponibile [qui](https://aka.ms/apim/dapr/walkthru).
```yml
template:
    metadata:
      labels:
        app: app-name
      annotations:
        dapr.io/enabled: "true"
        dapr.io/app-id: "app-name"
```


## <a name="distributed-application-runtime-dapr-integration-policies"></a>Criteri di integrazione Dapr (Distributed Apps Runtime)

-  [Inviare una richiesta a un servizio](api-management-dapr-policies.md#invoke): usa il runtime Dapr per individuare e comunicare in modo affidabile con un microservizio Dapr. Per ulteriori informazioni sulla chiamata del servizio in Dapr, vedere la descrizione nel file [Leggimi](https://github.com/dapr/docs/blob/master/concepts/service-invocation/README.md#service-invocation) .
-  [Invia messaggio a pub/argomento secondario](api-management-dapr-policies.md#pubsub): usa il runtime Dapr per pubblicare un messaggio in un argomento di pubblicazione/sottoscrizione. Per ulteriori informazioni sulla messaggistica di pubblicazione/sottoscrizione in Dapr, vedere la descrizione nel file [Leggimi](https://github.com/dapr/docs/blob/master/concepts/publish-subscribe-messaging/README.md) .
-  [Binding di output del trigger](api-management-dapr-policies.md#bind): usa il runtime Dapr per richiamare un sistema esterno tramite l'associazione di output. Per ulteriori informazioni sulle associazioni in Dapr, vedere la descrizione nel file [Leggimi](https://github.com/dapr/docs/blob/master/concepts/bindings/README.md) .

## <a name="send-request-to-a-service"></a><a name="invoke"></a> Inviare una richiesta a un servizio

Questo criterio imposta l'URL di destinazione per la richiesta corrente di `http://localhost:3500/v1.0/invoke/{app-id}/method/{method-name}` sostituzione dei parametri del modello con i valori specificati nell'istruzione dei criteri.

Il criterio presuppone che Dapr venga eseguito in un contenitore sidecar nello stesso pod del gateway. Al momento della ricezione della richiesta, il runtime di Dapr esegue l'individuazione del servizio e la chiamata effettiva, inclusa la possibile conversione del protocollo tra HTTP e gRPC, tentativi, traccia distribuita e gestione degli errori.

### <a name="policy-statement"></a>Istruzione del criterio

```xml
<set-backend-service backend-id="dapr" dapr-app-id="app-id" dapr-method="method-name" />
```

### <a name="examples"></a>Esempi

#### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato come richiamare il metodo denominato "back" nel microservizio denominato "echo". Il `set-backend-service` criterio imposta l'URL di destinazione. Il `forward-request` criterio Invia la richiesta al runtime di Dapr, che lo recapita al microservizio.

`forward-request`Per maggiore chiarezza, il criterio è riportato di seguito. I criteri sono in genere "ereditati" dall'ambito globale tramite la `base` parola chiave.

```xml
<policies>
    <inbound>
        <base />
        <set-backend-service backend-id="dapr" dapr-app-id="echo" dapr-method="back" />
    </inbound>
    <backend>
        <forward-request />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

### <a name="elements"></a>Elementi

| Elemento             | Descrizione  | Obbligatorio |
|---------------------|--------------|----------|
| set-backend-service | Elemento radice | Sì      |

### <a name="attributes"></a>Attributes

| Attributo        | Descrizione                     | Obbligatorio | Valore predefinito |
|------------------|---------------------------------|----------|---------|
| backend-id       | Deve essere impostato su "dapr"           | Sì      | N/D     |
| dapr-App-ID      | Nome del microservizio di destinazione. Esegue il mapping al parametro [AppID](https://github.com/dapr/docs/blob/master/reference/api/service_invocation_api.md) in Dapr.| Sì | N/D |
| dapr-metodo      | Nome del metodo o URL da richiamare nel microservizio di destinazione. Esegue il mapping al parametro del [nome di metodo](https://github.com/dapr/docs/blob/master/reference/api/service_invocation_api.md) in Dapr.| Sì | N/D |

### <a name="usage"></a>Usage

Questo criterio può essere usato nelle [sezioni](./api-management-howto-policies.md#sections) e negli [ambiti](./api-management-howto-policies.md#scopes) del criterio seguenti.

- **Sezioni del criterio:** inbound
- **Ambiti del criterio:** tutti gli ambiti

## <a name="send-message-to-pubsub-topic"></a><a name="pubsub"></a> Invia messaggio a pub/argomento secondario

Questo criterio indica al gateway di gestione API di inviare un messaggio a un argomento di pubblicazione/sottoscrizione Dapr. Il criterio esegue questa operazione effettuando una richiesta HTTP POST per `http://localhost:3500/v1.0/publish/{{pubsub-name}}/{{topic}}` sostituire i parametri del modello e aggiungere il contenuto specificato nell'istruzione dei criteri.

Il criterio presuppone che il runtime di Dapr sia in esecuzione in un contenitore sidecar nello stesso pod del gateway. Il runtime di Dapr implementa la semantica di pubblicazione/sottoscrizione.

### <a name="policy-statement"></a>Istruzione del criterio

```xml
<publish-to-dapr pubsub-name="pubsub-name" topic=”topic-name” ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template=”Liquid” content-type="application/json">
    <!-- message content -->
</publish-to-dapr>
```

### <a name="examples"></a>Esempi

#### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato l'invio del corpo della richiesta corrente all' [argomento](https://github.com/dapr/docs/blob/master/reference/api/pubsub_api.md#url-parameters) "New" del [componente](https://github.com/dapr/docs/blob/master/reference/api/pubsub_api.md#url-parameters)pub/sub "Orders". La risposta ricevuta dal runtime Dapr viene archiviata nella voce "Dapr-Response" della raccolta Variables nell'oggetto [context](api-management-policy-expressions.md#ContextVariables) .

Se Dapr Runtime non è in grado di individuare l'argomento di destinazione, ad esempio e risponde con un errore, viene attivata la sezione "On-Error". La risposta ricevuta dal runtime Dapr viene restituita al chiamante Verbatim. In caso contrario, `200 OK` viene restituita la risposta predefinita.

La sezione "backend" è vuota e la richiesta non viene trasmessa al back-end.

```xml
<policies>
     <inbound>
        <base />
        <publish-to-dapr
           pubsub-name="orders"
               topic="new"
               response-variable-name="dapr-response">
            @(context.Request.Body.As<string>())
        </publish-to-dapr>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="pubsub-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>Elementi

| Elemento             | Descrizione  | Obbligatorio |
|---------------------|--------------|----------|
| da Publish a dapr     | Elemento radice | Sì      |

### <a name="attributes"></a>Attributes

| Attributo        | Descrizione                     | Obbligatorio | Valore predefinito |
|------------------|---------------------------------|----------|---------|
| PubSub-nome      | Nome del componente PubSub di destinazione. Esegue il mapping al parametro [pubsubname](https://github.com/dapr/docs/blob/master/reference/api/pubsub_api.md) in Dapr. Se non è presente, il valore dell'attributo dell' __argomento__ deve essere nel formato `pubsub-name/topic-name` .    | No       | nessuno    |
| argomento            | Il nome dell'argomento. Esegue il mapping al parametro dell' [argomento](https://github.com/dapr/docs/blob/master/reference/api/pubsub_api.md) in Dapr.               | Sì      | N/D     |
| ignore-error     | Se impostato su `true` indica ai criteri di non attivare la sezione ["On-Error"](api-management-error-handling-policies.md) dopo la ricezione di un errore dal runtime di Dapr | No | `false` |
| response-variable-name | Nome della voce di raccolta [variables](api-management-policy-expressions.md#ContextVariables) da usare per archiviare la risposta dal runtime di Dapr | No | nessuno |
| timeout | Tempo (in secondi) di attesa per la risposta del runtime di Dapr. Può variare da 1 a 240 secondi. | No | 5 |
| template | Motore di creazione del modello da utilizzare per trasformare il contenuto del messaggio. "Liquid" è l'unico valore supportato. | No | nessuno |
| content-type | Tipo di contenuto del messaggio. "application/json" è l'unico valore supportato. | No | nessuno |

### <a name="usage"></a>Usage

Questo criterio può essere usato nelle [sezioni](./api-management-howto-policies.md#sections) e negli [ambiti](./api-management-howto-policies.md#scopes) del criterio seguenti.

- **Sezioni del criterio:** inbound, outbound, on-error
- **Ambiti del criterio:** tutti gli ambiti

## <a name="trigger-output-binding"></a><a name="bind"></a> Attiva binding di output

Questo criterio indica al gateway di gestione API di attivare un' [associazione](https://github.com/dapr/docs/blob/master/concepts/bindings/README.md)Dapr in uscita. Il criterio esegue questa operazione effettuando una richiesta HTTP POST per `http://localhost:3500/v1.0/bindings/{{bind-name}}` sostituire il parametro del modello e aggiungere il contenuto specificato nell'istruzione dei criteri.

Il criterio presuppone che il runtime di Dapr sia in esecuzione in un contenitore sidecar nello stesso pod del gateway. Il runtime di Dapr è responsabile per richiamare la risorsa esterna rappresentata dall'associazione.

### <a name="policy-statement"></a>Istruzione del criterio

```xml
<invoke-dapr-binding name=”bind-name" operation="op-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template=”Liquid content-type="application/json">
    <metadata>
        <item key=”item-name”><!-- item-value --></item>
    </metadata>
    <data>
        <!-- message content -->
    </data>
</invoke-dapr-binding>
```

### <a name="examples"></a>Esempi

#### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrata l'attivazione di un'associazione in uscita denominata "External-Systems" con il nome dell'operazione "create", i metadati costituiti da due elementi chiave/valore denominati "Source" e "client-IP" e il corpo proveniente dalla richiesta originale. La risposta ricevuta dal runtime Dapr viene acquisita nella voce "bind-Response" della raccolta Variables nell'oggetto [context](api-management-policy-expressions.md#ContextVariables) .

Se il runtime Dapr non riesce per qualche motivo e risponde con un errore, viene attivata la sezione "On-Error" e la risposta ricevuta dal runtime Dapr viene restituita al chiamante Verbatim. In caso contrario, `200 OK` viene restituita la risposta predefinita.

La sezione "backend" è vuota e la richiesta non viene trasmessa al back-end.

```xml
<policies>
     <inbound>
        <base />
        <invoke-dapr-binding
                      name="external-system"
                      operation="create"
                      response-variable-name="bind-response">
            <metadata>
                <item key="source">api-management</item>
                <item key="client-ip">@( context.Request.IpAddress )</item>
            </metadata>
            <data>
                @( context.Request.Body.As<string>() )
            </data>
        </invoke-dapr-binding>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="bind-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>Elementi

| Elemento             | Descrizione  | Obbligatorio |
|---------------------|--------------|----------|
| Invoke-dapr-binding | Elemento radice | Sì      |
| metadata            | Associazione di metadati specifici sotto forma di coppie chiave/valore. Esegue il mapping alla proprietà [dei metadati](https://github.com/dapr/docs/blob/master/reference/api/bindings_api.md#invoking-output-bindings) in Dapr. | No |
| Data            | Contenuto del messaggio. Esegue il mapping alla proprietà [dei dati](https://github.com/dapr/docs/blob/master/reference/api/bindings_api.md#invoking-output-bindings) in Dapr. | No |


### <a name="attributes"></a>Attributes

| Attributo        | Descrizione                     | Obbligatorio | Valore predefinito |
|------------------|---------------------------------|----------|---------|
| name            | Nome dell'associazione di destinazione. Deve corrispondere al nome delle associazioni [definite](https://github.com/dapr/docs/blob/master/reference/api/bindings_api.md#bindings-structure) in Dapr.           | Sì      | N/D     |
| operation       | Nome dell'operazione di destinazione (specifica dell'associazione). Esegue il mapping alla proprietà [Operation](https://github.com/dapr/docs/blob/master/reference/api/bindings_api.md#invoking-output-bindings) in Dapr. | No | nessuno |
| ignore-error     | Se impostato su `true` indica ai criteri di non attivare la sezione ["On-Error"](api-management-error-handling-policies.md) dopo la ricezione di un errore dal runtime di Dapr | No | `false` |
| response-variable-name | Nome della voce di raccolta [variables](api-management-policy-expressions.md#ContextVariables) da usare per archiviare la risposta dal runtime di Dapr | No | nessuno |
| timeout | Tempo (in secondi) di attesa per la risposta del runtime di Dapr. Può variare da 1 a 240 secondi. | No | 5 |
| template | Motore di creazione del modello da utilizzare per trasformare il contenuto del messaggio. "Liquid" è l'unico valore supportato. | No | nessuno |
| content-type | Tipo di contenuto del messaggio. "application/json" è l'unico valore supportato. | No | nessuno |

### <a name="usage"></a>Usage

Questo criterio può essere usato nelle [sezioni](./api-management-howto-policies.md#sections) e negli [ambiti](./api-management-howto-policies.md#scopes) del criterio seguenti.

- **Sezioni del criterio:** inbound, outbound, on-error
- **Ambiti del criterio:** tutti gli ambiti
