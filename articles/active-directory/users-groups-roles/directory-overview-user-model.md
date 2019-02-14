---
title: Panoramica su utenti, gruppi, licenze e ruoli - Azure Active Directory | Microsoft Docs
description: Relazione tra utenti e licenze assegnate, ruoli di amministratore, appartenenza a gruppi in Azure Active Directory
keywords: ''
author: curtand
manager: mtillman
ms.author: curtand
ms.reviewer: vincesm
ms.date: 01/28/2019
ms.topic: overview
ms.service: active-directory
ms.workload: identity
services: active-directory
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65983a5c9273b0eb88dbb7f2e3a8779b326352b3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56205393"
---
# <a name="users-groups-licensing-and-roles-for-large-organizations"></a>Utenti, gruppi, licenze e ruoli per organizzazioni di grandi dimensioni

Questo articolo fornisce un'introduzione per l'amministratore di Azure AD alla relazione tra le principali attività di [gestione delle identità](/azure/active-directory/fundamentals/identity-fundamentals?context=azure/active-directory/users-groups-roles/context/ugr-context) per gli utenti in termini di gruppi, licenze, app aziendali distribuite e ruoli di amministratore. Con la crescita dell'organizzazione, è possibile usare i gruppi e i ruoli di amministratore di Azure AD per:

* Assegnare licenze a gruppi anziché singolarmente
* Delegare le autorizzazioni necessarie per distribuire il lavoro di gestione di Azure AD a ruoli con meno privilegi
* Assegnare a gruppi l'accesso alle app aziendali

## <a name="assign-users-to-groups"></a>Assegnare utenti a gruppi

È possibile usare i gruppi in Azure AD per assegnare licenze a un numero elevato di utenti o per assegnare agli utenti l'accesso alle app aziendali distribuite. È possibile usare i gruppi per assegnare ruoli di amministratore in Azure AD o per concedere l'accesso a risorse esterne, ad esempio applicazioni SaaS o siti di SharePoint.

Per una maggiore flessibilità e per diminuire le operazioni di gestione delle appartenenze ai gruppi, è possibile usare i [gruppi dinamici](groups-create-rule.md) in Azure AD per ampliare e ridurre automaticamente il numero di membri dei gruppi. È necessaria una licenza Azure AD Premium P1 per ogni utente univoco membro di uno o più gruppi dinamici.

## <a name="assign-licenses-to-groups"></a>Assegnare licenze a gruppi

L'assegnazione o rimozione di licenze per singoli utenti può richiedere tempo e attenzione. Se invece si [assegnano le licenze a gruppi](/azure/active-directory/fundamentals/license-users-groups?context=azure/active-directory/users-groups-roles/context/ugr-context), è possibile semplificare la gestione delle licenze su larga scala.

In Azure AD, agli utenti che entrano a far parte di un gruppo con licenze vengono assegnate automaticamente le licenze appropriate. Quando gli utenti lasciano il gruppo, Azure AD rimuove le licenze assegnate. Senza i gruppi di Azure AD sarebbe necessario scrivere uno script di PowerShell oppure usare l'API Graph per aggiungere o rimuovere in blocco le licenze degli utenti che entrano in azienda o ne escono.

Se non vi sono licenze sufficienti disponibili o si verifica un problema, ad esempio piani di servizio che non possono essere assegnati allo stesso tempo, è possibile visualizzare lo stato di eventuali problemi di licenza del gruppo nel portale di Azure.

>[!NOTE]
>La funzionalità di licenze basate sui gruppi è attualmente in anteprima pubblica. Durante l'anteprima pubblica, la funzionalità è disponibile in tutti i piani di licenza a pagamento o di prova di Azure Active Directory (Azure AD).

## <a name="delegate-administrator-roles"></a>Delegare i ruoli di amministratore

Molte grandi organizzazioni richiedono la possibilità di assegnare ai propri utenti autorizzazioni sufficienti per le loro attività lavorative, senza necessità di assegnare il potente ruolo di amministratore globale, ad esempio per gli utenti che devono registrare applicazioni. Di seguito è riportato un esempio di nuovi ruoli di amministratore di Azure AD, che consentono di distribuire il lavoro di gestione delle applicazioni con maggiore granularità:

 Nome del ruolo | Riepilogo delle autorizzazioni
 --------- | -------------------
 **Amministratore di applicazioni** | Può aggiungere e gestire applicazioni aziendali e registrazioni di applicazioni e configurare le impostazioni proxy delle applicazioni. L'amministratore di applicazioni può visualizzare i criteri di accesso condizionale e i dispositivi, ma non gestirli.
 **Amministratore di applicazioni cloud** | Può aggiungere e gestire applicazioni aziendali e registrazioni di app aziendali. Questo ruolo ha tutte le autorizzazioni dell'amministratore di applicazioni, tranne quella per gestire le impostazioni proxy delle applicazioni.
**Sviluppatore di applicazioni** | Può aggiungere e aggiornare registrazioni di applicazioni, ma non può gestire le applicazioni aziendali né configurare i proxy delle applicazioni.

Nuovi ruoli di amministratore di Azure AD sono in via di aggiunta. Vedere il portale di Azure o il [riferimento alle autorizzazioni dei ruoli di amministratore](directory-assign-admin-roles.md) per conoscere i ruoli attualmente disponibili.

## <a name="assign-app-access"></a>Assegnare l'accesso alle app

È possibile usare Azure AD per assegnare l'accesso di gruppo alle [app aziendali distribuite nel proprio tenant di Azure AD](/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups?context=azure/active-directory/users-groups-roles/context/ugr-context). Se si combinano i gruppi dinamici con l'assegnazione di gruppi alle app, è possibile automatizzare le assegnazioni degli accessi utente adattandole alla crescita dell'organizzazione. Per assegnare l'accesso alle app aziendali è necessaria una licenza Azure Active Directory Premium P1 o Premium P2.

Azure AD consente anche un controllo granulare dei dati scambiati tra l'app e i gruppi a cui si è assegnato l'accesso. In [Applicazioni aziendali](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) aprire un'app e selezionare **Provisioning** per:

* Configurare il provisioning automatico per le app che lo supportano
* Specificare le credenziali di connessione all'API di gestione utenti dell'app
* Configurare i mapping che controllano quali attributi utente sono trasmessi tra Azure AD e l'app quando viene effettuato il provisioning o l'aggiornamento degli account utente
* Avviare e arrestare il servizio di provisioning di Azure AD per un'app, cancellare la cache di provisioning o riavviare il servizio
* Visualizzare il **report dell'attività di provisioning**, con il log di tutti gli utenti e i gruppi creati, aggiornati e rimossi tra Azure AD e l'app, e il **report degli errori di provisioning**, con messaggi di errore più dettagliati

## <a name="next-steps"></a>Passaggi successivi

Le nozioni di base per gli amministratori di Azure AD agli inizi sono disponibili nella [Documentazione sui concetti fondamentali di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/index).

È anche possibile cominciare a [creare gruppi](/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context), [assegnare licenze](/azure/active-directory/fundamentals/license-users-groups?context=azure/active-directory/users-groups-roles/context/ugr-context), [assegnare l'accesso alle app](/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups?context=azure/active-directory/users-groups-roles/context/ugr-context) o [assegnare i ruoli di amministratore](directory-assign-admin-roles.md).
