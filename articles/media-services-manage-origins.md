<properties pageTitle="Come gestire le origini in un account di Servizi multimediali" description="" services="media-services" documentationCenter="" authors="juliako" writer="juliako" manager="dwrede" editor=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/29/2015" ms.author="juliako"/>


# <a id="managemediaservicesorigins"></a>Come gestire gli endpoint di streaming in un account di Servizi multimediali

Servizi multimediali consente di aggiungere più endpoint di streaming all'account e di configurare gli endpoint di streaming. Ogni account di Servizi multimediali ha associato almeno un endpoint di streaming **predefinito**.

>[AZURE.NOTE] Gli endpoint di streaming erano noti in passato come Origini. 


## Aggiunta ed eliminazione degli endpoint di streaming 

1. Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Servizi multimediali**. Fare quindi clic sul nome del servizio multimediale.
2. Selezionare la pagina ENDPOINT DI STREAMING. 
3. Fare clic sul pulsante AGGIUNGI o ELIMINA nella parte inferiore della pagina. Gli endpoint di streaming predefiniti non possono essere eliminati. 
4. Fare clic sul pulsante AVVIA per avviare l'endpoint di streaming. 
5. Fare clic sul nome dell'endpoint di streaming per configurarlo.   

	![Origin page][origin-page]

## Ridimensionare l'endpoint di Streaming

Le unità di streaming forniscono sia capacità in uscita dedicata che può essere acquistata in incrementi di 200 Mbps che  funzionalità aggiuntive che attualmente includono [funzionalità di creazione dinamica dei pacchetti](http://go.microsoft.com/fwlink/?LinkId=276874). Per impostazione predefinita, lo streaming on demand è configurato in un modello di istanza condivisa in base al quale le risorse del server (ad esempio, calcolo, capacità in uscita e così via) vengono condivise con tutti gli altri utenti. Per migliorare la velocità effettiva dello streaming on demand, si consiglia di acquistare unità di streaming. 

Per modificare il numero di unità di streaming, eseguire le operazioni seguenti:

1. Per specificare il numero di unità di streaming, selezionare la scheda RIDIMENSIONA e spostare il dispositivo di scorrimento **capacità riservata**.

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. Fare clic sul pulsante SALVA per salvare le modifiche apportate.

	L'allocazione di nuove unità di streaming richiede circa 20 minuti. 

	 
	>[AZURE.NOTE] Attualmente, se si riporta a zero qualsiasi valore positivo delle unità di streaming, è possibile che lo streaming on demand venga disabilitato per un periodo che può durare fino a un'ora.

	>[AZURE.NOTE] Il numero più alto di unità specificato in un periodo di 24 ore è quello che verrà usato per il calcolo del costo. Per informazioni sui prezzi, vedere [Dettagli prezzi di Servizi multimediali](http://go.microsoft.com/fwlink/?LinkId=275107).
	
## Configurazione dell'endpoint di streaming

La scheda CONFIGURA consente di eseguire le configurazioni, come illustrato nell'immagine. Segue la descrizione dei campi.

>[AZURE.NOTE] La configurazione in questa pagina si applica solo agli endpoint di streaming con almeno un'unità riservata. Per prenotare le unità riservate di streaming on demand.

![Configure origin][configure-origin]
  

1. Impostare il periodo di memorizzazione nella cache massimo che verrà specificato nell'intestazione del controllo cache delle risposte HTTP. Questo valore non eseguirà l'override delle impostazioni cache massime impostate direttamente sul contenuto BLOB.

2. Specificare gli indirizzi IP a cui è consentito connettersi all'endpoint di streaming pubblicato. Se non viene specificato alcun indirizzo IP, la connessione è consentita a qualsiasi indirizzo IP.

3. Specificare una configurazione per l'autenticazione dell'intestazione firma Akamai.

4. È possibile specificare un criterio di accesso tra domini per i client di Adobe Flash. Per altre informazioni, vedere la pagina [Cross-domain policy file specification](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html). È altresì possibile specificare criteri di accesso client per i client di Microsoft Silverlight. Per altre informazioni, vedere la pagina su [come rendere un servizio disponibile attraverso i limiti del dominio](https://msdn.microsoft.com/it-it/library/cc197955(v=vs.95).  

5. È anche possibile configurare nomi host personalizzati facendo clic sul pulsante **Configura**. Per altre informazioni, vedere la proprietà **CustomHostNames** nell'argomento [StreamingEndpoint](https://msdn.microsoft.com/it-it/library/dn783468.aspx).  




[origin-page]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png


<!--HONumber=42-->
