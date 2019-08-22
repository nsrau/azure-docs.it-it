---
title: Risolvere i problemi di iscrizione ad Azure
description: Risoluzione di un problema quando si tenta di effettuare l'iscrizione per un nuovo account nel centro account portale di Microsoft Azure.
author: v-miegge
manager: na
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: 57dc9c05778563bc0c5df1a8ac8895b51008b01f
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657073"
---
# <a name="troubleshoot-azure-sign-up"></a>Risolvere i problemi di iscrizione ad Azure

È possibile che si verifichi un problema quando si tenta di effettuare l'iscrizione per un nuovo account nel portale di Microsoft Azure o nel centro account di Azure. Prima di risolvere il problema, verificare prima di tutto quanto segue:

- Le informazioni fornite per il profilo dell'account di Azure (inclusi indirizzo di posta elettronica di contatto, indirizzo via e numero di telefono) sono corrette.
- Le informazioni sulla carta di credito sono corrette.
- Non si dispone già di un account Microsoft con le stesse informazioni.

## <a name="resolutions"></a>Risoluzioni

Per risolvere eventuali errori, selezionare il problema che si verifica quando si tenta di eseguire l'iscrizione ad Azure.

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Errore: *Non è possibile procedere con l'iscrizione a causa di un problema con l'account. Contattare il supporto per la fatturazione.*

Per risolvere il problema, seguire questa procedura:

