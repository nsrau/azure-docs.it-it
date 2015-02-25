<properties pageTitle="Usare Servizi mobili per caricare immagini nell'archiviazione BLOB (Windows Phone) | Servizi mobili" description="Informazioni su come usare Servizi mobili per caricare immagini nel servizio di archiviazione BLOB di Azure." documentationCenter="windows" authors="wesmc7777" writer="wesmc" services="mobile-services, storage" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="10/06/2014" ms.author="wesmc"/>

# Caricare immagini in Archiviazione di Azure usando Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

Questo argomento descrive come usare Servizi mobili di Azure per consentire alla propria app di caricare e archiviare immagini generate dall'utente in Archiviazione di Azure. Servizi mobili usa un database SQL per archiviare i dati. I dati BLOB vengono tuttavia archiviati in modo più efficiente nel servizio di archiviazione BLOB di Azure. 

Non è possibile distribuire in modo sicuro con l'app client le credenziali necessarie per caricare i dati nel servizio di archiviazione BLOB senza rischi. Queste credenziali devono invece essere archiviate nel servizio mobile ed essere usate per generare una firma di accesso condiviso usata per caricare una nuova immagine. La firma di accesso condiviso, ovvero una credenziale con una scadenza breve (in questo caso 5 minuti), viene restituita in modo sicuro da Servizi mobili all'app client. L'app usa quindi questa credenziale temporanea per caricare l'immagine. In questo esempio, i download del servizio BLOB sono pubblici.

In questa esercitazione verranno aggiunte funzionalità al [progetto di app di esempio GetStartedWithData](/it-it/documentation/articles/mobile-services-windows-phone-get-started-data/) per poter scattare foto e caricare le immagini in Azure tramite una firma di accesso condiviso generata da Servizi mobili. L'esercitazione fornisce istruzioni dettagliate sulle operazioni di base per aggiornare la semplice app TodoList in modo da caricare immagini nel servizio di archiviazione BLOB:

1. [Installazione della libreria client di archiviazione]
2. [Aggiornamento dello script insert per generare una firma di accesso condiviso]
3. [Aggiornamento dell'app client per l'acquisizione di immagini]
4. [Caricamento di immagini per testare l'app]

Per completare questa esercitazione, è necessario soddisfare i seguenti requisiti:

+ Microsoft Visual Studio 2012 Express per Windows 8 o versione successiva
+ [Windows Phone SDK 8.0] o versione successiva
+ Programma per la gestione dei pacchetti Nuget installato per Microsoft Visual Studio.
+ [Account di archiviazione di Azure][Come creare un account di archiviazione]
+ Completare l'esercitazione [Aggiungere Servizi mobili a un'app esistente](/it-it/documentation/articles/mobile-services-windows-phone-get-started-data/)  


##<a name="install-storage-client"></a>Installazione del client di archiviazione per le app di Windows Phone

Per poter usare una firma di accesso condiviso per caricare immagini nel servizio di archiviazione BLOB, è necessario aggiungere innanzitutto il pacchetto NuGet che consente di installare la libreria client di archiviazione per le app di Windows Phone.

1. In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Gestisci pacchetti NuGet**.

2. Nel riquadro sinistro selezionare la categoria **Online**, selezionare **Includi versione preliminare**, cercare **WindowsAzure.Storage-Preview**, fare clic su **Installa** nel pacchetto di **Archiviazione di Azure**, quindi accettare i contratti di licenza. 

  	![][2]

  	La libreria client per i servizi di archiviazione di Azure verrà aggiunta al progetto.

A questo punto, aggiornare l'app di guida introduttiva in modo da acquisire e caricare le immagini.

##<a name="update-scripts"></a>Aggiornare gli script insert registrati nel portale di gestione


[AZURE.INCLUDE [mobile-services-configure-blob-storage](../includes/mobile-services-configure-blob-storage.md)]

>[AZURE.NOTE]Per aggiungere nuove proprietà all'oggetto TodoItem, è necessario che lo schema dinamico sia abilitato nel servizio mobile. Quando lo schema dinamico è abilitato, nella tabella TodoItem vengono automaticamente aggiunte nuove colonne mappate a queste nuove proprietà.

[AZURE.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]


## <a name="next-steps"> </a>Passaggi successivi

Ora che le immagini sono state caricate in modo sicuro integrando il servizio mobile con il servizio BLOB, leggere altri argomenti sul servizio back-end e sull'integrazione:

+ [Invio di posta elettronica da Servizi mobili con SendGrid]
 
  Informazioni su come aggiungere funzionalità di posta elettronica a un servizio mobile tramite il servizio di posta elettronica SendGrid. Questo argomento descrive come aggiungere script sul lato server per inviare messaggi di posta elettronica tramite SendGrid.

+ [Pianificazione di processi back-end in Servizi mobili]

  Informazioni su come usare la funzionalità di pianificazione di processi di Servizi mobili per definire codice dello script del server da eseguire in base a una pianificazione definita dall'utente.

+ [Informazioni di riferimento sugli script del server di Servizi mobili]

  Argomenti di riferimento per l'uso di script del server per eseguire operazioni sul lato server e per l'integrazione con altri componenti e risorse esterne di Azure.
 
+ [Riferimento per i concetti e le procedure di .NET per Servizi mobili]

  Altre informazioni su come usare Servizi mobili con .NET.
  
 
<!-- Anchors. -->
[Installazione della libreria client di archiviazione]: #install-storage-client
[Aggiornamento dell'app client per l'acquisizione di immagini]: #add-select-images
[Aggiornamento dello script insert per generare una firma di accesso condiviso]: #update-scripts
[Caricamento di immagini per testare l'app]: #test
[Passaggi successivi]:#next-steps

<!-- Images. -->


[2]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


[5]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-WMAppmanifest-wp8.png
[6]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-wp8.png
[7]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-camera-wp8.png
[8]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-save-wp8.png
[9]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-final-wp8.png

[11]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-camera-accept-wp8.png

<!-- URLs. -->
[Invio di posta elettronica da Servizi mobili con SendGrid]: /it-it/develop/mobile/tutorials/send-email-with-sendgrid/
[Pianificazione di processi back-end in Servizi mobili]: /it-it/develop/mobile/tutorials/schedule-backend-tasks/
[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-phone-get-started

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Come creare un account di archiviazione]: /it-it/manage/services/storage/how-to-create-a-storage-account
[Libreria client di archiviazione di Azure per app di Windows Store]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library
[Windows Phone SDK 8.0]: http://www.microsoft.com/it-it/download/details.aspx?id=35471




<!--HONumber=42-->
