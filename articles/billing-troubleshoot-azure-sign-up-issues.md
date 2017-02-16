---
title: Risolvere i problemi relativi all&quot;iscrizione ad Azure | Documentazione Microsoft
description: Descrive come eseguire la risoluzione di alcuni problemi comuni relativi all&quot;iscrizione ad Azure.
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: a0907da1-cb2d-41d1-a97f-43841fabe355
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 01/24/2016
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 73c4ff6fdbde09dd08d1bcacf0ec87c7fa43f3b2
ms.openlocfilehash: 7ded94f432f8e61153ee8dfbf2508b288a4ad5fd


---
# <a name="i-cant-sign-up-for-azure"></a>Non è possibile eseguire la sottoscrizione di Azure
Se non è possibile eseguire la sottoscrizione di Azure, esistono diversi aspetti da controllare per la risoluzione del problema.

## <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>L'indicatore di stato si blocca nella sezione "Verifica dell'identità tramite smart carta"

Nella procedura di sottoscrizione ad Azure è disponibile una sezione denominata "Verifica dell'identità tramite smart carta". Se l'indicatore di stato si blocca:

![Schermata della sezione Verifica dell'identità tramite smart card bloccata durante la sottoscrizione](./media/billing-troubleshoot-azure-sign-up-issues/identity-verification-hangs.PNG)

Questo problema si verifica quando nel browser sono bloccati i cookie di terze.

### <a name="suggestion"></a>Suggerimento

1. Consentire i cookie di terze parti nelle impostazioni del browser.
  * In Edge, passare a Impostazioni -> Visualizza impostazioni avanzate -> Cookie e selezionare "Non bloccare i cookie".
  * In Chrome, passare a Impostazioni -> Mostra impostazioni avanzate -> Privacy-> Impostazioni contenuti e deselezionare l'opzione "Blocca cookie di terze parti e dati di siti".
2. Aggiornare la pagina di sottoscrizione di Azure e controllare se il problema è risolto.
3. Se il problema persiste, chiudere e riavviare il browser, quindi riprovare.

## <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Assenza di messaggi di testo o chiamate durante la verifica dell'account di accesso
* Verificare che il numero di telefono possa ricevere SMS.
* Verificare il numero di telefono immesso, inclusa la selezione del prefisso internazionale nel menu a discesa.
* Assicurarsi che il telefono possa ricevere messaggi SMS se si sceglie l'opzione "Invia SMS" o le telefonate se si sceglie l'alternativa "Chiama".
* Se si usa un telefono cellulare, verificare che il segnale della rete cellulare sia buono.
* Attendere fino a&4; minuti per consentire al sistema di messaggistica di inviare il codice di testo se si sceglie l'opzione "Invia SMS".
* Quando si riceve l'SMS, inserire il codice nella casella di testo e fare clic sul pulsante di verifica per continuare.

### <a name="suggestions"></a>Suggerimenti
* se non si ricevono messaggi di testo (SMS) nel telefono, usare il metodo di verifica alternativo "chiama utente".
* Se il passaggio di verifica telefonica non riesce né tramite SMS, né tramite il metodo "chiama utente", usare un altro numero di telefono.
* Non è possibile usare un numero di telefono VoIP per il processo di verifica tramite telefono.

> [!NOTE]
> È possibile modificare il numero di telefono preferito in un secondo momento [aggiornando le informazioni del profilo](billing-how-to-change-azure-account-profile.md).
>
>

## <a name="credit-card-declined-or-not-accepted"></a>Carta di credito rifiutata o non accettata
Assicurarsi che il metodo di pagamento indicato durante la procedura di iscrizione sia supportato per le attivazioni o i pagamenti di Azure.

* Le carte di credito/debito virtuali e prepagate non sono accettate.
* I fornitori di carte di credito/debito accettati variano in base al paese dell'account.

### <a name="suggestion"></a>Suggerimento
Per le cause più comuni dei problemi relativi all'iscrizione tramite carta di credito o di debito, vedere l'articolo [La carta di credito o di debito viene rifiutata quando si tenta di eseguire l'iscrizione ad Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Non è possibile attivare un piano con vantaggi di Azure, ad esempio MSDN, BizSpark, BizSparkPlus o MPN
Verificare tramite il canale del programma dei vantaggi se si è idonei al piano scelto:

* MSDN
  * Verificare lo stato di idoneità nella [pagina dell'account MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  * Se non è possibile verificare lo stato, contattare i [centri del Servizio clienti per abbonamenti MSDN](https://msdn.microsoft.com/subscriptions/contactus.aspx)
* MPN
  * Accedere al [portale di MPN](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx) e verificare lo stato di idoneità. È possibile usufruire di vantaggi aggiuntivi se si hanno le [competenze Cloud Platform](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx)appropriate.
  * Se non è possibile verificare lo stato, contattare il [supporto tecnico di MPN](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx).
* BizSpark
  * Accedere al [portale di BizSpark](https://www.microsoft.com/bizspark/default.aspx#start-two) e verificare lo stato di idoneità per BizSpark e BizSpark Plus.
  * Se non è possibile verificare lo stato, contattare il supporto di BizSpark con un messaggio di posta elettronica facendo clic su [Contatta il team di BizSpark](mailto:bizspark@microsoft.com?subject=BizSpark%20Support&body=Thank%20you%20for%20contacting%20BizSpark.%20Please%20provide%20as%20much%20of%20the%20following%20information%20as%20possible,%20as%20it%20will%20help%20expedite%20our%20response%20to%20you.%0aContact%20name:%0aStartup%20name:%0aMicrosoft%20Account/Live%20ID:%0aSpecific%20description%20of%20issue%20experienced%20or%20question:%0a%0aThank%20you,%0a%0aThe%20BizSpark%20Team)

### <a name="suggestion"></a>Suggerimento
Se si è provato ad attivare una nuova sottoscrizione tramite i vantaggi e sono stati riscontrati errori durante l'iscrizione, verificare se l'installazione della sottoscrizione è stata completata nella [pagina di sottoscrizione di Azure](http://account.windowsazure.com/Subscriptions). Potrebbero essere necessari alcuni minuti prima che la sottoscrizione risulti attiva. Quando la sottoscrizione è attivata, si riceverà un messaggio di posta elettronica. Se lo stato della sottoscrizione resta in sospeso per più di quattro minuti, [contattare il supporto tecnico di Azure](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409) per richiedere assistenza.

## <a name="cant-activate-new-azure-in-open-subscription"></a>Non è possibile attivare una nuova sottoscrizione di Azure in Open
È necessaria una chiave di attivazione del servizio online (OSA, Online Service Activation) a cui è associato almeno un token di Azure in Open per attivare una nuova sottoscrizione di Azure in Open.

### <a name="suggestion"></a>Suggerimento
Se non è disponibile una chiave di attivazione del servizio online, contattare uno dei partner Microsoft elencati in [Microsoft Pinpoint](http://pinpoint.microsoft.com/).

## <a name="cant-activate-azure-free-trial"></a>Non è possibile attivare una versione di valutazione gratuita di Azure
È stata usata una sottoscrizione di Azure in passato? Le condizioni per l'utilizzo di Azure prevedono un limite di una sola attivazione della versione di valutazione gratuita per un utente che non ha mai usato Azure in precedenza. Se quindi si è già usato un qualsiasi altro tipo di sottoscrizione di Azure, non è possibile attivare una versione di valutazione gratuita.

### <a name="suggestion"></a>Suggerimento
* Se è stata attivata una sottoscrizione di Azure in passato e l'attivazione della versione di valutazione gratuita non riesce, prendere in considerazione una sottoscrizione con pagamento in base al consumo.
* Verificare se si ha diritto a un'offerta con vantaggi. Per altre informazioni, vedere la [pagina dettagli dell'offerta Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/). I piani con vantaggi richiedono prerequisiti specifici.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.



<!--HONumber=Jan17_HO1-->


