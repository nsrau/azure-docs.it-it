---
title: Delegare le autorizzazioni di creazione e gestione dell'amministratore dell'applicazione-Azure Active Directory | Microsoft Docs
description: Concedere le autorizzazioni per la gestione dell'accesso alle applicazioni in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 09/04/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b5479bc7f4e65f23a2e2dcf7deb91742fe50610
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382497"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Delegare le autorizzazioni di registrazione all'app in Azure Active Directory

Questo articolo descrive come usare le autorizzazioni concesse dai ruoli personalizzati in Azure Active Directory (Azure AD) per soddisfare le esigenze di gestione delle applicazioni. In Azure AD è possibile delegare le autorizzazioni per la creazione e la gestione delle applicazioni nei modi seguenti:

- [Limitazione degli utenti che possono creare applicazioni](#restrict-who-can-create-applications) e gestire le applicazioni create. Per impostazione predefinita, in Azure AD, tutti gli utenti possono registrare le registrazioni dell'applicazione e gestire tutti gli aspetti delle applicazioni che creano. Questo può essere limitato in modo da consentire solo a utenti selezionati tale autorizzazione.
- [Assegnazione di uno o più proprietari a un'applicazione](#assign-application-owners). Si tratta di un modo semplice per concedere a un utente la possibilità di gestire tutti gli aspetti della configurazione Azure AD per un'applicazione specifica.
- [Assegnazione di un ruolo amministrativo predefinito](#assign-built-in-application-admin-roles) che concede l'accesso per gestire la configurazione in Azure ad per tutte le applicazioni. Questo è il modo consigliato per concedere agli esperti IT l'accesso per gestire le autorizzazioni di configurazione delle applicazioni generali senza concedere l'accesso per gestire altre parti di Azure AD non correlate alla configurazione dell'applicazione.
- [Creazione di un ruolo personalizzato](#create-and-assign-a-custom-role-preview) che definisce autorizzazioni molto specifiche e relativa assegnazione a un utente nell'ambito di una singola applicazione come proprietario limitato oppure nell'ambito di directory (tutte le applicazioni) come amministratore con limitazioni.

È importante considerare la possibilità di concedere l'accesso usando uno dei metodi descritti in precedenza per due motivi. Innanzitutto, delegare la possibilità di eseguire attività amministrative riduce il sovraccarico dell'amministratore globale. In secondo luogo, l'utilizzo di autorizzazioni limitate migliora il comportamento di sicurezza e riduce il rischio di accesso non autorizzato. Per indicazioni generali e informazioni sui problemi relativi alla delega, vedere [Delegare i ruoli di amministratore in Azure Active Directory](roles-concept-delegation.md).

## <a name="restrict-who-can-create-applications"></a>Limitare gli utenti che possono creare applicazioni

Per impostazione predefinita, in Azure AD, tutti gli utenti possono registrare le registrazioni dell'applicazione e gestire tutti gli aspetti delle applicazioni che creano. Tutti hanno anche la possibilità di concedere il consenso alle app che accedono ai dati aziendali per loro conto. È possibile scegliere di concedere tali autorizzazioni in modo selettivo impostando il commutatore globale su' No ' e aggiungendo gli utenti selezionati al ruolo di sviluppatore dell'applicazione.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>Per disabilitare la capacità predefinita di creare registrazioni di applicazioni o il consenso alle applicazioni

1. Accedere all'organizzazione Azure AD con un account idoneo per il ruolo di amministratore globale nell'organizzazione Azure AD.
1. Impostare una o entrambe le opzioni seguenti:

    - Nella [pagina impostazioni utente per l'organizzazione](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)impostare l'impostazione **utenti può registrare le applicazioni** su No. In questo modo si disabilita la capacità predefinita per gli utenti di creare registrazioni di applicazioni.
    - Nelle [impostazioni utente per le applicazioni aziendali](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)impostare gli utenti in modo **da consentire alle applicazioni che accedono ai dati aziendali per conto** dell'utente di impostare su No. Questa operazione Disabilita la capacità predefinita per gli utenti di fornire il consenso alle applicazioni che accedono ai dati aziendali per loro conto.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>Concedere le singole autorizzazioni per la creazione e il consenso alle applicazioni quando la funzionalità predefinita è disabilitata

Assegnare il ruolo Sviluppatore applicazioni per concedere la possibilità di creare registrazioni di applicazioni quando l'impostazione **utenti può registrare le applicazioni** è impostata su No. Questo ruolo concede anche l'autorizzazione al consenso per conto dell'utente quando gli **utenti possono dare il consenso alle app che accedono ai dati aziendali per loro conto** . l'impostazione è impostata su No. Come comportamento del sistema, quando un utente crea una nuova registrazione dell'applicazione, viene automaticamente aggiunto come primo proprietario. Le autorizzazioni di proprietà consentono all'utente di gestire tutti gli aspetti di una registrazione dell'applicazione o di un'applicazione aziendale di cui sono proprietari.

## <a name="assign-application-owners"></a>Assegnare i proprietari dell'applicazione

L'assegnazione dei proprietari è un modo semplice per garantire la possibilità di gestire tutti gli aspetti della configurazione Azure AD per una specifica applicazione aziendale o di registrazione dell'applicazione. Come comportamento del sistema, quando un utente crea una nuova registrazione dell'applicazione vengono aggiunti automaticamente come primo proprietario. Le autorizzazioni di proprietà consentono all'utente di gestire tutti gli aspetti di una registrazione dell'applicazione o di un'applicazione aziendale di cui sono proprietari. Il proprietario originale può essere rimosso ed è possibile aggiungere altri proprietari.

### <a name="enterprise-application-owners"></a>Proprietari di applicazioni aziendali

In qualità di proprietario, un utente può gestire la configurazione specifica dell'organizzazione dell'applicazione aziendale, ad esempio la configurazione Single Sign-On, il provisioning e le assegnazioni utente. Un proprietario può anche aggiungere o rimuovere altri proprietari. Diversamente dagli amministratori globali, i proprietari possono gestire solo le applicazioni aziendali di loro proprietà.

In alcuni casi, le applicazioni aziendali create dalla raccolta di applicazioni includono sia un'applicazione aziendale che la registrazione di un'applicazione. Quando è true, l'aggiunta di un proprietario all'applicazione aziendale aggiunge automaticamente il proprietario alla registrazione dell'applicazione corrispondente come proprietario.

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>Per assegnare un proprietario a un'applicazione aziendale

1. Accedere all' [organizzazione](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) Azure ad con un account idoneo per l'amministratore dell'applicazione o l'amministratore di applicazioni cloud per l'organizzazione.
1. Nella [pagina](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) registrazioni app dell'organizzazione selezionare un'app per aprire la pagina Panoramica dell'app.
1. Selezionare **owners (proprietari** ) per visualizzare l'elenco dei proprietari dell'app.
1. Selezionare **Aggiungi** per selezionare uno o più proprietari da aggiungere all'app.

> [!IMPORTANT]
> Gli utenti e le entità servizio possono essere proprietari di registrazioni di applicazioni. Solo gli utenti possono essere proprietari di applicazioni aziendali. I gruppi non possono essere assegnati come proprietari di uno dei due.
>
> I proprietari possono aggiungere le credenziali a un'applicazione e usare tali credenziali per rappresentare l'identità dell'applicazione. È possibile che l'applicazione disponga di più autorizzazioni rispetto al proprietario e pertanto costituisce un'elevazione dei privilegi rispetto a cui il proprietario ha accesso come utente o entità servizio. Il proprietario di un'applicazione potrebbe potenzialmente creare o aggiornare utenti o altri oggetti durante la rappresentazione dell'applicazione, a seconda delle autorizzazioni dell'applicazione.

## <a name="assign-built-in-application-admin-roles"></a>Assegnare i ruoli di amministratore dell'applicazione predefiniti

Azure AD dispone di un set di ruoli di amministratore predefiniti per la concessione dell'accesso per la gestione della configurazione in Azure AD per tutte le applicazioni. Questi ruoli sono la soluzione consigliata per concedere agli esperti IT l'accesso per gestire le autorizzazioni di configurazione delle applicazioni generali senza concedere l'accesso per gestire altre parti di Azure AD non correlate alla configurazione dell'applicazione.

- Amministratore applicazione: gli utenti in questo ruolo possono creare e gestire tutti gli aspetti delle applicazioni aziendali, le registrazioni delle applicazioni e le impostazioni proxy dell'applicazione. Questo ruolo concede inoltre la possibilità consentire le autorizzazioni delegate e le autorizzazioni dell'applicazione escluse Microsoft Graph e Azure AD Graph. Gli utenti assegnati a questo ruolo non vengono aggiunti come proprietari per la creazione di nuove registrazioni di applicazioni o applicazioni aziendali.
- Amministratore applicazione cloud: gli utenti in questo ruolo hanno le stesse autorizzazioni del ruolo di amministratore di applicazioni, esclusa la possibilità di gestire il proxy dell'applicazione. Gli utenti assegnati a questo ruolo non vengono aggiunti come proprietari per la creazione di nuove registrazioni di applicazioni o applicazioni aziendali.

Per ulteriori informazioni e per visualizzare la descrizione di questi ruoli, vedere [available Roles](directory-assign-admin-roles.md#available-roles).

Seguire le istruzioni riportate nella Guida per l' [assegnazione di ruoli agli utenti con Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md) procedure per assegnare i ruoli di amministratore dell'applicazione o dell'amministratore dell'applicazione cloud.

> [!IMPORTANT]
> Gli amministratori di applicazioni e gli amministratori di applicazioni cloud possono aggiungere credenziali a un'applicazione e usare tali credenziali per rappresentare l'identità dell'applicazione. È possibile che l'applicazione disponga di autorizzazioni che rappresentano un'elevazione dei privilegi rispetto alle autorizzazioni del ruolo di amministratore. Un amministratore in questo ruolo potrebbe potenzialmente creare o aggiornare utenti o altri oggetti durante la rappresentazione dell'applicazione, a seconda delle autorizzazioni dell'applicazione.
> Nessuno dei ruoli consente di gestire le impostazioni di accesso condizionale.

## <a name="create-and-assign-a-custom-role-preview"></a>Creare e assegnare un ruolo personalizzato (anteprima)

La creazione di ruoli personalizzati e l'assegnazione di ruoli personalizzati sono passaggi distinti:

- [Creare una *definizione di ruolo* personalizzata](roles-create-custom.md) e [aggiungervi le autorizzazioni da un elenco di set di impostazioni](roles-custom-available-permissions.md). Si tratta delle stesse autorizzazioni utilizzate nei ruoli predefiniti.
- [Creare un' *assegnazione di ruolo* ](roles-assign-powershell.md) per assegnare il ruolo personalizzato.

Questa separazione consente di creare una singola definizione di ruolo e di assegnarla più volte in *ambiti*diversi. Un ruolo personalizzato può essere assegnato a livello di organizzazione oppure può essere assegnato all'ambito se un singolo oggetto Azure AD. Un esempio di ambito di un oggetto è costituito da una singola registrazione dell'app. Con ambiti diversi, la stessa definizione di ruolo può essere assegnata a Sally su tutte le registrazioni dell'app nell'organizzazione e quindi a Naver solo per la registrazione di app per la spesa di contoso.

Suggerimenti per la creazione e l'utilizzo di ruoli personalizzati per la delega della gestione delle applicazioni:
- I ruoli personalizzati concedono l'accesso solo nei pannelli di registrazione delle app più recenti del portale di Azure AD. Non concedono l'accesso nei pannelli registrazioni di app legacy.
- I ruoli personalizzati non concedono l'accesso al portale di Azure AD quando l'impostazione utente "limita l'accesso al portale di amministrazione di Azure AD" è impostata su Sì.
- Registrazioni app l'utente ha accesso a usando le assegnazioni di ruolo vengono visualizzate solo nella scheda ' tutte le applicazioni ' della pagina di registrazione dell'app. Non vengono visualizzati nella scheda "applicazioni di proprietà".

Per ulteriori informazioni sulle nozioni di base dei ruoli personalizzati, vedere [Cenni preliminari sui ruoli personalizzati](roles-custom-overview.md), nonché come [creare un ruolo personalizzato](roles-create-custom.md) e come [assegnare un ruolo](roles-assign-powershell.md).

## <a name="next-steps"></a>Passaggi successivi

- [Sottotipi e autorizzazioni di registrazione dell'applicazione](roles-custom-available-permissions.md)
- [Autorizzazioni del ruolo di amministratore in Azure Active Directory](directory-assign-admin-roles.md)
