---
title: Introduzione all'API di creazione report di Azure AD | Microsoft Docs
description: Come iniziare a usare l'API di creazione report di Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: a818c9b0e2c9097f45d2fdd39676ef6807d06ca5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33929087"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Introduzione all'API di creazione report di Azure Active Directory

Azure Active Directory fornisce una serie di [report](active-directory-reporting-azure-portal.md). I dati di questi report possono essere molto utili per le applicazioni, ad esempio i sistemi SIEM e gli strumenti di controllo e business intelligence. 

Usando l'API di creazione report di Azure AD è possibile ottenere l'accesso ai dati a livello di codice tramite un set di API basate su REST. È possibile chiamare le API da numerosi linguaggi di programmazione e strumenti.

Questo articolo offre una roadmap per l'accesso ai dati di report tramite l'API correlata.

Nel caso di problemi, vedere [Come ottenere supporto per Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).


## <a name="prerequisites"></a>prerequisiti

Per accedere all'API di creazione report, anche se si prevede di accedervi tramite uno script, è necessario:

1. Assegnare i ruoli (Ruolo con autorizzazioni di lettura per la sicurezza, Amministratore della sicurezza, Amministratore globale)
2. Registrare un'applicazione
3. Concedere le autorizzazioni
4. Ottenere le impostazioni di configurazione


 
Per istruzioni dettagliate vedere i [prerequisiti di accesso all'API di creazione report di Azure AD](active-directory-reporting-api-prerequisites-azure-portal.md).


## <a name="recommendation"></a>Raccomandazione 

Se si intende recuperare dei dati dei report senza l'intervento dell'utente, è consigliabile usare l'API di creazione di report di Azure AD con certificati.

Per istruzioni dettagliate vedere [Ottenere dati con l'API di creazione report di Azure Active Directory con certificati](active-directory-reporting-api-with-certificates.md).


## <a name="explore"></a>Esplorazione

Ottenere una prima impressione delle API di creazione report:
   
   - [Utilizzo degli esempi dell'API di controllo](active-directory-reporting-api-audit-samples.md) 
 
   - [Utilizzo degli esempi dell'API di creazione report sull'attività di accesso](active-directory-reporting-api-sign-in-activity-samples.md)


## <a name="customize"></a>Personalizza  

Creare una soluzione personalizzata: 
   
   - [Utilizzo delle informazioni di riferimento sull'API di controllo](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 

   - [Utilizzo delle informazioni di riferimento sull'API di creazione report sull'attività di accesso](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



