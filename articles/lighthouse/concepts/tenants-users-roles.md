---
title: Tenant, ruoli e utenti negli scenari di Azure Lighthouse
description: Informazioni sui concetti di tenant, utenti e ruoli di Azure Active Directory, nonché su come possono essere usati negli scenari di Azure Lighthouse.
ms.date: 07/03/2020
ms.topic: conceptual
ms.openlocfilehash: 6dae09ddd7760af1663e0329eb646c8956dff3ac
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424116"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Tenant, ruoli e utenti negli scenari di Azure Lighthouse

Prima di caricare i clienti per [Azure Lighthouse](../overview.md), è importante comprendere come funzionano i tenant, gli utenti e i ruoli di Azure Active Directory (Azure ad), nonché come possono essere usati negli scenari di Azure Lighthouse.

Un *tenant* è un'istanza dedicata e attendibile di Azure AD. Un tenant di Azure rappresenta in genere una singola organizzazione. [Gestione delle risorse delegata di Azure](azure-delegated-resource-management.md) consente la proiezione logica delle risorse da un tenant a un altro tenant. In questo modo, gli utenti nel tenant di gestione, ad esempio uno appartenente a un provider di servizi, possono accedere alle risorse delegate nel tenant di un cliente o consentire alle [aziende con più tenant di centralizzare le operazioni di gestione](enterprise.md).

Per ottenere questa proiezione logica *, è necessario* caricare in Azure Lighthouse una sottoscrizione, o uno o più gruppi di risorse all'interno di una sottoscrizione, nel tenant del cliente. Questo processo di onboarding può essere eseguito [tramite modelli di Azure Resource Manager](../how-to/onboard-customer.md) o [pubblicando un'offerta pubblica o privata in Azure Marketplace](../how-to/publish-managed-services-offers.md).

Indipendentemente dal metodo di onboarding scelto, sarà necessario definire le *autorizzazioni*. Ogni autorizzazione specifica un account utente nel tenant di gestione che avrà accesso alle risorse delegate e un ruolo predefinito che imposta le autorizzazioni di cui ognuno di tali utenti disporrà per queste risorse.

## <a name="role-support-for-azure-lighthouse"></a>Supporto dei ruoli per Azure Lighthouse

Quando si definisce un'autorizzazione, a ogni account utente deve essere assegnato uno dei [ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md). I ruoli personalizzati e i [ruoli di amministratore della sottoscrizione classica](../../role-based-access-control/classic-administrators.md) non sono supportati.

Tutti i [ruoli predefiniti](../../role-based-access-control/built-in-roles.md) sono attualmente supportati con Azure Lighthouse, con le eccezioni seguenti:

- Il ruolo [Proprietario](../../role-based-access-control/built-in-roles.md#owner) non è supportato.
- I ruoli predefiniti con autorizzazione [DataActions](../../role-based-access-control/role-definitions.md#dataactions) non sono supportati.
- Il ruolo predefinito [Amministratore accessi utente](../../role-based-access-control/built-in-roles.md#user-access-administrator) è supportato, ma solo per lo scopo limitato di [assegnazione di ruoli a un'identità gestita nel tenant del cliente](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant). Non verranno applicate altre autorizzazioni generalmente concesse da questo ruolo. Se si definisce un utente con questo ruolo, è necessario specificare anche uno o più ruoli predefiniti che possono essere assegnati da questo utente alle identità gestite.

> [!NOTE]
> Quando un nuovo ruolo incorporato applicabile viene aggiunto ad Azure, può essere assegnato durante [l'onboarding di un cliente usando Azure Resource Manager modelli](../how-to/onboard-customer.md). Potrebbe verificarsi un ritardo prima che il ruolo appena aggiunto diventi disponibile nel centro per i partner durante [la pubblicazione di un'offerta di servizio gestito](../how-to/publish-managed-services-offers.md).

## <a name="best-practices-for-defining-users-and-roles"></a>Procedure consigliate per la definizione di utenti e ruoli

Quando si creano le autorizzazioni, è consigliabile attenersi alle procedure consigliate seguenti:

- Nella maggior parte dei casi, è consigliabile assegnare le autorizzazioni a un gruppo di utenti o a un'entità servizio di Azure AD, invece che a una serie di singoli account utente. In questo modo è possibile aggiungere o rimuovere l'accesso per i singoli utenti senza dover aggiornare e ripubblicare il piano quando cambiano i requisiti di accesso.
- Assicurarsi di seguire il principio dei privilegi minimi, in modo che gli utenti abbiano solo le autorizzazioni necessarie per completare il proprio lavoro, riducendo la possibilità di errori accidentali. Per altre informazioni, vedere [procedure di sicurezza consigliate](../concepts/recommended-security-practices.md).
- Includere un utente con il [ruolo di eliminazione di assegnazione di registrazione dei servizi gestiti](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) in modo che sia possibile [rimuovere l'accesso alla delega](../how-to/remove-delegation.md) in un secondo momento, se necessario. Se questo ruolo non viene assegnato, le risorse delegate possono essere rimosse solo da un utente che si trova nel tenant del cliente.
- Assicurarsi che tutti gli utenti che devono [visualizzare la pagina Clienti personali nel portale di Azure](../how-to/view-manage-customers.md) dispongano del ruolo [Lettore](../../role-based-access-control/built-in-roles.md#reader) o di un altro ruolo predefinito che include l'accesso in lettura.

> [!IMPORTANT]
> Per aggiungere autorizzazioni per un gruppo di Azure AD, il **tipo di gruppo** deve essere impostato su **sicurezza**. Questa opzione è selezionata quando viene creato il gruppo. Per altre informazioni, vedere [Creare un gruppo di base e aggiungere membri con Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [procedure consigliate per la sicurezza per Azure Lighthouse](recommended-security-practices.md).
- È possibile caricare i clienti in Azure Lighthouse, [usando Azure Resource Manager modelli](../how-to/onboard-customer.md) o pubblicando [un'offerta di servizi gestiti pubblici o privati in Azure Marketplace](../how-to/publish-managed-services-offers.md).
