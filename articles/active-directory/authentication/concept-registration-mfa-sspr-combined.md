---
title: Registrazione combinata per SSPR e multi-factor authentication-Azure Active Directory
description: Azure AD Multi-Factor Authentication e la registrazione per la reimpostazione della password self-service (anteprima)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3221e6424b449066510667f8067e3bc6d8aec12a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381949"
---
# <a name="combined-security-information-registration-preview"></a>Registrazione di informazioni di sicurezza combinate (anteprima)

Prima della registrazione combinata, gli utenti registravano i metodi di autenticazione per Azure Multi-Factor Authentication e la reimpostazione della password self-service (SSPR) separatamente. Gli utenti erano confusi che venivano usati metodi simili per Multi-Factor Authentication e SSPR, ma dovevano registrarsi per entrambe le funzionalità. Ora, con la registrazione combinata, gli utenti possono registrarsi una sola volta e sfruttare i vantaggi di Multi-Factor Authentication e SSPR.

![Profilo personale che mostra le informazioni di sicurezza registrate per un utente](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Prima di abilitare la nuova esperienza, esaminare questa documentazione incentrata sull'amministratore e la documentazione incentrata sull'utente per assicurarsi di comprendere la funzionalità e l'effetto di questa funzionalità. Basare il training sulla [documentazione dell'utente](../user-help/user-help-security-info-overview.md) per preparare gli utenti per la nuova esperienza e contribuire a garantire una corretta implementazione.

Azure AD registrazione di informazioni di sicurezza combinata non è attualmente disponibile per i cloud nazionali come Azure US Government, Azure Germania o Azure Cina 21Vianet.

|     |
| --- |
| La registrazione delle informazioni di sicurezza combinate per la reimpostazione della password Self-Service Multi-Factor Authentication e Azure Active Directory (Azure AD) è una funzionalità di anteprima pubblica di Azure AD. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!IMPORTANT]
> Gli utenti abilitati per l'anteprima originale e per l'esperienza di registrazione combinata migliorata vedranno il nuovo comportamento. Gli utenti abilitati per entrambe le esperienze vedranno solo la nuova esperienza profilo personale. Il nuovo profilo è allineato all'aspetto della registrazione combinata e offre un'esperienza uniforme per gli utenti. Per visualizzare il profilo, gli utenti possono passare a [https://myprofile.microsoft.com](https://myprofile.microsoft.com).

> [!NOTE] 
> È possibile che venga visualizzato un messaggio di errore durante il tentativo di accedere all'opzione Info di sicurezza. Ad esempio, "spiacenti, non è possibile accedere". In questo caso, verificare che non sia presente alcun oggetto Criteri di gruppo o di configurazione che blocca i cookie di terze parti nel Web browser. 

Le pagine del profilo sono localizzate in base alle impostazioni della lingua del computer che accede alla pagina. Microsoft archivia il linguaggio più recente usato nella cache del browser, quindi i tentativi successivi di accesso alle pagine continueranno a essere visualizzati nell'ultima lingua usata. Se si cancella la cache, viene eseguito nuovamente il rendering delle pagine. Se si vuole forzare una lingua specifica, è possibile aggiungere `?lng=<language>` alla fine dell'URL, dove `<language>` è il codice della lingua di cui si vuole eseguire il rendering.

![Configurare SSPR o altri metodi di verifica della sicurezza](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Metodi disponibili nella registrazione combinata

La registrazione combinata supporta le azioni e i metodi di autenticazione seguenti:

|   | Register | Modifica | Elimina |
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

> [!NOTE]
> Le password dell'app sono disponibili solo per gli utenti che sono stati applicati per Multi-Factor Authentication. Le password dell'app non sono disponibili per gli utenti abilitati per Multi-Factor Authentication tramite criteri di accesso condizionale.

Gli utenti possono impostare una delle opzioni seguenti come metodo predefinito Multi-Factor Authentication:

- Microsoft Authenticator: notifica.
- App Authenticator o token hardware: codice.
- Telefonata.
- Messaggio di testo.

Continuando ad aggiungere altri metodi di autenticazione per Azure AD, questi metodi saranno disponibili nella registrazione combinata.

## <a name="combined-registration-modes"></a>Modalità di registrazione combinate

Sono disponibili due modalità di registrazione combinata: interrupt e Gestisci.

- La **modalità interrupt** è un'esperienza simile a una procedura guidata, presentata agli utenti durante la registrazione o l'aggiornamento delle informazioni di sicurezza al momento dell'accesso.

- La **modalità di gestione** è parte del profilo utente e consente agli utenti di gestire le informazioni di sicurezza.

Per entrambe le modalità, gli utenti che hanno precedentemente registrato un metodo che può essere usato per Multi-Factor Authentication dovranno eseguire Multi-Factor Authentication prima di poter accedere alle informazioni di sicurezza.

### <a name="interrupt-mode"></a>Modalità di interrupt

La registrazione combinata rispetta i criteri Multi-Factor Authentication e SSPR, se entrambi sono abilitati per il tenant. Questi criteri controllano se un utente viene interrotto per la registrazione durante l'accesso e quali metodi sono disponibili per la registrazione.

Di seguito sono riportati diversi scenari in cui agli utenti potrebbe essere richiesto di registrare o aggiornare le informazioni di sicurezza:

- Multi-Factor Authentication registrazione applicata tramite Identity Protection: agli utenti viene richiesto di effettuare la registrazione durante l'accesso. Registrano Multi-Factor Authentication metodi e metodi SSPR (se l'utente è abilitato per SSPR).
- Multi-Factor Authentication registrazione applicata tramite Multi-Factor Authentication per utente: agli utenti viene richiesto di effettuare la registrazione durante l'accesso. Registrano Multi-Factor Authentication metodi e metodi SSPR (se l'utente è abilitato per SSPR).
- Multi-Factor Authentication registrazione applicata tramite l'accesso condizionale o altri criteri: agli utenti viene richiesto di effettuare la registrazione quando usano una risorsa che richiede Multi-Factor Authentication. Registrano Multi-Factor Authentication metodi e metodi SSPR (se l'utente è abilitato per SSPR).
- Registrazione SSPR applicata: agli utenti viene richiesto di effettuare la registrazione durante l'accesso. Registrano solo metodi SSPR.
- Aggiornamento di SSPR applicato: gli utenti devono esaminare le informazioni di sicurezza a intervalli impostati dall'amministratore. Gli utenti visualizzano le informazioni e possono confermare le informazioni correnti o apportare modifiche, se necessario.

