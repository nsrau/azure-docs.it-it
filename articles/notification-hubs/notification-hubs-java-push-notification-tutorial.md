---
title: Come usare Hub di notifica con Java
description: Informazioni su come usare Hub di notifica di Azure da un back-end Java.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 993eafd5a0b74be706d13fe8e06483c223f81eb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61461205"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Come usare Hub di notifica da Java

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Questo argomento descrive le funzionalità principali del nuovo ufficiale SDK per Java di Hub di notifica di Azure, completamente supportato.
Questo è un progetto open source ed è possibile visualizzare il codice SDK completo nell'articolo relativo all'[SDK per Java].

In generale, è possibile accedere a tutte le funzionalità di Hub di notifica da un back-end Java/PHP/Python/Ruby tramite l'interfaccia REST di Hub di notifica, come descritto nell'argomento [API REST degli hub di notifica](https://msdn.microsoft.com/library/dn223264.aspx)di MSDN. L'SDK per Java fornisce un semplice wrapper per le interfacce REST in Java.

L'SDK supporta attualmente:

* CRUD in Hub di notifica
* CRUD in Registrazioni
* Gestione dell'installazione
* Importazione/esportazione di registrazioni
* Invii regolari
* Invii pianificati
* Operazioni asincrone tramite Java NIO
* Piattaforme supportate: APNS (iOS), FCM (Android), WNS (app di Windows Store), MPNS (Windows Phone), ADM (Amazon Kindle Fire) e Baidu (Android senza servizi Google)

## <a name="sdk-usage"></a>Uso dell'SDK

### <a name="compile-and-build"></a>Compilazione e creazione

Usare [Maven]

Per creare:

    mvn package

## <a name="code"></a>Codice

### <a name="notification-hub-cruds"></a>CRUD di Hub di notifica

**Creare un oggetto NamespaceManager:**

    ```java
    NamespaceManager namespaceManager = new NamespaceManager("connection string")
    ```

**Creare un hub di notifica:**

    ```java
    NotificationHubDescription hub = new NotificationHubDescription("hubname");
    hub.setWindowsCredential(new WindowsCredential("sid","key"));
    hub = namespaceManager.createNotificationHub(hub);
    ```

 Oppure

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Ottenere un hub di notifica:**

    ```java
    hub = namespaceManager.getNotificationHub("hubname");
    ```

**Aggiornare un hub di notifica:**

    ```java
    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);
    ```

**Eliminare un hub di notifica:**

    ```java
    namespaceManager.deleteNotificationHub("hubname");
    ```

### <a name="registration-cruds"></a>CRUD di registrazione

**Creare un client di Hub di notifica:**

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Creare una registrazione per Windows:**

    ```java
    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

**Creare una registrazione per iOS:**

    ```java
    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

Analogamente è possibile creare registrazioni per Android (FCM), Windows Phone (MPNS) e Kindle Fire (ADM).

**Creare registrazioni modello:**

    ```java
    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);
    ```

**Creare registrazioni usando il modello create registration ID + upsert:**

Rimuove i duplicati causati dalle risposte perse se gli ID di registrazione sono stati archiviati nel dispositivo:

    ```java
    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);
    ```

**Aggiornare registrazioni:**

    ```java
    hub.updateRegistration(reg);
    ```

**Eliminare registrazioni:**

    ```java
    hub.deleteRegistration(regid);
    ```

**Effettuare query di registrazioni:**

* **Ottenere una singola registrazione:**

    ```java
    hub.getRegistration(regid);
    ```

* **Ottenere tutte le registrazioni nell'hub:**

    ```java
    hub.getRegistrations();
    ```

* **Ottenere registrazioni con tag:**

    ```java
    hub.getRegistrationsByTag("myTag");
    ```

* **Ottenere registrazioni per canale:**

    ```java
    hub.getRegistrationsByChannel("devicetoken");
    ```

Tutte le query di raccolta supportano i token $top e di continuazione.

### <a name="installation-api-usage"></a>Uso dell'API di installazione

L'API di installazione rappresenta un meccanismo alternativo per la gestione delle registrazioni. La gestione di più registrazioni non è semplice e può facilmente essere eseguita in maniera errata o inefficace, pertanto ora è possibile usare un SINGOLO oggetto di installazione.

L'installazione contiene tutti gli elementi necessari: il canale push (token del dispositivo), tag, modelli, riquadri secondari (per WNS e APN). Non è più necessario chiamare il servizio per ottenere l'ID. È sufficiente generare un GUID o qualsiasi altro identificatore, mantenerlo nel dispositivo e inviarlo al back-end con il canale push (token del dispositivo).

Nel back-end si dovrebbe eseguire una singola chiamata a `CreateOrUpdateInstallation`. È del tutto idempotente, pertanto è possibile ritentare, se necessario.

Un esempio per Amazon Kindle Fire:

    ```java
    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);
    ```

Per aggiornarlo:

    ```java
    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);
    ```

Per scenari avanzati, usare la funzionalità di aggiornamento parziale che consente di modificare solo determinate proprietà dell'oggetto di installazione. L'aggiornamento parziale è un subset di operazioni Patch JSON che è possibile eseguire in un oggetto di installazione.

    ```java
    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);
    ```

Eliminare l'installazione:

    ```java
    hub.deleteInstallation(installation.getInstallationId());
    ```

Le operazioni `CreateOrUpdate`, `Patch` e `Delete` saranno coerenti con `Get`. L'operazione richiesta passa alla coda di sistema durante la chiamata e viene eseguita in background. L'operazione Get non è progettata per lo scenario di runtime principale, ma solo per il debug e la risoluzione dei problemi ed è strettamente limitata dal servizio.

Il flusso di invio per le installazioni è identico a quello delle registrazioni. Per indirizzare la notifica all'installazione specifica: per farlo, usare il tag "InstallationId: {desired-id}". In questo caso, il codice è:

    ```java
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");
    ```

Per uno dei diversi modelli:

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");
    ```

### <a name="schedule-notifications-available-for-standard-tier"></a>Pianificare le notifiche (disponibile per il livello STANDARD)

Si tratta di un invio normale ma con un parametro aggiuntivo, scheduledTime, che indica quando deve essere recapitata la notifica. Il servizio accetta qualsiasi data e ora da 5 minuti a sette giorni a partire dalla data e ora correnti.

**Pianificare una notifica nativa di Windows:**

    ```java
    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());
    ```

### <a name="importexport-available-for-standard-tier"></a>Importazione/esportazione (disponibile per il livello STANDARD)

Può essere necessario eseguire un'operazione in blocco sulle registrazioni. In genere un'operazione di questo tipo viene eseguita per l'integrazione con un altro sistema o per una correzione su larga scala, come l'aggiornamento di tag. Se sono interessate migliaia di registrazioni, non è consigliabile usare il flusso Get/Update. La soluzione appropriata per tale scenario è la funzionalità di importazione/esportazione del sistema. Si fornirà l'accesso a un contenitore BLOB dell'account di archiviazione come origine dei dati in ingresso e come percorso per l'output.

**Inviare un processo di esportazione:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Inviare un processo di importazione:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Attendere il completamento di un processo:**

    ```java
    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }
    ```

**Ottenere tutti i processi:**

    ```java
    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();
    ```

**URI con firma SAS**:

 l'URL di un file BLOB o di un contenitore BLOB con un set di parametri come le autorizzazioni e l'ora di scadenza e con la firma di tutti questi elementi eseguita usando la chiave della firma di accesso condiviso dell'account. Azure Storage Java SDK include funzionalità avanzate, compresa la creazione di URI di questo tipo. In alternativa, esaminare la classe di test `ImportExportE2E` (dal percorso di GitHub) che include un'implementazione semplice e compatta dell'algoritmo di firma.

### <a name="send-notifications"></a>Inviare notifiche

L'oggetto notifica è semplicemente un corpo con intestazioni ed esistono alcuni metodi di utilità che consentono la creazione di notifiche modello e notifiche native.

* **Windows Store e Windows Phone 8.1 (non Silverlight)**

    ```java
    String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
    Notification n = Notification.createWindowsNotification(toast);
    hub.sendNotification(n);
    ```

* **iOS**

    ```java
    String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
    Notification n = Notification.createAppleNotification(alert);
    hub.sendNotification(n);
    ```

* **Android**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createFcmNotification(message);
    hub.sendNotification(n);
    ```

