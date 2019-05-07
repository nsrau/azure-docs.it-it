---
title: Guida alla distribuzione della reimpostazione della password self-service - Azure Active Directory
description: Consigli per l'implementazione della reimpostazione password self-service di Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c254ef3a71e95b33df2a779c728d47fff3c3759
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190354"
---
# <a name="how-to-successfully-roll-out-self-service-password-reset"></a>Come implementare la reimpostazione della password self-service

Per garantire un'implementazione semplice della funzionalità di reimpostazione della password self-service di Azure Active Directory (Azure AD), la maggior parte dei clienti completa la procedura seguente:

> [!VIDEO https://www.youtube.com/embed/OZn5btP6ZXw]

1. Completare l'implementazione di un gruppo pilota con un piccolo sottoinsieme dell'organizzazione.
   * Informazioni su come eseguire un'installazione pilota sono disponibili nell'articolo [Esercitazione: Completare l'implementazione di un gruppo pilota per la reimpostazione della password self-service di Azure AD](tutorial-sspr-pilot.md).
1. Provvedere alla formazione del team di supporto tecnico.
   * Come fornirà l'assistenza necessaria ai clienti?
   * Si obbligherà gli utenti a usare la reimpostazione della password self-service e non si consentirà al supporto tecnico di fornire assistenza gli utenti?
   * Sono stati forniti loro gli URL necessari per la registrazione e la reimpostazione?
      * Registrazione: https://aka.ms/ssprsetup
      * Reimpostazione: https://aka.ms/sspr

   > [!WARNING]
   > Utilizzo della casella di controllo "Cambiamento obbligatorio password all'accesso successivo" in strumenti di amministrazione Active Directory in locale come Active Directory Users e computer o il centro di amministrazione di Active Directory non è supportato. Quando si modifica una password locale non selezionare questa opzione. 

1. Provvedere alla formazione degli utenti.
   * Le sezioni seguenti di questo documento contengono comunicazioni di esempio, i portali delle password e le procedure di implementazione della registrazione e di inserimento dei dati di autenticazione.
   * Il gruppo che si occupa dei prodotti Azure Active Directory ha creato un [piano di distribuzione dettagliato](https://aka.ms/SSPRDeploymentPlan) che le organizzazioni possono usare in parallelo con la documentazione di questo sito per delineare un caso aziendale e un piano per la distribuzione della funzionalità di reimpostazione della password self-service.
1. Abilitare la reimpostazione della password self-service per l'intera organizzazione.
   * Quando si è pronti, abilitare la reimpostazione della password per tutti gli utenti impostando l'opzione **Reimpostazione delle password self-service abilitata** su **Tutti**.

## <a name="sample-communication"></a>Comunicazione di esempio

Molti clienti ritengono che il modo più facile per invitare gli utenti a usare la reimpostazione password self-service sia una campagna di posta elettronica che includa istruzioni semplici. [Sono stati creati semplici messaggi di posta elettronica e altri documenti che è possibile usare come modelli per agevolare l'implementazione](https://www.microsoft.com/download/details.aspx?id=56768):

* **Presto disponibile**: modello di messaggio usato nelle settimane o nei giorni precedenti l'implementazione per informare gli utenti che sarà necessario eseguire alcune operazioni.
* **Ora disponibile**: modello di messaggio usato il giorno del lancio del programma per invitare gli utenti a eseguire la registrazione e verificare i dati di autenticazione. Se gli utenti si registrano subito, la reimpostazione password self-service sarà disponibile al momento necessario.
* **Promemoria di registrazione**: modello di messaggio usato per alcuni giorni o settimane dopo la distribuzione per ricordare agli utenti di eseguire la registrazione e verificare i dati di autenticazione.
* **Poster sulla reimpostazione della password self-service**: poster che è possibile personalizzare e visualizzare all'interno dell'organizzazione nei giorni e nelle settimane che precedono e che seguono la procedura di implementazione.
* **Table tent sulla reimpostazione della password self-service**: schede da tavolo che è possibile posizionare in sala mensa, in aula riunioni o sulle scrivanie per incoraggiare gli utenti a completare la registrazione.
* **Adesivi sulla reimpostazione della password self-service**: modelli di adesivi che è possibile personalizzare, stampare e attaccare a computer portatili, monitor, tastiere o telefoni cellulari per ricordare la procedura di accesso alla reimpostazione della password self-service.

![Esempi di messaggio di posta elettronica SSPR di implementazione per gli utenti][Email]

## <a name="create-your-own-password-portal"></a>Creare il portale delle password

Molti clienti scelgono di ospitare una pagina Web e di creare una voce DNS radice, ad esempio https://passwords.contoso.com. Popolano questa pagina con collegamenti alle informazioni seguenti:

* [Portale di reimpostazione della password di Azure AD - https://aka.ms/sspr](https://aka.ms/sspr)
* [Portale per la registrazione della reimpostazione della password di Azure AD - https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
* [Portale di modifica della password di Azure AD - https://account.activedirectory.windowsazure.com/ChangePassword.aspx](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)
* Altre informazioni specifiche dell'organizzazione

In qualsiasi comunicazione cartacea o di posta elettronica inviata è possibile includere un URL personalizzato facile da ricordare al quale gli utenti possono accedere quando devono usare i servizi. È stata creata una [pagina di reimpostazione della password di esempio](https://github.com/ajamess/password-reset-page), che è possibile usare e personalizzare in base alle esigenze dell'organizzazione.

## <a name="use-enforced-registration"></a>Usare la registrazione applicata

Se si vuole che gli utenti eseguano la registrazione per la reimpostazione della password, è possibile rendere obbligatoria la registrazione quando accedono tramite Azure AD. È possibile abilitare questa opzione dal riquadro **Reimpostazione password** della directory, selezionando l'opzione **Richiedere agli utenti di registrarsi all'accesso?** nella scheda **Registrazione**.

Gli amministratori possono richiedere agli utenti di registrarsi nuovamente dopo un periodo di tempo specifico. Possono impostare l'opzione **Number of days before users are asked to reconfirm their authentication information** (Numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione) su un valore compreso tra 0 e 730 giorni.

Dopo aver abilitato questa opzione, quando gli utenti accedono, visualizzano un messaggio che informa che l'amministratore ha richiesto loro di verificare le informazioni di autenticazione.

## <a name="populate-authentication-data"></a>Popolare i dati di autenticazione

È necessario valutare la possibilità di [prepopolare alcuni dei dati di autenticazione degli utenti](howto-sspr-authenticationdata.md). In questo modo non è necessario che gli utenti eseguano registrazione per la reimpostazione della password prima di poter usare la reimpostazione password self-service. Se hanno fornito dati di autenticazione che soddisfano i criteri di reimpostazione della password, potranno reimpostare le proprie password.

## <a name="disable-self-service-password-reset"></a>Disabilitare la reimpostazione password self-service

Se l'organizzazione decide di disabilitare la reimpostazione della password self-service, la procedura da seguire è molto semplice. Aprire il tenant di Azure AD e passare a **Reimpostazione password** > **Proprietà** e scegliere **Nessuno** in **Reimpostazione password self-service abilitata**. Gli utenti manterranno i metodi di autenticazione registrati per un uso futuro.

## <a name="next-steps"></a>Passaggi successivi

* [Reimpostare o modificare la password](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrarsi per la reimpostazione della password self-service](../user-help/active-directory-passwords-reset-register.md)
* [Abilitare la registrazione convergente per Azure Multi-Factor Authentication e la reimpostazione della password self-service di Azure AD](concept-registration-mfa-sspr-converged.md)
* [Domande sulle licenze](concept-sspr-licensing.md)
* [Dati usati dalla reimpostazione della password self-service e dati da immettere per gli utenti](howto-sspr-authenticationdata.md)
* [Opzioni dei criteri per la reimpostazione della password self-service](concept-sspr-policy.md)
* [Panoramica del writeback delle password](howto-sspr-writeback.md)
* [Come creare un report sull'attività relativa alla reimpostazione della password self-service](howto-sspr-reporting.md)
* [Informazioni sulle opzioni della reimpostazione della password self-service](concept-sspr-howitworks.md)
* [Come risolvere i problemi di reimpostazione della password self-service](active-directory-passwords-troubleshoot.md)
* [Altre informazioni non illustrate altrove](active-directory-passwords-faq.md)

[Email]: ./media/howto-sspr-deployment/sspr-emailtemplates.png "Personalizzare questi modelli di messaggi di posta elettronica in base ai requisiti aziendali"
