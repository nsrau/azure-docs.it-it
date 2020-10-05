---
title: Risolvere i problemi di iscrizione a un nuovo account nel portale di Azure o nel Centro account di Azure
description: Risoluzione di un problema che si verifica quando si prova a effettuare l'iscrizione a un nuovo account nel portale di Microsoft Azure o nel Centro account di Azure.
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 08/20/2020
ms.author: v-miegge
ms.openlocfilehash: 3e611e25288d52b0be93c76e8a3a28ce6d7d5088
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326607"
---
# <a name="troubleshoot-issues-when-you-sign-up-for-a-new-account-in-azure-portal-or-azure-account-center"></a>Risolvere i problemi di iscrizione a un nuovo account nel portale di Azure o nel Centro account di Azure

Si verifica un problema quando si prova a effettuare l'iscrizione a un nuovo account nel portale di Microsoft Azure o nel Centro account di Azure. In questa breve guida verrà illustrato il processo di iscrizione e verranno descritti alcuni problemi comuni in ogni passaggio.

> [!NOTE]
> Se si ha già un account esistente e si cercano indicazioni per la risoluzione dei problemi di accesso, vedere [Risolvere i problemi di accesso alla sottoscrizione di Azure](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue).

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la procedura di iscrizione, verificare quanto segue:

- Le informazioni del profilo dell'account Azure, tra cui indirizzo di posta elettronica di contatto, indirizzo e numero di telefono, sono corrette.
- Le informazioni della carta di credito sono corrette.
- Non è già presente un account Microsoft con le stesse informazioni.

## <a name="guided-walkthrough-of-azure-sign-up"></a>Procedura dettagliata per l'iscrizione ad Azure

L'esperienza di iscrizione ad Azure è costituita da quattro sezioni:

- Informazioni personali
- Verifica dell'identità mediante telefono
- Verifica dell'identità mediante smart card
- Contratto

Questa procedura dettagliata fornisce esempi delle informazioni corrette da specificare per iscriversi a un account Azure. Ogni sezione descrive anche alcuni problemi comuni e come risolverli.

## <a name="about-you"></a>Informazioni personali

![Informazioni personali](./media/troubleshoot-azure-sign-up/1.png)
 
### <a name="common-issues-and-solutions"></a>Problemi noti e soluzioni

#### <a name="you-see-the-message-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Viene visualizzato il messaggio "Non è possibile procedere con l'iscrizione a causa di un problema con l'account. Contattare il supporto tecnico per la fatturazione"

Per risolvere il problema, seguire questa procedura:

