---
title: Combinazione di registrazione per multi-Factor Authentication (anteprima) - Azure Active Directory e Azure AD SSPR
description: Registrazione (anteprima) di reimpostazione della password self-service e multi-Factor Authentication di Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cf8d5cb13b39d58920555ff9d99a4949e1bfc20
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521434"
---
# <a name="combined-security-information-registration-preview"></a>Registrazione di informazioni di sicurezza combinati (anteprima)

Prima della registrazione combinata, gli utenti registrati metodi di autenticazione separatamente per Azure multi-Factor Authentication e reimpostazione della password self-service (SSPR). Le persone erano confusione che sono stati usati metodi simili per multi-Factor Authentication e SSPR ma che avevano la registrazione per entrambe le funzionalità. A questo punto, con la registrazione combinata, gli utenti possono registrare una sola volta e sfrutta i vantaggi del multi-Factor Authentication sia SSPR.

![Che mostra il profilo registrato info di sicurezza per un utente](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Prima di abilitare la nuova esperienza, esaminare questa documentazione incentrata su amministratore e la documentazione incentrato sull'utente per verificare di che conoscere le funzionalità e l'effetto di questa funzionalità. La formazione su documentazione per l'utente per preparare gli utenti per la nuova esperienza e contribuire a garantire una corretta implementazione di base.

Azure AD combinare le informazioni di sicurezza registrazione non è attualmente disponibile nei cloud nazionali, ad esempio Azure US Government, Azure Germania o Azure Cina 21Vianet.

|     |
| --- |
| Registrazione di informazioni di sicurezza combinato per multi-Factor Authentication e reimpostazione della password self-service di Azure Active Directory (Azure AD) è una funzionalità in anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!IMPORTANT]
> Gli utenti che sono abilitati per l'anteprima originale e l'esperienza di registrazione combinata avanzata verranno visualizzato il nuovo comportamento. Gli utenti che sono abilitati per entrambe le esperienze visualizzeranno la nuova esperienza del profilo personale. Il nuovo profilo personale in linea con l'aspetto della registrazione combinata e fornisce un'esperienza trasparente per gli utenti. Gli utenti possono visualizzare My Profile visitando [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com).

Le pagine del profilo sono localizzate in base alle impostazioni della lingua del computer accede alla pagina. Vengono archiviati il linguaggio più recente utilizzato nella cache del browser, in modo che i tentativi successivi di accedere alle pagine continuerà il rendering dell'ultima lingua utilizzata da Microsoft. Se si cancella la cache, le pagine verranno nuovamente visualizzati. Se si desidera forzare una lingua specifica, è possibile aggiungere `?lng=<language>` alla fine dell'URL, in cui `<language>` è riportato il codice della lingua che si desidera eseguire il rendering.

