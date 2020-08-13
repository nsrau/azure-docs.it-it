---
title: Offerte di servizi gestiti in Azure Marketplace
description: Le offerte di servizi gestiti ti permettono di vendere offerte di gestione delle risorse ai clienti di Azure Marketplace.
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 6c3047cd95128f689e75d9c1f5fba5a39f86291c
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163323"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Offerte di servizi gestiti in Azure Marketplace

Questo articolo descrive il tipo di offerta del **servizio gestito** in [Azure Marketplace](https://azuremarketplace.microsoft.com). Le offerte del servizio gestito consentono di offrire servizi di gestione delle risorse ai clienti tramite [Azure Lighthouse](../overview.md). È possibile rendere disponibili queste offerte a tutti i potenziali clienti o solo a uno o più clienti specifici. Poiché i costi relativi a questi servizi gestiti vengono fatturati direttamente ai clienti, non vengono addebitate spese da Microsoft.

## <a name="understand-managed-service-offers"></a>Informazioni sulle offerte del servizio gestito

Il servizio gestito offre una semplificazione del processo di onboarding dei clienti nel Faro di Azure. Quando un cliente acquista un'offerta in Azure Marketplace, può specificare le sottoscrizioni e/o i gruppi di risorse di cui deve essere eseguito l'onboarding.

Successivamente, gli utenti dell'organizzazione potranno lavorare su tali risorse dall'interno del tenant di gestione tramite la [gestione delle risorse delegate di Azure](azure-delegated-resource-management.md), in base all'accesso definito durante la creazione dell'offerta. Questa operazione viene eseguita tramite un manifesto che specifica gli utenti, i gruppi e le entità servizio Azure Active Directory (Azure AD) che avranno accesso alle risorse dei clienti, insieme ai ruoli che definiscono il livello di accesso. Assegnando le autorizzazioni a un gruppo di Azure AD invece che a una serie di singoli account utente o applicazione, è possibile aggiungere o rimuovere singoli utenti quando cambiano i requisiti di accesso.

## <a name="public-and-private-offers"></a>Offerte pubbliche e private

Ogni offerta di servizi gestiti include uno o più piani. I piani possono essere privati o pubblici.

Per limitare l'offerta a clienti specifici, è possibile pubblicare un piano privato. Quando si esegue questa operazione, è possibile acquistare il piano solo per gli ID sottoscrizione specifici forniti. Per altre informazioni, vedere [Offerte private](../../marketplace/private-offers.md).

> [!NOTE]
> Le offerte private non sono supportate con le sottoscrizioni stabilite tramite un rivenditore del programma Cloud Solution Provider (CSP).

I piani pubblici consentono di promuovere i servizi presso i nuovi clienti. Questi sono in genere più appropriati quando è necessario solo un accesso limitato al tenant del cliente. Dopo aver stabilito una relazione con un cliente, se quest'ultimo decide di concedere all'organizzazione un accesso aggiuntivo, è possibile pubblicare un nuovo piano privato solo per quel cliente o [eseguirne l'onboarding per un ulteriore accesso usando i modelli di Azure Resource Manager](../how-to/onboard-customer.md).

Se necessario, è possibile includere nella stessa offerta piani sia pubblici che privati.

> [!IMPORTANT]
> Quando un piano viene pubblicato come pubblico non è possibile modificarlo in un piano privato. Per controllare i clienti che possono accettare l'offerta e delegare le risorse, usare un piano privato. Con un piano pubblico non è possibile limitare la disponibilità a determinati clienti o a un determinato numero di clienti, ma è possibile interrompere completamente la vendita del piano, se necessario. È possibile [rimuovere l'accesso a una delega](../how-to/remove-delegation.md) dopo che un cliente accetta un'offerta solo se è stata inclusa un'**Autorizzazione** con **Definizione ruolo** impostata su [Ruolo con autorizzazioni di eliminazione assegnazioni di registrazione dei servizi gestiti](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) quando è stata pubblicata l'offerta. È anche possibile contattare il cliente e chiedere di [rimuovere l'accesso](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Pubblicare le offerte del servizio gestito

Per informazioni su come pubblicare un'offerta di servizi gestiti, vedere [pubblicare un'offerta di servizi gestiti in Azure Marketplace](../how-to/publish-managed-services-offers.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [gestione risorse delegate di Azure](azure-delegated-resource-management.md) e sulle [esperienze di gestione tra tenant](cross-tenant-management-experience.md).
- [Pubblicare offerte di servizi gestiti](../how-to/publish-managed-services-offers.md) in Azure Marketplace.
