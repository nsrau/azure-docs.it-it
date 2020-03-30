---
title: Controlli personalizzati in Azure AD Conditional AccessCustom controls in Azure AD Conditional Access
description: Informazioni sul funzionamento dei controlli personalizzati in Azure Active Directory Conditional Access.Learn how custom controls in Azure Active Directory Conditional Access work.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/18/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8c149279a755eb186a3fdc7891e9b511d18c7f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050542"
---
# <a name="custom-controls-preview"></a>Controlli personalizzati (anteprima)

Controlli personalizzati è una funzionalità di anteprima di Azure Active Directory.Custom controls is a preview capability of the Azure Active Directory. Quando si usano controlli personalizzati, gli utenti vengono reindirizzati a un servizio compatibile per soddisfare i requisiti di autenticazione all'esterno di Azure Active Directory.When using custom controls, your users are redirected to a compatible service to satisfy authentication requirements outside of Azure Active Directory. To satisfy this control, a user's browser is redirected to the external service, performs any required authentication, and is then redirected back to Azure Active Directory. Azure Active Directory verifica la risposta e, se l'utente è stato autenticato o convalidato correttamente, l'utente continua nel flusso di accesso condizionale.

> [!NOTE]
> Per ulteriori informazioni sulle modifiche che si stanno pianificando per la funzionalità di controllo personalizzato, vedere [l'aggiornamento Novità di](../fundamentals/whats-new.md#upcoming-changes-to-custom-controls)febbraio 2020 .

## <a name="creating-custom-controls"></a>Creazione di controlli personalizzati

I controlli personalizzati funzionano con un set limitato di provider di autenticazione approvati. Per creare un controllo personalizzato, è opportuno prima contattare il provider a cui ci si vuole rivolgere. Ogni provider non Microsoft ha il proprio processo e requisiti per iscriversi, sottoscrivere o altrimenti diventare parte del servizio e per indicare che si desidera integrare con l'accesso condizionale. A questo punto, il provider fornirà un blocco di dati in formato JSON. Questi dati consentono al provider e all'accesso condizionale di collaborare per il tenant, crea il nuovo controllo e definisce il modo in cui l'accesso condizionale può stabilire se gli utenti hanno eseguito correttamente la verifica con il provider.

Copiare i dati JSON e incollarli nella casella di testo corrispondente. Non apportare modifiche al codice JSON a meno che non si comprenda in modo esplicito la modifica che si sta apportando. L'introduzione di una modifica potrebbe interrompere la connessione tra il provider e Microsoft e potenzialmente bloccare gli utenti fuori dai rispettivi account.

L'opzione per creare un controllo personalizzato si trova nella sezione **Gestisci** della pagina **Accesso condizionale.**

![Controllo](./media/controls/82.png)

Facendo clic su **Nuovo controllo personalizzato** si apre un pannello con una casella di testo per i dati JSON relativi al controllo.  

![Controllo](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>Eliminazione di controlli personalizzati

Per eliminare un controllo personalizzato, è innanzitutto necessario assicurarsi che non venga utilizzato in alcun criterio di accesso condizionale. Al termine della procedura:

1. Passare all'elenco di controlli personalizzati
1. Fare clic su ...  
1. Selezionare **Elimina**.

## <a name="editing-custom-controls"></a>Modifica di controlli personalizzati

Per modificare un controllo personalizzato, è necessario eliminare il controllo corrente e creare un nuovo controllo con le informazioni aggiornate.

## <a name="known-limitations"></a>Limitazioni note

I controlli personalizzati non possono essere usati con l'automazione di Identity Protection che richiede Azure Multi-Factor Authentication, la reimpostazione della password self-service (SSPR) di Azure AD, la soddisfazione dei requisiti di attestazione di autenticazione a più fattori o l'elevazione dei ruoli in Privilegiprivilegiati Identity Manager (PIM).

## <a name="next-steps"></a>Passaggi successivi

- [Criteri comuni di Accesso condizionaleConditional Access common policies](concept-conditional-access-policy-common.md)

- [Modalità solo report](concept-conditional-access-report-only.md)

- [Simulare il comportamento di accesso con lo strumento Elementi di ricerca condizionali](troubleshoot-conditional-access-what-if.md)