* **Windows Phone 8.0 e 8.1 Silverlight**

    ```java
    String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>Hello from Java!</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
    Notification n = Notification.createMpnsNotification(toast);
    hub.sendNotification(n);
    ```

* **Kindle Fire**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createAdmNotification(message);
    hub.sendNotification(n);
    ```

* **Inviare a tag**
  
    ```java
    Set<String> tags = new HashSet<String>();
    tags.add("boo");
    tags.add("foo");
    hub.sendNotification(n, tags);
    ```

* **Inviare a espressione tag**

    ```java
    hub.sendNotification(n, "foo && ! bar");
    ```

* **Inviare una notifica modello**

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("prop1", "v1");
    prop.put("prop2", "v2");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n);
    ```

Quando si esegue il codice Java, dovrebbe essere visualizzata una notifica sul dispositivo di destinazione.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento è stato illustrato come creare un semplice client REST Java per Hub di notifica. A questo punto è possibile:

* Scaricare la versione completa di [SDK per Java]contenente l'intero codice SDK.
* Consultare gli esempi:
  * [Introduzione ad Hub di notifica]
  * [Inviare le ultime notizie]
  * [Inviare le ultime notizie localizzate]
  * [Inviare notifiche agli utenti autenticati]
  * [Inviare notifiche multipiattaforma agli utenti autenticati]

[SDK per Java]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Introduzione ad Hub di notifica]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Inviare le ultime notizie]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Inviare le ultime notizie localizzate]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Inviare notifiche agli utenti autenticati]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Inviare notifiche multipiattaforma agli utenti autenticati]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven]: https://maven.apache.org/
