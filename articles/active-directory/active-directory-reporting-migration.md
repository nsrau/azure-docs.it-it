---
title: "Trovare i report attività nel portale di Azure | Microsoft Docs"
description: "Informazioni su come trovare i report attività di Azure Active Directory nel portale di Azure."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: d93521f8-dc21-4feb-aaff-4bb300f04812
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/21/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 6f1485ad57eb28841ef5fe585e8cf1b8eb29e4a7
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2017
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Trovare i report attività nel portale di Azure

Se è in corso la migrazione dal portale di Azure classico al portale di Azure, l'aspetto dei log attività di Azure Active Directory (Azure AD) cambierà. In un recente [post del blog](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/) viene illustrato come visualizzare i log attività nel contesto della risorsa su cui si sta lavorando nel portale di Azure. Questo articolo descrive come trovare nel portale di Azure i report usati nel portale di Azure classico.

## <a name="whats-new"></a>Novità

I report nel portale di Azure classico sono suddivisi in categorie:

1.  Report sulla sicurezza
2.  Report sull’attività
3.  Report app integrate

### <a name="activity-and-integrated-app-reports"></a>Report attività e app integrate

Per i report basati sul contesto nel portale di Azure, i report esistenti vengono uniti in una singola visualizzazione. Una singola API sottostante fornisce i dati alla visualizzazione.

Per accedere a questa visualizzazione, nel pannello **Azure Active Directory**, in **ATTIVITÀ** selezionare **Log di controllo**.

![Log di controllo](./media/active-directory-reporting-migration/482.png "Log di controllo")

In questa visualizzazione sono consolidati i report seguenti:

-   Report di controllo
-   Attività di reimpostazione password
-   Attività di registrazione reimpostazione password
-   Attività dei gruppi self-service
-   Modifiche del nome del gruppo di Office 365
-   Attività di provisioning dell'account
-   Stato rollover della password
-   Errori di provisioning dell'account


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

I report di sicurezza sulle attività anomale di Azure AD del portale di Azure classico sono stati consolidati per fornire una singola visualizzazione centralizzata. Questa visualizzazione mostra tutti gli eventi di rischio correlati alla sicurezza che Azure AD è in grado di rilevare e segnalare.

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

Questi report sono ancora disponibili nel portale di Azure classico, ma in futuro saranno deprecati.

Per altre informazioni, vedere [Eventi di rischio di Azure Active Directory](active-directory-identity-protection-risk-events.md).  


#### <a name="detected-risk-events"></a>Eventi di rischio rilevati

Nel portale di Azure è possibile accedere ai report sugli eventi di rischio rilevati nella sezione **SICUREZZA** del pannello **Azure Active Directory**. Gli eventi di rischio rilevati vengono registrati nei report seguenti:   

- Utenti a rischio
- Accessi a rischio

![Report di sicurezza](./media/active-directory-reporting-migration/04.png "Report di sicurezza")

Per altre informazioni sui report di sicurezza, vedere:

- [Report di sicurezza per gli utenti a rischio nel portale di Azure Active Directory](active-directory-reporting-security-user-at-risk.md)
- [Report degli accessi a rischio nel portale di Azure Active Directory](active-directory-reporting-security-risky-sign-ins.md)


## <a name="activity-reports-in-the-azure-classic-portal-vs-the-azure-portal"></a>Report sulle attività nel portale di Azure classico rispetto al portale di Azure

La tabella in questa sezione elenca i report esistenti nel portale di Azure classico. La tabella descrive anche come ottenere le stesse informazioni nel portale di Azure.

Per visualizzare tutti i dati di controllo, nel pannello **Azure Active Directory**, in **ATTIVITÀ** andare a **Log di controllo**.

![Log di controllo](./media/active-directory-reporting-migration/61.png "Log di controllo")

| portale di Azure classico                 | Nel portale di Azure:                                                         |
| ---                                  | ---                                                                        |
| Log di controllo                           | Per **Categoria attività** selezionare **Core directory** (Directory principale).                       |
| Attività di reimpostazione password              | Per **Categoria attività** selezionare **Self Service Password Management** (Gestione delle password self-service). |
| Attività di registrazione reimpostazione password | Per **Categoria attività** selezionare **Self Service Password Management** (Gestione delle password self-service).     |
| Attività dei gruppi self-service         | Per **Categoria attività** selezionare **Gestione gruppi self-service**.        |
| Attività di provisioning dell'account        | Per **Categoria attività** selezionare **Account User Provisioning** (Provisioning dell'utente account).         |
| Stato rollover della password             | Per **Categoria attività** selezionare **Automatic App Password Rollover** (Rollover password app automatico).      |
| Errori di provisioning dell'account          | Per **Categoria attività** selezionare **Account User Provisioning** (Provisioning dell'utente account).        |
| Modifiche del nome del gruppo di Office 365         | Per **Categoria attività** selezionare **Self Service Password Management** (Gestione delle password self-service). Per **Activity Resource Type** (Tipo di risorsa attività) selezionare **Gruppo**. Per **Origine attività** selezionare **O365 groups** (Gruppi di O365).|

Per visualizzare il report **Utilizzo applicazioni**, nel pannello **Azure Active Directory**, in **GESTISCI** selezionare **Applicazioni aziendali** e scegliere **Accessi**.


![Report degli accessi alle applicazioni aziendali](./media/active-directory-reporting-migration/199.png "Report degli accessi alle applicazioni aziendali")

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica della creazione di report, vedere [Creazione di report in Azure Active Directory](active-directory-reporting-azure-portal.md).
