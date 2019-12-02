---
title: Messaggio di posta elettronica di Azure relativo a un saldo dovuto non pagato
description: Viene descritto come effettuare il pagamento se la sottoscrizione di Azure presenta un saldo dovuto non pagato
author: genlin
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: banders
ms.openlocfilehash: c042f5cee3d0a1e874729911a2162e48c1630a72
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226417"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Risolvere problemi relativi a un saldo dovuto non pagato per la sottoscrizione di Azure

Questo articolo si applica ai clienti con account del programma Microsoft Online Services.

Se il pagamento non viene ricevuto o non può essere elaborato, è possibile che si riceva un messaggio di posta elettronica oppure che si visualizzi un avviso nel portale di Azure o nel Centro account.
L'[amministratore account](billing-subscription-transfer.md#whoisaa) può pagare gli addebiti in sospeso nel [portale di Azure](https://portal.azure.com). Se si usa un metodo di pagamento con fattura, inviare il pagamento al destinatario elencato nella parte inferiore della fattura.

> [!IMPORTANT]
> * Se si hanno più sottoscrizioni scadute, associate alla stessa carta di credito, è necessario pagare l'intero saldo scoperto in una sola volta.
> * Lo strumento di pagamento usato per il pagamento degli addebiti in sospeso diventerà il nuovo metodo di pagamento attivo per tutte le sottoscrizioni per cui veniva usato il metodo di pagamento in errore.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Risolvere il saldo scaduto nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account.
1. Cercare **Gestione dei costi e fatturazione**.
1. Nella pagina Panoramica verrà visualizzato l'elenco delle sottoscrizioni. Se lo stato della sottoscrizione è scaduto, fare clic sul collegamento **Liquidazione del saldo**.
    ![Screenshot che mostra il collegamento Liquidazione del saldo](./media/billing-azure-subscription-past-due-balance/settle-balance-entry-point.png)
1. Il saldo scoperto totale riflette gli addebiti in sospeso in tutti i servizi Microsoft per cui si usa il metodo di pagamento in errore.
1. Selezionare un metodo di pagamento per pagare il saldo. Questo metodo di pagamento diventerà quello attivo per tutte le sottoscrizioni per cui attualmente si usa il metodo di pagamento in errore.
    ![Screenshot che mostra il collegamento al metodo di pagamento selezionato](./media/billing-azure-subscription-past-due-balance/settle-balance-screen.png)
1. Se sono presenti addebiti in sospeso per i servizi Microsoft anche con il metodo di pagamento selezionato, questa situazione si rifletterà anche nel saldo scoperto totale. È necessario pagare anche questi addebiti in sospeso.
1. Fare clic su **Pagamento**.

## <a name="troubleshoot-declined-credit-card"></a>Risolvere i problemi relativi a una carta di credito rifiutata

Se l'addebito sulla carta di credito viene rifiutato dall'istituto finanziario, contattare tale istituto per risolvere il problema. Rivolgersi alla banca per verificare se:
- Le transazioni internazionali sono abilitate per la carta.
- Il limite di credito o i fondi presenti sulla carta sono sufficienti per pagare il saldo.
- I pagamenti ricorrenti sono abilitati per la carta.

## <a name="not-getting-billing-email-notifications"></a>Non si ricevono notifiche di posta elettronica relative alla fatturazione?

Se si è un amministratore account, [verificare l'indirizzo e-mail usato per le notifiche](billing-how-to-change-azure-account-profile.md). È consigliabile usare un indirizzo di posta elettronica che viene controllato regolarmente. Se l'indirizzo è corretto, controllare la cartella di posta indesiderata.

## <a name="if-i-forget-to-pay-what-happens"></a>Cosa accade se ci si dimentica di effettuare il pagamento?

Il servizio viene annullato e le risorse non saranno più disponibili. I dati di Azure verranno eliminati 90 giorni dopo il termine del servizio. Per altre informazioni, vedere [Microsoft Trust Center - Come vengono gestiti i dati](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

Se si è certi che il pagamento è stato elaborato, ma la sottoscrizione è ancora disabilitata, contattare il [supporto di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
