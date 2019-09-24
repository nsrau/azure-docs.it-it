---
title: Open Banking (PSD2) e Strong Customer Authentication (SCA) per i clienti di Azure
description: Questo articolo spiega il motivo per cui per alcuni acquisti di Azure è necessaria l'autenticazione a più fattori e descrive come completarla.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: banders
ms.openlocfilehash: 3074ed30c5e1f6edae78a8ef3c3d655e302a2663
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997282"
---
# <a name="open-banking-psd2-and-strong-customer-authentication-sca-for-azure-customers"></a>Open Banking (PSD2) e Strong Customer Authentication (SCA) per i clienti di Azure

A partire dal 14 settembre 2019, le banche di 31 paesi dello [Spazio economico europeo](https://en.wikipedia.org/wiki/European_Economic_Area) sono tenute a verificare l'identità delle persone che effettuano acquisti online prima dell'elaborazione del pagamento. Questa verifica richiede l'autenticazione a più fattori per garantire la sicurezza e la protezione degli acquisti online. In alcuni paesi, la data di entrata in vigore di questo requisito di verifica verrà posticipata. Per altre informazioni, vedere [Domande frequenti di Microsoft sulla direttiva PSD2](https://support.microsoft.com/en-us/help/4517854?preview).

## <a name="what-psd2-means-for-azure-customers"></a>Cosa implica la direttiva PSD2 per i clienti di Azure

Se il pagamento di Azure viene effettuato tramite una carta di credito emessa da una banca operante nello [Spazio economico europeo](https://en.wikipedia.org/wiki/European_Economic_Area), può essere necessario completare l'autenticazione a più fattori per il metodo di pagamento dell'account. È possibile che venga chiesto di completare la richiesta di autenticazione a più fattori durante l'iscrizione o l'aggiornamento dell'account Azure, anche se in quel momento non si effettuano acquisti. È anche possibile che venga chiesto di eseguire l'autenticazione a più fattori quando si cambia il metodo di pagamento dell'account Azure, si rimuove il limite di spesa o si effettua un pagamento immediato dal portale di Azure, ad esempio per liquidare i saldi scoperti o acquistare crediti Azure.

Se la banca rifiuta gli addebiti mensili di Azure, si riceve un messaggio di posta elettronica relativo a scadenza superata con le istruzioni su come risolvere il problema. È possibile completare la richiesta di autenticazione a più fattori e pagare gli addebiti in sospeso nel portale di Azure.

## <a name="complete-multi-factor-authentication-in-the-azure-portal"></a>Completare l'autenticazione a più fattori nel portale di Azure

Le sezioni seguenti descrivono come completare l'autenticazione a più fattori nel portale di Azure. Usare le informazioni applicabili a situazioni specifiche.

### <a name="change-the-active-payment-method-of-your-azure-account"></a>Cambiare il metodo di pagamento attivo dell'account Azure

Per cambiare il metodo di pagamento attivo dell'account Azure, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account e passare a **Gestione dei costi e fatturazione**.
2. Nella pagina **Panoramica** selezionare la sottoscrizione corrispondente nella griglia **Sottoscrizioni personali**.
3. In 'Fatturazione' selezionare **Metodi di pagamento**. È possibile aggiungere una nuova carta di credito oppure impostare quella esistente come metodo di pagamento attivo per la sottoscrizione. Se la banca richiede l'autenticazione a più fattori, verrà chiesto di completarla durante il processo.

Per altre informazioni, vedere [Aggiungere, aggiornare o rimuovere una carta di credito per Azure](billing-how-to-change-credit-card.md).

### <a name="settle-outstanding-charges-for-azure-services"></a>Pagare gli addebiti in sospeso per i servizi di Azure

Se la banca rifiuta gli addebiti, lo stato dell'account di Azure diventerà **Scaduto** nel portale di Azure. Per verificare lo stato dell'account, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore account.
2. Eseguire una ricerca in **Gestione costi e fatturazione**.
3. Nella pagina **Panoramica** di **Gestione dei costi e fatturazione** esaminare la colonna dello stato nella griglia **Sottoscrizioni personali**.
4. Se lo stato della sottoscrizione è **Scaduto**, fare clic sul collegamento **Liquidazione del saldo**. Viene chiesto di completare l'autenticazione a più fattori durante il processo.

### <a name="settle-outstanding-charges-for-marketplace-and-reservation-purchases"></a>Pagare gli addebiti in sospeso per gli acquisti di prenotazioni e nel Marketplace

Gli acquisti di prenotazioni e quelli effettuati nel Marketplace vengono fatturati separatamente rispetto ai servizi di Azure. Se la banca rifiuta questi addebiti, lo stato della fattura diventerà **Scaduto** nel portale di Azure. Per verificare lo stato delle fatture relative a prenotazioni e Marketplace, eseguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore account.
2. Eseguire una ricerca in **Gestione costi e fatturazione**.
3. In 'Fatturazione' selezionare **Fatture**.
4. Fare clic sulla scheda **Azure Marketplace e prenotazioni** a destra.
5. Selezionare la sottoscrizione corrispondente.
6. Nella griglia delle fatture esaminare la colonna dello stato. Se lo stato della fattura è **Scadenza** o **Scaduto**, fare clic su **Pagamento immediato.** Viene chiesto di completare l'autenticazione a più fattori durante il processo.

## <a name="next-steps"></a>Passaggi successivi
- Se è necessario pagare una fattura di Azure, vedere [Risolvere i problemi relativi al saldo scaduto per la sottoscrizione di Azure](billing-azure-subscription-past-due-balance.md).
