---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 69dc0e1c14bc88cdbf0aa48700f95058ba759cc0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180653"
---
1. Nella visualizzazione della soluzione (o **Esplora soluzioni** in Visual Studio) fare clic con il pulsante destro del mouse sulla cartella **Componenti**, scegliere **Ottieni altri componenti**, cercare il componente **Google Cloud Messaging Client** e aggiungerlo al progetto.
2. Aprire il nuovo file di progetto ToDoActivity.cs e aggiungere l'istruzione using seguente alla classe:

    ```csharp
    using Gcm.Client;
    ```

3. Nella classe **ToDoActivity** aggiungere il nuovo codice seguente: 

    ```csharp
    // Create a new instance field for this activity.
    static ToDoActivity instance = new ToDoActivity();

    // Return the current activity instance.
    public static ToDoActivity CurrentActivity
    {
        get
        {
            return instance;
        }
    }
    // Return the Mobile Services client.
    public MobileServiceClient CurrentClient
    {
        get
        {
            return client;
        }
    }
    ```

    Questa operazione consente di accedere all'istanza del client mobile dal processo del servizio del gestore push.
4. Aggiungere il codice seguente al metodo **OnCreate**, dopo la creazione di **MobileServiceClient**:

    ```csharp
    // Set the current instance of TodoActivity.
    instance = this;

    // Make sure the GCM client is set up correctly.
    GcmClient.CheckDevice(this);
    GcmClient.CheckManifest(this);

    // Register the app for push notifications.
    GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);
    ```

La classe **ToDoActivity** è ora pronta per l'aggiunta di notifiche push.
