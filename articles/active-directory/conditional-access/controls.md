---
title: Quali sono i controlli di accesso nell'accesso condizionale di Azure Active Directory? | Microsoft Docs
description: Informazioni sul funzionamento dei controlli di accesso nell'accesso condizionale di Azure Active Directory.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: acc30bc18921b79be3b5e5b2ae340eab29dd5bcf
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305764"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>Quali sono i controlli di accesso nell'accesso condizionale di Azure Active Directory?

Con l'[accesso condizionale di Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md) è possibile controllare il modo in cui gli utenti autorizzati accedono alle app cloud. Nei criteri di accesso condizionale si definisce la risposta ("fare questo") al motivo che ha attivato i criteri ("quando accade questo").

![Controllo](./media/controls/10.png)

Nel contesto dell'accesso condizionale,

- "**Quando accade questo**" è la **condizione**.

- "**Fare questo**" è il **controllo di accesso**.

La combinazione di un'istruzione della condizione con i controlli rappresenta un criterio di accesso condizionale.

![Controllo](./media/controls/61.png)

Ogni controllo è un requisito che deve essere soddisfatto dalla persona o dal sistema che esegue l'accesso o una restrizione sulle operazioni che l'utente può eseguire dopo l'accesso.

Sono disponibili due tipi di controlli:

- **Controlli di concessione**: per controllare l'accesso

- **Controlli di sessione**: per limitare l'accesso all'interno di una sessione

Questo argomento illustra i diversi controlli disponibili nell'accesso condizionale di Azure AD. 

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

È possibile configurare criteri di accesso condizionale in base al dispositivo. Lo scopo di un criterio di accesso condizionale basato sul dispositivo è di concedere l'accesso solo alle app cloud selezionate da [dispositivi gestiti](require-managed-devices.md). Una delle opzioni disponibili per limitare l'accesso ai dispositivi gestiti consiste nel richiedere che un dispositivo sia contrassegnato come conforme. Un dispositivo può essere contrassegnato come conforme da Intune (per qualsiasi sistema operativo del dispositivo) o dal sistema MDM di terze parti per i dispositivi Windows 10. I sistemi MDM di terze parti per sistemi operativi per dispositivo diversi da Windows 10 non sono supportati. 

Il dispositivo deve essere registrato in Azure AD prima che possa essere contrassegnato come conforme. Per registrare un dispositivo, sono disponibili tre opzioni: 

