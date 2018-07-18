---
title: Trovare i report attività utente di Azure Active Directory nel portale di Azure | Microsoft Docs
description: Informazioni su dove trovare i report attività utente di Azure Active Directory nel portale di Azure.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: users-groups-roles
ms.date: 12/06/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: f22219a0e2ff342e25a2efdeb319f389250ecfef
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231322"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Trovare i report attività nel portale di Azure

In questo articolo viene descritto come trovare i report attività utente di Azure Active Directory nel portale di Azure.

## <a name="activity-and-integrated-app-reports"></a>Report attività e app integrate

Per i report basati sul contesto nel portale di Azure, i report esistenti vengono uniti in una singola visualizzazione. Una singola API sottostante fornisce i dati alla visualizzazione.

Per accedere a questa visualizzazione, nel pannello **Azure Active Directory**, in **ATTIVITÀ** selezionare **Log di controllo**.

![Log di controllo](./media/active-directory-reporting-migration/482.png "Log di controllo")

In questa visualizzazione sono consolidati i report seguenti:

* Report di controllo
* Attività di reimpostazione password
* Attività di registrazione reimpostazione password
* Attività dei gruppi self-service
* Modifiche del nome del gruppo di Office 365
* Attività di provisioning dell'account
* Stato rollover della password
* Errori di provisioning dell'account


Il report Utilizzo delle applicazioni è stato migliorato e incluso nella visualizzazione **Accessi**. Per accedere a questa visualizzazione, nel pannello **Azure Active Directory**, in **ATTIVITÀ** selezionare **Accessi**.

![Visualizzazione Accessi](./media/active-directory-reporting-migration/483.png "Visualizzazione Accessi")

La visualizzazione **Accessi** include tutti gli accessi dell'utente. È possibile usare queste informazioni per ottenere informazioni sull'utilizzo dell'applicazione. È anche possibile visualizzare le informazioni sull'utilizzo dell'applicazione nella panoramica **Applicazioni aziendali**, nella sezione **GESTISCI**.

![Applicazioni aziendali](./media/active-directory-reporting-migration/484.png "Applicazioni aziendali")

## <a name="access-a-specific-report"></a>Accedere a un report specifico

Sebbene il portale di Azure offra una visualizzazione singola, è possibile anche esaminare report specifici.

### <a name="audit-logs"></a>Log di controllo

In risposta ai commenti dei clienti, nel portale di Azure è possibile usare filtri avanzati per accedere ai dati. Uno dei filtri che è possibile usare riguarda la *categoria di attività*, che elenca i diversi tipi di log attività disponibili in Azure AD. Per restringere i risultati della ricerca, è possibile selezionare una categoria.

Ad esempio, se si è interessati solo alle attività relative alla reimpostazione password self-service, è possibile scegliere la categoria **Self-service Password Management** (Gestione delle password self-service). Le categorie visualizzate sono basate sulla risorsa in cui si sta lavorando.  

![Opzioni Categoria nella pagina Filtra log di controllo](./media/active-directory-reporting-migration/06.png "Opzioni Categoria nella pagina Filtra log di controllo")

Le categorie delle attività includono:

- Directory principale
- Gestione delle password self-service
- Gestione gruppi self-service
- Provisioning degli account

### <a name="application-usage"></a>Utilizzo applicazioni

Per visualizzare informazioni dettagliate sull'utilizzo dell'applicazione per tutte le applicazioni o per una singola applicazione, in **ATTIVITÀ** selezionare **Accessi**. Per restringere i risultati, è possibile filtrare in base a nome utente o nome applicazione.

![Pagina Filtra eventi di accesso](./media/active-directory-reporting-migration/07.png "Pagina Filtra eventi di accesso")

### <a name="security-reports"></a>Report sulla sicurezza

#### <a name="azure-ad-anomalous-activity-reports"></a>Report di Anomalie dell'attività di Azure AD

I report di sicurezza sulle attività anomale di Azure AD sono stati consolidati per fornire una singola visualizzazione centralizzata. Questa visualizzazione mostra tutti gli eventi di rischio correlati alla sicurezza che Azure AD è in grado di rilevare e segnalare.

La tabella seguente elenca i report di sicurezza sulle attività anomale di Azure AD e le corrispondenti tipologie degli eventi di rischio nel portale di Azure.

| Report di Anomalie dell'attività di Azure AD |  Tipo di evento di rischio di Identity Protection|
| :--- | :--- |
| Utenti con credenziali perse | Credenziali perse |
| Attività di accesso irregolare | Trasferimento impossibile a posizioni atipiche |
| Accessi da dispositivi potenzialmente infetti | Accessi da dispositivi infetti|
| Accessi da origini sconosciute | Accessi da indirizzi IP anonimi |
| Accessi da indirizzi IP con attività sospette | Accessi da indirizzi IP con attività sospette |
| - | Accessi da posizioni non note |

I report di sicurezza sulle attività anomale di Azure AD seguenti non sono inclusi come eventi di rischio nel portale di Azure:

* Accessi dopo più errori
* Accessi da più aree geografiche

Per altre informazioni, vedere [Eventi di rischio di Azure Active Directory](active-directory-identity-protection-risk-events.md).  


#### <a name="detected-risk-events"></a>Eventi di rischio rilevati

Nel portale di Azure è possibile accedere ai report sugli eventi di rischio rilevati nella sezione **SICUREZZA** del pannello **Azure Active Directory**. Gli eventi di rischio rilevati vengono registrati nei report seguenti:   

- Utenti a rischio
- Accessi a rischio

![Report di sicurezza](./media/active-directory-reporting-migration/04.png "Report di sicurezza")

Per altre informazioni sui report di sicurezza, vedere:

- [Report di sicurezza per gli utenti a rischio nel portale di Azure Active Directory](active-directory-reporting-security-user-at-risk.md)
- [Report degli accessi a rischio nel portale di Azure Active Directory](active-directory-reporting-security-risky-sign-ins.md)


Per visualizzare il report **Utilizzo applicazioni**, nel pannello **Azure Active Directory**, in **GESTISCI** selezionare **Applicazioni aziendali** e scegliere **Accessi**.


![Report degli accessi alle applicazioni aziendali](./media/active-directory-reporting-migration/199.png)

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica della creazione di report, vedere [Creazione di report in Azure Active Directory](active-directory-reporting-azure-portal.md).
