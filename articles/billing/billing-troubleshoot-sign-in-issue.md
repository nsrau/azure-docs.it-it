---
title: Risolvere i problemi di accesso alla sottoscrizione di Azure
description: Consente di risolvere i problemi in cui non è possibile accedere al portale di Azure o al centro account di Azure.
author: v-miegge
manager: na
editor: na
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: ca641813e8b01a39d31a56e3730424b0fa1d6436
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657047"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Risolvere i problemi di accesso alla sottoscrizione di Azure

Questa guida consente di risolvere i problemi in cui non è possibile accedere all'portale di Azure o al centro account di Azure. 

## <a name="issues"></a>Problemi

### <a name="page-hangs-in-the-loading-status"></a>La pagina resta in stato di caricamento

Se la pagina del browser Internet si blocca, provare ognuno dei passaggi seguenti fino a quando non è possibile ottenere il portale di Azure.

- Aggiornare la pagina.
- Usare un browser Internet diverso.
- Usare la modalità di esplorazione privata per il browser. Per Internet Explorer: Fare clic su **strumenti** > **sicurezza** > **InPrivate Browsing**, quindi accedere al [portale di Azure](https://portal.azure.com/) o al [centro account di Azure](https://account.azure.com/Subscriptions).

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>Viene eseguito l'accesso automaticamente come utente diverso

Questo problema può verificarsi se si usa più di un account utente in un browser Internet.

Per risolvere il problema, utilizzare uno dei seguenti metodi alternativi:

- Svuotare la cache ed eliminare i cookie di Internet. In Internet Explorer fare clic su **strumenti** > **Opzioni** > Internet**Elimina**. Verificare di aver selezionato sia le caselle di controllo per file temporanei, cookie, password e cronologia esplorazioni, quindi fare clic su Elimina.
- Riconfigurare le impostazioni di Internet Explorer per ripristinare le impostazioni personali definite. Fare clic su **strumenti** > **Opzioni** > Internet**Avanzate** > selezionare la casella **Elimina impostazioni personali** > **Reimposta**.
- Usare la modalità di esplorazione privata per il browser. Per Internet Explorer:  Fare clic su **strumenti** > **sicurezza** > **InPrivate Browsing**, quindi accedere al [portale di Azure](https://portal.azure.com/) o al [centro account di Azure](https://account.azure.com/Subscriptions).

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>Posso accedere ma non è *stata trovata alcuna sottoscrizione*

Questo problema si verifica se è stata effettuata una selezione nella directory errata o se l'account non ha autorizzazioni sufficienti.

**Scenario 1:** Il messaggio di errore appare nel [portale di Azure](https://portal.azure.com/)

Per risolvere il problema:

- Verificare che sia selezionata la directory di Azure corretta facendo clic sull'account in alto a destra.
- Se è selezionata la directory di Azure corretta, ma il messaggio di errore viene comunque visualizzato, fare in modo che l'account venga [aggiunto come proprietario](billing-add-change-azure-subscription-administrator.md).

**Scenario 2:** Il messaggio di errore appare nel [Centro account di Azure](https://account.windowsazure.com/Subscriptions)

Controllare se l'account usato è l'amministratore account. Per verificare chi è l'amministratore account, seguire questa procedura:

1. Accedere alla [visualizzazione Sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Scegliere la sottoscrizione da controllare e quindi controllare la sezione **Impostazioni**.

3. Selezionare **Proprietà**. L'amministratore account della sottoscrizione viene visualizzato nella casella **Amministratore account** .

## <a name="additional-help-resources"></a>Risorse della guida aggiuntive

Altri articoli sulla risoluzione dei problemi per la fatturazione e le sottoscrizioni di Azure

- [Carta rifiutata](billing-troubleshoot-declined-card.md)
- [Problemi di iscrizione alla sottoscrizione](billing-troubleshoot-azure-sign-up.md)
- [Non sono state trovate sottoscrizioni](billing-no-subscriptions-found.md)
- [Vista dei costi Enterprise disabilitata](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Contattaci per assistenza

In caso di domande o per assistenza, [creare una richiesta di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

- [Documentazione sulla fatturazione di Azure](index.md)
