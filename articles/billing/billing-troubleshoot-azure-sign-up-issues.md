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
ms.date: 03/21/2017
ms.author: cjiang
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: a50b4fb4b19acf270aaf94fea613e745ec2af649
ms.lasthandoff: 03/22/2017

---
# <a name="troubleshoot-sign-up-issues-for-azure"></a>Risolvere i problemi di iscrizione per Azure
Se non si riesce a iscriversi ad Azure, usare i suggerimenti in questo articolo per risolvere i problemi più comuni. In caso di problemi con la carta di credito durante l'iscrizione, vedere [La carta di credito o di debito viene rifiutata quando si tenta di eseguire l'iscrizione ad Azure](billing-credit-card-fails-during-azure-sign-up.md). Se si ha un account Azure, ma non è possibile accedere, vedere [Non è possibile accedere per gestire la sottoscrizione di Azure](billing-cannot-login-subscription.md).

## <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>L'indicatore di stato si blocca nella sezione "Verifica dell'identità mediante smart card"

Per completare la verifica dell'identità mediante smart card, è necessario che il browser consenta i cookie di terze parti.

![Schermata della sezione Verifica dell'identità tramite smart card bloccata durante la sottoscrizione](./media/billing-troubleshoot-azure-sign-up-issues/identity-verification-hangs.PNG)

Seguire questa procedura per aggiornare le impostazioni relative ai cookie del browser.

1. Se si usa Chrome, passare a **Impostazioni** > **Mostra impostazioni avanzate** > **Privacy** > **Impostazioni contenuti**. Deselezionare **Blocca cookie di terze parti e dati dei siti**.
2. Se si usa Edge, passare a **impostazioni** > **Visualizza impostazioni avanzate** > **Cookie**. Selezionare **Non bloccare i cookie**.
3. Aggiornare la pagina di sottoscrizione di Azure e controllare se il problema è risolto.
4. Se il problema persiste, chiudere e riavviare il browser, quindi riprovare.

## <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Assenza di messaggi di testo o chiamate durante la verifica dell'account di sottoscrizione
Se si seleziona **Invia messaggi di testo** potrebbero occorrere fino a quattro minuti per il recapito del codice di testo al telefono. Per la verifica dell'identità, immettere ovviamente un numero di telefono che può ricevere messaggi SMS o, per l'opzione **Chiama**, che può ricevere chiamate. Il numero di telefono immesso viene usato solo per la verifica dell'identità e non viene archiviato come un numero di contatto per l'account.

Di seguito sono riportati alcuni suggerimenti:
* Non è possibile usare un numero di telefono VoIP per il processo di verifica tramite telefono.
* Verificare il numero di telefono immesso, incluso il prefisso internazionale selezionato nel menu a discesa.
* Se il telefono non può ricevere SMS, usare l'opzione **Chiama**.
* Se il passaggio di verifica telefonica non riesce né con l'opzione **Invia messaggio di testo**, né tramite il metodo **Chiama**, usare un altro numero di telefono.

Quando si riceve l'SMS o la chiamata, immettere il codice ricevuto nella casella di testo.

## <a name="credit-card-declined-or-not-accepted"></a>Carta di credito rifiutata o non accettata
Le carte di credito o debito virtuali o prepagate non sono accettate come opzione di pagamento per l'iscrizione ad Azure. Per informazioni sugli altri motivi per cui la carta può essere rifiutata, vedere [La carta di debito o credito viene rifiutata al momento dell'iscrizione ad Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="free-trial-is-not-available"></a>"La prova gratuita non è disponibile"
È stata usata una sottoscrizione di Azure in passato? Le condizioni per l'utilizzo di Azure prevedono un limite di una sola attivazione della versione di valutazione gratuita per un utente che non ha mai usato Azure in precedenza. Se quindi si è già usato un qualsiasi altro tipo di sottoscrizione di Azure, non è possibile attivare una versione di valutazione gratuita. Considerare l'iscrizione per una [sottoscrizione con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/).

## <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Non è possibile attivare un piano con vantaggi di Azure, ad esempio MSDN, BizSpark, BizSparkPlus o MPN
Assicurarsi di usare le credenziali di accesso corrette. Controllare quindi il programma dei vantaggi per assicurarsi di averne diritto. 

* MSDN
  * Verificare lo stato di idoneità nella [pagina dell'account MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  * Se non è possibile verificare lo stato, contattare i [centri del Servizio clienti per abbonamenti MSDN](https://msdn.microsoft.com/subscriptions/contactus.aspx)
* BizSpark
  * Accedere al [portale di BizSpark](https://www.microsoft.com/bizspark/default.aspx#start-two) e verificare lo stato di idoneità per BizSpark e BizSpark Plus.
  * Se non è possibile verificare lo stato, è possibile [contattare il team di BizSpark](mailto:bizspark@microsoft.com?subject=BizSpark%20Support&body=Thank%20you%20for%20contacting%20BizSpark.%20Please%20provide%20as%20much%20of%20the%20following%20information%20as%20possible,%20as%20it%20will%20help%20expedite%20our%20response%20to%20you.%0aContact%20name:%0aStartup%20name:%0aMicrosoft%20Account/Live%20ID:%0aSpecific%20description%20of%20issue%20experienced%20or%20question:%0a%0aThank%20you,%0a%0aThe%20BizSpark%20Team)
* MPN
  * Accedere al [portale di MPN](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx) e verificare lo stato di idoneità. È possibile usufruire di vantaggi aggiuntivi se si hanno le [competenze Cloud Platform](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx)appropriate.
  * Se non è possibile verificare lo stato, contattare il [supporto tecnico di MPN](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx).

## <a name="cant-activate-new-azure-in-open-subscription"></a>Non è possibile attivare una nuova sottoscrizione di Azure in Open
Per creare una sottoscrizione di Azure in Open, è necessaria una chiave di attivazione del servizio online (OSA, Online Service Activation) valida a cui è associato almeno un token di Azure in Open. Se non è disponibile una chiave di attivazione del servizio online, contattare uno dei partner Microsoft elencati in [Microsoft Pinpoint](http://pinpoint.microsoft.com/).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

