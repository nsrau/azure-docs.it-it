---
title: Informazioni sulla delega di ruoli di amministratore - Azure Active Directory | Microsoft Docs
description: Modelli di delega, esempi e sicurezza dei ruoli in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0cb6e2b1df062c3d056bd9a5aa0c1ff89f6636a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60469112"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Delegare i ruoli di amministratore in Azure Active Directory

Alla crescita dell'organizzazione si accompagna una maggiore complessità. Una risposta comune consiste nel ridurre parte del carico di gestione dell'accesso con ruoli di amministratore di Azure Active Directory (AD). È possibile assegnare agli utenti i minimi privilegi possibili per accedere alle rispettive app ed eseguire le attività correlate. Anche se non si assegna il ruolo di amministratore globale a qualsiasi proprietario di applicazione, le responsabilità della gestione delle applicazioni vengono delegate agli amministratori globali esistenti. Esistono molti motivi per cui un'organizzazione può scegliere di passare a un'amministrazione più decentralizzata. Questo articolo contiene informazioni che possono essere utili per delegare ruoli all'interno di un'organizzazione.

<!--What about reporting? Who has which role and how do I audit?-->

## <a name="centralized-versus-delegated-permissions"></a>Differenza tra autorizzazioni centralizzate e delegate

Con la progressiva crescita di un'organizzazione, può essere difficile tenere traccia degli utenti con specifici ruoli di amministratore. Un'organizzazione può essere vulnerabile a violazioni della sicurezza se un dipendente dispone di diritti di amministratore che non dovrebbe avere. In generale, il numero di amministratori supportati e la granularità delle autorizzazioni dipendono dalle dimensioni e dalla complessità della distribuzione.

* Nelle distribuzioni di piccole dimensioni o implementate come modello di verifica, tutte le operazioni vengono eseguite da un solo amministratore o da un numero limitato di amministratori. Non è necessaria alcuna delega. In questo caso, creare ogni amministratore con il ruolo di amministratore globale.
* Nelle distribuzioni più grandi con più computer, applicazioni e desktop, è necessario un maggiore grado di delega. Più amministratori possono avere responsabilità funzionali (ruoli) più specifiche. Ad esempio, alcuni possono amministrare identità con privilegi, mentre altri possono amministrare applicazioni. Inoltre, un amministratore può gestire solo determinati gruppi di oggetti, ad esempio i dispositivi.
* Le distribuzioni di dimensioni ancora maggiori possono richiedere autorizzazioni ancora più granulari e possibilmente anche amministratori con ruoli non convenzionali o ibridi.

Nel portale di Azure AD è possibile [visualizzare tutti i membri di qualsiasi ruolo](directory-manage-roles-portal.md), in modo da esaminare rapidamente la distribuzione e delegare le autorizzazioni.

Se si è interessati a delegare l'accesso alle risorse di Azure e non l'accesso amministrativo in Azure AD, vedere [Assegnare un ruolo di controllo degli accessi in base al ruolo](../../role-based-access-control/role-assignments-portal.md).

## <a name="delegation-planning"></a>Pianificazione della delega

Lo sviluppo di un modello di delega adatto alle proprie esigenze richiede impegno. Lo sviluppo di un modello di delega è un processo di progettazione iterativo per cui è consigliabile seguire questi passaggi:

* Definire i ruoli necessari
* Delegare l'amministrazione di app
* Concedere il diritto di registrare le applicazioni
* Delegare la proprietà delle app
* Sviluppare un piano di sicurezza
* Definire account di emergenza
* Proteggere i ruoli di amministratore
* Rendere temporanea l'elevazione dei privilegi

## <a name="define-roles"></a>Definire i ruoli

Determinare le attività di Active Directory che vengono eseguite dagli amministratori e il relativo mapping ai ruoli. È possibile [visualizzare le descrizioni dettagliate dei ruoli](directory-manage-roles-portal.md) nel portale di Azure.

Ogni attività deve essere valutata in termini di frequenza, importanza e difficoltà. Questi criteri sono aspetti fondamentali della definizione delle attività perché determinano se un'autorizzazione deve essere delegata:

* Le attività svolte con frequenza regolare, con rischi limitati e semplici da eseguire sono particolarmente adatte a essere delegate.
* Le attività che vengono eseguite raramente, ma hanno un notevole impatto su tutta l'organizzazione e richiedono competenze di alto livello, devono essere valutate molto attentamente prima di un'eventuale delega. In alternativa, è possibile [elevare temporaneamente un account al ruolo richiesto](../active-directory-privileged-identity-management-configure.md) o riassegnare l'attività.

## <a name="delegate-app-administration"></a>Delegare l'amministrazione di app

L'ampia diffusione di app all'interno di un'organizzazione può mettere a dura prova il modello di delega. Se comporta un maggiore impegno di gestione dell'accesso alle applicazioni per l'amministratore globale, è probabile che con il passare del tempo il modello sia soggetto a un crescente sovraccarico. Se si è concesso agli utenti il ruolo di amministratore globale per attività come la configurazione delle applicazioni aziendali, è ora possibile assegnare tali attività ai ruoli di livello inferiore, con minori privilegi. Ciò consente di migliorare il livello generale di sicurezza e ridurre il rischio di spiacevoli errori. I ruoli di amministratore di applicazioni con maggiori privilegi sono:

