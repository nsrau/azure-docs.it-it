<properties pageTitle="Use Mobile Services to upload images to blob storage (Windows Store) | Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage and access the images from your Windows Store app." metaCanonical="" services="" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" solutions="mobile" manager="" editor="" />

Caricamento di immagini in Archiviazione di Azure utilizzando Servizi mobili
============================================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage "Windows Store C#")[Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-upload-data-blob-storage "Windows Phone")

In questo argomento verrà illustrato come utilizzare Servizi mobili di Azure per consentire alla propria app di caricare e archiviare immagini generate dall'utente in Archiviazione di Azure. Servizi mobili utilizza un database SQL per archiviare i dati. I dati BLOB vengono tuttavia archiviati in modo più efficiente nel servizio di archiviazione BLOB di Azure.

Non è possibile distribuire in modo sicuro con l'app client le credenziali necessarie per caricare i dati nel servizio di archiviazione BLOB senza rischi. Queste credenziali devono invece essere archiviate nel servizio mobile ed essere utilizzate per generare una firma di accesso condiviso utilizzata per caricare una nuova immagine. La firma di accesso condiviso, ovvero una credenziale con una scadenza breve, in questo caso 5 minuti, viene restituita in modo sicuro da Servizi mobili all'app client. L'app utilizza quindi questa credenziale temporanea per caricare l'immagine. In questo esempio, i download del servizio BLOB sono pubblici.

In questa esercitazione verranno aggiunte funzionalità all'app di guida introduttiva per Servizi mobili per poter scattare foto e caricare le immagini in Azure tramite una firma di accesso condiviso generata da Servizi mobili. L'esercitazione fornisce istruzioni dettagliate sulle operazioni di base per aggiornare il progetto di guida introduttiva per Servizi mobili in modo da caricare immagini nel servizio di archiviazione BLOB:

1.  [Installazione della libreria client di archiviazione](#install-storage-client)
2.  [Aggiornamento dello script insert per generare una firma di accesso condiviso](#update-scripts)
3.  [Aggiornamento dell'app client per l'acquisizione di immagini](#add-select-images)
4.  [Caricamento delle immagini per verificare l'app](#test)

Per completare questa esercitazione, è necessario disporre di:

-   Microsoft Visual Studio 2012 Express per Windows 8 o versione successiva
-   [Account di archiviazione di Azure](/en-us/manage/services/storage/how-to-create-a-storage-account)
-   Una fotocamera o un altro dispositivo per l'acquisizione di immagini collegato al computer.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili](/en-us/documentation/articles/mobile-services-windows-store-get-started).

Installazione del client di archiviazione per le app di Windows Store
---------------------------------------------------------------------

Per poter utilizzare una firma di accesso condiviso per caricare immagini nel servizio di archiviazione BLOB, è necessario aggiungere innanzitutto il pacchetto NuGet che consente di installare la libreria client di archiviazione per le app di Windows Store.

1.  In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Manage NuGet Packages**.

2.  Nel riquadro sinistro selezionare la categoria **Online**, cercare `WindowsAzure.Storage`, fare clic su **Installa** nel pacchetto di **Archiviazione di Azure**, quindi accettare il contratto di licenza.

  	![](./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png)

  	La libreria client per i servizi di archiviazione di Azure verrà aggiunta al progetto.

A questo punto, aggiornare l'app di guida introduttiva in modo da acquisire e caricare le immagini.

Aggiornamento dello script insert registrato nel portale di gestione
--------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-configure-blob-storage](../includes/mobile-services-configure-blob-storage.md)]

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

Passaggi successivi
-------------------

Ora che le immagini sono state caricate in modo sicuro integrando il servizio mobile con il servizio BLOB, leggere altri argomenti sul servizio back-end e sull'integrazione:

-   [Invio di posta elettronica da Servizi mobili con SendGrid](/en-us/develop/mobile/tutorials/send-email-with-sendgrid/)

    Informazioni su come aggiungere funzionalità di posta elettronica a un servizio mobile tramite il servizio di posta elettronica SendGrid. Questo argomento illustra come aggiungere script sul lato server per inviare messaggi di posta elettronica tramite SendGrid.

-   [Pianificazione di processi back-end in Servizi mobili](/en-us/documentation/articles/mobile-services-schedule-recurring-tasks)

    Informazioni su come utilizzare la funzionalità di pianificazione di processi di Servizi mobili per definire codice dello script del server da eseguire in base a una pianificazione definita dall'utente.

-   [Riferimento per gli script server di Servizi mobili](http://go.microsoft.com/fwlink/p/?LinkId=262293)

    Argomenti di riferimento per l'utilizzo di script del server per eseguire operazioni sul lato server e per l'integrazione con altri componenti e risorse esterne di Azure.

-   [Riferimento per i concetti e le procedure di .NET per Servizi mobili](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)

    Ulteriori informazioni su come utilizzare Servizi mobili con .NET.


