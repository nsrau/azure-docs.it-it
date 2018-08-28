---
title: Delegare i ruoli di amministratore in Azure Active Directory | Microsoft Docs
description: Modelli di delega, esempi e sicurezza dei ruoli in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/09/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: ad772a2e0c036c30aca2918496ac01f31157fc64
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2018
ms.locfileid: "40209202"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Delegare i ruoli di amministratore in Azure Active Directory

La crescita di un'organizzazione ha per effetto un aumento della complessità a cui si risponde in genere riducendo parte del sovraccarico di gestione degli accessi con i ruoli di amministratore di Azure Active Directory (AD). È possibile assegnare agli utenti i minimi privilegi possibili per accedere alle rispettive app ed eseguire le attività correlate. Si può ritenere inopportuno assegnare il ruolo di amministratore globale a ogni proprietario di applicazione, ma il compromesso può essere quello di imporre le responsabilità di gestione delle applicazioni agli amministratori globali esistenti. Esistono molti motivi per cui un'organizzazione può scegliere di passare a un'amministrazione più decentralizzata. Questo articolo contiene informazioni che possono essere utili per delegare ruoli all'interno di un'organizzazione.

<!--What about reporting? Who has which role and how do I audit?-->


## <a name="centralized-versus-delegated-permissions"></a>Differenza tra autorizzazioni centralizzate e delegate

Con la progressiva crescita di un'organizzazione, può essere difficile tenere traccia degli utenti con specifici ruoli di amministratore. Un'organizzazione può essere vulnerabile a violazioni della sicurezza se un dipendente dispone di diritti di amministratore che non dovrebbe avere. In genere, il numero di amministratori e la granularità delle relative autorizzazioni varia a seconda delle dimensioni e dalla complessità della distribuzione.

* Nelle distribuzioni di piccole dimensioni o implementate come modello di verifica, tutte le operazioni vengono eseguite da un solo amministratore o da un numero limitato di amministratori. Non è necessaria alcuna delega. In questo caso, creare ogni amministratore con il ruolo di amministratore globale.
* Nelle distribuzioni più grandi con più computer, applicazioni e desktop, è necessario un maggiore grado di delega. Più amministratori possono avere responsabilità funzionali (ruoli) più specifiche. Ad esempio, alcuni possono amministrare identità con privilegi, mentre altri possono amministrare applicazioni. Inoltre, un amministratore può gestire solo determinati gruppi di oggetti, ad esempio i dispositivi.
* Le distribuzioni di dimensioni ancora maggiori possono richiedere autorizzazioni ancora più granulari e possibilmente anche amministratori con ruoli non convenzionali o ibridi.

Nel portale di Azure AD è possibile [visualizzare tutti i membri di qualsiasi ruolo](directory-manage-roles-portal.md), in modo da esaminare rapidamente la distribuzione e delegare le autorizzazioni.

Se si è interessati a delegare l'accesso alle risorse di Azure e non l'accesso amministrativo in Azure AD, vedere [Assegnare un ruolo di controllo degli accessi in base al ruolo](../../role-based-access-control/role-assignments-portal.md).

## <a name="delegation-planning"></a>Pianificazione della delega

Non è facile sviluppare un modello di delega in grado di soddisfare le esigenze specifiche di un'organizzazione, ma a tale scopo è possibile fare riferimento a modelli molto semplici, applicabili con lievi modifiche. Lo sviluppo di un modello di delega è un processo di progettazione iterativo per cui è consigliabile seguire questi passaggi:

* Definire i ruoli necessari
* Delegare l'amministrazione di app
* Concedere il diritto di registrare le applicazioni
* Delegare la proprietà delle app
* Sviluppare un piano di sicurezza
* Definire account di emergenza
* Proteggere i ruoli di amministratore
* Rendere temporanea l'elevazione dei privilegi

## <a name="define-roles"></a>Definire i ruoli

Determinare quali attività di Active Directory vengono eseguite dagli amministratori e come tali attività sono associate ai ruoli. Ad esempio, la creazione del sito di Active Directory è un'attività di amministrazione del servizio, mentre la modifica dell'appartenenza al gruppo di sicurezza rientra in genere nelle attività di amministrazione dei dati. È possibile [visualizzare le descrizioni dettagliate dei ruoli](directory-manage-roles-portal.md) nel portale di Azure.

