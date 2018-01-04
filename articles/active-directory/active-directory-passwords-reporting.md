---
title: 'Creazione di rapporti: reimpostazione password self-service di Azure AD | Documentazione Microsoft'
description: Creazione di rapporti in reimpostazione self-service della password di Azure AD
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: a6edc777b7b6ec3cfeacc8c548bb3c6ad306303c
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2017
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Opzioni di creazione di rapporti per la gestione delle password di Azure AD

Dopo la distribuzione molte organizzazioni vogliono sapere come o se il servizio Reimpostazione password self-service viene effettivamente usato. La funzionalità di creazione di report disponibile in Azure Active Directory (Azure AD) consente di rispondere a domande specifiche grazie a report predefiniti. Se si dispone di una licenza appropriata, è anche possibile creare query personalizzate.

![Creazione di report][Reporting]

È possibile rispondere alle domande seguenti grazie ai report presenti nel [portale di Azure] (https://portal.azure.com/):

> [!NOTE]
> È necessario essere [un amministratore globale](active-directory-assign-admin-roles-azure-portal.md) e si deve acconsentire esplicitamente alla raccolta di questi dati per conto dell'organizzazione. A tale scopo, è necessario visualizzare almeno una volta la scheda **Creazione report** o i log di controllo. I dati vengono raccolti per l'organizzazione solo dopo questa operazione.
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

Per gli utenti di Power BI è disponibile un pacchetto di contenuto per Azure AD che include funzionalità semplici da usare per la creazione di report per la reimpostazione delle password self-service. Per altre informazioni su come usare e distribuire il pacchetto di contenuto, vedere l'articolo [Come usare il pacchetto di contenuto Power BI di Azure Active Directory](active-directory-reporting-power-bi-content-pack-how-to.md). Grazie al pacchetto di contenuto è possibile creare dashboard personalizzati e condividerli con altri utenti nell'organizzazione.

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Come visualizzare i rapporti di gestione delle password nel portale di Azure

Nel portale di Azure è disponibile un modo migliore per visualizzare la reimpostazione delle password e l'attività di registrazione per la reimpostazione delle password. Attenersi alla procedura seguente per trovare gli eventi correlati alla reimpostazione delle password e alla registrazione per la reimpostazione delle password:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** nel riquadro a sinistra.
3. Cercare **Azure Active Directory** nell'elenco dei servizi e selezionarlo.
4. Selezionare **Utenti e gruppi**.
5. Selezionare **Log di controllo** dal menu **Utenti e gruppi**. Vengono visualizzati tutti gli eventi di controllo che si verificano per tutti gli utenti nella directory. È possibile filtrare la visualizzazione per vedere tutti gli eventi correlati alle password.
6. Per filtrare questa visualizzazione esclusivamente in base agli eventi correlati alla reimpostazione delle password, selezionare il pulsante **Filtra** nella parte superiore del riquadro.
7. Dal menu **Filtra** selezionare l'elenco a discesa **Categoria** e impostare l'elemento su **Self-service Password Management** (Gestione delle password self-service).
8. Facoltativamente, è possibile filtrare ulteriormente l'elenco scegliendo un'attività specifica in **Attività**.

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>Come recuperare eventi di gestione delle password dall'API relativa a report ed eventi di Azure AD

L'API di creazione di report ed eventi di Azure AD supporta il recupero di tutte le informazioni incluse nei report di reimpostazione delle password e di registrazione per la reimpostazione delle password. Tramite questa API è possibile scaricare eventi singoli di reimpostazione della password e di registrazione per la reimpostazione della password per l'integrazione con la tecnologia di creazione di report in uso.

> [!IMPORTANT]
> Attualmente, l'API di creazione di report ed eventi di Azure AD recupera fino a *75.000 eventi singoli* del tipo [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) e [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent). L'API comprende gli ultimi 30 giorni.
> 
> Se è necessario recuperare o archiviare dati oltre questo intervallo, è consigliabile salvarli in modo permanente in un database esterno e usare l'API per eseguire una query sui delta risultanti. È consigliabile iniziare a recuperare i dati quando nell'organizzazione si inizia a usare la funzionalità di reimpostazione delle password self-service. Rendere questi dati persistenti esternamente e quindi continuare a monitorarne i delta.
>

### <a name="how-to-get-started-with-the-reporting-api"></a>Come iniziare a usare l'API di creazione report

Per accedere a questi dati, è necessario scrivere una piccola applicazione o uno script per recuperarli dal server. Per altre informazioni, vedere [Introduzione all'API di creazione report di Azure Active Directory](active-directory-reporting-api-getting-started.md).

Dopo aver creato uno script di lavoro, è opportuno esaminare gli eventi di registrazione e di reimpostazione password che è possibile recuperare per soddisfare i requisiti degli scenari in uso:

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): elenca le colonne disponibili per gli eventi di reimpostazione delle password.
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): elenca le colonne disponibili per gli eventi di registrazione per la reimpostazione delle password.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Descrizione delle colonne dei report nel portale di Azure

