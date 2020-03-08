---
title: Controlli personalizzati in Azure AD accesso condizionale
description: Informazioni sul funzionamento di controlli personalizzati in Azure Active Directory l'accesso condizionale.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fff08690eb2807fbbd50f297761c57d3fef88fe
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671834"
---
# <a name="custom-controls-preview"></a>Controlli personalizzati (anteprima)

I controlli personalizzati sono una funzionalità dell'edizione Azure Active Directory Premium P1. Quando si usano i controlli personalizzati, gli utenti vengono reindirizzati a un servizio compatibile per soddisfare altri requisiti esterni ad Azure Active Directory. Per soddisfare questo controllo, il browser di un utente viene reindirizzato al servizio esterno, esegue le attività di autenticazione o convalida richieste e quindi viene reindirizzato di nuovo al Azure Active Directory. Azure Active Directory verifica la risposta e, se l'utente è stato autenticato o convalidato correttamente, l'utente continua nel flusso di accesso condizionale.

Questi controlli consentono l'uso di determinati servizi esterni o personalizzati come controlli di accesso condizionale e in genere estendono le funzionalità dell'accesso condizionale.

Di seguito sono elencati i provider che attualmente offrono un servizio compatibile:

- [Duo Security](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Ping Identity](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- [RSA](https://community.rsa.com/docs/DOC-81278)
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Per altre informazioni su questi servizi, contattare direttamente i provider.

## <a name="creating-custom-controls"></a>Creazione di controlli personalizzati

Per creare un controllo personalizzato, è opportuno prima contattare il provider a cui ci si vuole rivolgere. Ogni provider non Microsoft dispone di un proprio processo e requisiti per iscriversi, sottoscrivere o in altro modo diventare parte del servizio e per indicare che si desidera eseguire l'integrazione con l'accesso condizionale. A questo punto, il provider fornirà un blocco di dati in formato JSON. Questi dati consentono al provider e all'accesso condizionale di interagire con il tenant, creare il nuovo controllo e definire il modo in cui l'accesso condizionale può determinare se gli utenti hanno eseguito correttamente la verifica con il provider.

I controlli personalizzati non possono essere usati con l'automazione di Identity Protection che richiede l'autenticazione a più fattori o per elevare i ruoli in Privileged Identity Manager (PIM).

Copiare i dati JSON e incollarli nella casella di testo corrispondente. Non apportare modifiche al file JSON a meno che non si conosca in modo esplicito la modifica apportata. L'introduzione di una modifica potrebbe interrompere la connessione tra il provider e Microsoft e potenzialmente bloccare gli utenti fuori dai rispettivi account.

L'opzione per creare un controllo personalizzato si trova nella sezione **Gestisci** della pagina **accesso condizionale** .

![Control](./media/controls/82.png)

Facendo clic su **Nuovo controllo personalizzato** si apre un pannello con una casella di testo per i dati JSON relativi al controllo.  

![Control](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>Eliminazione di controlli personalizzati

Per eliminare un controllo personalizzato, è prima necessario assicurarsi che non venga usato in alcun criterio di accesso condizionale. Al termine della procedura:

1. Passare all'elenco di controlli personalizzati
1. Fare clic su ...  
1. Selezionare **Elimina**.

## <a name="editing-custom-controls"></a>Modifica di controlli personalizzati

Per modificare un controllo personalizzato, è necessario eliminare il controllo corrente e creare un nuovo controllo con le informazioni aggiornate.

## <a name="next-steps"></a>Passaggi successivi

- [Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)

- [Modalità solo report](concept-conditional-access-report-only.md)

- [Simulare il comportamento di accesso usando lo strumento di What If dell'accesso condizionale](troubleshoot-conditional-access-what-if.md)