* Il ruolo **Amministratore di applicazioni**, che consente di eseguire le attività di gestione di tutte le applicazioni nella directory, incluse le registrazioni, le impostazioni dell'accesso Single Sign-On, le concessioni di licenze e le assegnazioni a utenti e gruppi, le impostazioni di Application Proxy e il consenso. Non consente tuttavia di gestire l'accesso condizionale.
* Il ruolo **Amministratore applicazione cloud**, che concede tutti i diritti del ruolo Amministratore di applicazioni tranne l'accesso alle impostazioni di proxy dell'applicazione poiché non ha autorizzazioni locali.

## <a name="delegate-app-registration"></a>Delegare la registrazione di app

Per impostazione predefinita, tutti gli utenti possono creare registrazioni di applicazioni. Per concedere in modo selettivo la possibilità di creare registrazioni per l'applicazione:

* Impostare **Gli utenti possono registrare applicazioni** su No in **Impostazioni utente**
* Assegnare l'utente al ruolo Sviluppatore applicazioni

Per concedere in modo selettivo la possibilità di fornire il consenso per consentire a un'applicazione di accedere ai dati:

* Impostare **Gli utenti possono fornire il consenso alle app che accedono ai dati aziendali per loro conto** su No in **Impostazioni utente**
* Assegnare l'utente al ruolo Sviluppatore applicazioni

Quando uno sviluppatore di applicazioni crea una nuova registrazione di applicazione, viene aggiunto automaticamente come primo proprietario.

## <a name="delegate-app-ownership"></a>Delegare la proprietà delle app

Per una delega dell'accesso alle app ancora più granulare, è possibile assegnare la proprietà di singole applicazioni aziendali. Questa delega si integra al supporto esistente per l'assegnazione dei proprietari delle registrazioni di applicazioni. La proprietà viene assegnata a livello di singola applicazione aziendale nel pannello Applicazioni aziendali. Il vantaggio è dato dal fatto che gli utenti possono gestire solo le applicazioni aziendali di cui sono proprietari. Se ad esempio si assegna un proprietario per l'applicazione Salesforce, tale proprietario può gestire l'accesso e la configurazione per Salesforce e non per altre applicazioni. Un'applicazione aziendale può avere molti proprietari e un utente può essere proprietario per molte applicazioni aziendali. Sono disponibili due ruoli di proprietario di app:

* Il ruolo **Proprietario di applicazioni aziendali** consente di gestire le applicazioni aziendali di cui si è proprietari, tra cui le impostazioni dell'accesso Single Sign-on, le assegnazioni di utenti e gruppi e l'aggiunta di altri proprietari. Non consente tuttavia di gestire le impostazioni di proxy dell'applicazione o l'accesso condizionale.
* Il ruolo **Proprietario delle registrazioni dell'applicazione** consente di gestire le registrazioni per l'app di cui si è proprietari, tra cui il manifesto dell'applicazione e l'aggiunta di altri proprietari.

## <a name="develop-a-security-plan"></a>Sviluppare un piano di sicurezza

Nella documentazione di Azure AD è disponibile una guida per la pianificazione e l'esecuzione di un piano di sicurezza relativo ai ruoli di amministratore di Azure AD: [Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD](directory-admin-roles-secure.md).

## <a name="establish-emergency-accounts"></a>Definire account di emergenza

Per mantenere l'accesso all'archivio di gestione delle identità quando si verifica un problema, preparare gli account di accesso di emergenza in base alle indicazioni riportate in [Gestire gli account amministrativi di accesso di emergenza](directory-emergency-access.md).

## <a name="secure-your-administrator-roles"></a>Proteggere i ruoli di amministratore

Gli utenti malintenzionati che ottengono il controllo degli account con privilegi possono causare gravi danni. È quindi necessario dare priorità alla protezione di questi account usando i [criteri di accesso di base](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) che per impostazione predefinita sono disponibili per tutti i tenant di Azure AD (in anteprima pubblica). Questi criteri impongono l'autenticazione a più fattori agli account di Azure AD con privilegi. Di seguito sono elencati i ruoli di Azure AD a cui vengono applicati i criteri di base di Azure AD:

* Amministratore globale
* Amministratore di SharePoint
* Amministratore di Exchange
* Amministratore di accesso condizionale
* Amministratore della sicurezza

## <a name="elevate-privilege-temporarily"></a>Elevare temporaneamente i privilegi

Per la maggior parte delle attività quotidiane, non tutti gli utenti necessitano di diritti di amministratore globale e non tutti devono essere assegnati in modo permanente al ruolo Amministratore globale. Quando gli utenti devono disporre delle autorizzazioni di Amministratore globale, devono attivare l'assegnazione del ruolo in Azure AD [Privileged Identity Management](../active-directory-privileged-identity-management-configure.md) per il proprio account o per un account di amministratore alternativo.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni di riferimento sui ruoli di Azure AD, vedere [Assegnazione dei ruoli di amministratore in Azure AD](directory-assign-admin-roles.md)
