---
title: Risolvere i problemi di accesso alla sottoscrizione di Azure
description: Questo articolo aiuta a risolvere i problemi che impediscono l'accesso al portale di Azure o al Centro account di Azure.
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 08/20/2020
ms.author: v-miegge
ms.openlocfilehash: 2e9b14fa264f3286134913e3c279c4400ce5bcc3
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132330"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Risolvere i problemi di accesso alla sottoscrizione di Azure

Questa guida aiuta a risolvere i problemi che impediscono l'accesso al portale di Azure o al Centro account di Azure.

> [!NOTE]
> In caso di problemi di iscrizione a un nuovo account di Azure, vedere [Risolvere i problemi di iscrizione a un nuovo account nel portale di Azure o nel Centro account di Azure](./troubleshoot-azure-sign-up.md).

## <a name="page-hangs-in-the-loading-status"></a>La pagina resta in stato di caricamento

Se si blocca la pagina del browser Internet, provare ciascuna delle seguenti operazioni fino a quando non è possibile accedere al portale di Azure.

- Aggiornare la pagina.
- Usare un browser Internet diverso.
- Usare la modalità di esplorazione privata per il browser:

   - **Microsoft Edge:** Aprire **Impostazioni** (i tre puntini accanto all'immagine del profilo), selezionare **Nuova finestra InPrivate** e quindi individuare e accedere al [portale di Azure](https://portal.azure.com/) o al [Centro account di Azure](https://account.azure.com/Subscriptions). 
   - **Chrome:** Scegliere la modalità **Incognito**.
   - **Safari:** Scegliere **File**, quindi **Nuova finestra privata**.

- Svuotare la cache ed eliminare i cookie di Internet:

   - **Microsoft Edge:** Aprire **Impostazioni** e selezionare **Privacy e servizi**. Seguire la procedura in **Cancella dati delle esplorazioni**. Verificare che le caselle di controllo **Cronologia esplorazioni**, **Cronologia download** e **Immagini e file memorizzati nella cache** siano selezionate, quindi selezionare **Elimina**.
   - **Chrome:** Scegliere **Impostazioni** e selezionare **Cancella dati di navigazione** in **Privacy e sicurezza**.

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>Viene eseguito l'accesso automaticamente come utente diverso

Questo problema può verificarsi se si usa più di un account utente in un browser Internet.

Per risolvere il problema, utilizzare uno dei seguenti metodi alternativi:

- Svuotare la cache ed eliminare i cookie di Internet.

   - **Microsoft Edge:** Aprire **Impostazioni** e selezionare **Privacy e servizi**. Seguire la procedura in **Cancella dati delle esplorazioni**. Verificare che le caselle di controllo **Cronologia esplorazioni**, **Cronologia download**, **Cookie** e **Immagini e file memorizzati nella cache** siano selezionate, quindi selezionare **Elimina**.
   - **Chrome:** Scegliere **Impostazioni** e selezionare **Cancella dati di navigazione** in **Privacy e sicurezza**.
- Ripristinare le impostazioni predefinite del browser.
- Usare la modalità di esplorazione privata per il browser. 
   - **Microsoft Edge:** Aprire **Impostazioni** (i tre puntini accanto all'immagine del profilo), selezionare **Nuova finestra InPrivate** e quindi individuare e accedere al [portale di Azure](https://portal.azure.com/) o al [Centro account di Azure](https://account.azure.com/Subscriptions). 
   - **Chrome:** Scegliere la modalità **Incognito**.
   - **Safari:** Scegliere **File**, quindi **Nuova finestra privata**.

## <a name="i-can-sign-in-but-i-see-the-error-no-subscriptions-found"></a>È possibile accedere, ma viene visualizzato il messaggio di errore Non sono state trovate sottoscrizioni

Questo problema si verifica se è stata effettuata una selezione nella directory errata o se l'account non ha autorizzazioni sufficienti.

**Scenario 1:** il messaggio di errore viene visualizzato quando si accede al [portale di Azure](https://portal.azure.com/)

Per risolvere il problema:

- Verificare che sia selezionata la directory di Azure corretta selezionando l'account nell'angolo in alto a destra.
- Se è selezionata la directory di Azure corretta, ma viene comunque visualizzato il messaggio di errore, richiedere che il proprio account venga [aggiunto come proprietario](./add-change-subscription-administrator.md).

**Scenario 2:** il messaggio di errore viene visualizzato quando si accede al [Centro account di Azure](https://account.windowsazure.com/Subscriptions)

Controllare se l'account usato è l'amministratore account. Per verificare chi è l'amministratore account, seguire questa procedura:

1.  Accedere alla [visualizzazione Sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1.  Scegliere la sottoscrizione da controllare, quindi selezionare **Impostazioni**.
1.  Selezionare **Proprietà**. L'amministratore account della sottoscrizione viene visualizzato nella casella **Amministratore account** .

## <a name="additional-help-resources"></a>Risorse della guida aggiuntive

Altri articoli sulla risoluzione dei problemi per la fatturazione e le sottoscrizioni di Azure

- [Carta rifiutata](./troubleshoot-declined-card.md)
- [Problemi di iscrizione alla sottoscrizione](./troubleshoot-azure-sign-up.md)
- [Non sono state trovate sottoscrizioni](./no-subscriptions-found.md)
- [Vista dei costi Enterprise disabilitata](./enterprise-mgmt-grp-troubleshoot-cost-view.md)
- [Documentazione sulla fatturazione di Azure](../index.yml)

## <a name="contact-us-for-help"></a>Contattare Microsoft per richiedere assistenza

In caso di domande o per assistenza, [creare una richiesta di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).