---
title: Creare & eliminare gli account utente consumer Di Azure AD B2C nel portale di AzureCreate a delete Azure AD B2C consumer user accounts in the Azure portal
description: Informazioni su come usare il portale di Azure per creare ed eliminare utenti consumer nella directory B2C di Azure AD.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c8fad8b5bd2dccba36c0c6888712fa806f3cbfc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187220"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Usare il portale di Azure per creare ed eliminare utenti consumer in Azure AD B2CUse the Azure portal to create and delete consumer users in Azure AD B2C

Potrebbero esserci scenari in cui si desidera creare manualmente gli account consumer nella directory Azure Active Directory B2C (Azure AD B2C). Anche se gli account consumer in una directory B2C di Azure AD vengono in genere creati quando gli utenti si iscrivono per usare una delle applicazioni, è possibile crearli a livello di codice e usando il portale di Azure.Although consumer accounts in an Azure AD B2C directory are most commonly created when users sign up to use one of your applications, you can create them programmatically and by using the Azure portal. Questo articolo è incentrato sul metodo del portale di Azure per la creazione e l'eliminazione degli utenti.

Per aggiungere o eliminare utenti, all'account deve essere assegnato il ruolo *Amministratore utente* o *Amministratore globale.*

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="types-of-user-accounts"></a>Tipi di account utente

Come descritto in [Panoramica degli account utente in Azure AD B2C](user-overview.md), è possibile creare tre tipi di account utente in una directory B2C di Azure AD:As described in Overview of user accounts in Azure AD B2C , there are three types of user accounts that can be created in an Azure AD B2C directory:

* Work
* Guest
* Utente

Questo articolo è incentrato sull'uso degli account consumer nel portale di Azure.This article focuses on working with **consumer accounts** in the Azure portal. Per informazioni sulla creazione e l'eliminazione di account di lavoro e guest, vedere [Aggiungere o eliminare utenti tramite Azure Active Directory.](../active-directory/fundamentals/add-users-azure-active-directory.md)

## <a name="create-a-consumer-user"></a>Creare un utente consumerCreate a consumer user

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. In **Gestisci** selezionare **Utenti**.
1. Selezionare **Nuovo utente**.
1. Selezionare **Crea utente Di Azure AD B2C**.
1. Scegliere un **metodo di accesso** e immettere un indirizzo di posta **elettronica** o un **nome utente** per il nuovo utente. Il metodo di accesso selezionato qui deve corrispondere all'impostazione specificata per il provider di *identità dell'account locale* del tenant di Azure AD B2C (vedere **Gestire i** > provider di**identità** nel tenant di Azure AD B2C).
1. Immettere un **Nome** per l'utente. Si tratta in genere del nome completo (dato e cognome) dell'utente.
1. (Facoltativo) È possibile **bloccare l'accesso** se si desidera ritardare la possibilità per l'utente di accedere. È possibile abilitare l'accesso in un secondo momento modificando il profilo dell'utente nel portale di Azure.You can enable sign in later by editing the user's **Profile** in the Azure portal.
1. Scegliere **Genera automaticamente password** o Consenti creazione **password**.
1. Specificare il **nome** e il **cognome**dell'utente .
1. Selezionare **Crea**.

A meno che non sia stata selezionata l'opzione **Blocca accesso**, l'utente può ora accedere utilizzando il metodo di accesso (e-mail o nome utente) specificato.

## <a name="delete-a-consumer-user"></a>Eliminare un utente consumerDelete a consumer user

1. Nella directory B2C di Azure AD selezionare **Utenti**e quindi selezionare l'utente da eliminare.
1. Selezionare **Elimina**e quindi **Sì** per confermare l'eliminazione.

Per informazioni dettagliate sul ripristino di un utente entro i primi 30 giorni dopo l'eliminazione o per l'eliminazione definitiva di un utente, vedere [Ripristinare o rimuovere un utente eliminato di recente tramite Azure Active Directory.](../active-directory/fundamentals/active-directory-users-restore.md)

## <a name="next-steps"></a>Passaggi successivi

Per gli scenari di gestione automatica degli utenti, ad esempio la migrazione di utenti da un altro provider di identità alla directory B2C di Azure AD, vedere [Azure AD B2C: Migrazione degli](user-migration.md)utenti.
