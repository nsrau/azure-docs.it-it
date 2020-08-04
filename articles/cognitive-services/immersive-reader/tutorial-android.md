---
title: 'Esercitazione: Avviare lo strumento di lettura immersiva con gli esempi di codice Android'
titleSuffix: Azure Cognitive Services
description: In questa esercitazione si configurerà e si eseguirà un'applicazione Android di esempio che avvia lo strumento di lettura immersiva.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.custom: devx-track-java
ms.openlocfilehash: f8420a42b87481b5e51b383ace1ba8a510d2a6a7
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87366739"
---
# <a name="tutorial-start-the-immersive-reader-using-the-android-java-code-sample"></a>Esercitazione: Avviare lo strumento di lettura immersiva con l'esempio di codice Android Java

Nella [panoramica](./overview.md) si è appreso che cos'è lo strumento di lettura immersiva e come questo strumento implementa tecniche comprovate per migliorare la comprensione della lettura per studenti di lingue, lettori emergenti e studenti con differenze nell'apprendimento. Questa esercitazione illustra come creare un'applicazione Android che avvia lo strumento di lettura immersiva. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare ed eseguire un'app per Android con un progetto di esempio.
> * Acquisire un token di accesso.
> * Avviare lo strumento di lettura immersiva con contenuto di esempio.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Una risorsa dello strumento di lettura immersiva configurata per l'autenticazione Azure Active Directory. Seguire [queste istruzioni](./how-to-create-immersive-reader.md) per configurare l'ambiente. Quando si configurano le proprietà dell'ambiente, sarà necessario usare alcuni valori creati qui. Salvare l'output della sessione in un file di testo per riferimento futuro.
* [Git](https://git-scm.com/).
* [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk).
* [Android Studio](https://developer.android.com/studio).

## <a name="configure-authentication-credentials"></a>Configurare le credenziali di autenticazione

1. Avviare Android Studio e aprire il progetto dalla directory **immersive-reader-sdk/js/samples/quickstart-java-android** (Java) o dalla directory **immersive-reader-sdk/js/samples/quickstart-kotlin** (Kotlin).

1. Creare un file denominato **env** all'interno della cartella **/assets**. Aggiungere i nomi e i valori seguenti e specificare i valori appropriati. Non eseguire il commit di questo file nel controllo del codice sorgente, perché contiene segreti che non devono essere resi pubblici.
    
    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET=<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

## <a name="start-the-immersive-reader-with-sample-content"></a>Avviare lo strumento di lettura immersiva con contenuto di esempio

Scegliere un emulatore di dispositivo da AVD Manager ed eseguire il progetto.

## <a name="next-steps"></a>Passaggi successivi

* Esplorare [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) e le [informazioni di riferimento su Immersive Reader SDK](./reference.md).
* Visualizzare gli esempi di codice in [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).