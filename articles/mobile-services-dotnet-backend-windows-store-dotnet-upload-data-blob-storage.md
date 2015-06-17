<properties 
	pageTitle="Usare Servizi mobili per caricare immagini nell'archiviazione BLOB (Windows Store) | Servizi mobili" 
	description="Informazioni su come usare Servizi mobili per caricare immagini nel servizio di archiviazione BLOB di Azure." 
	documentationCenter="windows" 
	authors="ggailey777" 
	writer="glenga" 
	services="mobile-services,storage" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="glenga"/>

# Caricare immagini in Archiviazione di Azure mediante Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

In questo argomento verrà illustrato come utilizzare Servizi mobili di Azure per consentire alla propria app di caricare e archiviare immagini generate dall'utente in Archiviazione di Azure. Servizi mobili utilizza un database SQL per archiviare i dati. I dati BLOB vengono tuttavia archiviati in modo più efficiente nel servizio di archiviazione BLOB di Azure.

Non è possibile distribuire in modo sicuro con l'app client le credenziali necessarie per caricare i dati nel servizio di archiviazione BLOB senza rischi. Queste credenziali devono invece essere archiviate nel servizio mobile ed essere utilizzate per generare una firma di accesso condiviso utilizzata per caricare una nuova immagine. La firma di accesso condiviso, ovvero una credenziale con una scadenza breve, in questo caso 5 minuti, viene restituita in modo sicuro da Servizi mobili all'app client. L'app usa quindi questa credenziale temporanea per caricare l'immagine. In questo esempio, i download del servizio BLOB sono pubblici.

In questa esercitazione verranno aggiunte funzionalità all'app di guida introduttiva per Servizi mobili per poter scattare foto e caricare le immagini in Azure tramite una firma di accesso condiviso generata da Servizi mobili. L'esercitazione fornisce istruzioni dettagliate sulle operazioni di base per aggiornare il progetto di guida introduttiva per Servizi mobili in modo da caricare immagini nel servizio di archiviazione BLOB:

1. [Installare la libreria client di archiviazione]
2. [Aggiornare l'app client per l'acquisizione di immagini]
3. [Installare il client di archiviazione nel progetto di servizio mobile]
4. [Aggiornare la definizione di TodoItem nel modello di dati]
5. [Aggiornare il controller tabella per generare una firma di accesso condiviso]
6. [Caricare le immagini per testare l'app]

Per completare questa esercitazione, è necessario disporre di:

+ Microsoft Visual Studio 2013 o versione successiva.
+ Programma per la gestione dei pacchetti Nuget installato per Microsoft Visual Studio.
+ [Account di archiviazione di Azure][How To Create a Storage Account]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili].

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

##<a name="install-storage-client"></a>Installare il client di archiviazione per le app di Windows Store

Per poter usare una firma di accesso condiviso allo scopo di caricare immagini dall'app nella risorsa di archiviazione BLOB, è necessario prima aggiungere il pacchetto NuGet che installa la libreria client di archiviazione per le app di Windows Store.

1. In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul progetto dell'app client e quindi scegliere **Gestisci pacchetti NuGet**.

2. Nel riquadro sinistro selezionare la categoria **Online**, selezionare **Include Prerelease**, cercare **WindowsAzure.Storage-Preview**, fare clic su **Installa** nel pacchetto di **Archiviazione di Azure**, quindi accettare i contratti di licenza.

  	![][2]

  	La libreria client per i servizi di archiviazione di Azure verrà aggiunta al progetto.

A questo punto, aggiornare l'app di guida introduttiva in modo da acquisire e caricare le immagini.

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

 
<!-- Anchors. -->
[Installare la libreria client di archiviazione]: #install-storage-client
[Aggiornare l'app client per l'acquisizione di immagini]: #add-select-images
[Installare il client di archiviazione nel progetto di servizio mobile]: #storage-client-server
[Aggiornare la definizione di TodoItem nel modello di dati]: #update-data-model
[Aggiornare il controller tabella per generare una firma di accesso condiviso]: #update-scripts
[Caricare le immagini per testare l'app]: #test
[Next Steps]: #next-steps

<!-- Images. -->
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png

<!-- URLs. -->
[Send email from Mobile Services with SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Schedule backend jobs in Mobile Services]: mobile-services-dotnet-backend-schedule-recurring-tasks.md
[Introduzione a Servizi mobili]: mobile-services-windows-store-dotnet-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: storage/storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Windows Phone SDK 8.0]: http://www.microsoft.com/download/details.aspx?id=35471



<!--HONumber=54-->