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
ms.openlocfilehash: 6f1df037b66c72177a96f77231cee70782d04992
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620704"
---
# <a name="custom-controls-preview"></a>Controlli personalizzati (anteprima)

I controlli personalizzati sono una funzionalità dell'edizione Azure Active Directory Premium P1. Quando si usano i controlli personalizzati, gli utenti vengono reindirizzati a un servizio compatibile per soddisfare altri requisiti esterni ad Azure Active Directory. Per soddisfare questo controllo, il browser dell'utente viene reindirizzato al servizio esterno, esegue le eventuali attività di autenticazione e convalida richieste e viene quindi reindirizzato ad Azure Active Directory. Azure Active Directory verifica la risposta e, se l'utente è stato autenticato o convalidato correttamente, l'utente continua nel flusso di accesso condizionale.

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

Copiare i dati JSON e incollarli nella casella di testo corrispondente. Non apportare modifiche ai dati JSON a meno di non aver compreso in modo esplicito la modifica che si sta apportando. L'introduzione di una modifica potrebbe interrompere la connessione tra il provider e Microsoft e potenzialmente bloccare gli utenti fuori dai rispettivi account.

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

## <a name="session-controls"></a>Controlli di sessione

I controlli di sessione consentono di limitare l'esperienza in un'app cloud. Questi controlli sono imposti dalle app cloud e si basano sulle informazioni aggiuntive relative alla sessione fornite da Azure AD all'app.

![Control](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Usa restrizioni imposte dalle app

È possibile usare questo controllo per richiedere ad Azure AD di passare le informazioni sul dispositivo alle app cloud selezionate. Le informazioni sul dispositivo consentono alle app cloud di sapere se una connessione viene avviata da un dispositivo conforme o aggiunto al dominio. Questo controllo supporta solo SharePoint Online ed Exchange Online come app cloud selezionate. Una volta selezionata, l'app cloud usa le informazioni sul dispositivo per offrire agli utenti un'esperienza completa o limitata, a seconda dello stato del dispositivo.

Per altre informazioni, vedere:

- [Abilitazione dell'accesso limitato con SharePoint Online](https://aka.ms/spolimitedaccessdocs)
- [Abilitazione dell'accesso limitato con Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare criteri di accesso condizionale, vedere [Richiedere MFA per app specifiche con l'accesso condizionale di Azure Active Directory](app-based-mfa.md).
- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere le [procedure consigliate per l'accesso condizionale](best-practices.md) in Azure Active Directory.
