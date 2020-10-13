---
title: Collegare l'ID partner per abilitare il credito guadagnato dal partner per le risorse delegate
description: Informazioni su come associare l'ID partner per ricevere il credito guadagnato dal partner (PEC) sulle risorse dei clienti gestite tramite Azure Lighthouse.
ms.date: 10/05/2020
ms.topic: how-to
ms.openlocfilehash: 5caa205ce74152c7ec047952f66c1bf9188ddf02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776171"
---
# <a name="link-your-partner-id-to-enable-partner-earned-credit-on-delegated-resources"></a>Collegare l'ID partner per abilitare il credito guadagnato dal partner per le risorse delegate

Se si è membri del [Microsoft Partner Network](https://partner.microsoft.com/), è possibile collegare l'ID partner con le credenziali usate per gestire le risorse dei clienti Delegate. In questo modo è possibile tenere traccia dell'effetto tra gli impegni dei clienti e ricevere il [credito guadagnato dal partner per i servizi gestiti (PEC)](/partner-center/partner-earned-credit).

Se si caricano [i clienti con offerte di servizi gestiti in Azure Marketplace](publish-managed-services-offers.md), questo collegamento viene eseguito automaticamente usando l'ID MPN associato all'account del centro per i partner usato per pubblicare le offerte. Non sono necessarie altre azioni per ricevere PEC per questi clienti.

Se si caricano [i clienti usando i modelli di gestione risorse di Azure](onboard-customer.md), è necessario intervenire per creare questo collegamento. Questa operazione viene eseguita [collegando l'ID MPN](../../cost-management-billing/manage/link-partner-id.md) con almeno un account utente nel tenant di gestione che ha accesso a ognuna delle sottoscrizioni caricate.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Associare l'ID partner quando si onboarding di nuovi clienti

Quando si caricano i clienti tramite modelli di Azure Resource Manager (modelli ARM), usare la procedura seguente per collegare l'ID partner e abilitare il credito guadagnato dal partner. Per completare questa procedura, è necessario essere a conoscenza dell' [ID partner MPN](/partner-center/partner-center-account-setup#locate-your-mpn-id) . Assicurarsi di usare l' **ID MPN associato** indicato nel profilo partner.

Per semplicità, è consigliabile creare un account dell'entità servizio nel tenant, collegarlo all' **ID MPN associato**, quindi concedere l'accesso a tutti i clienti caricati con un [ruolo predefinito di Azure idoneo per PEC](https://docs.microsoft.com/partner-center/azure-roles-perms-pec).

1. [Creare un account dell'entità servizio](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) nel tenant di gestione. Per questo esempio, l'account di automazione PEC dell'entità servizio verrà denominato.
1. Usando l'account dell'entità servizio, [collegarsi all'ID MPN associato](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) nel tenant di gestione. È sufficiente eseguire questa operazione una sola volta.
1. Quando si carica [un cliente usando i modelli ARM](onboard-customer.md), assicurarsi di includere un'autorizzazione che include l'account di automazione PEC come utente con un [ruolo predefinito di Azure idoneo per PEC](https://docs.microsoft.com/partner-center/azure-roles-perms-pec).

Seguendo questa procedura, ogni tenant del cliente gestito verrà associato all'ID partner, consentendo di ricevere PEC per i clienti. Per l'account di automazione PEC non è necessario autenticare o eseguire azioni nel tenant del cliente.

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>Aggiungere l'ID partner ai clienti precedentemente caricati

Se è già stato eseguito l'onboarding di un cliente, è possibile che non si voglia eseguire un'altra distribuzione per aggiungere l'entità servizio dell'account di automazione PEC. È invece possibile collegare l' **ID MPN associato** a un account utente che ha già accesso per lavorare nel tenant del cliente. Assicurarsi che all'account sia stato concesso un [ruolo predefinito di Azure idoneo per PEC](https://docs.microsoft.com/partner-center/azure-roles-perms-pec).

Dopo che l'account è stato [collegato all'ID MPN associato](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) nel tenant di gestione, sarà possibile ricevere PEC per quel cliente.

## <a name="confirm-partner-earned-credit"></a>Conferma credito guadagnato partner

È possibile [visualizzare i dettagli di PEC nell'portale di Azure](/partner-center/partner-earned-credit-explanation#azure-cost-management) e verificare quali costi hanno ricevuto il vantaggio di PEC.

Se è stata eseguita la procedura precedente e l'associazione non è visibile, aprire una richiesta di supporto nella portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [Microsoft Partner Network](/partner-center/mpn-overview).
- Scopri in [che modo PEC viene calcolato e a pagamento](/partner-center/partner-earned-credit-explanation).
- Eseguire l' [onboarding dei clienti](onboard-customer.md) nel Faro di Azure.
