---
title: "Guida introduttiva: Rilevare i visi in un'immagine usando .NET SDK Viso di Azure"
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si userà l'SDK Viso di Azure per rilevare i visi in un'immagine.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: pafarley
ms.openlocfilehash: 57605f9bd1a39435e27a2f2c56c06cf3bfb38605
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630711"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-net-sdk"></a>Guida introduttiva: Rilevare i visi in un'immagine usando .NET SDK Viso

In questa guida introduttiva si userà l'SDK del servizio Viso per rilevare i visi umani in un'immagine. Per un esempio pratico del codice in questa guida introduttiva, vedere il progetto Viso nell'archivio di [soluzioni di avvio rapido csharp Vision di Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face) in GitHub.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="prerequisites"></a>Prerequisiti

- Una chiave di sottoscrizione API Viso. È possibile ottenere una chiave di sottoscrizione della versione di valutazione gratuita da [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=face-api). In alternativa, seguire le istruzioni in [Creare un account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per effettuare la sottoscrizione al servizio API Viso e ottenere la chiave.
- Qualsiasi edizione di [Visual Studio 2015 o 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Creare il progetto di Visual Studio

1. In Visual Studio creare un nuovo progetto **App console (.NET Framework)** e assegnargli il nome **FaceDetection**. 
1. Se la soluzione contiene anche altri progetti, selezionare questo come progetto di avvio singolo.
1. Ottenere i pacchetti NuGet necessari. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**. Fare clic sulla scheda **Sfoglia** e selezionare **Includi versione preliminare**, quindi cercare e installare il pacchetto seguente:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)
1. Verificare di aver installato le versioni più recenti di tutti i pacchetti NuGet del progetto. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**. Scegliere la scheda **Aggiornamenti** e installare le versioni più recenti di qualsiasi pacchetto visualizzato.

## <a name="add-face-detection-code"></a>Aggiungere codice di rilevamento volto

Aprire il nuovo file *Program.cs* del progetto. In questo caso, si aggiungerà il codice necessario per caricare immagini e rilevare i visi.

### <a name="include-namespaces"></a>Includere gli spazi dei nomi

Aggiungere le istruzioni `using` seguenti all'inizio del file *Program.cs*.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=1-7)]

### <a name="add-essential-fields"></a>Aggiungere campi fondamentali

Aggiungere la classe **Program** con i campi seguenti. Questi dati consentono di specificare come connettersi al servizio Viso e dove ottenere i dati di input. Sarà necessario aggiornare il campo `subscriptionKey` con il valore della chiave di sottoscrizione e potrebbe essere necessario modificare la stringa `faceEndpoint` in modo che contenga l'identificatore di area corretta. Sarà necessario impostare anche i valori `localImagePath` e/o`remoteImageUrl` sui percorsi che puntano ai file immagine effettivi.

Il campo `faceAttributes` è semplicemente una matrice di alcuni tipi di attributi, che specifica quali informazioni recuperare sui visi rilevati.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=9-34)]

### <a name="create-and-use-the-face-client"></a>Creare e usare il client Viso

Quindi, aggiungere il metodo **Main** della classe **Program** con il codice seguente. Viene impostato un client API Viso.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=36-41)]

In più, nel metodo **Main**, aggiungere il codice seguente per usare il client Viso appena creato allo scopo di rilevare i visi in un'immagine remota e locale. I metodi di rilevamento verranno definiti successivamente. 

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=43-50)]

### <a name="detect-faces"></a>Rilevare visi

Aggiungere il metodo seguente alla classe **Program**. Usa il client del servizio Viso per rilevare visi in un'immagine remota, a cui fa riferimento un URL. Si noti che usa il campo `faceAttributes`: gli oggetti **DetectedFace** aggiunti a `faceList` avranno gli attributi specificati (in questo caso, età e sesso).

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=52-74)]

Analogamente, aggiungere il metodo **DetectLocalAsync**. Usa il client del servizio Viso per rilevare visi in un'immagine locale, a cui fa riferimento un percorso file.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=76-101)]

### <a name="retrieve-and-display-face-attributes"></a>Recuperare e visualizzare attributi del viso

Successivamente, definire il metodo **GetFaceAttributes**. Restituisce una stringa con le informazioni pertinenti degli attributi.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=103-116)]

Infine, definire il metodo **DisplayAttributes** per scrivere i dati dell'attributo viso nell'output della console. È quindi possibile chiudere la classe e lo spazio dei nomi.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=118-125)]

## <a name="run-the-app"></a>Esecuzione dell'app

In caso di risposta positiva, verranno visualizzati il sesso e l'età di ogni viso nell'immagine. Ad esempio: 

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata creata una semplice applicazione console .NET che usa il servizio API Viso per rilevare i visi in immagini locali e remote. Successivamente, si eseguirà un'esercitazione più dettagliata per vedere come è possibile presentare informazioni sul viso all'utente in modo intuitivo.

> [!div class="nextstepaction"]
> [Esercitazione: Creare un'app WPF per rilevare e analizzare i visi in un'immagine](../Tutorials/FaceAPIinCSharpTutorial.md)