1. Accedere al [Centro account di Azure](https://account.azure.com/Profile) usando le credenziali di amministratore account.

2. Selezionare **Modifica dettagli**.

3. Assicurarsi che tutti i campi relativi all'indirizzo siano completati e validi.

4. Quando ci si iscrive alla sottoscrizione di Azure, assicurarsi che l'indirizzo di fatturazione per la registrazione della carta di credito corrisponda ai record bancari.

Se si continua a ricevere il messaggio di errore, provare ad iscriversi usando un browser diverso.

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>L'indicatore di stato si blocca nella sezione *Verifica identità per scheda* .

Per completare la verifica dell'identità mediante smart card, è necessario che il browser consenta i cookie di terze parti.

![Verifica dell'identità mediante smart card](./media/billing-troubleshoot-azure-sign-up/identify-verification-by-card.png)
 
Seguire questa procedura per aggiornare le impostazioni relative ai cookie del browser.

1. Se si usa Chrome, selezionare **Impostazioni** > **Mostra impostazioni avanzate** > **privacy** > **impostazioni contenuto**. Deselezionare **blocca i cookie di terze parti e i dati del sito**.

2. Se si usa Microsoft Edge, selezionare **Impostazioni** > Visualizza**cookie** > **Impostazioni** > avanzate**non bloccare i cookie**.

3. Aggiornare la pagina di iscrizione ad Azure, quindi controllare se il problema è stato risolto.

4. Se l'aggiornamento non ha risolto il problema, chiudere e riavviare il browser, quindi riprovare.

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>Il modulo della carta di credito non supporta il mio indirizzo di fatturazione

L'indirizzo di fatturazione deve trovarsi nel paese selezionato nella sezione **informazioni su** . Assicurarsi di selezionare il paese corretto.

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Assenza di messaggi di testo o chiamate durante la verifica dell'account di sottoscrizione

Anche se il processo è in genere rapido, potrebbero essere necessari fino a quattro minuti per il recapito di un codice di verifica. Il numero di telefono immesso per la verifica non viene archiviato come numero di contatto per l'account.

Altri suggerimenti:

- Per il processo di verifica del telefono non è possibile usare un numero di telefono VoiP (Voice-over-IP).
- Verificare il numero di telefono immesso, incluso il codice paese selezionato nel menu a discesa.
- Se il telefono non può ricevere SMS, provare l'opzione **Chiama**.
- Verificare che il telefono possa ricevere chiamate o messaggi SMS da un numero di telefono basato su Stati Uniti.

Quando si riceve l'SMS o la chiamata telefonica, immettere il codice ricevuto nella casella di testo.

### <a name="credit-card-declined-or-not-accepted"></a>Carta di credito rifiutata o non accettata

Le carte di credito o di debito virtuali o prepagate non sono accettate come pagamento per le sottoscrizioni di Azure. Per vedere che altro potrebbe causare il rifiuto della carta, vedere la carta di debito o la carta di credito [rifiutata al momento dell'iscrizione ad Azure](https://support.microsoft.com/help/4042960).

### <a name="free-trial-is-not-available"></a>La versione di valutazione gratuita non è disponibile

È stata usata una sottoscrizione di Azure in passato? Le condizioni per l'utilizzo di Azure prevedono un limite di una sola attivazione della versione di valutazione gratuita per un utente che non ha mai usato Azure in precedenza. Se quindi si è già usato un qualsiasi altro tipo di sottoscrizione di Azure, non è possibile attivare una versione di valutazione gratuita. Considerare l'iscrizione per una [sottoscrizione con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/).

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Ho visto un addebito sul mio account della versione di valutazione gratuita

Dopo l'iscrizione, è possibile che venga visualizzata una piccola verifica in possesso dell'account della carta di credito. Questa operazione viene rimossa entro tre o cinque giorni. Se si è preoccupati della gestione dei costi, leggere altre informazioni per [evitare costi imprevisti](billing-getting-started.md).

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Non è possibile attivare un piano con vantaggi di Azure, ad esempio MSDN, BizSpark, BizSparkPlus o MPN

Assicurarsi di usare le credenziali di accesso corrette. Quindi, controllare il programma benefit per assicurarsi di essere idonei.

- MSDN 
  - Verificare lo stato di idoneità nella [pagina dell'account MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  - Se non è possibile verificare lo stato, contattare i [centri del servizio clienti per abbonamenti MSDN](https://msdn.microsoft.com/library/aa493452.aspx).
- Microsoft for Startups
  - Accedere al [portale Microsoft for startups](https://startups.microsoft.com/#start-two) per verificare lo stato di idoneità per Microsoft per le startup.
  - Se non è possibile verificare lo stato, è possibile ottenere assistenza nei [Forum di Microsoft for startups](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN 
  - Accedere al [portale MPN](https://mspartner.microsoft.com/Pages/Locale.aspx) per verificare lo stato di idoneità. È possibile usufruire di vantaggi aggiuntivi se si hanno le [competenze Cloud Platform](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx)appropriate.
  - Se non è possibile verificare lo stato, contattare il [supporto di MPN](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).

### <a name="cant-activate-new-azure-in-open-subscription"></a>Non è possibile attivare la nuova sottoscrizione di Azure in Open

Per creare una sottoscrizione di Azure in Open, è necessario disporre di una chiave di attivazione del servizio online (OSA) valida con almeno un token di Azure in Open associato. Se non si dispone di una chiave OSA, contattare uno dei partner Microsoft elencati in [Microsoft Pinpoint](http://pinpoint.microsoft.com/).

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>Errore: *Non si è idonei per una sottoscrizione di Azure*

Per risolvere questo problema, verificare che siano soddisfatte le seguenti voci:

- Le informazioni fornite per il profilo dell'account di Azure (inclusi indirizzo di posta elettronica di contatto, indirizzo via e numero di telefono) sono corrette.
- Le informazioni sulla carta di credito sono corrette.
- Non si dispone già di un account Microsoft che utilizza le stesse informazioni.

### <a name="error-your-current-account-type-is-not-supported"></a>Errore: *Il tipo di account corrente non è supportato*

Questo problema può verificarsi se l'account è registrato in una [directory di Azure ad non gestita](../active-directory/users-groups-roles/directory-self-service-signup.md)e non è presente nella directory di Azure ad dell'organizzazione. 

Per risolvere questo problema, iscrivere l'account Azure usando un altro account o assumere la proprietà della directory di Active Directory non gestita. Per altre informazioni, vedere [assumere una directory non gestita come amministratore in Azure Active Directory](../active-directory/users-groups-roles/domains-admin-takeover.md).
 
## <a name="additional-help-resources"></a>Risorse della guida aggiuntive

Altri articoli sulla risoluzione dei problemi per la fatturazione e le sottoscrizioni di Azure

- [Carta rifiutata](billing-troubleshoot-declined-card.md)
- [Problemi di accesso alla sottoscrizione](billing-troubleshoot-sign-in-issue.md)
- [Non sono state trovate sottoscrizioni](billing-no-subscriptions-found.md)
- [Vista dei costi Enterprise disabilitata](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Contattaci per assistenza

In caso di domande o per assistenza, [creare una richiesta di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

- [Documentazione sulla fatturazione di Azure](index.md)