- [Dispositivi registrati in Azure AD](../devices/overview.md#azure-ad-registered-devices)
- [Dispositivi aggiunti ad Azure AD](../devices/overview.md#azure-ad-joined-devices)  
- [Dispositivi aggiunti ad Azure AD ibrido](../devices/overview.md#hybrid-azure-ad-joined-devices)

Per altre informazioni, vedere [Come richiedere dispositivi gestiti per l'accesso alle app cloud con l'accesso condizionale](require-managed-devices.md).

### <a name="hybrid-azure-ad-joined-device"></a>Dispositivo aggiunto all'identità ibrida di Azure AD

Richiedere un dispositivo aggiunto all'identità ibrida di Azure AD è un'altra opzione disponibile per configurare criteri di accesso condizionale basato su dispositivo. Questo requisito si riferisce a tablet aziendali, laptop e desktop di Windows aggiunti ad Active Directory locale. Se questa opzione è selezionata, i criteri di accesso condizionale consentono l'accesso tramite dispositivi aggiunti ad Active Directory locale e ad Azure Active Directory in uso.  

Per altre informazioni, vedere [Configurare i criteri di accesso condizionale basato su dispositivo di Azure Active Directory](require-managed-devices.md).

### <a name="approved-client-app"></a>App client approvata

Poiché i dipendenti di un'azienda usano i dispositivi mobili per attività sia lavorative che personali, può essere utile avere la possibilità di proteggere i dati aziendali accessibili da dispositivi anche nel caso in cui vengano gestiti da altri utenti.
È possibile usare i [criteri di protezione delle app di Intune](https://docs.microsoft.com/intune/app-protection-policy) per proteggere i dati aziendali indipendentemente dalla soluzione di gestione dei dispositivi mobili (MDM).

Con le app client approvate, è possibile richiedere a un'app client che prova ad accedere alle app cloud di supportare i [criteri di protezione delle app di Intune](https://docs.microsoft.com/intune/app-protection-policy). È possibile, ad esempio, limitare l'accesso a Exchange Online all'app Outlook. I criteri di accesso condizionale che richiedono app client approvate sono noti anche come [criteri di accesso condizionale basato su app](app-based-conditional-access.md). Per un elenco di app client approvate supportate, vedere [Requisito per le app client approvate](technical-reference.md#approved-client-app-requirement).

### <a name="app-protection-policy-preview"></a>Criteri di protezione delle App (anteprima)

Poiché i dipendenti di un'azienda usano i dispositivi mobili per attività sia lavorative che personali, può essere utile avere la possibilità di proteggere i dati aziendali accessibili da dispositivi anche nel caso in cui vengano gestiti da altri utenti.
È possibile usare i [criteri di protezione delle app di Intune](https://docs.microsoft.com/intune/app-protection-policy) per proteggere i dati aziendali indipendentemente dalla soluzione di gestione dei dispositivi mobili (MDM).

Con i criteri di protezione delle app, è possibile limitare l'accesso alle applicazioni client che hanno segnalato ad Azure AD è invece disponibile dopo aver ricevuto [criteri di protezione app di Intune](https://docs.microsoft.com/intune/app-protection-policy). Ad esempio, è possibile limitare l'accesso a Exchange Online per l'app Outlook con criteri di protezione app di Intune. Un criterio di accesso condizionale che richiede criteri di protezione delle app è noto anche come [criteri di accesso condizionale basato su protezione delle app](app-protection-based-conditional-access.md). 

Il dispositivo deve essere registrato in Azure AD prima di un'applicazione può essere contrassegnata come protetto da criteri.

Per un elenco dei criteri supportati di App client protetti, vedere [requisito dei criteri di protezione app](technical-reference.md#app-protection-policy-requirement).


### <a name="terms-of-use"></a>Condizioni per l'utilizzo

È possibile richiedere a un utente nel tenant di accettare le condizioni per l'utilizzo prima di concedergli il permesso di accedere a una risorsa. Gli amministratori possono configurare e personalizzare le condizioni per l'utilizzo caricando un documento PDF. Se un utente rientra nell'ambito di questo controllo, gli viene concesso l'accesso a un'applicazione solo se ha accettato le condizioni per l'utilizzo.

### <a name="custom-controls-preview"></a>Controlli personalizzati (anteprima)

È possibile aggiungere controlli personalizzati nell'accesso condizionale che reindirizza gli utenti a un servizio compatibile per soddisfare altri requisiti esterni ad Azure Active Directory. In questo modo è possibile usare determinati provider di verifica e l'autenticazione a più fattori esterni per applicare i requisiti di accesso condizionale. Per soddisfare questo controllo, il browser dell'utente viene reindirizzato al servizio esterno, esegue le eventuali attività di autenticazione e convalida richieste e viene quindi reindirizzato ad Azure Active Directory. Se l'utente è stato correttamente autenticato o convalidato, può proseguire nel flusso di accesso condizionale. 

## <a name="custom-controls"></a>Controlli personalizzati

I controlli personalizzati sono una funzionalità dell'edizione Azure Active Directory Premium P1. Quando si usano i controlli personalizzati, gli utenti vengono reindirizzati a un servizio compatibile per soddisfare altri requisiti esterni ad Azure Active Directory. Per soddisfare questo controllo, il browser dell'utente viene reindirizzato al servizio esterno, esegue le eventuali attività di autenticazione e convalida richieste e viene quindi reindirizzato ad Azure Active Directory. Azure Active Directory verifica la risposta e, se l'utente è stato correttamente autenticato o convalidato, può proseguire nel flusso di accesso condizionale.

Questi controlli consentono l'utilizzo di determinati servizi esterni o personalizzati come controlli di accesso condizionale e, in genere, estendono le funzionalità dell'accesso condizionale.

Di seguito sono elencati i provider che attualmente offrono un servizio compatibile:

- [Duo Security](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [Ping Identity](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- RSA
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Per altre informazioni su questi servizi, contattare direttamente i provider.

### <a name="creating-custom-controls"></a>Creazione di controlli personalizzati

Per creare un controllo personalizzato, è opportuno prima contattare il provider a cui ci si vuole rivolgere. Ogni provider diverso da Microsoft presenta specifici processi e requisiti da soddisfare per poter iscriversi a un servizio, sottoscriverlo o entrare a far parte di esso o per specificare che si intende integrarlo con l'accesso condizionale. A questo punto, il provider fornirà un blocco di dati in formato JSON. Questi dati consentono al provider di interagire con l'accesso condizionale per il tenant e permettono di creare il nuovo controllo e di definire le modalità con cui l'accesso condizionale specifica se gli utenti hanno eseguito correttamente la verifica con il provider.

Impossibile utilizzare i controlli personalizzati con automazione di Identity Protection che richiedono l'autenticazione a più fattori o per elevare i privilegi dei ruoli in Privileged Identity Manager (PIM).

Copiare i dati JSON e incollarli nella casella di testo corrispondente. Non apportare modifiche ai dati JSON a meno di non aver compreso in modo esplicito la modifica che si sta apportando. L'introduzione di una modifica potrebbe interrompere la connessione tra il provider e Microsoft e potenzialmente bloccare gli utenti fuori dai rispettivi account.

L'opzione che consente di creare un controllo personalizzato si trova nella sezione **Gestisci** della pagina **Accesso condizionale**.

![Controllo](./media/controls/82.png)

Facendo clic su **Nuovo controllo personalizzato** si apre un pannello con una casella di testo per i dati JSON relativi al controllo.  

![Controllo](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>Eliminazione di controlli personalizzati

Per eliminare un controllo personalizzato, è necessario prima accertarsi che non sia in uso in nessuno dei criteri di accesso condizionale. Al termine della procedura:

1. Passare all'elenco di controlli personalizzati

2. Fare clic su ...  

3. Selezionare **Elimina**.

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

- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere il [Procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md).
