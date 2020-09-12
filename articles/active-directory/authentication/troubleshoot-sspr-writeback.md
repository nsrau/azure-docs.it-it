---
title: Risolvere i problemi di writeback della reimpostazione della password self-service-Azure Active Directory
description: Informazioni su come risolvere i problemi comuni e i passaggi di risoluzione per il writeback della reimpostazione della password self-service in Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50e202d26574c0fc8adfeb7f73eb150ebb1781af
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664122"
---
# <a name="troubleshoot-self-service-password-reset-writeback-in-azure-active-directory"></a>Risolvere i problemi di writeback della reimpostazione della password self-service in Azure Active Directory

Azure Active Directory (Azure AD) la reimpostazione della password self-service (SSPR) consente agli utenti di reimpostare le password nel cloud. Il writeback delle password è una funzione abilitata con [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) che consente di riscrivere le modifiche alle password nel cloud in una directory locale esistente in tempo reale.

In caso di problemi con il writeback del SSPR, potrebbero essere utili i seguenti passaggi per la risoluzione dei problemi e gli errori comuni. Se non si riesce a trovare la risposta al problema, [i team di supporto sono sempre disponibili](#contact-microsoft-support) per assistenza.

## <a name="troubleshoot-connectivity"></a>Risolvere i problemi di connettività

In caso di problemi con il writeback delle password per Azure AD Connect, esaminare i passaggi seguenti per risolvere il problema. Per ripristinare il servizio, si consiglia di seguire questi passaggi nell'ordine indicato:

* [Verificare la connettività della rete](#confirm-network-connectivity)
* [Riavviare il servizio di sincronizzazione Azure AD Connect](#restart-the-azure-ad-connect-sync-service)
* [Disabilitare e riabilitare la funzionalità di writeback delle password](#disable-and-re-enable-the-password-writeback-feature)
* [Installare la versione più recente di Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Risolvere un problema relativo al writeback delle password](#common-password-writeback-errors)

### <a name="confirm-network-connectivity"></a>Verificare la connettività della rete

Il punto di errore più comune è che le porte firewall o proxy o i timeout di inattività non sono configurati correttamente.

Per Azure AD Connect versione *1.1.443.0* e successive, è necessario l'accesso *https in uscita* agli indirizzi seguenti:

* *\*. passwordreset.microsoftonline.com*
* *\*. servicebus.windows.net*

Se è necessaria una maggiore granularità, vedere l' [elenco di Microsoft Azure intervalli IP del Data Center](https://www.microsoft.com/download/details.aspx?id=41653). Questo elenco viene aggiornato ogni mercoledì ed entrerà in vigore il lunedì successivo.

Per ulteriori informazioni, vedere i [prerequisiti di connettività per Azure ad Connect](../hybrid/how-to-connect-install-prerequisites.md).

### <a name="restart-the-azure-ad-connect-sync-service"></a>Riavviare il servizio di sincronizzazione Azure AD Connect

Per risolvere i problemi di connettività o altri problemi temporanei del servizio, completare i passaggi seguenti per riavviare il servizio di sincronizzazione Azure AD Connect:

1. In qualità di amministratore del server che esegue Azure AD Connect, selezionare **Avvia**.
1. Immettere *services.msc* nel campo di ricerca e premere **INVIO**.
1. Cercare la voce *Microsoft Azure AD Sync*.
1. Fare clic con il pulsante destro del mouse sulla voce del servizio, scegliere **Riavvia**e attendere il completamento dell'operazione.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/service-restart.png" alt-text="Riavviare il servizio Azure AD Sync con l'interfaccia utente grafica" border="false":::

Questi passaggi ristabiliscono la connessione con Azure AD e devono risolvere i problemi di connettività.

Se il riavvio del servizio Azure AD Connect Sync non risolve il problema, provare a disabilitare e riabilitare la funzionalità di writeback delle password nella sezione successiva.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Disabilitare e riabilitare la funzionalità di writeback delle password

Per continuare a risolvere i problemi, attenersi alla procedura seguente per disabilitare e riabilitare la funzionalità di writeback delle password:

1. In qualità di amministratore del server che esegue Azure AD Connect, aprire la **Configurazione guidata Azure ad Connect**.
1. In **Connessione ad Azure AD** immettere le credenziali di amministratore globale di Azure AD.
1. In **Connetti a servizi di dominio Active Directory**immettere le credenziali di amministratore Active Directory Domain Services locali.
1. In **Identificazione univoca per gli utenti** fare clic su **Avanti**.
1. In **Funzionalità facoltative** deselezionare la casella di controllo **Writeback password**.
1. Fare clic su **Avanti** nelle pagine rimanenti senza apportare alcuna modifica fino a quando non viene visualizzata la pagina **Pronto per la configurazione**.
1. Verificare che nella **pagina pronto per la configurazione** venga visualizzata l'opzione *writeback password* come *disabilitata*. Selezionare il pulsante verde **Configura** per eseguire il commit delle modifiche.
1. In **Operazione completata** deselezionare l'opzione **Sincronizza adesso** e quindi fare clic su **Fine** per chiudere la procedura guidata.
1. Riaprire la **Configurazione guidata Azure ad Connect**.
1. Ripetere i passaggi 2-8, questa volta selezionando l'opzione *writeback password* nella pagina **funzionalità facoltative** per riabilitare il servizio.

Questi passaggi ristabiliscono la connessione con Azure AD e devono risolvere i problemi di connettività.

Se la disabilitazione e la riabilitazione della funzionalità di writeback delle password non risolve il problema, reinstallare Azure AD Connect nella sezione successiva.

### <a name="install-the-latest-azure-ad-connect-release"></a>Installare la versione più recente di Azure AD Connect

La reinstallazione di Azure AD Connect può risolvere i problemi di connettività e configurazione tra Azure AD e l'ambiente Active Directory Domain Services locale. Si consiglia di eseguire questo passaggio solo dopo aver provato i passaggi precedenti per verificare e risolvere i problemi di connettività.

> [!WARNING]
> Se sono state personalizzate le regole di sincronizzazione predefinite, *eseguirne il backup prima di procedere con l'aggiornamento, quindi ridistribuirle manualmente al termine* dell'operazione.

1. Scaricare la versione più recente di Azure AD Connect dall'[Area download Microsoft](https://go.microsoft.com/fwlink/?LinkId=615771).
1. Come è già stato installato Azure AD Connect, eseguire un aggiornamento sul posto per aggiornare l'installazione di Azure AD Connect alla versione più recente.

    Eseguire il pacchetto scaricato e seguire le istruzioni visualizzate per aggiornare Azure AD Connect.

Questa procedura deve ristabilire la connessione con Azure AD e risolvere i problemi di connettività.

Se l'installazione della versione più recente di Azure AD Connect Server non risolve il problema, provare a disabilitare e quindi riabilitare il writeback delle password come passaggio finale dopo l'installazione della versione più recente.

## <a name="verify-that-azure-ad-connect-has-the-required-permissions"></a>Verificare che Azure AD Connect disponga delle autorizzazioni necessarie

Azure AD Connect richiede l'autorizzazione **Reimposta password** di servizi di dominio Active Directory per eseguire il writeback delle password. Per verificare se Azure AD Connect dispone dell'autorizzazione necessaria per un determinato account utente di Active Directory Domain Services locale, utilizzare la funzionalità di **autorizzazione valida di Windows** :

1. Accedere al server Azure AD Connect e avviare **Synchronization Service Manager** selezionando **Start** > **Synchronization Service**.
1. Nella scheda **Connettori** selezionare il connettore **Active Directory Domain Services** locale e quindi selezionare **Proprietà**.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/synchronization-service-manager.png" alt-text="Modifica delle proprietà in Synchronization Service Manager" border="false":::
  
1. Nella finestra popup selezionare **Connetti a foresta Active Directory** e annotare la proprietà **Nome utente**. Questa proprietà corrisponde all'account di Active Directory Domain Services usato da Azure AD Connect per eseguire la sincronizzazione della directory.

    Per consentire ad Azure AD Connect di eseguire il writeback delle password l'account di Active Directory Domain Services deve avere l'autorizzazione di reimpostazione della password. Si controllano le autorizzazioni per questo account utente nei passaggi seguenti.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/synchronization-service-manager-properties.png" alt-text="Ricerca dell'account utente Active Directory per il servizio di sincronizzazione" border="false":::
  
1. Accedere a un controller di dominio locale e avviare l'applicazione **Utenti e computer di Active Directory**.
1. Selezionare **Visualizza** e verificare che l'opzione **Funzionalità avanzate** sia abilitata.  

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-advanced-features.png" alt-text="Funzionalità avanzate in Utenti e computer di Active Directory" border="false":::
  
1. Cercare l'account utente di servizi di dominio Active Directory che si desidera verificare. Fare clic con il pulsante destro del mouse sull'account e scegliere **Proprietà**.  
1. Nella finestra popup passare alla scheda **Sicurezza** e selezionare **Avanzate**.  
1. Nella finestra popup **Advanced Security Settings for Administrator** (Impostazioni di sicurezza avanzate per l'amministratore) passare alla scheda **Accesso valido**.
1. Scegliere **Seleziona un utente**, selezionare l'account Active Directory Domain Services usato da Azure ad Connect, quindi selezionare **Visualizza accesso valido**.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-effective-access.png" alt-text="Scheda Accesso valido con account di sincronizzazione" border="false":::
  
1. Scorrere verso il basso e cercare **Reimposta password**. Se la voce è selezionata, significa che l'account di Active Directory Domain Services ha l'autorizzazione per reimpostare la password dell'account utente di Active Directory selezionato.  

    :::image type="content" source="./media/troubleshoot-sspr-writeback/check-permissions.png" alt-text="Verificare che l'account di sincronizzazione abbia l'autorizzazione Reimposta password" border="false":::

## <a name="common-password-writeback-errors"></a>Errori comuni di writeback delle password

Con il writeback delle password possono verificarsi i seguenti problemi più specifici. Se si verifica uno di questi errori, esaminare la soluzione proposta e verificare se il writeback delle password funziona correttamente.

| Errore | Soluzione |
| --- | --- |
| Il servizio di reimpostazione della password non viene avviato in locale. L'errore 6800 viene visualizzato nel registro eventi dell'applicazione del computer Azure AD Connect. <br> <br> Dopo l'onboarding, gli utenti federati, con autenticazione pass-through o con sincronizzazione dell'hash delle password non possono reimpostare le password. | Quando il writeback delle password è abilitato, il motore di sincronizzazione chiama la libreria di writeback per eseguire la configurazione, ovvero l'onboarding, comunicando con il servizio di onboarding cloud. Gli eventuali errori che si verificano durante l'onboarding o durante l'avvio dell'endpoint Windows Communication Foundation (WCF) per il writeback delle password si riflettono nel log eventi del computer che esegue Azure AD Connect. <br> <br> Se è stato configurato il writeback, durante il riavvio del servizio Azure AD Sync (ADSync) viene avviato l'endpoint WCF. Tuttavia, se l'avvio dell'endpoint ha esito negativo, si registra l'evento 6800 e si consente l'avvio del servizio di sincronizzazione. La presenza di questo evento indica che l'endpoint di writeback della password non è stato avviato. I dettagli del log per questo evento 6800 insieme alle voci del log eventi generate dal componente PasswordResetService indicano i motivi per cui non è possibile avviare l'endpoint. Esaminare questi errori nel registro eventi e provare a riavviare il Azure AD Connect se il writeback delle password non funziona ancora. Se il problema persiste, provare a disabilitare e quindi riabilitare il writeback delle password.
| Quando un utente prova a reimpostare una password o a sbloccare un account con il writeback delle password abilitato, l'operazione non riesce. <br> <br> Viene inoltre visualizzato un evento nel registro eventi Azure AD Connect che contiene: "il motore di sincronizzazione ha restituito un errore HR = 800700CE, Message = il nome file o l'estensione è troppo lungo" dopo l'operazione di sblocco. | Trovare l'account Active Directory per Azure AD Connect e reimpostare la password in modo che non contenga più di 256 caratteri. Aprire quindi il **servizio di sincronizzazione** dal menu **Start** . Passare a **Connettori** e trovare **Active Directory Connector**. Selezionare il connettore e quindi **Proprietà**. Passare alla pagina **Credenziali** e immettere la nuova password. Fare clic su **OK** per chiudere la pagina. |
| Nell'ultimo passaggio del processo di installazione di Azure AD Connect viene visualizzato un errore che indica che non è stato possibile configurare il writeback delle password. <br> <br> Il registro eventi dell'applicazione Azure AD Connect contiene l'errore 32009 con il testo "errore durante il recupero del token di autenticazione". | Questo errore si verifica nei due casi seguenti: <br><ul><li>È stata specificata una password non corretta per l'account amministratore globale fornito all'inizio del processo di installazione di Azure AD Connect.</li><li>Si è tentato di usare un utente federato per l'account amministratore globale specificato all'inizio del processo di installazione di Azure AD Connect.</li></ul> Per risolvere il problema, assicurarsi che non si stia usando un account federato per l'amministratore globale specificato all'inizio del processo di installazione e che la password specificata sia corretta. |
| Il log eventi del computer che esegue Azure AD Connect contiene l'errore 32002 generato eseguendo PasswordResetService. <br> <br> Errore di lettura: "errore durante la connessione a ServiceBus. Il provider di token non è stato in grado di fornire un token di sicurezza. " | L'ambiente locale non è in grado di connettersi all'endpoint del bus di servizio di Azure nel cloud. Questo errore è in genere causato da una regola del firewall che blocca la connessione in uscita a una porta o a un indirizzo Web specifico. Per altre informazioni, vedere i [prerequisiti di connettività](../hybrid/how-to-connect-install-prerequisites.md). Dopo avere aggiornato queste regole, riavviare il Azure AD Connect Server e il writeback delle password dovrebbe iniziare a funzionare di nuovo. |
| Dopo un certo periodo di tempo, gli utenti federati, con autenticazione pass-through o con sincronizzazione dell'hash delle password non possono reimpostare le password. | In rari casi è possibile che il servizio di writeback delle password non venga riavviato quando si riavvia Azure AD Connect. In questi casi, controllare innanzitutto se il writeback delle password è abilitato in locale. È possibile controllare usando la procedura guidata Azure AD Connect o PowerShell. Se la funzionalità appare abilitata, provare ad abilitare o disabilitare di nuovo la funzionalità. Se il passaggio per la risoluzione dei problemi non funziona, provare a disinstallare e reinstallare completamente Azure AD Connect. |
| Gli utenti federati, con autenticazione pass-through o con sincronizzazione dell'hash delle password che cercano di reimpostare le password visualizzano un errore dopo l'invio della password. L'errore indica che si è verificato un problema del servizio. <br ><br> Oltre a questo problema, durante le operazioni di reimpostazione della password è possibile che venga visualizzato un errore di accesso negato per l'agente di gestione nei log eventi locali. | Se nel log eventi sono presenti errori di questo tipo, verificare che l'account Active Directory Management Agent (ADMA) specificato nella procedura guidata al momento della configurazione abbia le autorizzazioni necessarie per il writeback delle password. <br> <br> Dopo aver concesso l'autorizzazione, la relativa propagazione tramite l'attività in background `sdprop` nel controller di dominio può richiedere fino a un'ora. <br> <br> Per il funzionamento della reimpostazione della password, è necessario che l'autorizzazione sia indicata nel descrittore di sicurezza dell'oggetto utente per il quale viene reimpostata la password. Finché l'autorizzazione non sarà indicata nell'oggetto utente, per la reimpostazione della password continuerà a verificarsi un errore con un messaggio di accesso negato. |
| Gli utenti federati, con autenticazione pass-through o con sincronizzazione dell'hash delle password che cercano di reimpostare le password visualizzano un errore dopo l'invio della password. L'errore indica che si è verificato un problema del servizio. <br> <br> Oltre a questo problema, durante le operazioni di reimpostazione della password, è possibile che venga visualizzato un errore nei registri eventi del servizio Azure AD Connect che indica l'errore "Impossibile trovare l'oggetto". | Questo errore indica di solito che il motore di sincronizzazione non è in grado di trovare l'oggetto utente nello spazio connettore di Azure AD o nell'oggetto spazio connettore di Azure AD o metaverse (MV) collegato. <br> <br> Per risolvere il problema, assicurarsi che l'utente sia effettivamente sincronizzato dall'ambiente locale ad Azure AD tramite l'istanza corrente di Azure AD Connect e controllare lo stato degli oggetti negli spazi connettore e nell'oggetto MV. Verificare che l'oggetto Active Directory Servizi certificati (AD CS) sia connesso all'oggetto MV tramite la regola "Microsoft.InfromADUserAccountEnabled.xxx".|
| Gli utenti federati, con autenticazione pass-through o con sincronizzazione dell'hash delle password che cercano di reimpostare le password visualizzano un errore dopo l'invio della password. L'errore indica che si è verificato un problema del servizio. <br> <br> Oltre a questo problema, durante le operazioni di reimpostazione della password, è possibile che venga visualizzato un errore nei registri eventi del servizio Azure AD Connect che indica che sono state trovate più corrispondenze. | Ciò indica che il motore di sincronizzazione ha rilevato che l'oggetto MV è connesso a più di un oggetto Servizi certificati Active Directory tramite "Microsoft.InfromADUserAccountEnabled.xxx". Questo significa che l'utente dispone di un account abilitato in più foreste. Questo scenario non è supportato per il writeback delle password. |
| Le operazioni con le password non riescono a causa di un errore di configurazione. Il log eventi dell'applicazione contiene l'errore 6329 di Azure AD Connect con un testo simile al seguente: 0x8023061f (Operazione non riuscita perché la sincronizzazione della password non è abilitata in questo agente di gestione). | L'errore si verifica se si modifica la configurazione di Azure AD Connect per aggiungere una nuova foresta di Active Directory (o per rimuovere e aggiungere di nuovo una foresta esistente) dopo aver abilitato la funzionalità di writeback delle password. Le operazioni con le password per gli utenti in queste foreste aggiunte di recente hanno esito negativo. Per risolvere il problema, disabilitare e riabilitare la funzionalità di writeback delle password dopo avere completato le modifiche alla configurazione della foresta. |

## <a name="password-writeback-event-log-error-codes"></a>Codici errore del registro eventi di writeback della password

Una procedura consigliata per la risoluzione dei problemi relativi al writeback delle password consiste nell'esaminare il log eventi dell'applicazione nel computer che esegue Azure AD Connect. Questo registro eventi contiene gli eventi di due origini per il writeback delle password. L'origine *PasswordResetService* descrive le operazioni e i problemi correlati all'operazione di writeback delle password. L'origine *AdSync* descrive le operazioni e i problemi correlati all'impostazione delle password nell'ambiente Active Directory Domain Services.

### <a name="if-the-source-of-the-event-is-adsync"></a>Se l'origine dell'evento è ADSync

| Codice | Nome o messaggio | Descrizione |
| --- | --- | --- |
| 6329 | BAIL: MMS (4924) 0x80230619: "una restrizione impedisce che la password venga modificata in quella corrente specificata". | Questo evento si verifica quando il servizio di writeback delle password tenta di impostare una password nella directory locale che non soddisfa i requisiti di validità, cronologia, complessità o filtro del dominio. <br> <br> Se è prevista una validità minima della password e di recente la password è stata modificata in tale intervallo di tempo, non sarà possibile modificarla di nuovo finché non si raggiunge il periodo di validità specificato nel dominio. A scopo di test, è consigliabile impostare la validità minima su 0. <br> <br> Se sono abilitati i requisiti per la cronologia delle password, è necessario selezionare una password che non sia stata usata nelle ultime *n* volte, dove *N* è l'impostazione della cronologia delle password. Se si seleziona una password che è stata usata nelle ultime *N* volte, si verifica un errore. A scopo di test, è consigliabile impostare la cronologia delle password su 0. <br> <br> Se abilitati, tutti i requisiti di complessità della password vengono applicati quando l'utente tenta di modificare o reimpostare la password. <br> <br> Se sono abilitati i filtri delle password e un utente seleziona una password che non soddisfa i criteri di filtro, l'operazione di reimpostazione o di modifica avrà esito negativo. |
| 6329 | MMS (3040): admaexport. cpp (2837): il server non contiene il controllo dei criteri password LDAP. | Questo problema si verifica se il controllo LDAP_SERVER_POLICY_HINTS_OID (1.2.840.113556.1.4.2066) non è abilitato nei controller di dominio. Per usare la funzionalità di writeback delle password, è necessario abilitare il controllo. A tale scopo, i controller di dominio devono usare Windows Server 2008 R2 o versioni successive. |
| HR 8023042 | Il motore di sincronizzazione ha restituito un errore hr = 80230402, messaggio = Tentativo di ottenere un oggetto non riuscito. Sono presenti voci duplicate con lo stesso ancoraggio. | Questo errore si verifica quando lo stesso ID utente è abilitato in più domini. Ad esempio, se si esegue la sincronizzazione di foreste di risorse e account e lo stesso ID utente è presente e abilitato in ogni foresta. <br> <br> Questo errore può verificarsi anche se si usa un attributo di ancoraggio non univoco (come un alias o UPN) e due utenti condividono lo stesso attributo di ancoraggio. <br> <br> Per risolvere questo problema, assicurarsi che non ci siano utenti duplicati nei domini e di usare un attributo di ancoraggio univoco per ogni utente. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Se l'origine dell'evento è PasswordResetService

| Codice | Nome o messaggio | Descrizione |
| --- | --- | --- |
| 31001 | PasswordResetStart | Questo evento indica che il servizio locale ha rilevato una richiesta di reimpostazione della password per un utente federato, con autenticazione pass-through o con sincronizzazione dell'hash delle password originata dal cloud. Si tratta del primo evento di ogni operazione di writeback per la reimpostazione della password. |
| 31002 | PasswordResetSuccess | Questo evento indica che un utente ha selezionato una nuova password durante un'operazione di reimpostazione della password. È stato determinato che la password soddisfa i requisiti aziendali per le password. La password è stata scritta correttamente nell'ambiente Active Directory locale. |
| 31003 | PasswordResetFail | Questo evento indica che un utente ha selezionato una password che è pervenuta correttamente all'ambiente locale. Quando però è stato eseguito un tentativo di impostare la password nell'ambiente Active Directory locale, si è verificato un errore. Questo problema può verificarsi per diversi motivi: <br><ul><li>La password dell'utente non soddisfa i requisiti di validità, cronologia, complessità o filtro per il dominio. Per risolvere il problema, creare una nuova password.</li><li>L'account del servizio ADMA non dispone delle autorizzazioni appropriate per impostare la nuova password per l'account utente in questione.</li><li>L'account dell'utente si trova in un gruppo protetto, ad esempio dominio o gruppo di amministratori dell'organizzazione, che non consente operazioni di impostazione delle password.</li></ul>|
| 31004 | OnboardingEventStart | Questo evento si verifica se si Abilita il writeback delle password con Azure AD Connect ed è stato avviato il caricamento dell'organizzazione nel servizio Web di writeback delle password. |
| 31005 | OnboardingEventSuccess | Questo evento indica che il processo di onboarding è stato completato e che la funzionalità di writeback delle password è pronta per l'uso. |
| 31006 | ChangePasswordStart | Questo evento indica che il servizio locale ha rilevato una richiesta di modifica della password per un utente federato, con autenticazione pass-through o con sincronizzazione dell'hash delle password originata dal cloud. Si tratta del primo evento di ogni operazione di writeback per la modifica della password. |
| 31007 | ChangePasswordSuccess | Questo evento indica che un utente ha selezionato una nuova password durante un'operazione di modifica della password, è stato determinato che la password soddisfa i requisiti aziendali e la password è stata scritta correttamente nell'ambiente Active Directory locale. |
| 31008 | ChangePasswordFail | Questo evento indica che un utente ha selezionato una password che è pervenuta correttamente all'ambiente locale, ma quando si è cercato di impostare la password nell'ambiente Active Directory locale si è verificato un errore. Questo problema può verificarsi per diversi motivi: <br><ul><li>La password dell'utente non soddisfa i requisiti di validità, cronologia, complessità o filtro per il dominio. Per risolvere il problema, creare una nuova password.</li><li>L'account del servizio ADMA non dispone delle autorizzazioni appropriate per impostare la nuova password per l'account utente in questione.</li><li>L'account dell'utente si trova in un gruppo protetto, ad esempio Domain Admins o Enterprise Admins, che non consente operazioni di impostazione delle password.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | Il servizio locale ha rilevato una richiesta di reimpostazione della password per un utente federato, con autenticazione pass-through o con sincronizzazione dell'hash delle password originata dall'amministratore per conto di un utente. Si tratta del primo evento di ogni operazione di writeback per la reimpostazione della password avviata da un amministratore. |
| 31010 | ResetUserPasswordByAdminSuccess | L'amministratore ha selezionato una nuova password durante un'operazione di reimpostazione della password avviata dall'amministratore. È stato determinato che la password soddisfa i requisiti aziendali per le password. La password è stata scritta correttamente nell'ambiente Active Directory locale. |
| 31011 | ResetUserPasswordByAdminFail | L'amministratore ha selezionato una password per conto dell'utente. La password è pervenuta correttamente all'ambiente locale. Quando però è stato eseguito un tentativo di impostare la password nell'ambiente Active Directory locale, si è verificato un errore. Questo problema può verificarsi per diversi motivi: <br><ul><li>La password dell'utente non soddisfa i requisiti di validità, cronologia, complessità o filtro per il dominio. Per risolvere il problema, provare a usare una nuova password.</li><li>L'account del servizio ADMA non dispone delle autorizzazioni appropriate per impostare la nuova password per l'account utente in questione.</li><li>L'account dell'utente si trova in un gruppo protetto, ad esempio Domain Admins o Enterprise Admins, che non consente operazioni di impostazione delle password.</li></ul>  |
| 31012 | OffboardingEventStart | Questo evento si verifica se si disabilita il writeback delle password con Azure AD Connect e indica che è stato avviato l'offboarding dell'organizzazione nel servizio Web di writeback delle password. |
| 31013| OffboardingEventSuccess| Questo evento indica che il processo di offboarding è stato completato e che la funzionalità di writeback delle password è stata disabilitata. |
| 31014| OffboardingEventFail| Questo evento indica che il processo offboarding non è riuscito. La causa può essere legata a un errore di autorizzazioni nel cloud oppure a un account amministratore locale specificato durante la configurazione. L'errore può verificarsi anche se si sta cercando di usare un amministratore globale cloud federato quando si disabilita il writeback delle password. Per risolvere il problema, controllare le autorizzazioni amministrative e verificare che non sia in uso un account federato durante la configurazione della funzionalità di writeback delle password.|
| 31015| WriteBackServiceStarted| Questo evento indica che il servizio di writeback delle password è stato avviato correttamente. Il servizio è pronto ad accettare le richieste di gestione delle password dal cloud.|
| 31016| WriteBackServiceStopped| Questo evento indica che il servizio di writeback delle password è stato arrestato. Eventuali richieste di gestione delle password dal cloud non riusciranno.|
| 31017| AuthTokenSuccess| Questo evento indica l'operazione di recupero di un token di autorizzazione per l'amministratore globale specificato durante l'installazione di Azure AD Connect per avviare il processo di scaricamento o caricamento è stata eseguita correttamente.|
| 31018| KeyPairCreationSuccess| Questo evento indica che la chiave di crittografia delle password è stata creata correttamente. La chiave verrà usata per crittografare le password cloud da inviare all'ambiente locale.|
| 32000| UnknownError| Questo evento indica che si è verificato un errore sconosciuto durante un'operazione di gestione delle password. Per altri dettagli, fare riferimento al testo dell'eccezione. In caso di problemi, provare a disabilitare e quindi riabilitare il writeback delle password. Se questa operazione non è utile, includere una copia del registro eventi insieme all'ID di traccia specificato quando si apre una richiesta di supporto.|
| 32001| ServiceError| Questo evento indica che si è verificato un errore di connessione al servizio cloud di reimpostazione delle password. L'errore si verifica in genere quando il servizio locale non è riuscito a connettersi al servizio Web di reimpostazione della password.|
| 32002| ServiceBusError| Questo evento indica che si è verificato un errore durante la connessione all'istanza del bus di servizio del tenant. Ciò può verificarsi se le connessioni in uscita nell'ambiente locale sono bloccate. Verificare che nel firewall siano consentite le connessioni su TCP 443 e verso https://ssprdedicatedsbprodncu.servicebus.windows.net e riprovare. Se il problema persiste, provare a disabilitare e quindi riabilitare il writeback delle password.|
| 32003| InPutValidationError| Questo evento indica che l'input passato all'API del servizio Web non è valido. Riprovare a eseguire l'operazione.|
| 32004| DecryptionError| Questo evento indica che si è verificato un errore di decrittografia della password ricevuta dal cloud. La causa potrebbe essere una mancata corrispondenza della chiave di decrittografia tra il servizio cloud e l'ambiente locale. Per risolvere il problema, disabilitare e quindi riabilitare il writeback delle password nell'ambiente locale.|
| 32005| ConfigurationError| Durante il caricamento, le informazioni specifiche del tenant vengono salvate in un file di configurazione nell'ambiente locale. Questo evento indica che si è verificato un errore durante il salvataggio del file o che durante l'avvio del servizio si è verificato un errore di lettura del file. Per risolvere il problema, provare a disabilitare e quindi riabilitare il writeback delle password per forzare la riscrittura del file di configurazione.|
| 32007| OnBoardingConfigUpdateError| Durante l'onboarding vengono inviati dati dal cloud al servizio di reimpostazione della password locale. I dati vengono quindi scritti in un file in memoria prima di essere inviati al servizio di sincronizzazione per l'archiviazione sicura su disco. Questo evento indica che si è verificato un problema di scrittura o aggiornamento dei dati in memoria. Per risolvere il problema, provare a disabilitare e quindi riabilitare il writeback delle password per forzare la riscrittura del file di configurazione.|
| 32008| ValidationError| Questo evento indica che è stata ricevuta una risposta non valida dal servizio Web di reimpostazione della password. Per risolvere il problema, provare a disabilitare e quindi riabilitare il writeback delle password.|
| 32009| AuthTokenError| Questo evento indica che non è stato possibile ottenere un token di autorizzazione per l'account amministratore globale specificato durante l'installazione di Azure AD Connect. L'errore può essere causato da una password o un nome utente non valido specificato per l'account amministratore globale. L'errore può verificarsi anche se l'account amministratore globale specificato è federato. Per risolvere il problema, ripetere la configurazione con il nome utente e la password corretti e verificare che l'amministratore sia un account gestito, solo cloud o con sincronizzazione della password.|
| 32010| CryptoError| Questo evento indica che si è verificato un errore durante la generazione della chiave di crittografia della password o durante la decrittografia di una password ricevuta dal servizio cloud. L'errore indica probabilmente un problema dell'ambiente. Per altre informazioni su come risolvere il problema, esaminare i dettagli del log eventi. È anche possibile provare a disabilitare e quindi riabilitare il servizio di writeback delle password.|
| 32011| OnBoardingServiceError| Questo evento indica che il servizio locale non è stato in grado di comunicare correttamente con il servizio Web di reimpostazione della password per avviare il processo di onboarding. Questo problema può verificarsi a causa di una regola del firewall o se si verifica un problema durante il recupero di un token di autenticazione per il tenant. Per risolvere il problema, verificare che le connessioni in uscita su TCP 443 e TCP 9350-9354 o verso https://ssprdedicatedsbprodncu.servicebus.windows.net non siano bloccate. Verificare anche che l'account amministratore di Azure AD usato per l'onboarding non sia federato.|
| 32013| OffBoardingError| Questo evento indica che il servizio locale non è stato in grado di comunicare correttamente con il servizio Web di reimpostazione della password per avviare il processo di offboarding. Questo problema può verificarsi a causa di una regola del firewall o se si verifica un problema durante il recupero di un token di autorizzazione per il tenant. Per risolvere il problema, assicurarsi che le connessioni in uscita sulla porta 443 o verso https://ssprdedicatedsbprodncu.servicebus.windows.net non siano bloccate e che l'account amministratore di Azure Active Directory usato per l'offboarding non sia federato.|
| 32014| ServiceBusWarning| Questo evento indica che è stato necessario ripetere la connessione all'istanza del bus di servizio del tenant. In condizioni normali, questo non dovrebbe essere un problema, ma se questo evento viene visualizzato più volte, provare a controllare la connessione di rete al bus di servizio, soprattutto se si tratta di una connessione a latenza elevata o a larghezza di banda ridotta.|
| 32015| ReportServiceHealthError| Per monitorare l'integrità del servizio di writeback delle password, vengono inviati dati heartbeat al servizio Web di reimpostazione della password ogni cinque minuti. Questo evento indica che si è verificato un errore durante l'invio delle informazioni sull'integrità al servizio Web cloud. Queste informazioni sull'integrità non includono dati personali ed è esclusivamente un heartbeat e statistiche di base del servizio per poter fornire informazioni sullo stato del servizio nel cloud.|
| 33001| ADUnKnownError| Questo evento indica che si è verificato un errore sconosciuto restituito da Active Directory. Per altre informazioni, verificare se nel log eventi del server Azure AD Connect sono presenti eventi dall'origine ADSync.|
| 33002| ADUserNotFoundError| Questo evento indica che l'utente che sta tentando di reimpostare o modificare una password non è stato trovato nella directory locale. L'errore può verificarsi quando l'utente è stato eliminato in locale ma non nel cloud. Questo errore può verificarsi anche se si verifica un problema con la sincronizzazione. Per ulteriori informazioni, controllare i log di sincronizzazione e gli ultimi dettagli dell'esecuzione della sincronizzazione.|
| 33003| ADMutliMatchError| Quando una richiesta di reimpostazione o modifica della password ha origine dal cloud, viene usato l'ancoraggio cloud specificato durante l'installazione di Azure AD Connect per determinare la modalità di collegamento della richiesta a un utente nell'ambiente locale. Questo evento indica che nella directory locale sono stati trovati due utenti con lo stesso attributo di ancoraggio cloud. Per altre informazioni, controllare i log di sincronizzazione e i dettagli relativi alle esecuzioni della sincronizzazione più recenti.|
| 33004| ADPermissionsError| Questo evento indica che l'account del servizio di Active Directory Management Agent (ADMA) non dispone delle autorizzazioni appropriate per l'account in questione per impostare una nuova password. Verificare che l'account ADMA nella foresta dell'utente disponga delle autorizzazioni di reimpostazione della password per tutti gli oggetti nella foresta. Per altre informazioni su come impostare le autorizzazioni, vedere il Passaggio 4: Impostare le autorizzazioni di Active Directory appropriate. Questo errore può verificarsi anche quando l'attributo AdminCount dell'utente è impostato su 1.|
| 33005| ADUserAccountDisabled| Questo evento indica che è stato eseguito un tentativo di reimpostare o modificare una password per un account disabilitato in locale. Abilitare l'account e ripetere l'operazione.|
| 33006| ADUserAccountLockedOut| Questo evento indica che è stato eseguito un tentativo di reimpostare o modificare una password per un account bloccato in locale. I blocchi possono verificarsi quando un utente ha tentato troppe volte di eseguire un'operazione di modifica o reimpostazione della password in un breve intervallo di tempo. Sbloccare l'account e ripetere l'operazione.|
| 33007| ADUserIncorrectPassword| Questo evento indica che l'utente ha specificato una password corrente non corretta durante l'esecuzione di un'operazione di modifica della password. Specificare la password corrente corretta e riprovare.|
| 33008| ADPasswordPolicyError| Questo evento si verifica quando il servizio di writeback delle password tenta di impostare una password nella directory locale che non soddisfa i requisiti di validità, cronologia, complessità o filtro del dominio. <br> <br> Se è prevista una validità minima della password e di recente la password è stata modificata in tale intervallo di tempo, non sarà possibile modificarla di nuovo finché non si raggiunge il periodo di validità specificato nel dominio. A scopo di test, è consigliabile impostare la validità minima su 0. <br> <br> Se sono abilitati i requisiti per la cronologia delle password, sarà necessario selezionare una password che non sia stata usata nelle ultime *N* volte, dove *N* è l'impostazione relativa alla cronologia delle password. Se si seleziona una password che è stata usata nelle ultime *N* volte, si verifica un errore. A scopo di test, è consigliabile impostare la cronologia delle password su 0. <br> <br> Se abilitati, tutti i requisiti di complessità della password vengono applicati quando l'utente tenta di modificare o reimpostare la password. <br> <br> Se sono abilitati i filtri delle password e un utente seleziona una password che non soddisfa i criteri di filtro, l'operazione di reimpostazione o di modifica avrà esito negativo.|
| 33009| ADConfigurationError| Questo evento indica che si è verificato un problema durante la scrittura di una password nella directory locale, a causa di un problema di configurazione con Active Directory. Per ulteriori informazioni sull'errore che si è verificato, controllare il registro eventi dell'applicazione del computer Azure AD Connect per individuare i messaggi dal servizio ADSync.|

## <a name="azure-ad-forums"></a>Forum di Azure AD

Per domande di carattere generale su Azure AD e la reimpostazione della password self-service, è possibile richiedere assistenza alla community nella [pagina Domande e risposte di Microsoft&per Azure Active Directory](/answers/topics/azure-active-directory.html). La community è composta da ingegneri, responsabili di prodotto, MVP e informatici.

## <a name="contact-microsoft-support"></a>Contattare il supporto Microsoft

Se non si riesce a trovare la risposta a un problema, i team di supporto Microsoft sono sempre disponibili per offrire maggiore assistenza.

Per garantire un supporto adeguato, verrà richiesto il maggior numero di dettagli possibile al momento dell'apertura di un caso. Questi dettagli includono quanto segue:

* **Descrizione generale dell'errore**: indicare il tipo di errore, il comportamento notato, e le modalità in cui è possibile riprodurre l'errore. Fornire il maggior numero di dettagli possibili.
* **Pagina**: indicare la pagina che si stava consultando quando è stato visualizzato l'errore. Includere l'URL, se possibile, e uno screenshot della pagina.
* **Codice di supporto**: indicare il codice di supporto generato quando è stato visualizzato l'errore.
   * Per trovare questo codice, riprodurre l'errore, quindi fare clic sul collegamento **Codice di supporto** nella parte inferiore della schermo e inviare al personale del supporto tecnico il GUID risultante.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="Il codice di supporto si trova nella parte inferiore destra della finestra del browser Web.":::

  * Se è visualizzata una pagina senza un codice di supporto nella parte inferiore, premere F12 ed eseguire una ricerca di SID e CID, quindi inviare i due risultati al personale del supporto tecnico.
* **Data, ora e fuso orario**: includere la data e l'ora precise *con il fuso orario* di quando si è verificato l'errore.
* **ID utente**: indicare l'ID dell'utente che ha visualizzato l'errore. Ad esempio, *utente\@contoso.com*.
   * Indicare se si tratta di un utente federato,
   * Si tratta di un utente con autenticazione pass-through?
   * Si tratta di un utente con sincronizzazione di hash della password?
   * Si tratta di un utente solo cloud?
* **Licenze**: all'utente è stata assegnata una licenza di Azure AD?
* **Log eventi dell'applicazione**: se si usa il writeback delle password e l'errore si verifica nell'infrastruttura locale, includere una copia compressa del log eventi dell'applicazione del server Azure AD Connect.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su SSPR, vedere [come funziona: Azure ad la reimpostazione della password self-service](concept-sspr-howitworks.md) o [come funziona il writeback della reimpostazione della password self-service in Azure ad?](concept-sspr-writeback.md).
