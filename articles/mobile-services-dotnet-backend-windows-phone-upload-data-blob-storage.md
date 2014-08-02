<properties pageTitle="Use Mobile Services to upload images to blob storage (Windows Phone) | Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage." metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" writer="glenga" />

Caricamento di immagini in Archiviazione di Azure utilizzando Servizi mobili
============================================================================

> [AZURE.SELECTOR-LIST (Platform | Backend )] 
- [(Windows Store C\# | .NET)](mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage.md) 
- [(Windows Store C\# | JavaScript)](mobile-services-windows-store-dotnet-upload-data-blob-storage.md) 
- [(Windows Phone | .NET)](mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage.md) 
- [(Windows Phone | JavaScript)](mobile-services-windows-phone-upload-data-blob-storage.md)

In questo argomento verrà illustrato come utilizzare Servizi mobili di Azure per consentire alla propria app di caricare e archiviare immagini generate dall'utente in Archiviazione di Azure. Servizi mobili utilizza un database SQL per archiviare i dati. I dati BLOB vengono tuttavia archiviati in modo più efficiente nel servizio di archiviazione BLOB di Azure.

Non è possibile distribuire in modo sicuro con l'app client le credenziali necessarie per caricare i dati nel servizio di archiviazione BLOB senza rischi. Queste credenziali devono invece essere archiviate nel servizio mobile ed essere utilizzate per generare una firma di accesso condiviso utilizzata per caricare una nuova immagine. La firma di accesso condiviso, ovvero una credenziale con una scadenza breve, in questo caso 5 minuti, viene restituita in modo sicuro da Servizi mobili all'app client. L'app utilizza quindi questa credenziale temporanea per caricare l'immagine. In questo esempio, i download del servizio BLOB sono pubblici.

In questa esercitazione verranno aggiunte funzionalità all'app di guida introduttiva per Servizi mobili per poter scattare foto e caricare le immagini in Azure tramite una firma di accesso condiviso generata da Servizi mobili. L'esercitazione fornisce istruzioni dettagliate sulle operazioni di base per aggiornare il progetto di guida introduttiva per Servizi mobili in modo da caricare immagini nel servizio di archiviazione BLOB:

1.  [Installazione della libreria client di archiviazione](#install-storage-client)
2.  [Aggiornamento del controller tabella per generare una firma di accesso condiviso](#update-scripts)
3.  [Aggiornamento dell'app client per l'acquisizione di immagini](#add-select-images)
4.  [Caricamento delle immagini per verificare l'app](#test)

Per completare questa esercitazione, è necessario disporre di:

-   Microsoft Visual Studio 2012 Express per Windows 8 o versione successiva
-   [Windows Phone SDK 8.0](http://www.microsoft.com/en-us/download/details.aspx?id=35471) o versione successiva
-   Programma per la gestione dei pacchetti Nuget installato per Microsoft Visual Studio.
-   [Account di archiviazione di Azure](/en-us/manage/services/storage/how-to-create-a-storage-account)

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili](/en-us/documentation/articles/mobile-services-windows-phone-get-started).

Installazione del client di archiviazione per le app di Windows Store
---------------------------------------------------------------------

Per poter utilizzare una firma di accesso condiviso per caricare immagini nel servizio di archiviazione BLOB, è necessario aggiungere innanzitutto il pacchetto NuGet che consente di installare la libreria client di archiviazione per le app di Windows Store.

1.  In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Manage NuGet Packages**.

2.  Nel riquadro sinistro selezionare la categoria **Online**, selezionare **Include Prerelease**, cercare **WindowsAzure.Storage-Preview**, fare clic su **Installa** nel pacchetto di **Archiviazione di Azure**, quindi accettare i contratti di licenza.

	![](./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png) 

	La libreria client per i servizi di archiviazione di Azure verrà aggiunta al progetto.

A questo punto, aggiornare l'app di guida introduttiva in modo da acquisire e caricare le immagini.

Aggiornamento del controller TodoItem per generare una firma di accesso condiviso
---------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../includes/mobile-services-configure-blob-storage.md)]

[WACOM.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]

Passaggi successivi
-------------------

Ora che le immagini sono state caricate in modo sicuro integrando il servizio mobile con il servizio BLOB, leggere altri argomenti sul servizio back-end e sull'integrazione:

-   [Invio di posta elettronica da Servizi mobili con SendGrid](/en-us/develop/mobile/tutorials/send-email-with-sendgrid/)

    Informazioni su come aggiungere funzionalità di posta elettronica a un servizio mobile tramite il servizio di posta elettronica SendGrid. Questo argomento illustra come aggiungere script sul lato server per inviare messaggi di posta elettronica tramite SendGrid.

-   [Pianificazione di processi back-end in Servizi mobili](/en-us/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks)

    Informazioni su come utilizzare la funzionalità di pianificazione di processi di Servizi mobili per definire codice dello script del server da eseguire in base a una pianificazione definita dall'utente.

-   [Riferimento per i concetti e le procedure di .NET per Servizi mobili](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)

    Ulteriori informazioni su come utilizzare Servizi mobili con .NET.


<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Update the table controller to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]:#next-steps

<!-- Images. -->
[2]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png

<!-- URLs. -->
[Send email from Mobile Services with SendGrid]: /en-us/develop/mobile/tutorials/send-email-with-sendgrid/
[Schedule backend jobs in Mobile Services]: /en-us/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-windows-phone-get-started

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: /en-us/manage/services/storage/how-to-create-a-storage-account
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[Mobile Services .NET How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
[Windows Phone SDK 8.0]: http://www.microsoft.com/en-us/download/details.aspx?id=35471


