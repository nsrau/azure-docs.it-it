---
title: Configurazione dell'accesso condizionale locale con il servizio Registrazione dispositivo di Azure Active Directory | Microsoft Docs
description: Guida dettagliata all'abilitazione dell'accesso condizionale alle applicazioni locali usando Active Directory Federation Services (AD FS) in Windows Server 2012 R2.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 6ae9df8b-31fe-4d72-9181-cf50cfebbf05
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 9c4b8364f88548cfc4595261302248cc2840c233
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="setting-up-on-premises-conditional-access-by-using-azure-active-directory-device-registration"></a>Configurazione dell'accesso condizionale locale usando il servizio Registrazione dispositivo di Azure Active Directory
Quando si chiede agli utenti di aggiungere i dispositivi personali all'area di lavoro con il servizio Registrazione dispositivo di Azure Active Directory (Azure AD), i dispositivi possono essere contrassegnati come noti all'organizzazione. Di seguito è riportata una guida dettagliata all'abilitazione dell'accesso condizionale alle applicazioni locali con Active Directory Federation Services (AD FS) in Windows Server 2012 R2.

> [!NOTE]
> Quando si usano dispositivi registrati nei criteri di accesso condizionale del servizio Registrazione dispositivo di Azure Active Directory, è necessaria una licenza di Office 365 o una licenza di Azure AD Premium. Sono inclusi i criteri applicati da AD FS nelle risorse locali.
> 
> Per altre informazioni sugli scenari di accesso condizionale per le risorse locali, vedere [Aggiunta all'area di lavoro da qualsiasi dispositivo per SSO e autenticazione a due fattori trasparente per tutte le applicazioni aziendali](https://technet.microsoft.com/library/dn280945.aspx).

Queste funzionalità sono disponibili per i clienti che acquistano una licenza di Azure Active Directory Premium.

## <a name="supported-devices"></a>Dispositivi supportati
* Dispositivi Windows 7 aggiunti a un dominio
* Dispositivi Windows 8.1 personali e aggiunti a un dominio
* iOS 6 e versioni successive per il browser Safari
* Android 4.0 o versioni successive, telefoni Samsung GS3 o successivi, tablet Samsung Galaxy Note 2 o successivi

## <a name="scenario-prerequisites"></a>Prerequisiti dello scenario
* Sottoscrizione di Office 365 o Azure Active Directory Premium
* Tenant di Azure Active Directory
* Windows Server Active Directory (Windows Server 2008 o versioni successive)
* Schema aggiornato in Windows Server 2012 R2
* Licenza di Azure Active Directory Premium
* Windows Server 2012 R2 Federation Services configurato per l'accesso SSO ad Azure AD
* Proxy applicazione Web di Windows Server 2012 R2 
* Microsoft Azure Active Directory Connect (Azure AD Connect) [(Download di Azure AD Connect)](http://www.microsoft.com/en-us/download/details.aspx?id=47594)
* Dominio verificato

## <a name="known-issues-in-this-release"></a>Problemi noti in questa versione
* I criteri di accesso condizionale basati su dispositivo richiedono la funzionalità di writeback degli oggetti dispositivo in Active Directory da Azure Active Directory. L'esecuzione del writeback degli oggetti dispositivo in Active Directory può richiedere fino a tre ore.
* I dispositivi iOS 7 richiedono sempre di selezionare un certificato durante l'autenticazione dei certificati client.
* Alcune versioni di iOS 8 precedenti a iOS 8.3 non funzionano.

## <a name="scenario-assumptions"></a>Presupposti dello scenario
Questo scenario presuppone l'esistenza di un ambiente ibrido, costituito da un tenant di Azure AD e un'istanza di Active Directory locale. Questi tenant devono essere connessi con Azure AD Connect, con un dominio verificato e con AD FS per SSO. Usare il seguente elenco di controllo per configurare l'ambiente in base ai requisiti.

## <a name="checklist-prerequisites-for-conditional-access-scenario"></a>Elenco di controllo: prerequisiti per lo scenario di accesso condizionale
Connettere il tenant di Azure AD all'istanza di Active Directory locale.

## <a name="configure-azure-active-directory-device-registration-service"></a>Configurare il servizio Registrazione dispositivo di Azure Active Directory
Usare questa guida per distribuire e configurare il servizio Registrazione dispositivo di Azure Active Directory per l'organizzazione.

Nella guida si presuppone che Windows Server Active Directory sia stato configurato e che sia stata effettuata la sottoscrizione a Microsoft Azure Active Directory. Vedere i prerequisiti descritti in precedenza.

Per distribuire il servizio Registrazione dispositivo di Azure Active Directory con il tenant di Azure Active Directory, completare nell'ordine indicato le attività riportate nell'elenco di controllo seguente. Quando un collegamento a un riferimento porta a un argomento concettuale, tornare a questo elenco di controllo per poter procedere con le attività restanti. Alcune attività includono un passaggio di convalida dello scenario, che consente di verificare se il passaggio è stato eseguito correttamente.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>Parte 1: Abilitare Registrazione dispositivo di Azure Active Directory
Seguire i passaggi dell'elenco di controllo per abilitare e configurare il servizio Registrazione dispositivo di Azure Active Directory.

| Attività | Riferimenti | 
| --- | --- |
| Abilitare Registrazione dispositivo nel tenant di Azure Active Directory per consentire l'aggiunta dei dispositivi all'area di lavoro. Per impostazione predefinita, la funzionalità Azure Multi-Factor Authentication non è abilitata per il servizio. Tuttavia, è consigliabile usare l'autenticazione a più fattori quando si registra un dispositivo. Prima di abilitare Multi-Factor Authentication nel servizio Registrazione dispositivo di Active Directory, verificare che AD FS sia configurato per un provider di autenticazione a più fattori. |[Abilitare Registrazione dispositivo di Azure Active Directory](active-directory-device-registration-get-started.md)| 
|I dispositivi individuano il servizio Registrazione dispositivo di Azure Active Directory cercando record DNS noti. Configurare il DNS della società in modo che i dispositivi possano rilevare il servizio Registrazione dispositivo di Azure Active Directory. |[Configurare l'individuazione di Registrazione dispositivo di Azure Active Directory.](active-directory-device-registration-get-started.md)| 


## <a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>Parte 2: Distribuire e configurare Active Directory Federation Services con Windows Server 2012 R2 e configurare una relazione federativa con Azure AD

| Attività | Riferimenti |
| --- | --- |
| Distribuire Active Directory Domain Services con le estensioni dello schema di Windows Server 2012 R2. Non è necessario aggiornare alcun controller di dominio a Windows Server 2012 R2. L'unico requisito è l'aggiornamento dello schema. |[Aggiornare lo schema di Active Directory Domain Services](#upgrade-your-active-directory-domain-services-schema) |
| I dispositivi individuano il servizio Registrazione dispositivo di Azure Active Directory cercando record DNS noti. Configurare il DNS della società in modo che i dispositivi possano rilevare il servizio Registrazione dispositivo di Azure Active Directory. |[Preparare Active Directory per supportare i dispositivi](#prepare-your-active-directory-to-support-devices) |

## <a name="part-3-enable-device-writeback-in-azure-ad"></a>Parte 3: Abilitare il writeback dei dispositivi in Azure AD
| Attività | Riferimenti |
| --- | --- |
| Completare la parte due di "Abilitazione del writeback dei dispositivi in Azure AD Connect". Al termine, tornare a questa guida. |[Abilitazione del writeback dei dispositivi in Azure AD Connect](#upgrade-your-active-directory-domain-services-schema) |

## <a name="optional-part-4-enable-multi-factor-authentication"></a>[Facoltativo] Parte 4: Abilitazione di Multi-Factor Authentication
È consigliabile configurare una delle diverse opzioni per Multi-Factor Authentication. Se si vuole rendere obbligatoria l'autenticazione a più fattori, vedere [Scegliere la soluzione Azure Multi-Factor Authentication più adatta alle proprie esigenze](../multi-factor-authentication/multi-factor-authentication-get-started.md). È inclusa una descrizione di ogni soluzione con collegamenti che consentono di configurare la soluzione selezionata.

## <a name="part-5-verification"></a>Parte 5: Verifica
La distribuzione è stata completata e ora è possibile provare alcuni scenari. Usare i collegamenti riportati di seguito per provare il servizio e acquisire familiarità con le relative funzionalità.

| Attività | Riferimenti |
| --- | --- |
| Aggiungere alcuni dispositivi all'area di lavoro usando il servizio Registrazione dispositivo di Azure Active Directory. È possibile aggiungere dispositivi iOS, Windows e Android. |[Aggiungere dispositivi all'area di lavoro con il servizio Registrazione dispositivo di Azure Active Directory](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| È possibile visualizzare e abilitare o disabilitare i dispositivi registrati usando il portale dell'amministratore. In questa attività si visualizzano alcuni dispositivi registrati usando il portale dell'amministratore. |[Panoramica del servizio Registrazione dispositivo di Azure Active Directory](active-directory-device-registration-get-started.md) |
| Verificare che venga eseguito il writeback degli oggetti dispositivo da Azure Active Directory a Windows Server Active Directory. |[Verificare che venga eseguito il writeback dei dispositivi registrati in Active Directory](#verify-registered-devices-are-written-back-to-active-directory) |
| Ora che gli utenti possono registrare i propri dispositivi, è possibile creare criteri di accesso alle applicazioni in AD FS che consentano l'accesso solo a dispositivi registrati. In questa attività viene creata una regola di accesso alle applicazioni e un messaggio di accesso negato personalizzato. |[Creare un criterio di accesso alle applicazioni e un messaggio di accesso negato personalizzato](#create-an-application-access-policy-and-custom-access-denied-message) |

## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Integrare Azure Active Directory con l'istanza di Active Directory locale

**Vedere:**

- [Integrare le directory locali con Azure Active Directory](./connect/active-directory-aadconnect.md) : per esaminare informazioni concettuali.

- [Installazione personalizzata di Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md) : per le istruzioni di installazione.


## <a name="upgrade-your-active-directory-domain-services-schema"></a>Aggiornare lo schema di Servizi di dominio Active Directory
> [!NOTE]
> Dopo aver aggiornato lo schema di Active Directory, il processo non può essere annullato. È quindi consigliabile eseguire prima l'aggiornamento in un ambiente di test.
> 

1. Accedere al controller di dominio usando un account con diritti sia di amministratore dell'organizzazione sia di amministratore dello schema.
2. Copiare la directory e le sottodirectory **[media]\support\adprep** in uno dei controller di dominio di Active Directory (dove **[media]** è il percorso al supporto di installazione di Windows Server 2012 R2).
4. A un prompt dei comandi passare alla directory **adprep** ed eseguire **adprep.exe /forestprep**. Seguire le istruzioni visualizzate per completare l'aggiornamento dello schema.

## <a name="prepare-your-active-directory-to-support-devices"></a>Preparare Active Directory per supportare i dispositivi
> [!NOTE]
> Questa è un'operazione da eseguire una sola volta per preparare la foresta Active Directory per supportare i dispositivi. Per completare questa procedura è necessario accedere con autorizzazioni di amministratore dell'organizzazione e la foresta Active Directory deve avere lo schema di Windows Server 2012 R2.
> 


### <a name="prepare-your-active-directory-forest"></a>Preparare la foresta Active Directory
1. Nel server federativo aprire una finestra di comando di Windows PowerShell e digitare **Initialize-ADDeviceRegistration**. 
2. Quando viene richiesto di specificare **ServiceAccountName**, immettere il nome dell'account del servizio selezionato come account del servizio per AD FS. Se si tratta di un account del servizio gestito del gruppo, immetterlo nel formato **dominio\nomeaccount$**. Per un account di dominio, usare il formato **dominio\nomeaccount**.

### <a name="enable-device-authentication-in-ad-fs"></a>Abilitare l'autenticazione dispositivo in AD FS
1. Nel server federativo aprire la console di gestione di AD FS e passare ad **AD FS** > **Criteri di autenticazione**.
2. Selezionare **Modifica autenticazione primaria globale** nel riquadro **Azioni**.
3. Selezionare **Abilita autenticazione dispositivo** e quindi **OK**.
4. Per impostazione predefinita, AD FS rimuove periodicamente da Active Directory i dispositivi inutilizzati. Disabilitare questa attività quando si usa il servizio Registrazione dispositivo di Azure Active Directory, in modo che i dispositivi possano essere gestiti in Azure.

### <a name="disable-unused-device-cleanup"></a>Disabilitare la pulizia dei dispositivi inutilizzati
Nel server federativo aprire una finestra di comando di Windows PowerShell e digitare **Set-AdfsDeviceRegistration -MaximumInactiveDays 0**.

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>Preparare Azure AD Connect per il writeback dei dispositivi
Completare la parte 1: preparare Azure AD Connect.

## <a name="join-devices-to-your-workplace-by-using-azure-active-directory-device-registration-service"></a>Aggiungere dispositivi all'area di lavoro con il servizio Registrazione dispositivo di Azure Active Directory

### <a name="join-an-ios-device-by-using-azure-active-directory-device-registration"></a>Aggiungere un dispositivo iOS con Registrazione dispositivo di Azure Active Directory
Il servizio Registrazione dispositivo di Azure Active Directory usa il processo di registrazione del profilo OTA (Over-the-Air) per i dispositivi iOS. Questo processo inizia quando l'utente si connette per l'URL di registrazione del profilo con Safari. Il formato dell'URL è il seguente:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

In questo caso `yourdomainname` è il nome di dominio configurato con Azure Active Directory. Ad esempio, se il nome di dominio è contoso.com, l'URL è:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

È possibile comunicare questo URL agli utenti in diversi modi. Un metodo consigliato, ad esempio, consiste nel pubblicare l'URL in un messaggio personalizzato di accesso all'applicazione negato in AD FS. Queste informazioni sono descritte nella sezione seguente: [Creare un criterio di accesso alle applicazioni e un messaggio di accesso negato personalizzato](#create-an-application-access-policy-and-custom-access-denied-message).

### <a name="join-a-windows-81-device-by-using-azure-active-directory-device-registration"></a>Aggiungere un dispositivo Windows 8.1 con Registrazione dispositivo di Azure Active Directory
1. Nel dispositivo Windows 8.1 selezionare **Impostazioni PC** > **Rete** > **Area di lavoro**.
2. Immettere il proprio nome utente in formato UPN, ad esempio **dan@contoso.com**.
3. Selezionare **Aggiungi**.
4. Quando richiesto, accedere con le proprie credenziali. Il dispositivo ora è aggiunto.

### <a name="join-a-windows-7-device-by-using-azure-active-directory-device-registration"></a>Aggiungere un dispositivo Windows 7 con Registrazione dispositivo di Azure Active Directory
Per registrare dispositivi Windows 7 aggiunti a un dominio, è necessario distribuire il [pacchetto software di registrazione dispositivo](https://www.microsoft.com/download/details.aspx?id=53554).

Per istruzioni su come usare il pacchetto, vedere [Pacchetti di Windows Installer per i computer non Windows 10](device-management-hybrid-azuread-joined-devices-setup.md#windows-installer-packages-for-non-windows-10-computers).

## <a name="verify-that-registered-devices-are-written-back-to-active-directory"></a>Verificare che venga eseguito il writeback dei dispositivi registrati in Active Directory
È possibile verificare che sia stato eseguito il writeback degli oggetti dispositivo in Active Directory usando LDP.exe o ADSI Edit. Entrambi sono disponibili con gli strumenti dell'amministratore di Active Directory.

Per impostazione predefinita, gli oggetti dispositivo di cui viene eseguito il writeback da Azure Active Directory vengono inseriti nello stesso dominio della farm AD FS.

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>Creare un criterio di accesso alle applicazioni e un messaggio di accesso negato personalizzato
Considerare lo scenario seguente: si crea un trust della relying party per l'applicazione in AD FS e si configura una regola di autorizzazione rilascio che consente solo dispositivi registrati. Ora solo i dispositivi registrati possono accedere all'applicazione. 

Per semplificare l'accesso degli utenti all'applicazione, configurare un messaggio di accesso negato personalizzato che include istruzioni su come aggiungere il dispositivo dell'utente. A questo punto per gli utenti è più facile registrare i propri dispositivi per accedere a un'applicazione.

Nei passaggi seguenti viene illustrato come implementare questo scenario.

> [!NOTE]
> Questa sezione presuppone che sia già stato configurato un trust della relying party per l'applicazione in AD FS.
> 

1. Aprire lo strumento MMC di AD FS e selezionare **AD FS** > **Relazioni di trust** > **Attendibilità componente**.
2. Individuare l'applicazione a cui applicare questa nuova regola di accesso. Fare clic con il pulsante destro del mouse sull'applicazione e selezionare **Modifica regole attestazione**.
3. Selezionare la scheda **Regole di autorizzazione rilascio** e quindi selezionare **Aggiungi regola**.
4. Nell'elenco a discesa **Modello di regola attestazione** selezionare **Consentire o negare l'accesso agli utenti in base a un'attestazione in ingresso**. Quindi selezionare **Avanti**.
5. Nel campo **Nome regola attestazione** digitare **Consenti accesso dai dispositivi registrati**.
6. Nell'elenco a discesa **Tipo di attestazione in ingresso** selezionare **È un utente registrato**.
7. Digitare **true** nel campo **Valore attestazione in ingresso**.
8. Selezionare il pulsante di opzione **Consenti accesso agli utenti con questa attestazione in ingresso** .
9. Selezionare **Fine** e quindi **Applica**.
10. Rimuovere eventuali regole più permissive di quella creata. Ad esempio, rimuovere la regola predefinita **Consenti accesso a tutti gli utenti** .

L'applicazione ora è configurata per consentire l'accesso solo quando l'utente opera da un dispositivo registrato e aggiunto all'area di lavoro. Per i criteri di accesso più avanzati, vedere l'articolo relativo alla [gestione dei rischi con autenticazione a più fattori aggiuntiva per le applicazioni sensibili](https://technet.microsoft.com/library/dn280949.aspx).

Configurare quindi un messaggio di errore personalizzato per l'applicazione. Tale messaggio indica agli utenti che devono aggiungere il proprio dispositivo all'area di lavoro per poter accedere all'applicazione. È possibile creare un messaggio personalizzato di accesso all'applicazione negato usando codice HTML personalizzato e PowerShell.

Nel server federativo aprire una finestra di comando di PowerShell e digitare il comando seguente. Sostituire alcune parti del comando con gli elementi specifici del proprio sistema:

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
Prima di poter accedere all'applicazione, è necessario registrare il dispositivo.

**Se si usa un dispositivo iOS, selezionare questo collegamento per accedere al dispositivo**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Aggiungere questo dispositivo iOS alla propria area di lavoro.

Se si usa un dispositivo Windows 8.1, è possibile aggiungere il dispositivo selezionando **Impostazioni PC** > **Rete** > **Area di lavoro**.

Nei comandi precedenti **relying party trust name** è il nome dell'oggetto trust della relying party dell'applicazione in AD FS.
E **yourdomain.com** è il nome di dominio configurato con Azure Active Directory, ad esempio contoso.com.
Assicurarsi di rimuovere le eventuali interruzioni di riga presenti nel contenuto HTML passato al cmdlet **Set-AdfsRelyingPartyWebContent** .

A questo punto, quando gli utenti accedono all'applicazione da un dispositivo non registrato nel servizio Registrazione dispositivo di Azure Active Directory, verrà visualizzato un errore.

