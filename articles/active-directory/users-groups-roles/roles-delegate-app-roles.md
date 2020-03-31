---
title: Delegare le autorizzazioni di gestione dell'amministratore dell'applicazione - Azure AD Documenti Microsoft
description: Concedere le autorizzazioni per la gestione dell'accesso alle applicazioni in Azure Active DirectoryGrant permissions for application access management in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 251bc1c2277f9e43543f95c49d0b730a5a41c3d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253039"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Delegare le autorizzazioni di registrazione dell'app in Azure Active DirectoryDelegate app registration permissions in Azure Active Directory

Questo articolo descrive come usare le autorizzazioni concesse dai ruoli personalizzati in Azure Active Directory (Azure AD) per soddisfare le esigenze di gestione delle applicazioni. In Azure AD è possibile delegare le autorizzazioni di creazione e gestione delle applicazioni nei modi seguenti:In Azure AD, you can delegate Application creation and management permissions in the following ways:

- [Limitazione degli utenti che possono creare applicazioni](#restrict-who-can-create-applications) e gestire le applicazioni che creano. Per impostazione predefinita in Azure AD, tutti gli utenti possono registrare le registrazioni delle applicazioni e gestire tutti gli aspetti delle applicazioni create. Questa opzione può essere limitata per consentire solo a persone selezionate l'autorizzazione.
- [Assegnazione di uno o più proprietari a un'applicazione](#assign-application-owners). Si tratta di un modo semplice per concedere a un utente la possibilità di gestire tutti gli aspetti della configurazione di Azure AD per un'applicazione specifica.
- [Assegnazione di un ruolo amministrativo predefinito](#assign-built-in-application-admin-roles) che concede l'accesso per gestire la configurazione in Azure AD per tutte le applicazioni. Questo è il modo consigliato per concedere agli esperti IT l'accesso per gestire ampie autorizzazioni di configurazione delle applicazioni senza concedere l'accesso per gestire altre parti di Azure AD non correlate alla configurazione dell'applicazione.
- [Creazione di un ruolo personalizzato](#create-and-assign-a-custom-role-preview) che definisce autorizzazioni molto specifiche e assegnarlo a un utente all'ambito di una singola applicazione come proprietario limitato o nell'ambito della directory (tutte le applicazioni) come amministratore limitato.

È importante considerare la concessione dell'accesso utilizzando uno dei metodi descritti sopra per due motivi. In primo luogo, la delega della possibilità di eseguire attività amministrative riduce l'overhead dell'amministratore globale. In secondo luogo, l'utilizzo di autorizzazioni limitate migliora la sicurezza e riduce il rischio di accesso non autorizzato. Per indicazioni generali e informazioni sui problemi relativi alla delega, vedere [Delegare i ruoli di amministratore in Azure Active Directory](roles-concept-delegation.md).

## <a name="restrict-who-can-create-applications"></a>Limitare gli utenti che possono creare applicazioni

Per impostazione predefinita in Azure AD, tutti gli utenti possono registrare le registrazioni delle applicazioni e gestire tutti gli aspetti delle applicazioni create. Tutti hanno anche la possibilità di acconsentire alle app che accedono ai dati aziendali per loro conto. È possibile scegliere di concedere in modo selettivo tali autorizzazioni impostando le opzioni globali su "No" e aggiungendo gli utenti selezionati al ruolo Sviluppatore applicazioni.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>Per disabilitare la possibilità predefinita di creare registrazioni di applicazioni o il consenso alle applicazioni

1. Accedere all'organizzazione di Azure AD con un account idoneo per il ruolo di amministratore globale nell'organizzazione di Azure AD.
1. Impostare una o entrambe le opzioni seguenti:

    - Nella [pagina Impostazioni utente per l'organizzazione](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)impostare l'impostazione **Gli utenti possono registrare le applicazioni** su No. In questo modo verrà disabilitata la possibilità predefinita per gli utenti di creare registrazioni delle applicazioni.
    - Nelle [impostazioni utente per le applicazioni aziendali](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/), impostare l'impostazione Gli utenti possono acconsentire alle applicazioni che **accedono ai dati aziendali per loro conto su** No. In questo modo verrà disabilitata la possibilità predefinita per gli utenti di autorizzare le applicazioni che accedono ai dati aziendali per loro conto.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>Concedere autorizzazioni individuali per creare e concedere il consenso alle applicazioni quando la capacità predefinita è disabilitata

Assegnare il ruolo di sviluppatore Applicazione per concedere la possibilità di creare registrazioni applicazioni quando l'impostazione **Gli utenti possono registrare** le applicazioni è impostata su No.Assign the Application developer role to grant the ability to create application registrations when the Users can register applications setting is set to No. Questo ruolo concede anche l'autorizzazione al consenso per proprio conto quando l'impostazione **Gli utenti possono acconsentire alle app che accedono ai dati aziendali per loro conto** è impostata su No. Come comportamento del sistema, quando un utente crea una nuova registrazione dell'applicazione, vengono aggiunti automaticamente come primo proprietario. Le autorizzazioni di proprietà consentono all'utente di gestire tutti gli aspetti di una registrazione dell'applicazione o di un'applicazione aziendale di cui è proprietario.

## <a name="assign-application-owners"></a>Assegnare i proprietari delle applicazioni

L'assegnazione dei proprietari è un modo semplice per concedere la possibilità di gestire tutti gli aspetti della configurazione di Azure AD per una registrazione o un'applicazione aziendale specifica dell'applicazione. Come comportamento del sistema, quando un utente crea una nuova registrazione dell'applicazione vengono aggiunti automaticamente come primo proprietario. Le autorizzazioni di proprietà consentono all'utente di gestire tutti gli aspetti di una registrazione dell'applicazione o di un'applicazione aziendale di cui è proprietario. Il proprietario originale può essere rimosso e altri proprietari possono essere aggiunti.

### <a name="enterprise-application-owners"></a>Proprietari di applicazioni aziendali

In qualità di proprietario, un utente può gestire la configurazione specifica dell'organizzazione dell'applicazione aziendale, ad esempio la configurazione Single Sign-On, il provisioning e le assegnazioni utente. Un proprietario può anche aggiungere o rimuovere altri proprietari. A differenza degli amministratori globali, i proprietari possono gestire solo le applicazioni aziendali di cui sono proprietari.

In alcuni casi, le applicazioni aziendali create dalla raccolta di applicazioni includono sia un'applicazione aziendale che la registrazione dell'applicazione. In questo caso, l'aggiunta di un proprietario all'applicazione enterprise aggiunge automaticamente il proprietario alla registrazione dell'applicazione corrispondente come proprietario.

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>Per assegnare un proprietario a un'applicazione aziendale

1. Accedere [all'organizzazione](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) di Azure AD con un account idoneo per l'amministratore dell'applicazione o l'amministratore dell'applicazione cloud per l'organizzazione.
1. Nella [pagina](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) Registrazioni app per l'organizzazione selezionare un'app per aprire la pagina Panoramica per l'app.
1. Selezionare **Proprietari** per visualizzare l'elenco dei proprietari dell'app.
1. Selezionare **Aggiungi** per selezionare uno o più proprietari da aggiungere all'app.

> [!IMPORTANT]
> Gli utenti e le entità servizio possono essere proprietari delle registrazioni delle applicazioni. Solo gli utenti possono essere proprietari di applicazioni aziendali. I gruppi non possono essere assegnati come proprietari di entrambi i due gruppi.
>
> I proprietari possono aggiungere credenziali a un'applicazione e utilizzarle per rappresentare l'identità dell'applicazione. L'applicazione può disporre di più autorizzazioni rispetto al proprietario e pertanto sarebbe un'elevazione dei privilegi su ciò che il proprietario ha accesso a come utente o entità servizio. Il proprietario di un'applicazione potrebbe potenzialmente creare o aggiornare utenti o altri oggetti durante la rappresentazione dell'applicazione, a seconda delle autorizzazioni dell'applicazione.

## <a name="assign-built-in-application-admin-roles"></a>Assegnare ruoli di amministratore applicazioni incorporatiAssign built-in application admin roles

Azure AD include un set di ruoli di amministratore predefiniti per concedere l'accesso per gestire la configurazione in Azure AD per tutte le applicazioni. Questi ruoli sono il modo consigliato per concedere agli esperti IT l'accesso per gestire ampie autorizzazioni di configurazione dell'applicazione senza concedere l'accesso per gestire altre parti di Azure AD non correlate alla configurazione dell'applicazione.

- Amministratore di applicazioni: gli utenti in questo ruolo possono creare e gestire tutti gli aspetti di applicazioni aziendali, le registrazioni delle applicazioni e le impostazioni proxy dell'applicazione. Questo ruolo consente inoltre di concedere la possibilità di concedere il consenso alle autorizzazioni delegate e alle autorizzazioni dell'applicazione, escluso Microsoft Graph. Gli utenti assegnati a questo ruolo non vengono aggiunti come proprietari durante la creazione di nuove registrazioni di applicazioni o applicazioni aziendali.
- Amministratore applicazione cloud: gli utenti in questo ruolo dispongono delle stesse autorizzazioni del ruolo di amministratore di applicazioni, esclusa la possibilità di gestire il proxy dell'applicazione. Gli utenti assegnati a questo ruolo non vengono aggiunti come proprietari durante la creazione di nuove registrazioni di applicazioni o applicazioni aziendali.

Per ulteriori informazioni e per visualizzare la descrizione di questi ruoli, vedere [Ruoli disponibili](directory-assign-admin-roles.md#available-roles).

Seguire le istruzioni nella guida alle procedure [per assegnare ruoli agli utenti con Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md) per assegnare i ruoli Amministratore applicazioni o Amministratore applicazioni cloud.

> [!IMPORTANT]
> Gli amministratori dell'applicazione e gli amministratori delle applicazioni cloud possono aggiungere credenziali a un'applicazione e utilizzarle per rappresentare l'identità dell'applicazione. L'applicazione può disporre di autorizzazioni che sono un'elevazione dei privilegi sulle autorizzazioni del ruolo di amministratore. Un amministratore in questo ruolo potrebbe potenzialmente creare o aggiornare utenti o altri oggetti durante la rappresentazione dell'applicazione, a seconda delle autorizzazioni dell'applicazione.
> Nessuno dei ruoli consente di gestire le impostazioni di accesso condizionale.

## <a name="create-and-assign-a-custom-role-preview"></a>Creare e assegnare un ruolo personalizzato (anteprima)Create and assign a custom role (preview)

La creazione di ruoli personalizzati e l'assegnazione di ruoli personalizzati sono passaggi separati:Creating custom roles and assigning custom roles are separate steps:

- [Creare una *definizione* ](roles-create-custom.md) di ruolo personalizzata e [aggiungervi autorizzazioni da un elenco preimpostato.](roles-custom-available-permissions.md) Si tratta delle stesse autorizzazioni utilizzate nei ruoli predefiniti.
- [Creare *un'assegnazione* ](roles-assign-powershell.md) di ruolo per assegnare il ruolo personalizzato.

Questa separazione consente di creare una singola definizione di ruolo e quindi assegnarla più volte in *ambiti*diversi. Un ruolo personalizzato può essere assegnato nell'ambito a livello di organizzazione oppure nell'ambito se un singolo oggetto Azure AD. Un esempio di ambito di un oggetto è la registrazione di una singola app. Utilizzando ambiti diversi, la stessa definizione di ruolo può essere assegnata a Sally su tutte le registrazioni di app nell'organizzazione e quindi a Naveen solo per la registrazione dell'app Contoso Expense Reports.

Suggerimenti per la creazione e l'utilizzo di ruoli personalizzati per la delega della gestione delle applicazioni:
- I ruoli personalizzati concedono l'accesso solo nei pannelli di registrazione app più aggiornati del portale di Azure AD. Non concedono l'accesso nei blade di registrazione dell'app legacy.
- I ruoli personalizzati non concedono l'accesso al portale di Azure AD quando l'impostazione utente "Limita l'accesso al portale di amministrazione di Azure AD" è impostata su Sì.Custom roles do not grant access to the Azure AD portal when the "Restrict access to Azure AD administration portal" user setting is set to Yes.
- Le registrazioni delle app a cui l'utente ha accesso utilizzando le assegnazioni di ruolo vengono visualizzate solo nella scheda "Tutte le applicazioni" della pagina Registrazione app. Non vengono visualizzati nella scheda "Applicazioni di proprietà".

Per ulteriori informazioni sulle nozioni di base dei ruoli personalizzati, vedere Panoramica dei [ruoli personalizzati,](roles-custom-overview.md)nonché come [creare un ruolo personalizzato](roles-create-custom.md) e come [assegnare un ruolo.](roles-assign-powershell.md)

## <a name="next-steps"></a>Passaggi successivi

- [Sottotipi e autorizzazioni di registrazione delle applicazioni](roles-custom-available-permissions.md)
- [Autorizzazioni del ruolo di amministratore in Azure Active Directory](directory-assign-admin-roles.md)
