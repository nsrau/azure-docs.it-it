<properties linkid="scripting-center-index" urlDisplayName="index" pageTitle="How to Manage Origins in a Media Services Account" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Manage Origins in a Media Services Account" authors="juliako"  solutions="" writer="juliako" manager="" editor=""  />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako"></tags>

# <span id="managemediaservicesorigins"></span></a>Come gestire le origini in un account di Servizi multimediali

Servizi multimediali consente di aggiungere più origini di streaming al proprio account e di configurare le origini. A ogni account di Servizi multimediali è associata almeno un'origine di streaming denominata **predefinita**.

## Aggiunta ed eliminazione di origini

1.  Nel [portale di gestione][] fare clic su **Media Services**. Fare quindi clic sul nome del servizio multimediale.
2.  Selezionare la pagina ORIGINS.
3.  Fare clic sul pulsante ADD o DELETE nella parte inferiore della pagina. Si noti che non è possibile eliminare l'origine predefinita.
4.  Fare clic sul pulsante START per avviare l'origine.
5.  Se si desidera configurare l'origine, fare clic sul nome dell'origine.

    ![Pagina relativa all'origine][]

## Configurazione dell'origine

La scheda CONFIGURE consente di eseguire le configurazioni seguenti:

1.  Impostare il periodo di memorizzazione nella cache massimo che verrà specificato nell'intestazione del controllo cache delle risposte HTTP. Questo valore non sovrascriverà le impostazioni cache massime impostate direttamente sul contenuto Blob.

2.  Specificare gli indirizzi IP a cui è consentito connettersi all'endpoint di streaming pubblicato. Se non viene specificato alcun indirizzo IP, la connessione è consentita a qualsiasi indirizzo IP.

3.  Specificare una configurazione per l'autenticazione dell'intestazione firma Akamai.

    ![Configurazione dell'origine][]

    La configurazione in questa pagina verrà applicata solo alle origini che hanno almeno un'unità riservata. Per riservare le unità riservate di streaming on demand, passare alla scheda SCALE. Per informazioni dettagliate sulle unità riservate, vedere l'argomento relativo alla [scalabilità di Servizi multimediali][].

  [portale di gestione]: https://manage.windowsazure.com/
  [Pagina relativa all'origine]: ./media/media-services-manage-origins/media-services-origins-page.png
  [Configurazione dell'origine]: ./media/media-services-manage-origins/media-services-origins-configure.png
  [scalabilità di Servizi multimediali]: http://go.microsoft.com/fwlink/?LinkID=275847&clcid=0x409/
