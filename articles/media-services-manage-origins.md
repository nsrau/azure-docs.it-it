<properties urlDisplayName="index" pageTitle="Come gestire le origini in un account di Servizi multimediali" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Come gestire gli endpoint di streaming in un account di Servizi multimediali" authors="juliako"  solutions="" writer="juliako" manager="dwrede" editor=""  />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# <span id="managemediaservicesorigins"></span></a>Come gestire gli endpoint di streaming in un account di Servizi multimediali

Servizi multimediali consente di aggiungere più endpoint di streaming all'account e di configurare gli endpoint di streaming. Ogni account di Servizi multimediali ha associato almeno un endpoint di streaming **predefinito**.

> [WACOM.NOTE] Gli endpoint di streaming era noti in passato come Origini.

## Aggiunta ed eliminazione degli endpoint di streaming

1.  Nel [portale di gestione][portale di gestione] fare clic su **Servizi multimediali**. Fare quindi clic sul nome del servizio multimediale.
2.  Selezionare la pagina ENDPOINT DI STREAMING.
3.  Fare clic sul pulsante AGGIUNGI o ELIMINA nella parte inferiore della pagina. Gli endpoint di streaming predefiniti non possono essere eliminati.
4.  Fare clic sul pulsante AVVIA per avviare l'endpoint di streaming.
5.  Fare clic sul nome dell'endpoint di streaming per configurarlo.

    ![Origin page][Origin page]

## Configurazione dell'endpoint di streaming

La scheda CONFIGURA consente di eseguire le configurazioni seguenti:

1.  Impostare il periodo di memorizzazione nella cache massimo che verrà specificato nell'intestazione del controllo cache delle risposte HTTP. Questo valore non sovrascriverà le impostazioni cache massime impostate direttamente sul contenuto BLOB.

2.  Specificare gli indirizzi IP a cui è consentito connettersi all'endpoint di streaming pubblicato. Se non viene specificato alcun indirizzo IP, la connessione è consentita a qualsiasi indirizzo IP.

3.  Specificare una configurazione per l'autenticazione dell'intestazione firma Akamai.

    ![Configure origin][Configure origin]

    La configurazione in questa pagina si applica solo agli endpoint di streaming con almeno un'unità riservata. Per riservare le unità riservate di streaming on demand, passare alla scheda SCALA. Per informazioni dettagliate sulle unità riservate, vedere l'argomento relativo alla [scalabilità di Servizi multimediali][scalabilità di Servizi multimediali].

  [portale di gestione]: https://manage.windowsazure.com/
  [Origin page]: ./media/media-services-manage-origins/media-services-origins-page.png
  [Configure origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
  [scalabilità di Servizi multimediali]: ../media-services-how-to-scale/
