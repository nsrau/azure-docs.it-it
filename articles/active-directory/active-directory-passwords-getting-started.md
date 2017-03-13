---
title: Introduzione alla gestione delle password di Azure AD | Microsoft Docs
description: Consentire agli utenti di reimpostare le proprie password, individuare i prerequisiti per la reimpostazione delle password e abilitare il writeback delle password per gestire le password in locale in Active Directory.
services: active-directory
keywords: Gestione delle password di Active Directory, gestione delle password management, reimpostare la password di Azure AD
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: c40fca54b02f2673194ab16c41314f1e50be12be
ms.lasthandoff: 03/06/2017


---
# <a name="getting-started-with-password-management"></a>Introduzione alla gestione delle password
> [!IMPORTANT]
> **Se si sta visualizzando questa pagina perché si riscontrano problemi nell'accesso** , [seguire questa procedura per cambiare e reimpostare la password](active-directory-passwords-update-your-own-password.md).
>
>

Consentire agli utenti di gestire le proprie password di Azure Active Directory su cloud o di Active Directory locale richiede solo alcuni semplici passaggi. Dopo aver verificato che siano stati soddisfatti alcuni semplici prerequisiti, sarà subito possibile abilitare la modifica e la reimpostazione delle password per tutta l'organizzazione. Questo articolo illustra i concetti seguenti:

