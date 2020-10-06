---
title: 'Avvio rapido: Ottenere un numero di telefono da Servizi di comunicazione di Azure'
description: Informazioni su come acquistare un numero di telefono di Servizi di comunicazione di Azure tramite il portale di Azure.
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/09/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: f5cf8f8ef004dacc9fe2bbdd1b815f2ae5275311
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91298117"
---
# <a name="quickstart-get-a-phone-number-using-the-azure-portal"></a>Avvio rapido: Ottenere un numero di telefono tramite il portale di Azure

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Introduzione a Servizi di comunicazione di Azure, con l'acquisto di un numero di telefono tramite il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Una risorsa attiva di Servizi di comunicazione.](../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Ottenere un numero di telefono

Per iniziare il provisioning dei numeri, passare alla risorsa di Servizi di comunicazione nel [portale di Azure](https://portal.azure.com).

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::

### <a name="getting-new-phone-numbers"></a>Ottenere nuovi numeri di telefono

Passare al pannello Numeri di telefono nel menu della risorsa.

:::image type="content" source="../media/manage-phone-azure-portal-phone-page.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::

Premere il pulsante `Get` per avviare la procedura guidata. La procedura guidata del pannello `Phone numbers` include una serie di domande che aiutano a scegliere il numero di telefono più adatto allo specifico scenario. 

Sarà prima di tutto necessario scegliere il `Country/region` in cui effettuare il provisioning del numero di telefono. Dopo aver selezionato il paese/area geografica, è necessario selezionare il `phone plan` più adatto alle esigenze. 

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::

### <a name="select-a-phone-plan"></a>Selezionare un piano telefonico

La selezione di un piano telefonico è divisa in due passaggi: 

1. Selezione di [Tipo di numero](../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. Selezione di [Piano](../../concepts/telephony-sms/plan-solution.md#plans)

Sono attualmente disponibili due tipi di numeri: `Geographic` e `Toll-free`. Dopo aver selezionato un tipo di numero, verranno offerti diversi piani tra cui scegliere.

> [!NOTE]
> Attualmente è supportata solo la selezione di numeri di telefono con chiamate in ingresso OPPURE in uscita. È tuttavia possibile acquistare un numero di telefono con le chiamate in ingresso abilitate, quindi configurare l'ID chiamante in uscita in modo che corrisponda al numero di telefono abilitato per le chiamate in ingresso (quello che gli utenti vedono quando ricevono una chiamata dall'applicazione Servizi di comunicazione).
> Questo vale solo per le chiamate bidirezionali. L'SMS bidirezionale è supportato a livello nativo.

In questo esempio è stato scelto un tipo di numero `Toll-free` con il piano `Outbound calling`.

:::image type="content" source="../media/manage-phone-azure-portal-select-plans.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::

### <a name="declare-purpose"></a>Dichiarare lo scopo

Successivamente, la procedura guidata chiederà lo scopo per cui usare il numero. Questa informazione viene raccolta per applicare le normative corrette per le imposte e le chiamate di emergenza.

:::image type="content" source="../media/quickstart-search-and-acquire-bot-or-human.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::

Da qui, fare clic sul pulsante `Next: Numbers` nella parte inferiore della pagina per personalizzare i numeri di telefono di cui effettuare il provisioning.

### <a name="customizing-phone-numbers"></a>Personalizzare i numeri di telefono

Nella pagina `Numbers` sarà possibile personalizzare i numeri di telefono di cui effettuare il provisioning.

:::image type="content" source="../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::

> [!NOTE]
> Questa guida di avvio rapido mostra il flusso di personalizzazione del tipo di numero `Toll-free`. Se si sceglie il tipo di numero `Geographic`, l'esperienza potrebbe essere leggermente diversa, ma il risultato finale sarà lo stesso.

Scegliere il valore di `Area code` nell'elenco di prefissi disponibili e immettere la quantità di cui effettuare il provisioning, quindi fare clic su `Search` per trovare i numeri che soddisfano i requisiti selezionati. Verranno visualizzati i numeri di telefono che soddisfano le esigenze insieme al relativo costo mensile.

:::image type="content" source="../media/manage-phone-azure-portal-found-numbers.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::

> [!NOTE]
> La disponibilità varia a seconda delle selezioni effettuate per tipo di numero, località e piano.
> I numeri sono riservati per un breve periodo di tempo prima della scadenza della transazione. Se la transazione scade, sarà necessario selezionarli di nuovo.

Per visualizzare il riepilogo dell'acquisto ed effettuare l'ordine, fare clic sul pulsante `Next: Summary` nella parte inferiore della pagina.

### <a name="place-order"></a>Effettuare l'ordine

Nella pagina di riepilogo è possibile rivedere il tipo di numero, le funzionalità, i numeri di telefono e il costo mensile totale per il provisioning dei numeri di telefono.

> [!NOTE]
> I prezzi indicati sono gli **addebiti mensili ricorrenti** che coprono il costo del leasing del numero di telefono selezionato. In questa visualizzazione non sono inclusi i **costi con pagamento in base al consumo** che vengono addebitati quando si effettuano o si ricevono chiamate. I listini prezzi sono [disponibili qui](../../concepts/pricing.md). Questi costi dipendono dal tipo di numero e dalle destinazioni chiamate. Ad esempio, il prezzo al minuto per una chiamata da un numero locale di Seattle a un numero locale di New York può essere diverso da quello per una chiamata dallo stesso numero a un numero di cellulare del Regno Unito.

Infine, fare clic su `Place order` nella parte inferiore della pagina per confermare.

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::.

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Trovare i numeri di telefono nel portale di Azure

Passare alla risorsa di Servizi di comunicazione di Azure nel [portale di Azure](https://portal.azure.com):

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::

Selezionare la scheda Numeri di telefono nel menu per gestire i numeri di telefono.

:::image type="content" source="../media/manage-phone-azure-portal-phones.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::

> [!NOTE]
> Potrebbero essere necessari alcuni minuti prima che i numeri di cui è stato effettuato il provisioning vengano visualizzati in questa pagina.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Domande e problemi comuni:

- Al momento, l'acquisto di numeri di telefono è supportato solo negli Stati Uniti. La località viene ricavata dall'indirizzo di fatturazione della sottoscrizione a cui è associata la risorsa. Al momento, non è possibile spostare una risorsa in un'altra sottoscrizione.

- Quando un numero di telefono viene eliminato, non verrà rilasciato e non potrà essere riacquistato fino alla fine del periodo di fatturazione.

- Quando una risorsa di Servizi di comunicazione viene eliminata, i numeri di telefono associati verranno rilasciati automaticamente allo stesso tempo.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come:

> [!div class="checklist"]
> * Acquistare un numero di telefono
> * Gestire il numero di telefono
> * Rilasciare un numero di telefono

> [!div class="nextstepaction"]
> [Inviare un SMS](../telephony-sms/send.md)
> [Introduzione alle chiamate](../voice-video-calling/getting-started-with-calling.md)
