---
title: Messaggio di posta elettronica di Azure relativo a un saldo dovuto non pagato | Microsoft Docs
description: Viene descritto come effettuare il pagamento se la sottoscrizione di Azure presenta un saldo dovuto non pagato
services: ''
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.assetid: d0b88c92-fb9d-4d12-931b-c26104ad63e9
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: banders
ms.openlocfilehash: f58969b167b6c0f0a66d46731ad76c1f6e9acc41
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491462"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Risolvere problemi relativi a un saldo dovuto non pagato per la sottoscrizione di Azure

Se non viene ricevuto il pagamento o se non è possibile elaborare il pagamento, si potrebbe ottenere un messaggio di posta elettronica o visualizzi un avviso nel portale di Azure o centro Account.
Se si è il [amministratore dell'Account](billing-subscription-transfer.md#whoisaa), è possibile pagare gli addebiti in sospeso [portale di Azure](https://portal.azure.com). Se si usa un metodo di pagamento con fattura, inviare il pagamento al destinatario elencato nella parte inferiore della fattura.

> [!IMPORTANT]
> * Se si hanno più sottoscrizioni che usano la stessa carta di credito e sono tutti scaduti, è necessario sostenere il saldo inevaso intero in una sola volta.
> * Lo strumento di pagamento che è usare per pagare gli addebiti in sospeso diventerà il nuovo metodo di pagamento attivo per tutte le sottoscrizioni che usava il metodo di pagamento non riuscito.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Risolvere scaduto saldo nel portale di Azure

1. Accedi per il [portale di Azure](https://portal.azure.com) come amministratore dell'Account.
1. Cercare **costi di gestione e fatturazione**.
1. Nella pagina Panoramica, si verrà visualizzato un elenco delle sottoscrizioni. Se lo stato della sottoscrizione è scaduto, fare clic sui **finalizzazione saldo** collegamento.
    ![Screenshot che mostra stabilizzarsi collegamento saldo](./media/billing-azure-subscription-past-due-balance/settle-balance-entry-point.png)
1. Il saldo totale riflette gli addebiti in sospeso in tutti i servizi di Microsoft usando il metodo di pagamento non riuscito.
1. Selezionare un metodo di pagamento per pagare il saldo. Questo metodo di pagamento diventerà il metodo di pagamento attivo per tutte le sottoscrizioni attualmente utilizzando il metodo di pagamento non riuscito.
    ![Screenshot che mostra i collegamenti di metodo di pagamento select](./media/billing-azure-subscription-past-due-balance/settle-balance-screen.png)
1. Se il metodo di pagamento selezionati ha anche gli addebiti in sospeso per i servizi Microsoft, questo si rifletteranno nel saldo totale. Gli addebiti in sospeso, devi pagare troppo.
1. Fare clic su **pagare**.

## <a name="not-getting-billing-email-notifications"></a>Non si ricevono notifiche di posta elettronica relative alla fatturazione?

Se si è un amministratore account, [verificare l'indirizzo e-mail usato per le notifiche](billing-how-to-change-azure-account-profile.md). È consigliabile usare un indirizzo di posta elettronica che viene controllato regolarmente. Se l'indirizzo è corretto, controllare la cartella di posta indesiderata.

## <a name="if-i-forget-to-pay-what-happens"></a>Cosa accade se ci si dimentica di effettuare il pagamento?

Il servizio viene annullato e le risorse non saranno più disponibili. I dati di Azure verranno eliminati 90 giorni dopo il termine del servizio. Per altre informazioni, vedere [Microsoft Trust Center - Come vengono gestiti i dati](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

Se si è certi che il pagamento è stato elaborato, ma la sottoscrizione è ancora disabilitata, contattare il [supporto di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
