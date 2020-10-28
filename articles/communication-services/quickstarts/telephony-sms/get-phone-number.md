---
title: 'Avvio rapido: Ottenere un numero di telefono da Servizi di comunicazione di Azure'
description: Informazioni su come acquistare un numero di telefono di Servizi di comunicazione di Azure tramite il portale di Azure.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 10/05/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: cff39f93f9caddfdbe48788f14b62642a373e2bf
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148188"
---
# <a name="quickstart-get-a-phone-number-using-the-azure-portal"></a>Avvio rapido: Ottenere un numero di telefono tramite il portale di Azure

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Introduzione a Servizi di comunicazione di Azure, con l'acquisto di un numero di telefono tramite il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Una risorsa attiva di Servizi di comunicazione.](../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Ottenere un numero di telefono

Per iniziare il provisioning dei numeri, passare alla risorsa di Servizi di comunicazione nel [portale di Azure](https://portal.azure.com).

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::

### <a name="getting-new-phone-numbers"></a>Ottenere nuovi numeri di telefono

Passare al pannello **Numeri di telefono** nel menu della risorsa.

:::image type="content" source="../media/manage-phone-azure-portal-phone-page.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::

Premere il pulsante **Ottieni** per avviare la procedura guidata. La procedura guidata del pannello **Numeri di telefono** include una serie di domande che aiutano a scegliere il numero di telefono più adatto allo specifico scenario. 

Sarà prima di tutto necessario scegliere il **paese/area geografica** in cui effettuare il provisioning del numero di telefono. Dopo aver selezionato il paese/area geografica, è necessario selezionare il **Caso d'uso** più adatto alle esigenze. 

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::

### <a name="select-your-phone-number-features"></a>Selezionare le funzionalità dei numeri di telefono

La configurazione del numero di telefono è suddivisa in due passaggi: 

1. La selezione del [tipo di numero](../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. La selezione delle [funzionalità del numero](../../concepts/telephony-sms/plan-solution.md#phone-number-features-in-azure-communication-services)

È possibile scegliere tra due tipi di numero di telefono: **geografico** e **verde** . Dopo aver selezionato un tipo di numero, è possibile scegliere la funzionalità.

In questo esempio è stato selezionato un tipo di **numero verde** con le funzionalità per le **chiamate in uscita** e per gli **SMS in ingresso e in uscita** .

:::image type="content" source="../media/manage-phone-azure-portal-select-plans.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::

Da qui, fare clic sul pulsante **Avanti: Numeri** nella parte inferiore della pagina per personalizzare i numeri di telefono di cui effettuare il provisioning.

### <a name="customizing-phone-numbers"></a>Personalizzare i numeri di telefono

Nella pagina **Numeri** sarà possibile personalizzare i numeri di telefono di cui effettuare il provisioning.

:::image type="content" source="../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::

> [!NOTE]
> Questa guida di avvio rapido mostra il flusso di personalizzazione del tipo di **numero verde** . Se si sceglie il tipo di numero **geografico** , l'esperienza potrebbe essere leggermente diversa, ma il risultato finale sarà lo stesso.

Scegliere il valore di **Prefisso** nell'elenco di quelli disponibili e immettere la quantità di cui effettuare il provisioning, quindi fare clic su **Cerca** per trovare i numeri che soddisfano i requisiti selezionati. Verranno visualizzati i numeri di telefono che soddisfano le esigenze insieme al relativo costo mensile.

:::image type="content" source="../media/manage-phone-azure-portal-found-numbers.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::

> [!NOTE]
> La disponibilità varia a seconda delle selezioni effettuate per tipo di numero, località e funzionalità.
> I numeri sono riservati per un breve periodo di tempo prima della scadenza della transazione. Se la transazione scade, sarà necessario selezionarli di nuovo.

Per visualizzare il riepilogo dell'acquisto ed effettuare l'ordine, fare clic sul pulsante **Avanti: Riepilogo** nella parte inferiore della pagina.

### <a name="place-order"></a>Effettuare l'ordine

Nella pagina di riepilogo è possibile rivedere il tipo di numero, le funzionalità, i numeri di telefono e il costo mensile totale per il provisioning dei numeri di telefono.

> [!NOTE]
> I prezzi indicati sono gli **addebiti mensili ricorrenti** che coprono il costo del leasing del numero di telefono selezionato. In questa visualizzazione non sono inclusi i **costi con pagamento in base al consumo** che vengono addebitati quando si effettuano o si ricevono chiamate. I listini prezzi sono [disponibili qui](../../concepts/pricing.md). Questi costi dipendono dal tipo di numero e dalle destinazioni chiamate. Ad esempio, il prezzo al minuto per una chiamata da un numero locale di Seattle a un numero locale di New York può essere diverso da quello per una chiamata dallo stesso numero a un numero di cellulare del Regno Unito.

Infine, fare clic su **Effettua ordine** nella parte inferiore della pagina per confermare.

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::.

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Trovare i numeri di telefono nel portale di Azure

Passare alla risorsa di Servizi di comunicazione di Azure nel [portale di Azure](https://portal.azure.com):

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::

Selezionare il pannello Numeri di telefono nel menu per gestire i numeri di telefono.

:::image type="content" source="../media/manage-phone-azure-portal-phones.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::

> [!NOTE]
> Potrebbero essere necessari alcuni minuti prima che i numeri di cui è stato effettuato il provisioning vengano visualizzati in questa pagina.


### <a name="customizing-phone-numbers"></a>Personalizzare i numeri di telefono

Nella pagina **Numeri** è possibile selezionare un numero di telefono per configurarlo.

:::image type="content" source="../media/manage-phone-azure-portal-capability-update.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::

Selezionare le funzionalità nelle opzioni disponibili, quindi fare clic su **Conferma** per applicare la selezione.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Domande e problemi comuni:

- Al momento, l'acquisto di numeri di telefono è supportato solo negli Stati Uniti. La località viene ricavata dall'indirizzo di fatturazione della sottoscrizione a cui è associata la risorsa. Al momento, non è possibile spostare una risorsa in un'altra sottoscrizione.

- I numeri di telefono rilasciati non potranno essere rilasciati o riacquistati fino al termine del ciclo di fatturazione.

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
