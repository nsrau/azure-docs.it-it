---
title: Azure AD Connect Health - Avviso "I dati del Servizio integrità non sono aggiornati" | Microsoft Docs
description: In questo documento vengono descritte la causa dell'avviso "I dati del Servizio integrità non sono aggiornati" e la modalità di risoluzione di questo problema.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: 430ea5f0a6f737d7632a4352c24d893368b80558
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2018
ms.locfileid: "46310380"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>I dati del Servizio integrità non sono aggiornati

## <a name="overview"></a>Panoramica
<li>Azure AD Connect Health genera l'avviso sull'aggiornamento dei dati quando non riceve tutti i punti dati dal server per due ore. Il titolo dell'avviso è **I dati del Servizio integrità non sono aggiornati**. </li>
<li>Viene attivato l'avviso dello stato di tipo **Avviso** se Azure AD Connect Health non riceve elementi dati parziali dal server per due ore. L'avviso di stato di tipo Avviso non attiva le notifiche tramite posta elettronica all'amministratore del tenant. </li>
<li>Viene attivato l'avviso di stato di tipo **Errore** se Azure AD Connect Health non riceve elementi dati dal server per due ore. L'avviso di stato di tipo Errore attiva le notifiche tramite posta elettronica all'amministratore del tenant. </li>

>[!IMPORTANT] 
> Questo avviso è conforme ai [criteri di conservazione dei dati](reference-connect-health-user-privacy.md#data-retention-policy) di Connect Health.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi 
* Assicurarsi di esaminare le informazioni nella [sezione dei requisiti](how-to-connect-health-agent-install.md#requirements) e di essere conformi a tali requisiti.
* Usare lo [strumento per il test della connettività](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) per individuare i problemi di connettività.


## <a name="next-steps"></a>Passaggi successivi
* [Domande frequenti su Azure AD Connect Health](reference-connect-health-faq.md)
