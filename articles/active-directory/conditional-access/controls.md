---
title: Controlli personalizzati in Azure AD accesso condizionale
description: Informazioni sul funzionamento di controlli personalizzati in Azure Active Directory l'accesso condizionale.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 147c6016c56d45fadca4f4b8e583bce54006ef07
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88922106"
---
# <a name="custom-controls-preview"></a>Controlli personalizzati (anteprima)

I controlli personalizzati sono una funzionalità di anteprima della Azure Active Directory. Quando si usano i controlli personalizzati, gli utenti vengono reindirizzati a un servizio compatibile per soddisfare i requisiti di autenticazione al di fuori della Azure Active Directory. Per soddisfare questo controllo, il browser di un utente viene reindirizzato al servizio esterno, esegue tutte le operazioni di autenticazione necessarie e viene reindirizzato di nuovo al Azure Active Directory. Azure Active Directory verifica la risposta e, se l'utente è stato autenticato o convalidato correttamente, l'utente continua nel flusso di accesso condizionale.

> [!NOTE]
> Per ulteriori informazioni sulle modifiche pianificate per la funzionalità di controllo personalizzato, vedere la pagina relativa al [nuovo aggiornamento](../fundamentals/whats-new.md#upcoming-changes-to-custom-controls)di febbraio 2020.

## <a name="creating-custom-controls"></a>Creazione di controlli personalizzati

I controlli personalizzati funzionano con un set limitato di provider di autenticazione approvati. Per creare un controllo personalizzato, è opportuno prima contattare il provider a cui ci si vuole rivolgere. Ogni provider non Microsoft dispone di un proprio processo e requisiti per iscriversi, sottoscrivere o in altro modo diventare parte del servizio e per indicare che si desidera eseguire l'integrazione con l'accesso condizionale. A questo punto, il provider fornirà un blocco di dati in formato JSON. Questi dati consentono al provider e all'accesso condizionale di interagire con il tenant, creare il nuovo controllo e definire il modo in cui l'accesso condizionale può determinare se gli utenti hanno eseguito correttamente la verifica con il provider.

Copiare i dati JSON e incollarli nella casella di testo corrispondente. Non apportare modifiche al file JSON a meno che non si conosca in modo esplicito la modifica apportata. L'introduzione di una modifica potrebbe interrompere la connessione tra il provider e Microsoft e potenzialmente bloccare gli utenti fuori dai rispettivi account.

L'opzione per creare un controllo personalizzato si trova nella sezione **Gestisci** della pagina **accesso condizionale** .

![Interfaccia di controlli personalizzati nell'accesso condizionale](./media/controls/custom-controls-conditional-access.png)

Facendo clic su **Nuovo controllo personalizzato** si apre un pannello con una casella di testo per i dati JSON relativi al controllo.  

![Nuovo controllo personalizzato](./media/controls/new-custom-controls-conditional-access.png)

## <a name="deleting-custom-controls"></a>Eliminazione di controlli personalizzati

Per eliminare un controllo personalizzato, è prima necessario assicurarsi che non venga usato in alcun criterio di accesso condizionale. Al termine della procedura:

1. Passare all'elenco di controlli personalizzati
1. Fare clic su ...  
1. Selezionare **Elimina**.

## <a name="editing-custom-controls"></a>Modifica di controlli personalizzati

Per modificare un controllo personalizzato, è necessario eliminare il controllo corrente e creare un nuovo controllo con le informazioni aggiornate.

## <a name="known-limitations"></a>Limitazioni note

I controlli personalizzati non possono essere usati con l'automazione di Identity Protection che richiede Azure Multi-Factor Authentication, Azure AD la reimpostazione della password self-service (SSPR), soddisfa i requisiti di attestazione di autenticazione a più fattori, per elevare i ruoli in Privileged Identity Manager (PIM) o come parte della registrazione dei dispositivi di Intune.

## <a name="next-steps"></a>Passaggi successivi

- [Criteri comuni di accesso condizionale ](concept-conditional-access-policy-common.md)

- [Modalità solo report](concept-conditional-access-report-only.md)

- [Simulare il comportamento di accesso usando lo strumento What If per l'accesso condizionale](troubleshoot-conditional-access-what-if.md)
