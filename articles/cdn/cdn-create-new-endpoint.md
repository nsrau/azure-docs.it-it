<properties
	 pageTitle="Uso della rete CDN di Azure"
	 description="Questo argomento illustra come abilitare la rete per la distribuzione di contenuti (CDN) per Azure. Questa esercitazione illustra la creazione di un nuovo profilo ed endpoint della rete CDN."
	 services="cdn"
	 documentationCenter=""
	 authors="camsoper"
	 manager="erikre"
	 editor=""/>
<tags
	 ms.service="cdn"
	 ms.workload="media"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="get-started-article"
	 ms.date="05/24/2016" 
	 ms.author="casoper"/>

# Uso della rete CDN di Azure  

Questo argomento descrive in dettaglio l'abilitazione della rete CDN di Azure creando un nuovo profilo di rete CDN e un endpoint.

>[AZURE.IMPORTANT] Per un'introduzione al funzionamento della rete CDN e per un elenco delle funzionalità, vedere [Panoramica della rete per la distribuzione di contenuti (rete CDN) di Azure](./cdn-overview.md).

## Creare un nuovo profilo di rete CDN

Un profilo di rete CDN è una raccolta di endpoint della rete CDN. Ogni profilo contiene uno o più endpoint della rete CDN. Si consiglia di usare più profili per organizzare gli endpoint della rete CDN tramite il dominio internet, l’applicazione web o altri criteri.

> [AZURE.NOTE] Per impostazione predefinita, ogni sottoscrizione di Azure è limitata a otto profili della rete CDN. Ogni profilo della rete CDN è limitato a dieci endpoint della rete CDN.
>
> I prezzi della rete CDN vengono applicati a livello di profilo della rete CDN. Se si vuole usare una combinazione di piani tariffari della rete CDN di Azure, è necessario avere più profili CDN.

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## Creare un nuovo endpoint della rete CDN

**Per creare nuovo endpoint della rete CDN**

