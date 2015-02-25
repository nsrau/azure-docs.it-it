<properties pageTitle="Uso di Servizi mobili per caricare immagini nell'archiviazione BLOB (Windows Store) | Servizi mobili" description="Informazioni su come usare Servizi mobili per caricare immagini nel servizio di archiviazione BLOB di Azure e accedere alle immagini dall'app per Windows Store." services="mobile-services, storage" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/26/2014" ms.author="glenga"/>

# Caricare immagini in Archiviazione di Azure usando Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

Questo argomento descrive come usare Servizi mobili di Azure per consentire alla propria app di caricare e archiviare immagini generate dall'utente in Archiviazione di Azure. Servizi mobili usa un database SQL per archiviare i dati. I dati BLOB vengono tuttavia archiviati in modo più efficiente nel servizio di archiviazione BLOB di Azure. 

Non è possibile distribuire in modo sicuro con l'app client le credenziali necessarie per caricare i dati nel servizio di archiviazione BLOB senza rischi. Queste credenziali devono invece essere archiviate nel servizio mobile ed essere usate per generare una firma di accesso condiviso usata per caricare una nuova immagine. La firma di accesso condiviso, ovvero una credenziale con una scadenza breve, in questo caso 5 minuti, viene restituita in modo sicuro da Servizi mobili all'app client. L'app usa quindi questa credenziale temporanea per caricare l'immagine. In questo esempio, i download del servizio BLOB sono pubblici.

In questa esercitazione verranno aggiunte funzionalità all'app di guida introduttiva per Servizi mobili per poter scattare foto e caricare le immagini in Azure tramite una firma di accesso condiviso generata da Servizi mobili. L'esercitazione fornisce istruzioni dettagliate sulle operazioni di base per aggiornare il progetto di guida introduttiva per Servizi mobili in modo da caricare immagini nel servizio di archiviazione BLOB:

1. [Installazione della libreria client di archiviazione]
2. [Aggiornamento dello script insert per generare una firma di accesso condiviso]
3. [Aggiornamento dell'app client per l'acquisizione di immagini]
4. [Caricamento di immagini per testare l'app]

Per completare questa esercitazione, è necessario soddisfare i seguenti requisiti:

+ Microsoft Visual Studio 2012 Express per Windows 8 o versione successiva
+ [Account di archiviazione di Azure][Come creare un account di archiviazione]
+ Una fotocamera o un altro dispositivo per l'acquisizione di immagini collegato al computer.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili]. 

##<a name="install-storage-client"></a>Installare il client di archiviazione per le app di Windows Store

Per poter usare una firma di accesso condiviso per caricare immagini nel servizio di archiviazione BLOB, è necessario aggiungere innanzitutto il pacchetto NuGet che consente di installare la libreria client di archiviazione per le app di Windows Store.

1. In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Gestisci pacchetti NuGet**.

2. Nel riquadro sinistro selezionare la categoria **Online**, cercare `WindowsAzure.Storage`, fare clic su **Installa** nel pacchetto di **Archiviazione di Azure**, quindi accettare i contratti di licenza. 

  	![][2]

  	La libreria client per i servizi di archiviazione di Azure verrà aggiunta al progetto.

A questo punto, aggiornare l'app di guida introduttiva in modo da acquisire e caricare le immagini.

##<a name="update-scripts"></a>Aggiornare gli script insert registrati nel portale di gestione

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../includes/mobile-services-configure-blob-storage.md)]

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

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

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[Invio di posta elettronica da Servizi mobili con SendGrid]: /it-it/develop/mobile/tutorials/send-email-with-sendgrid/
[Pianificazione di processi back-end in Servizi mobili]: /it-it/documentation/articles/mobile-services-schedule-recurring-tasks
[Invio di notifiche push alle app di Windows Store tramite il bus di servizio da un back-end .NET]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-store-get-started

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Come creare un account di archiviazione]: /it-it/manage/services/storage/how-to-create-a-storage-account
[Libreria client di archiviazione di Azure per app di Windows Store]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library
[Impostazioni app]: http://msdn.microsoft.com/it-it/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7


<!--HONumber=42-->
