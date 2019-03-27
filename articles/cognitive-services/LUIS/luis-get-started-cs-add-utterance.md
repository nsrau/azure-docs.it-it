---
title: Modificare l'app ed eseguirne il training, C#
titleSuffix: Language Understanding - Azure Cognitive Services
description: In questa guida introduttiva per C# si aggiungono espressioni di esempio in un'app Home Automation e si esegue il training dell'app.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 12/17/2018
ms.author: diberry
ms.openlocfilehash: dd60897d19ef4de7369b2b127c88e778363a387f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57852272"
---
# <a name="quickstart-change-model-using-c"></a>Guida introduttiva: Modificare il modello tramite C#

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* Versione più recente di [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/).
* Linguaggio di programmazione C# installato
* Pacchetti NuGet [JsonFormatterPlus](https://www.nuget.org/packages/JsonFormatterPlus) e [CommandLine](https://www.nuget.org/packages/CommandLineParser/)

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>File JSON delle espressioni di esempio

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Creare il codice della guida introduttiva 

In Visual Studio creare una nuova app **console Desktop classico di Windows** con .NET Framework. 

![Tipo di progetto di Visual Studio](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

### <a name="add-the-systemweb-dependency"></a>Aggiungere la dipendenza System.Web

Per il progetto di Visual Studio è necessario **System.Web**. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti** e scegliere **Aggiungi riferimento**.

![Aggiungere il riferimento System.web](./media/luis-quickstart-cs-add-utterance/system.web.png)

### <a name="add-other-dependencies"></a>Aggiungere altre dipendenze

Per il progetto di Visual Studio sono necessari i pacchetti **JsonFormatterPlus** e **CommandLineParser**. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti** e selezionare **Gestisci pacchetti NuGet**. Cercare ognuno delle due pacchetti e aggiungerlo. 

![Aggiungere dipendenze di terze parti](./media/luis-quickstart-cs-add-utterance/add-dependencies.png)


### <a name="write-the-c-code"></a>Scrivere il codice C#
Il file **Program.cs** deve essere:

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp3
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Aggiungere le dipendenze.

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=1-11 "Add the dependencies")]


Aggiungere gli ID e le stringhe di Language Understanding alla classe **Program**.

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=19-30&dedent=8 "Add the LUIS IDs and strings")]

Aggiungere la classe per gestire i parametri della riga di comando alla classe **Program**.

   [!code-csharp[Add class to manage command line parameters.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=32-46 "Add class to manage command-line parameters.")]

Aggiungere il metodo della richiesta GET alla classe **Program**.

   [!code-csharp[Add the GET request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=49-59 "Add the GET request.")]


Aggiungere il metodo della richiesta POST alla classe **Program**. 

   [!code-csharp[Add the POST request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=60-76 "Add the POST request.")]

Aggiungere espressioni di esempio del metodo del file alla classe **Program**.

   [!code-csharp[Add example utterances from file.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=77-86 "Add example utterances from file.")]

Dopo aver apportato le modifiche al modello, eseguire il training. Aggiungere il metodo alla classe **Program**.

   [!code-csharp[After the changes are applied to the model, train the model.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=87-96 "After the changes are applied to the model, train the model.")]

È possibile che il training non venga eseguito subito. Controllare lo stato per verificare il training sia stato completato. Aggiungere il metodo alla classe **Program**.

   [!code-csharp[Training may not complete immediately, check status to verify training is complete.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=97-103 "Training may not complete immediately, check status to verify training is complete.")]

Per gestire gli argomenti della riga di comando, aggiungere il codice main. Aggiungere il metodo alla classe **Program**.

   [!code-csharp[To manage command line arguments, add the main code.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=104-137 "To manage command-line arguments, add the main code.")]

### <a name="copy-utterancesjson-to-output-directory"></a>Copiare utterances.json nella directory di output

In Esplora soluzioni fare clic con il pulsante destro del mouse su `utterances.json`, quindi scegliere **Proprietà**. Nelle finestre delle proprietà contrassegnare **Azione di compilazione** di `Content` e **Copia nella directory di output** di `Copy Always`.  

![Contrassegnare il file JSON come contenuto](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="build-code"></a>Compilare il codice

Compilare il codice in Visual Studio. 

## <a name="run-code"></a>Eseguire il codice

Nella directory /bin/Debug del progetto eseguire l'applicazione da una riga di comando. 

```console
ConsoleApp\bin\Debug> ConsoleApp1.exe --add utterances.json --train --status
```

Questa riga di comando visualizza i risultati della chiamata all'API di aggiunta delle espressioni. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>Pulire le risorse
Dopo aver completato la procedura illustrata, rimuovere tutti i file creati in questa guida introduttiva. 

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Compilare un'app di Language Understanding Intelligent Service (LUIS) a livello di codice](luis-tutorial-node-import-utterances-csv.md) 
