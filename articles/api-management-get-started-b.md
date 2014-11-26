<properties pageTitle="Get started with Azure API Management" metaKeywords="" description="Learn how to create APIs, operations, and get started with API Management." metaCanonical="" services="" documentationCenter="API Management" title="Get started with Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

# Introduzione a Gestione API di Azure

Questa guida illustra le procedure per iniziare subito a usare Gestione API ed effettuare la prima chiamata API.

> Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][versione di valutazione gratuita di Azure].

Per poter usare Gestione API, occorre innanzitutto creare un'istanza del servizio. Accedere al [portale di gestione][portale di gestione] e fare clic su **Nuovo**, **Servizi app**, **Gestione API**, **Crea**.

![API Management new instance][API Management new instance]

Per **URL** specificare un nome univoco di sottodominio da usare per l'URL del servizio.

Specificare i valori desiderati di **Livello di prezzo**, **Sottoscrizione** e **Area** per l'istanza del servizio. Per questa esercitazione si possono usare tutti i livelli di prezzo. Una volta effettuate le selezioni, fare clic sul pulsante Avanti.

![New API Management service][New API Management service]

Immettere **Contoso Ltd.** in **Nome organizzazione** e specificare l'indirizzo di posta elettronica nel campo Indirizzo di posta elettronica dell'amministratore.

> Questo indirizzo di posta elettronica viene usato per le notifiche inviate dal sistema Gestione API. Per altre informazioni, vedere [Configurare le notifiche][Configurare le notifiche].

Selezionare la casella di controllo per creare l'istanza del servizio.

![New API Management service][1]

![New API Management service][2]

Dopo aver creato l'istanza del servizio, l'operazione successiva consiste nel creare un'API.

## <a name="create-api"> </a>Creare un'API

Un'API rappresenta un set di operazioni che possono essere richiamate da un'applicazione client. Le operazioni API vengono trasmesse tramite proxy ai servizi Web esistenti.

Ogni istanza del servizio Gestione API è preconfigurata con un'API Echo di esempio su cui è possibile richiamare qualsiasi verbo HTTP e il valore restituito sarà corrispondente alle intestazioni e al corpo inviato. In questa esercitazione viene usato il servizio Web back-end per l'API Echo per creare una nuova API denominata **My Echo Service**.

Le API vengono create e configurate dalla console di Gestione API, accessibile tramite il portale di gestione di Azure. Per passare alla console di Gestione API, fare clic su **Console di gestione** nel portale di Azure per il servizio Gestione API.

![New API Management console][New API Management console]

Per creare l'**API My Echo**, fare clic su **API** nel menu **Gestione API** sulla sinistra, quindi scegliere **Aggiungi API**.

![Create API][Create API]

![Add new API][Add new API]

Per configurare la nuova API, vengono usati i tre campi seguenti.

-	Digitare **My Echo API** nella casella di testo **Titolo API Web**. La casella **Titolo API Web** consente di specificare un nome univoco e descrittivo per l'API, che viene visualizzato nel portale di gestione e nel portale per sviluppatori.
-	Digitare **<http://echoapi.cloudapp.net/api>** nella casella di testo **URL del servizio Web**. Il valore specificato nella casella **URL del servizio Web** fa riferimento al servizio HTTP che implementa l'API e corrisponde all'indirizzo a cui Gestione API inoltra le richieste.
-   Digitare **myecho** nella casella di testo **Suffisso dell'URL dell'API Web**. Il valore della casella **Suffisso dell'URL dell'API Web** viene aggiunto all'URL di base del servizio Gestione API. Le API condividono un URL di base comune al quale viene aggiunto un suffisso univoco che consente di distinguerle.

Fare clic su **Salva** per creare l'API. Una volta creata la nuova API, la pagina di riepilogo dell'API viene visualizzata nel portale di gestione.

![API summary][API summary]