Quando viene applicata la registrazione, agli utenti viene mostrato il numero minimo di metodi necessari per essere conformi ai criteri di Multi-Factor Authentication e SSPR, dal più sicuro al meno sicuro.

Ad esempio:

- Un utente è abilitato per SSPR. Il criterio SSPR ha richiesto due metodi per reimpostare e ha abilitato il codice dell'app mobile, la posta elettronica e il telefono.
   - Questo utente è necessario per registrare due metodi.
      - Per impostazione predefinita, l'utente Visualizza l'app Authenticator e il telefono.
      - L'utente può scegliere di registrare la posta elettronica anziché l'app Authenticator o il telefono.

Questo diagramma di flusso descrive i metodi visualizzati da un utente quando viene interrotto per la registrazione durante l'accesso:

![Diagramma di flusso delle informazioni di sicurezza combinato](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Se sono abilitati sia Multi-Factor Authentication che SSPR, è consigliabile applicare la registrazione Multi-Factor Authentication.

Se il criterio SSPR richiede agli utenti di esaminare le info di sicurezza a intervalli regolari, gli utenti vengono interrotti durante l'accesso e mostrati tutti i metodi registrati. Possono confermare le informazioni correnti se sono aggiornate oppure possono apportare modifiche se lo sono, se necessario.

### <a name="manage-mode"></a>Modalità di gestione

Gli utenti possono accedere alla modalità di gestione passando a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) o selezionando **info di sicurezza** da profilo personale. Da qui, gli utenti possono aggiungere metodi, eliminare o modificare metodi esistenti, modificare il metodo predefinito e altro ancora.

## <a name="key-usage-scenarios"></a>Scenari di utilizzo chiave

### <a name="set-up-security-info-during-sign-in"></a>Configurare le informazioni di sicurezza durante l'accesso

Un amministratore ha applicato la registrazione.

Un utente non ha configurato tutte le informazioni di sicurezza necessarie e passa al portale di Azure. Dopo l'immissione di nome utente e password, all'utente viene richiesto di impostare le informazioni di sicurezza. L'utente segue quindi i passaggi illustrati nella procedura guidata per configurare le informazioni di sicurezza necessarie. Se le impostazioni lo consentono, l'utente può scegliere di impostare metodi diversi da quelli mostrati per impostazione predefinita. Al termine della procedura guidata, gli utenti esaminano i metodi impostati e il metodo predefinito per Multi-Factor Authentication. Per completare il processo di installazione, l'utente conferma le informazioni e continua con l'portale di Azure.

### <a name="set-up-security-info-from-my-profile"></a>Configura le info di sicurezza dal profilo personale

Un amministratore non ha applicato la registrazione.

Un utente che non ha ancora configurato tutte le informazioni di sicurezza necessarie passa a [https://myprofile.microsoft.com](https://myprofile.microsoft.com). L'utente seleziona **info di sicurezza** nel riquadro sinistro. Da qui, l'utente sceglie di aggiungere un metodo, seleziona uno dei metodi disponibili e segue i passaggi per la configurazione di tale metodo. Al termine, l'utente vedrà il metodo appena configurato nella pagina info di sicurezza.

### <a name="delete-security-info-from-my-profile"></a>Elimina le informazioni di sicurezza dal profilo personale

Un utente che ha precedentemente configurato almeno un metodo passa a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). L'utente sceglie di eliminare uno dei metodi precedentemente registrati. Al termine, l'utente non vedrà più questo metodo nella pagina delle informazioni di sicurezza.

### <a name="change-the-default-method-from-my-profile"></a>Modificare il metodo predefinito dal profilo personale

Un utente che ha precedentemente configurato almeno un metodo che può essere usato per Multi-Factor Authentication passa a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). L'utente modifica il metodo predefinito corrente impostando un metodo predefinito diverso. Al termine, l'utente Visualizza il nuovo metodo predefinito nella pagina delle informazioni di sicurezza.

## <a name="next-steps"></a>Passaggi successivi

[Forzare gli utenti a registrare nuovamente i metodi di autenticazione](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[Abilitare la registrazione combinata nel tenant](howto-registration-mfa-sspr-combined.md)

[Report sull'utilizzo e informazioni dettagliate su SSPR e multi-factor authentication](howto-authentication-methods-usage-insights.md)

[Metodi disponibili per Multi-Factor Authentication e SSPR](concept-authentication-methods.md)

[Configurare la reimpostazione self-service delle password](howto-sspr-deployment.md)

[Configurare Multi-Factor Authentication di Azure](howto-mfa-getstarted.md)
