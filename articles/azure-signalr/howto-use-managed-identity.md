---
title: Identità gestite nel servizio Azure SignalR
description: Informazioni su come funzionano le identità gestite nel servizio Azure SignalR e su come usare un'identità gestita in scenari senza server.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: 9b6141e6009cb868d63429836f8c8f050c792ee5
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92152303"
---
# <a name="managed-identities-for-azure-signalr-service"></a>Identità gestite per il servizio Azure SignalR

Questo articolo illustra come creare un'identità gestita per il servizio Azure SignalR e come usarla in scenari senza server.

> [!Important] 
> Il servizio Azure SignalR può supportare una sola identità gestita. Ciò significa che è possibile aggiungere un'identità assegnata dal sistema o un'identità assegnata dall'utente. 

## <a name="add-a-system-assigned-identity"></a>Aggiungere un'identità assegnata dal sistema

Per configurare un'identità gestita nel portale di Azure, creare prima di tutto un'istanza del servizio Azure SignalR e quindi abilitare la funzionalità.

1. Creare un'istanza del servizio SignalR di Azure nel portale normalmente. Passare al portale.

2. Selezionare **Identità**.

4. Nella scheda **sistema assegnato** , impostare **stato** **su**attivato. Selezionare **Salva**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="Aggiungere un'identità assegnata dal sistema nel portale":::

## <a name="add-a-user-assigned-identity"></a>Aggiungere un'identità assegnata dall'utente

Per creare un'istanza del servizio Azure SignalR con un'identità assegnata dall'utente, è necessario creare l'identità e quindi aggiungere il relativo identificatore di risorsa al servizio.

1. Seguire [queste istruzioni](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) per creare una risorsa identità gestita assegnata dall'utente.

2. Creare un'istanza del servizio SignalR di Azure nel portale normalmente. Passare al portale.

3. Selezionare **Identità**.

4. Nella scheda **assegnato dall'utente** selezionare **Aggiungi**.

5. Cercare l'identità creata in precedenza e selezionarla. Selezionare **Aggiungi**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="Aggiungere un'identità assegnata dal sistema nel portale":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>Usa un'identità gestita in scenari senza server

Il servizio Azure SignalR è un servizio completamente gestito, pertanto non è possibile usare un'identità gestita per ottenere i token manualmente. Il servizio Azure SignalR usa invece l'identità gestita impostata per ottenere un token di accesso. Il servizio imposta quindi il token di accesso in un' `Authorization` intestazione in una richiesta upstream in scenari senza server.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>Abilitare l'autenticazione dell'identità gestita nelle impostazioni upstream

1. Aggiungere un'identità assegnata dal sistema o un'identità assegnata dall'utente.

2. Configurare le impostazioni upstream e usare **ManagedIdentity** come impostazioni di **autenticazione** . Per informazioni su come creare le impostazioni upstream con l'autenticazione, vedere [Impostazioni upstream](concept-upstream.md).

3. In impostazioni di autenticazione identità gestite, per **risorsa**, è possibile specificare la risorsa di destinazione. La risorsa diventerà un' `aud` attestazione nel token di accesso ottenuto, che può essere usata come parte della convalida negli endpoint upstream. La risorsa può essere una delle seguenti:
    - Empty
    - ID applicazione (client) dell'entità servizio
    - URI dell'ID applicazione dell'entità servizio
    - [ID risorsa di un servizio di Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > Se si convalida un token di accesso autonomamente nel servizio, è possibile scegliere uno qualsiasi dei formati di risorse. È sufficiente assicurarsi che il valore della **risorsa** nelle impostazioni di **autenticazione** e la convalida siano coerenti. Se si usa il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per un piano dati, è necessario usare la risorsa richiesta dal provider di servizi.

### <a name="validate-access-tokens"></a>Convalidare i token di accesso

Il token nell' `Authorization` intestazione è un [token di accesso della piattaforma Microsoft Identity](../active-directory/develop/access-tokens.md#validating-tokens).

Per convalidare i token di accesso, l'app deve anche convalidare i destinatari e i token di firma. Questi elementi devono essere convalidati in base ai valori contenuti nel documento di individuazione OpenID. Vedere, ad esempio, la [versione indipendente dal tenant del documento](https://login.microsoftonline.com/common/.well-known/openid-configuration).

Il middleware Azure Active Directory (Azure AD) dispone di funzionalità predefinite per la convalida dei token di accesso. È possibile esplorare gli [esempi](../active-directory/develop/sample-v2-code.md) per trovarne uno nel linguaggio desiderato.

Sono disponibili librerie ed esempi di codice che illustrano come gestire la convalida dei token. Sono disponibili anche diverse librerie partner open source per la convalida del token Web JSON (JWT). Esiste almeno un'opzione per quasi tutte le piattaforme e la lingua. Per ulteriori informazioni sulle librerie di autenticazione Azure AD e sugli esempi di codice, vedere [Microsoft Identity Platform Authentication Libraries](../active-directory/develop/reference-v2-libraries.md).

## <a name="next-steps"></a>Passaggi successivi

- [Sviluppo e configurazione di Funzioni di Azure e con il Servizio Azure SignalR](signalr-concept-serverless-development-config.md)