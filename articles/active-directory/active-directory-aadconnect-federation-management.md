---
title: Gestione e personalizzazione di Active Directory Federation Services con Azure AD Connect | Microsoft Docs
description: Gestione di AD FS con Azure AD Connect e personalizzazione dell'esperienza utente di accesso ad AD FS con Azure AD Connect e PowerShell.
keywords: AD FS, ADFS, gestione di AD FS, AAD Connect, Connect, accesso, personalizzazione di AD FS, ripristino trust, O365, federazione, relying party
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy

---
# <a name="active-directory-federation-services-management-and-customization-with-azure-ad-connect"></a>Gestione e personalizzazione di Active Directory Federation Services con Azure AD Connect
Questo articolo descrive in dettaglio le attività correlate ad Active Directory Federation Services (AD FS) che possono essere eseguite con Microsoft Azure Active Directory Connect, nonché altre attività comuni di AD FS eventualmente necessarie per la configurazione completa di una farm AD FS.

| Argomento | Contenuto |
|:--- |:--- |
| **Gestione di AD FS.** | |
| [Ripristinare il trust](#repairthetrust) |Ripristino del trust federativo con Office 365 |
| [Aggiungere un server AD FS](#addadfsserver) |Espansione della farm AD FS con un server AD FS aggiuntivo |
| [Aggiungere un server proxy applicazione Web AD FS](#addwapserver) |Espansione della farm AD FS con un server WAP aggiuntivo |
| [Aggiunta di un dominio federato](#addfeddomain) |Aggiunta di un dominio federato |
| **Personalizzazione di AD FS** | |
| [Aggiungere un'illustrazione o il logo personalizzato della società](#customlogo) |Personalizzazione della pagina di accesso ad AD FS con un'illustrazione o il logo della società |
| [Aggiungere una descrizione di accesso](#addsignindescription) |Aggiunta di una descrizione della pagina di accesso |
| [Modificare le regole attestazioni per AD FS](#modclaims) |Modifica delle attestazioni di AD FS per vari scenari federativi |

## <a name="ad-fs-management"></a>Gestione di AD FS.
Azure AD Connect offre diverse attività correlate ad AD FS che possono essere eseguite tramite la procedura guidata di Azure AD Connect con un intervento minimo dell'utente. Al termine dell'installazione guidata di Azure AD Connect, è possibile eseguire nuovamente la procedura guidata per eseguire attività aggiuntive.

### <a name="repair-the-trust-a-namerepairthetrusta"></a>Ripristinare il trust <a name=repairthetrust></a>
Azure AD Connect può controllare lo stato di integrità corrente della relazione di trust tra AD FS e Azure Active Directory e intraprendere le azioni necessarie per ripristinare il trust. Per ripristinare la relazione di trust tra Azure AD e AD FS, seguire questa procedura.

1. Selezionare **Ripristino del trust di AAD e AD FS** nell'elenco delle attività aggiuntive.
   ![Ripristino del trust di AAD e AD FS](media\\active-directory-aadconnect-federation-management\\RepairADTrust1.PNG)
2. Nella pagina **Connessione ad Azure AD** specificare le credenziali di amministratore globale per Azure AD e fare clic su **Avanti**.
   ![Connessione ad Azure AD](media\\active-directory-aadconnect-federation-management\\RepairADTrust2.PNG)
3. Nella pagina **Credenziali di accesso remoto** specificare le credenziali dell'amministratore di dominio.
   ![Credenziali di accesso remoto](media\\active-directory-aadconnect-federation-management\\RepairADTrust3.PNG)
   
    Dopo aver fatto clic su **Avanti**, Azure AD Connect verifica l'integrità del certificato e visualizza gli eventuali problemi.
   
    ![Stato dei certificati](media\\active-directory-aadconnect-federation-management\\RepairADTrust4.PNG)
   
    La pagina **Pronto per la configurazione** mostra l'elenco delle azioni che verranno eseguite per ripristinare il trust.
   
    ![Pronto per la configurazione](media\\active-directory-aadconnect-federation-management\\RepairADTrust5.PNG)
4. Fare clic su **Installa** per ripristinare il trust.

> [!NOTE]
> Azure AD Connect può solo ripristinare o eseguire azioni sui certificati autofirmati. I certificati di terze parti non possono essere ripristinati da Azure AD Connect.
> 
> 

### <a name="add-an-ad-fs-server-a-nameaddadfsservera"></a>Aggiungere un server AD FS <a name=addadfsserver></a>
> [!NOTE]
> Per aggiungere un server AD FS, Azure AD Connect richiede il file PFX del certificato. È quindi possibile eseguire questa operazione solo se la farm AD FS è stata configurata con Azure AD Connect.
> 
> 

1. Selezionare **Distribuzione di un server federativo aggiuntivo** e fare clic su **Avanti**.
   ![Server federativo aggiuntivo](media\\active-directory-aadconnect-federation-management\\AddNewADFSServer1.PNG)
2. Nella pagina **Connessione ad Azure AD** specificare le credenziali di amministratore globale per Azure AD e fare clic su **Avanti**.
   ![Connessione ad Azure AD](media\\active-directory-aadconnect-federation-management\\AddNewADFSServer2.PNG)
3. Specificare le credenziali di amministratore di dominio.
   ![Credenziali dell'amministratore di dominio](media\\active-directory-aadconnect-federation-management\\AddNewADFSServer3.PNG)
4. Azure AD Connect chiederà la password del file PFX specificato durante la configurazione della nuova farm AD FS con Azure AD Connect. Fare clic su **Immettere la password** per specificare la password del file PFX.
   ![Password certificato](media\\active-directory-aadconnect-federation-management\\AddNewADFSServer4.PNG)
   
    ![Specificare il certificato SSL](media\\active-directory-aadconnect-federation-management\\AddNewADFSServer5.PNG)
5. Nella pagina **Server ADFS** immettere il nome del server o l'indirizzo IP da aggiungere alla farm ADFS.
   ![AD FS servers](media\\active-directory-aadconnect-federation-management\\AddNewADFSServer6.PNG)
6. Fare clic su **Avanti** e procedere fino alla pagina **Configura** finale. Quando Azure AD Connect avrà completato l'aggiunta dei server alla farm AD FS, si potrà verificare la connettività.
   ![Pronto per la configurazione](media\\active-directory-aadconnect-federation-management\\AddNewADFSServer7.PNG)
   
    ![Installazione completata](media\\active-directory-aadconnect-federation-management\\AddNewADFSServer8.PNG)

### <a name="add-an-ad-fs-web-application-proxy-server-a-nameaddwapservera"></a>Aggiungere un server proxy applicazione Web AD FS <a name=addwapserver></a>
> [!NOTE]
> Per aggiungere un server proxy applicazione Web AD FS, Azure AD Connect richiede il file PFX del certificato. Sarà quindi possibile eseguire questa operazione solo se la farm AD FS è stata configurata con Azure AD Connect.
> 
> 

1. Selezionare **Distribuisci il proxy applicazione Web (nessuno attualmente configurato)** nell'elenco delle attività disponibili.
   ![Deploy web application proxy](media\\active-directory-aadconnect-federation-management\\WapServer1.PNG)
2. Specificare le credenziali di amministratore globale di Azure.
   ![Connessione ad Azure AD](media\\active-directory-aadconnect-federation-management\\wapserver2.PNG)
3. Nella pagina **Specificare il certificato SSL** fornire la password per il file PFX specificato durante la configurazione della farm AD FS con Azure AD Connect.
   ![Password certificato](media\\active-directory-aadconnect-federation-management\\WapServer3.PNG)
   
    ![Specificare il certificato SSL](media\\active-directory-aadconnect-federation-management\\WapServer4.PNG)
4. Aggiungere il server da usare come proxy applicazione Web. Poiché il server proxy applicazione Web potrebbe non essere aggiunto al dominio, la procedura guidata richiederà le credenziali amministrative per il server da aggiungere.
   ![Credenziali amministrative del server](media\\active-directory-aadconnect-federation-management\\WapServer5.PNG)
5. Nella pagina **Credenziali attendibilità proxy** specificare le credenziali amministrative per configurare l'attendibilità del proxy e accedere al server primario nella farm AD FS.
   ![Credenziali attendibilità proxy](media\\active-directory-aadconnect-federation-management\\WapServer6.PNG)
6. La pagina **Pronto per la configurazione** della procedura guidata mostra l'elenco delle azioni che verranno eseguite.
   ![Pronto per la configurazione](media\\active-directory-aadconnect-federation-management\\WapServer7.PNG)
7. Fare clic su **Installa** per completare la configurazione. Al termine della configurazione, la procedura guidata offre la possibilità di verificare la connettività ai server. Fare clic su **Verifica** per controllare la connettività.
   ![Installazione completata](media\\active-directory-aadconnect-federation-management\\WapServer8.PNG)

### <a name="add-a-federated-domain-a-nameaddfeddomaina"></a>Aggiunta di un dominio federato <a name=addfeddomain></a>
Con Azure AD Connect è facile aggiungere un dominio per la federazione con Azure AD. Azure AD Connect aggiunge il dominio per la federazione e modifica le regole attestazioni per riflettere correttamente l'autorità di certificazione quando sono presenti più domini federati con Azure AD.

1. Per aggiungere un dominio federato, selezionare l'attività **Aggiunta di un altro dominio di Azure AD**.
   ![Dominio di Azure AD aggiuntivo](media\\active-directory-aadconnect-federation-management\\AdditionalDomain1.PNG)
2. Nella pagina successiva della procedura guidata specificare le credenziali di amministratore globale per Azure AD.
   ![Connessione ad Azure AD](media\\active-directory-aadconnect-federation-management\\AdditionalDomain2.PNG)
3. Nella pagina **Credenziali di accesso remoto** specificare le credenziali di amministratore di dominio.
   ![Credenziali di accesso remoto](media\\active-directory-aadconnect-federation-management\\additionaldomain3.PNG)
4. Nella pagina successiva la procedura guidata mostra un elenco di domini di Azure AD con cui è possibile attuare la federazione della directory locale. Scegliere il dominio dall'elenco.
   ![Dominio di Azure AD](media\\active-directory-aadconnect-federation-management\\AdditionalDomain4.PNG)
   
    Dopo aver scelto il dominio, la procedura guidata fornisce informazioni appropriate sulle altre azioni che verranno eseguite e sull'impatto della configurazione. In alcuni casi, se si seleziona un dominio non ancora verificato in Azure AD, la procedura guidata fornisce informazioni che consentono di verificare il dominio. Per altre informazioni, vedere [Aggiungere un nome di dominio personalizzato ad Azure Active Directory](active-directory-add-domain.md) .
5. Fare clic su **Avanti**. Nella pagina **Pronto per la configurazione** verrà visualizzato l'elenco delle azioni che verranno eseguite da Azure AD Connect. Fare clic su **Installa** per completare la configurazione.
   ![Pronto per la configurazione](media\\active-directory-aadconnect-federation-management\\AdditionalDomain5.PNG)

## <a name="ad-fs-customization"></a>Personalizzazione di AD FS
Le sezioni seguenti forniscono informazioni dettagliate su alcune attività comuni che potrebbe essere necessario eseguire durante la configurazione della pagina di accesso ad AD FS.

### <a name="add-a-custom-company-logo-or-illustration-a-namecustomlogoa"></a>Aggiungere un'illustrazione o il logo personalizzato della società <a name=customlogo></a>
Per modificare il logo della società visualizzato nella pagina **Accesso** , usare la sintassi e il cmdlet di Windows PowerShell seguenti.

> [!NOTE]
> Le dimensioni consigliate per il logo sono 260x35 @ 96 DPI, con una dimensione di file non maggiore di 10 KB.
> 
> 

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> Il parametro *TargetName* è obbligatorio. Il tema predefinito incluso in AD FS è denominato Predefinito.
> 
> 

### <a name="add-a-signin-description-a-nameaddsignindescriptiona"></a>Aggiungere una descrizione di accesso <a name=addsignindescription></a>
Per aggiungere una descrizione alla **Pagina di accesso**stessa, usare la sintassi e il cmdlet di Windows PowerShell seguenti.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

### <a name="modify-ad-fs-claim-rules-a-namemodclaimsa"></a>Modificare le regole attestazioni per AD FS <a name=modclaims></a>
AD FS supporta un linguaggio di attestazione avanzato che può essere usato per creare regole attestazioni personalizzate. Per altre informazioni, vedere [Ruolo del linguaggio delle regole attestazioni](https://technet.microsoft.com/library/dd807118.aspx).

Le sezioni seguenti descrivono come scrivere regole personalizzate per alcuni scenari relativi alla federazione di AD FS e Azure AD.

#### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>ID non modificabile in base alla presenza di un valore nell'attributo
Azure AD Connect consente di specificare un attributo da usare come ancoraggio di origine durante la sincronizzazione degli oggetti con Azure AD. Se il valore dell'attributo personalizzato non è vuoto, è consigliabile rilasciare un'attestazione con ID non modificabile. Ad esempio, è possibile selezionare **ms-ds-consistencyguid** come attributo per l'ancoraggio di origine e rilasciare **ImmutableID** come **ms-ds-consistencyguid** nel caso in cui il valore dell'attributo non sia vuoto. Se non esiste un valore a fronte dell'attributo, rilasciare **objectGuid** come ID non modificabile.  È possibile costruire il set di regole attestazioni personalizzate come descritto nella sezione seguente.

**Regola 1: Attributi della query**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

In questa regola si esegue una query da Active Directory sui valori di **ms-ds-consistencyguid** e **objectGuid** per l'utente. Modificare il nome dell'archivio con un nome di archivio appropriato nella distribuzione di AD FS. Sostituire anche il tipo di attestazioni con il tipo appropriato per la federazione, come definito per **objectGuid** e **ms-ds-consistencyguid**.

Se si usa **add** invece di **issue**, si evita di aggiungere un rilascio in uscita per l'entità ed è possibile usare i valori come valori intermedi. L'attestazione verrà rilasciata in una regola successiva, dopo aver stabilito il valore usare come ID non modificabile.

**Regola 2: Verificare la presenza di ms-ds-consistencyguid per l'utente**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Questa regola definisce un flag temporaneo denominato **idflag**, che è impostato su **useguid** se non è presente un **ms-ds-concistencyguid** popolato l'utente. Questo perché secondo la logica di AD FS non sono ammesse attestazioni vuote. Pertanto quando si aggiungono le attestazioni http://contoso.com/ws/2016/02/identity/claims/objectguid e http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid nella regola 1, si ottiene un'attestazione **msdsconsistencyguid** solo se il valore viene popolato per l'utente. Se non è popolato, AD FS rileva che avrà un valore vuoto e lo rimuove immediatamente. Tutti gli oggetti avranno **objectGuid**, quindi l'attestazione sarà sempre presente dopo l'esecuzione della regola 1.

**Regola 3: Rilasciare ms-ds-consistencyguid come ID non modificabile se presente**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Si tratta di un controllo **Exist** implicito. Se il valore per l'attestazione esiste, viene emesso come ID non modificabile. Nell'esempio precedente si usa l'attestazione **nameidentifier** . Sarà necessario sostituirla con il tipo di attestazione appropriato per l'ID non modificabile nel proprio ambiente.

**Regola 4: Rilasciare objectGuid come ID non modificabile se ms-ds-consistencyGuid non è presente**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

In questa regola si verifica semplicemente il flag temporaneo **idflag**. Decidere se rilasciare l'attestazione in base al relativo valore.

> [!NOTE]
> La sequenza delle regole è importante.
> 
> 

#### <a name="sso-with-a-subdomain-upn"></a>SSO con un UPN di sottodominio
È possibile aggiungere più domini per la federazione usando Azure AD Connect, come descritto in [Aggiungere un nuovo dominio federato](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain). È necessario modificare l'attestazione basata su UPN, in modo che l'ID autorità di certificazione corrisponda al dominio radice e non al sottodominio, perché il dominio radice federato include anche il dominio figlio.

Per impostazione predefinita, la regola attestazioni per l'ID autorità di certificazione è impostata come:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Attestazione predefinita per l'ID autorità di certificazione](media\\active-directory-aadconnect-federation-management\\issuer_id_default.png)

La regola predefinita usa semplicemente il suffisso UPN nell'attestazione per l'ID autorità di certificazione. Ad esempio, John è un utente in sub.contoso.com e contoso.com è federato con Azure AD. John immette john@sub.contoso.com come nome utente durante l'accesso ad Azure AD e la regola attestazioni predefinita per l'ID autorità di certificazione in AD FS lo gestisce nel modo seguente.

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**Valore dell'attestazione:** http://sub.contoso.com/adfs/services/trust/

Perché il valore attestazione dell'autorità di certificazione contenga solo il dominio radice, modificare la regola attestazioni in modo che corrisponda a quanto segue.

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulle [opzioni di accesso utente](active-directory-aadconnect-user-signin.md).

<!--HONumber=Oct16_HO2-->


