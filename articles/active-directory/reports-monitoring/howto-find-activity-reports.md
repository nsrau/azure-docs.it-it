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
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: fab94088d1d54012a955b0663b078d03b13d6299
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624913"
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

È possibile usare filtri avanzati nel report di controllo per accedere a una categoria specifica di dati di controllo, impostando la modalità di filtro desiderata nel filtro **Activity category** (Categoria attività). Ad esempio, per visualizzare tutte le attività relative alla reimpostazione password self-service, selezionare la categoria **Self-service Password Management** (Gestione delle password self-service). 

    ![Category options on the Filter Audit Logs page](./media/howto-find-activity-reports/06.png "Category options on the Filter Audit Logs page")

Le categorie delle attività includono:

- Directory principale
- Gestione delle password self-service
- Gestione gruppi self-service
- Provisioning degli account


## <a name="sign-ins-report"></a>Report sugli accessi 

Nella vista **Accessi** sono inclusi tutti gli accessi utente, nonché il report **Utilizzo delle applicazioni**. È anche possibile visualizzare le informazioni sull'uso delle applicazioni nella sezione **Gestisci** della pagina di panoramica **Applicazioni aziendali**.

    ![Enterprise applications](./media/howto-find-activity-reports/484.png "Enterprise applications")

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

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica dei log di controllo](concept-audit-logs.md)
* [Panoramica degli accessi](concept-sign-ins.md)
* [Panoramica degli eventi di rischio](concept-risk-events.md)
