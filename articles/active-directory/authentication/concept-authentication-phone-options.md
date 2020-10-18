---
title: Metodi di autenticazione telefono-Azure Active Directory
description: Per informazioni sull'uso dei metodi di autenticazione tramite telefono, vedere Azure Active Directory per contribuire a migliorare e proteggere gli eventi di accesso
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25161054a53377b9fbbb12621b95dff9c119fa17
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165511"
---
# <a name="authentication-methods-in-azure-active-directory---phone-options"></a>Metodi di autenticazione nelle opzioni del telefono Azure Active Directory

Per l'autenticazione diretta tramite SMS, è possibile [configurare e abilitare gli utenti per l'autenticazione basata su SMS (anteprima)](howto-authentication-sms-signin.md). L'accesso basato su SMS è ideale per chi lavora a contatto con il pubblico. Con l'accesso basato su SMS, non è necessario che l'utente conosca nome utente e password per accedere alle applicazioni e ai servizi. Può immettere invece il numero di telefono cellulare registrato, ricevere un SMS con un codice di verifica e inserirlo nell'interfaccia di accesso.

Gli utenti possono autenticarsi anche usando un telefono cellulare o un telefono dell'ufficio come forma di autenticazione secondaria durante l'autenticazione a più fattori di Azure o la reimpostazione della password self-service.

Per garantirne il corretto funzionamento, i numeri di telefono devono essere nel formato *+PrefissoInternazionale NumeroTelefonico*, ad esempio *+1 4251234567*.

> [!NOTE]
> È necessario uno spazio tra il prefisso internazionale o della regione e il numero di telefono.
>
> La reimpostazione della password non supporta le estensioni del telefono. Anche nel formato *+1 4251234567X12345*, le estensioni vengono rimosse prima della chiamata.

## <a name="mobile-phone-verification"></a>Verifica tramite telefono cellulare

Per Azure Multi-Factor Authentication o la reimpostazione della password self-service, gli utenti possono scegliere di ricevere un SMS con un codice di verifica da immettere nell'interfaccia di accesso oppure una telefonata in cui si richiedere di immettere il codice PIN definito.

Se gli utenti non desiderano che il numero di telefono cellulare sia visibile nella directory, ma vogliono comunque usarlo per la reimpostazione della password, gli amministratori non devono inserirlo nella directory. Gli utenti devono invece popolare il proprio attributo **Telefono per l'autenticazione** tramite la registrazione delle informazioni di sicurezza combinate all'indirizzo [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo). Gli amministratori possono visualizzare queste informazioni nel profilo dell'utente, ma non vengono pubblicate altrove.

:::image type="content" source="media/concept-authentication-methods/user-authentication-methods.png" alt-text="Screenshot del portale di Azure che mostra i metodi di autenticazione con un numero di telefono inserito":::

Microsoft non garantisce l'invio coerente di prompt Azure Multi-Factor Authentication via SMS o tramite messaggio vocale allo stesso numero. Nell'interesse degli utenti, Microsoft può aggiungere o rimuovere codici brevi in qualsiasi momento per eseguire modifiche di route e migliorare il recapito degli SMS. Microsoft non supporta i codici brevi in paesi o aree geografiche diversi da Stati Uniti e Canada.

### <a name="text-message-verification"></a>Verifica tramite SMS

Con la verifica tramite SMS durante la reimpostazione della password self-service o l'uso di Azure Multi-Factor Authentication, viene inviato un SMS al numero di telefono cellulare contenente un codice di verifica. Per completare il processo di accesso, il codice di verifica inviato viene immesso nell'interfaccia di accesso.

### <a name="phone-call-verification"></a>Verifica tramite telefonata

Con la verifica tramite telefonata durante la reimpostazione della password self-service o l'uso di Azure Multi-Factor Authentication, viene eseguita una chiamata vocale automatica al numero di telefono registrato dall'utente. Per completare il processo di accesso, all'utente viene richiesto di immettere il numero PIN seguito da # sul tastierino.

## <a name="office-phone-verification"></a>Verifica tramite telefono dell'ufficio

L'attributo telefono dell'ufficio viene gestito dall'amministratore di Azure AD e non può essere registrato dall'utente stesso.

Con la verifica tramite telefonata durante la reimpostazione della password self-service o l'uso di Azure Multi-Factor Authentication, viene eseguita una chiamata vocale automatica al numero di telefono registrato dall'utente. Per completare il processo di accesso, all'utente viene richiesto di immettere il numero PIN seguito da # sul tastierino.

## <a name="troubleshooting-phone-options"></a>Risoluzione dei problemi delle opzioni che si servono del telefono

In caso di problemi con l'autenticazione tramite telefono in Azure AD, esaminare la procedura per la risoluzione dei problemi seguente:

* ID del chiamante bloccato su un solo dispositivo.
   * Esaminare i numeri bloccati configurati sul dispositivo.
* Numero di telefono errato o codice internazionale o dell'area errato, oppure confusione tra il numero di telefono personale e il numero di telefono dell'ufficio.
   * Risolvere i problemi relativi all'oggetto utente e ai metodi di autenticazione configurati. Assicurarsi che siano registrati i numeri di telefono corretti.
* PIN immesso errato.
   * Verificare che l'utente abbia usato il PIN corretto registrato per il proprio account.
* Chiamata inoltrata alla segreteria telefonica.
   * Verificare che il telefono dell'utente sia acceso e che il servizio sia disponibile nella propria area oppure usare un altro metodo.
* L'utente è bloccato
   * Chiedere a un amministratore di Azure AD di sbloccare l'utente nel portale di Azure.
* Il metodo con SMS non è stato sottoscritto sul dispositivo.
   * Chiedere all'utente di modificare i metodi o di attivare il metodo con SMS sul dispositivo.
* Servizi di telecomunicazione non funzionanti correttamente, ad esempio input del telefono mancanti, problemi di toni DTMF mancanti, ID del chiamante bloccato oppure SMS bloccati in più dispositivi.
   * Microsoft usa più provider di telecomunicazioni per instradare telefonate e messaggi SMS per l'autenticazione. Se si nota uno dei problemi descritti in precedenza, chiedere all'utente di provare a usare il metodo almeno cinque volte in 5 minuti e recuperare le informazioni dell'utente prima di contattare il supporto tecnico Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, vedere l'[esercitazione sulla reimpostazione della password self-service][tutorial-sspr] e [Azure Multi-Factor Authentication][tutorial-azure-mfa].

Per altre informazioni sui concetti di reimpostazione della password self-service, vedere [Come funziona la reimpostazione della password self-service di Azure AD][concept-sspr].

Per altre informazioni sui concetti di MFA, vedere [Come funziona Azure Multi-Factor Authentication][concept-mfa].

Altre informazioni sulla configurazione dei metodi di autenticazione con l' [API REST di Microsoft Graph beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