L'elenco seguente descrive in modo dettagliato le colonne dei report nel portale di Azure:

* **Utente** : l'utente che ha provato a eseguire un'operazione di registrazione per la reimpostazione delle password.
* **Ruolo** : il ruolo dell'utente nella directory.
* **Data e ora** : la data e l'ora del tentativo.
* **Dati registrati**: i dati di autenticazione specificati dall'utente durante la registrazione per la reimpostazione delle password.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Descrizione dei valori dei report nel portale di Azure

Nella tabella seguente sono descritti i diversi valori che è possibile impostare per ogni colonna nel portale di Azure:

| Colonna | Valori consentiti e relativi significati |
| --- | --- |
| Dati registrati |**Indirizzo di posta elettronica alternativo**: l'utente ha usato un indirizzo di posta elettronica alternativo o di autenticazione per autenticarsi.<p><p>**Telefono ufficio**: l'utente ha usato il telefono dell'ufficio per autenticarsi.<p>**Telefono cellulare**: l'utente ha usato un telefono cellulare o di autenticazione per autenticarsi.<p>**Domande di sicurezza**: l'utente ha usato le domande di sicurezza per autenticarsi.<p>**Qualsiasi combinazione dei metodi precedenti (ad esempio, indirizzo di posta elettronica alternativo + cellulare)**: si verifica quando vengono specificati criteri con doppio controllo che indicano i due metodi usati dall'utente durante l'autenticazione per richiedere la reimpostazione della password. |

## <a name="self-service-password-management-activity-types"></a>Tipi di attività di gestione delle password self-service

Vengono visualizzati i seguenti tipi di attività nella categoria degli eventi di controllo **Self-Service Password Management** (Gestione delle password self-service):

