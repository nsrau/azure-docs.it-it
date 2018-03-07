---
title: "Azure AD Connect Health - Avviso \"I dati del Servizio integrità non sono aggiornati\" | Microsoft Docs"
description: "In questo documento vengono descritte la causa dell'avviso \"I dati del Servizio integrità non sono aggiornati\" e la modalità di risoluzione di questo problema."
services: active-directory
documentationcenter: 
author: zhiweiw
manager: maheshu
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: 45e88320a64770239c8f322212e12ca7826cd0da
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>I dati del Servizio integrità non sono aggiornati

## <a name="overview"></a>Panoramica
<li>Azure AD Connect Health genera l'avviso sull'aggiornamento dei dati quando non riceve tutti i punti dati dal server per due ore. Il titolo dell'avviso è **I dati del Servizio integrità non sono aggiornati**. </li>
<li>Viene attivato l'avviso dello stato di tipo **Avviso** se Azure AD Connect Health non riceve elementi dati parziali dal server per due ore. L'avviso di stato di tipo Avviso non attiva le notifiche tramite posta elettronica all'amministratore del tenant. </li>
<li>Viene attivato l'avviso di stato di tipo **Errore** se Azure AD Connect Health non riceve elementi dati dal server per due ore. L'avviso di stato di tipo Errore attiva le notifiche tramite posta elettronica all'amministratore del tenant. </li>

>[!IMPORTANT] 
> Questo avviso è conforme ai [criteri di conservazione dei dati](active-directory-aadconnect-health-gdpr.md#data-retention-policy) di Connect Health.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi 
* Assicurarsi di esaminare le informazioni nella [sezione dei requisiti](active-directory-aadconnect-health-agent-install.md#requirements) e di essere conformi a tali requisiti.
* Usare lo [strumento per il test della connettività](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) per individuare i problemi di connettività.


## <a name="next-steps"></a>Passaggi successivi
* [Domande frequenti su Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
