---
title: Offerte di servizi gestiti in Azure MarketplaceManaged service offers in Azure Marketplace
description: Le offerte di servizi gestiti consentono ai provider di servizi di vendere offerte di gestione delle risorse ai clienti in Azure Marketplace.Managed service offers allow service providers to sell resource management offers to customers in Azure Marketplace.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: a0670bd74971132dcf243736bdf882a00154a942
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672433"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Offerte di servizi gestiti in Azure MarketplaceManaged service offers in Azure Marketplace

Questo articolo descrive il tipo di offerta **del servizio gestito** in Azure [Marketplace.](https://azuremarketplace.microsoft.com) Le offerte di servizi gestiti consentono di offrire servizi di gestione delle risorse ai clienti tramite la gestione delle risorse delegate di [Azure.](azure-delegated-resource-management.md) È possibile rendere queste offerte disponibili a tutti i potenziali clienti o solo a uno o più clienti specifici. Poiché i costi relativi a questi servizi gestiti vengono fatturati direttamente ai clienti, non vengono addebitate spese da Microsoft.

## <a name="understand-managed-service-offers"></a>Comprendere le offerte di servizi gestiti

Il servizio gestito offre un'ottimizzazione del processo di onboarding dei clienti per la gestione delle risorse delegate di Azure.Managed service offers streamline the process of onboarding customers for Azure delegated resource management. Quando un cliente acquista un'offerta in Azure Marketplace, può specificare le sottoscrizioni e/o i gruppi di risorse di cui deve essere eseguito l'onboarding.

Successivamente, gli utenti dell'organizzazione potranno lavorare su tali risorse dall'interno del tenant dell'organizzazione, in base all'accesso definito durante la creazione dell'offerta nel [portale Cloud Partner.](https://cloudpartner.azure.com/) A tale scopo si usa un manifesto che specifica gli utenti, i gruppi e le entità servizio di Azure AD che avranno accesso alle risorse del cliente con la gestione risorse delegate di Azure, insieme ai ruoli che ne definiscono il livello di accesso. Assegnando le autorizzazioni a un gruppo di Azure AD invece che a una serie di singoli account utente o applicazione, è possibile aggiungere o rimuovere singoli utenti quando cambiano i requisiti di accesso.

## <a name="public-and-private-offers"></a>Offerte pubbliche e private

Ogni offerta di servizi gestiti include uno o più piani. I piani possono essere privati o pubblici.

Per limitare l'offerta a clienti specifici, è possibile pubblicare un piano privato. In questo caso, è possibile acquistare il piano solo per gli ID di sottoscrizione specificati. Per altre informazioni, vedere [Offerte private](../../marketplace/private-offers.md).

I piani pubblici consentono di promuovere i servizi presso i nuovi clienti. Questi sono in genere più appropriati quando è necessario solo un accesso limitato al tenant del cliente. Dopo aver stabilito una relazione con un cliente, se quest'ultimo decide di concedere all'organizzazione un accesso aggiuntivo, è possibile pubblicare un nuovo piano privato solo per quel cliente o [eseguirne l'onboarding per un ulteriore accesso usando i modelli di Azure Resource Manager](../how-to/onboard-customer.md).

Se necessario, è possibile includere nella stessa offerta piani sia pubblici che privati.

> [!IMPORTANT]
> Una volta che un piano è stato pubblicato come pubblico, non è possibile modificarlo in privato. Per controllare quali clienti possono accettare l'offerta e delegare le risorse, utilizzare un piano privato. Con un piano pubblico, non puoi limitare la disponibilità a determinati clienti o anche a un certo numero di clienti (anche se puoi smettere di vendere completamente il piano se scegli di farlo). È possibile [rimuovere l'accesso a una delega](../how-to/onboard-customer.md#remove-access-to-a-delegation) dopo che un cliente ha accettato un'offerta solo se è stata inclusa un'autorizzazione con la **definizione** di ruolo impostata su Ruolo di eliminazione assegnazione registrazione [servizi gestiti](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) al momento della pubblicazione dell'offerta. **Authorization** Puoi anche contattare il cliente e chiedere loro di [rimuovere il tuo accesso.](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)

## <a name="publish-managed-service-offers"></a>Pubblicare offerte di servizi gestiti

Per informazioni su come pubblicare un'offerta di servizi gestiti, vedere [Pubblicare un'offerta di servizi gestiti in Azure Marketplace](../how-to/publish-managed-services-offers.md). Per informazioni generali sulla pubblicazione in Azure Marketplace tramite il portale Cloud Partner, vedere Guida alla pubblicazione di [Azure Marketplace e AppSource.](../../marketplace/marketplace-publishers-guide.md)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [gestione risorse delegate di Azure](azure-delegated-resource-management.md) e sulle [esperienze di gestione tra tenant](cross-tenant-management-experience.md).
- [Pubblicare offerte](../how-to/publish-managed-services-offers.md) di servizi gestiti in Azure Marketplace.Publish managed services offers to Azure Marketplace.
