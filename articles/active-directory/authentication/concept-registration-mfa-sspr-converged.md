---
title: Registrazione convergente per la reimpostazione password self-service e MFA di Azure AD
description: Registrazione per Multi-Factor Authentication e la reimpostazione password self-service di Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: cdd100d113c3fbeda8ac840d479b065d648ac3ff
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39415654"
---
# <a name="converged-registration-for-self-service-password-reset-and-azure-multi-factor-authentication"></a>Registrazione convergente per la reimpostazione password self-service e Azure Multi-Factor Authentication

Gli utenti sono stati finora obbligati a registrare i metodi di autenticazione per Azure Multi-Factor Authentication (MFA) e la reimpostazione password self-service in due diversi portali. Il fatto che metodi simili fossero usati sia per Azure MFA che per la reimpostazione password self-service creava confusione in molti utenti che finivano per eseguire la registrazione in entrambi i portali. A causa di questa disparità alcuni utenti non riuscivano a usare Azure MFA o la reimpostazione password self-service quando era necessario e chiamavano il supporto tecnico o si sentivano contrariati. Gli utenti possono ora eseguire la registrazione una sola volta e usufruire dei vantaggi sia di Azure MFA che della reimpostazione password self-service, senza dover registrare due volte i metodi di autenticazione per queste funzionalità.  

