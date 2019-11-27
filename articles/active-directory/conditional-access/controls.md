---
title: Controlli di accesso nell'accesso condizionale Azure Active Directory
description: Informazioni sul funzionamento di controlli di accesso in Azure Active Directory l'accesso condizionale.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: cac92da744b3d5b7aeaa325c7cc564a3d7e2abdd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380800"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>Che cosa sono i controlli di accesso nell'accesso condizionale Azure Active Directory?

Con [l'accesso condizionale Azure Active Directory (Azure ad)](../active-directory-conditional-access-azure-portal.md), è possibile controllare il modo in cui gli utenti autorizzati accedono alle app cloud. In un criterio di accesso condizionale si definisce la risposta ("Esegui questa operazione") per il motivo dell'attivazione dei criteri ("quando ciò accade").

![Controllo](./media/controls/10.png)

Nel contesto dell'accesso condizionale,

- "**Quando accade questo**" è la **condizione**.
- "**Fare questo**" è il **controllo di accesso**.

La combinazione di un'istruzione Condition con i controlli rappresenta un criterio di accesso condizionale.

![Controllo](./media/controls/61.png)

Ogni controllo è un requisito che deve essere soddisfatto dalla persona o dal sistema che esegue l'accesso o una restrizione sulle operazioni che l'utente può eseguire dopo l'accesso.

Sono disponibili due tipi di controlli:

- **Controlli di concessione**: per controllare l'accesso
- **Controlli di sessione**: per limitare l'accesso all'interno di una sessione

In questo argomento vengono illustrati i vari controlli disponibili in Azure AD l'accesso condizionale. 

## <a name="grant-controls"></a>Controlli di concessione

Con i controlli di concessione è possibile bloccare completamente l'accesso o concedere l'accesso con requisiti aggiuntivi selezionando i controlli desiderati. Per più controlli, è possibile:

- Richiedere che tutti i controlli selezionati siano soddisfatti (*AND*)
- Richiedere che un solo controllo selezionato sia soddisfatto (*OR*)

![Controllo](./media/controls/18.png)

### <a name="multi-factor-authentication"></a>Autenticazione a più fattori

È possibile usare questo controllo per richiedere l'autenticazione a più fattori per l'accesso all'app cloud specificata. Questo controllo supporta i provider di autenticazione a più fattori seguenti:

- Azure Multi-Factor Authentication
- Un provider di autenticazione a più fattori locale, in combinazione con Active Directory Federation Services (AD FS)

L'uso dell'autenticazione a più fattori assicura la protezione delle risorse nel caso in cui un utente non autorizzato abbia avuto accesso alle credenziali primarie di un utente valido.

### <a name="compliant-device"></a>Dispositivo conforme

È possibile configurare i criteri di accesso condizionale basati su dispositivo. L'obiettivo dei criteri di accesso condizionale basato su dispositivo consiste nel concedere l'accesso alle app Cloud selezionate solo da [dispositivi gestiti](require-managed-devices.md). Una delle opzioni disponibili per limitare l'accesso ai dispositivi gestiti consiste nel richiedere che un dispositivo sia contrassegnato come conforme. Un dispositivo può essere contrassegnato come conforme da Intune (per qualsiasi sistema operativo del dispositivo) o dal sistema MDM di terze parti per i dispositivi Windows 10. I sistemi MDM di terze parti per sistemi operativi per dispositivo diversi da Windows 10 non sono supportati. 

Il dispositivo deve essere registrato in Azure AD prima che possa essere contrassegnato come conforme. Per registrare un dispositivo, sono disponibili tre opzioni: 

- Dispositivi registrati in Azure AD
- Dispositivi aggiunti ad Azure AD  
- Dispositivi aggiunti all'identità ibrida di Azure AD

Queste tre opzioni sono illustrate nell'articolo [che cos'è un'identità del dispositivo?](../devices/overview.md)

Per altre informazioni, vedere [come richiedere i dispositivi gestiti per l'accesso alle app cloud con accesso condizionale](require-managed-devices.md).

### <a name="hybrid-azure-ad-joined-device"></a>Dispositivo aggiunto all'identità ibrida di Azure AD

La richiesta di un dispositivo Azure AD ibrido aggiunto è un'altra opzione per configurare i criteri di accesso condizionale basati su dispositivo. Questo requisito si riferisce a tablet aziendali, laptop e desktop di Windows aggiunti ad Active Directory locale. Se questa opzione è selezionata, i criteri di accesso condizionale concedono l'accesso ai tentativi di accesso effettuati con i dispositivi aggiunti all'Active Directory locale e al Azure Active Directory.  

Per altre informazioni, vedere [configurare Azure Active Directory Criteri di accesso condizionale basato su dispositivo](require-managed-devices.md).

### <a name="approved-client-app"></a>App client approvata

Poiché i dipendenti di un'azienda usano i dispositivi mobili per attività sia lavorative che personali, può essere utile avere la possibilità di proteggere i dati aziendali accessibili da dispositivi anche nel caso in cui vengano gestiti da altri utenti.
È possibile usare i [criteri di protezione delle app di Intune](https://docs.microsoft.com/intune/app-protection-policy) per proteggere i dati aziendali indipendentemente dalla soluzione di gestione dei dispositivi mobili (MDM).

Con le app client approvate, è possibile richiedere a un'app client che prova ad accedere alle app cloud di supportare i [criteri di protezione delle app di Intune](https://docs.microsoft.com/intune/app-protection-policy). È possibile, ad esempio, limitare l'accesso a Exchange Online all'app Outlook. I criteri di accesso condizionale che richiedono app client approvate sono noti anche come [criteri di accesso condizionale basato su app](app-based-conditional-access.md). Per un elenco di app client approvate supportate, vedere [Requisito per le app client approvate](technical-reference.md#approved-client-app-requirement).

### <a name="app-protection-policy-preview"></a>Criteri di protezione delle app (anteprima)

Poiché i dipendenti di un'azienda usano i dispositivi mobili per attività sia lavorative che personali, può essere utile avere la possibilità di proteggere i dati aziendali accessibili da dispositivi anche nel caso in cui vengano gestiti da altri utenti.
È possibile usare i [criteri di protezione delle app di Intune](https://docs.microsoft.com/intune/app-protection-policy) per proteggere i dati aziendali indipendentemente dalla soluzione di gestione dei dispositivi mobili (MDM).

Con i criteri di protezione delle app è possibile limitare l'accesso alle applicazioni client che hanno segnalato a Azure AD hanno ricevuto i [criteri di protezione delle app di Intune](https://docs.microsoft.com/intune/app-protection-policy). Ad esempio, è possibile limitare l'accesso a Exchange Online all'app Outlook con i criteri di protezione delle app di Intune. I criteri di accesso condizionale che richiedono criteri di protezione delle app sono noti anche come [criteri di accesso condizionale basato sulla protezione delle app](app-protection-based-conditional-access.md). 

Il dispositivo deve essere registrato per Azure AD prima che un'applicazione possa essere contrassegnata come protetta da criteri.

Per un elenco delle app client protette da criteri supportate, vedere Requisiti per i [criteri di protezione delle app](technical-reference.md#app-protection-policy-requirement).

### <a name="terms-of-use"></a>Condizioni per l'utilizzo

È possibile richiedere a un utente nel tenant di accettare le condizioni per l'utilizzo prima di concedergli il permesso di accedere a una risorsa. Gli amministratori possono configurare e personalizzare le condizioni per l'utilizzo caricando un documento PDF. Se un utente rientra nell'ambito di questo controllo, gli viene concesso l'accesso a un'applicazione solo se ha accettato le condizioni per l'utilizzo.

## <a name="custom-controls-preview"></a>Controlli personalizzati (anteprima)

I controlli personalizzati sono una funzionalità dell'edizione Azure Active Directory Premium P1. Quando si usano i controlli personalizzati, gli utenti vengono reindirizzati a un servizio compatibile per soddisfare altri requisiti esterni ad Azure Active Directory. Per soddisfare questo controllo, il browser dell'utente viene reindirizzato al servizio esterno, esegue le eventuali attività di autenticazione e convalida richieste e viene quindi reindirizzato ad Azure Active Directory. Azure Active Directory verifica la risposta e, se l'utente è stato autenticato o convalidato correttamente, l'utente continua nel flusso di accesso condizionale.

Questi controlli consentono l'uso di determinati servizi esterni o personalizzati come controlli di accesso condizionale e in genere estendono le funzionalità dell'accesso condizionale.

Di seguito sono elencati i provider che attualmente offrono un servizio compatibile:

- [Duo Security](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Ping Identity](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- RSA
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Per altre informazioni su questi servizi, contattare direttamente i provider.

### <a name="creating-custom-controls"></a>Creazione di controlli personalizzati

Per creare un controllo personalizzato, è opportuno prima contattare il provider a cui ci si vuole rivolgere. Ogni provider non Microsoft dispone di un proprio processo e requisiti per iscriversi, sottoscrivere o in altro modo diventare parte del servizio e per indicare che si desidera eseguire l'integrazione con l'accesso condizionale. A questo punto, il provider fornirà un blocco di dati in formato JSON. Questi dati consentono al provider e all'accesso condizionale di interagire con il tenant, creare il nuovo controllo e definire il modo in cui l'accesso condizionale può determinare se gli utenti hanno eseguito correttamente la verifica con il provider.

I controlli personalizzati non possono essere usati con l'automazione di Identity Protection che richiede l'autenticazione a più fattori o per elevare i ruoli in Privileged Identity Manager (PIM).

Copiare i dati JSON e incollarli nella casella di testo corrispondente. Non apportare modifiche ai dati JSON a meno di non aver compreso in modo esplicito la modifica che si sta apportando. L'introduzione di una modifica potrebbe interrompere la connessione tra il provider e Microsoft e potenzialmente bloccare gli utenti fuori dai rispettivi account.

L'opzione per creare un controllo personalizzato si trova nella sezione **Gestisci** della pagina **accesso condizionale** .

![Controllo](./media/controls/82.png)

Facendo clic su **Nuovo controllo personalizzato** si apre un pannello con una casella di testo per i dati JSON relativi al controllo.  

![Controllo](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>Eliminazione di controlli personalizzati

Per eliminare un controllo personalizzato, è prima necessario assicurarsi che non venga usato in alcun criterio di accesso condizionale. Al termine della procedura:

1. Passare all'elenco di controlli personalizzati
1. Fare clic su ...  
1. Selezionare **Elimina**.

### <a name="editing-custom-controls"></a>Modifica di controlli personalizzati

Per modificare un controllo personalizzato, è necessario eliminare il controllo corrente e creare un nuovo controllo con le informazioni aggiornate.

## <a name="session-controls"></a>Controlli di sessione

I controlli di sessione consentono di limitare l'esperienza in un'app cloud. Questi controlli sono imposti dalle app cloud e si basano sulle informazioni aggiuntive relative alla sessione fornite da Azure AD all'app.

![Controllo](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Usa restrizioni imposte dalle app

È possibile usare questo controllo per richiedere ad Azure AD di passare le informazioni sul dispositivo alle app cloud selezionate. Le informazioni sul dispositivo consentono alle app cloud di sapere se una connessione viene avviata da un dispositivo conforme o aggiunto al dominio. Questo controllo supporta solo SharePoint Online ed Exchange Online come app cloud selezionate. Una volta selezionata, l'app cloud usa le informazioni sul dispositivo per offrire agli utenti un'esperienza completa o limitata, a seconda dello stato del dispositivo.

Per altre informazioni, vedere:

- [Abilitazione dell'accesso limitato con SharePoint Online](https://aka.ms/spolimitedaccessdocs)
- [Abilitazione dell'accesso limitato con Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare criteri di accesso condizionale, vedere [Richiedere MFA per app specifiche con l'accesso condizionale di Azure Active Directory](app-based-mfa.md).
- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere le [procedure consigliate per l'accesso condizionale](best-practices.md) in Azure Active Directory.
