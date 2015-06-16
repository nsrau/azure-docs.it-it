<properties 
	pageTitle="Come creare e usare gruppi per gestire account di sviluppatori in Gestione API di Azure" 
	description="Informazioni su come gestire gli account di sviluppatori tramite i gruppi in Gestione API di Azure." 
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
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# Come creare e usare gruppi per gestire account di sviluppatori in Gestione API di Azure

In Gestione API (Anteprima) i gruppi permettono di gestire la visibilità dei prodotti agli sviluppatori. I prodotti vengono innanzitutto resi visibili ai gruppi, quindi gli sviluppatori in quei gruppi possono visualizzare e sottoscriversi ai prodotti associati ai gruppi.

In Gestione API sono inclusi i gruppi predefiniti seguenti.

-   **Amministratori**: gli amministratori gestiscono le istanze del servizio Gestione API e creano le API, le operazioni e i prodotti usati dagli sviluppatori.
-   **Sviluppatori**: gli sviluppatori sono i clienti che compilano applicazioni usando le API. Agli amministratori viene concesso di accedere al portale per sviluppatori e di creare applicazioni che chiamano le operazioni di un'API.
-   **Guest**: gli utenti non autenticati, ad esempio i potenziali clienti, che visitano il portale per sviluppatori di un'istanza di Gestione API rientrano in questo gruppo. È possibile concedere agli utenti guest un determinato livello di accesso di sola lettura, ad esempio la possibilità di visualizzare le API ma non di chiamarle.

Oltre ai gruppi predefiniti, gli amministratori possono creare gruppi personalizzati. I gruppi personalizzati hanno gli stessi privilegi del gruppo sviluppatori predefinito e possono essere usati per gestire più gruppi di sviluppatori. Ad esempio, è possibile creare un gruppo personalizzato per gli sviluppatori che useranno le API da un prodotto e un altro gruppo che useranno le API da un prodotto diverso.

Questa guida illustra come gli amministratori di un'istanza di Gestione API possono aggiungere nuovi gruppi e associarli a prodotti e sviluppatori.

## Contenuto dell'argomento

-   [Creare un gruppo][Creare un gruppo]
-   [Associare un gruppo a un prodotto][Associare un gruppo a un prodotto]
-   [Associare gruppi a sviluppatori][Associare gruppi a sviluppatori]
-   [Passaggi successivi][Passaggi successivi]

## <a name="create-group"> </a>Creare un gruppo

Per creare un nuovo gruppo, fare clic sulla **Console di gestione** nel portale di Azure per il servizio Gestione API. Verrà visualizzato il portale amministrativo di Gestione API.

> Se non è stato creata un'istanza del servizio Gestione API, vedere [Creare un'istanza del servizio Gestione API][Creare un'istanza del servizio Gestione API] nell'esercitazione [Introduzione a Gestione API di Azure][Introduzione a Gestione API di Azure].

![API Management console][API Management console]

Fare clic su **Gruppi** dal menu **Gestione API** sulla sinistra, quindi scegliere **Aggiungi gruppo**.

![Aggiungere un nuovo gruppo][Aggiungere un nuovo gruppo]

Immettere un nome univoco per il gruppo e una descrizione facoltativa, quindi fare clic su **Salva**.

![Aggiungere un nuovo gruppo][1]

Il nuovo gruppo viene visualizzato nella scheda relativa ai gruppi. Per modificare il **Nome** o la **Descrizione** del gruppo, fare clic sul nome del gruppo nell'elenco. Per eliminare il gruppo, fare clic su **Elimina**.

![Gruppo aggiunto][Gruppo aggiunto]

Ora che il gruppo è stato creato, può essere associato a prodotti e sviluppatori.

## <a name="associate-group-product"> </a>Associare un gruppo a un prodotto

Per associare un gruppo a un prodotto, fare clic su **Prodotti** dal menu **Gestione API** a sinistra, quindi fare clic sul nome del prodotto desiderato.

![Visibilità dei prodotti][Visibilità dei prodotti]

Selezionare la scheda **Visibilità** per aggiungere e rimuovere gruppi e per visualizzare gli attuali gruppi associati al prodotto. Per aggiungere o rimuovere gruppi, selezionare o deselezionare le caselle di controllo per i gruppi desiderati, quindi fare clic su **Salva**.

![Visibilità dei prodotti][2]

> Per configurare gruppi dalla scheda **Visibilità** per un prodotto, fare clic su **Gestisci gruppi**.

Dopo che un prodotto è stato associato a un gruppo, gli sviluppatori in quel gruppo possono visualizzare il prodotto e sottoscriversi ad esso.

## <a name="associate-group-developer"> </a>Associare gruppi a sviluppatori

Per associare gruppi a sviluppatori, fare clic su **Sviluppatori** dal menu **Gestione API** a sinistra, quindi selezionare la casella di controllo a fianco degli sviluppatori da associare a un gruppo.

![Aggiungere uno sviluppatore a un gruppo][Aggiungere uno sviluppatore a un gruppo]

Dopo aver selezionato gli sviluppatori desiderati, fare clic sul gruppo desiderato nel menu a discesa **Aggiungi gruppo**. È possibile rimuovere sviluppatori dai gruppi usando l'elenco a discesa **Rimuovi da gruppo**.

![Sviluppatori][Sviluppatori]

Dopo l'aggiunta dell'associazione tra sviluppatore e gruppo, è possibile visualizzarla nella scheda **Sviluppatori**.

## <a name="next-steps"> </a>Passaggi successivi

Dopo che uno sviluppatore è stato associato a un gruppo, potrà visualizzare i prodotti associati a quel gruppo e sottoscriversi ad essi. Per altre informazioni, vedere [Come creare e pubblicare un prodotto in Gestione API di Azure][Come creare e pubblicare un prodotto in Gestione API di Azure].

  [Creare un gruppo]: #create-group
  [Associare un gruppo a un prodotto]: #associate-group-product
  [Associare gruppi a sviluppatori]: #associate-group-developer
  [Passaggi successivi]: #next-steps
  [Creare un'istanza del servizio Gestione API]: ../api-management-get-started/#create-service-instance
  [Introduzione a Gestione API di Azure]: ../api-management-get-started
  [API Management console]: ./media/api-management-howto-create-groups/api-management-management-console.png
  [Aggiungere un nuovo gruppo]: ./media/api-management-howto-create-groups/api-management-add-group.png
  [1]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
  [Gruppo aggiunto]: ./media/api-management-howto-create-groups/api-management-new-group.png
  [Visibilità dei prodotti]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
  [2]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
  [Aggiungere uno sviluppatore a un gruppo]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
  [Sviluppatori]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png
  [Come creare e pubblicare un prodotto in Gestione API di Azure]: ../api-management-howto-add-products

<!--HONumber=46--> 

<!--HONumber=46--> 
 