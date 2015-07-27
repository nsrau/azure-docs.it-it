<properties 
	pageTitle="Usare Servizi mobili per caricare immagini nell'archiviazione BLOB (Windows Phone) | Servizi mobili" 
	description="Informazioni su come usare Servizi mobili per caricare immagini nel servizio di archiviazione BLOB di Azure." 
	documentationCenter="windows" 
	authors="ggailey777" 
	services="mobile-services,storage" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# Caricare immagini in Archiviazione di Azure mediante Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

##Panoramica
Questo argomento illustra come usare Servizi mobili di Azure per consentire alla propria app per Windows Phone 8 o Windows Phone 8.1 Silverlight di caricare e archiviare immagini generate dall'utente in Archiviazione di Azure. Servizi mobili utilizza un database SQL per archiviare i dati. I dati BLOB vengono tuttavia archiviati in modo più efficiente nel servizio di archiviazione BLOB di Azure.

Non è possibile distribuire in modo sicuro con l'app client le credenziali necessarie per caricare i dati nel servizio di archiviazione BLOB senza rischi. Queste credenziali devono invece essere archiviate nel servizio mobile ed essere utilizzate per generare una firma di accesso condiviso utilizzata per caricare una nuova immagine. La firma di accesso condiviso, ovvero una credenziale con una scadenza breve, in questo caso 5 minuti, viene restituita in modo sicuro da Servizi mobili all'app client. L'app usa quindi questa credenziale temporanea per caricare l'immagine. In questo esempio, i download del servizio BLOB sono pubblici.

Questa esercitazione consente di aggiungere funzionalità al progetto di app di esempio [GetStartedWithData](mobile-services-dotnet-backend-windows-phone-get-started-data.md) per scattare foto e caricare le immagini in Azure tramite una firma di accesso condiviso generata da Servizi mobili.

##Prerequisiti 

Per completare questa esercitazione, è necessario disporre di:

+ Microsoft Visual Studio 2013 o versione successiva
+ [Windows Phone SDK 8.0] o versione successiva
+ Programma per la gestione dei pacchetti Nuget installato per Microsoft Visual Studio.
+ [Account di archiviazione di Azure][How To Create a Storage Account]
+ Completare l'esercitazione [Aggiungere Servizi mobili a un'app esistente](mobile-services-dotnet-backend-windows-phone-get-started-data.md)  

>[AZURE.NOTE]Questa esercitazione supporta solo app per Windows Phone 8 e Windows Phone 8.1 Silverlight. Non supporta app di Windows Phone Store 8.1 o app di Windows 8.1 universali.

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

##<a name="install-storage-client"></a>Installare il client di archiviazione per le app di Windows Store

Per poter usare una firma di accesso condiviso allo scopo di caricare immagini dall'app nella risorsa di archiviazione BLOB, è necessario prima aggiungere il pacchetto NuGet che installa la libreria client di archiviazione per le app di Windows Store.

1. In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul progetto dell'app client e quindi scegliere **Gestisci pacchetti NuGet**.

2. Nel riquadro sinistro selezionare la categoria **Online**, selezionare **Include Prerelease**, cercare **WindowsAzure.Storage-Preview**, fare clic su **Installa** nel pacchetto di **Archiviazione di Azure**, quindi accettare i contratti di licenza.

  	![][2]

  	La libreria client per i servizi di archiviazione di Azure verrà aggiunta al progetto.

[AZURE.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]
 
<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Install the storage client in the mobile service project]: #storage-client-server
[Update the TodoItem definition in the data model]: #update-data-model
[Update the table controller to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->
[2]: ./media/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png

<!-- URLs. -->
[Send email from Mobile Services with SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Schedule backend jobs in Mobile Services]: mobile-services-dotnet-backend-schedule-recurring-tasks.md
[Get started with Mobile Services]: ../mobile-services-windows-phone-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Windows Phone SDK 8.0]: http://www.microsoft.com/download/details.aspx?id=35471


 

<!---HONumber=July15_HO3-->