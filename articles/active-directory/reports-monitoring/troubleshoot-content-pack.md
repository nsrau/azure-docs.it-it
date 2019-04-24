---
title: Risoluzione dei problemi relativi ai pacchetti di contenuto dei log attività di Azure Active Directory | Microsoft Docs
description: Offre un elenco dei messaggi di errore del pacchetto di contenuto delle attività di Azure Active Directory e i passaggi per risolvere il problema.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78676ac2f2dcff74a27e0260a5d83e924f7c246f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60284951"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Risoluzione dei problemi relativi ai pacchetti di contenuto dei log attività di Azure Active Directory 

|  |
|--|
|Attualmente, il pacchetto di contenuto Power BI di Azure AD usa le API Graph di Azure AD per recuperare dati dal tenant di Azure AD. Di conseguenza, potrebbe notarsi una certa disparità tra i dati disponibili nel pacchetto di contenuto e i dati recuperati tramite le [API Microsoft Graph per la creazione di report](concept-reporting-api.md). |
|  |

Quando si usa il pacchetto di contenuto di Power BI per Azure Active Directory (Azure AD), è possibile che si verifichino gli errori seguenti: 

- [Aggiornamento non riuscito](troubleshoot-content-pack.md#refresh-failed) 
- [L'aggiornamento delle credenziali dell'origine dati non è riuscito](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [L'importazione dei dati richiede troppo tempo](#data-import-is-too-slow) 

Questo articolo offre informazioni sulle possibili cause e su come risolvere questi errori.
 
## <a name="refresh-failed"></a>Aggiornamento non riuscito 
 
**Modalità di esposizione dell’errore**: Messaggio di posta elettronica inviato da Power BI o stato di errore nella cronologia aggiornamenti. 


| Causa | Modalità di correzione |
| ---   | ---        |
| Gli errori di aggiornamento non riuscito possono verificarsi quando le credenziali degli utenti che si connettono al pacchetto di contenuto sono state reimpostate ma non sono state aggiornate nelle impostazioni di connessione del pacchetto di contenuto. | Individuare in Power BI il set di dati corrispondente al dashboard dei log attività di Azure AD (**Log attività di Azure Active Directory**), scegliere Pianifica aggiornamenti e quindi immettere le credenziali di Azure AD. |
| Un aggiornamento può non riuscire a causa di problemi relativi ai dati nel pacchetto di contenuto sottostante. | [Inviare un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
## <a name="failed-to-update-data-source-credentials"></a>L'aggiornamento delle credenziali dell'origine dati non è riuscito 
 
**Modalità di esposizione dell’errore**: In Power BI durante la connessione al pacchetto di contenuto dei log attività di Azure AD. 

| Causa | Modalità di correzione |
| ---   | ---        |
| L'utente che sta tentando di connettersi non è un amministratore globale, non dispone di un ruolo con autorizzazioni di lettura per la sicurezza né è un amministratore della sicurezza. | Per accedere ai pacchetti di contenuto, usare un account di amministratore globale, di amministratore della sicurezza o con autorizzazioni di lettura per la sicurezza. |
| Il tenant non è un tenant Premium o non include almeno un utente con un file di licenza Premium. | [Inviare un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 


## <a name="data-import-is-too-slow"></a>L'importazione dei dati è troppo lenta 
 
**Modalità di esposizione dell’errore**: In Power BI, dopo la connessione del pacchetto di contenuto, viene avviato il processo di importazione dei dati per preparare il dashboard per il log attività di Azure AD. Viene visualizzato il messaggio: **Importazione di dati...** senza alcun ulteriore avanzamento.  

| Causa | Modalità di correzione |
| ---   | ---        |
| A seconda delle dimensioni del tenant, questo passaggio può richiedere da alcuni minuti a 30 minuti. | Se entro un'ora il messaggio non cambia e non viene visualizzato il dashboard, [inviare un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Passaggi successivi

* [Installa pacchetto di contenuto di Power BI per i report di Azure AD](quickstart-install-power-bi-content-pack.md).
* [Usare il pacchetto di contenuto di Power BI per i report di Azure AD per visualizzare i dati](howto-power-bi-content-pack.md)
* [Come ottenere supporto per Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md)
