---
title: Guida e supporto tecnico per l&quot;app Microsoft Authenticator | Documentazione Microsoft
description: Elenca le domande frequenti e le risposte correlate all&quot;app Microsoft Authentication e ad Azure Multi-Factor Authentication.
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: pblachar, librown
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 28846f2effd26c5b7ca7a79fba52d8ba1cb4ea91
ms.openlocfilehash: 9abf35aebceb4a109becba6e97e0a506fc4375d7


---
# <a name="microsoft-authenticator-app-faq"></a>Domande frequenti sull'app Microsoft Authenticator

Questo articolo risponde a domande comuni che Microsoft riceve sull'app Microsoft Authenticator. Se non viene visualizzata una risposta alla domanda, accedere al [forum dell'app Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp). È disponibile anche un'altra sezione dedicata alle domande frequenti su una funzionalità specifica relativa all'app, le [domande frequenti sull'accesso con il telefono](microsoft-authenticator-app-phone-signin-faq.md).

L'app Microsoft Authenticator ha sostituito l'app Azure Authenticator ed è consigliata quando si usa Azure Multi-Factor Authentication. Questa applicazione è disponibile per Windows Phone, Android e iOS.

## <a name="frequently-asked-questions"></a>Domande frequenti
### <a name="what-happened-to-the-azure-authenticator-multi-factor-auth-and-microsoft-account-apps"></a>Dove sono Azure Authenticator, Multi-Factor Authentication e le app dell'account Microsoft?
L'app Microsoft Authenticator sostituisce tutte queste applicazioni. Azure Authenticator è stato aggiornato a Microsoft Authenticator. Se si usa Multi-Factor Authentication e le app dell'account Microsoft, installare Microsoft Authenticator e aggiungere nuovamente gli account. Assicurarsi di completare l'aggiunta degli account alla nuova app prima di eliminare le app precedenti.

L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

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

Si tratta di un comportamento previsto da progettazione. La verifica in due passaggi richiede di dimostrare di conoscere una determinata informazione e di essere in possesso di qualcosa, ovvero di conoscere la password e di essere in possesso del telefono, configurato con l'app Microsoft Authenticator e registrato come prova MFA.  Avendo il telefono e approvando la richiesta vengono quindi soddisfatti i criteri per il secondo fattore dell'autenticazione. 

## <a name="next-steps"></a>Passaggi successivi

### <a name="contact-us"></a>Contatti
Se questo articolo non ha fornito le risposte necessarie, saranno apprezzati commenti e suggerimenti. Visitare il [forum relativo all'app Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) per pubblicare una domanda e ricevere assistenza dalla community o lasciare un commento in questa pagina.


### <a name="related-topics"></a>Argomenti correlati
* [Informazioni sulla verifica in due passaggi](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) per l'account Microsoft
* [Problemi con la verifica in due passaggi](multi-factor-authentication-end-user-troubleshoot.md) per un account aziendale o dell'istituto di istruzione
* [Usare Microsoft Authenticator per accedere dal telefono](microsoft-authenticator-app-phone-signin-faq.md)




<!--HONumber=Feb17_HO2-->


