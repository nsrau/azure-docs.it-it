---
title: Registrazione combinata per Azure AD SSPR e MFA (anteprima)
description: Registrazione (anteprima) di reimpostazione della password self-service e multi-Factor Authentication di Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa9872ef76264956430bb69856a197042c196dfd
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57550191"
---
# <a name="combined-security-information-registration-preview"></a>Registrazione di informazioni di sicurezza combinati (anteprima)

Prima della registrazione combinata, gli utenti registrati metodi di autenticazione per Azure multi-Factor Authentication (MFA) e la reimpostazione della password self-service (SSPR) illustrate due diverse esperienze. Le persone erano confusione che sono stati usati metodi simili per Azure MFA e SSPR ma che avevano la registrazione per le singole funzionalità separatamente. A questo punto, con la registrazione combinata, gli utenti possono registrare una sola volta e ottenere i vantaggi di Azure MFA e SSPR.

![Combinare le informazioni di sicurezza - profilo personale che mostra le info di sicurezza registrata per un utente, tra cui Microsoft Authenticator e telefono per un utente di esempio nella directory.](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Prima di abilitare la nuova esperienza, esaminare questa documentazione incentrata su amministratore e la documentazione incentrato sull'utente per verificare di che conoscere le funzionalità e l'impatto di questa funzionalità. La formazione su documentazione per l'utente per preparare gli utenti per la nuova esperienza e contribuire a garantire una corretta implementazione di base.

|     |
| --- |
| Registrazione di informazioni di sicurezza combinato per la reimpostazione password self-service di Azure multi-Factor Authentication e Azure AD è una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle funzioni in anteprima, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!IMPORTANT]
> Se un utente è abilitato per l'anteprima originale e l'esperienza di registrazione combinata avanzata, visualizzeranno la nuova esperienza. Gli utenti che sono abilitati per entrambe le esperienze verranno visualizzati solo la nuova esperienza del profilo personale. Il nuovo profilo personale in linea con l'aspetto della registrazione combinata e fornisce un'esperienza trasparente per gli utenti. Gli utenti possono visualizzare My Profile visitando [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com).

Le pagine MyProfile sono localizzate in base alle impostazioni della lingua corrente del computer di accedere alla pagina. Vengono archiviati il linguaggio più recente utilizzato nella cache del browser in modo che i successivi tentativi di accesso continuerà a eseguire il rendering nell'ultima lingua utilizzata da Microsoft. La cancellazione della cache causerà delle pagine di cui eseguire nuovamente il rendering. Se si vuole forzare una lingua specifica aggiungendo un `?lng=de-DE` alla fine dell'URL in cui `de-DE` è impostato per la lingua appropriata codice forzerà delle pagine di cui eseguire il rendering in tale lingua.

![L'interfaccia di profilo che mostra informazioni di sicurezza e la possibilità di configurare la reimpostazione o altri metodi di verifica aggiuntiva di sicurezza utente.](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-converged-registration"></a>Metodi disponibili nella registrazione per la convergenza

A questo punto, registrazione combinata supporta i seguenti metodi e le azioni per questi metodi:

|   | Register | Modifica | Delete |
| --- | --- | --- | --- |
| Microsoft Authenticator | Sì (massimo 5) | No  | Sì |
| Altre app authenticator | Sì (massimo 5) | No  | Sì |
| Token hardware | No  | No  | Sì |
| Telefono | Sì | Sì | Sì |
| Telefono alternativo | Sì | Sì | Sì |
| Telefono ufficio | No  | No  | No  |
| Email | Sì | Sì | Sì |
| Domande di sicurezza | Sì | No  | Sì |
| Password dell'app | Sì | No  | Sì |

> [!NOTE]
> Le password dell'App sono disponibili solo per gli utenti che sono stati applicati per MFA. Le password dell'App non sono disponibili agli utenti che sono abilitati per MFA tramite criteri di accesso condizionale.

Gli utenti possono impostare le opzioni seguenti come metodo predefinito per l'autenticazione a più fattori:

- Microsoft Authenticator: notifica
- App Authenticator o all'hardware del token: codice
- Chiamata telefonica
- SMS

Per continuare a aggiungere ulteriori metodi di autenticazione tali ad Azure AD, tali metodi saranno disponibili nella registrazione combinata.

## <a name="combined-registration-modes"></a>Registrazione combinata modalità

Sono disponibili due "modalità" della registrazione combinata: interrompere e gestire. 

Modalità di interrupt, è un'esperienza simile a procedura guidata, mostrata a un utente quando registrare o aggiornare le relative informazioni di sicurezza all'accesso. 

Gestire la modalità fa parte del profilo dell'utente e consente loro di gestire le tue info di sicurezza. 

Per entrambe le modalità, se un utente ha registrato in precedenza un metodo che può essere usato per MFA, sarà necessario eseguire l'autenticazione a più fattori prima di poter accedere le info di sicurezza.

### <a name="interrupt-mode"></a>Modalità di interrupt

Registrazione combinata rispetta i criteri SSPR sia l'autenticazione MFA, se entrambi sono abilitati per il tenant. Questi controllano i criteri, se un utente viene interrotta per registrare durante l'accesso e quali metodi sono disponibili da registrare.

Il seguente elenco di diversi scenari in cui un utente potrebbe essere richiesto di registrare o aggiornare le tue info di sicurezza:

* Registrazione MFA applicata tramite Identity Protection: Gli utenti verranno richiesto di registrare durante l'accesso. Si registrano i metodi di autenticazione a più fattori e SSPR (se l'utente è abilitato per SSPR).
* Registrazione MFA applicata tramite autenticazione a più fattori per utente: Gli utenti verranno richiesto di registrare durante l'accesso. Si registrano i metodi di autenticazione a più fattori e SSPR (se l'utente è abilitato per SSPR).
* Registrazione MFA applicata tramite l'accesso condizionale o altri criteri: Agli utenti venga chiesto di registrarsi all'accesso a una risorsa che richiede l'autenticazione a più fattori. Gli utenti verranno registrati i metodi di autenticazione a più fattori e SSPR (se l'utente è abilitato per SSPR).
* Registrazione SSPR applicata: Gli utenti vengono richiesto di registrare durante l'accesso. Si registrano solo i metodi SSPR
* Aggiornamento SSPR applicato: Gli utenti devono verificare le info di sicurezza in base all'intervallo impostato dall'amministratore. Gli utenti vengono visualizzati le relative informazioni e possono scegliere "Esito positivo" o apportare modifiche se necessario.

