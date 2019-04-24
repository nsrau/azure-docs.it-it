---
title: Trovare i report attività utente di Azure Active Directory nel portale di Azure | Microsoft Docs
description: Informazioni su dove trovare i report attività utente di Azure Active Directory nel portale di Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d47072713c57576abe780134792c3a5cbc27127c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60438327"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Trovare i report attività nel portale di Azure

In questo articolo viene descritto come trovare i report attività utente di Azure Active Directory (Azure AD) nel portale di Azure.

## <a name="audit-logs-report"></a>Report dei log di controllo

Il report dei log di controllo combina diversi report relativi alle attività dell'applicazione in una singola vista per la creazione di report basati sul contesto. Per accedere al report dei log di controllo:

1. Passare al [portale di Azure](https://portal.azure.com).
2. Selezionare la directory nell'angolo in alto a destra, quindi selezionare il pannello **Azure Active Directory** nel riquadro di spostamento a sinistra.
3. Selezionare **Log di controllo** nella sezione **Attività** del pannello Azure Active Directory. 

    ![Log di controllo](./media/howto-find-activity-reports/482.png "Log di controllo")

Il report dei log di controllo consente di consolidare i report seguenti:

* Report di controllo
* Attività di reimpostazione password
* Attività di registrazione reimpostazione password
* Attività dei gruppi self-service
* Modifiche del nome del gruppo di Office 365
* Attività di provisioning dell'account
* Stato rollover della password
* Errori di provisioning dell'account

### <a name="filtering-on-audit-logs"></a>Filtro dei log di controllo

È possibile usare filtri avanzati nel report di controllo per accedere a una categoria di dati di controllo, impostandolo nel **categoria** filtro. Ad esempio, per visualizzare tutte le attività relative agli utenti, selezionare la **Gestione utente** categoria. 

Le categorie includono:

- Tutti
- AdministrativeUnit
- ApplicationManagement
- Authentication
- Authorization
- Contatto
- Dispositivo
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- Altri
- Policy
- Gestione
- RoleManagement
- Gestione utente

È anche possibile filtrare in un servizio specifico tramite il **servizio** filtro elenco a discesa. Ad esempio, per ottenere tutti gli eventi di controllo correlati alla gestione delle password self-service, selezionare la **Self-Service Password Management** filtro.

I servizi includono:

- Tutti
- Verifiche di accesso
- Provisioning degli account 
- Applicazione SSO
- Metodi di autenticazione
- B2C
- Accesso condizionale
- Directory principale
- Gestione entitlement
- Identity Protection
- Utenti invitati
- PIM
- Gestione gruppi self-service
- Gestione delle password self-service
- Condizioni per l'utilizzo

## <a name="sign-ins-report"></a>Report sugli accessi 

Nella vista **Accessi** sono inclusi tutti gli accessi utente, nonché il report **Utilizzo delle applicazioni**. È anche possibile visualizzare le informazioni sull'uso delle applicazioni nella sezione **Gestisci** della pagina di panoramica **Applicazioni aziendali**.

Per accedere al report degli accessi:

1. Passare al [portale di Azure](https://portal.azure.com).
2. Selezionare la directory nell'angolo in alto a destra, quindi selezionare il pannello **Azure Active Directory** nel riquadro di spostamento a sinistra.
3. Selezionare **Accessi** nella sezione **Attività** del pannello Azure Active Directory. 

    ![Visualizzazione Accessi](./media/howto-find-activity-reports/483.png "Visualizzazione Accessi")


### <a name="filtering-on-application-name"></a>Filtro in base al nome dell'applicazione

È possibile usare i report degli accessi per visualizzare i dettagli sull'uso delle applicazioni, filtrando in base al nome utente o al nome dell'applicazione.

![Pagina Filtra eventi di accesso](./media/howto-find-activity-reports/07.png "Pagina Filtra eventi di accesso")

## <a name="security-reports"></a>Report sulla sicurezza

### <a name="anomalous-activity-reports"></a>Report Anomalie dell'attività

I report Anomalie dell'attività forniscono informazioni sugli eventi di rischio correlati alla sicurezza che Azure AD può rilevare e segnalare.

La tabella seguente elenca i report di sicurezza sulle attività anomale di Azure AD e le corrispondenti tipologie degli eventi di rischio nel portale di Azure. Per altre informazioni, vedere [Eventi di rischio di Azure Active Directory](concept-risk-events.md).  


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


### <a name="detected-risk-events"></a>Eventi di rischio rilevati

È possibile accedere ai report sugli eventi di rischio rilevati nella sezione **Sicurezza** del pannello **Azure Active Directory** nel [portale di Azure](https://portal.azure.com). Gli eventi di rischio rilevati vengono registrati nei report seguenti:   

- [Utenti a rischio](concept-user-at-risk.md)
- [Accessi a rischio](concept-risky-sign-ins.md)

    ![Report di sicurezza](./media/howto-find-activity-reports/04.png "Report di sicurezza")

## <a name="troubleshoot-issues-with-activity-reports"></a>Risolvere i problemi con i report di attività

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Dati mancanti nei log attività scaricati

#### <a name="symptoms"></a>Sintomi 

I log attività (controllo o accessi) sono stati scaricati ma non vengono visualizzati tutti i record per l'orario scelto. Perché? 

 ![Creazione di report](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Causa

Quando si scaricano i log attività nel portale di Azure, è necessario limitare la scalabilità necessaria per i 250000 record, ordinate dalla più recente. 

#### <a name="resolution"></a>Risoluzione

È possibile sfruttare le [API di Creazione rapporti di Azure AD](concept-reporting-api.md) per recuperare fino a un milione di record per un momento specifico.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Dati di controllo mancanti per le azioni recenti nel portale di Azure

#### <a name="symptoms"></a>Sintomi

Sono state eseguite alcune azioni nel portale di Azure e si prevedeva la visualizzazione dei log di controllo per tali azioni nel pannello `Activity logs > Audit Logs`, ma non è possibile trovarli.

 ![Creazione di report](./media/troubleshoot-missing-audit-data/01.png)
 
#### <a name="cause"></a>Causa

Le azioni non vengono visualizzate immediatamente nei log attività. La tabella seguente elenca i numeri di latenza per i log attività. 

| Report | &nbsp; | Latenza (P95) | Latenza (P99) |
|--------|--------|---------------|---------------|
| Directory Audit (Controllo directory) | &nbsp; | 2 min | 5 min |
| Attività di accesso | &nbsp; | 2 min | 5 min | 

#### <a name="resolution"></a>Risoluzione

Attendere tra 15 minuti e due ore e verificare se le azioni vengono visualizzate nel log. Se i log non vengono visualizzati nemmeno dopo due ore, [inviare un ticket di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) che verrà preso in esame.

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Log mancanti per gli accessi utente recenti nel log attività degli accessi di Azure AD

#### <a name="symptoms"></a>Sintomi

Di recente si è eseguito l'accesso al portale di Azure e si prevedeva la visualizzazione dei log di accesso per tali azioni nel pannello `Activity logs > Sign-ins`, ma non è possibile trovarli.

 ![Creazione di report](./media/troubleshoot-missing-audit-data/02.png)
 
#### <a name="cause"></a>Causa

Le azioni non vengono visualizzate immediatamente nei log attività. La tabella seguente elenca i numeri di latenza per i log attività. 

| Report | &nbsp; | Latenza (P95) | Latenza (P99) |
|--------|--------|---------------|---------------|
| Directory Audit (Controllo directory) | &nbsp; | 2 min | 5 min |
| Attività di accesso | &nbsp; | 2 min | 5 min | 

#### <a name="resolution"></a>Risoluzione

Attendere tra 15 minuti e due ore e verificare se le azioni vengono visualizzate nel log. Se i log non vengono visualizzati nemmeno dopo due ore, [inviare un ticket di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) che verrà preso in esame.

### <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Non è possibile visualizzare più di 30 giorni di dati di report nel portale di Azure

#### <a name="symptoms"></a>Sintomi

Non è possibile visualizzare più di 30 giorni di dati di accesso e controllo nel portale di Azure. Perché? 

 ![Creazione di report](./media/troubleshoot-missing-audit-data/03.png)

#### <a name="cause"></a>Causa

In base alla licenza, Azioni di Azure Active Directory archivia i report delle attività per le durate seguenti:

| Report           | &nbsp; |  Azure AD Free | Azure AD P1 Premium | Azure AD P2 Premium |
| ---              | ----   |  ---           | ---                 | ---                 |
| Directory Audit (Controllo directory)  | &nbsp; |   7 giorni     | 30 giorni             | 30 giorni             |
| Attività di accesso | &nbsp; | Non disponibile. È possibile accedere alle informazioni di accesso per 7 giorni dal pannello del singolo profilo utente | 30 giorni | 30 giorni             |

Per altre informazioni, vedere [Criteri di conservazione dei report di Azure Active Directory](reference-reports-data-retention.md).  

#### <a name="resolution"></a>Risoluzione

Sono disponibili due opzioni per conservare i dati per più di 30 giorni. È possibile usare le [API creazione report di Azure AD](concept-reporting-api.md) per recuperare i dati a livello di codice e archiviarli in un database. In alternativa, è possibile integrare i log di controllo in un sistema SIEM di terze parti, ad esempio Splunk o SumoLogic.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica dei log di controllo](concept-audit-logs.md)
* [Panoramica degli accessi](concept-sign-ins.md)
* [Panoramica degli eventi di rischio](concept-risk-events.md)
