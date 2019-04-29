---
title: Trigger e associazioni in Funzioni di Azure
description: Informazioni su come usare trigger e associazioni per connettere la funzione di Azure a eventi online e servizi basati sul cloud.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
origin.date: 02/18/2019
ms.date: 03/04/2019
ms.author: v-junlch
ms.openlocfilehash: 3865f748a9ca2fe09660d6454542d64f73a8e3c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61020963"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Concetti di Trigger e associazioni di Funzioni di Azure

In questo articolo descrive i concetti generali che circondano funzioni trigger e associazioni.

I trigger sono le cause di una funzione da eseguire. Un trigger definisce come viene richiamata una funzione e una funzione deve avere esattamente un trigger. I trigger sono associati dati, che viene spesso forniti come il payload della funzione. 

Associazione a una funzione è una modalità di connessione in modo dichiarativo un'altra risorsa rispetto alla funzione. le associazioni possono essere connessi come *associazioni di input*, *le associazioni di output*, o entrambi. I dati di binding viene forniti alla funzione come parametri.

È possibile combinare e associare le associazioni diverse in base alle esigenze. Le associazioni sono facoltative e una funzione può avere uno o più input e/o le associazioni di output.

Trigger e associazioni consentono di evitare l'accesso a livello di codice ad altri servizi. La funzione riceve i dati, ad esempio il contenuto di un messaggio della coda, nei parametri della funzione. I dati vengono inviati, ad esempio per creare un messaggio della coda, usando il valore restituito della funzione. 

Considerare i seguenti esempi di come si può implementare diverse funzioni.

| Scenario di esempio | Trigger | Associazione di input | Associazione di output |
|-------------|---------|---------------|----------------|
| Un nuovo messaggio di arrivo che esegue una funzione per scrivere in un'altra coda. | Queue<sup>*</sup> | *Nessuno* | Queue<sup>*</sup> |
|Un processo pianificato legge il contenuto di archiviazione Blob e crea un nuovo documento di Cosmos DB. | Timer | Archiviazione BLOB | Cosmos DB |
|Griglia di eventi consente di leggere un'immagine dall'archiviazione Blob e un documento da Cosmos DB per inviare un messaggio di posta elettronica. | Griglia di eventi | Archiviazione BLOB e Cosmos DB | SendGrid |
| Un webhook che usa Microsoft Graph per aggiornare un foglio di Excel. | HTTP | *Nessuno* | Microsoft Graph |

<sup>\*</sup> Rappresenta diverse code

Questi esempi non sono da considerarsi esaustive, ma vengono forniti per illustrare come usare trigger e associazioni insieme.

###  <a name="trigger-and-binding-definitions"></a>Definizioni di trigger e binding

Trigger e associazioni sono definite in modo diverso a seconda dell'approccio di sviluppo.

| Piattaforma | Trigger e associazioni sono configurate per... |
|-------------|--------------------------------------------|
| C#libreria di classi | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;decorazione di metodi e parametri con C# attributi |
| Tutti gli altri utenti (inclusi il portale di Azure) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;updating [function.json](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

Il portale offre un'interfaccia utente per questa configurazione, ma è possibile modificare il file aprendo direttamente la **editor avanzato** disponibili tramite il **integra** scheda della funzione.

In .NET, il tipo di parametro definisce il tipo di dati per i dati di input. Ad esempio, usare `string` da associare al testo di un trigger della coda, una matrice di byte da leggere come file binario e un tipo personalizzato per deserializzare un oggetto.

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
- Alcune associazioni supportano una direzione speciale `inout`. Se si usa `inout`, solo le **editor avanzato** è disponibile tramite il **integra** scheda nel portale.

Quando si usano gli [attributi in una libreria di classi](functions-dotnet-class-library.md) per configurare i trigger e le associazioni, la direzione viene specificata in un costruttore di attributo o dedotta dal tipo di parametro.

## <a name="supported-bindings"></a>Binding supportati

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Per informazioni sulle associazioni in anteprima o approvate per l'uso in ambiente di produzione, vedere [Linguaggi supportati ](supported-languages.md).

## <a name="resources"></a>Risorse
- [Modelli ed espressioni di associazione](./functions-bindings-expressions-patterns.md)
- [Usando il valore restituito di funzione di Azure](./functions-bindings-return-value.md)
- [Come registrare un'espressione di associazione](./functions-bindings-register.md)
- Testing:
  - [Strategie per il test del codice in Funzioni di Azure](functions-test-a-function.md)
  - [Eseguire manualmente una funzione non attivata da HTTP](functions-manually-run-non-http.md)
- [Gestione degli errori di associazione](./functions-bindings-errors.md)

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Registrare le estensioni delle associazioni di funzioni di Azure](./functions-bindings-register.md)

<!-- Update_Description: wording update -->