Ogni attività deve essere valutata in termini di frequenza, importanza e difficoltà. Questi sono aspetti fondamentali della definizione delle attività perché determinano se un'autorizzazione deve essere delegata. Le attività svolte con frequenza regolare, con rischi limitati e semplici da eseguire sono particolarmente adatte a essere delegate. Al contrario, le attività che vengono eseguite raramente, ma hanno un notevole impatto su tutta l'organizzazione e richiedono competenze di alto livello, devono essere valutate molto attentamente prima di un'eventuale delega. In alternativa, è possibile [elevare temporaneamente un account al ruolo richiesto](../active-directory-privileged-identity-management-configure.md) o riassegnare l'attività.

## <a name="delegate-app-administration"></a>Delegare l'amministrazione di app

L'ampia diffusione di app all'interno di un'organizzazione può mettere a dura prova il modello di delega. Se comporta un maggiore impegno di gestione dell'accesso alle applicazioni per l'amministratore globale, è probabile che con il passare del tempo il modello sia soggetto a un crescente sovraccarico. Se si è concesso agli utenti il ruolo di amministratore globale per attività come la configurazione delle applicazioni aziendali, è ora possibile assegnare tali attività ai ruoli di livello inferiore, con minori privilegi. Ciò consente di migliorare il livello generale di sicurezza e ridurre il rischio di spiacevoli errori. I ruoli di amministratore di applicazioni con maggiori privilegi sono:

* Il ruolo **Amministratore di applicazioni**, che consente di eseguire le attività di gestione di tutte le applicazioni nella directory, incluse le registrazioni, le impostazioni dell'accesso Single Sign-On, le concessioni di licenze e le assegnazioni a utenti e gruppi, le impostazioni di Application Proxy e il consenso. Non consente tuttavia di gestire l'accesso condizionale.
* Il ruolo **Amministratore applicazione cloud**, che concede tutti i diritti del ruolo Amministratore di applicazioni tranne l'accesso alle impostazioni di Application Proxy poiché non dispone di autorizzazioni locali. ### Delegare le autorizzazioni di proprietario di app per le singole app

## <a name="delegate-app-registration"></a>Delegare la registrazione di app

Per impostazione predefinita, tutti gli utenti possono creare registrazioni di applicazioni. Se si vuole concedere in modo selettivo il diritto di creare registrazioni delle applicazioni, è necessario impostare **Gli utenti possono registrare applicazioni** su No in Impostazioni utente e quindi assegnare il ruolo Sviluppatore di applicazioni. Questo ruolo consente di creare registrazioni di applicazioni solo quando l'opzione **Gli utenti possono registrare applicazioni** è disattivata. Gli sviluppatori di applicazioni possono inoltre fornire il consenso per se stessi quando l'opzione **Gli utenti possono fornire il consenso alle app che accedono ai dati aziendali per loro conto** è impostata su No. Quando uno sviluppatore di applicazioni crea una nuova registrazione di applicazione, viene aggiunto automaticamente come primo proprietario.

## <a name="delegate-app-ownership"></a>Delegare la proprietà delle app

Per una delega dell'accesso alle app ancora più granulare, è possibile assegnare la proprietà di singole applicazioni aziendali. Questa delega si integra al supporto esistente per l'assegnazione dei proprietari delle registrazioni di applicazioni. La proprietà viene assegnata a livello di singola applicazione aziendale nel pannello delle app aziendali. Il vantaggio è dato dal fatto che gli utenti possono gestire solo le applicazioni aziendali di cui sono proprietari. Se ad esempio si assegna un proprietario per l'applicazione Salesforce, tale proprietario può gestire l'accesso e la configurazione per Salesforce e non per altre applicazioni. Un'applicazione aziendale può avere molti proprietari e un utente può essere proprietario per molte applicazioni aziendali. Sono disponibili due ruoli di proprietario di app:

* Il ruolo **Proprietario di applicazioni aziendali** consente di gestire le applicazioni aziendali di cui si è proprietari, tra cui le impostazioni dell'accesso Single Sign-on, le assegnazioni di utenti e gruppi e l'aggiunta di altri proprietari. Non consente tuttavia di gestire le impostazioni di Application Proxy o l'accesso condizionale.
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

Per la maggior parte delle attività quotidiane, non tutti gli utenti devono disporre dei diritti di amministratore globale. Inoltre, non tutti gli utenti devono essere assegnati in modo permanente al ruolo di amministratore globale. Quando gli utenti devono operare come amministratori globali, devono attivare l'assegnazione del ruolo in Azure AD [Privileged Identity Management](../active-directory-privileged-identity-management-configure.md) per il proprio account o per un account di amministratore alternativo.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni di riferimento sui ruoli di Azure AD, vedere [Assegnazione dei ruoli di amministratore in Azure AD](directory-assign-admin-roles.md)