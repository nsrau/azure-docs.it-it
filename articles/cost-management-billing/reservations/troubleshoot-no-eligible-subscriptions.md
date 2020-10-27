---
title: Risolvere i problemi quando non risultano sottoscrizioni idonee nel portale di Azure
description: Questo articolo consente di risolvere i problemi relativi al messaggio di errore Nessuna sottoscrizione idonea visualizzato nel portale di Azure quando si acquista una prenotazione.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/14/2020
ms.openlocfilehash: fd7a2bde47f34a61390082a223409070275b64ce
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92115202"
---
# <a name="troubleshoot-no-eligible-subscriptions"></a>Risolvere i problemi quando non risultano sottoscrizioni idonee

Questo articolo consente di risolvere i problemi relativi al messaggio di errore *Nessuna sottoscrizione idonea* visualizzato nel portale di Azure quando si acquista una prenotazione.

## <a name="symptoms"></a>Sintomi

1. Accedere al [portale di Azure](https://portal.azure.com) e passare a **Prenotazioni** .
1. Selezionare **Aggiungi** e quindi selezionare un servizio.
1. Viene visualizzato il messaggio di errore seguente:
   ```
    No eligible subscriptions
    
    You do not have any eligible subscriptions to purchase reservations. To purchase a reservation, you should be an owner on at least one subscription of the following type: Pay-as-you-go, CSP, Microsoft Enterprise or Microsoft Customer Agreement.
    ```
1. Nell'area **Selezionare il prodotto da acquistare** espandere l'elenco **Sottoscrizione di fatturazione** per vedere il motivo per cui una sottoscrizione specifica non è idonea per l'acquisto di un'istanza riservata. L'immagine seguente mostra gli esempi dei motivi per cui non è possibile acquistare una prenotazione.  
    :::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" alt-text="Esempio che mostra perché non è possibile acquistare una prenotazione" lightbox="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" :::

## <a name="cause"></a>Causa

Per acquistare un'istanza riservata di Azure, è necessario avere almeno una sottoscrizione che soddisfi i requisiti seguenti:

- La sottoscrizione deve essere un tipo di offerta supportato. I tipi di offerta supportati sono: Con pagamento in base al consumo, Cloud Solution Provider (CSP), Microsoft Azure Enterprise o Contratto del cliente Microsoft.
- È necessario essere un proprietario della sottoscrizione.

Se non si ha una sottoscrizione che soddisfa i requisiti, si riceverà l'errore `No eligible subscriptions`.

### <a name="cause-1"></a>Causa 1

La sottoscrizione deve essere un tipo di offerta supportato. I tipi di offerta supportati sono: Con pagamento in base al consumo, CSP, Microsoft Azure Enterprise o Contratto del cliente Microsoft. Il proprio tipo di sottoscrizione non è supportato. Se si seleziona una sottoscrizione con un tipo di offerta che non supporta le prenotazioni, viene visualizzato l'errore seguente.

```
Subscription not eligible for purchase

This subscription is not eligible for reservation benefit an cannot be used to purchase a reservation.
```

:::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/subscription-not-eligible.png" alt-text="Esempio che mostra perché non è possibile acquistare una prenotazione" :::

### <a name="cause-2"></a>Causa 2

È necessario essere un proprietario della sottoscrizione. Non si è proprietari della sottoscrizione. Se si seleziona una sottoscrizione di cui non si è proprietari, viene visualizzato l'errore seguente.

```
You do not have owner access on the subscription

You can only purchase reservations using subscriptions on which you have owner access.
```

:::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/no-owner-access.png" alt-text="Esempio che mostra perché non è possibile acquistare una prenotazione" :::

## <a name="solution"></a>Soluzione

- Se l'offerta corrente non supporta le prenotazioni, è necessario creare una nuova sottoscrizione di Azure.
- Se non si ha accesso a una prenotazione esistente, è possibile ottenerlo dal proprietario corrente.

### <a name="solution-1"></a>Soluzione 1

Per acquistare una prenotazione, è necessario creare una nuova sottoscrizione di Azure che supporti le prenotazioni.

- Se si ha una versione di valutazione gratuita di Azure, è possibile [aggiornare la sottoscrizione](../manage/upgrade-azure-subscription.md).
- È possibile creare una nuova sottoscrizione di Azure [con pagamento in base al consumo](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).
- Iscriversi per ottenere un [Contratto del cliente Microsoft](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) e creare una nuova sottoscrizione.
- Acquistare una nuova sottoscrizione con un [CSP](https://www.microsoft.com/solution-providers/home) e creare una nuova sottoscrizione.

### <a name="solution-2"></a>Soluzione 2

Per ottenere l'accesso come proprietario a una prenotazione, è necessario ottenere l'accesso a:

- L'ordine con cui è stata acquistata la prenotazione
- La prenotazione stessa

Il proprietario dell'ordine di prenotazione o il proprietario della prenotazione corrente può delegare l'accesso ad altri utenti seguendo questa procedura.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Tutti i servizi** > **Prenotazione** per visualizzare l'elenco delle prenotazioni a cui è possibile accedere.
1. Selezionare la prenotazione per la quale si desidera delegare l'accesso ad altri utenti.
1. Selezionare **Controllo di accesso (IAM)** .
1. Selezionare **Aggiungi assegnazione di ruolo** > **Ruolo** > **Proprietario** . Oppure selezionare un ruolo diverso se si intende concedere un ruolo limitato.
1. Digitare l'indirizzo e-mail dell'utente che si vuole aggiungere come proprietario.
1. Selezionare l'utente e quindi selezionare **Salva** .

Per altre informazioni vedere [Aggiungere o modificare gli utenti che possono gestire una prenotazione](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="next-steps"></a>Passaggi successivi

- Se è necessario chiedere a un proprietario di prenotazione di concedere l'accesso, vedere [Aggiungere o cambiare gli utenti che possono gestire una prenotazione](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).