* [**Come consentire agli utenti di reimpostare le password di Azure Active Directory**](#enable-users-to-reset-their-azure-ad-passwords)
  * [Prerequisiti per la reimpostazione delle password self-service](#prerequisites)
  * [Passaggio 1: Configurare i criteri di reimpostazione delle password](#step-1-configure-password-reset-policy)
  * [Passaggio 2: Aggiungere i dati di contatto per l'utente test](#step-2-add-contact-data-for-your-test-user)
  * [Passaggio 3: Reimpostare la password come utente](#step-3-reset-your-azure-ad-password-as-a-user)
* [**Come consentire agli utenti di reimpostare o modificare le password di Active Directory locale**](#enable-users-to-reset-or-change-their-ad-passwords)
  * [Prerequisiti per il writeback delle password](#writeback-prerequisites)
  * [Passaggio 1: Scaricare la versione più recente di Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
  * [Passaggio 2: Abilitare il writeback delle password in Azure AD Connect tramite l'interfaccia utente o PowerShell e verificare il risultato](#step-2-enable-password-writeback-in-azure-ad-connect)
  * [Passaggio 3: Configurare il firewall](#step-3-configure-your-firewall)
  * [Passaggio 4: Impostare le autorizzazioni appropriate](#step-4-set-up-the-appropriate-active-directory-permissions)
  * [Passaggio 5: Reimpostare la password di Active Directory come utente e verificare il risultato](#step-5-reset-your-ad-password-as-a-user)

## <a name="enable-users-to-reset-their-azure-ad-passwords"></a>Consentire agli utenti di reimpostare le password di Azure AD
Questa sezione illustra le procedure per abilitare la reimpostazione delle password self-service per la directory cloud di AAD, registrare gli utenti per la reimpostazione delle password self-service e infine testare la reimpostazione delle password self-service come utente.

* [Prerequisiti per la reimpostazione delle password self-service](#prerequisites)
* [Passaggio 1: Configurare i criteri di reimpostazione delle password](#step-1-configure-password-reset-policy)
* [Passaggio 2: Aggiungere i dati di contatto per l'utente test](#step-2-add-contact-data-for-your-test-user)
* [Passaggio 3: Reimpostare la password come utente](#step-3-reset-your-azure-ad-password-as-a-user)

### <a name="prerequisites"></a>Prerequisiti
Prima di poter abilitare e usare la reimpostazione delle password self-service, è necessario completare i prerequisiti seguenti:

* Creare un tenant di AAD. Per altre informazioni, vedere [Introduzione ad Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/)
* Ottenere una sottoscrizione di Azure. Per altre informazioni, vedere [Che cos'è un tenant di Azure AD?](active-directory-administer.md#what-is-an-azure-ad-tenant).
* Associare il tenant di AAD alla sottoscrizione di Azure. Per altre informazioni, vedere [Associazione delle sottoscrizioni di Azure ad Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx).
* Eseguire l'aggiornamento ad Azure AD Premium o Basic oppure usare una licenza di Office&365; a pagamento. Per altre informazioni, vedere [Edizioni di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

  > [!NOTE]
  > Per abilitare la reimpostazione delle password self-service per gli utenti del cloud, è necessario eseguire l'aggiornamento ad Azure AD Premium o ad Azure AD Basic oppure usare una licenza di Office&365; a pagamento.  Per abilitare la reimpostazione delle password self-service per gli utenti locali, è necessario eseguire l'aggiornamento ad Azure AD Premium. Per altre informazioni, vedere [Edizioni di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Queste informazioni includono istruzioni dettagliate su come effettuare l'iscrizione ad Azure AD Premium o Basic, come attivare il piano di licenze e attivare l'accesso ad AD Azure e come assegnare l'accesso agli account amministratore e utente.
  >
  >
* Creare almeno un account amministratore e un account utente nella directory di AAD.
* Assegnare una licenza di AAD Premium o Basic oppure una licenza di Office&365; a pagamento agli account amministratore e utente creati.

### <a name="step-1-configure-password-reset-policy"></a>Passaggio 1: Configurare i criteri di reimpostazione delle password
Per configurare criteri di reimpostazione della password utente, completare i passaggi seguenti:

1. Aprire il browser preferito e accedere al [portale di Azure classico](https://manage.windowsazure.com).
2. Nel [portale di Azure classico](https://manage.windowsazure.com), trovare l'**estensione Active Directory** nella barra di spostamento a sinistra.

   ![Gestione delle password in Azure AD][001]
3. Nella scheda **Directory** fare clic sulla directory in cui si vogliono configurare i criteri di reimpostazione della password utente, ad esempio Wingtip Toys.

    ![][002]
4. Fare clic sulla scheda **Configura**.

   ![][003]

5. Nella scheda **Configura** scorrere verso il basso fino alla sezione **Criteri di reimpostazione password utente**.  In questa sezione è possibile configurare tutti gli aspetti dei criteri di reimpostazione della password utente per una determinata directory. *Se la scheda Configura non viene visualizzata, verificare di aver effettuato l'iscrizione ad Azure Active Directory Premium o Basic e di aver __assegnato una licenza__ all'account amministratore che sta configurando questa funzionalità.*  

   > [!NOTE]
   > **I criteri impostati si applicano solo agli utenti finali dell'organizzazione, non agli amministratori**. Per motivi di sicurezza, Microsoft controlla i criteri di reimpostazione delle password per gli amministratori. I criteri attuali per gli amministratori prevedono due test: telefono cellulare e indirizzo di posta elettronica.

   ![][004]
6. Per configurare i criteri di reimpostazione della password utente, impostare l'opzione **Utenti abilitati per la reimpostazione della password** su **Sì**.  Verranno così visualizzati molti altri controlli che consentono di configurare il modo in cui usare questa funzionalità nella propria directory.  Sarà quindi possibile personalizzare la funzionalità di reimpostazione delle password in base alle proprie esigenze specifiche.  Per saperne di più sul funzionamento di ognuno dei controlli dei criteri di reimpostazione delle password, vedere [Personalizzare: Gestione delle password di Azure AD](active-directory-passwords-customize.md).

   ![][005]
7. Dopo avere configurato i criteri di reimpostazione della password utente nel modo desiderato per il proprio tenant, fare clic sul pulsante **Salva** nella parte inferiore della schermata.

   > [!NOTE]
   > È consigliabile usare criteri di reimpostazione della password utente basati su due test in modo da valutare il funzionamento di questa funzionalità nel caso più complesso.
   >
   >

   ![][006]

   > [!NOTE]
   > **I criteri impostati si applicano solo agli utenti finali dell'organizzazione, non agli amministratori**. Per motivi di sicurezza, Microsoft controlla i criteri di reimpostazione delle password per gli amministratori. I criteri attuali per gli amministratori prevedono due test: telefono cellulare e indirizzo di posta elettronica.
   >
   >

### <a name="step-2-add-contact-data-for-your-test-user"></a>Passaggio 2: Aggiungere i dati di contatto per l'utente test
Esistono diverse opzioni su come specificare i dati che gli utenti dell'organizzazione useranno per la reimpostazione delle password.

* Modificare gli utenti nel [portale di Azure classico](https://manage.windowsazure.com)o nel [portale di amministrazione di Office 365](https://portal.microsoftonline.com)
* Usare AAD Connect per sincronizzare le proprietà utente in Azure AD da un dominio di Active Directory locale
* Usare Windows PowerShell per modificare le proprietà utente
* Consentire agli utenti di registrare i propri dati indirizzandoli al portale di registrazione all'indirizzo [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
* Richiedere agli utenti di effettuare la registrazione per la reimpostazione delle password quando accedono al pannello di accesso all'indirizzo [http://myapps.microsoft.com](http://myapps.microsoft.com) impostando l'opzione di configurazione per la reimpostazione delle password self-service **Richiedere agli utenti di registrarsi al primo accesso al pannello?** su **Sì**.

Per altre informazioni sui dati che vengono usati per la reimpostazione delle password e sui requisiti di formattazione di tali dati, vedere [Dati usati per la reimpostazione della password](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

#### <a name="to-add-user-contact-data-via-the-user-registration-portal"></a>Per aggiungere i dati di contatto degli utenti tramite il portale di registrazione utente
1. Per poter usare il portale di registrazione per la reimpostazione delle password, è necessario fornire agli utenti dell'organizzazione un collegamento alla pagina[http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)oppure attivare l'opzione per richiedere la registrazione automatica.  Dopo aver fatto clic sul collegamento, agli utenti verrà chiesto di effettuare l'accesso con l'account aziendale.  A questo punto, verrà visualizzata la pagina seguente:

   ![][007]
2. In questa pagina gli utenti possono fornire e verificare il telefono cellulare, l'indirizzo di posta elettronica alternativo o le domande di sicurezza.  Di seguito è riportato un esempio di verifica di un telefono cellulare.

   ![][008]
3. Dopo aver specificato queste informazioni, la pagina (offuscata nella figura seguente) verrà aggiornata indicando che le informazioni sono valide.  Quando l'utente fa clic sui pulsanti **Fine** o **Annulla**, viene visualizzato il pannello di accesso.

   ![][009]
4. Dopo che l'utente ha verificato entrambe queste informazioni, il relativo profilo verrà aggiornato con i dati forniti.  In questo esempio il numero di **telefono dell'ufficio** è stato specificato manualmente, pertanto l'utente potrà usarlo come metodo di contatto per la reimpostazione della password.

   ![][010]

### <a name="step-3-reset-your-azure-ad-password-as-a-user"></a>Passaggio 3: Reimpostare la password di Azure AD come utente
A questo punto, dopo aver configurato i criteri di reimpostazione della password utente e aver specificato i dati di contatto dell'utente, tale utente potrà eseguire la reimpostazione della password self-service.

#### <a name="to-perform-a-self-service-password-reset"></a>Per eseguire la reimpostazione della password self-service
1. In un sito come [**portal.microsoftonline.com**](http://portal.microsoftonline.com) viene visualizzata una schermata di accesso simile alla seguente.  Fare clic sul collegamento **Problemi di accesso all'account?** per testare l'interfaccia utente di reimpostazione della password.

   ![][011]
2. Dopo aver fatto clic su **Problemi di accesso all'account?**, verrà visualizzata una nuova pagina in cui viene richiesto di immettere l'**ID utente** per il quale reimpostare la password.  Immettere l'**ID utente** test e superare il test CAPTCHA, quindi fare clic su **Avanti**.

   ![][012]
3. Dato che in questo caso l'utente ha specificato un **telefono dell'ufficio**, un **telefono cellulare** e un **indirizzo di posta elettronica alternativo**, tutti e tre gli elementi sono stati forniti come opzioni per superare la prima richiesta di autenticazione.

   ![][013]
4. In questo caso, scegliere di **chiamare** prima il **telefono dell'ufficio**.  Si noti che quando si seleziona un metodo basato sul telefono, agli utenti verrà chiesto di **verificare il numero di telefono** prima che sia possibile reimpostare le password.  Tale accorgimento consente di evitare che malintenzionati possano usare i numeri di telefono degli utenti dell'organizzazione per lo spam.

   ![][014]
5. Dopo la conferma del numero di telefono, quando l'utente fa clic sulla chiamata verrà visualizzato un indicatore di avanzamento e il telefono dell'utente inizierà a squillare.  Quando l'utente risponde al telefono, verrà riprodotto un messaggio in cui si invita l’utente a **premere "#"** per verificare l'account.  Premendo questo tasto si verificherà che l'utente ha superato il primo test e può passare all'interfaccia utente per eseguire il secondo passaggio di verifica.

   ![][015]
6. Dopo il superamento del primo test l'interfaccia utente verrà aggiornata automaticamente in modo da rimuoverlo dall'elenco delle opzioni disponibili.  In questo caso, dato che è stata usata per prima l'opzione **Telefono ufficio**, le sole opzioni valide per il secondo passaggio di verifica sono il **Cellulare** e **Indirizzo e-mail alternativo**.  Fare clic sull'opzione **Invia messaggio di posta elettronica all'indirizzo di posta elettronica alternativo dell'utente** .  Dopo l'esecuzione di questa operazione, premendo il pulsante per l'invio del messaggio si determinerà l'invio del messaggio all'indirizzo di posta elettronica alternativo registrato.

   ![][016]
7. Questo è un esempio di un messaggio di posta elettronica che verrà visualizzato dagli utenti. Si noti la personalizzazione del tenant.

   ![][017]
8. Dopo l'arrivo del messaggio di posta elettronica, la pagina verrà aggiornata e sarà possibile immettere la verifica trovata nel messaggio di posta elettronica nella casella di immissione riportata sotto.  Se il codice immesso è corretto, il pulsante Avanti diventa attivo e anche il secondo passaggio di verifica sarà superato.

   ![][018]
9. Dopo aver soddisfatto i requisiti dei criteri aziendali, all'utente sarà consentito scegliere una nuova password.  La password verrà convalidata in base ai requisiti di complessità delle password di AAD (vedere [Criteri password in Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx)); verrà inoltre visualizzato un indicatore di convalida della complessità per segnalare se la password immessa è conforme ai criteri.

   ![][019]
10. Dopo aver specificato password corrispondenti e conformi ai criteri aziendali, la password esistente verrà reimpostata e sarà possibile accedere subito con la nuova password.

    ![][020]

## <a name="enable-users-to-reset-or-change-their-ad-passwords"></a>Consentire agli utenti di reimpostare o modificare le password di Active Directory
Questa sezione illustra come configurazione la reimpostazione delle password per il writeback delle password in un'istanza di Active Directory locale.

* [Prerequisiti per il writeback delle password](#writeback-prerequisites)
* [Passaggio 1: Scaricare la versione più recente di Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
* [Passaggio 2: Abilitare il writeback delle password in Azure AD Connect tramite l'interfaccia utente o PowerShell e verificare il risultato](#step-2-enable-password-writeback-in-azure-ad-connect)
* [Passaggio 3: Configurare il firewall](#step-3-configure-your-firewall)
* [Passaggio 4: Impostare le autorizzazioni appropriate](#step-4-set-up-the-appropriate-active-directory-permissions)
* [Passaggio 5: Reimpostare la password di Active Directory come utente e verificare il risultato](#step-5-reset-your-ad-password-as-a-user)

### <a name="writeback-prerequisites"></a>Prerequisiti per il writeback
Per poter abilitare e usare il writeback delle password, verificare che vengano soddisfatti i prerequisiti seguenti:

* È necessario avere un tenant di Azure AD con Azure AD Premium abilitato.  Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).
* Nel tenant deve essere stata configurata e abilitata la reimpostazione delle password.  Per altre informazioni, vedere [Consentire agli utenti di reimpostare le password di Azure AD](#enable-users-to-reset-their-azure-ad-passwords)
* È necessario avere almeno un account amministratore e un account utente test con una licenza Azure AD Premium da usare per testare questa funzionalità.  Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).

  > [!NOTE]
  > Assicurarsi che l'account amministratore usato per abilitare il writeback delle password sia un account amministratore cloud (creato in Azure AD), non un account federato (creato in AD locale e sincronizzato in Azure AD).
  >
  >
* È necessario avere una distribuzione locale di Active Directory con foresta singola o a più foreste in cui sia in esecuzione Windows Server 2008, Windows Server 2008 R2, Windows Server 2012 o Windows Server 2012 R2 con i Service Pack più recenti installati.

  > [!NOTE]
  > Se si esegue una versione precedente di Windows Server 2008 o 2008 R2, è comunque possibile usare questa funzionalità, ma sarà necessario [scaricare e installare la KB 2386717](https://support.microsoft.com/kb/2386717) per poter applicare i criteri delle password di Active Directory locale nel cloud.
  >
  >
* È necessario che sia installato lo strumento Azure AD Connect e che l'ambiente Active Directory sia stato preparato per la sincronizzazione con il cloud.  Per altre informazioni, vedere [Usare l'infrastruttura di identità locale nel cloud](connect/active-directory-aadconnect.md).

  > [!NOTE]
  > Prima di testare il writeback delle password, assicurarsi di eseguire un'importazione completa e una sincronizzazione completa sia da AD che da Azure AD Connect.
  >
  >
* Se si usa Azure AD Sync o Azure AD Connect, è necessario aprire solo la porta **TCP 443** in uscita e in alcuni casi la porta **TCP 9350-9354**.  Per altre informazioni, vedere [Passaggio 3: Configurare il firewall](#step-3-configure-your-firewall) . L'uso di DirSync per questo scenario non è più supportato.  Se si usa ancora DirSync, eseguire l'aggiornamento all'ultima versione di Azure AD Connect prima di distribuire il writeback delle password.

  > [!NOTE]
  > È consigliabile che chiunque usi gli strumenti Azure AD Sync o DirSync esegua l'aggiornamento alla versione più recente di Azure AD Connect in modo da ottenere la migliore esperienza possibile e le nuove funzionalità non appena vengono rilasciate.
  >
  >

### <a name="step-1-download-the-latest-version-of-azure-ad-connect"></a>Passaggio 1: Scaricare la versione più recente di Azure AD Connect
Il writeback delle password è disponibile nelle versioni di Azure AD Connect o nello strumento Azure AD Sync a partire dalla versione **1.0.0419.0911** .  Il writeback delle password con sblocco automatico dell'account è disponibile nelle versioni di Azure AD Connect o nello strumento Azure AD Sync a partire dalla versione **1.0.0485.0222** . Se si esegue una versione precedente, aggiornarla almeno a questa versione prima di procedere. [Fare clic qui per scaricare la versione più recente di Azure AD Connect](connect/active-directory-aadconnect.md#install-azure-ad-connect).

#### <a name="to-check-the-version-of-azure-ad-sync"></a>Per verificare la versione di Azure AD Sync
1. Passare a **%ProgramFiles%\Azure Active Directory Sync\**.
2. Trovare il file eseguibile **ConfigWizard.exe** .
3. Fare clic con il pulsante destro del mouse sul file eseguibile e scegliere **Proprietà** dal menu di scelta rapida.
4. Fare clic sulla scheda **Dettagli** .
5. Trovare il campo **Versione file** .

   ![][021]

Se il numero di versione è uguale o superiore a **1.0.0419.0911** oppure se si sta eseguendo l'installazione di Azure AD Connect, è possibile andare direttamente al [Passaggio 2: Abilitare il writeback delle password in Azure AD Connect tramite l'interfaccia utente o PowerShell e verificare il risultato](#step-2-enable-password-writeback-in-azure-ad-connect).

> [!NOTE]
> Se è la prima volta che si installa lo strumento Azure AD Connect, è opportuno seguire alcune procedure consigliate per preparare l'ambiente per la sincronizzazione della directory.  Prima di installare lo strumento Azure AD Connect, è necessario attivare la sincronizzazione della directory nel [portale di amministrazione di Office 365](https://portal.microsoftonline.com) o nel [portale di Azure classico](https://manage.windowsazure.com).  Per altre informazioni, vedere [Gestione di Azure AD Connect](active-directory-aadconnect-whats-next.md).
>
>

### <a name="step-2-enable-password-writeback-in-azure-ad-connect"></a>Passaggio 2: Abilitare il writeback delle password in Azure AD Connect
Dopo aver scaricato lo strumento Azure AD Connect, è possibile abilitare il writeback delle password.  Questa operazione può essere eseguita in due modi:  nella schermata delle funzionalità facoltative della configurazione guidata di Azure AD Connect oppure tramite Windows PowerShell.

#### <a name="to-enable-password-writeback-in-the-configuration-wizard"></a>Per abilitare il writeback delle password nella configurazione guidata
1. Nel **computer in cui è installato Directory Sync** aprire la configurazione guidata di **Azure AD Connect**.
2. Fare clic sui passaggi fino a visualizzare la schermata di configurazione delle **funzionalità facoltative** .
3. Selezionare l'opzione **Writeback password** .

   ![][022]
4. Completare la procedura guidata. La pagina finale visualizzerà un riepilogo delle modifiche e includerà la modifica alla configurazione del writeback delle password.

> [!NOTE]
> È possibile disabilitare il writeback delle password in qualsiasi momento, eseguendo di nuovo la procedura guidata e deselezionando la funzionalità oppure impostando il **writeback delle password nella directory locale** su **No** nella sezione **Criteri di reimpostazione password utente** della scheda **Configura** della directory nel [portale di Azure classico](https://manage.windowsazure.com).  Per altre informazioni sulla personalizzazione della procedura di reimpostazione delle password, vedere [Personalizzare: Gestione delle password di Azure AD](active-directory-passwords-customize.md).
>
>

#### <a name="to-enable-password-writeback-using-windows-powershell"></a>Per abilitare il writeback delle password con Windows PowerShell
1. Nel **computer in cui è installato Directory Sync** aprire una nuova **finestra di Windows PowerShell con privilegi elevati**.
2. Se il modulo non è già stato caricato, immettere il comando `import-module ADSync` per caricare i cmdlet di Azure AD Connect nella sessione attuale.
3. Ottenere l'elenco dei connettori di Azure AD nel sistema eseguendo il cmdlet `Get-ADSyncConnector` e archiviando i risultati in `$aadConnectorName``$connectors = Get-ADSyncConnector|where-object {$\_.name -like "\*AAD"}`.
4. Ottenere lo stato corrente del writeback per il connettore corrente eseguendo il cmdlet seguente: `Get-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name`.
5. Abilitare il writeback delle password eseguendo il cmdlet: `Set-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name –Enable $true`.

> [!NOTE]
> Se viene visualizzato un messaggio di richiesta delle credenziali, assicurarsi che l'account amministratore specificato per AzureADCredential sia un **account amministratore cloud (creato in Azure AD)**, non un account federato (creato in AD locale e sincronizzato in Azure AD).
>
> [!NOTE]
> È possibile disabilitare il writeback delle password tramite PowerShell ripetendo la procedura precedente, ma passando `$false` nel passaggio oppure impostando il **writeback delle password nella directory locale** su **No** nella sezione **Criteri di reimpostazione password utente** della scheda **Configura** della directory nel [Portale di Azure classico](https://manage.windowsazure.com).
>
>

#### <a name="verify-that-the-configuration-was-successful"></a>Verificare che la configurazione sia stata eseguita correttamente
Dopo il completamento della configurazione nella finestra di Windows PowerShell verrà visualizzato un messaggio che indica che il writeback per la reimpostazione delle password è abilitato oppure nell'interfaccia utente di configurazione verrà visualizzato un messaggio che indica il completamento dell'operazione.

Per verificare che il servizio sia stato installato correttamente, è anche possibile aprire il Visualizzatore eventi, passare al log eventi dell'applicazione e cercare l'evento **31005 - OnboardingEventSuccess** nell'origine **PasswordResetService**.

  ![][023]

### <a name="step-3-configure-your-firewall"></a>Passaggio 3: Configurare il firewall
Dopo avere abilitato il writeback delle password, è necessario assicurarsi che il computer che esegue Azure AD Connect possa raggiungere i servizi cloud Microsoft per ricevere le richieste di writeback delle password. Questo passaggio implica l'aggiornamento delle regole di connessione nelle appliance di rete (server proxy, firewall e così via) per consentire le connessioni in uscita verso determinati [URL e indirizzi IP di proprietà di Microsoft](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US) tramite porte di rete specifiche. Queste modifiche possono variare in base alla versione dello strumento Azure AD Connect. Per altre informazioni sul contesto, è possibile leggere le sezioni sul [funzionamento del writeback delle password](active-directory-passwords-learn-more.md#how-password-writeback-works) e sul [modello di sicurezza del writeback delle password](active-directory-passwords-learn-more.md#password-writeback-security-model).

#### <a name="why-do-i-need-to-do-this"></a>Perché è necessario eseguire questa operazione?

Affinché il writeback delle password funzioni correttamente, il computer che esegue Azure AD Connect deve essere in grado di stabilire connessioni HTTPS in uscita verso **.servicebus.windows.net* e l'indirizzo IP specifico usato da Azure, come definito nell'[elenco intervalli IP del data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

Per la versione **1.1.439.0** (ultima) e le versioni successive dello strumento Azure AD Connect:

- La versione più recente dello strumento Azure AD Connect dovrà disporre dell'accesso **HTTPS in uscita** a:
    - *passwordreset.microsoftonline.com*
    - *servicbus.windows.net*

Per le versioni da **1.0.8667.0** a **1.1.380.0** dello strumento Azure AD Connect:

- **Opzione 1:** consentire di eseguire tutte le connessioni HTTPS in uscita sulla porta 443 (usando l'indirizzo IP o l'URL).
    - Quando usare questa opzione:
        - Usare questa opzione se si desidera eseguire la configurazione più semplice che non deve essere aggiornata quando gli intervalli IP dei data center di Azure cambiano.
    - Procedura:
        - Consentire di eseguire tutte le connessioni HTTPS in uscita sulla porta 443 usando l'indirizzo IP o l'URL.
<br><br>
- **Opzione 2:** consentire di eseguire le connessioni HTTPS in uscita verso determinati intervalli IP e URL
    - Quando usare questa opzione:
        - Usare questa opzione se l'ambiente di rete presenta restrizioni oppure se non ci si sente a proprio agio nel consentire le connessioni in uscita.
        - In questa configurazione, affinché il writeback delle password continui a funzionare, è necessario assicurarsi che le appliance di rete vengano aggiornate settimanalmente con gli indirizzi IP più recenti dall'elenco di intervalli IP dei data center di Microsoft Azure. Questi intervalli IP sono disponibili sotto forma di file XML che viene aggiornato ogni mercoledì (orario del Pacifico) e diventa effettivo il lunedì successivo (orario del Pacifico).
    - Procedura:
        - Consentire tutte le connessioni HTTPS in uscita verso *.servicebus.windows.net
        - Consentire tutte le connessioni HTTPS in uscita verso tutti gli indirizzi IP nell'elenco di intervalli IP dei data center di Microsoft Azure e mantenere la configurazione aggiornata con frequenza settimanale.

> [!NOTE]
> Se è stato configurato il writeback delle password seguendo le istruzioni precedenti e non vengono visualizzati errori nel log eventi di Azure AD Connect, ma si ricevono errori di connettività durante il test, il problema potrebbe essere legato a un'appliance di rete nell'ambiente in uso che blocca le connessioni HTTPS agli indirizzi IP. Ad esempio, se viene consentita una connessione a *https://*.servicebus.windows.net*, potrebbe essere bloccata un'altra connessione a un indirizzo IP specifico all'interno dell'intervallo. Per risolvere questo problema, è necessario configurare l'ambiente di rete per consentire tutte le connessioni HTTPS in uscita sulla porta 443 verso qualsiasi indirizzo IP o URL (Opzione 1 sopra) o consultare il team responsabile della rete per consentire le connessioni HTTPS verso indirizzi IP specifici (Opzione 2 sopra) in modo esplicito.

**Per le versioni precedenti:**

- Consentire le connessioni TCP in uscita sulla porta 443, 9350-9354 e sulla porta 5671
- Consentire le connessioni in uscita verso *https://ssprsbprodncu-sb.accesscontrol.windows.net/*

> [!NOTE]
> Se si usa una versione di Azure AD Connect precedente alla versione 1.0.8667.0, è consigliabile eseguire l'aggiornamento alla [versione più recente di Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594), che include determinati miglioramenti della rete di writeback per semplificare la configurazione.

Dopo aver configurato le appliance di rete, riavviare il computer che esegue lo strumento Azure AD Connect.

#### <a name="idle-connections-on-azure-ad-connect-114390-and-up"></a>Connessioni inattive in Azure AD (versione&1;.1.439.0 e successive)
Lo strumento Azure AD Connect invierà ping/keep-alive periodici agli endpoint di ServiceBus per assicurarsi che le connessioni rimangano attive. Nel caso lo strumento rilevi che vengono interrotte troppe connessioni, aumenterà automaticamente la frequenza dei ping all'endpoint. L'intervallo di ping più breve che può essere impostato è di 1 ping ogni 60 secondi, tuttavia, **è caldamente consigliato che i proxy/firewall consentano la persistenza delle connessioni inattive per almeno 2-3 minuti**. \*Per le versioni precedenti, è consigliata una persistenza di 4 minuti o più.

### <a name="step-4-set-up-the-appropriate-active-directory-permissions"></a>Passaggio 4: Impostare le autorizzazioni di Active Directory appropriate
Per ogni foresta che contiene utenti di cui verranno reimpostate le password, se X è l'account specificato per tale foresta nella configurazione guidata iniziale, sarà necessario assegnare a X i diritti estesi **Reimposta password**, **Modifica password**, **Autorizzazioni di scrittura** per `lockoutTime` e **Autorizzazioni di scrittura** per `pwdLastSet` sull'oggetto radice per ogni dominio della foresta. I diritti devono essere contrassegnati come ereditati da tutti gli oggetti utente.  

Se non si è certi dell'identità dell'account al quale si fa riferimento, aprire la configurazione di Azure Active Directory Connect dell'interfaccia utente e fare clic sull'opzione **Esaminare la soluzione** .  L'account necessario per aggiungere autorizzazioni è sottolineato in rosso nella schermata seguente.

**<font color="red">Assicurarsi di impostare questa autorizzazione per ogni dominio in ogni foresta nel sistema, in caso contrario il writeback delle password non funzionerà correttamente.</font>**

  ![][032]

  L'impostazione di queste autorizzazioni consentirà all'account del servizio dell'agente di gestione per ogni foresta di gestire le password per conto di account utente all'interno di tale foresta. Se non si assegnano tali autorizzazioni, anche se il writeback è configurato correttamente, gli utenti visualizzeranno errori durante il tentativo di gestire le password locali dal cloud. Di seguito sono descritti i passaggi dettagliati su come effettuare questa operazione usando lo snap-in di gestione di **Utenti e computer di Active Directory** :

> [!NOTE]
> La replica delle autorizzazioni in tutti gli oggetti nella directory può richiedere fino a un'ora.
>
>

#### <a name="to-set-up-the-right-permissions-for-writeback-to-occur"></a>Per impostare le autorizzazioni appropriate per l'esecuzione del writeback
1. Aprire **Utenti e computer di Active Directory** con un account con le autorizzazioni appropriate per l'amministrazione del dominio.
2. Verificare che l'opzione **Funzionalità avanzate** sia attivata nel menu **Visualizza**.
3. Nel riquadro sinistro fare clic con il pulsante destro del mouse sull'oggetto che rappresenta la radice del dominio.
4. Fare clic sulla scheda **Sicurezza** .
5. Fare clic su **Avanzate**.

   ![][024]
6. Fare clic su **Aggiungi** nella scheda **Autorizzazioni**.

   ![][025]
7. Selezionare l'account a cui si vuole concedere le autorizzazioni (è lo stesso account specificato durante l'impostazione della sincronizzazione per tale foresta).
8. Nell'elenco a discesa nella parte superiore selezionare **Oggetti Utente discendente**.
9. Nella finestra di dialogo **Voce autorizzazione** visualizzata selezionare le caselle **Reimposta password**, **Modifica password**, **Autorizzazioni di scrittura** per `lockoutTime` e **Autorizzazioni di scrittura** per `pwdLastSet`.

   ![][026]
   ![][027]
   ![][028]
10. Quindi fare clic su **Applica/OK** nelle finestre di dialogo aperte.

### <a name="step-5-reset-your-ad-password-as-a-user"></a>Passaggio 5: Reimpostare la password di AD come utente
Dopo aver abilitato il writeback delle password, è possibile verificarne il funzionamento reimpostando la password di un utente il cui account è stato sincronizzato nel tenant del cloud.

#### <a name="to-verify-password-writeback-is-working-properly"></a>Per verificare il corretto funzionamento del writeback delle password
1. Passare a [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com) o a una schermata di accesso con l'ID dell'organizzazione e fare clic sul collegamento **Problemi di accesso all'account?** .

   ![][029]
2. A questo punto dovrebbe essere visualizzata una pagina in cui viene chiesto di specificare un ID utente per cui si vuole reimpostare una password. Immettere l'ID utente test e procedere con il flusso di reimpostazione delle password.
3. Dopo aver reimpostato la password, verrà visualizzata una schermata simile alla seguente, che indica che la reimpostazione della password è stata effettuata correttamente nelle directory locale e/o nel cloud.

   ![][030]
4. Per verificare l'esito positivo dell'operazione o per diagnosticare eventuali errori, passare al **computer in cui è installato Directory Sync**, aprire il **Visualizzatore eventi**, passare al **log eventi dell'applicazione** e cercare l'evento **31002 - PasswordResetSuccess** nell'origine **PasswordResetService** per l'utente test.

   ![][031]



## <a name="next-steps"></a>Passaggi successivi
Di seguito vengono forniti collegamenti a tutte le pagine della documentazione relative alla reimpostazione della password in Azure AD:

* **Se si sta visualizzando questa pagina perché si riscontrano problemi nell'accesso,** [seguire questa procedura per cambiare e reimpostare la password](active-directory-passwords-update-your-own-password.md).
* [**Funzionamento**](active-directory-passwords-how-it-works.md): informazioni sui sei diversi componenti del servizio e sulle relative funzioni
* [**Personalizzazione**](active-directory-passwords-customize.md): informazioni su come personalizzare l'aspetto e il comportamento del servizio in base alle esigenze dell'organizzazione
* [**Procedure consigliate**](active-directory-passwords-best-practices.md): informazioni su come distribuire rapidamente e gestire in modo efficace le password nell'organizzazione
* [**Informazioni dettagliate**](active-directory-passwords-get-insights.md): informazioni sulle funzionalità di creazione report integrate
* [**Domande frequenti**](active-directory-passwords-faq.md) : risposte alle domande frequenti
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md): informazioni su come risolvere rapidamente eventuali problemi con il servizio
* [**Altre informazioni**](active-directory-passwords-learn-more.md): approfondimenti sui dettagli tecnici del funzionamento del servizio

[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"
[032]: ./media/active-directory-passwords-getting-started/032.jpg "Image_032.jpg"

