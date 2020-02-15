---
title: Associazioni di output HTTP di funzioni di Azure
description: Informazioni su come restituire risposte HTTP in funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: a25658677e436edf4d001599bb4981f527016596
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210905"
---
# <a name="azure-functions-http-output-bindings"></a>Associazioni di output HTTP di funzioni di Azure

Usare l'associazione di output HTTP per rispondere al mittente della richiesta HTTP. Questa associazione richiede un trigger HTTP e consente di personalizzare la risposta associata alla richiesta del trigger.

Il valore restituito predefinito per una funzione attivata tramite HTTP è:

- `HTTP 204 No Content` con un corpo vuoto in functions 2. x e versioni successive
- `HTTP 200 OK` con un corpo vuoto nelle funzioni 1. x

## <a name="configuration"></a>Configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json*. Per le librerie di classe C# non vi sono proprietà di attributo che corrispondano a queste proprietà *function.json*.

|Proprietà  |Descrizione  |
|---------|---------|
| **type** |Il valore deve essere impostato su `http`. |
| **direction** | Il valore deve essere impostato su `out`. |
| **nome** | Nome della variabile usato nel codice della funzione per la risposta, o `$return`per usare il valore restituito. |

## <a name="usage"></a>Utilizzo

Per inviare una risposta HTTP, usare modelli di risposta standard del linguaggio. In C# o nello script C#, eseguire il tipo restituito della funzione `IActionResult` o `Task<IActionResult>`. In C#, non è necessario un attributo del valore restituito.

Per le risposte di esempio, vedere l'[esempio di trigger](./functions-bindings-http-webhook-trigger.md#example).

## <a name="hostjson-settings"></a>impostazioni host.json

Questa sezione descrive le impostazioni di configurazione globali disponibili per questa associazione nelle versioni 2. x e successive. Il file host. JSON di esempio seguente contiene solo le impostazioni della versione 2. x + per questa associazione. Per ulteriori informazioni sulle impostazioni di configurazione globali nelle versioni 2. x e successive, vedere informazioni di [riferimento su host. JSON per funzioni di Azure](functions-host-json.md).

> [!NOTE]
> Per informazioni di riferimento su host.json in Funzioni 1.x, vedere [Informazioni di riferimento su host.json per Funzioni di Azure 1.x](functions-host-json-v1.md#http).

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|Proprietà  |Default | Descrizione |
|---------|---------|---------| 
| customHeaders|none|Consente di impostare intestazioni personalizzate nella risposta HTTP. Nell'esempio precedente l'intestazione `X-Content-Type-Options` viene aggiunta alla risposta per evitare l'analisi dei tipi di contenuto. |
|dynamicThrottlesEnabled|<sup>\*</sup> true|Se questa impostazione è abilitata, la pipeline di elaborazione della richiesta controlla periodicamente i contatori delle prestazioni del sistema, ad esempio `connections/threads/processes/memory/cpu/etc` e se uno di questi contatori supera una soglia elevata predefinita (80%), le richieste verranno rifiutate con una risposta `429 "Too Busy"` fino a quando i contatori non tornano ai livelli normali.<br/><sup>\*</sup> Il valore predefinito in un piano a consumo è `true`. Il valore predefinito in un piano dedicato è `false`.|
|HSTS|non abilitato|Quando `isEnabled` è impostato su `true`, viene applicato il [comportamento di sicurezza del trasporto http Strict (HSTS) di .NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) , come definito nella [classe`HstsOptions`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0). Nell'esempio precedente viene inoltre impostata la proprietà [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) su 10 giorni. Le proprietà supportate di `hsts` sono: <table><tr><th>Proprietà</th><th>Descrizione</th></tr><tr><td>excludedHosts</td><td>Matrice di stringhe di nomi host per cui non viene aggiunta l'intestazione HSTS.</td></tr><tr><td>includeSubDomains</td><td>Valore booleano che indica se il parametro includeSubDomain dell'intestazione Strict-Transport-Security è abilitato.</td></tr><tr><td>maxAge</td><td>Stringa che definisce il parametro max-age dell'intestazione Strict-Transport-Security.</td></tr><tr><td>precaricare</td><td>Valore booleano che indica se il parametro preload dell'intestazione Strict-Transport-Security è abilitato.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|Numero massimo di funzioni HTTP eseguite in parallelo. Questo valore consente di controllare la concorrenza, che consente di gestire l'utilizzo delle risorse. Ad esempio, si potrebbe avere una funzione HTTP che usa un numero elevato di risorse di sistema (memoria/CPU/socket) in modo che causi problemi quando la concorrenza è troppo elevata. Oppure è possibile che si disponga di una funzione che esegue richieste in uscita per un servizio di terze parti e tali chiamate devono essere limitate. In questi casi potrebbe risultare utile l'applicazione di una limitazione. <br/><sup>*</sup> Il valore predefinito per un piano a consumo è 100. Il valore predefinito per un piano dedicato è unbounded (`-1`).|
|maxOutstandingRequests|200<sup>\*</sup>|Il numero massimo di richieste in sospeso che verrà mantenuto in un determinato intervallo. Questo limite include le richieste che vengono messe in coda ma non hanno avviato l'esecuzione, nonché le esecuzioni in corso. Le richieste in arrivo che superano questo limite vengono rifiutate con la risposta 429 "Occupato". Ciò consente ai chiamanti di usare strategie di ripetizione dei tentativi basate sul tempo e di controllare la latenza massima delle richieste. Questa impostazione controlla solo l'accodamento che si verifica all'interno del percorso di esecuzione dell'host dello script. Altre code, ad esempio la coda di richieste ASP.NET, saranno valide e non interessate da questa impostazione. <br/><sup>\*</sup> Il valore predefinito per un piano a consumo è 200. Il valore predefinito per un piano dedicato è unbounded (`-1`).|
|routePrefix|api|Il prefisso della route che si applica a tutte le route. Utilizzare una stringa vuota per rimuovere il prefisso predefinito. |

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una funzione da una richiesta HTTP](./functions-bindings-http-webhook-trigger.md)