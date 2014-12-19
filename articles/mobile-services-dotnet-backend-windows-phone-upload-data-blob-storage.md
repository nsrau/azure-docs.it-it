<properties pageTitle="Usare Servizi mobili per caricare immagini nell'archiviazione BLOB (Windows Phone) | Servizi mobili" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage." metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" writer="glenga" services="mobile-services, storage" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="10/08/2014" ms.author="glenga" />

# Caricare immagini in Archiviazione di Azure usando Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

Questo argomento illustra come usare Servizi mobili di Azure per permettere alla propria app di caricare e archiviare immagini generate dall'utente in Archiviazione di Azure. Servizi mobili usa un database SQL per archiviare i dati. I dati BLOB vengono tuttavia archiviati in modo più efficiente nel servizio di archiviazione BLOB di Azure. 

Non è possibile distribuire in modo sicuro con l'app client le credenziali necessarie per caricare i dati nel servizio di archiviazione BLOB senza rischi. Queste credenziali devono invece essere archiviate nel servizio mobile ed essere usate per generare una firma di accesso condiviso usata per caricare una nuova immagine. La firma di accesso condiviso, ovvero una credenziale con una scadenza breve&mdash;in questo caso 5 minuti, viene restituita in modo sicuro da Servizi mobili all'app client. L'app usa quindi questa credenziale temporanea per caricare l'immagine. In questo esempio, i download del servizio BLOB sono pubblici.

In questa esercitazione verranno aggiunte funzionalità al [progetto di app di esempio GetStartedWithData](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/) per poter scattare foto e caricare le immagini in Azure tramite una firma di accesso condiviso generata da Servizi mobili. L'esercitazione fornisce istruzioni dettagliate sulle operazioni di base per aggiornare la semplice app TodoList in modo da caricare immagini nel servizio di archiviazione BLOB:

1. [Installare la libreria client di archiviazione]
2. [Aggiornare l'app client per l'acquisizione di immagini]
3. [Installare il client di archiviazione nel progetto di servizio mobile]
4. [Aggiornare la definizione di TodoItem nel modello di dati]
5. [Aggiornare il controller tabella per generare una firma di accesso condiviso]
6. [Caricare le immagini per verificare l'app]

Per completare questa esercitazione, è necessario disporre di:

+ Microsoft Visual Studio 2013 o versione successiva
+ [Windows Phone SDK 8.0] o versione successiva
+ Programma per la gestione dei pacchetti Nuget installato per Microsoft Visual Studio.
+ [Account di archiviazione di Azure][Come creare un account di archiviazione]
+ Completare l'esercitazione [Aggiungere Servizi mobili a un'app esistente](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/)  

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

##<a name="install-storage-client"></a>Installare il client di archiviazione per le app di Windows Store

Per poter usare una firma di accesso condiviso allo scopo di caricare immagini dall'app nella risorsa di archiviazione BLOB, è necessario prima aggiungere il pacchetto NuGet che installa la libreria client di archiviazione per le app di Windows Store.

1. In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Gestisci pacchetti NuGet**.

2. Nel riquadro sinistro selezionare la categoria **Online**, selezionare **Include Prerelease**, cercare **WindowsAzure.Storage-Preview**, fare clic su **Installa** nel pacchetto di **Archiviazione di Azure**, quindi accettare i contratti di licenza. 

  	![][2]

  	La libreria client per i servizi di archiviazione di Azure verrà aggiunta al progetto.

[WACOM.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]
 
<!-- Anchors. -->
[Installare la libreria client di archiviazione]: #install-storage-client
[Aggiornare l'app client per l'acquisizione di immagini]: #add-select-images
[Installare il client di archiviazione nel progetto di servizio mobile]: #storage-client-server
[Aggiornare la definizione di TodoItem nel modello di dati]: #update-data-model
[Aggiornare il controller tabella per generare una firma di accesso condiviso]: #update-scripts
[Caricare le immagini per verificare l'app]: #test
[Passaggi successivi]:#next-steps

<!-- Images. -->
[2]: ./media/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png

<!-- URLs. -->
[Inviare posta elettronica da Servizi mobili con SendGrid]: /it-it/documentation/articles/store-sendgrid-mobile-services-send-email-scripts/
[Pianificare processi back-end in Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-phone-get-started

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Come creare un account di archiviazione]: /it-it/documentation/articles/storage-create-storage-account/
[Libreria client di archiviazione di Azure per app di Windows Store]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
[Windows Phone SDK 8.0]: http://www.microsoft.com/it-it/download/details.aspx?id=35471


