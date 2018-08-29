---
title: Introduzione all'API di creazione report di Azure AD | Microsoft Docs
description: Come iniziare a usare l'API di creazione report di Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 7de7c87e5cf1747f4899f33d9e6b9cbf0bb430e1
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2018
ms.locfileid: "42146260"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Introduzione all'API di creazione report di Azure Active Directory

Azure Active Directory fornisce una serie di [report](overview-reports.md). I dati di questi report possono essere molto utili per le applicazioni, ad esempio i sistemi SIEM e gli strumenti di controllo e business intelligence. 

Usando l'API di creazione report di Azure AD è possibile ottenere l'accesso ai dati a livello di codice tramite un set di API basate su REST. È possibile chiamare le API da numerosi linguaggi di programmazione e strumenti.

Questo articolo offre una roadmap per l'accesso ai dati di report tramite l'API correlata.

Nel caso di problemi, vedere [Come ottenere supporto per Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).


## <a name="prerequisites"></a>Prerequisiti

Per accedere all'API di creazione report, anche se si prevede di accedervi tramite uno script, è necessario:

1. Assegnare i ruoli (Ruolo con autorizzazioni di lettura per la sicurezza, Amministratore della sicurezza, Amministratore globale)
2. Registrare un'applicazione
3. Concedere le autorizzazioni
4. Ottenere le impostazioni di configurazione

Per istruzioni dettagliate vedere i [prerequisiti di accesso all'API di creazione report di Azure AD](howto-configure-prerequisites-for-reporting-api.md).

## <a name="apis-with-graph-explorer"></a>API con Graph explorer

È possibile usare [Microsoft Graph explorer](https://developer.microsoft.com/graph/graph-explorer) per verificare i dati dell'API di accesso e di controllo. Assicurarsi di accedere al proprio account con entrambi i pulsanti di accesso nell'interfaccia utente di Graph Explorer e impostare le autorizzazioni **AuditLog.Read.All** e **Directory.Read.All** per il tenant come illustrato.   

![Graph Explorer](./media/concept-reporting-api/graph-explorer.png)

![Interfaccia utente di Autorizzazioni di modifica](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Usare i certificati per accedere all'API di creazione report di Azure AD 

Usare l'API di creazione report di Azure AD con certificati se si prevede di recuperare i dati dei report senza l'intervento dell'utente.

Per istruzioni dettagliate vedere [Ottenere dati con l'API di creazione report di Azure AD con certificati](tutorial-access-api-with-certificates.md).


## <a name="next-steps"></a>Passaggi successivi

 * [Informazioni di riferimento sulle API di controllo](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
 * [Informazioni di riferimento sulle API di report di attività di accesso](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
 * [Risolvere gli errori relativi all'API di creazione report di Azure AD](troubleshoot-graph-api.md)


