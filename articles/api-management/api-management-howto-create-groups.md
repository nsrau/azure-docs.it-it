---
title: Gestire gli account di sviluppatori tramite i gruppi in Gestione API di Azure | Documentazione Microsoft
description: Informazioni su come gestire gli account di sviluppatori tramite i gruppi in Gestione API di Azure.
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: 1587243bcd5f2b9af98b8b529c152ba49ef676be
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Come creare e usare gruppi per gestire account di sviluppatori in Gestione API di Azure
In Gestione API i gruppi permettono di gestire quali prodotti sono visibili per gli sviluppatori. I prodotti vengono innanzitutto resi visibili ai gruppi, quindi gli sviluppatori in quei gruppi possono visualizzare e sottoscriversi ai prodotti associati ai gruppi. 

In Gestione API sono inclusi i gruppi di sistema non modificabili seguenti.

* **Amministratori** : gli amministratori delle sottoscrizioni di Azure sono membri di questo gruppo. Gli amministratori gestiscono le istanze del servizio Gestione API e creano le API, le operazioni e i prodotti usati dagli sviluppatori.
* **Sviluppatori** : gli utenti autenticati del portale per sviluppatori rientrano in questo gruppo. Gli sviluppatori sono i clienti che compilano applicazioni usando le API. Agli sviluppatori viene concesso di accedere al portale per sviluppatori e di creare applicazioni che chiamano le operazioni di un'API.
* **Guest** : gli utenti non autenticati del portale per sviluppatori, ad esempio i potenziali clienti, che visitano il portale per sviluppatori di un'istanza di Gestione API rientrano in questo gruppo. È possibile concedere agli utenti guest un determinato livello di accesso di sola lettura, ad esempio la possibilità di visualizzare le API ma non di chiamarle.

Oltre a questi gruppi di sistema, gli amministratori possono creare gruppi personalizzati oppure [sfruttare i gruppi esterni nei tenant di Azure Active Directory associati][leverage external groups in associated Azure Active Directory tenants]. È possibile usare gruppi personalizzati ed esterni assieme ai gruppi di sistema per offrire agli sviluppatori visibilità e accesso ai prodotti API. Ad esempio, si può creare un gruppo personalizzato per gli sviluppatori affiliati a un'organizzazione partner specifica e consentire loro di accedere alle API da un prodotto che contiene solo le API pertinenti. Un utente può essere un membro di più gruppi.

Questa guida illustra come gli amministratori di un'istanza di Gestione API possono aggiungere nuovi gruppi e associarli a prodotti e sviluppatori.

> [!NOTE]
> Oltre a creare e gestire gruppi nel portale di pubblicazione, è possibile creare e gestire i gruppi utilizzando l'entità API REST di gestione [Gruppo](https://msdn.microsoft.com/library/azure/dn776329.aspx) .
> 
> 

## <a name="create-group"> </a>Creare un gruppo
Per creare un nuovo gruppo, fare clic su **Portale di pubblicazione** nel portale di Azure per il servizio Gestione API. Verrà visualizzato il portale di pubblicazione di Gestione API.

![Portale di pubblicazione][api-management-management-console]

> Se non è ancora stata creata un'istanza del servizio Gestione API, vedere [Creare un'istanza di Gestione API][Create an API Management service instance].
> 
> 

Fare clic su **Gruppi** dal menu **Gestione API** sulla sinistra, quindi scegliere **Aggiungi gruppo**.

![Aggiungere un nuovo gruppo][api-management-add-group]

Immettere un nome univoco per il gruppo e una descrizione facoltativa, quindi fare clic su **Salva**.

![Aggiungere un nuovo gruppo][api-management-add-group-window]

Il nuovo gruppo viene visualizzato nella scheda relativa ai gruppi. Per modificare il **Nome** o la **Descrizione** del gruppo, fare clic sul nome del gruppo nell'elenco. Per eliminare il gruppo, fare clic su **Elimina**.

![Gruppo aggiunto][api-management-new-group]

Ora che il gruppo è stato creato, può essere associato a prodotti e sviluppatori.

## <a name="associate-group-product"></a>Associare un gruppo a un prodotto
Per associare un gruppo a un prodotto, fare clic su **Prodotti** dal menu **Gestione API** a sinistra, quindi fare clic sul nome del prodotto desiderato.

![Visibilità dei prodotti][api-management-add-group-to-product]

Selezionare la scheda **Visibilità** per aggiungere e rimuovere gruppi e per visualizzare gli attuali gruppi associati al prodotto. Per aggiungere o rimuovere gruppi, selezionare o deselezionare le caselle di controllo per i gruppi desiderati, quindi fare clic su **Salva**.

![Visibilità dei prodotti][api-management-add-group-to-product-visibility]

> [!NOTE]
> Per aggiungere gruppi di Azure Active Directory, vedere [Come autorizzare gli account per sviluppatore usando Azure Active Directory in Gestione API di Azure](api-management-howto-aad.md).
> 
> Per configurare gruppi dalla scheda **Visibilità** per un prodotto, fare clic su **Gestisci gruppi**.
> 
> 

Dopo che un prodotto è stato associato a un gruppo, gli sviluppatori in quel gruppo possono visualizzare il prodotto e sottoscriversi ad esso.

## <a name="associate-group-developer"> </a>Associare gruppi a sviluppatori
Per associare gruppi a sviluppatori, fare clic su **Utenti** dal menu **Gestione API** a sinistra, quindi selezionare la casella di controllo accanto agli sviluppatori da associare a un gruppo.

![Aggiungere uno sviluppatore a un gruppo][api-management-add-group-to-developer]

Dopo aver selezionato gli sviluppatori desiderati, fare clic sul gruppo desiderato nel menu a discesa **Aggiungi gruppo** . È possibile rimuovere sviluppatori dai gruppi usando l'elenco a discesa **Rimuovi da gruppo** . 

![Sviluppatori][api-management-add-group-to-developer-saved]

Dopo l'aggiunta dell'associazione tra sviluppatore e gruppo, è possibile visualizzarla nella scheda **Utenti** .

## <a name="next-steps"> </a>Passaggi successivi
* Dopo che uno sviluppatore è stato associato a un gruppo, potrà visualizzare i prodotti associati a quel gruppo e sottoscriversi ad essi. Per altre informazioni, vedere [Come creare e pubblicare un prodotto in Gestione API di Azure][How create and publish a product in Azure API Management],
* Oltre a creare e gestire gruppi nel portale di pubblicazione, è possibile creare e gestire i gruppi utilizzando l'entità API REST di gestione [Gruppo](https://msdn.microsoft.com/library/azure/dn776329.aspx) .

[api-management-management-console]: ./media/api-management-howto-create-groups/api-management-management-console.png
[api-management-add-group]: ./media/api-management-howto-create-groups/api-management-add-group.png
[api-management-add-group-window]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
[api-management-new-group]: ./media/api-management-howto-create-groups/api-management-new-group.png
[api-management-add-group-to-product]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
[api-management-add-group-to-product-visibility]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
[api-management-add-group-to-developer]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
[api-management-add-group-to-developer-saved]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png

[api-management-]: ./media/api-management-howto-create-groups/api-management-.png

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group
