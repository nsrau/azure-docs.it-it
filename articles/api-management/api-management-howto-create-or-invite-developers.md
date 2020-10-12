---
title: Come gestire gli account utente in gestione API di Azure | Microsoft Docs
description: Informazioni su come creare o invitare gli utenti in gestione API di Azure. Consente di visualizzare risorse aggiuntive da usare dopo la creazione di un account per sviluppatore.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 92e032eb104835788f515cc7800fe5dacfa8adaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88566132"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Come gestire gli account utente in Gestione API di Azure

In Gestione API gli sviluppatori sono gli utenti delle API esposte con Gestione API. Questa guida illustra come creare e invitare gli sviluppatori a usare le API e i prodotti resi disponibili con l'istanza di gestione API. Per informazioni sulla gestione degli account utente a livello di codice, vedere la documentazione [Entità utente](/rest/api/apimanagement/2019-12-01/user) nel riferimento [API Management REST (REST di gestione API)](/rest/api/apimanagement/).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Prerequisiti

Completare le attività riportate in questo articolo: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-new-developer"></a><a name="create-developer"> </a>Creare un nuovo sviluppatore

Per aggiungere un nuovo utente, eseguire la procedura in questa sezione:

1. Selezionare la scheda **Utenti** a sinistra della schermata.
2. Premere **+Aggiungi**.
3. Immettere le informazioni appropriate per l'utente.
4. Premere **Aggiungi**.

    ![Aggiungere un nuovo utente](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Per impostazione predefinita, gli account sviluppatore appena creati sono **attivi**e associati al gruppo **Developers** . Gli account per sviluppatori che si trovano in uno stato **attivo** possono essere usati per accedere a tutte le API per le quali hanno sottoscrizioni. Per associare il nuovo sviluppatore creato ad altri gruppi, vedere [Come associare i gruppi agli sviluppatori][How to associate groups with developers].

## <a name="invite-a-developer"></a><a name="invite-developer"> </a>Invitare uno sviluppatore
Per invitare uno sviluppatore, eseguire la procedura in questa sezione:

1. Selezionare la scheda **Utenti** a sinistra della schermata.
2. Fare clic su **+Invita**.

Viene visualizzato un messaggio di conferma, ma il nuovo sviluppatore invitato non appare nell'elenco finché l'invito non viene accettato. 

Quando uno sviluppatore viene invitato, gli viene inviato un messaggio di posta elettronica generato con un modello e personalizzabile. Per altre informazioni, vedere [Configurare modelli di e-mail][Configure email templates].

Dopo l'accettazione dell'invito, l'account diventa attivo.

## <a name="deactivate-or-reactivate-a-developer-account"></a><a name="block-developer"> </a> Disattivare o riattivare un account sviluppatore

Per impostazione predefinita, un nuovo account sviluppatore creato o invitato è **Attivo**. Per disattivare un account sviluppatore, fare clic su **Blocca**. Per riattivare un account sviluppatore bloccato, fare clic su **Attiva**. Un account sviluppatore bloccato non può accedere al portale per sviluppatori né chiamare le API. Per eliminare un account utente, fare clic su **Elimina**.

Per bloccare un utente, eseguire la procedura seguente.

1. Selezionare la scheda **Utenti** a sinistra della schermata.
2. Fare clic sull'utente che si vuole bloccare.
3. Fare clic su **Blocca**.

## <a name="reset-a-user-password"></a>Reimpostare la password di un utente

Per usare gli account utente a livello di codice, vedere la documentazione relativa all'entità User in informazioni di riferimento sull' [API REST di gestione API](/rest/api/apimanagement/) . Per reimpostare la password di un account utente su un valore specifico, è possibile usare la procedura [Update a user (Aggiornamento di un utente)](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser) e specificare la password desiderata.

## <a name="next-steps"></a><a name="next-steps"> </a>Passaggi successivi
Dopo aver creato un account sviluppatore, è possibile associarlo ai ruoli ed effettuarne la sottoscrizione a prodotti e API. Per altre informazioni, vedere [Come creare e usare i gruppi][How to create and use groups].

[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png


[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
