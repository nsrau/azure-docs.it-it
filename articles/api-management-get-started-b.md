<properties 
	pageTitle="Introduzione a Gestione API di Azure" 
	description="Informazioni su come creare API e operazioni e su come iniziare a usare Gestione API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/11/2015" 
	ms.author="sdanie"/>

# Introduzione a Gestione API di Azure

Questa guida illustra le procedure per iniziare subito a usare Gestione API ed effettuare la prima chiamata API.

> Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla versione di valutazione gratuita di Azure[][].

Per poter usare Gestione API, occorre innanzitutto creare un'istanza del servizio. Accedere al [portale di gestione][] e fare clic su **Nuovo**, **Servizi app**, **Gestione API**, **Crea**.

![API Management new instance][api-management-create-instance-menu]

Nel campo **URL** specificare un nome di sottodominio univoco da usare per l'URL del servizio.

Selezionare i valori di **Sottoscrizione** e **Area** desiderati per l'istanza del servizio. Una volta effettuate le selezioni, fare clic sul pulsante Avanti.

![New API Management service][api-management-create-instance-step1]

Immettere **Contoso Ltd.** in **Nome organizzazione** e specificare l'indirizzo di posta elettronica nel campo relativo alla posta elettronica dell'amministratore.

>Questo indirizzo di posta elettronica viene usato per le notifiche inviate dal sistema Gestione API. Per altre informazioni, vedere [Configurare notifiche][]

![New API Management service][api-management-create-instance-step2]

Le istanze del servizio Gestione API sono disponibili in due livelli: Developer e Standard. Per impostazione predefinita, le nuove istanze del servizio Gestione API vengono create nel livello Developer. Per selezionare il livello Standard, selezionare la casella di controllo **Impostazioni avanzate** e scegliere Standard nella schermata seguente.

>Microsoft Azure offre due livelli in cui è possibile eseguire il servizio Gestione API: Developer e Standard. Il livello Developer è dedicato allo sviluppo, al test alla distribuzione pilota di programmi API in cui l'elevata disponibilità non è un fattore rilevante. Nel livello Standard è possibile aumentare il numero di unità riservate per gestire un maggior volume di traffico. Il livello Standard fornisce inoltre al servizio Gestione API una potenza di elaborazione e prestazioni maggiori. Per questa esercitazione è possibile scegliere entrambi i livelli. Per altre informazioni sui livelli di Gestione API, vedere [Gestione API - Prezzi][].

Selezionare la casella di controllo per creare l'istanza del servizio.

![New API Management service][api-management-instance-created]

Dopo aver creato l'istanza del servizio, l'operazione successiva consiste nel creare un'API.

## <a name="create-api"> </a>Creare un'API

Un'API rappresenta un set di operazioni che possono essere richiamate da un'applicazione client. Le operazioni API vengono trasmesse tramite proxy ai servizi Web esistenti.

Ogni istanza del servizio Gestione API è preconfigurata con un'API Echo di esempio su cui è possibile richiamare qualsiasi verbo HTTP e il valore restituito sarà corrispondente alle intestazioni e al corpo inviato. Questa esercitazione usa il servizio Web back-end per l'API Echo per la creazione di una nuova API denominata **My Echo Service**.

Le API vengono create e configurate dalla console di Gestione API, accessibile tramite il portale di gestione di Azure. Per passare alla console di Gestione API, fare clic su **Console di gestione** nel portale di Azure per il servizio Gestione API.

![New API Management console][api-management-management-console]

Per creare la **My Echo API**, fare clic su **API** nel menu **Gestione API** sulla sinistra e quindi scegliere **Aggiungi API**.

![Create API][api-management-create-api]

![Add new API][api-management-add-new-api]

Per configurare la nuova API, vengono usati i tre campi seguenti.

-	Digitare **My Echo API** nella casella di testo **Titolo API Web**. ****Questa casella consente di specificare un nome univoco e descrittivo per l'API, che viene visualizzato nel portale di gestione e nel portale per sviluppatori.
-	Digitare **http://echoapi.cloudapp.net/api** nella casella di testo **URL del servizio Web**. ****Il valore specificato in questa casella fa riferimento al servizio HTTP che implementa l'API. e corrisponde all'indirizzo a cui Gestione API inoltra le richieste.
-	Digitare **myecho** nella casella di testo **Suffisso URL API Web**. ****Il valore di questa casella viene aggiunto all'URL di base del servizio Gestione API. Le API condividono un URL di base comune al quale viene aggiunto un suffisso univoco che consente di distinguerle.
-	L'opzione **Schema URL API Web** determina il protocollo da usare per l'accesso all'API. Per impostazione predefinita è specificato il valore HTTPs.