1. Nel [portale di Azure](https://portal.azure.com) passare al profilo della rete CDN. Lo si potrebbe aver bloccato nel dashboard nel passaggio precedente. Se così non fosse, è possibile trovarlo cliccando su **Sfoglia**, quindi su **Profili di rete CDN** e facendo clic sul profilo in cui si prevede di aggiungere l'endpoint.

    Viene visualizzato il pannello del profilo di rete CDN.

    ![Profilo di rete CDN][cdn-profile-settings]

2. Fare clic sul pulsante **Aggiungi Endpoint**.

    ![Pulsante Aggiungi endpoint][cdn-new-endpoint-button]

    Viene visualizzato il pannello **Aggiungi un endpoint**.

    ![Pannello Aggiungi endpoint][cdn-add-endpoint]

3. Immettere un **Nome** per questo endpoint della rete CDN. Questo nome verrà usato per accedere alle risorse memorizzate nella cache nel dominio `<endpointname>.azureedge.net`.

4. Nell'elenco a discesa **Tipo origine** selezionare il tipo di origine. Selezionare **Archiviazione** per un account di archiviazione di Azure, **Servizio Cloud** per un servizio cloud di Azure, **App Web** per un'app Web di Azure oppure **Origine personalizzata** per qualsiasi altra origine di server Web accessibile pubblicamente, ospitata in Azure o altrove.

	![Tipo di origine della rete CDN](./media/cdn-create-new-endpoint/cdn-origin-type.png)
		
5. Nell'elenco a discesa **Nome host origine** selezionare o digitare il dominio di origine. Nell’elenco a discesa compariranno tutte le origini disponibili del tipo specificato nel passaggio 4. Se è stato selezionato l’elemento *Origine personalizzata* come **Tipo di origine**, si digiterà nel dominio di origine personalizzato.

6. Nella casella di testo **Percorso origine** inserire il percorso per le risorse che si desidera memorizzare nella cache oppure lasciare vuoto per consentire la memorizzazione nella cache di qualsiasi risorsa nel dominio specificato nel passaggio 5.

7. Nell’**Intestazione dell’host di origine**, inserire l'intestazione dell’host che si desidera che la rete CDN invii con ogni richiesta di immettere, o lasciare il valore predefinito.

	> [AZURE.WARNING] Per alcuni tipi di origini, ad esempio Archiviazione di Azure e App Web, è necessario che l'intestazione host corrisponda al dominio dell'origine. A meno che non si abbia un'origine che richiede un'intestazione host diversa dal dominio, è consigliabile lasciare il valore predefinito.

8. Per **Protocollo** e **Porta origine** specificare i protocolli e le porte usate per accedere alle risorse in corrispondenza dell'origine. È necessario selezionare almeno un protocollo (HTTP o HTTPS).
	
	> [AZURE.NOTE] **Porta dell'origine** interessa solo la porta usata dall'endpoint per recuperare informazioni dall'origine. L'endpoint stesso sarà disponibile solo per i client finali sulle porte HTTP e HTTPS (80 e 443), indipendentemente dalla **Porta dell'origine**.
	>
	> Gli endpoint del servizio **Rete CDN di Azure da Akamai** non consentono l'intera gamma di porte TCP per le origini. Per un elenco di porte di origine non consentite, vedere l'articolo relativo ai [dettagli sul comportamento della rete CDN di Azure di Akamai](cdn-akamai-behavior-details.md).
	>
	> L'accesso al contenuto della rete CDN tramite HTTPS presenta i vincoli seguenti:
	> 
	> - È necessario usare il certificato SSL fornito dalla rete CDN. I certificati di terze parti non sono supportati.
	> - È necessario usare il dominio fornito dalla rete CDN, `<endpointname>.azureedge.net`, per accedere al contenuto HTTPS. Il supporto HTTPS non è disponibile per i nomi di dominio personalizzati (CNAME) perché la rete CDN attualmente non supporta i certificati personalizzati.

9. Per creare il nuovo endpoint, fare clic sul pulsante **Aggiungi**.

10. Dopo la creazione, l'endpoint sarà visualizzato in un elenco di endpoint per il profilo. Nella visualizzazione elenco sono mostrati gli URL da usare per accedere a contenuti memorizzati nella cache, oltre al dominio di origine.

    ![Endpoint della rete CDN][cdn-endpoint-success]

    > [AZURE.IMPORTANT] L'endpoint non sarà disponibile immediatamente per l'uso, perché la propagazione della registrazione nella rete CDN richiede tempo. Per i profili del servizio <b>Rete CDN di Azure da Akamai</b> la propagazione in genere viene completata entro un minuto. Per i profili del servizio <b>Rete CDN di Azure da Verizon</b> la propagazione in genere viene completata entro 90 minuti, ma in alcuni casi può richiedere più tempo.
	>	 
	> Gli utenti che provano a usare il nome di dominio della rete CDN prima che la configurazione dell'endpoint sia stata propagata ai POP riceveranno codici di risposta HTTP 404. Se sono trascorse diverse ore da quando è stato creato l'endpoint e si ricevono ancora risposte 404, vedere l'articolo relativo alla [risoluzione dei problemi degli endpoint della rete CDN che restituiscono stati 404](cdn-troubleshoot-endpoint.md).


##Vedere anche
- [Controllo del comportamento di memorizzazione nella cache delle richieste della rete CDN con le stringhe di query](cdn-query-string.md)
- [Come eseguire il mapping del contenuto della rete CDN a un dominio personalizzato](cdn-map-content-to-custom-domain.md)
- [Precaricamento di risorse in un endpoint della rete CDN di Azure](cdn-preload-endpoint.md)
- [Ripulire un endpoint della rete CDN di Azure](cdn-purge-endpoint.md)
- [Risoluzione dei problemi degli endpoint della rete CDN che restituiscono stati 404](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png

<!---HONumber=AcomDC_0525_2016-->