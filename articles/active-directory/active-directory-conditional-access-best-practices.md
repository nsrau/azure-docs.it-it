---
title: Procedure consigliate per l'accesso condizionale in Azure Active Directory | Microsoft Docs
description: "Informazioni sugli aspetti da conoscere e su ciò che è consigliabile evitare quando si configurano i criteri di accesso condizionale."
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/22/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 61fd58063063d69e891d294e627ae40cb878d65b
ms.openlocfilehash: 9cecbfd1fd5db8fffc9fbdfb2d6ca949b6ff385a
ms.contentlocale: it-it
ms.lasthandoff: 06/22/2017


---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Procedure consigliate per l'accesso condizionale in Azure Active Directory

Questo argomento fornisce informazioni sugli aspetti da conoscere e su ciò che è consigliabile evitare quando si configurano i criteri di accesso condizionale. Prima di procedere nella lettura, occorre acquisire familiarità con i concetti e la terminologia descritti in [Accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md)

## <a name="what-you-should-know"></a>Informazioni utili

### <a name="do-i-need-to-assign-a-user-to-my-policy"></a>È necessario assegnare un utente al criterio?

Quando si configura un criterio di accesso condizionale, è consigliabile assegnargli almeno un gruppo. Un criterio di accesso condizionale a cui non sono assegnati utenti e gruppi non viene mai attivato.

Quando si vogliono assegnare più utenti e gruppi a un criterio, è consigliabile iniziare assegnando un solo utente o gruppo e quindi testare la configurazione. Se il criterio funziona come previsto, sarà quindi possibile aggiungergli altre assegnazioni.  


### <a name="how-are-assignments-evaluated"></a>Come vengono valutate le assegnazioni?

Tutte le assegnazioni vengono collegate logicamente con l'operatore **AND**. Se è configurata più di un'assegnazione, per attivare un criterio, devono essere soddisfatte tutte le assegnazioni.  

Se è necessario configurare una condizione della località che si applica a tutte le connessioni stabilite dall'esterno della rete dell'organizzazione, è possibile:

- Includere **Tutte le località**
- Escludere **Tutti gli indirizzi IP attendibili**

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Che cosa accade se sono configurati criteri nel portale di Azure classico e nel portale di Azure?  
Entrambi i criteri vengono applicati da Azure Active Directory e l'utente ottiene l'accesso solo quando tutti i requisiti vengono soddisfatti.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Che cosa accade se sono presenti criteri nel portale di Intune Silverlight e nel portale di Azure?
Entrambi i criteri vengono applicati da Azure Active Directory e l'utente ottiene l'accesso solo quando tutti i requisiti vengono soddisfatti.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Che cosa accade se sono configurati più criteri per lo stesso utente?  
Per ogni accesso, Azure Active Directory valuta tutti i criteri e verifica che tutti i requisiti vengano soddisfatti prima di concedere l'accesso all'utente.


### <a name="does-conditional-access-work-with-exchange-activesync"></a>L'accesso condizionale funziona con Exchange ActiveSync?

Sì, è possibile usare Exchange ActiveSync in criteri di accesso condizionale.


## <a name="what-you-should-avoid-doing"></a>Azioni da evitare

Il framework di accesso condizionale offre ottima flessibilità di configurazione. Tuttavia, questa flessibilità ideale comporta anche che è opportuno esaminare attentamente ogni criterio di configurazione prima che venga rilasciato, in modo da evitare risultati indesiderati. In questo contesto, è necessario prestare particolare attenzione alle assegnazioni che interessano set completi, ad esempio **tutti gli utenti/i gruppi/le applicazioni cloud**.

Nell'ambiente, è necessario evitare le seguenti configurazioni:


**Per tutti gli utenti e tutte le applicazioni cloud:**

- **Blocca accesso**: questa configurazione consente di bloccare l'intera organizzazione. Un'idea chiaramente non buona.

- **Richiedi un dispositivo conforme**: per gli utenti che non hanno ancora registrato i propri dispositivi, questo criterio blocca tutti gli accessi, tra cui l'accesso al portale di Intune. Se l'utente è amministratore senza un dispositivo registrato, questo criterio blocca l'accesso al portale di Azure per la modifica dei criteri.

- **Require domain join** (Richiedi aggiunta a dominio): se ancora non si dispone di un dispositivo aggiunto al dominio, questo criterio di blocco dell'accesso è anche in grado di bloccare l'accesso per tutti gli utenti nell'organizzazione.


**Per tutti gli utenti, tutte le applicazioni cloud e tutte le piattaforme per dispositivi:**

- **Blocca accesso**: questa configurazione consente di bloccare l'intera organizzazione. Un'idea chiaramente non buona.


## <a name="common-scenarios"></a>Scenari comuni

### <a name="requiring-multi-factor-authentication-for-apps"></a>Richiesta dell'autenticazione a più fattori per le app

Diversi ambienti hanno app che richiedono un livello di protezione maggiore delle altre,
ad esempio, app che hanno accesso a dati sensibili.
Per aggiungere un altro livello di protezione a queste app, è possibile configurare un criterio di accesso condizionale che richiede l'autenticazione a più fattori quando gli utenti accedono a queste app.


### <a name="requiring-multi-factor-authentication-for-access-from-networks-that-are-not-trusted"></a>Richiesta dell'autenticazione a più fattori per l'accesso da reti non considerate attendibili

Questo scenario è simile a quello precedente perché aggiunge un requisito per l'autenticazione a più fattori.
La differenza principale è tuttavia la condizione per questo requisito.  
Mentre l'elemento essenziale dello scenario precedente sono le app con accesso a dati sensibili, in questo scenario l'elemento essenziale sono le località attendibili.  
In altre parole, è possibile avere un requisito per l'autenticazione a più fattori se un'app è accessibile a un utente da una rete non considerata attendibile.


### <a name="only-trusted-devices-can-access-office-365-services"></a>Solo i dispositivi attendibili possono accedere ai servizi di Office 365

Se si usa Intune nell'ambiente, è possibile iniziare immediatamente a usare l'interfaccia del criterio di accesso condizionale nella console Azure.

Molti clienti Intune usano l'accesso condizionale per assicurarsi che solo i dispositivi attendibili possano accedere a Office 365. Di conseguenza i dispositivi mobili vengono registrati con Intune e soddisfano i requisiti dei criteri di conformità e i PC Windows vengono aggiunti e un dominio locale. Un importante miglioramento consiste nel fatto che non è necessario impostare lo stesso criterio per ogni servizio di Office 365.  Quando si crea un nuovo criterio, configurare le app per cloud per includere ogni app di O365 che si vuole proteggere con l'accesso condizionale.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare un criterio di accesso condizionale, vedere [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introduzione all'accesso condizionale in Azure Active Directory).

