<properties pageTitle="Aggiungere paging ai dati (Windows Phone) | Mobile Developer Center" metaKeywords="" description="Informazioni su come usare il paging per gestire la quantità di dati restituiti all'app per Windows Phone da Servizi mobili." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="10/11/2014" ms.author="glenga" />

# Usare il paging per ridefinire le query di Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

Questo argomento illustra come usare il paging per gestire la quantità di dati restituiti all'app di Windows Phone da Servizi mobili di Azure. In questa esercitazione verranno usati i metodi di query **Take** e **Skip** sul client per richiedere "pagine" di dati specifiche.

>[WACOM.NOTE]Per evitare l'overflow dei dati nei client dei dispositivi mobili, Servizi mobili implementa un limite di pagina automatico, che prevede un massimo di 50 elementi in una risposta per impostazione predefinita. Specificando le dimensioni della pagina, è possibile richiedere in modo esplicito fino a un massimo di 1.000 elementi nella risposta.

Questa esercitazione è basata sulle procedure e sull'app di esempio dell'esercitazione precedente [Aggiungere Servizi mobili a un'app esistente](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/), che deve essere completata prima di iniziare la presente esercitazione: 

##Impostare la dimensione delle pagine del server

[WACOM.INCLUDE [mobile-services-windows-dotnet-paging](../includes/mobile-services-windows-dotnet-paging.md)]

## <a name="next-steps"> </a>Passaggi successivi

Il set di esercitazioni relative alle nozioni di base dell'uso dei dati in Servizi mobili è concluso. Per altre informazioni, vedere anche i seguenti argomenti su Servizi mobili:

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con un account Windows.

* [Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app.

<!-- Anchors. -->

[Passaggi successivi]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/


[Portale di gestione]: https://manage.windowsazure.com/
