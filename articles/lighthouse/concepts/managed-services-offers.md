---
title: Offerte di servizi gestiti in Azure Marketplace
description: Le offerte di servizi gestiti consentono ai provider di servizi di vendere offerte di gestione delle risorse ai clienti in Azure Marketplace.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: a6fcf5f1d0ac194d60f834fb8d26db019c538410
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810875"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Offerte di servizi gestiti in Azure Marketplace

Questo articolo descrive il nuovo tipo di offerta **Servizi gestiti** in [Azure Marketplace](https://azuremarketplace.microsoft.com). Le offerte di servizi gestiti consentono di offrire servizi di gestione delle risorse ai clienti con la gestione delle risorse delegate di Azure. È possibile rendere disponibili queste offerte a tutti i potenziali clienti oppure solo a uno o più clienti specifici. Poiché i costi relativi a questi servizi gestiti vengono fatturati direttamente ai clienti, non vengono addebitate spese da Microsoft.

## <a name="understand-managed-services-offers"></a>Informazioni sulle offerte di servizi gestiti

Le offerte di servizi gestiti semplificano il processo di onboarding dei clienti per la gestione delle risorse delegate di Azure. Quando un cliente acquista un'offerta in Azure Marketplace, potrà specificare le sottoscrizioni e/o i gruppi di risorse di cui eseguire l'onboarding in modo che gli utenti specificati dell'organizzazione possano eseguire attività amministrative per il cliente dal tenant dell'organizzazione.

Non sono necessarie altre azioni da parte del cliente o del provider di servizi per eseguire l'onboarding del cliente. Quando infatti si definisce l'offerta nel [portale Cloud Partner](https://cloudpartner.azure.com/), si crea un manifesto che specifica gli utenti, i gruppi e le entità servizio di Azure AD che avranno accesso alle risorse del cliente usando la gestione delle risorse delegate di Azure, insieme ai ruoli che definiscono il livello di accesso. Assegnando le autorizzazioni a un gruppo di Azure AD invece che a una serie di singoli account utente o applicazione, è possibile aggiungere o rimuovere singoli utenti quando cambiano i requisiti di accesso.

## <a name="public-and-private-offers"></a>Offerte pubbliche e private

Ogni offerta di servizi gestiti include uno o più piani. Questi piani possono essere privati o pubblici.

Per limitare l'offerta a clienti specifici, è possibile pubblicare un piano privato. In questo caso, è possibile acquistare il piano solo per gli ID di sottoscrizione specificati. Per altre informazioni, vedere [Offerte private](https://docs.microsoft.com/azure/marketplace/private-offers).

I piani pubblici consentono di promuovere i servizi presso i nuovi clienti. Questi sono in genere più appropriati quando è necessario solo un accesso limitato al tenant del cliente. Dopo aver stabilito una relazione con un cliente, se quest'ultimo decide di concedere all'organizzazione un accesso aggiuntivo, è possibile pubblicare un nuovo piano privato solo per quel cliente o [eseguirne l'onboarding per un ulteriore accesso usando i modelli di Azure Resource Manager](../how-to/onboard-customer.md).

Tenere presente che un piano pubblicato come pubblico non può essere impostato in seguito come privato. Non è neppure possibile limitare la disponibilità di un piano pubblico a determinati clienti o nemmeno a un certo numero di clienti, ma è possibile smettere di vendere completamente il piano, se necessario.

Se necessario, è possibile includere nella stessa offerta piani sia pubblici che privati.

## <a name="publish-managed-service-offers"></a>Pubblicare offerte di servizi gestiti

Per informazioni su come pubblicare un'offerta di servizi gestiti, vedere [Pubblicare un'offerta di servizi gestiti in Azure Marketplace](../how-to/publish-managed-services-offers.md). Per informazioni generali sulla pubblicazione in Azure Marketplace tramite il portale Cloud Partner, vedere [Guida alla pubblicazione per Azure Marketplace e AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) e [Gestire le offerte di Azure Marketplace e del marketplace di AppSource](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [gestione risorse delegate di Azure](azure-delegated-resource-management.md) e sulle [esperienze di gestione tra tenant](cross-tenant-management-experience.md).
- [Pubblicare offerte di servizi gestiti](../how-to/publish-managed-services-offers.md) in Azure Marketplace.
