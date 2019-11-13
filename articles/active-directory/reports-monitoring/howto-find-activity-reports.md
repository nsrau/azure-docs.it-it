---
title: Trovare i report attività utente in portale di Azure | Microsoft Docs
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
ms.openlocfilehash: d356f8c10ae3770d9f4ade100ab0496ee58d772f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74008221"
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

È possibile utilizzare il filtro avanzato nel report di controllo per accedere a una categoria specifica di dati di controllo, specificandone il filtro di **categoria** . Ad esempio, per visualizzare tutte le attività correlate agli utenti, selezionare la categoria **UserManagement** . 

Le categorie includono:

- Tutti
- AdministrativeUnit
- ApplicationManagement
- Autenticazione
- Autorizzazione
- Contatto
- Dispositivo
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- Altre
- Criteri
- ResourceManagement
- RoleManagement
- UserManagement

È anche possibile filtrare in base a un servizio specifico usando il filtro dell'elenco a discesa del **servizio** . Ad esempio, per ottenere tutti gli eventi di controllo relativi alla gestione delle password self-service, selezionare il filtro **self-service** per la gestione delle password.

I servizi includono:

- Tutti
- Verifiche di accesso
- Provisioning degli account 
- SSO applicazione
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

    ![Visualizzazione accessi](./media/howto-find-activity-reports/483.png "Visualizzazione accessi")


### <a name="filtering-on-application-name"></a>Filtro in base al nome dell'applicazione

È possibile usare i report degli accessi per visualizzare i dettagli sull'uso delle applicazioni, filtrando in base al nome utente o al nome dell'applicazione.

![Pagina Filtra eventi di accesso](./media/howto-find-activity-reports/07.png "Pagina Filtra eventi di accesso")

## <a name="security-reports"></a>Report sulla sicurezza

### <a name="anomalous-activity-reports"></a>Report Anomalie dell'attività

I report di attività anomale forniscono informazioni sui rilevamenti dei rischi correlati alla sicurezza che Azure AD possibile rilevare e segnalare.

Nella tabella seguente sono elencati i report di sicurezza delle attività anomale Azure AD e i tipi di rilevamento dei rischi corrispondenti nell'portale di Azure. Per altre informazioni, vedere [Rilevamenti dei rischi in Azure Active Directory](concept-risk-events.md).  


| Report di Anomalie dell'attività di Azure AD |  Tipo di rilevamento del rischio di Identity Protection|
| :--- | :--- |
| Utenti con credenziali perse | Credenziali perse |
| Attività di accesso irregolare | Trasferimento impossibile a posizioni atipiche |
| Accessi da dispositivi potenzialmente infetti | Accessi da dispositivi infetti|
| Accessi da origini sconosciute | Accessi da indirizzi IP anonimi |
| Accessi da indirizzi IP con attività sospette | Accessi da indirizzi IP con attività sospette |
| - | Accessi da posizioni non note |

I report di sicurezza delle attività anomale Azure AD seguenti non sono inclusi come rilevamenti dei rischi nel portale di Azure:

* Accessi dopo più errori
* Accessi da più aree geografiche


### <a name="detected-risk-detections"></a>Rilevamento del rischio rilevato

È possibile accedere ai report sui rilevamenti dei rischi rilevati nella sezione **sicurezza** del pannello **Azure Active Directory** della [portale di Azure](https://portal.azure.com). I rilevamenti dei rischi rilevati vengono registrati nei report seguenti:   

- [Utenti a rischio](concept-user-at-risk.md)
- [Accessi a rischio](concept-risky-sign-ins.md)

    ![Report sulla sicurezza](./media/howto-find-activity-reports/04.png "Report sulla sicurezza")

## <a name="troubleshoot-issues-with-activity-reports"></a>Risolvere i problemi con i report di attività

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Dati mancanti nei log attività scaricati

#### <a name="symptoms"></a>Sintomi 

I log attività (controllo o accessi) sono stati scaricati ma non vengono visualizzati tutti i record per l'orario scelto. Perché? 

 ![Creazione di report](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Causa

Quando si scaricano i log attività nel portale di Azure, viene limitata la scalabilità a 250000 record, ordinati in base alla prima più recente. 

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