Fare clic su **Salva** per creare l'API. Una volta creata la nuova API, la pagina di riepilogo dell'API viene visualizzata nel portale di gestione.

![API summary][api-management-new-api-summary]


>Nell'API Echo di esempio non viene usata l'autenticazione, ma per altre informazioni sulla configurazione dell'autenticazione, vedere [Configurare le impostazioni API][].


## <a name="add-operation"> </a>Aggiungere un'operazione

Fare clic su **Operazioni** per visualizzare il riquadro operazioni per l'API. Le definizioni delle operazioni vengono usate per convalidare le richieste in arrivo e generare automaticamente la documentazione. Il riquadro non contiene operazioni dal momento che non ne sono ancora state aggiunte.

![Operations][api-management-myecho-operations]

Fare clic su **Aggiungi operazione** per aggiungere una nuova operazione. Viene visualizzata la finestra **Nuova operazione ** in cui, per impostazione predefinita, è selezionata la scheda **Firma**.

![Operation signature][api-management-operation-signature]

In questo esempio verrà specificata un'operazione GET per il servizio Echo. Immettere i valori seguenti nei campi della scheda **Firma**.

-	Digitare **GET** nella casella di testo **Verbo HTTP**. Quando si inizia a digitare, è possibile selezionare **GET** nell'elenco di verbi HTTP visualizzato.
-	Digitare **/resource** nella casella di testo **Modello di URL**.
-	Digitare **GET resource** nella casella di testo **Nome visualizzato**.
-	Digitare **A demonstration of a GET call on a sample resource. It is handled by an "echo" backend which returns a response equal to the request (the supplied headers and body are being returned as received).** nella casella di testo **Descrizione**. Questa descrizione viene usata per generare la documentazione per questa operazione quando gli sviluppatori usano l'API.

Fare clic su **Parametri** per configurare i parametri di query di tipo stringa per l'operazione. Per aggiungere un parametro di query, fare clic su **Aggiungi parametro di query** e specificare i seguenti valori.

-	Digitare **param1** nella casella di testo **Nome**.
-	Digitare **A sample parameter that is required.** nella casella di testo **Descrizione**.
-	Fare clic nel campo **Tipo** e selezionare **string** nell'elenco. I tipi supportati sono **string**, **number**, **boolean** e **dateTime**.
-	Fare clic nel campo **Valori**, digitare **sample** nella casella di testo e fare clic sul segno più per aggiungere al parametro il testo del valore predefinito. Dopo aver aggiunto il testo predefinito, fare clic in un punto qualsiasi all'esterno del campo **Valori** per chiudere la finestra di aggiunta dei valori.
-	Selezionare la casella di controllo **Obbligatorio**.

Fare clic su **Salva** per aggiungere per aggiungere la nuova operazione configurata all'API.


## <a name="add-api-to-product"> </a>Aggiungere la nuova API a un prodotto

Prima di effettuare chiamate API gli sviluppatori devono effettuare la sottoscrizione di un prodotto. I prodotti offrono l'accesso a una o più API e possono prevedere restrizioni relative all'accesso, come quote di uso e limiti di frequenza. In questo passaggio dell'esercitazione l'API My Echo verrà aggiunta a un prodotto esistente.

Fare clic su **Prodotti** nel menu **Gestione API** sulla sinistra per visualizzare e configurare i prodotti disponibili in questa istanza dell'API.

![Products][api-management-list-products]

Per impostazione predefinita, con ogni istanza di Gestione API vengono forniti due prodotti di esempio:

-	**Starter**
-	**Senza limiti**

Questa esercitazione userà il prodotto **Starter**. Fare clic su **Starter** per visualizzare le impostazioni, incluse le API associate al prodotto.

![Add API][api-management-add-api-to-product]

Fare clic su **Aggiungi API al prodotto**.

![Add API][api-management-add-myechoapi-to-product]

Selezionare la casella **My Echo API** e fare clic su **Salva**.

![API added][api-management-api-added-to-product]

A questo punto, **My Echo API** è associata a un prodotto e gli sviluppatori possono effettuare la sottoscrizione per iniziare a usare l'API.

>Questo passaggio dell'esercitazione usa il prodotto **Starter**, che viene fornito preconfigurato e pronto per l'uso. Per una guida dettagliata sulla creazione e la pubblicazione di un nuovo prodotto, vedere [Come creare e pubblicare un prodotto][].