Quando viene applicata la registrazione, gli utenti vengono visualizzati il numero minimo di metodi necessari per essere conformi ai criteri di autenticazione a più fattori sia SSPR dalla maggior parte a livello di protezione minimo.

Esempio:

* Un utente è abilitato per SSPR. I criteri di SSPR necessarie due metodi per reimpostare e telefono, posta elettronica e il codice di app per dispositivi mobili sono state abilitate.
   * Questo utente deve registrare due metodi.
      * L'app authenticator e telefono compaiono per impostazione predefinita.
      * L'utente può scegliere di registrare l'indirizzo di posta elettronica anziché l'app authenticator o il telefono.

Diagramma di flusso seguente vengono descritti i metodi che vengono visualizzati a un utente quando interrotta la registrazione durante l'accesso:

![Combinare diagramma di flusso info di sicurezza che spiegano numerosi metodi obbligatorio quando sono necessarie ulteriori informazioni quando si accede. Questo può cambiare se solo MFA o solo SSPR è obbligatorio](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Se si dispone di SSPR abilitate sia l'autenticazione MFA, è consigliabile che implementare la registrazione per MFA.

Se i criteri di SSPR richiedono agli utenti di controllare le info di sicurezza a intervalli regolari, gli utenti vengono interrotti durante l'accesso e visualizzati tutti i relativi metodi registrati. Se le informazioni siano aggiornate o scegliere "Modifica info" possono scegliere "Esito positivo" per apportare modifiche.

### <a name="manage-mode"></a>Gestire la modalità

Gli utenti possono accedere per gestire la modalità, passare a [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo) oppure scegliendo "Info di sicurezza" dal profilo personale. Da qui, gli utenti possono aggiungere i metodi, eliminare o modificare i metodi esistenti o modificare il metodo predefinito e altro ancora.

## <a name="key-usage-scenarios"></a>Principali scenari di utilizzo

### <a name="set-up-security-info-during-sign-in"></a>Configurare info di sicurezza durante l'accesso

Un amministratore è applicata la registrazione.

Un utente non ha configurato tutte le info di sicurezza necessarie e consente di passare al portale di Azure. Dopo aver immesso il nome utente e password, l'utente viene richiesto di impostare le tue info di sicurezza. Quindi, l'utente segue i passaggi illustrati nella procedura guidata per configurare le info di sicurezza richiesto. L'utente può scegliere di configurare metodi diversi da ciò che viene visualizzato per impostazione predefinita, se le impostazioni consentono. Al termine della procedura guidata, l'utente esamina i metodi configurati e il metodo predefinito per MFA. Per completare il processo di installazione, l'utente conferma le informazioni e continua nel portale di Azure.

### <a name="set-up-security-info-from-my-profile"></a>Impostare le tue info di sicurezza dal profilo personale

Un amministratore non è applicata la registrazione.

Un utente che non ha ancora eseguito il configurato tutte le info di sicurezza necessarie passa a [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com). L'utente sceglie quindi **info di sicurezza** dal riquadro di spostamento a sinistra. Da qui, l'utente sceglie di aggiungere un metodo, consente di selezionare uno dei metodi disponibili e vengono seguiti i passaggi per configurare tale metodo. Al termine, l'utente vede il metodo che è sufficiente impostare la pagina delle info di sicurezza.

### <a name="delete-security-info-from-my-profile"></a>Elimina info di sicurezza dal profilo personale

Un utente che ha precedentemente configurato almeno un metodo passa a [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). L'utente sceglie di eliminare uno dei metodi registrati in precedenza. Al termine, tale metodo l'utente non è più visibile la pagina delle info di sicurezza.

### <a name="change-default-method-from-my-profile"></a>Modifica il metodo predefinito dal profilo personale

Consente di passare a un utente che ha precedentemente configurato almeno un metodo che può essere usato per l'autenticazione a più fattori [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). L'utente modifica il metodo predefinito corrente a un metodo predefiniti diversi. Al termine, l'utente vede il nuovo metodo di impostazione predefinita la pagina delle info di sicurezza.

## <a name="next-steps"></a>Passaggi successivi

[Abilitare la registrazione combinata nel tenant](howto-registration-mfa-sspr-combined.md)

[Metodi disponibili per l'autenticazione a più fattori e SSPR](concept-authentication-methods.md)

[Configurare la reimpostazione password self-service](howto-sspr-deployment.md)

[Configurare Azure multi-Factor Authentication](howto-mfa-getstarted.md)
