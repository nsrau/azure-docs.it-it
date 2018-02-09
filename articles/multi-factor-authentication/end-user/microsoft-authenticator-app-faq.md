---
title: Guida e supporto tecnico per l'app Microsoft Authenticator | Microsoft Docs
description: Elenca le domande frequenti e le risposte correlate all'app Microsoft Authentication e ad Azure Multi-Factor Authentication.
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: 2c109a53866f9fff8c61a157cf1194fe4dd6e1ac
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="microsoft-authenticator-app-faq"></a>Domande frequenti sull'app Microsoft Authenticator

Questo articolo risponde a domande comuni che Microsoft riceve sull'app Microsoft Authenticator. Se non viene visualizzata una risposta alla domanda, accedere al [forum dell'app Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp). È disponibile anche un'altra sezione dedicata alle domande frequenti su una funzionalità specifica relativa all'app, le [domande frequenti sull'accesso con il telefono](microsoft-authenticator-app-phone-signin-faq.md).

L'app Microsoft Authenticator ha sostituito l'app Azure Authenticator ed è consigliata quando si usa Azure Multi-Factor Authentication. L'app Microsoft Authenticator è disponibile per [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) e [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="what-are-the-codes-in-the-app-for-why-does-the-number-keep-counting-down"></a>A cosa servono i codici nell'app? Perché il numero continua a diminuire?

Quando si apre l'app Microsoft Authenticator vengono visualizzati gli account aggiunti e un numero di sei o otto cifre vicino a ognuno di essi. Può essere presente anche un timer di trenta secondi che procede a ritroso.

Questi codici vengono usati quando si accede al proprio account. Dopo aver immesso il nome utente e la password, potrebbe essere necessario immettere un codice di verifica. Aprire l'app Microsoft Authenticator e copiare il codice visualizzato al momento. Immettere il codice nella pagina di accesso per terminare.

I codici cambiano ogni 30 secondi in modo che non venga usato lo stesso codice due volte. A differenza di una password, che deve essere nota all'utente, l'idea è che solo un utente con accesso al telefono possa entrare in possesso del codice di verifica.

I codici non richiedono l'uso di Internet e non creano traffico dati, pertanto non è necessario che il servizio telefonico sia attivo per eseguire l'accesso. Quando viene chiusa, l'app non continua a essere eseguita in background e quindi non scarica la batteria. È possibile chiudere l'app e ignorarla fino al successivo accesso.  

### <a name="i-only-get-notifications-when-i-have-the-app-open-if-the-app-isnt-open-i-dont-get-any-notifications"></a>Ricevo le notifiche solo quando l'app è aperta. Se l'applicazione non è aperta, non ricevo alcuna notifica.

Se si ricevono le notifiche, ma non si sentono rumori o vibrazioni nonostante la suoneria sia attivata, verificare innanzitutto le impostazioni dell'applicazione. Abilitare l'app a emettere suoni o vibrazioni quando riceve una notifica.

Se non si ricevono le notifiche, controllare quanto segue:

- Il telefono è in modalità non disturbare o non interattiva? Tale modalità può impedire l'invio di notifiche da parte delle app.
- Si ricevono notifiche da altre app? In questo caso, potrebbe esserci un problema con le connessioni di rete sul telefono o con il canale di notifiche Android o Apple. Nel primo caso è possibile risolvere il problema con le impostazioni del telefono, mentre nel secondo caso è possibile che sia necessario contattare il provider di servizi per assistenza specifica.
- Si ricevono notifiche per alcuni account sull'app, ma non per altri? In caso affermativo, rimuovere l'account problematico dall'app e aggiungerlo nuovamente per abilitare le notifiche push.

Se i problemi persistono anche dopo aver provato questi suggerimenti, inviare i log per la diagnostica. Andare alle impostazioni dell'app e quindi selezionare **Guida e commenti** e **Invia log**. Accedere quindi al [forum delle app di Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) per illustrare il problema e descrivere i passaggi provati fino ad ora.

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>Sto già usando l'app Microsoft Authenticator per i codici di verifica. Come posso attivare le notifiche push con un clic?
L'approvazione dell'accesso tramite notifica push è disponibile solo per account Microsoft personali o account Microsoft aziendali o dell'istituto di istruzione, non per account di terze parti come Google o Facebook. Se si ha un account Microsoft aziendale o dell'istituto di istruzione, tuttavia, l'organizzazione può scegliere di disabilitare questa opzione.

Se si usa un account Microsoft per l'account personale e si vogliono attivare le notifiche push, è necessario aggiungere nuovamente l'account. Registrare nuovamente il dispositivo con il proprio account e impostare le notifiche push.  

Se si usa Microsoft Authenticator per l'account aziendale o dell'istituto di istruzione, l'organizzazione decide se consentire le notifiche con un clic.

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>Le notifiche push con un clic funzionano con gli account non Microsoft?
No, le notifiche push funzionano solo con gli account Microsoft e gli account Azure Active Directory. Se l'azienda o l'istituto di istruzione usa account di Azure AD, è possibile che disabilitino questa funzionalità.  

### <a name="i-restored-my-device-from-a-backup-and-my-account-codes-are-missing-or-not-working-what-happened"></a>Ho ripristinato il mio dispositivo da una copia di backup e i codici del mio account mancano o non funzionano. Che cosa è successo?
A scopo di sicurezza, gli account non vengono ripristinati dalle copie di backup dell'app.  Dopo aver ripristinato l'app, eliminare gli account e aggiungerli di nuovo.

### <a name="i-got-a-new-device-how-do-i-remove-the-microsoft-authenticator-app-from-my-old-device-and-move-to-the-new-one"></a>Ho un nuovo dispositivo. Come si rimuove l'app Microsoft Authenticator dal dispositivo precedente per spostarla su quello nuovo?
Se si aggiunge l'app Microsoft Authenticator a un nuovo dispositivo, questa non viene rimossa automaticamente da tutti gli altri dispositivi. Per gestire i dispositivi che sono configurati per l'account, visitare lo stesso sito Web che si usa per gestire la verifica in due passaggi e scegliere di rimuovere le app precedenti.

Per gli account Microsoft personali, il sito Web è la pagina della [sicurezza dell'account](https://account.microsoft.com/security). Per gli account Microsoft aziendali o dell'istituto di istruzione, il sito Web può essere [Mie app](https://myapps.microsoft.com) o un portale personalizzato configurato dall'organizzazione.

### <a name="how-do-i-remove-an-account-from-the-app"></a>Come rimuovere un account dall'app?
* iOS: dalla schermata principale, scorrere rapidamente verso sinistra un riquadro dell'account. Selezionare **Elimina**.
* Windows Phone: dalla schermata principale, selezionare il pulsante di menu, quindi **Modifica account**. Toccare la **X** accanto al nome dell'account.
* Android: dalla schermata principale, selezionare il pulsante di menu e quindi **Modifica account**. Toccare la **X** accanto al nome dell'account.

Se si ha un dispositivo registrato con l'organizzazione, potrebbe essere necessario completare un passaggio aggiuntivo per rimuovere l'account. In questi dispositivi l'app Microsoft Authenticator viene registrata automaticamente come amministratore del dispositivo. Se si vuole disinstallare completamente l'app, è necessario prima annullare la registrazione dell'app nelle impostazioni relative.

### <a name="why-does-the-app-request-so-many-permissions"></a>Perché l'app richiede così tante autorizzazioni?
Di seguito è riportato l'elenco completo delle autorizzazioni che potrebbero essere richieste e la descrizione di come vengono usate nell'app. Le autorizzazioni specifiche visualizzate dipendono dal tipo di telefono in uso.

* **Fotocamera**: la fotocamera viene usata per effettuare la scansione dei codici QR quando si aggiunge un account aziendale, dell'istituto di istruzione o non Microsoft.
* **Contatti e telefono**: quando si accede con l'account Microsoft personale, il processo viene semplificato mediante la ricerca di account esistenti usati sul telefono.
* **SMS**: quando si accede con l'account Microsoft personale per la prima volta, è necessario assicurarsi che il numero di telefono corrisponda a quello registrato. Viene inviato un SMS al telefono su cui è stata scaricata l'app. Il messaggio contiene un codice di verifica da 6 a 8 cifre. Anziché richiedere di trovare il codice e immetterlo nell'app, viene inserito nel testo del messaggio.
* **Sovrapposizione su altre app**: quando si riceve una notifica per la verifica dell'identità, tale notifica viene visualizzata su qualsiasi altra applicazione in esecuzione al momento.
* **Ricezione di dati da Internet**: questa autorizzazione è necessaria per l'invio di notifiche.
* **Impedire la sospensione del telefono**: se si registra il dispositivo con l'organizzazione, è possibile modificare questo criterio sul telefono.
* **Controllo vibrazione**: è possibile scegliere se si vuole che il dispositivo vibri alla ricezione di una notifica per la verifica dell'identità.
* **Uso dell'hardware per la lettura delle impronte digitali**: alcuni account aziendali e dell'istituto di istruzione richiedono un PIN aggiuntivo al momento della verifica dell'identità. Per semplificare il processo, è possibile usare l'impronta digitale anziché immettere il PIN.
* **Visualizzazione delle connessioni di rete**: quando si aggiunge un account Microsoft, l'app richiede la connessione di rete o Internet.
* **Lettura del contenuto della risorsa di archiviazione**: questa autorizzazione viene usata solo quando si segnala un problema tecnico tramite le impostazioni dell'app. Vengono raccolte alcune informazioni dalla risorsa di archiviazione per diagnosticare il problema.
* **Accesso completo alla rete**: questa autorizzazione è necessaria per l'invio di notifiche per la verifica dell'identità.
* **Esecuzione all'avvio**: se si riavvia il telefono, questa autorizzazione consente di continuare a ricevere notifiche per la verifica dell'identità.

### <a name="why-does-the-microsoft-authenticator-app-allow-you-to-approve-a-request-without-unlocking-the-device"></a>Perché l'app Microsoft Authenticator consente di approvare una richiesta senza sbloccare il dispositivo?

Non è necessario sbloccare il dispositivo per approvare le richieste di verifica, poiché è sufficiente dimostrare che si ha un telefono a portata di mano. La verifica in due passaggi richiede di dimostrare di conoscere una determinata informazione e di essere in possesso di qualcosa, ovvero di conoscere la password. e di essere in possesso del telefono, configurato con l'app Microsoft Authenticator e registrato come prova MFA. Avendo il telefono e approvando la richiesta vengono quindi soddisfatti i criteri per il secondo fattore dell'autenticazione.

### <a name="what-does-the-lock-icon-in-the-account-list-mean"></a>Cosa significa l'icona a forma di lucchetto nell'elenco degli account?

L'icona del lucchetto indica che il dispositivo è registrato in Azure AD e registrato per l'account. La registrazione del dispositivo per iOS avviene durante l'enrollment di Microsoft Intune.

## <a name="next-steps"></a>Passaggi successivi

### <a name="contact-us"></a>Contatti
Se questo articolo non ha fornito le risposte necessarie, saranno apprezzati commenti e suggerimenti. Visitare il [forum relativo all'app Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) per pubblicare una domanda e ricevere assistenza dalla community o lasciare un commento in questa pagina.


### <a name="related-topics"></a>Argomenti correlati
* [Informazioni sulla verifica in due passaggi](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) per l'account Microsoft
* [Problemi con la verifica in due passaggi](multi-factor-authentication-end-user-troubleshoot.md) per un account aziendale o dell'istituto di istruzione
* [Usare Microsoft Authenticator per accedere dal telefono](microsoft-authenticator-app-phone-signin-faq.md)
