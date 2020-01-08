---
title: Offerte di servizi gestiti in Azure Marketplace
description: Le offerte di servizi gestiti consentono ai provider di servizi di vendere offerte di gestione delle risorse ai clienti in Azure Marketplace.
ms.date: 12/16/2019
ms.topic: conceptual
ms.openlocfilehash: 1b4f0d7457a74afe710a48f429cfe47535a9b122
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453577"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Offerte di servizi gestiti in Azure Marketplace

Questo articolo descrive il nuovo tipo di offerta **Servizi gestiti** in [Azure Marketplace](https://azuremarketplace.microsoft.com). Le offerte di servizi gestiti consentono di offrire servizi di gestione delle risorse ai clienti con la gestione delle risorse delegate di Azure. È possibile rendere disponibili queste offerte a tutti i potenziali clienti oppure solo a uno o più clienti specifici. Poiché i costi relativi a questi servizi gestiti vengono fatturati direttamente ai clienti, non vengono addebitate spese da Microsoft.

## <a name="understand-managed-services-offers"></a>Informazioni sulle offerte di servizi gestiti

Le offerte di servizi gestiti semplificano il processo di onboarding dei clienti per la gestione delle risorse delegate di Azure. Quando un cliente acquista un'offerta in Azure Marketplace, può specificare le sottoscrizioni e/o i gruppi di risorse di cui deve essere eseguito l'onboarding. Si noti che è prima di tutto necessario autorizzare la sottoscrizione per l'onboarding registrando manualmente il provider di risorse **Microsoft.ManagedServices**.

Successivamente, gli utenti dell'organizzazione potranno eseguire le attività di amministrazione per tali risorse dal tenant dell'organizzazione, in base all'accesso definito durante la creazione dell'offerta nel [portale Cloud Partner](https://cloudpartner.azure.com/). A tale scopo si usa un manifesto che specifica gli utenti, i gruppi e le entità servizio di Azure AD che avranno accesso alle risorse del cliente con la gestione risorse delegate di Azure, insieme ai ruoli che ne definiscono il livello di accesso. Assegnando le autorizzazioni a un gruppo di Azure AD invece che a una serie di singoli account utente o applicazione, è possibile aggiungere o rimuovere singoli utenti quando cambiano i requisiti di accesso.

## <a name="public-and-private-offers"></a>Offerte pubbliche e private

Ogni offerta di servizi gestiti include uno o più piani. I piani possono essere privati o pubblici. 

Per limitare l'offerta a clienti specifici, è possibile pubblicare un piano privato. In questo caso, è possibile acquistare il piano solo per gli ID di sottoscrizione specificati. Per altre informazioni, vedere [Offerte private](../../marketplace/private-offers.md).

I piani pubblici consentono di promuovere i servizi presso i nuovi clienti. Questi sono in genere più appropriati quando è necessario solo un accesso limitato al tenant del cliente. Dopo aver stabilito una relazione con un cliente, se quest'ultimo decide di concedere all'organizzazione un accesso aggiuntivo, è possibile pubblicare un nuovo piano privato solo per quel cliente o [eseguirne l'onboarding per un ulteriore accesso usando i modelli di Azure Resource Manager](../how-to/onboard-customer.md).

Se necessario, è possibile includere nella stessa offerta piani sia pubblici che privati.

> [!IMPORTANT]
> Quando un piano è stato pubblicato come pubblico, non è possibile modificarlo in privato. Per controllare quali clienti possono accettare l'offerta e delegare le risorse, usare un piano privato. Con un piano pubblico, non è possibile limitare la disponibilità a determinati clienti o anche a un certo numero di clienti (sebbene sia possibile smettere completamente di vendere il piano se si sceglie di farlo). Attualmente non è disponibile alcun meccanismo per rifiutare o rimuovere le deleghe quando un cliente accetta un'offerta, anche se è sempre possibile rivolgersi a un cliente e chiedere di [rimuovere l'accesso](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Pubblicare offerte di servizi gestiti

Per informazioni su come pubblicare un'offerta di servizi gestiti, vedere [Pubblicare un'offerta di servizi gestiti in Azure Marketplace](../how-to/publish-managed-services-offers.md). Per informazioni generali sulla pubblicazione in Azure Marketplace tramite il portale Cloud Partner, vedere [Guida alla pubblicazione per Azure Marketplace e AppSource](../../marketplace/marketplace-publishers-guide.md) e [Gestire le offerte di Azure Marketplace e del marketplace di AppSource](../../marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [gestione risorse delegate di Azure](azure-delegated-resource-management.md) e sulle [esperienze di gestione tra tenant](cross-tenant-management-experience.md).
- [Pubblicare offerte di servizi gestiti](../how-to/publish-managed-services-offers.md) in Azure Marketplace.