1.  Accedere al [Centro account di Azure](https://account.azure.com/Profile) usando le credenziali di amministratore account.
1.  Selezionare **Modifica dettagli**.
1.  Verificare che tutti i campi relativi all'indirizzo siano completi e validi.
1.  Quando ci si iscrive alla sottoscrizione di Azure, verificare che l'indirizzo di fatturazione per la registrazione della carta di credito corrisponda ai dati bancari.

Se si continua a ricevere questo messaggio, provare a usare un browser diverso per l'iscrizione.

Provare ad esempio a usare InPrivate Browsing.

#### <a name="free-trial-is-not-available"></a>La versione di valutazione gratuita non è disponibile

È stata usata una sottoscrizione di Azure in passato? Le condizioni per l'utilizzo di Azure prevedono un limite di una sola attivazione della versione di valutazione gratuita per un utente che non ha mai usato Azure in precedenza. Se quindi si è già usato un qualsiasi altro tipo di sottoscrizione di Azure, non è possibile attivare una versione di valutazione gratuita. Considerare l'iscrizione per una [sottoscrizione con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/).

#### <a name="you-see-the-message-you-are-not-eligible-for-an-azure-subscription"></a>Viene visualizzato il messaggio 'Non si è idonei per una sottoscrizione di Azure'

Per risolvere questo problema, effettuare le verifiche seguenti:

- Le informazioni fornite per il profilo dell'account di Azure, ad esempio indirizzo di posta elettronica di contatto, indirizzo e numero di telefono, sono corrette.
- Le informazioni della carta di credito sono corrette.
- Non è già presente un account Microsoft che usa le stesse informazioni.

#### <a name="you-see-the-message-your-current-account-type-is-not-supported"></a>Viene visualizzato il messaggio 'Il tipo di account corrente non è supportato'

Questo problema può verificarsi se l'account è registrato in una [directory di Azure AD non gestita](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup) e non è presente nella directory di Azure AD dell'organizzazione.
Per risolvere questo problema, iscrivere l'account Azure usando un altro account o assumere la proprietà della directory di Azure AD non gestita. Per altre informazioni, vedere [Acquisire la proprietà di una directory non gestita come amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover).

## <a name="identity-verification-by-phone"></a>Verifica dell'identità mediante telefono

![Verifica dell'identità mediante telefono](./media/troubleshoot-azure-sign-up/2.png)
 
Quando si riceve l'SMS o la chiamata, immettere il codice ricevuto nella casella di testo.

### <a name="common-issues-and-solutions"></a>Problemi noti e soluzioni

#### <a name="no-verification-text-message-or-phone-call"></a>Nessun SMS o telefonata di verifica

Anche se il processo di verifica dell'iscrizione è in genere rapido, il recapito del codice di verifica potrebbe richiedere fino a quattro minuti.

Altri suggerimenti:

- È possibile usare qualsiasi numero di telefono per la verifica, purché soddisfi i requisiti. Il numero di telefono immesso per la verifica non viene archiviato come numero di contatto per l'account.
  - Non è possibile usare un numero di telefono VoIP (Voice-over-IP) per il processo di verifica tramite telefono.
  - Assicurarsi che il telefono possa ricevere chiamate o SMS da un numero degli Stati Uniti.
- Verificare il numero di telefono immesso, incluso il prefisso internazionale selezionato nel menu a discesa.
- Se il telefono non può ricevere SMS, provare l'opzione **Chiama**.

## <a name="identity-verification-by-card"></a>Verifica dell'identità mediante smart card

![Verifica dell'identità mediante smart card](./media/troubleshoot-azure-sign-up/3.png)
 
### <a name="common-issues-and-solutions"></a>Problemi noti e soluzioni

#### <a name="credit-card-declined-or-not-accepted"></a>Carta di credito rifiutata o non accettata

Le carte di credito o debito virtuali o prepagate non sono accettate come opzione di pagamento per le sottoscrizioni di Azure. Per informazioni sugli altri motivi per cui la carta può essere rifiutata, vedere [Risolvere un problema di carta rifiutata all'iscrizione ad Azure](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card).

#### <a name="credit-card-form-doesnt-support-my-billing-address"></a>Il modulo della carta di credito non supporta il mio indirizzo di fatturazione

L'indirizzo di fatturazione deve trovarsi nel paese selezionato nella sezione **Informazioni personali**. Verificare di avere selezionato il paese corretto.

#### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>L'indicatore di stato si blocca nella sezione Verifica dell'identità mediante smart card

Per completare la verifica dell'identità mediante smart card, è necessario che il browser consenta i cookie di terze parti.

Seguire questa procedura per aggiornare le impostazioni relative ai cookie del browser.

1. Aggiornare le impostazioni dei cookie.
   - Se si usa **Chrome**:
     - Selezionare **Impostazioni** > **Mostra impostazioni avanzate** > **Privacy** > **Impostazioni contenuti**. Deselezionare **Blocca cookie di terze parti e dati dei siti**.

   - Se si usa **Microsoft Edge**:
     - Selezionare **Impostazioni** > **Visualizza impostazioni avanzate** > **Cookie** > **Non bloccare i cookie**.

1. Aggiornare la pagina di iscrizione ad Azure e controllare se il problema è stato risolto.
1. Se il problema persiste, chiudere e riavviare il browser, quindi riprovare.

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Viene visualizzato un addebito sull'account di valutazione gratuita

Dopo l'iscrizione, si potrebbe notare una piccola trattenuta di verifica temporanea sulla carta di credito. Questa trattenuta viene rimossa entro tre-cinque giorni. Se si è preoccupati per la gestione dei costi, leggere altre informazioni in [Analizzare gli addebiti imprevisti](../understand/analyze-unexpected-charges.md).

## <a name="agreement"></a>Contratto

Completare il contratto.

## <a name="other-issues"></a>Altri problemi

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Non è possibile attivare un piano con vantaggi di Azure, ad esempio MSDN, BizSpark, BizSparkPlus o MPN

Assicurarsi di usare le credenziali di accesso corrette. Controllare quindi il programma dei vantaggi per verificare se si è idonei.
- MSDN
  - Verificare lo stato di idoneità nella [pagina dell'account MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  - Se non è possibile verificare lo stato, contattare i [centri del Servizio clienti per abbonamenti MSDN](https://msdn.microsoft.com/library/aa493452.aspx).
- Microsoft for Startups
  - Accedere al [portale di Microsoft for Startups](https://startups.microsoft.com/#start-two) per verificare il proprio stato di idoneità per Microsoft for Startups.
  - Se non è possibile verificare lo stato, è possibile ottenere assistenza nei [forum di Microsoft for Startups](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN
  - Accedere al [portale di MPN](https://mspartner.microsoft.com/Pages/Locale.aspx) per verificare lo stato di idoneità. È possibile usufruire di vantaggi aggiuntivi se si hanno le [competenze Cloud Platform](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx)appropriate.
  - Se non è possibile verificare lo stato, contattare il [supporto tecnico di MPN](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).


### <a name="cant-activate-new-azure-in-open-subscription"></a>Non è possibile attivare una nuova sottoscrizione di Azure in Open

Per creare una sottoscrizione di Azure in Open, è necessaria una chiave di attivazione del servizio online (OSA, Online Service Activation) valida a cui è associato almeno un token di Azure in Open. Se non è disponibile una chiave di attivazione del servizio online, contattare uno dei partner Microsoft elencati in [Microsoft Pinpoint](https://pinpoint.microsoft.com/).

## <a name="additional-help-resources"></a>Risorse della guida aggiuntive

Altri articoli sulla risoluzione dei problemi per la fatturazione e le sottoscrizioni di Azure

- [Carta rifiutata](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)
- [Problemi di accesso alla sottoscrizione](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue)
- [Non sono state trovate sottoscrizioni](https://docs.microsoft.com/azure/cost-management-billing/manage/no-subscriptions-found)
- [Vista dei costi Enterprise disabilitata](https://docs.microsoft.com/azure/cost-management-billing/manage/enterprise-mgmt-grp-troubleshoot-cost-view)

## <a name="contact-us-for-help"></a>Contattare Microsoft per richiedere assistenza

In caso di domande o per assistenza, [creare una richiesta di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="find-out-more-about-azure-cost-management"></a>Altre informazioni su Gestione costi di Azure

- [Documentazione di Gestione dei costi e fatturazione di Azure](https://docs.microsoft.com/azure/cost-management-billing)
