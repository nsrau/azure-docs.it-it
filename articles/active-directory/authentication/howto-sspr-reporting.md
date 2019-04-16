---
title: Report di reimpostazione della password self-service - Azure Active Directory
description: Creazione di rapporti in reimpostazione self-service della password di Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bc2acaa24637c3297af8e91f01b67e5d30f2931
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59577968"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Opzioni di creazione di rapporti per la gestione delle password di Azure AD

Dopo la distribuzione molte organizzazioni vogliono sapere come o se il servizio Reimpostazione password self-service viene effettivamente usato. La funzionalità di creazione di report disponibile in Azure Active Directory (Azure AD) consente di rispondere a domande specifiche grazie a report predefiniti. Se si dispone di una licenza appropriata, è anche possibile creare query personalizzate.

![Creazione di report su SSPR usando il controllo del log in Azure AD][Reporting]

È possibile rispondere alle domande seguenti grazie ai report presenti nel [Portale di Azure](https://portal.azure.com/):

> [!NOTE]
> È necessario essere [un amministratore globale](../users-groups-roles/directory-assign-admin-roles.md) e si deve acconsentire esplicitamente alla raccolta di questi dati per conto dell'organizzazione. A tale scopo, è necessario visualizzare almeno una volta la scheda **Creazione report** o i log di controllo. I dati vengono raccolti per l'organizzazione solo dopo questa operazione.
>

* Quante persone si sono registrate per la reimpostazione delle password?
* Chi ha eseguito la registrazione per la reimpostazione delle password?
* Quali dati vengono registrati?
* Quante persone hanno reimpostato le proprie password negli ultimi sette giorni?
* Quali sono i metodi più comuni usati da utenti o amministratori per reimpostare le proprie password?
* Quali sono i problemi più comuni affrontati da utenti o amministratori quando provano a usare la reimpostazione delle password?
* Con quale frequenza gli amministratori reimpostano le proprie password?
* Sono state rilevate eventuali attività sospette nell'ambito della reimpostazione delle password?

## <a name="power-bi-content-pack"></a>Pacchetto di contenuto di Power BI

Per gli utenti di Power BI è disponibile un pacchetto di contenuto per Azure AD che include funzionalità semplici da usare per la creazione di report per la reimpostazione delle password self-service. Per altre informazioni su come usare e distribuire il pacchetto di contenuto, vedere l'articolo [Come usare il pacchetto di contenuto Power BI di Azure Active Directory](../reports-monitoring/howto-power-bi-content-pack.md). Grazie al pacchetto di contenuto è possibile creare dashboard personalizzati e condividerli con altri utenti nell'organizzazione.

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Come visualizzare i rapporti di gestione delle password nel portale di Azure

Nel portale di Azure è disponibile un modo migliore per visualizzare la reimpostazione delle password e l'attività di registrazione per la reimpostazione delle password. Attenersi alla procedura seguente per trovare gli eventi correlati alla reimpostazione delle password e alla registrazione per la reimpostazione delle password:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** nel riquadro a sinistra.
3. Cercare **Azure Active Directory** nell'elenco dei servizi e selezionarlo.
4. Selezionare **utenti** dalla sezione Gestisci.
5. Selezionare **log di controllo** dalle **utenti** pannello. Vengono visualizzati tutti gli eventi di controllo che si verificano per tutti gli utenti nella directory. È possibile filtrare la visualizzazione per vedere tutti gli eventi correlati alle password.
6. Dal **filtro** dal menu nella parte superiore del riquadro, seleziona il **servizio** elenco a discesa elenco e modificarla in modo che il **Self-Service Password Management** tipo di servizio.
7. Facoltativamente, è possibile filtrare ulteriormente l'elenco scegliendo un'attività specifica in **Attività**.

### <a name="converged-registration-preview"></a>Registrazione convergente (anteprima)

Se si fa parte dell'anteprima pubblica di registrazione convergente, le informazioni relative alle attività dell'utente nei log di controllo sarà disponibile il servizio **metodi di autenticazione**.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Descrizione delle colonne dei report nel portale di Azure

L'elenco seguente descrive in modo dettagliato le colonne dei report nel portale di Azure:

* **Utente**: utente che ha provato a eseguire un'operazione di registrazione per la reimpostazione della password.
* **Ruolo**: ruolo dell'utente nella directory.
* **Data e ora**: data e ora del tentativo.
* **Data Registered** (Dati registrati): dati di autenticazione specificati dall'utente durante la registrazione per la reimpostazione della password.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Descrizione dei valori dei report nel portale di Azure

Nella tabella seguente sono descritti i diversi valori che è possibile impostare per ogni colonna nel portale di Azure:

| Colonna | Valori consentiti e relativi significati |
| --- | --- |
| Dati registrati |**Indirizzo di posta elettronica alternativo**: per eseguire l'autenticazione, l'utente ha usato un indirizzo di posta elettronica alternativo o un indirizzo di posta elettronica per l'autenticazione.<p><p>**Telefono ufficio**: per eseguire l'autenticazione, l'utente ha usato il telefono dell'ufficio.<p>**Telefono cellulare**: per eseguire l'autenticazione, l'utente ha usato un telefono cellulare o un telefono per l'autenticazione.<p>**Domande di sicurezza**: per eseguire l'autenticazione, l'utente ha usato le domande di sicurezza.<p>**Qualsiasi combinazione dei metodi precedenti, ad esempio Indirizzo di posta elettronica alternativo e Telefono cellulare**: si verifica quando viene specificato un criterio a doppio controllo che indica i due metodi usati dall'utente per l'autenticazione della richiesta di reimpostazione della password. |

## <a name="self-service-password-management-activity-types"></a>Tipi di attività di gestione delle password self-service

Vengono visualizzati i seguenti tipi di attività nella categoria degli eventi di controllo **Self-Service Password Management** (Gestione delle password self-service):

* [Blocked from self-service password reset](#activity-type-blocked-from-self-service-password-reset) (Bloccato per reimpostazione password self-service): indica che un utente ha provato a reimpostare una password, ha usato un'attività di controllo specifica o ha convalidato un numero di telefono più di cinque volte in 24 ore.
* [Change password (self-service)](#activity-type-change-password-self-service) (Modifica password (self-service)): indica che un utente ha eseguito una modifica della password volontaria o obbligata (a causa della scadenza).
* [Reset password (by admin)](#activity-type-reset-password-by-admin) (Reimposta password (tramite amministratore)): indica che un amministratore ha eseguito una reimpostazione della password dal portale di Azure per conto di un utente.
* [Reset password (self-service)](#activity-type-reset-password-self-service) (Reimposta password (self-service)): indica che un utente ha reimpostato la propria password dal [portale di reimpostazione delle password di Azure AD](https://passwordreset.microsoftonline.com).
* [Self-service password reset flow activity progress](#activity-type-self-serve-password-reset-flow-activity-progress) (Stato di avanzamento attività di reimpostazione password self-service): indica ogni passaggio specifico seguito da un utente, ad esempio il passaggio di una specifica attività di controllo per l'autenticazione, durante il processo di reimpostazione della password.
* [Unlock user account (self-service)](#activity-type-unlock-a-user-account-self-service) (Sblocca account utente (self-service)): indica che un utente ha sbloccato l'account Active Directory senza reimpostare la propria password dal [portale di reimpostazione delle password di Azure AD](https://passwordreset.microsoftonline.com) usando la funzionalità di Active Directory per lo sblocco dell'account senza reimpostazione.
* [User registered for self-service password reset](#activity-type-user-registered-for-self-service-password-reset) (Utente registrato per reimpostazione password self-service): indica che un utente ha registrato tutte le informazioni necessarie per essere in grado di reimpostare la propria password in conformità con i criteri di reimpostazione della password del tenant attualmente specificati.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Tipo di attività: Bloccato dalla reimpostazione password self-service

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity description** (Descrizione attività): indica che un utente ha provato a reimpostare una password, ha usato un'attività di controllo specifica o ha convalidato un numero di telefono più di cinque volte in 24 ore.
* **Activity actor** (Attore attività): utente a cui è stata imposta una limitazione per ulteriori operazioni di reimpostazione. L'utente può essere un utente finale o un amministratore.
* **Activity target** (Destinatario attività): utente a cui è stata imposta una limitazione per ulteriori operazioni di reimpostazione. L'utente può essere un utente finale o un amministratore.
* **Stato attività**:
  * _Operazione riuscita_: indica che a un utente è stata imposta una limitazione per ulteriori operazioni di reimpostazione, tentativi di usare altri metodi di autenticazione o convalida di altri numeri di telefono per le 24 ore successive.
* **Activity Status Failure Reason** (Motivo dell'errore sullo stato dell'attività): Non applicabile

### <a name="activity-type-change-password-self-service"></a>Tipo di attività: Modificare la password (self-service)

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity description** (Descrizione attività): indica che un utente ha eseguito una modifica della password volontaria o obbligata (a causa della scadenza).
* **Activity actor** (Attore attività): utente che ha modificato la propria password. L'utente può essere un utente finale o un amministratore.
* **Activity target** (Destinatario attività): utente che ha modificato la propria password. L'utente può essere un utente finale o un amministratore.
* **Stati attività**:
  * _Operazione riuscita_: indica che un utente ha modificato la propria password.
  * _Operazione non riuscita_: indica che un utente non è riuscito a modificare la propria password. È possibile selezionare la riga per visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.
* **Activity Status Failure Reason** (Motivo dell'errore sullo stato dell'attività):
  * _FuzzyPolicyViolationInvalidPassword_: l'utente ha selezionato una password che è stata automaticamente esclusa perché le funzionalità di rilevamento delle password da escludere di Microsoft hanno riscontrato che tale password è troppo comune o particolarmente debole.

### <a name="activity-type-reset-password-by-admin"></a>Tipo di attività: Reimpostare la password (amministratore)

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity description** (Descrizione attività): indica che un amministratore ha eseguito una reimpostazione della password dal portale di Azure per conto di un utente.
* **Activity actor** (Attore attività): amministratore che ha eseguito la reimpostazione della password per conto di un altro utente finale o amministratore. Deve essere un amministratore password, l'utente amministratore o amministratore supporto tecnico.
* **Activity target** (Destinatario attività): utente la cui password è stata reimpostata. L'utente può essere un utente finale o un amministratore diverso.
* **Stati attività**:
  * _Operazione riuscita_: indica che un amministratore ha reimpostato la password di un utente.
  * _Operazione non riuscita_: indica che un amministratore non è riuscito a modificare la password di un utente. È possibile selezionare la riga per visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.

### <a name="activity-type-reset-password-self-service"></a>Tipo di attività: Reimpostare la password (self-service)

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity description** (Descrizione attività): indica che un utente ha reimpostato la propria password dal [portale di reimpostazione delle password di Azure AD](https://passwordreset.microsoftonline.com).
* **Activity actor** (Attore attività): utente che ha reimpostato la propria password. L'utente può essere un utente finale o un amministratore.
* **Activity target** (Destinatario attività): utente che ha reimpostato la propria password. L'utente può essere un utente finale o un amministratore.
* **Stati attività**:
  * _Operazione riuscita_: indica che un utente ha reimpostato la propria password.
  * _Operazione non riuscita_: indica che un utente non è riuscito a reimpostare la propria password. È possibile selezionare la riga per visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.
* **Activity Status Failure Reason** (Motivo dell'errore sullo stato dell'attività):
  * _FuzzyPolicyViolationInvalidPassword_: l'amministratore ha selezionato una password che è stata automaticamente esclusa perché le funzionalità di rilevamento delle password da escludere di Microsoft hanno riscontrato che tale password è troppo comune o particolarmente debole.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Tipo di attività: Self serve password reset flow activity progress (Stato di avanzamento attività di reimpostazione password self-service)

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity description** (Descrizione attività): indica ogni passaggio specifico seguito da un utente, ad esempio il passaggio di una specifica attività di controllo per l'autenticazione, durante il processo di reimpostazione della password.
* **Activity actor** (Attore attività): utente che ha eseguito parte del flusso di reimpostazione della password. L'utente può essere un utente finale o un amministratore.
* **Activity target** (Destinatario attività): utente che ha eseguito parte del flusso di reimpostazione della password. L'utente può essere un utente finale o un amministratore.
* **Stati attività**:
  * _Operazione riuscita_: indica che un utente ha completato un passaggio specifico del flusso di reimpostazione della password.
  * _Operazione non riuscita_: indica che un passaggio specifico del flusso di reimpostazione della password non è riuscito. È possibile selezionare la riga per visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.
* **Activity status reasons** (Motivi dello stato attività):   vedere la tabella seguente per [tutti i motivi degli stati relativi alle attività di reimpostazione consentite](#description-of-the-report-columns-in-the-azure-portal).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Tipo di attività: Unlock a user account (self-service) (Sblocca account utente (self-service))

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity description** (Descrizione attività): indica che un utente ha sbloccato l'account Active Directory senza reimpostare la propria password dal [portale di reimpostazione delle password di Azure AD](https://passwordreset.microsoftonline.com) usando la funzionalità di Active Directory per lo sblocco dell'account senza reimpostazione.
* **Activity actor** (Attore attività): utente che ha sbloccato il proprio account senza reimpostare la password. L'utente può essere un utente finale o un amministratore.
* **Activity target** (Destinatario attività): utente che ha sbloccato il proprio account senza reimpostare la password. L'utente può essere un utente finale o un amministratore.
* **Allowed Activity Statuses** (Stati attività consentite):
  * _Operazione riuscita_: indica che un utente ha sbloccato il proprio account.
  * _Operazione non riuscita_: indica che un utente non è riuscito a sbloccare il proprio account. È possibile selezionare la riga per visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Tipo di attività: Utente registrato per la reimpostazione della password self-service

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity description** (Descrizione attività): indica che un utente ha registrato tutte le informazioni necessarie per essere in grado di reimpostare la propria password in conformità con i criteri di reimpostazione della password del tenant attualmente specificati. 
* **Activity actor** (Attore attività): utente che ha eseguito la registrazione per la reimpostazione della password. L'utente può essere un utente finale o un amministratore.
* **Activity target** (Destinatario attività): utente che ha eseguito la registrazione per la reimpostazione della password. L'utente può essere un utente finale o un amministratore.
* **Allowed Activity Statuses** (Stati attività consentite):
  * _Operazione riuscita_: indica che un utente ha eseguito la registrazione per la reimpostazione della password in base ai criteri correnti. 
  * _Operazione non riuscita_: indica che un utente non è riuscito a eseguire la registrazione per la reimpostazione della password. È possibile selezionare la riga per visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.

     >[!NOTE]
     >Failure (Operazione non riuscita) non significa che un utente non è in grado di reimpostare la propria password. Indica invece che l'utente non ha completato il processo di registrazione. Se sull'account dell'utente sono presenti dati non verificati e corretti, ad esempio un numero di telefono non convalidato, anche se non si esegue la verifica di questo numero di telefono, l'utente può comunque usarlo per reimpostare la password.

## <a name="next-steps"></a>Passaggi successivi

* [Come completare l'implementazione della reimpostazione della password self-service per gli utenti](howto-sspr-deployment.md)
* [Reimpostare o modificare la password](../user-help/active-directory-passwords-update-your-own-password.md).
* [Registrarsi per la reimpostazione della password self-service](../user-help/active-directory-passwords-reset-register.md).
* [Domande sulle licenze](concept-sspr-licensing.md)
* [Dati usati dalla reimpostazione della password self-service e dati da immettere per gli utenti](howto-sspr-authenticationdata.md)
* [Metodi di autenticazione disponibili per gli utenti](concept-sspr-howitworks.md#authentication-methods)
* [Opzioni dei criteri per la reimpostazione della password self-service](concept-sspr-policy.md)
* [Panoramica del writeback delle password](howto-sspr-writeback.md)
* [Informazioni sulle opzioni della reimpostazione della password self-service](concept-sspr-howitworks.md)
* [Come risolvere i problemi di reimpostazione della password self-service](active-directory-passwords-troubleshoot.md)
* [Altre informazioni non illustrate altrove](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Esempio di log di controllo delle attività di reimpostazione password self-service in Azure AD"
