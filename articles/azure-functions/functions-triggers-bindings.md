---
title: Trigger e associazioni in Funzioni di Azure
description: Informazioni su come usare trigger e associazioni per connettere la funzione di Azure agli eventi online e ai servizi basati sul cloud.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 914158ba7cfcc7530120d427c62e69036b3bb156
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085081"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Concetti di Trigger e associazioni di Funzioni di Azure

In questo articolo vengono illustrati i concetti di alto livello che circondano i trigger e le associazioni di funzioni.

I trigger sono la cosa che determina l'esecuzione di una funzione. Un trigger definisce il modo in cui viene richiamata una funzione e una funzione deve avere esattamente un trigger. Ai trigger sono associati dati, che vengono spesso forniti come payload della funzione. 

Il binding a una funzione è un modo per connettere in modo dichiarativo un'altra risorsa alla funzione; i binding possono essere connessi come *binding di input*, *associazioni di output*o entrambi. I dati dei binding vengono forniti alla funzione come parametri.

È possibile combinare e associare associazioni diverse in base alle esigenze. Le associazioni sono facoltative e una funzione può avere una o più associazioni di input e/o output.

Trigger e associazioni consentono di evitare l'accesso hardcoded ad altri servizi. La funzione riceve i dati, ad esempio il contenuto di un messaggio della coda, nei parametri della funzione. I dati vengono inviati, ad esempio per creare un messaggio della coda, usando il valore restituito della funzione. 

Si considerino gli esempi seguenti di come implementare funzioni diverse.

| Scenario di esempio | Trigger | Binding di input | Binding di output |
|-------------|---------|---------------|----------------|
| Arriva un nuovo messaggio di coda che esegue una funzione per scrivere in un'altra coda. | Queue<sup>*</sup> | *None* | Queue<sup>*</sup> |
|Un processo pianificato legge il contenuto dell'archiviazione BLOB e crea un nuovo documento Cosmos DB. | Timer | Archiviazione BLOB | Cosmos DB |
|La griglia di eventi viene usata per leggere un'immagine dall'archiviazione BLOB e un documento da Cosmos DB per inviare un messaggio di posta elettronica. | Griglia eventi | Archiviazione BLOB e Cosmos DB | SendGrid |
| Un webhook che usa Microsoft Graph per aggiornare un foglio di Excel. | HTTP | *None* | Microsoft Graph |

<sup>\*</sup>Rappresenta le code diverse

Questi esempi non sono destinati a essere esaustivi, ma vengono forniti per illustrare come è possibile usare i trigger e le associazioni.

###  <a name="trigger-and-binding-definitions"></a>Trigger e definizioni di binding

I trigger e le associazioni sono definiti in modo diverso a seconda dell'approccio di sviluppo.

| Piattaforma | Trigger e associazioni sono configurati da... |
|-------------|--------------------------------------------|
| C#libreria di classi | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;decorazione di metodi e parametri C# con attributi |
| Tutti gli altri (inclusi portale di Azure) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;aggiornamento di [Function. JSON](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

Il portale fornisce un'interfaccia utente per questa configurazione, ma è possibile modificare il file direttamente aprendo l' **Editor avanzato** disponibile tramite la scheda **integra** della funzione.

In .NET, il tipo di parametro definisce il tipo di dati per i dati di input. Ad esempio, usare `string` per eseguire l'associazione al testo di un trigger della coda, una matrice di byte da leggere come binary e un tipo personalizzato da deserializzare in un oggetto.

Per i linguaggi tipizzati in modo dinamico, ad esempio JavaScript, usare la proprietà `dataType` nel file *function.json*. Ad esempio, per eseguire la lettura del contenuto di una richiesta HTTP in formato binario, impostare `dataType` su `binary`:

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Altre opzioni per `dataType` sono `stream` e `string`.

## <a name="binding-direction"></a>Direzione dell'associazione

Tutti i trigger e le associazioni hanno una proprietà `direction` nel file [function.json](./functions-reference.md):

- Per i trigger, la direzione è sempre `in`
- Le associazioni di input e di output usano `in` e `out`
- Alcune associazioni supportano una direzione speciale `inout`. Se si usa `inout`, solo l' **Editor avanzato** è disponibile tramite la scheda **integrazione** nel portale.

Quando si usano gli [attributi in una libreria di classi](functions-dotnet-class-library.md) per configurare i trigger e le associazioni, la direzione viene specificata in un costruttore di attributo o dedotta dal tipo di parametro.

## <a name="supported-bindings"></a>Binding supportati

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Per informazioni sulle associazioni in anteprima o approvate per l'uso in ambiente di produzione, vedere [Linguaggi supportati ](supported-languages.md).

## <a name="resources"></a>Risorse
- [Espressioni e modelli di associazione](./functions-bindings-expressions-patterns.md)
- [Uso del valore restituito della funzione di Azure](./functions-bindings-return-value.md)
- [Come registrare un'espressione di associazione](./functions-bindings-register.md)
- Testing:
  - [Strategie per il test del codice in Funzioni di Azure](functions-test-a-function.md)
  - [Eseguire manualmente una funzione non attivata da HTTP](functions-manually-run-non-http.md)
- [Gestione degli errori di associazione](./functions-bindings-errors.md)

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Registrare le estensioni di binding di funzioni di Azure](./functions-bindings-register.md)
