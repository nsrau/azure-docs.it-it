---
title: Messaggio di posta elettronica di Azure relativo a un saldo dovuto non pagato
description: Viene descritto come effettuare il pagamento se la sottoscrizione di Azure presenta un saldo dovuto non pagato
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 9548900fe627f774aca08c05a243d807bb5a699c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282688"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Risolvere problemi relativi a un saldo dovuto non pagato per la sottoscrizione di Azure

Questo articolo si applica ai clienti che si sono iscritti ad Azure online con una carta di credito e hanno un account di fatturazione del programma di Microsoft Online Services. Vedere [Verificare il tipo di account in uso](#check-the-type-of-your-account). Se si ha un account di fatturazione del Contratto del cliente Microsoft, vedere come [pagare la fattura per Microsoft Azure](../understand/pay-bill.md).

Se il pagamento non viene ricevuto o non può essere elaborato, si riceverà un messaggio di posta elettronica e nel portale di Azure verrà visualizzato un avviso che indica che la sottoscrizione è scaduta. Se il metodo di pagamento predefinito è la carta di credito, l'[amministratore account](billing-subscription-transfer.md#whoisaa) può saldare gli addebiti in sospeso nel portale di Azure. Se il metodo di pagamento è tramite fattura (assegno/bonifico), inviare il pagamento alla località indicata nella parte inferiore della fattura.

> [!IMPORTANT]
> * Se si hanno più sottoscrizioni scadute, associate alla stessa carta di credito, è necessario pagare l'intero saldo scoperto in una sola volta.
> * La carta di credito che si usa per saldare egli addebiti in sospeso diventerà il nuovo metodo di pagamento predefinito per tutte le sottoscrizioni per cui veniva usato il metodo di pagamento in errore.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Risolvere il saldo scaduto nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account.
1. Cercare **Gestione dei costi e fatturazione**.
1. Selezionare la sottoscrizione scaduta nella pagina **Panoramica**.
1. Nella pagina **Panoramica della sottoscrizione** fare clic sul banner rosso relativo alla scadenza per liquidare il saldo.
    > [!NOTE]
    > Se non si è l'amministratore account, non sarà possibile liquidare il saldo.
1. Nella pagina **Liquidazione del saldo** fare clic su **Selezionare un metodo di pagamento**.
    ![Screenshot che mostra il collegamento al metodo di pagamento selezionato](./media/resolve-past-due-balance/settle-balance-screen.png)

1. Nel nuovo pannello a destra selezionare una carta di credito esistente nell'elenco a discesa o aggiungerne una nuova facendo clic sul collegamento blu **Aggiungi un nuovo metodo di pagamento**. Questa carta di credito diventerà il metodo di pagamento attivo per tutte le sottoscrizioni per cui attualmente si usa il metodo di pagamento in errore.
     > [!NOTE]
     > * Il saldo scoperto totale riflette gli addebiti in sospeso in tutti i servizi Microsoft per cui si usa il metodo di pagamento in errore.
     > * Se sono presenti addebiti in sospeso per i servizi Microsoft anche con il metodo di pagamento selezionato, questa situazione si rifletterà anche nel saldo scoperto totale. È necessario pagare anche questi addebiti in sospeso.
1. Fare clic su **Pagamento**.

## <a name="troubleshoot-declined-credit-card"></a>Risolvere i problemi relativi a una carta di credito rifiutata

Se l'addebito sulla carta di credito viene rifiutato dall'istituto finanziario, contattare tale istituto per risolvere il problema. Rivolgersi alla banca per verificare se:
- Le transazioni internazionali sono abilitate per la carta.
- Il limite di credito o i fondi presenti sulla carta sono sufficienti per pagare il saldo.
- I pagamenti ricorrenti sono abilitati per la carta.

## <a name="not-getting-billing-email-notifications"></a>Non si ricevono notifiche di posta elettronica relative alla fatturazione?

Se si è un amministratore account, [verificare l'indirizzo e-mail usato per le notifiche](change-azure-account-profile.md). È consigliabile usare un indirizzo di posta elettronica che viene controllato regolarmente. Se l'indirizzo è corretto, controllare la cartella di posta indesiderata.

## <a name="if-i-forget-to-pay-what-happens"></a>Cosa accade se ci si dimentica di effettuare il pagamento?

Il servizio viene annullato e le risorse non saranno più disponibili. I dati di Azure verranno eliminati 90 giorni dopo il termine del servizio. Per altre informazioni, vedere [Microsoft Trust Center - Come vengono gestiti i dati](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

Se si è certi che il pagamento è stato elaborato, ma la sottoscrizione è ancora disabilitata, contattare il [supporto di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="check-the-type-of-your-account"></a>Verificare il tipo di account in uso
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