> Nell'API Echo di esempio non viene usata l'autenticazione, ma per altre informazioni sulla configurazione dell'autenticazione, vedere [Configurare le impostazioni dell'API][Configurare le impostazioni dell'API].


## <a name="add-operation"> </a>Aggiungere un'operazione

Fare clic su **Operazioni** per visualizzare il riquadro operazioni per l'API. Le definizioni delle operazioni vengono usate per convalidare le richieste in arrivo e generare automaticamente la documentazione. Il riquadro non contiene operazioni dal momento che non ne sono ancora state aggiunte.

![Operations][Operations]

Fare clic su **aggiungi operazione** per aggiungere una nuova operazione. Verrà visualizzata la finestra **Nuova operazione** in cui la scheda **Firma** è visualizzata per impostazione predefinita.

![Operation signature][Operation signature]

In questo esempio verrà specificata un'operazione GET per il servizio Echo. Immettere i valori seguenti nei campi della scheda **Firma**.

-	Digitare **GET** nella casella **Verbo HTTP**. Quando si inizia a digitare, è possibile selezionare **GET** nell'elenco di verbi HTTP visualizzato.
-	Digitare **/resource** nella casella di testo **Modello di URL**.
-	Digitare **GET resource** nella casella di testo **Nome visualizzato**.
-	Digitare **A demonstration of a GET call on a sample resource. It is handled by an "echo" backend which returns a response equal to the request (the supplied headers and body are being returned as received).** nella casella di testo **Descrizione**. Questa descrizione viene usata per generare la documentazione per questa operazione quando gli sviluppatori usano l'API.

Fare clic su **Parametri** per configurare i parametri di query di tipo stringa per l'operazione. Per aggiungere un parametro di query, fare clic su **Aggiungi parametro di query** e specificare i seguenti valori.

-	Digitare **param1** nella casella di testo **Nome**.
-	Digitare **A sample parameter that is required.** nella casella di testo **Descrizione**.
-	Fare clic nel campo **Tipo** e selezionare **string** nell'elenco. I tipi supportati sono **string**, **number**, **boolean** e **dateTime**.
-	Fare clic nel campo **Valori**, digitare **sample** nella casella di testo e fare clic sul segno più per aggiungere il testo del valore predefinito al parametro. Dopo aver aggiunto il testo predefinito, fare clic in un punto qualsiasi all'esterno del campo **Valori** per chiudere la finestra di aggiunta valori.
-   Selezionare la casella di controllo **Obbligatorio**.

Fare clic su **Salva** per aggiungere la nuova operazione configurata all'API.


## <a name="add-api-to-product"> </a>Aggiungere la nuova API a un prodotto

Prima di effettuare chiamate API gli sviluppatori devono effettuare la sottoscrizione di un prodotto. I prodotti offrono l'accesso a una o più API e possono prevedere restrizioni relative all'accesso, come quote di uso e limiti di velocità. In questo passaggio dell'esercitazione l'API My Echo verrà aggiunta a un prodotto esistente.

Fare clic su **Prodotti** nel menu **Gestione API** sulla sinistra per visualizzare e configurare i prodotti disponibili in questa istanza dell'API.

![Prodotti][Prodotti]

Per impostazione predefinita, con ogni istanza di Gestione API vengono forniti due prodotti di esempio:

-	**Starter**
-	**Senza limiti**

In questa esercitazione verrà usato il prodotto **Starter**. Fare clic su **Starter** per visualizzare le impostazioni, incluse le API associate al prodotto.

![Add API][Add API]

Fare clic su **Aggiungi API al prodotto**.

![Add API][3]

Selezionare la casella **API My Echo** e fare clic su **Salva**.

![API added][API added]

A questo punto, l'**API My Echo** è associata a un prodotto e gli sviluppatori possono effettuare la sottoscrizione per iniziare a usare l'API.

>In questo passaggio dell'esercitazione viene usato il prodotto **Starter**, che viene fornito preconfigurato e pronto per l'uso. Per una guida dettagliata sulla creazione e la pubblicazione di un nuovo prodotto, vedere [Come creare e pubblicare un prodotto][Come creare e pubblicare un prodotto].

L'utente amministratore è automaticamente sottoscritto a tutti i prodotti e può accedere alle API a cui questi forniscono accesso. Non è quindi necessario sottoscrivere manualmente il prodotto appena creato prima che poter effettuare una chiamata.

## <a name="call-operation"> </a>Chiamare un'operazione dal portale per sviluppatori

È possibile chiamare le operazioni direttamente dal portale per sviluppatori, che consente di visualizzare e testare le operazioni di un'API in tutta comodità. In questo passaggio dell'esercitazione verrà chiamato il metodo Get aggiunto all'**API My Echo**. Fare clic su **Portale per sviluppatori** nel menu in alto a destra del portale di gestione.

![Portale per sviluppatori][Portale per sviluppatori]

Fare clic su **API** nel menu superiore e quindi su **API My Echo** per visualizzare le operazioni disponibili.

![Portale per sviluppatori][4]

Notare che vengono visualizzati la descrizione e i parametri aggiunti durante la creazione dell'operazione, che costituiscono la documentazione destinata agli sviluppatori che useranno l'operazione.

Fare clic su **GET su risorsa** e quindi su **Apri console**.

![Operation console][Operation console]

Immettere alcuni valori per i parametri, specificare la chiave sviluppatore e fare clic su **GET HTTP**.

![HTTP Get][HTTP Get]

Una volta richiamata un'operazione, nel portale per sviluppatori vengono visualizzati l'**URL richiesto** restituito dal servizio back-end, lo **Stato della risposta**, le **Intestazioni della risposta** e l'eventuale **Contenuto della risposta**.

![Response][Response]



## <a name="next-steps"> </a>Passaggi successivi

-   Configurare criteri
-   Personalizzare il portale per sviluppatori
-   Tenere traccia delle chiamate usando Controllo API

  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/
  [portale di gestione]: https://manage.windowsazure.com/
  [API Management new instance]: ./media/api-management-get-started/api-management-create-instance-menu.png
  [New API Management service]: ./media/api-management-get-started/api-management-create-instance-step1.png
  [Configurare le notifiche]: ../api-management-howto-configure-notifications
  [1]: ./media/api-management-get-started/api-management-create-instance-step2.png
  [2]: ./media/api-management-get-started/api-management-instance-created.png
  [New API Management console]: ./media/api-management-get-started/api-management-management-console.png
  [Create API]: ./media/api-management-get-started/api-management-create-api.png
  [Add new API]: ./media/api-management-get-started/api-management-add-new-api.png
  [API summary]: ./media/api-management-get-started/api-management-new-api-summary.png
  [Configurare le impostazioni dell'API]: ../api-management-howto-create-apis/#configure-api-settings
  [Operations]: ./media/api-management-get-started/api-management-myecho-operations.png
  [Operation signature]: ./media/api-management-get-started/api-management-operation-signature.png
  [Prodotti]: ./media/api-management-get-started/api-management-list-products.png
  [Add API]: ./media/api-management-get-started/api-management-add-api-to-product.png
  [3]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
  [API added]: ./media/api-management-get-started/api-management-api-added-to-product.png
  [Come creare e pubblicare un prodotto]: ../api-management-howto-add-products
  [Portale per sviluppatori]: ./media/api-management-get-started/api-management-developer-portal-menu.png
  [4]: ./media/api-management-get-started/api-management-developer-portal-myecho-api.png
  [Operation console]: ./media/api-management-get-started/api-management-developer-portal-myecho-api-console.png
  [HTTP Get]: ./media/api-management-get-started/api-management-invoke-get.png
  [Response]: ./media/api-management-get-started/api-management-invoke-get-response.png
