---
title: Creare & eliminare Azure AD B2C account utente consumer nel portale di Azure
description: Informazioni su come usare la portale di Azure per creare ed eliminare utenti consumer nella directory Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 259ce2b1881c31c2558539fed34513575d193a48
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961988"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Usare il portale di Azure per creare ed eliminare utenti consumer in Azure AD B2C

Potrebbero essere presenti scenari in cui si desidera creare manualmente gli account utente nella directory Azure Active Directory B2C (Azure AD B2C). Anche se gli account del consumer in una directory Azure AD B2C vengono creati più di frequente quando gli utenti si iscrivono per usare una delle applicazioni, è possibile crearli a livello di codice e usando il portale di Azure. Questo articolo è incentrato sul metodo portale di Azure per la creazione e l'eliminazione di utenti.

Per aggiungere o eliminare utenti, all'account deve essere assegnato il ruolo di *amministratore utente* o *amministratore globale* .

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="types-of-user-accounts"></a>Tipi di account utente

Come descritto in [Panoramica degli account utente in Azure ad B2C](user-overview.md), esistono tre tipi di account utente che è possibile creare in una directory Azure ad B2C:

* Work
* Guest
* Consumer

Questo articolo è incentrato sull'uso degli **account utente** nel portale di Azure. Per informazioni sulla creazione e l'eliminazione di account di lavoro e Guest, vedere [aggiungere o eliminare utenti con Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="create-a-consumer-user"></a>Creazione di un utente consumer

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. In **Gestisci** selezionare **Utenti**.
1. Selezionare **Nuovo utente**.
1. Selezionare **crea Azure ad B2C utente**.
1. Scegliere un **metodo di accesso** e immettere un indirizzo di **posta elettronica** o un **nome** utente per il nuovo utente. Il metodo di accesso selezionato deve corrispondere a quello specificato per il provider di identità dell' *account locale* del tenant di Azure ad B2C (vedere **gestire** i **provider di identità** > nel tenant di Azure ad B2C).
1. Immettere un **nome** per l'utente. Si tratta in genere del nome completo (dato e cognome) dell'utente.
1. Opzionale È possibile **bloccare l'accesso** se si vuole ritardare la possibilità per l'utente di accedere. È possibile abilitare l'accesso in un secondo momento modificando il **profilo** dell'utente nel portale di Azure.
1. Scegliere **genera automaticamente password** o **Consenti la creazione della password**.
1. Specificare il **nome** e il **Cognome**dell'utente.
1. Selezionare **Create**.

A meno che non sia stata selezionata l'opzione **Blocca accesso**, l'utente può ora accedere usando il metodo di accesso (posta elettronica o nome utente) specificato.

## <a name="delete-a-consumer-user"></a>Eliminare un utente consumer

1. Nella directory di Azure AD B2C selezionare **utenti**e quindi selezionare l'utente che si desidera eliminare.
1. Selezionare **Elimina**e quindi **Sì** per confermare l'eliminazione.

Per informazioni dettagliate sul ripristino di un utente entro i primi 30 giorni dopo l'eliminazione o per l'eliminazione definitiva di un utente, vedere [ripristinare o rimuovere un utente eliminato di recente con Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="next-steps"></a>Passaggi successivi

Per gli scenari di gestione automatizzata degli utenti, ad esempio la migrazione degli utenti da un altro provider di identità alla directory Azure AD B2C, vedere [Azure ad B2C: migrazione degli](active-directory-b2c-user-migration.md)utenti.
