---
title: Gestione risorse delegate di Azure
description: Gestione delle risorse delegate di Azure è un componente chiave di Azure Lighthouse, che consente ai provider di servizi di gestire risorse delegate su larga scala con agilità e precisione.
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: 9a499ceda546b7ea5c71cd8c770f1a4b99001b08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88163527"
---
# <a name="azure-delegated-resource-management"></a>Gestione risorse delegate di Azure

Gestione delle risorse delegate di Azure è uno dei componenti principali di [Azure Lighthouse](../overview.md). Con la gestione risorse delegate di Azure, i provider di servizi possono semplificare le esperienze di engagement e onboarding dei clienti, gestendo al contempo le risorse delegate su larga scala con agilità e precisione.

## <a name="what-is-azure-delegated-resource-management"></a>Informazioni sulla gestione risorse delegate di Azure

La gestione risorse delegate di Azure consente la proiezione logica delle risorse da un tenant a un altro. Questo consente agli utenti autorizzati di un tenant di Azure Active Directory (Azure AD) di eseguire operazioni di gestione su tenant di Azure AD diversi appartenenti ai clienti. I provider di servizi possono accedere al proprio tenant di Azure AD e avere l'autorizzazione per lavorare nelle sottoscrizioni e nei gruppi di risorse delegati dei clienti. In questo modo possono eseguire operazioni di gestione per conto dei clienti, senza dover accedere al tenant di ogni singolo cliente.

> [!TIP]
> La gestione risorse delegate di Azure può essere usata anche [all'interno di un'azienda con più tenant di Azure AD propri](enterprise.md) per semplificare la gestione tra tenant.

Con la gestione risorse delegate di Azure, gli utenti autorizzati possono lavorare direttamente nel contesto della sottoscrizione di un cliente senza avere un account nel tenant di tale cliente o essere comproprietario del tenant del cliente.

L' [esperienza di gestione tra tenant](cross-tenant-management-experience.md) ti permette di lavorare in modo più efficiente con i servizi di gestione di Azure, ad esempio criteri di Azure, il Centro sicurezza di Azure e altro ancora. Tutte le attività del provider di servizi vengono registrate nel log attività, che è archiviato nel tenant del cliente (e può essere visualizzato dagli utenti nel tenant di gestione). Ciò significa che gli utenti nel tenant di gestione e gestito possono identificare facilmente l'utente associato a qualsiasi modifica.

È possibile [pubblicare il nuovo tipo di offerta del servizio gestito in Azure Marketplace](../how-to/publish-managed-services-offers.md) per caricare facilmente i clienti nel Faro di Azure. In alternativa, è possibile [completare il processo di onboarding distribuendo i modelli di Azure Resource Manager](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Come funziona la gestione risorse delegate di Azure

Di seguito viene illustrato il funzionamento generale della gestione risorse delegate di Azure:

1. Per prima cosa, è necessario identificare l'accesso (ruoli) che i gruppi, le entità servizio o gli utenti dovranno gestire per le risorse di Azure del cliente. La definizione di accesso contiene l'ID tenant di gestione insieme alle identità **PrincipalId** del tenant di cui è stato eseguito il mapping ai [valori **roleDefinition** predefiniti](../../role-based-access-control/built-in-roles.md) (collaboratore, collaboratore macchina virtuale, lettore e così via).
2. È possibile specificare questo accesso e caricare il cliente in Azure Lighthouse in uno dei due modi seguenti:
   - [Pubblicare un'offerta di servizio gestito di Azure Marketplace](../how-to/publish-managed-services-offers.md) (privata o pubblica) che il cliente accetterà
   - [Distribuire un modello di Azure Resource Manager nel tenant del cliente](../how-to/onboard-customer.md) per una o più sottoscrizioni o gruppi di risorse specifici
3. Una volta caricato il cliente, gli utenti autorizzati possono accedere al tenant di gestione ed eseguire attività nell'ambito del cliente specificato, in base all'accesso definito.

> [!NOTE]
> È possibile gestire le risorse delegate che si trovano in [aree](../../availability-zones/az-overview.md#regions)diverse. Tuttavia, la delega delle sottoscrizioni in un [cloud nazionale](../../active-directory/develop/authentication-national-cloud.md) e nel cloud pubblico di Azure o in due cloud nazionali distinti non è supportata.

## <a name="support-for-azure-delegated-resource-management"></a>Supporto per la gestione risorse delegate di Azure

Per assistenza relativa alla gestione risorse delegate di Azure, è possibile aprire una richiesta di supporto nel portale di Azure. Per **Tipo di problema** scegliere **Tecnico**. Selezionare una sottoscrizione e quindi selezionare **Lighthouse** (in **monitoraggio & gestione**).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [esperienze di gestione tra tenant](cross-tenant-management-experience.md).
- Informazioni sulle [offerte di servizi gestiti in Azure Marketplace](managed-services-offers.md).