L'utente amministratore è automaticamente sottoscritto a tutti i prodotti e può accedere alle API a cui questi forniscono accesso. Non è quindi necessario sottoscrivere manualmente il prodotto appena creato prima che poter effettuare una chiamata.

## <a name="call-operation"> </a>Chiamare un'operazione dal portale per sviluppatori

È possibile chiamare le operazioni direttamente dal portale per sviluppatori, che consente di visualizzare e testare le operazioni di un'API in tutta comodità. In questo passaggio dell'esercitazione verrà chiamato il metodo Get aggiunto alla **My Echo API**. Fare clic su **Portale per sviluppatori** nel menu in alto a destra del portale di gestione.

![Developer portal][api-management-developer-portal-menu]

Fare clic su **API** nel menu superiore e quindi su **My Echo API** per visualizzare le operazioni disponibili.

![Developer portal][api-management-developer-portal-myecho-api]

Notare che vengono visualizzati la descrizione e i parametri aggiunti durante la creazione dell'operazione, che costituiscono la documentazione destinata agli sviluppatori che useranno l'operazione.

Fare clic su **GET Resource** e quindi su **Open Console**. 

![Operation console][api-management-developer-portal-myecho-api-console]

Immettere alcuni valori per i parametri, specificare la chiave sviluppatore e fare clic su **HTTP Get**.

€

Una volta richiamata un'operazione, nel portale per sviluppatori vengono visualizzati l'**URL richiesto** dal servizio di back-end, lo **stato**, le **intestazioni** ed eventualmente il **contenuto** della risposta. 

![Response][api-management-invoke-get-response]



## <a name="next-steps"> </a>Passaggi successivi

-   Configurare criteri
-   Personalizzare il portale per sviluppatori
-   Tenere traccia delle chiamate usando Controllo API

[Versione di valutazione gratuita di Azure]: http://azure.microsoft.com/pricing/free-trial/

[Creare un'istanza di Gestione API]: #create-service-instance
[Creare un'API]: #create-api
[Aggiungere un'operazione]: #add-operation
[Aggiungere la nuova API a un prodotto]: #add-api-to-product
[Sottoscrivere il prodotto che contiene l'API]: #subscribe
[Chiamare un'operazione dal portale per sviluppatori]: #call-operation
[Visualizzare l'analisi]: #view-analytics
[Passaggi successivi]: #next-steps

[Configurare le impostazioni API]: ../api-management-howto-create-apis/#configure-api-settings
[Configurare notifiche]: ../api-management-howto-configure-notifications
[Risposte]: ../api-management-howto-add-operations/#responses
[Come creare e pubblicare un prodotto]: ../api-management-howto-add-products
[Introduzione alla configurazione API avanzata]: ../api-management-get-started-advanced
[Gestione API - Prezzi]: http://azure.microsoft.com/pricing/details/api-management/
[Portale di gestione]: https://manage.windowsazure.com/

[Configurare criteri]: ../api-management-howto-policies
[Personalizzare il portale per sviluppatori]: ../api-management-customize-portal
[Tenere traccia delle chiamate usando Controllo API]: ../api-management-howto-api-inspector

[api-management-management-console]: ./media/api-management-get-started-b/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started-b/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started-b/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started-b/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started-b/api-management-instance-created.png
[api-management-create-api]: ./media/api-management-get-started-b/api-management-create-api.png
[api-management-add-new-api]: ./media/api-management-get-started-b/api-management-add-new-api.png
[api-management-new-api-summary]: ./media/api-management-get-started-b/api-management-new-api-summary.png
[api-management-myecho-operations]: ./media/api-management-get-started-b/api-management-myecho-operations.png
[api-management-operation-signature]: ./media/api-management-get-started-b/api-management-operation-signature.png
[api-management-list-products]: ./media/api-management-get-started-b/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started-b/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started-b/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started-b/api-management-api-added-to-product.png



[api-management-developer-portal-menu]: ./media/api-management-get-started-b/api-management-developer-portal-menu.png
[api-management-developer-portal-myecho-api]: ./media/api-management-get-started-b/api-management-developer-portal-myecho-api.png
[api-management-developer-portal-myecho-api-console]: ./media/api-management-get-started-b/api-management-developer-portal-myecho-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started-b/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started-b/api-management-invoke-get-response.png

<!--HONumber=35.2-->

<!--HONumber=46--> 
