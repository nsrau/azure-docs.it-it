---
title: Tenant, ruoli e utenti negli scenari di Azure Lighthouse
description: Informazioni sui concetti di tenant, utenti e ruoli di Azure Active Directory, nonché su come possono essere usati negli scenari di Azure Lighthouse.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 11/05/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: b87ef8534dab2c8f08aa8cdee9d939e2d1a3a0e7
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73616039"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Tenant, ruoli e utenti negli scenari di Azure Lighthouse

Prima di eseguire l'onboarding dei clienti per la [gestione delle risorse delegate di Azure](azure-delegated-resource-management.md), è importante comprendere come funzionano i tenant, gli utenti e i ruoli di Azure Active Directory (Azure AD) e come possono essere usati negli scenari di Azure Lighthouse.

Un *tenant* è un'istanza dedicata e attendibile di Azure AD. Un tenant di Azure rappresenta in genere una singola organizzazione. La gestione delle risorse delegate di Azure consente la proiezione logica delle risorse da un tenant a un altro. In questo modo, gli utenti nel tenant di gestione, ad esempio uno appartenente a un provider di servizi, possono accedere alle risorse delegate nel tenant di un cliente o consentire alle [aziende con più tenant di centralizzare le operazioni di gestione](enterprise.md).

Per ottenere questa proiezione logica, è necessario eseguire l'*onboarding* di una sottoscrizione nel tenant del cliente oppure di uno o più gruppi di risorse all'interno di una sottoscrizione, ai fini della gestione delle risorse delegate di Azure. Questo processo di onboarding può essere eseguito [tramite modelli di Azure Resource Manager](../how-to/onboard-customer.md) o [pubblicando un'offerta pubblica o privata in Azure Marketplace](../how-to/publish-managed-services-offers.md).

Indipendentemente dal metodo di onboarding scelto, sarà necessario definire le *autorizzazioni*. Ogni autorizzazione specifica un account utente nel tenant di gestione che avrà accesso alle risorse delegate e un ruolo predefinito che imposta le autorizzazioni di cui ognuno di tali utenti disporrà per queste risorse.

## <a name="role-support-for-azure-delegated-resource-management"></a>Supporto dei ruoli per la gestione delle risorse delegate di Azure

Quando si definisce un'autorizzazione, a ogni account utente deve essere assegnato uno dei [ruoli Controllo degli accessi in base al ruolo predefiniti](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). I ruoli personalizzati e i [ruoli di amministratore della sottoscrizione classica](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) non sono supportati.

Tutti i [ruoli predefiniti](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) sono attualmente supportati dalla gestione delle risorse delegate di Azure, con le eccezioni seguenti:

- Il ruolo [Proprietario](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) non è supportato.
- I ruoli predefiniti con autorizzazione [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) non sono supportati.
- Il ruolo predefinito [Amministratore accessi utente](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) è supportato, ma solo per lo scopo limitato di [assegnazione di ruoli a un'identità gestita nel tenant del cliente](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant). Non verranno applicate altre autorizzazioni generalmente concesse da questo ruolo. Se si definisce un utente con questo ruolo, è necessario specificare anche uno o più ruoli predefiniti che possono essere assegnati da questo utente alle identità gestite.

## <a name="best-practices-for-defining-users-and-roles"></a>Procedure consigliate per la definizione di utenti e ruoli

Quando si creano le autorizzazioni, è consigliabile attenersi alle procedure consigliate seguenti:

- Nella maggior parte dei casi, è consigliabile assegnare le autorizzazioni a un gruppo di utenti o a un'entità servizio di Azure AD, invece che a una serie di singoli account utente. In questo modo è possibile aggiungere o rimuovere l'accesso per i singoli utenti senza dover aggiornare e ripubblicare il piano quando cambiano i requisiti di accesso.
- Assicurarsi di seguire il principio dei privilegi minimi, in modo che gli utenti abbiano solo le autorizzazioni necessarie per completare il proprio lavoro, riducendo la possibilità di errori accidentali. Per altre informazioni, vedere [Procedure di sicurezza consigliate](../concepts/recommended-security-practices.md).
- Includere un utente con il [ruolo di eliminazione di assegnazione di registrazione dei servizi gestiti](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-services-registration-assignment-delete-role) in modo che sia possibile [rimuovere l'accesso alla delega](../how-to/onboard-customer.md#remove-access-to-a-delegation) in un secondo momento, se necessario. Se questo ruolo non è assegnato, le risorse delegate possono essere rimosse solo da un utente nel tenant del cliente.
- Assicurarsi che tutti gli utenti che devono [visualizzare la pagina Clienti personali nel portale di Azure](../how-to/view-manage-customers.md) dispongano del ruolo [Lettore](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) o di un altro ruolo predefinito che include l'accesso in lettura.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [procedure di sicurezza consigliate per la gestione delle risorse delegate di Azure](recommended-security-practices.md).
- Eseguire l'onboarding dei clienti nella gestione risorse delegate di Azure, [usando i modelli di Azure Resource Manager](../how-to/onboard-customer.md) o [pubblicando un'offerta di servizi gestiti privata o pubblica in Azure Marketplace](../how-to/publish-managed-services-offers.md).
