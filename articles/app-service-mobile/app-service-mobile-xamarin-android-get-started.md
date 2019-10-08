---
title: Introduzione alle app per dispositivi mobili di Azure per Xamarin.Android
description: Seguire questa esercitazione per iniziare a usare le app per dispositivi mobili di Azure per lo sviluppo per Xamarin Android.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: a94b302ad813094cb4ce67e4bffc3dd43586366b
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027051"
---
# <a name="create-a-xamarinandroid-app"></a>Creare un'app per Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center supporta end-to-end e servizi integrati centrali per lo sviluppo di app per dispositivi mobili. Gli sviluppatori possono utilizzare i servizi di **compilazione**, **test** e **distribuzione** per configurare la pipeline di integrazione e recapito continua. Una volta distribuita l'app, gli sviluppatori possono monitorare lo stato e l'utilizzo dell'app usando i servizi di **analisi** e **diagnostica** e coinvolgere gli utenti che usano il servizio di **push** . Gli sviluppatori possono inoltre sfruttare l' **autenticazione** per autenticare gli utenti e il servizio **dati** per salvare in modo permanente e sincronizzare i dati delle app nel cloud.
> Se si intende integrare servizi cloud nell'applicazione per dispositivi mobili, iscriversi con App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) oggi stesso.

## <a name="overview"></a>Panoramica
Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app Xamarin.Android. Per altre informazioni, vedere [Informazioni sulle app per dispositivi mobili](app-service-mobile-value-prop.md).

Di seguito è riportata una schermata dell'app completata:

![][0]

Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni relative alle app per dispositivi mobili per Xamarin.Android.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile iscriversi alla versione di valutazione di Azure e ottenere fino a un massimo di 10 app per dispositivi mobili gratuite. Per informazioni dettagliate, vedere [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio con Xamarin. Per le istruzioni vedere [Configurazione e installazione di Visual Studio e Xamarin](/visualstudio/cross-platform/setup-and-install) .

## <a name="create-an-azure-mobile-app-backend"></a>Creare un back-end dell'app per dispositivi mobili di Azure
Seguire questa procedura per creare un back-end dell'app per dispositivi mobili.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

È stato eseguito il provisioning di un back-end dell'app per dispositivi mobili di Azure che può essere usato dalle applicazioni client per dispositivi mobili. Scaricare quindi un progetto server per un semplice back-end "todo list" e pubblicarlo in Azure.

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Creare una connessione al database e configurare il progetto client e server
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>Eseguire l'app Novell. Android
1. Aprire il progetto Novell. Android.

2. Passare alla [portale di Azure](https://portal.azure.com/) e passare all'app per dispositivi mobili creata. Nel pannello `Overview` cercare l'URL che rappresenta l'endpoint pubblico per l'app per dispositivi mobili. Esempio: il SiteName per il nome dell'app "test123" sarà https://test123.azurewebsites.net.

3. Aprire il file `ToDoActivity.cs` in questa cartella-Novell. Android/ZUMOAPPNAME/ToDoActivity. cs. Il nome dell'applicazione è `ZUMOAPPNAME`.

4. Nella classe `ToDoActivity` sostituire la variabile `ZUMOAPPURL` con l'endpoint pubblico riportato sopra.

    `const string applicationURL = @"ZUMOAPPURL";`

    diventa
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Premere il tasto F5 per distribuire ed eseguire l'app.

6. Nell'app digitare un testo significativo, ad esempio *Complete the tutorial* quindi fare clic sul pulsante **Add**.

    ![][10]

    I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal back-end per app per dispositivi mobili e i dati vengono visualizzati nell'elenco.

   > [!NOTE]
   > È possibile esaminare il codice che accede al back-end per app mobili per eseguire una query e inserire i dati trovati nel file C# ToDoActivity.cs.
   
## <a name="troubleshooting"></a>Risoluzione dei problemi
In caso di problemi di compilazione della soluzione, eseguire Gestione pacchetti NuGet ed effettuare l'aggiornamento dei pacchetti di supporto `Xamarin.Android`. I progetti di avvio rapido potrebbero non includere sempre le versioni più recenti.

Si noti che tutti i pacchetti di supporto a cui fa riferimento il progetto devono avere la stessa versione. Il [pacchetto Azure Mobile Apps NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) presenta la dipendenza `Xamarin.Android.Support.CustomTabs` per la piattaforma Android, perciò se il progetto usa pacchetti di supporto più recenti è necessario installare questo pacchetto con la versione richiesta direttamente per evitare conflitti.

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png
<!-- URLs. -->
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
