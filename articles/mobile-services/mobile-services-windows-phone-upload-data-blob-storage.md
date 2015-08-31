<properties 
	pageTitle="Caricare immagini nell'archiviazione di Azure da un'app Windows Phone Silverlight | Microsoft Azure" 
	description="Informazioni su come usare Servizi mobili per caricare immagini dall'app Windows Phone Silverlight nell'archivio BLOB di Azure." 
	documentationCenter="windows" 
	authors="ggailey777" 
	services="mobile-services" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="glenga"/>

# Caricare immagini in Archiviazione di Azure mediante Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

##Panoramica

In questo argomento verrà illustrato come utilizzare Servizi mobili di Azure per consentire alla propria app di caricare e archiviare immagini generate dall'utente in Archiviazione di Azure. Servizi mobili utilizza un database SQL per archiviare i dati. I dati BLOB vengono tuttavia archiviati in modo più efficiente nel servizio di archiviazione BLOB di Azure.

Non è possibile distribuire in modo sicuro con l'app client le credenziali necessarie per caricare i dati nel servizio di archiviazione BLOB senza rischi. Queste credenziali devono invece essere archiviate nel servizio mobile ed essere utilizzate per generare una firma di accesso condiviso utilizzata per caricare una nuova immagine. La firma di accesso condiviso, ovvero una credenziale con una scadenza breve, in questo caso 5 minuti, viene restituita in modo sicuro da Servizi mobili all'app client. L'app usa quindi questa credenziale temporanea per caricare l'immagine. In questo esempio, i download del servizio BLOB sono pubblici.

Questa esercitazione consente di aggiungere funzionalità al progetto di app di esempio [GetStartedWithData](mobile-services-windows-phone-get-started-data.md) per scattare foto e caricare le immagini in Azure tramite una firma di accesso condiviso generata da Servizi mobili.


##Prerequisiti

Per completare questa esercitazione, è necessario disporre di:

+ Microsoft Visual Studio 2012 Express per Windows 8 o versione successiva
+ [Windows Phone SDK 8.0] o versione successiva
+ Programma per la gestione dei pacchetti Nuget installato per Microsoft Visual Studio.
+ [Account di archiviazione di Azure][How To Create a Storage Account]
+ Completare l'esercitazione [Aggiungere Servizi mobili a un'app esistente](mobile-services-windows-phone-get-started-data.md)  


##Installare il client di archiviazione per le app di Windows Phone

Per poter usare una firma di accesso condiviso per caricare immagini nel servizio di archiviazione BLOB, è necessario aggiungere innanzitutto il pacchetto NuGet che consente di installare la libreria client di archiviazione per le app di Windows Phone.

1. In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Manage NuGet Packages**.

2. Nel riquadro sinistro selezionare la categoria **Online**, selezionare **Include Prerelease**, cercare **WindowsAzure.Storage-Preview**, fare clic su **Installa** nel pacchetto di **Archiviazione di Azure**, quindi accettare i contratti di licenza.

  	![Aggiungere NuGet di Archiviazione di Azure](./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png)

  	La libreria client per i servizi di archiviazione di Azure verrà aggiunta al progetto.

A questo punto, aggiornare l'app di guida introduttiva in modo da acquisire e caricare le immagini.

##Aggiornare lo script insert registrato nel portale di gestione


[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]

>[AZURE.NOTE]Per aggiungere nuove proprietà all'oggetto TodoItem, è necessario che lo schema dinamico sia abilitato nel servizio mobile. Quando lo schema dinamico è abilitato, nella tabella TodoItem vengono automaticamente aggiunte nuove colonne mappate a queste nuove proprietà.

[AZURE.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]


##Passaggi successivi

Ora che le immagini sono state caricate in modo sicuro integrando il servizio mobile con il servizio BLOB, leggere altri argomenti sul servizio back-end e sull'integrazione:

+ [Inviare posta elettronica da Servizi mobili con SendGrid]
 
  Informazioni su come aggiungere funzionalità di posta elettronica a un servizio mobile tramite il servizio di posta elettronica SendGrid. Questo argomento illustra come aggiungere script sul lato server per inviare messaggi di posta elettronica tramite SendGrid.

+ [Pianificare processi back-end in Servizi mobili]

  Informazioni su come utilizzare la funzionalità di pianificazione di processi di Servizi mobili per definire codice dello script del server da eseguire in base a una pianificazione definita dall'utente.

##Vedere anche

+ [Riferimento per gli script del server di Servizi mobili]

  Argomenti di riferimento per l'utilizzo di script del server per eseguire operazioni sul lato server e per l'integrazione con altri componenti e risorse esterne di Azure.
 
+ [Riferimento per i concetti e le procedure di .NET per Servizi mobili]

  Ulteriori informazioni su come utilizzare Servizi mobili con .NET.
  
<!-- Images. -->

<!-- URLs. -->
[Inviare posta elettronica da Servizi mobili con SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Pianificare processi back-end in Servizi mobili]: mobile-services-schedule-recurring-tasks.md
[Riferimento per gli script del server di Servizi mobili]: mobile-services-how-to-use-server-scripts.md
[Get started with Mobile Services]: ../mobile-services-windows-phone-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Windows Phone SDK 8.0]: http://www.microsoft.com/download/details.aspx?id=35471


 

<!---HONumber=August15_HO8-->