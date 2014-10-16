<properties pageTitle="Use Mobile Services to upload images to blob storage (Windows Store) | Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage." metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" writer="glenga" services="mobile-services, storage" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Caricamento di immagini in Archiviazione di Azure utilizzando Servizi mobili

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage" title="Windows Store C#" class="current">Windows Store C#</a><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage" title="Windows Phone">Windows Phone</a></div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage" title=".NET backend" class="current">Back-end .NET</a> | <a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage"  title="JavaScript backend">Back-end JavaScript</a></div>

In questo argomento verrà illustrato come utilizzare Servizi mobili di Azure per consentire alla propria app di caricare e archiviare immagini generate dall'utente in Archiviazione di Azure. Servizi mobili utilizza un database SQL per archiviare i dati. I dati BLOB vengono tuttavia archiviati in modo più efficiente nel servizio di archiviazione BLOB di Azure.

Non è possibile distribuire in modo sicuro con l'app client le credenziali necessarie per caricare i dati nel servizio di archiviazione BLOB senza rischi. Queste credenziali devono invece essere archiviate nel servizio mobile ed essere utilizzate per generare una firma di accesso condiviso utilizzata per caricare una nuova immagine. La firma di accesso condiviso, ovvero una credenziale con una scadenza breve, in questo caso 5 minuti, viene restituita in modo sicuro da Servizi mobili all'app client. L'app utilizza quindi questa credenziale temporanea per caricare l'immagine. In questo esempio, i download del servizio BLOB sono pubblici.

In questa esercitazione verranno aggiunte funzionalità all'app di guida introduttiva per Servizi mobili per poter scattare foto e caricare le immagini in Azure tramite una firma di accesso condiviso generata da Servizi mobili. L'esercitazione fornisce istruzioni dettagliate sulle operazioni di base per aggiornare il progetto di guida introduttiva per Servizi mobili in modo da caricare immagini nel servizio di archiviazione BLOB:

1.  [Installazione della libreria client di archiviazione][]
2.  [Aggiornamento dell'app client per l'acquisizione di immagini][]
3.  [Installare il client di archiviazione nel progetto di servizio mobile][]
4.  [Aggiornare la definizione di TodoItem nel modello di dati][]
5.  [Aggiornamento del controller tabella per generare una firma di accesso condiviso][]
6.  [Caricamento delle immagini per verificare l'app][]

Per completare questa esercitazione, è necessario disporre di:

-   Microsoft Visual Studio 2013 o versione successiva.
-   Programma per la gestione dei pacchetti Nuget installato per Microsoft Visual Studio.
-   [Account di archiviazione di Azure][]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili][].

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage][]]

## <a name="install-storage-client"></a>Installazione del client di archiviazione per le app di Windows Store

Per poter usare una firma di accesso condiviso allo scopo di caricare immagini dall'app nella risorsa di archiviazione BLOB, è necessario prima aggiungere il pacchetto NuGet che installa la libreria client di archiviazione per le app Windows Store.

1.  In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Gestisci pacchetti NuGet**.

2.  Nel riquadro sinistro selezionare la categoria **Online**, selezionare **Include Prerelease**, cercare **WindowsAzure.Storage-Preview**, fare clic su **Installa** nel pacchetto di **Archiviazione di Azure**, quindi accettare i contratti di licenza.

    ![][]

    La libreria client per i servizi di archiviazione di Azure verrà aggiunta al progetto.

A questo punto, aggiornare l'app di guida introduttiva in modo da acquisire e caricare le immagini.

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage][]]

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows Store C\#]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage "Windows Store C#"
  [Windows Phone]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage "Windows Phone"
  [Back-end .NET]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage ".NET backend"
  [Back-end JavaScript]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage "JavaScript backend"
  [Installazione della libreria client di archiviazione]: #install-storage-client
  [Aggiornamento dell'app client per l'acquisizione di immagini]: #add-select-images
  [Installare il client di archiviazione nel progetto di servizio mobile]: #storage-client-server
  [Aggiornare la definizione di TodoItem nel modello di dati]: #update-data-model
  [Aggiornamento del controller tabella per generare una firma di accesso condiviso]: #update-scripts
  [Caricamento delle immagini per verificare l'app]: #test
  [Account di archiviazione di Azure]: /it-it/documentation/articles/storage-create-storage-account/
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started
  [mobile-services-dotnet-backend-configure-blob-storage]: ../includes/mobile-services-dotnet-backend-configure-blob-storage.md
  []: ./media/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png
  [mobile-services-windows-store-dotnet-upload-to-blob-storage]: ../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md
