---
title: Trigger e associazioni in Funzioni di Azure
description: Informazioni su come usare trigger e associazioni per connettere la funzione di Azure agli eventi online e ai servizi basati su cloud.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276504"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Concetti di Trigger e associazioni di Funzioni di Azure

In questo articolo vengono illustrati i concetti di alto livello relativi alle funzioni trigger e binding.

I trigger sono ciò che causa l'esecuzione di una funzione. Un trigger definisce come viene richiamata una funzione e una funzione deve avere esattamente un trigger. Ai trigger sono associati dati, che spesso vengono forniti come payload della funzione. 

L'associazione a una funzione è un modo per connettere in modo dichiarativo un'altra risorsa alla funzione; le associazioni possono essere connesse come associazioni di *input*, associazioni di *output*o entrambe. I dati dei binding vengono forniti alla funzione come parametri.

È possibile combinare binding diversi in base alle esigenze. I binding sono facoltativi e una funzione potrebbe avere uno o più binding di input e/o di output.

I trigger e le associazioni consentono di evitare l'accesso hardcoded ad altri servizi. La funzione riceve i dati, ad esempio il contenuto di un messaggio della coda, nei parametri della funzione. I dati vengono inviati, ad esempio per creare un messaggio della coda, usando il valore restituito della funzione. 

Si considerino i seguenti esempi di come è possibile implementare diverse funzioni.

| Scenario di esempio | Trigger | Associazione di input | Associazione di output |
|-------------|---------|---------------|----------------|
| Arriva un nuovo messaggio della coda che esegue una funzione per scrivere in un'altra coda. | Coda<sup>*</sup> | *Nessuno* | Coda<sup>*</sup> |
|Un processo pianificato legge il contenuto dell'archiviazione BLOB e crea un nuovo documento Cosmos DB. | Timer | Archiviazione BLOB | Cosmos DB |
|La griglia di eventi viene usata per leggere un'immagine dall'archivio BLOB e un documento da Cosmos DB per inviare un messaggio di posta elettronica. | Griglia di eventi | Blob Storage and Cosmos DB | SendGrid |
| Webhook che utilizza Microsoft Graph per aggiornare un foglio di Excel. | HTTP | *Nessuno* | Microsoft Graph |

<sup>\*</sup>Rappresenta code diverse

Questi esempi non devono essere esaustivi, ma vengono forniti per illustrare come è possibile usare trigger e associazioni insieme.

###  <a name="trigger-and-binding-definitions"></a>Definizioni di trigger e associazioneTrigger and binding definitions

I trigger e le associazioni vengono definiti in modo diverso a seconda dell'approccio di sviluppo.

| Piattaforma | I trigger e i binding sono configurati da... |
|-------------|--------------------------------------------|
| Libreria di classi C | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;metodi di decorazione e parametri con gli attributi di C |
| Tutti gli altri (incluso il portale di Azure)All others (including Azure portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;aggiornamento [di function.json](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

Il portale fornisce un'interfaccia utente per questa configurazione, ma è possibile modificare il file direttamente aprendo **l'editor avanzato** disponibile tramite la scheda **Integra** della funzione.

In .NET il tipo di parametro definisce il tipo di dati per i dati di input. Ad esempio, `string` utilizzare per eseguire l'associazione al testo di un trigger di coda, una matrice di byte da leggere come binario e un tipo personalizzato per deserializzare un oggetto.

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
- Alcune associazioni supportano una direzione speciale `inout`. Se si `inout`utilizza , solo l'editor **avanzato** è disponibile tramite la scheda **Integra** nel portale.

Quando si usano gli [attributi in una libreria di classi](functions-dotnet-class-library.md) per configurare i trigger e le associazioni, la direzione viene specificata in un costruttore di attributo o dedotta dal tipo di parametro.

## <a name="supported-bindings"></a>Binding supportati

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Per informazioni sulle associazioni in anteprima o approvate per l'uso in ambiente di produzione, vedere [Linguaggi supportati ](supported-languages.md).

## <a name="resources"></a>Risorse
- [Modelli ed espressioni di associazione](./functions-bindings-expressions-patterns.md)
- [Uso del valore restituito della funzione di AzureUsing the Azure Function return value](./functions-bindings-return-value.md)
- [Come registrare un'espressione di associazioneHow to register a binding expression](./functions-bindings-register.md)
- Testing:
  - [Strategie per il test del codice in Funzioni di Azure](functions-test-a-function.md)
  - [Eseguire manualmente una funzione non attivata da HTTP](functions-manually-run-non-http.md)
- [Gestione degli errori di associazione](./functions-bindings-errors.md)

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Registrare le estensioni di associazione di Funzioni di AzureRegister Azure Functions binding extensions](./functions-bindings-register.md)
