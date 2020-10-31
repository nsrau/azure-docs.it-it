---
title: Collegare l'ID partner per tenere traccia dell'effetto sulle risorse delegate
description: Informazioni su come associare l'ID partner per ricevere il credito guadagnato dal partner (PEC) sulle risorse dei clienti gestite tramite Azure Lighthouse.
ms.date: 10/30/2020
ms.topic: how-to
ms.openlocfilehash: fcbcc70e380116b8e9f9b1c1e365dee1adb87a99
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080278"
---
# <a name="link-your-partner-id-to-track-your-impact-on-delegated-resources"></a>Collegare l'ID partner per tenere traccia dell'effetto sulle risorse delegate 

Se si è membri del [Microsoft Partner Network](https://partner.microsoft.com/), è possibile collegare l'ID partner con le credenziali usate per gestire le risorse dei clienti Delegate. Partner admin link (PAL) consente a Microsoft di identificare e riconoscere i partner che fanno successo ai clienti di Azure. Questo collegamento consente anche ai partner [CSP (Cloud Solution Provider)](/partner-center/csp-overview) di ricevere un [credito guadagnato dal partner per i servizi gestiti (PEC)](/partner-center/partner-earned-credit) per i clienti che hanno [firmato il contratto di servizio Microsoft (MCA)](/partner-center/confirm-customer-agreement) e si trovano nel [piano Azure](/partner-center/azure-plan-get-started).

Se si caricano [clienti con offerte di servizi gestiti in Azure Marketplace](publish-managed-services-offers.md), il collegamento avviene automaticamente usando l'ID MPN associato all'account del centro per i partner usato per pubblicare le offerte. Non è necessaria alcuna azione aggiuntiva per tenere traccia dell'effetto di questi clienti.

Se si caricano [i clienti usando i modelli di gestione risorse di Azure](onboard-customer.md), è necessario intervenire per creare questo collegamento. Questa operazione viene eseguita [collegando l'ID MPN](../../cost-management-billing/manage/link-partner-id.md) con almeno un account utente nel tenant di gestione che ha accesso a ognuna delle sottoscrizioni caricate.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Associare l'ID partner quando si onboarding di nuovi clienti

Quando si caricano i clienti tramite modelli di Azure Resource Manager (modelli ARM), usare la procedura seguente per collegare l'ID partner e consentire il credito guadagnato dal partner, se applicabile. Per completare questa procedura, è necessario essere a conoscenza dell' [ID partner MPN](/partner-center/partner-center-account-setup#locate-your-mpn-id) . Assicurarsi di usare l' **ID MPN associato** visualizzato nel profilo del partner.

Per semplicità, è consigliabile creare un account dell'entità servizio nel tenant, collegarlo all' **ID MPN associato** , quindi concedere l'accesso a tutti i clienti caricati con un [ruolo predefinito di Azure idoneo per PEC](/partner-center/azure-roles-perms-pec).

1. [Creare un account dell'entità servizio](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) nel tenant di gestione. Per questo esempio, verrà usato l'account di *automazione del provider* di nomi per questa entità servizio.
1. Usando l'account dell'entità servizio, [collegarsi all'ID MPN associato](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) nel tenant di gestione. È sufficiente eseguire questa operazione una sola volta.
1. Quando si carica [un cliente usando i modelli ARM](onboard-customer.md), assicurarsi di includere un'autorizzazione che include l'account di automazione del provider come utente con un [ruolo predefinito di Azure idoneo per PEC](/partner-center/azure-roles-perms-pec).

Seguendo questa procedura, ogni tenant del cliente gestito verrà associato all'ID partner. L'account di automazione del provider non deve autenticare o eseguire azioni nel tenant del cliente.

:::image type="content" source="../media/lighthouse-pal.jpg" alt-text="Diagramma che illustra il processo di accesso alla pubblicazione con Azure Lighthouse.":::

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>Aggiungere l'ID partner ai clienti precedentemente caricati

Se è già stato caricato un cliente, è possibile che non si desideri eseguire un'altra distribuzione per aggiungere l'entità servizio dell'account di automazione del provider. È invece possibile collegare l' **ID MPN associato** a un account utente che ha già accesso per lavorare nel tenant del cliente. Assicurarsi che all'account sia stato concesso un [ruolo predefinito di Azure idoneo per PEC](/partner-center/azure-roles-perms-pec).

Dopo che l'account è stato [collegato all'ID MPN associato](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) nel tenant di gestione, sarà possibile tenere traccia del riconoscimento per l'effetto sul cliente.

## <a name="confirm-partner-earned-credit"></a>Conferma credito guadagnato partner

È possibile [visualizzare i dettagli di PEC nell'portale di Azure](/partner-center/partner-earned-credit-explanation#azure-cost-management) e verificare quali costi hanno ricevuto il vantaggio di PEC. Tenere presente che PEC si applica solo ai clienti CSP che hanno firmato il MCA e si trovano nel piano Azure.

Se è stata eseguita la procedura precedente e non viene visualizzata l'associazione prevista, aprire una richiesta di supporto nella portale di Azure.

È anche possibile usare il [centro per i partner SDK](/partner-center/develop/get-invoice-unbilled-consumption-lineitems) e filtrare `rateOfPartnerEarnedCredit` per automatizzare la verifica PEC per una sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [Microsoft Partner Network](/partner-center/mpn-overview).
- Scopri in [che modo PEC viene calcolato e a pagamento](/partner-center/partner-earned-credit-explanation).
- Eseguire l' [onboarding dei clienti](onboard-customer.md) nel Faro di Azure.