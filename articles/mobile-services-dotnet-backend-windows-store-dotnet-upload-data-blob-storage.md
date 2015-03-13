<properties 
	pageTitle="Uso di Servizi mobili per caricare immagini nell'archiviazione BLOB (Windows Store) | Servizi mobili" 
	description="Informazioni su come usare Servizi mobili per caricare immagini nel servizio di archiviazione BLOB di Azure." 
	documentationCenter="windows" 
	authors="ggailey777" 
	Writer="glenga" 
	services="mobile-services" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="glenga"/>

# Caricare immagini in Archiviazione di Azure usando Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

Questo argomento descrive come usare Servizi mobili di Azure per consentire alla propria app di caricare e archiviare immagini generate dall'utente in Archiviazione di Azure. Servizi mobili usa un database SQL per archiviare i dati. I dati BLOB vengono tuttavia archiviati in modo più efficiente nel servizio di archiviazione BLOB di Azure. 

Non è possibile distribuire in modo sicuro con l'app client le credenziali necessarie per caricare i dati nel servizio di archiviazione BLOB senza rischi. Queste credenziali devono invece essere archiviate nel servizio mobile ed essere usate per generare una firma di accesso condiviso usata per caricare una nuova immagine. La firma di accesso condiviso, ovvero una credenziale con una scadenza breve, in questo caso 5 minuti, viene restituita in modo sicuro da Servizi mobili all'app client. L'app usa quindi questa credenziale temporanea per caricare l'immagine. In questo esempio, i download del servizio BLOB sono pubblici.

In questa esercitazione verranno aggiunte funzionalità all'app di guida introduttiva per Servizi mobili per poter scattare foto e caricare le immagini in Azure tramite una firma di accesso condiviso generata da Servizi mobili. L'esercitazione fornisce istruzioni dettagliate sulle operazioni di base per aggiornare il progetto di guida introduttiva per Servizi mobili in modo da caricare immagini nel servizio di archiviazione BLOB:

1. [Installazione della libreria client di archiviazione]
2. [Aggiornamento dell'app client per l'acquisizione di immagini]
3. [Installare il client di archiviazione nel progetto di servizio mobile]
4. [Aggiornare la definizione di TodoItem nel modello di dati]
5. [Aggiornare il controller tabella per generare una firma di accesso condiviso]
6. [Caricamento di immagini per testare l'app]

Per completare questa esercitazione, è necessario soddisfare i seguenti requisiti:

+ Microsoft Visual Studio 2013 o versione successiva.
+ Programma per la gestione dei pacchetti Nuget installato per Microsoft Visual Studio.
+ [Account di archiviazione di Azure][Come creare un account di archiviazione]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili]. 

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

##<a name="install-storage-client"></a>Installare il client di archiviazione per le app di Windows Store

Per poter usare una firma di accesso condiviso allo scopo di caricare immagini dall'app nella risorsa di archiviazione BLOB, è necessario prima aggiungere il pacchetto NuGet che installa la libreria client di archiviazione per le app di Windows Store.

1. In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Gestisci pacchetti NuGet**.

2. Nel riquadro sinistro selezionare la categoria **Online**, selezionare **Includi versione preliminare**, cercare **WindowsAzure.Storage-Preview**, fare clic su **Installa** nel pacchetto di **Archiviazione di Azure**, quindi accettare i contratti di licenza. 

  	![][2]

  	La libreria client per i servizi di archiviazione di Azure verrà aggiunta al progetto.

A questo punto, aggiornare l'app di guida introduttiva in modo da acquisire e caricare le immagini.

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

 
<!-- Anchors. -->
[Installazione della libreria client di archiviazione]: #install-storage-client
[Aggiornamento dell'app client per l'acquisizione di immagini]: #add-select-images
[Installare il client di archiviazione nel progetto di servizio mobile]: #storage-client-server
[Aggiornare la definizione di TodoItem nel modello di dati]: #update-data-model
[Aggiornare il controller tabella per generare una firma di accesso condiviso]: #update-scripts
[Caricamento di immagini per testare l'app]: #test
[Passaggi successivi]:#next-steps

<!-- Images. -->
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png

<!-- URLs. -->
[Invio di posta elettronica da Servizi mobili con SendGrid]: /it-it/documentation/articles/store-sendgrid-mobile-services-send-email-scripts/
[Pianificazione di processi back-end in Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Come creare un account di archiviazione]: /it-it/documentation/articles/storage-create-storage-account/
[Libreria client di archiviazione di Azure per app di Windows Store]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
[Windows Phone SDK 8.0]: http://www.microsoft.com/it-it/download/details.aspx?id=35471




<!--HONumber=42-->
