---
title: Registrazione combinata per la reimpostazione della password self-service e MFA - Azure Active Directory
description: Azure AD Multi-Factor Authentication e registrazione della reimpostazione della password self-service (anteprima)Azure AD Multi-Factor Authentication and self-service password reset registration (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26656b6dafd91d47c05c2d1f923e53f4ba790cf8
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309923"
---
# <a name="combined-security-information-registration-preview"></a>Registrazione combinata delle informazioni di sicurezza (anteprima)Combined security information registration (preview)

Prima della registrazione combinata, gli utenti registrare i metodi di autenticazione per Azure Multi-Factor Authentication e la reimpostazione della password self-service (SSPR) separatamente. Le persone erano confuse sul fatto che metodi simili sono stati utilizzati per Multi-Factor Authentication e SSPR, ma hanno dovuto registrarsi per entrambe le funzionalità. Ora, con la registrazione combinata, gli utenti possono registrarsi una sola volta e ottenere i vantaggi di entrambi Multi-Factor Authentication e SSPR.

![Il mio profilo che mostra le informazioni di sicurezza registrate per un utente](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Prima di abilitare la nuova esperienza, consultare questa documentazione incentrata sull'amministratore e la documentazione incentrata sull'utente per assicurarsi di comprendere le funzionalità e gli effetti di questa funzionalità. Basare la formazione sulla [documentazione dell'utente](../user-help/user-help-security-info-overview.md) per preparare gli utenti per la nuova esperienza e contribuire a garantire un'implementazione di successo.

La registrazione delle informazioni di sicurezza combinata di Azure AD non è attualmente disponibile per i cloud nazionali come Azure US Government, Azure Germania o Azure China 21Vianet.Azure AD combined security information registration is not currently available to national clouds like Azure US Government, Azure Germany, or Azure China 21Vianet.

|     |
| --- |
| La registrazione combinata delle informazioni di sicurezza per L'autenticazione a più fattori e la reimpostazione della password self-service di Azure Active Directory (Azure AD) è una funzionalità di anteprima pubblica di Azure AD. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!IMPORTANT]
> Gli utenti abilitati sia per l'anteprima originale che per l'esperienza di registrazione combinata avanzata vedranno il nuovo comportamento. Gli utenti abilitati per entrambe le esperienze vedranno solo la nuova esperienza del profilo personale. Il nuovo profilo personale si allinea con l'aspetto della registrazione combinata e offre un'esperienza senza soluzione di continuità per gli utenti. Gli utenti possono vedere [https://myprofile.microsoft.com](https://myprofile.microsoft.com)il mio profilo accedendo a .

> [!NOTE] 
> È possibile che venga visualizzato un messaggio di errore durante il tentativo di accedere all'opzione Informazioni di sicurezza. Ad esempio, "Spiacenti, non è possibile accedere". In questo caso, verificare di non disporre di alcun oggetto Configurazione o Criteri di gruppo che blocchi i cookie di terze parti nel browser Web. 

Le pagine del profilo personale vengono localizzate in base alle impostazioni della lingua del computer che accede alla pagina. Microsoft memorizza la lingua più recente utilizzata nella cache del browser, pertanto i successivi tentativi di accesso alle pagine continueranno a eseguire il rendering nell'ultima lingua utilizzata. Se si cancella la cache, le pagine verranno nuovamente renderizzate. Se si desidera forzare una lingua `?lng=<language>` specifica, è possibile `<language>` aggiungere alla fine dell'URL, dove è il codice della lingua che si desidera eseguire il rendering.

![Configurare SSPR o altri metodi di verifica della sicurezza](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Metodi disponibili nella registrazione combinata

La registrazione combinata supporta i seguenti metodi e azioni di autenticazione:

|   | Register | Modifica | Delete |
| --- | --- | --- | --- |
| Microsoft Authenticator | Sì (massimo 5) | No | Sì |
| Altra app di autenticazione | Sì (massimo 5) | No | Sì |
| Token hardware | No | No | Sì |
| Telefono | Sì | Sì | Sì |
| Telefono alternativo | Sì | Sì | Sì |
| Telefono ufficio | No | No | No |
| Email | Sì | Sì | Sì |
| Domande di sicurezza | Sì | No | Sì |
| Password dell'app | Sì | No | Sì |
| Chiavi di protezione FIDO2<br />*La modalità gestita solo dalla pagina Informazioni di [sicurezza](https://mysignins.microsoft.com/security-info)*| Sì | Sì | Sì |

> [!NOTE]
> Le password delle app sono disponibili solo per gli utenti che sono stati applicati per Multi-Factor Authentication. Le password delle app non sono disponibili per gli utenti abilitati per Multi-Factor Authentication tramite criteri di accesso condizionale.

Gli utenti possono impostare una delle seguenti opzioni come metodo predefinito di Multi-Factor Authentication:

- Microsoft Authenticator – notifica.
- App Authenticator o token hardware - codice.
- Telefonata.
- SMS.

Man mano che continuiamo ad aggiungere altri metodi di autenticazione ad Azure AD, tali metodi saranno disponibili in combinazione di registrazione.

## <a name="combined-registration-modes"></a>Modalità di registrazione combinate

Ci sono due modalità di registrazione combinata: interrupt e manage.

- **La modalità di interrupt** è un'esperienza simile a una procedura guidata, presentata agli utenti quando registrano o aggiornano le informazioni di sicurezza all'accesso.

- **La modalità** di gestione fa parte del profilo utente e consente agli utenti di gestire le proprie informazioni di sicurezza.

Per entrambe le modalità, gli utenti che hanno registrato in precedenza un metodo che può essere utilizzato per Multi-Factor Authentication dovranno eseguire Multi-Factor Authentication prima di poter accedere alle informazioni di sicurezza.

### <a name="interrupt-mode"></a>Modalità di interruzione

La registrazione combinata rispetta i criteri di autenticazione a più fattori e SSPR, se entrambi sono abilitati per il tenant. Questi criteri controllano se un utente viene interrotto per la registrazione durante l'accesso e quali metodi sono disponibili per la registrazione.

Ecco diversi scenari in cui agli utenti potrebbe essere richiesto di registrare o aggiornare le informazioni di sicurezza:Here are several scenarios in which users might be prompted to register or refresh their security info:

- Registrazione di Multi-Factor Authentication applicata tramite Identity Protection: agli utenti viene chiesto di registrarsi durante l'accesso. Registrano i metodi e i metodi SSPR di Multi-Factor Authentication (se l'utente è abilitato per SSPR).
- Registrazione di Multi-Factor Authentication applicata tramite l'autenticazione a più fattori per utente: agli utenti viene chiesto di registrarsi durante l'accesso. Registrano i metodi e i metodi SSPR di Multi-Factor Authentication (se l'utente è abilitato per SSPR).
- Registrazione di Multi-Factor Authentication applicata tramite Accesso condizionale o altri criteri: agli utenti viene chiesto di registrarsi quando usano una risorsa che richiede Multi-Factor Authentication. Registrano i metodi e i metodi SSPR di Multi-Factor Authentication (se l'utente è abilitato per SSPR).
- Registrazione SSPR applicata: agli utenti viene chiesto di registrarsi durante l'accesso. Registrano solo i metodi SSPR.
- Aggiornamento SSPR applicato: gli utenti devono esaminare le informazioni di sicurezza a un intervallo impostato dall'amministratore. Gli utenti vengono mostrate le loro informazioni e possono confermare le informazioni correnti o apportare modifiche, se necessario.

Quando viene applicata la registrazione, agli utenti viene mostrato il numero minimo di metodi necessari per essere conformi ai criteri di autenticazione a più fattori e SSPR, dalla maggior parte alla meno sicura.

Ad esempio:

- Un utente è abilitato per SSPR. I criteri SSPR richiedevano due metodi per reimpostare e hanno abilitato il codice dell'app per dispositivi mobili, l'indirizzo di posta elettronica e il telefono.
   - Questo utente è tenuto a registrare due metodi.
      - Per impostazione predefinita, all'utente vengono visualizzati l'app e il telefono di autenticazione.
      - L'utente può scegliere di registrare l'e-mail invece di autenticatore app o telefono.

Questo diagramma di flusso descrive i metodi che vengono visualizzati a un utente quando vengono interrotti per la registrazione durante l'accesso:This flowchart describes which methods are shown to a user when interrupted to register during sign-in:

![Diagramma di flusso delle info di sicurezza combinato](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Se sono abilitate entrambe le opzioni di autenticazione a più fattori e SSPR, è consigliabile applicare la registrazione di Multi-Factor Authentication.

Se i criteri SSPR richiedono agli utenti di esaminare le informazioni di sicurezza a intervalli regolari, gli utenti vengono interrotti durante l'accesso e vengono visualizzati tutti i metodi registrati. Possono confermare le informazioni correnti se sono aggiornate oppure possono apportare modifiche se necessario. Gli utenti devono eseguire l'autenticazione a più fattori quando accedono a questa pagina.

### <a name="manage-mode"></a>Modalità di gestione

Gli utenti possono accedere [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) alla modalità di gestione accedendo o selezionando Informazioni di **sicurezza** da Profilo personale. Da lì, gli utenti possono aggiungere metodi, eliminare o modificare i metodi esistenti, modificare il metodo predefinito e altro ancora.

## <a name="key-usage-scenarios"></a>Scenari di utilizzo chiaveKey usage scenarios

### <a name="set-up-security-info-during-sign-in"></a>Configurare le informazioni di sicurezza durante l'accesso

Un amministratore ha imposto la registrazione.

Un utente non ha configurato tutte le informazioni di sicurezza necessarie e passa al portale di Azure.A user has not set up all required security info and goes to the Azure portal. Dopo aver immesso il nome utente e la password, all'utente viene richiesto di impostare le informazioni di sicurezza. L'utente segue quindi i passaggi illustrati nella procedura guidata per impostare le informazioni di sicurezza necessarie. Se le impostazioni lo consentono, l'utente può scegliere di configurare metodi diversi da quelli visualizzati per impostazione predefinita. Dopo aver completato la procedura guidata, gli utenti esaminano i metodi impostati e il metodo predefinito per Multi-Factor Authentication. Per completare il processo di installazione, l'utente conferma le informazioni e continua al portale di Azure.To complete the setup process, the user confirms the info and continues to the Azure portal.

### <a name="set-up-security-info-from-my-profile"></a>Configurare le informazioni di sicurezza da Profilo personale

Un amministratore non ha imposto la registrazione.

Un utente che non ha ancora configurato tutte [https://myprofile.microsoft.com](https://myprofile.microsoft.com)le informazioni di sicurezza necessarie passa a . L'utente seleziona Informazioni di **sicurezza** nel riquadro sinistro. Da lì, l'utente sceglie di aggiungere un metodo, seleziona uno dei metodi disponibili e segue i passaggi per impostare tale metodo. Al termine, l'utente visualizza il metodo appena impostato nella pagina Informazioni di sicurezza.

### <a name="delete-security-info-from-my-profile"></a>Eliminare le informazioni di sicurezza dal profilo personale

Un utente che in precedenza ha impostato [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)almeno un metodo passa a . L'utente sceglie di eliminare uno dei metodi registrati in precedenza. Al termine, l'utente non vede più tale metodo nella pagina Informazioni di sicurezza.

### <a name="change-the-default-method-from-my-profile"></a>Modificare il metodo predefinito da Profilo personale

Un utente che in precedenza ha impostato almeno un metodo che [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)può essere utilizzato per Multi-Factor Authentication passa a . L'utente modifica il metodo predefinito corrente in un metodo predefinito diverso. Al termine, l'utente vede il nuovo metodo predefinito nella pagina Informazioni di sicurezza.

## <a name="next-steps"></a>Passaggi successivi

[Forzare gli utenti a registrare nuovamente i metodi di autenticazione](howto-mfa-userdevicesettings.md#manage-user-authentication-options)

[Abilitare la registrazione combinata nel tenantEnable combined registration in your tenant](howto-registration-mfa-sspr-combined.md)

[Report di utilizzo e approfondimenti di SSPR e MFA](howto-authentication-methods-usage-insights.md)

[Metodi disponibili per Multi-Factor Authentication e SSPR](concept-authentication-methods.md)

[Configurare la reimpostazione della password self-service](howto-sspr-deployment.md)

[Configurazione di Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
