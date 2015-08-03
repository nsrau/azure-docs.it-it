<properties 
	pageTitle="Caricare immagini nell'archiviazione Blob di Azure da un'app Windows universale | Servizi mobili di Azure" 
	description="Informazioni su come utilizzare un servizio mobile back-end di .NET per caricare immagini in archiviazione Blob di Azure e accedere alle immagini dalla propria app Windows universale." 
	documentationCenter="windows" 
	authors="ggailey777" 
	services="mobile-services,storage" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="glenga"/>

# Caricare immagini in Archiviazione di Azure mediante Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

##Panoramica
In questo argomento verrà illustrato come utilizzare Servizi mobili di Azure per consentire alla propria app di caricare e archiviare immagini generate dall'utente in Archiviazione di Azure. Servizi mobili utilizza un database SQL per archiviare i dati. I dati BLOB vengono tuttavia archiviati in modo più efficiente nel servizio di archiviazione BLOB di Azure.

Non è possibile distribuire in modo sicuro con l'app client le credenziali necessarie per caricare i dati nel servizio di archiviazione BLOB senza rischi. Queste credenziali devono invece essere archiviate nel servizio mobile ed essere utilizzate per generare una firma di accesso condiviso utilizzata per caricare una nuova immagine. La firma di accesso condiviso, ovvero una credenziale con una scadenza breve, in questo caso 5 minuti, viene restituita in modo sicuro da Servizi mobili all'app client. L'app usa quindi questa credenziale temporanea per caricare l'immagine. In questo esempio, i download del servizio BLOB sono pubblici.

In questa esercitazione verranno aggiunte funzionalità all'app di guida introduttiva per Servizi mobili per poter scattare foto e caricare le immagini in Azure tramite una firma di accesso condiviso generata da Servizi mobili.

##Prerequisiti

Per completare questa esercitazione, è necessario disporre di:

+ Microsoft Visual Studio 2013 Update 3 o versione successiva.
+ [Account di archiviazione di Azure](../storage-create-storage-account.md)
+ Una fotocamera o un altro dispositivo per l'acquisizione di immagini collegato al computer.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili].

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

[AZURE.INCLUDE [Mobile-Services-Windows-Universal-dotnet-upload-to-BLOB-Storage](../../includes/mobile-services-windows-universal-dotnet-upload-to-blob-storage.md)]

##Passaggi successivi

Ora che le immagini sono state caricate in modo sicuro integrando il servizio mobile con il servizio BLOB, leggere altri argomenti sul servizio back-end e sull'integrazione:

+ [Pianificare processi back-end in Servizi mobili](../mobile-services-dotnet-backend-schedule-recurring-tasks.md)

     Informazioni su come utilizzare la funzionalità di pianificazione di processi di Servizi mobili per definire codice dello script del server da eseguire in base a una pianificazione definita dall'utente.

+ [Riferimento per i concetti e le procedure di .NET per Servizi mobili](../mobile-services-windows-dotnet-how-to-use-client-library.md)

     Ulteriori informazioni su come utilizzare Servizi mobili con .NET.
 
<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Install the storage client in the mobile service project]: #storage-client-server
[Update the TodoItem definition in the data model]: #update-data-model
[Update the table controller to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Introduzione a Servizi mobili]: ../mobile-services-windows-store-dotnet-get-started.md
[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
 

<!---HONumber=July15_HO4-->