![Configurare SSPR o altri metodi di verifica di sicurezza](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Metodi disponibili nella registrazione combinata

Combinare i seguenti metodi di autenticazione supporta la registrazione e le azioni:

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
> Le password dell'App sono disponibili solo per gli utenti che sono stati applicati per multi-Factor Authentication. Le password dell'App non sono disponibili agli utenti che sono abilitati per multi-Factor Authentication tramite criteri di accesso condizionale.

Gli utenti possono impostare una delle opzioni seguenti come metodo predefinito per multi-Factor Authentication:

- Microsoft Authenticator: notifica.
- App Authenticator o all'hardware del token, del codice.
- Chiamata telefonica.
- Testo del messaggio.

Per continuare a aggiungere ulteriori metodi di autenticazione ad Azure AD, tali metodi saranno disponibili nella registrazione combinata.

## <a name="combined-registration-modes"></a>Modalità di registrazione combinato

Sono disponibili due modalità di registrazione combinata: interrompere e gestire.

- **Modalità di interrupt** è un'esperienza simile a procedura guidata, presentata agli utenti quando registrare o aggiornare le relative informazioni di sicurezza al momento dell'accesso.

- **Gestire la modalità** fa parte del profilo utente e consente agli utenti di gestire le tue info di sicurezza.

Per entrambe le modalità, gli utenti che hanno registrato in precedenza un metodo che può essere usato per multi-Factor Authentication saranno necessario eseguire l'autenticazione a più fattori prima di poter accedere le info di sicurezza.

### <a name="interrupt-mode"></a>Modalità di interrupt

Registrazione combinata rispetta i criteri di multi-Factor Authentication sia SSPR, se entrambi sono abilitati per il tenant. Questi criteri controllano se un utente viene interrotta per la registrazione durante l'accesso e quali metodi sono disponibili per la registrazione.

Ecco alcuni scenari in cui agli utenti potrebbero essere richiesto di registrare o aggiornare le tue info di sicurezza:

* Registrazione per l'autenticazione a più fattori applicata tramite Identity Protection: Gli utenti vengono richiesto di registrare durante l'accesso. Si registrano i metodi di multi-Factor Authentication e i metodi SSPR (se l'utente è abilitato per SSPR).
* Registrazione per l'autenticazione a più fattori applicata tramite multi-Factor Authentication per utente: Gli utenti vengono richiesto di registrare durante l'accesso. Si registrano i metodi di multi-Factor Authentication e i metodi SSPR (se l'utente è abilitato per SSPR).
* Applicato tramite l'accesso condizionale o altri criteri di registrazione per l'autenticazione a più fattori: Gli utenti vengono richiesto di registrare quando usano una risorsa che richiede l'autenticazione a più fattori. Si registrano i metodi di multi-Factor Authentication e i metodi SSPR (se l'utente è abilitato per SSPR).
* Registrazione SSPR applicata: Gli utenti vengono richiesto di registrare durante l'accesso. Si registrano solo i metodi di reimpostazione.
* Aggiornamento SSPR applicato: Gli utenti devono verificare le info di sicurezza in base all'intervallo impostato dall'amministratore. Gli utenti vengono visualizzati le relative informazioni e confermare le informazioni attuali o apportare le modifiche necessarie.

Quando viene applicata la registrazione, gli utenti vengono visualizzati il numero minimo di metodi necessari per essere conformi ai criteri multi-Factor Authentication sia SSPR, dal più specifico al meno sicuro.

Ad esempio: 

* Un utente è abilitato per SSPR. I criteri di SSPR necessarie due metodi per reimpostare e telefono, posta elettronica e il codice di app per dispositivi mobili sono state abilitate.
   * Questo utente deve registrare due metodi.
      * All'utente viene visualizzato l'app authenticator e telefono, per impostazione predefinita.
      * L'utente può scegliere di registrare l'indirizzo di posta elettronica anziché l'app authenticator o il telefono.

Questo diagramma di flusso vengono descritti i metodi che vengono visualizzati per un utente quando interrotta la registrazione durante l'accesso:

![Diagramma di flusso della sicurezza combinato info](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Se si dispone di multi-Factor Authentication e SSPR abilitate, è consigliabile applicare la registrazione multi-Factor Authentication.

Se i criteri di SSPR richiedono agli utenti di controllare le info di sicurezza a intervalli regolari, gli utenti vengono interrotti durante l'accesso e visualizzati tutti i relativi metodi registrati. È possibile confermare le informazioni attuali se è aggiornato oppure è possibile apportare modifiche se è necessario.

### <a name="manage-mode"></a>Gestire la modalità

Gli utenti possono accedere per gestire la modalità, passare a [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo) o selezionando **info di sicurezza** dal profilo personale. Da qui, gli utenti possono aggiungere i metodi, eliminare o modificare i metodi esistenti o modificare il metodo predefinito e altro ancora.

## <a name="key-usage-scenarios"></a>Principali scenari di utilizzo

### <a name="set-up-security-info-during-sign-in"></a>Configurare info di sicurezza durante l'accesso

Un amministratore è applicata la registrazione.

Un utente non ha configurato tutte le info di sicurezza necessarie e passa al portale di Azure. Dopo aver immesso il nome utente e password, l'utente viene richiesto di impostare le tue info di sicurezza. Quindi, l'utente segue i passaggi illustrati nella procedura guidata per configurare le info di sicurezza richiesto. Se le impostazioni lo consentono, l'utente può scegliere di configurare metodi diversi da quelli mostrati per impostazione predefinita. Dopo aver completato la procedura guidata, gli utenti verifichino i metodi configurati e il metodo predefinito per multi-Factor Authentication. Per completare il processo di installazione, l'utente conferma le informazioni e continua nel portale di Azure.

### <a name="set-up-security-info-from-my-profile"></a>Impostare le tue info di sicurezza dal profilo personale

Un amministratore non è applicata la registrazione.

Un utente che non ha ancora impostato tutte le info di sicurezza necessarie passa a [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com). L'utente sceglie **info di sicurezza** nel riquadro sinistro. Da qui, l'utente sceglie di aggiungere un metodo, consente di selezionare uno dei metodi disponibili e vengono seguiti i passaggi per configurare tale metodo. Al termine, l'utente vede il metodo che è stato sufficiente impostare la pagina delle info di sicurezza.

### <a name="delete-security-info-from-my-profile"></a>Elimina info di sicurezza dal profilo personale

Un utente che ha precedentemente configurato almeno un metodo passa a [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). L'utente sceglie di eliminare uno dei metodi registrati in precedenza. Al termine, tale metodo l'utente non è più visibile la pagina delle info di sicurezza.

### <a name="change-the-default-method-from-my-profile"></a>Modificare il metodo predefinito dal profilo personale

Un utente che ha precedentemente configurato almeno un metodo che può essere usato per multi-Factor Authentication consente di passare alla [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). L'utente modifica il metodo predefinito corrente a un metodo predefiniti diversi. Al termine, l'utente vede il metodo predefinito di nuovo la pagina delle info di sicurezza.

## <a name="next-steps"></a>Passaggi successivi

[Abilitare la registrazione combinata nel tenant](howto-registration-mfa-sspr-combined.md)

[Metodi disponibili per multi-Factor Authentication e SSPR](concept-authentication-methods.md)

[Configurare la reimpostazione password self-service](howto-sspr-deployment.md)

[Configurare Azure multi-Factor Authentication](howto-mfa-getstarted.md)
