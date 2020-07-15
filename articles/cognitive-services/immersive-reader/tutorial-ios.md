---
title: "Esercitazione: Avviare lo strumento di lettura immersiva con l'esempio di codice Swift iOS"
titleSuffix: Azure Cognitive Services
description: In questa esercitazione si configurerà e si eseguirà un'applicazione Swift di esempio che avvia lo strumento di lettura immersiva.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: a9259026747102dd65be3bb8da853735098667db
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049319"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-swift-ios-code-sample"></a>Esercitazione: Avviare lo strumento di lettura immersiva con l'esempio di codice Swift iOS

Nella [panoramica](./overview.md) si è appreso che cos'è lo strumento di lettura immersiva e come questo strumento implementa tecniche comprovate per migliorare la comprensione della lettura per studenti di lingue, lettori emergenti e studenti con differenze nell'apprendimento. Questa esercitazione illustra la creazione di un'applicazione iOS che avvia lo strumento di lettura immersiva. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare ed eseguire un'app Swift per iOS con un progetto di esempio
> * Acquisire un token di accesso
> * Avviare lo strumento di lettura immersiva con contenuto di esempio

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Una risorsa dello strumento di lettura immersiva configurata per l'autenticazione Azure Active Directory. Seguire [queste istruzioni](./how-to-create-immersive-reader.md) per configurare l'ambiente. Per la configurazione delle proprietà dell'ambiente, saranno necessari alcuni dei valori creati qui. Salvare l'output della sessione in un file di testo per riferimento futuro.
* [macOS](https://www.apple.com/macos)
* [Git](https://git-scm.com/)
* [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk)
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)

## <a name="configure-authentication-credentials"></a>Configurare le credenziali di autenticazione

1. In Xcode aprire il progetto **immersive-reader-sdk/js/samples/ios/quickstart-swift/quickstart-swift.xcodeproj**.
2. Nel menu in alto fare clic su **Prodotto > Schema > Modifica schema**
3. Nella visualizzazione **Esegui** fare clic sulla scheda **Argomenti**.
4. Nella sezione **Variabili di ambiente** aggiungere i nomi e i valori seguenti, specificando i valori forniti durante la creazione della risorsa dello strumento di lettura immersiva.

```text
TENANT_ID=<YOUR_TENANT_ID>
CLIENT_ID=<YOUR_CLIENT_ID>
CLIENT_SECRET<YOUR_CLIENT_SECRET>
SUBDOMAIN=<YOUR_SUBDOMAIN>
```

Fare attenzione a non eseguire il commit della modifica precedente nel controllo del codice sorgente perché contiene segreti che non devono essere resi pubblici.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Avviare lo strumento di lettura immersiva con contenuto di esempio

1. In Xcode premere **CTRL+R** per eseguire il progetto.

## <a name="next-steps"></a>Passaggi successivi

* Esplorare [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) e le [informazioni di riferimento su Immersive Reader SDK](./reference.md)
* Visualizzare gli esempi di codice su [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
