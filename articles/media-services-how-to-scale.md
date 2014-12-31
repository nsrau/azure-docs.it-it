<properties urlDisplayName="How to scale" pageTitle="Come scalare un servizio multimediale | Documentazione di Azure" metaKeywords="" description="Learn how to scale Media Services by specifying the number of On-Demand Streaming Reserved Units and Encoding Reserved Units that you would like your account to be provisioned with." metaCanonical="" services="media-services" documentationCenter="" title="How to Scale a Media Service" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





#Come scalare un servizio multimediale  

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]


È possibile scalare servizi multimediali specificando il numero di **unità riservate di streaming on demand** e di **unità riservate di codifica** di cui si desidera eseguire il provisioning nell'account in uso. 


<h2>Unità riservate di streaming on demand</h2>

Le unità riservate di streaming on demand offrono sia capacità in uscita dedicata, acquistabile in incrementi di 200 Mbps, sia funzionalità aggiuntive che attualmente comprendono la [creazione dinamica dei pacchetti](http://go.microsoft.com/fwlink/?LinkId=276874). Per impostazione predefinita, lo streaming on demand è configurato in un modello di istanza condivisa in base al quale le risorse del server (ad esempio, calcolo, capacità in uscita e così via) vengono condivise con tutti gli altri utenti. Per migliorare la velocità effettiva dello streaming on demand, si consiglia di acquistare unità riservate di streaming on demand. 

Per modificare il numero di unità riservate di streaming on demand, eseguire le operazioni seguenti:

1. Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Servizi multimediali**. Fare quindi clic sul nome del servizio multimediale.

2. Selezionare la pagina ENDPOINT DI STREAMING. Quindi, fare clic sull'endpoint di streaming da modificare.


3. Per specificare il numero di unità di streaming, selezionare la scheda RIDIMENSIONA e spostare il dispositivo di scorrimento **capacità riservata**.

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. Fare clic sul pulsante SALVA per salvare le modifiche apportate.

	Il completamento dell'allocazione di nuove unità di streaming on demand richiede circa 20 minuti. 

	 
	>[Azure.Note] Attualmente, se si riporta a zero qualsiasi valore positivo delle unità di streaming on demand, è possibile che lo streaming on demand venga disattivato per un periodo che può durare fino a un'ora.

	>[Azure.Note] Il numero più alto di unità specificato in un periodo di 24 ore è quello che verrà usato per il calcolo del costo. Per informazioni sui prezzi, vedere [Dettagli prezzi di Servizi multimediali](http://go.microsoft.com/fwlink/?LinkId=275107).

<h2>Unità riservate di codifica</h2>

Il numero di unità riservate di codifica fornite corrisponde al numero di attività multimediali che è possibile elaborare simultaneamente in un determinato account. Se, ad esempio, il proprio account dispone di cinque unità riservate, è possibile eseguire simultaneamente cinque attività multimediali, purché siano presenti attività da elaborare. Le attività rimanenti verranno messe in coda e prelevate in sequenza per l'elaborazione non appena un'attività in esecuzione viene completata. Se per un account non sono state fornite unità riservate, le attività verranno prelevate in sequenza. In questo caso, il tempo di attesa tra il completamento di un'attività e l'avvio di quella successiva dipende dalle risorse disponibili nel sistema.

Per modificare il numero di unità riservate di codifica, eseguire le operazioni seguenti:

1. Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Servizi multimediali**. Fare quindi clic sul nome del servizio multimediale.

2. Selezionare la pagina CODIFICA. 
	
	In questa pagina è possibile scegliere tra tre diversi tipi di codifica delel unità riservate,  ovvero Di base, Standard e Premium (come illustrato di seguito).

	![Processors page](./media/media-services-how-to-scale/media-services-encoding-scale.png)

	Per cambiare il numero di unità riservate per il valore selezionato in TIPO DI UNITÀ RISERVATA, usare il dispositivo di scorrimento CODIFICA.  
	
	La principale differenza tra i tipi di unità riservate è data dalla velocità. Ad esempio, lo stesso processo di codifica viene eseguito più velocemente quando si usa il tipo di unità riservata Standard rispetto a quando si usa il tipo Di base. Per altre informazioni, vedere il blog sui tipi di unità riservata di codifica scritto da [Milan Gada](http://azure.microsoft.com/blog/author/milanga/).

	>[Azure.Note] I data center seguenti non offrono il tipo di unità riservata Premium: Singapore, Hong Kong, Osaka, Pechino, Shanghai.

3. Fare clic sul pulsante SALVA per salvare le modifiche apportate.

	Le nuove unità riservate di codifica vengono allocate non appena si fa clic su SALVA.

	>[Azure.Note] Il numero più alto di unità specificato in un periodo di 24 ore è quello che verrà usato per il calcolo del costo.

<h2>Apertura di un ticket di supporto</h2>


Per impostazione predefinita, ogni account di Media Services può includere fino a 25 unità di codifica riservate e cinque unità riservate di streaming on demand. È possibile richiedere l'applicazione di un limite superiore mediante l'apertura di un ticket di supporto.

Per aprire un ticket di supporto, eseguire le operazioni seguenti: 

1. Accedere all'account Azure nel [portale di gestione](http://manage.windowsazure.com).
2. Passare a [Supporto](http://www.windowsazure.com/it-it/support/contact/).
3. Fare clic su "Get Support".
4. Selezionare la propria sottoscrizione.
5. Per il tipo di supporto, selezionare "Technical".
6. Fare clic su "Create Ticket".
7. Selezionare "Azure Media Services" dall'elenco dei prodotti visualizzato nella pagina successiva.
8. Selezionare "Media Processing" come "Problem type", quindi selezionare "Reservation Units" come categoria.
9. Fare clic su Continue.
10. Attenersi alle istruzioni visualizzate nella pagina successiva, quindi specificare il numero di unità riservate di codifica o di streaming on demand necessarie.
11. Fare clic su Submit per aprire il ticket.





 





<!--HONumber=35_1-->