* [Bloccato dalla reimpostazione self-service delle password](#activity-type-blocked-from-self-service-password-reset): indica che un utente ha tentato di reimpostare una password, usato un'attività di controllo specifica o convalidato un numero di telefono più di cinque volte in 24 ore.
* [Modificare la password (self-service)](#activity-type-change-password-self-service): indica che un utente ha eseguito una modifica della password volontaria o obbligata (a causa della scadenza).
* [Reimpostare la password (amministratore)](#activity-type-reset-password-by-admin): indica che un amministratore ha eseguito una reimpostazione della password per conto di un utente dal portale di Azure.
* [Reimpostare la password (self-service)](#activity-type-reset-password-self-service): indica che un utente ha reimpostato correttamente la propria password nel [portale di reimpostazione delle password di Azure AD](https://passwordreset.microsoftonline.com).
* [Stato di avanzamento dell'attività di reimpostazione della password self-service](#activity-type-self-serve-password-reset-flow-activity-progress): indica ogni passaggio specifico seguito da un utente (come il passaggio dell'attività di controllo dell'autenticazione per la reimpostazione della password) come parte del processo di reimpostazione della password.
* [Sbloccare l'account utente (self-service)](#activity-type-unlock-user-account-self-service): indica che un utente ha sbloccato il proprio account Active Directory senza reimpostare la password dal [portale di reimpostazione delle password di Azure AD](https://passwordreset.microsoftonline.com) usando la funzionalità di Active Directory per lo sblocco dell'account senza reimpostazione.
* [Utente registrato per la reimpostazione della password self-service](#activity-type-user-registered-for-self-service-password-reset): indica che un utente ha registrato tutte le informazioni necessarie per essere in grado di reimpostare la password in conformità con i criteri di reimpostazione della password del tenant attualmente specificati.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Tipo di attività: Bloccato dalla reimpostazione self-service delle password

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity Description** (Descrizione attività): indica che un utente ha tentato di reimpostare una password, usato un'attività di controllo specifica o convalidato un numero di telefono più di cinque volte in 24 ore.
* **Activity Actor** (Attore attività): l'utente per il quale è stata imposta una limitazione per ulteriori operazioni di reimpostazione. L'utente può essere un utente finale o un amministratore.
* **Activity Target** (Destinatario attività): l'utente per il quale è stata imposta una limitazione per ulteriori operazioni di reimpostazione. L'utente può essere un utente finale o un amministratore.
* **Stato attività**:
  * _Success_ (Operazione riuscita): indica che un utente è stato limitato dall'esecuzione di qualsiasi reimpostazione, tentativo di metodi di autenticazione alternativi o convalida di numeri di telefono aggiuntivi per le 24 ore successive.
* **Activity Status Failure Reason** (Motivo dell'errore sullo stato dell'attività): non applicabile.

### <a name="activity-type-change-password-self-service"></a>Tipo di attività: Modificare la password (self-service)

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity Description** (Descrizione attività): indica che un utente ha eseguito una modifica della password volontaria o obbligata (a causa della scadenza).
* **Activity Actor** (Attore attività): l'utente che ha modificato la propria password. L'utente può essere un utente finale o un amministratore.
* **Activity Target** (Destinatario attività): l'utente che ha modificato la propria password. L'utente può essere un utente finale o un amministratore.
* **Stati attività**:
  * _Success_ (Operazione riuscita): indica che un utente ha modificato correttamente la propria password.
  * _Failure_ (Operazione non riuscita): indica che un utente non è riuscito a modificare correttamente la propria password. È possibile selezionare la riga per visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.
* **Activity Status Failure Reason** (Motivo dell'errore sullo stato dell'attività): 
  * _FuzzyPolicyViolationInvalidPassword_: l'utente ha selezionato una password automaticamente esclusa dalle funzionalità di rilevamento delle password da escludere di Microsoft, che hanno riscontrato una password troppo comune o particolarmente debole.

### <a name="activity-type-reset-password-by-admin"></a>Tipo di attività: Reimpostare la password (amministratore)

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity Description** (Descrizione attività): indica che un amministratore ha eseguito una reimpostazione della password per conto di un utente dal portale di Azure.
* **Activity Actor** (Attore attività): l'amministratore che ha eseguito la reimpostazione della password per conto di un altro utente finale o amministratore. Deve essere un amministratore globale, un amministratore di password, un amministratore utenti o un amministratore supporto tecnico.
* **Activity Target** (Destinatario attività): l'utente la cui password è stata reimpostata. L'utente può essere un utente finale o un amministratore diverso.
* **Stati attività**:
  * _Success_ (Operazione riuscita): indica che un amministratore ha reimpostato correttamente la password dell'utente.
  * _Failure_ (Operazione non riuscita): indica che un amministratore non è riuscito a modificare correttamente la password di un utente. È possibile selezionare la riga per visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.

### <a name="activity-type-reset-password-self-service"></a>Tipo di attività: Reimpostare la password (self-service)

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity Description** (Descrizione attività): indica che un utente ha reimpostato correttamente la propria password nel [portale di reimpostazione delle password di Azure AD](https://passwordreset.microsoftonline.com).
* **Activity Actor** (Attore attività): l'utente che ha reimpostato la propria password. L'utente può essere un utente finale o un amministratore.
* **Activity Target** (Destinatario attività): l'utente che ha reimpostato la propria password. L'utente può essere un utente finale o un amministratore.
* **Stati attività**:
  * _Success_ (Operazione riuscita): indica che un utente ha reimpostato correttamente la propria password.
  * _Failure_ (Operazione non riuscita): indica che un utente non è riuscito a reimpostare correttamente la propria password. È possibile selezionare la riga per visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.
* **Activity Status Failure Reason** (Motivo dell'errore sullo stato dell'attività): 
  * _FuzzyPolicyViolationInvalidPassword_: l'amministratore ha selezionato una password automaticamente esclusa dalle funzionalità di rilevamento delle password da escludere di Microsoft, che hanno riscontrato una password troppo comune o particolarmente debole.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Tipo di attività: Stato di avanzamento dell'attività di reimpostazione della password self-service

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity Description** (Descrizione attività): indica ogni passaggio specifico seguito da un utente (come il passaggio dell'attività di controllo dell'autenticazione per la reimpostazione della password) come parte del processo di reimpostazione della password.
* **Activity Actor** (Attore attività): l'utente che ha eseguito parte della password del flusso di reimpostazione della password. L'utente può essere un utente finale o un amministratore.
* **Activity Target** (Destinatario attività): l'utente che ha eseguito parte della password del flusso di reimpostazione della password. L'utente può essere un utente finale o un amministratore.
* **Stati attività**:
  * _Success_ (Operazione riuscita): indica che un utente ha completato un passaggio specifico del flusso di reimpostazione della password.
  * _Failure_ (Operazione non riuscita): indica che un passaggio specifico del flusso di reimpostazione della password non è riuscito. È possibile selezionare la riga per visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.
* **Activity status reasons** (Motivi dello stato attività): vedere la tabella seguente per [tutti i motivi degli stati relativi alle attività di reimpostazione consentite](#allowed-values-for-details-column).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Tipo di attività: sbloccare un account utente (self-service)

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity Description** (Descrizione attività): indica che un utente ha sbloccato il proprio account Active Directory senza reimpostare la password dal [portale di reimpostazione delle password di Azure AD](https://passwordreset.microsoftonline.com) usando la funzionalità di Active Directory per lo sblocco dell'account senza reimpostazione.
* **Activity Actor** (Attore attività): l'utente che ha sbloccato il proprio account senza reimpostare la password. L'utente può essere un utente finale o un amministratore.
* **Activity Target** (Destinatario attività): l'utente che ha sbloccato il proprio account senza reimpostare la password. L'utente può essere un utente finale o un amministratore.
* **Allowed Activity Statuses** (Stati attività consentite):
  * _Success_ (Operazione riuscita): indica che un utente ha sbloccato correttamente il proprio account.
  * _Failure_ (Operazione non riuscita): indica che un utente non è riuscito a sbloccare correttamente il proprio account. È possibile selezionare la riga per visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Tipo di attività: Utente registrato per la reimpostazione della password self-service

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity Description** (Descrizione attività): indica che un utente ha registrato tutte le informazioni necessarie per essere in grado di reimpostare la password in conformità con i criteri di reimpostazione della password del tenant attualmente specificati. 
* **Activity Actor** (Attore attività): l'utente che ha eseguito la registrazione per la reimpostazione della password. L'utente può essere un utente finale o un amministratore.
* **Activity Target** (Destinatario attività): l'utente che ha eseguito la registrazione per la reimpostazione della password. L'utente può essere un utente finale o un amministratore.
* **Allowed Activity Statuses** (Stati attività consentite):
  * _Success_ (Operazione riuscita): indica che un utente è riuscito a eseguire la registrazione per la reimpostazione della password in base ai criteri correnti. 
  * _Failure_ (Operazione non riuscita): indica che un utente non è riuscito a eseguire la registrazione per la reimpostazione della password. È possibile selezionare la riga per visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore. 

     >[!NOTE]
     >Failure (Operazione non riuscita) non significa che un utente non è in grado di reimpostare la propria password. Indica invece che l'utente non ha completato il processo di registrazione. Se sull'account dell'utente sono presenti dati non verificati e corretti, ad esempio un numero di telefono non convalidato, anche se non si esegue la verifica di questo numero di telefono, l'utente può comunque usarlo per reimpostare la password. Per altre informazioni, vedere [Cosa accade quando un utente si registra?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers)
     >

## <a name="next-steps"></a>Passaggi successivi

* [Come completare l'implementazione della reimpostazione della password self-service per gli utenti](active-directory-passwords-best-practices.md)
* [Reimpostare o modificare la password](active-directory-passwords-update-your-own-password.md).
* [Registrarsi per la reimpostazione della password self-service](active-directory-passwords-reset-register.md).
* [Domande sulle licenze](active-directory-passwords-licensing.md)
* [Dati usati dalla reimpostazione della password self-service e dati da immettere per gli utenti](active-directory-passwords-data.md)
* [Metodi di autenticazione disponibili per gli utenti](active-directory-passwords-how-it-works.md#authentication-methods)
* [Opzioni dei criteri per la reimpostazione della password self-service](active-directory-passwords-policy.md)
* [Panoramica del writeback delle password](active-directory-passwords-writeback.md)
* [Informazioni sulle opzioni della reimpostazione della password self-service](active-directory-passwords-how-it-works.md)
* [Come risolvere i problemi di reimpostazione della password self-service](active-directory-passwords-troubleshoot.md)
* [Altre informazioni non illustrate altrove](active-directory-passwords-faq.md)

[Reporting]: ./media/active-directory-passwords-reporting/sspr-reporting.png "Esempio di log di controllo delle attività di reimpostazione password self-service in Azure AD"
