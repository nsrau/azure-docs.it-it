<properties linkid="manage-services-mediaservices-scale-media-service" urlDisplayName="How to scale" pageTitle="How to Scale a media service | Azure Documentation" metaKeywords="" description="Learn how to scale Media Services by specifying the number of On-Demand Streaming Reserved Units and Encoding Reserved Units that you would like your account to be provisioned with." metaCanonical="" services="media-services" documentationCenter="" title="How to Scale a Media Service" authors="migree" solutions="" manager="" editor="" />

Come scalare un servizio multimediale
=====================================

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

È possibile scalare servizi multimediali specificando il numero di **unità riservate di streaming on demand** e di **unità riservate di codifica** di cui si desidera eseguire il provisioning nell'account in uso.

Unità riservate di streaming on demand
--------------------------------------

Le unità riservate di streaming on demand offrono sia capacità in uscita dedicata, acquistabile in incrementi di 200 Mbps, sia funzionalità aggiuntive che attualmente comprendono la [creazione dinamica dei pacchetti](http://go.microsoft.com/fwlink/?LinkId=276874). Per impostazione predefinita, lo streaming on demand è configurato in un modello di istanza condivisa in base al quale le risorse del server (ad esempio, calcolo, capacità in uscita e così via) vengono condivise con tutti gli altri utenti. Per migliorare la velocità effettiva dello streaming on demand, si consiglia di acquistare unità riservate di streaming on demand.

Per modificare il numero di unità riservate di streaming on demand, eseguire le operazioni seguenti:

1.  Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Media Services**. Fare quindi clic sul nome del servizio multimediale.

2.  Selezionare la pagina ORIGINS. Quindi, fare clic sull'origine da modificare.

    ![Pagina relativa all'origine](./media/media-services-how-to-scale/media-services-origin-page.png)

3.  Per specificare il numero di unità riservate, selezionare la scheda SCALE e spostare il dispositivo di scorrimento **reserved capacity**.

    ![Pagina Scale](./media/media-services-how-to-scale/media-services-origin-scale.png)

4.  Fare clic sul pulsante SAVE per salvare le modifiche apportate.

    Il completamento dell'allocazione di nuove unità di streaming on demand richiede circa 20 minuti.

    **Nota:** attualmente, se si riporta a zero qualsiasi valore positivo delle unità di streaming on demand, è possibile che lo streaming on demand venga disattivato per un periodo che può durare fino a un'ora.

    **Nota:** il numero più alto di unità specificato in un periodo di 24 ore è quello che verrà utilizzato per il calcolo del costo. Per informazioni sui prezzi, vedere [Dettagli prezzi di Servizi multimediali](http://go.microsoft.com/fwlink/?LinkId=275107).

Unità riservate di codifica
---------------------------

Il numero di unità riservate di codifica fornite corrisponde al numero di attività multimediali che è possibile elaborare simultaneamente in un determinato account. Se, ad esempio, il proprio account dispone di cinque unità riservate, è possibile eseguire simultaneamente cinque attività multimediali, purché siano presenti attività da elaborare. Le attività rimanenti verranno messe in coda e prelevate in sequenza per l'elaborazione non appena un'attività in esecuzione viene completata. Se per un account non sono state fornite unità riservate, le attività verranno prelevate in sequenza. In questo caso, il tempo di attesa tra il completamento di un'attività e l'avvio di quella successiva dipende dalle risorse disponibili nel sistema.

Per modificare il numero di unità riservate di codifica, eseguire le operazioni seguenti:

1.  Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Media Services**. Fare quindi clic sul nome del servizio multimediale.

2.  Selezionare la pagina PROCESSORS.

    ![Pagina relativa ai processori](./media/media-services-how-to-scale/media-services-encoding-scale.png)

3.  Fare clic sul pulsante SAVE per salvare le modifiche apportate.

    Le nuove unità riservate di codifica vengono allocate quasi immediatamente.

    **Nota:** il numero più alto di unità specificato in un periodo di 24 ore è quello che verrà utilizzato per il calcolo del costo.

Apertura di un ticket di supporto
---------------------------------

Per impostazione predefinita, ogni account di Media Services può includere fino a 25 unità di codifica riservate e cinque unità riservate di streaming on demand. È possibile richiedere l'applicazione di un limite superiore mediante l'apertura di un ticket di supporto.

Per aprire un ticket di supporto, eseguire le operazioni seguenti:

1.  Accedere all'account Azure nel [portale di gestione](http://manage.windowsazure.com).
2.  Passare a [Support](http://www.windowsazure.com/it-it/support/contact/).
3.  Fare clic su "Get Support".
4.  Selezionare la propria sottoscrizione.
5.  Per il tipo di supporto, selezionare "Technical".
6.  Fare clic su "Create Ticket".
7.  Selezionare "Azure Media Services" dall'elenco dei prodotti visualizzato nella pagina successiva.
8.  Selezionare "Media Processing" come "Problem type", quindi selezionare "Reservation Units" come categoria.
9.  Fare clic su Continue.
10. Attenersi alle istruzioni visualizzate nella pagina successiva, quindi specificare il numero di unità riservate di codifica o di streaming on demand necessarie.
11. Fare clic su Submit per aprire il ticket.

