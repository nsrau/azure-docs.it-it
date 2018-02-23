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
ms.date: 01/17/2018
ms.author: apimpm
ms.openlocfilehash: f377e1aadc126934fb47f6371f12435d2742efa6
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2018
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Come creare e usare gruppi per gestire account di sviluppatori in Gestione API di Azure
In Gestione API i gruppi permettono di gestire quali prodotti sono visibili per gli sviluppatori. I prodotti vengono innanzitutto resi visibili ai gruppi, quindi gli sviluppatori in quei gruppi possono visualizzare e sottoscriversi ai prodotti associati ai gruppi. 

Gestione API include i gruppi di sistema non modificabili seguenti:

* **Amministratori** : gli amministratori delle sottoscrizioni di Azure sono membri di questo gruppo. Gli amministratori gestiscono le istanze del servizio Gestione API e creano le API, le operazioni e i prodotti usati dagli sviluppatori.
* **Sviluppatori** : gli utenti autenticati del portale per sviluppatori rientrano in questo gruppo. Gli sviluppatori sono i clienti che compilano applicazioni usando le API. Agli sviluppatori viene concesso di accedere al portale per sviluppatori e di creare applicazioni che chiamano le operazioni di un'API.
* **Guest** : gli utenti non autenticati del portale per sviluppatori, ad esempio i potenziali clienti, che visitano il portale per sviluppatori di un'istanza di Gestione API rientrano in questo gruppo. È possibile concedere agli utenti guest un determinato livello di accesso di sola lettura, ad esempio la possibilità di visualizzare le API ma non di chiamarle.

Oltre a questi gruppi di sistema, gli amministratori possono creare gruppi personalizzati oppure [sfruttare i gruppi esterni nei tenant di Azure Active Directory associati][leverage external groups in associated Azure Active Directory tenants]. È possibile usare gruppi personalizzati ed esterni assieme ai gruppi di sistema per offrire agli sviluppatori visibilità e accesso ai prodotti API. Ad esempio, si può creare un gruppo personalizzato per gli sviluppatori affiliati a un'organizzazione partner specifica e consentire loro di accedere alle API da un prodotto che contiene solo le API pertinenti. Un utente può essere un membro di più gruppi.

Questa guida illustra come gli amministratori di un'istanza di Gestione API possono aggiungere nuovi gruppi e associarli a prodotti e sviluppatori.

Oltre a creare e gestire gruppi nel portale di pubblicazione, è possibile creare e gestire i gruppi utilizzando l'entità API REST di gestione [Gruppo](https://msdn.microsoft.com/library/azure/dn776329.aspx) .

## <a name="prerequisites"></a>prerequisiti

Completare le attività riportate in questo articolo: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-group"></a>Creare un gruppo

In questa sezione viene illustrato come aggiungere un nuovo gruppo all'account di Gestione API.

1. Selezionare la scheda **Gruppi** a sinistra della schermata.
2. Fare clic su **+Aggiungi**.
3. Immettere un nome univoco per il gruppo e una descrizione facoltativa.
4. Fare clic su **Crea**.

    ![Aggiungere un nuovo gruppo](./media/api-management-howto-create-groups/groups001.png)

Dopo averlo creato, il gruppo viene aggiunto all'elenco **Gruppi**. <br/>Per modificare il **nome** o la **descrizione** del gruppo, fare clic sul nome del gruppo e quindi scegliere **Impostazioni**.<br/>Per eliminare il gruppo, fare clic sul nome del gruppo e premere **CANC**.

Ora che il gruppo è stato creato, può essere associato a prodotti e sviluppatori.

## <a name="associate-group-product"></a>Associare un gruppo a un prodotto

1. Selezionare la scheda **Prodotti** a sinistra.
2. Fare clic sul nome del prodotto desiderato.
3. Premere **Controllo di accesso**.
4. Fare clic su **+ Aggiungi gruppo**.

    ![Aggiungere un nuovo gruppo](./media/api-management-howto-create-groups/groups002.png)
5. Selezionare il gruppo a cui che si vuole aggiungere.

    ![Aggiungere un nuovo gruppo](./media/api-management-howto-create-groups/groups003.png)

    Per rimuovere un gruppo dal prodotto, fare clic su **Elimina**.

    ![Eliminare un gruppo](./media/api-management-howto-create-groups/groups004.png)

Dopo che un prodotto è stato associato a un gruppo, gli sviluppatori in quel gruppo possono visualizzare il prodotto e sottoscriversi ad esso.

> [!NOTE]
> Per aggiungere gruppi di Azure Active Directory, vedere [Come autorizzare gli account per sviluppatore usando Azure Active Directory in Gestione API di Azure](api-management-howto-aad.md).

## <a name="associate-group-developer"> </a>Associare gruppi a sviluppatori

In questa sezione viene illustrato come associare gruppi ai membri.

1. Selezionare la scheda **Gruppi** a sinistra della schermata.
2. Selezionare **Membri**.

    ![Aggiungere un membro](./media/api-management-howto-create-groups/groups005.png)
3. Premere **+Aggiungi** e selezionare un membro.

    ![Aggiungere un membro](./media/api-management-howto-create-groups/groups006.png)
4. Fare clic su **Seleziona**.


Dopo l'aggiunta dell'associazione tra sviluppatore e gruppo, è possibile visualizzarla nella scheda **Utenti** .

## <a name="next-steps"></a>Passaggi successivi
* Dopo che uno sviluppatore è stato associato a un gruppo, potrà visualizzare i prodotti associati a quel gruppo e sottoscriversi ad essi. Per altre informazioni, vedere [Come creare e pubblicare un prodotto in Gestione API di Azure][How create and publish a product in Azure API Management],
* Oltre a creare e gestire gruppi nel portale di pubblicazione, è possibile creare e gestire i gruppi utilizzando l'entità API REST di gestione [Gruppo](https://msdn.microsoft.com/library/azure/dn776329.aspx) .

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