Prima di abilitare questa nuova esperienza per l'organizzazione, è consigliabile leggere questo articolo, oltre alla [documentazione per l'utente finale](https://aka.ms/securityinfoguide) per comprendere quale sarà impatto dell'esperienza sugli utenti. È possibile usare la [documentazione per l'utente finale](https://aka.ms/securityinfoguide) per formare e preparare gli utenti per la nuova esperienza e garantire una corretta implementazione.

|     |
| --- |
| La registrazione convergente per la reimpostazione password self-service e Azure Multi-Factor Authentication è una funzionalità in anteprima pubblica di Azure Active Directory. Per altre informazioni sulle funzioni in anteprima, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Per consentire agli utenti di registrare i metodi di autenticazione sia per Azure Multi-Factor Authentication che per la reimpostazione password self-service in un'unica esperienza, completare i passaggi seguenti:

1. Accedere al portale di Azure come amministratore globale o amministratore utenti.
2. Passare ad **Azure Active Directory**, **Impostazioni utente**, **Gestisci le impostazioni per le funzionalità in anteprima del pannello di accesso**.
3. In **Gli utenti possono usare le funzionalità in anteprima per la registrazione e la gestione delle informazioni di sicurezza** è possibile scegliere di abilitare un gruppo di utenti **selezionato** o **tutti** gli utenti.

Gli utenti possono ora accedere al sito [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) per registrare i metodi di autenticazione sia per MFA che per la reimpostazione password self-service. Per altre informazioni su questa nuova esperienza utente, vedere la [documentazione per l'utente finale](https://aka.ms/securityinfoguide).  

> [!NOTE]
> Dopo avere abilitato questa esperienza, gli utenti che registrano o confermano il numero di telefono o l'app per dispositivi mobili tramite la nuova esperienza potranno usarli per MFA e la reimpostazione password self-service, se tali metodi sono abilitati nei criteri MFA e di reimpostazione password self-service. Se poi si disabilita questa esperienza, gli utenti che accedono alla pagina di registrazione per la reimpostazione password self-service precedente all'indirizzo aka.ms/ssprsetup dovranno eseguire MFA prima di poter accedere alla pagina.  

## <a name="how-it-works"></a>Funzionamento

Se un utente ha in precedenza registrato i metodi di autenticazione tramite le esperienze separate di registrazione MFA e della reimpostazione password self-service, non deve più registrare tali informazioni. Se tuttavia le impostazioni richiedono che gli utenti eseguano la registrazione per MFA o la reimpostazione password self-service, potrebbe essere visualizzato un prompt che chiede di rivedere le informazioni di sicurezza durante l'accesso.

Se un utente ha registrato un metodo che può essere usato per MFA, gli verrà chiesto di eseguire MFA prima di poter accedere alla nuova esperienza.

Se è stata imposta la registrazione per MFA o la reimpostazione password self-service e un utente non ha ancora effettuato la registrazione, gli verrà chiesto di farlo durante l'accesso.

Gli utenti a cui viene chiesto di eseguire la registrazione durante l'accesso visualizzeranno l'esperienza seguente:

![Registrazione convergente. Configurare i metodi come nuovo utente](./media/concept-registration-mfa-sspr-converged/concept-registration-add-methods.png)

> [!NOTE]
> Questa esperienza viene visualizzata solo quando viene chiesto a un utente di eseguire la registrazione durante l'accesso. Gli utenti che passano direttamente alla nuova esperienza all'indirizzo aka.ms/setupsecurityinfo visualizzeranno una versione diversa dell'esperienza, descritta più avanti in questo articolo.

I metodi di autenticazione visualizzati saranno diversi a seconda dei metodi abilitati nei criteri MFA o di reimpostazione password self-service. Agli utenti verrà chiesto di registrare il numero minimo di metodi di autenticazione necessari per la conformità al criterio MFA, al criterio di reimpostazione password self-service o a entrambi. Se c'è flessibilità nei metodi di autenticazione che l'utente può registrare, può selezionare **Scegliere le informazioni di sicurezza** per scegliere altri metodi di autenticazione.  

Diversamente dalla precedente esperienza di registrazione MFA, agli utenti non verrà chiesto di registrare una password dell'app nel corso della nuova esperienza di registrazione. Dovranno invece seguire i passaggi elencati nell'esercitazione sulle password delle app per registrare le password delle app nella nuova esperienza.  

Dopo che un utente ha completato la registrazione, verrà automaticamente impostato il metodo MFA predefinito. Se l'utente ha registrato un'app di autenticazione, il metodo predefinito verrà impostato sull'app. Se l'utente non ha registrato un'app di autenticazione e ha registrato solo il numero di telefono, il metodo predefinito verrà impostato sulla chiamata telefonica. Per modificare l'impostazione predefinita, gli utenti possono andare all'indirizzo [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) e selezionare **Modifica l'impostazione predefinita**.  

Se non viene imposta la registrazione, gli utenti possono gestire i metodi di autenticazione all'indirizzo [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo). Se un utente ha in precedenza registrato un metodo che può essere usato per eseguire MFA, gli verrà chiesto di eseguire MFA prima di poter accedere alla pagina.  

![Registrazione convergente. Modificare i metodi come utente registrato](./media/concept-registration-mfa-sspr-converged/concept-registration-edit-methods.png)

In questa pagina gli utenti visualizzeranno i metodi di autenticazione registrati in precedenza e i metodi di autenticazione registrati automaticamente, ad esempio il telefono dell'ufficio. Gli utenti possono anche aggiungere, modificare o eliminare i metodi di autenticazione (tranne il telefono dell'ufficio).  

Per questa nuova esperienza esistono log di controllo nella categoria Metodi di autenticazione del log di controllo.  

## <a name="known-issues"></a>Problemi noti

**Il metodo MFA predefinito viene impostato sulla chiamata telefonica quando l'utente registra il telefono con un SMS**
   * Alcuni utenti potrebbero notare che il metodo MFA predefinito viene impostato sulla chiamata telefonica dopo che hanno registrato il numero di telefono con un SMS. Per risolvere questo problema, gli utenti possono modificare il metodo predefinito seguendo queste istruzioni. 

**Gli utenti non riescono ad accedere alla nuova esperienza di registrazione dopo che l'amministratore ha disabilito il metodo predefinito**
   * Alcuni utenti potrebbero non riuscire ad accedere alla nuova esperienza di registrazione se il metodo MFA predefinito registrato in precedenza è stato disabilitato dall'amministratore. Di seguito è riportato uno scenario di esempio: 
      1. L'utente ha in precedenza registrato il numero di telefono e impostato il metodo predefinito sulla chiamata telefonica.
      2. L'amministratore disabilita la chiamata telefonica come metodo MFA per il tenant.
      3. All'utente viene chiesto di eseguire la registrazione durante l'accesso perché deve registrare un metodo aggiuntivo per soddisfare il criterio di reimpostazione password self-service del tenant.
      4. L'utente prova a eseguire la registrazione, ma non può accedere alla pagina perché non ha impostato un metodo predefinito ed è bloccato in un ciclo.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come distribuire rapidamente la reimpostazione password self-service di Azure AD](howto-sspr-deployment.md)

[Informazioni su come richiedere l'autenticazione a più fattori durante l'accesso](howto-mfa-getstarted.md)

[Documentazione sulla configurazione di un metodo di autenticazione per l'utente finale](https://aka.ms/securityinfoguide)