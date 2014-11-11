<properties pageTitle="Use Mobile Services to upload images to blob storage (Windows Phone) | Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage." metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="wesmc" writer="wesmc" services="mobile-services,storage"  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Caricamento di immagini in Archiviazione di Azure utilizzando Servizi mobili

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage" title="Windows Store C#">Windows Store C#</a><a href="/it-it/documentation/articles/mobile-services-windows-phone-upload-data-blob-storage" title="Windows Phone" class="current">Windows Phone</a></div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage" title=".NET backend" >Back-end .NET</a> | <a href="/it-it/documentation/articles/mobile-services-windows-phone-upload-data-blob-storage" title="JavaScript backend" class="current">Back-end JavaScript</a></div>

In questo argomento verrà illustrato come utilizzare Servizi mobili di Azure per consentire alla propria app di caricare e archiviare immagini generate dall'utente in Archiviazione di Azure. Servizi mobili utilizza un database SQL per archiviare i dati. I dati BLOB vengono tuttavia archiviati in modo più efficiente nel servizio di archiviazione BLOB di Azure.

Non è possibile distribuire in modo sicuro con l'app client le credenziali necessarie per caricare i dati nel servizio di archiviazione BLOB senza rischi. Queste credenziali devono invece essere archiviate nel servizio mobile ed essere utilizzate per generare una firma di accesso condiviso utilizzata per caricare una nuova immagine. La firma di accesso condiviso, ovvero una credenziale con una scadenza breve, in questo caso 5 minuti, viene restituita in modo sicuro da Servizi mobili all'app client. L'app utilizza quindi questa credenziale temporanea per caricare l'immagine. In questo esempio, i download del servizio BLOB sono pubblici.

In questa esercitazione verranno aggiunte funzionalità all'app di guida introduttiva per Servizi mobili per poter scattare foto e caricare le immagini in Azure tramite una firma di accesso condiviso generata da Servizi mobili. L'esercitazione fornisce istruzioni dettagliate sulle operazioni di base per aggiornare il progetto di guida introduttiva per Servizi mobili in modo da caricare immagini nel servizio di archiviazione BLOB:

1.  [Installazione della libreria client di archiviazione][Installazione della libreria client di archiviazione]
2.  [Aggiornamento dello script insert per generare una firma di accesso condiviso][Aggiornamento dello script insert per generare una firma di accesso condiviso]
3.  [Aggiornamento dell'app client per l'acquisizione di immagini][Aggiornamento dell'app client per l'acquisizione di immagini]
4.  [Caricamento delle immagini per verificare l'app][Caricamento delle immagini per verificare l'app]

Per completare questa esercitazione, è necessario disporre di:

-   Microsoft Visual Studio 2012 Express per Windows 8 o versione successiva
-   [Windows Phone SDK 8.0][Windows Phone SDK 8.0] o versione successiva
-   Programma per la gestione dei pacchetti Nuget installato per Microsoft Visual Studio.
-   [Account di archiviazione di Azure][Account di archiviazione di Azure]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili][Introduzione a Servizi mobili].

## <a name="install-storage-client"></a>Installazione del client di archiviazione per le app di Windows Phone

Per poter usare una firma di accesso condiviso per caricare immagini nel servizio di archiviazione BLOB, è necessario aggiungere innanzitutto il pacchetto NuGet che consente di installare la libreria client di archiviazione per le app di Windows Phone.

1.  In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Manage NuGet Packages**.

2.  Nel riquadro sinistro selezionare la categoria **Online**, selezionare **Include Prerelease**, cercare **WindowsAzure.Storage-Preview**, fare clic su **Installa** nel pacchetto di **Archiviazione di Azure**, quindi accettare i contratti di licenza.

    ![][0]

    La libreria client per i servizi di archiviazione di Azure verrà aggiunta al progetto.

A questo punto, aggiornare l'app di guida introduttiva in modo da acquisire e caricare le immagini.

## <a name="update-scripts"></a>Aggiornamento dello script insert registrato nel portale di gestione

[WACOM.INCLUDE [mobile-services-configure-blob-storage](../includes/mobile-services-configure-blob-storage.md)]

> [WACOM.NOTE]Per aggiungere nuove proprietà all'oggetto TodoItem, è necessario che lo schema dinamico sia abilitato nel servizio mobile. Quando lo schema dinamico è abilitato, nella tabella TodoItem vengono automaticamente aggiunte nuove colonne mappate a queste nuove proprietà.

[WACOM.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]

## <a name="next-steps"> </a>Passaggi successivi

Ora che le immagini sono state caricate in modo sicuro integrando il servizio mobile con il servizio BLOB, leggere altri argomenti sul servizio back-end e sull'integrazione:

-   [Invio di posta elettronica da Servizi mobili con SendGrid][Invio di posta elettronica da Servizi mobili con SendGrid]

Informazioni su come aggiungere funzionalità di posta elettronica a un servizio mobile tramite il servizio di posta elettronica SendGrid. Questo argomento illustra come aggiungere script sul lato server per inviare messaggi di posta elettronica tramite SendGrid.

-   [Pianificazione di processi back-end in Servizi mobili][Pianificazione di processi back-end in Servizi mobili]

Informazioni su come utilizzare la funzionalità di pianificazione di processi di Servizi mobili per definire codice dello script del server da eseguire in base a una pianificazione definita dall'utente.

-   [Riferimento per gli script server di Servizi mobili][Riferimento per gli script server di Servizi mobili]

Argomenti di riferimento per l'utilizzo di script del server per eseguire operazioni sul lato server e per l'integrazione con altri componenti e risorse esterne di Azure.

-   [Riferimento per i concetti e le procedure di .NET per Servizi mobili][Riferimento per i concetti e le procedure di .NET per Servizi mobili]

Ulteriori informazioni su come utilizzare Servizi mobili con .NET.



  [Installazione della libreria client di archiviazione]: #install-storage-client
  [Aggiornamento dello script insert per generare una firma di accesso condiviso]: #update-scripts
  [Aggiornamento dell'app client per l'acquisizione di immagini]: #add-select-images
  [Caricamento delle immagini per verificare l'app]: #test
  [Windows Phone SDK 8.0]: http://www.microsoft.com/it-it/download/details.aspx?id=35471
  [Account di archiviazione di Azure]: /it-it/manage/services/storage/how-to-create-a-storage-account
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-phone-get-started
  [0]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png
  [mobile-services-configure-blob-storage]: ../includes/mobile-services-configure-blob-storage.md
  [mobile-services-windows-phone-upload-to-blob-storage]: ../includes/mobile-services-windows-phone-upload-to-blob-storage.md
  [Invio di posta elettronica da Servizi mobili con SendGrid]: /it-it/develop/mobile/tutorials/send-email-with-sendgrid/
  [Pianificazione di processi back-end in Servizi mobili]: /it-it/develop/mobile/tutorials/schedule-backend-tasks/
  [Riferimento per gli script server di Servizi mobili]: http://go.microsoft.com/fwlink/p/?LinkId=262293
  [Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library
