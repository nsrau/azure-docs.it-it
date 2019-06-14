---
title: Gestione dell'accesso alle app con Azure AD | Documentazione Microsoft
description: Descrive come Azure Active Directory consente alle organizzazioni di specificare le app a cui ha accesso ogni utente.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f86dfd414309e8420dc19c92c1cd20c40a5a13a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108192"
---
# <a name="managing-access-to-apps"></a>Gestione dell'accesso alle app
La gestione dell'accesso, la valutazione dell'utilizzo e la creazione di report durante l'utilizzo continuano a rappresentare una sfida dopo l'integrazione di un'app nel sistema di identità dell'organizzazione. Nella maggior parte dei casi, gli amministratori IT o l'helpdesk devono assumere un ruolo attivo nella gestione dell'accesso alle app. In alcuni casi, l'assegnazione viene eseguita da un team IT generale o di reparto. Spesso, la decisione di assegnazione deve essere delegata al responsabile aziendale, di cui è richiesta l'approvazione prima che l'IT esegua l'assegnazione.  Altre organizzazioni investono nell'integrazione mediante sistemi automatizzati esistenti di gestione dell'accesso e dell'identità, quali il controllo degli accessi in base al ruolo (RBAC) o il controllo degli accessi in base all'attributo (ABAC). Lo sviluppo mediante integrazione e regole tende a essere specializzato e costoso. Il monitoraggio o la creazione di report in entrambi gli approcci di gestione è di per sé un investimento isolato, complesso e costoso.

## <a name="how-does-azure-active-directory-help"></a>Quale contributo può offrire Azure Active Directory?
 Azure AD supporta la gestione estesa degli accessi per le applicazioni configurate, consentendo alle organizzazioni di ottenere facilmente gli opportuni criteri di accesso: dall'assegnazione automatica o in base agli attributi (scenari ABAC o RBAC), alla delega, inclusa la gestione degli amministratori. Con Azure AD si possono facilmente ottenere criteri complessi, combinando più modelli di gestione per una singola applicazione, e addirittura riusare regole di gestione nelle applicazioni con gli stessi destinatari.

* [Aggiunta di applicazioni nuove o esistenti](configure-single-sign-on-portal.md)

  L’assegnazione dell’applicazione di Azure AD riguarda due modalità di assegnazione primaria:

* **Singola assegnazione** : un amministratore IT con autorizzazioni di amministratore globale di directory può selezionare singoli account utente e concedere loro l'accesso all'applicazione.
* **Assegnazione basata su gruppo (con solo il pagamento di Azure AD)** : un amministratore IT con autorizzazioni di amministratore globale di directory può assegnare un gruppo all'applicazione. L'accesso utente specifico è determinato dall'appartenenza al gruppo dell'utente nel momento in cui tenta di accedere all'applicazione. In altri termini, un amministratore può in pratica creare una regola di assegnazione che indica che tutti i membri del gruppo assegnato hanno accesso all'applicazione. Con questa opzione di assegnazione, gli amministratori possono sfruttare le opzioni di gestione dei gruppi di Azure AD, tra cui [gruppi dinamici basati sugli attributi](../fundamentals/active-directory-groups-create-azure-portal.md), gruppi di sistema esterno (ad esempio, Active Directory locale o Workday), gruppi gestiti dall'amministratore o in modalità self-service. Un singolo gruppo può essere facilmente assegnato a più app, garantendo che le applicazioni con affinità di assegnazione condividano le regole di assegnazione, riducendo la complessità generale della gestione. Si noti che al momento le appartenenze ai gruppi annidati non sono supportate per l'assegnazione alle applicazioni in base al gruppo.

Mediante queste due modalità di assegnazione, gli amministratori possono ottenere qualsiasi approccio di gestione delle assegnazioni.

Con Azure AD, le funzionalità di utilizzo e creazione di report di assegnazione sono completamente integrate e consentono agli amministratori di creare facilmente report su stato dell'assegnazione, errori di assegnazione e utilizzo.

## <a name="complex-application-assignment-with-azure-ad"></a>Assegnazione di applicazioni complesse con Azure AD
Si tenga in considerazione un'applicazione come Salesforce. In molte organizzazioni, Salesforce viene principalmente usata dai team di vendita e marketing. Spesso, i membri del team di marketing dispongono di privilegi elevati per l'accesso a Salesforce, mentre i membri del team di vendita ha accesso limitato. In molti casi numerosi information worker hanno accesso limitato all'applicazione ed eventuali eccezioni a tale regola rendono la questione più complessa. Spesso è prerogativa dei team responsabili del marketing o delle vendite concedere a un utente l'accesso o modificare i ruoli indipendentemente da queste regole generiche.

Con Azure AD, applicazioni come Salesforce possono essere preconfigurate per l'accesso Single Sign-On e il provisioning automatizzato. Dopo aver configurato l'applicazione, un amministratore può intraprendere l'azione singola di creazione e assegnazione ai gruppi appropriati. In questo esempio un amministratore può eseguire le assegnazioni seguenti:

* [gruppi dinamici](../fundamentals/active-directory-groups-create-azure-portal.md) possono essere definiti in modo da rappresentare automaticamente tutti i membri dei team di marketing e vendita mediante attributi quali reparto o ruolo:
  
  * In Salesforce, tutti i membri dei gruppi di marketing verrebbero assegnati al ruolo "marketing".
  * In Salesforce, tutti i membri dei gruppi di vendita verrebbero assegnati al ruolo "sales". Un'ulteriore perfezionamento potrebbe prevedere l'utilizzo di più gruppi che rappresentino i team di vendita suddivisi per area assegnati a diversi ruoli in Salesforce.
* Per abilitare il meccanismo delle eccezioni, è possibile creare un gruppo self-service per ogni ruolo. Ad esempio, è possibile creare come gruppo self-service il gruppo "Eccezione marketing Salesforce". Il gruppo può essere assegnato al ruolo marketing in Salesforce e il team responsabile del marketing può essere definito come proprietario. I membri del team responsabile del marketing potrebbero aggiungere o rimuovere utenti, impostare criteri di join o addirittura approvare o negare le richieste di join dei singoli utenti. Questo meccanismo è supportato attraverso un'appropriata esperienza di un information worker che non richiede una formazione specifica per proprietari o membri.

In questo caso, per tutti gli utenti assegnati viene eseguito il provisioning automatico in Salesforce e, mentre vengono aggiunti a gruppi diversi, in Salesforce viene aggiornata la relativa assegnazione al ruolo. Gli utenti potranno individuare e accedere a Salesforce tramite il pannello di accesso dell'applicazione Microsoft, i client Web di Office, o anche passando alla pagina di accesso aziendale di Salesforce. Gli amministratori potranno visualizzare facilmente lo stato di assegnazione e utilizzo mediante la funzionalità di creazione di report di Azure AD.

Gli amministratori possono utilizzare [accesso condizionale di Azure AD](../active-directory-conditional-access-azure-portal.md) per impostare i criteri di accesso per ruoli specifici. Tali criteri possono includere la possibilità di accedere all'esterno dell'ambiente aziendale e anche i requisiti di dispositivo o autenticazione a più fattori per ottenere l'accesso in più situazioni.

## <a name="next-steps"></a>Passaggi successivi
* [Protezione delle App con accesso condizionale](../active-directory-conditional-access-azure-portal.md)
* [Gestione di gruppi self-service/SSAA](../users-groups-roles/groups-self-service-management.md)
