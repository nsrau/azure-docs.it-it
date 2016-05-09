<properties
	pageTitle="Gestione e personalizzazione di AD FS con Azure AD Connect | Microsoft Azure"
	description="Gestione di AD FS con Azure AD Connect e personalizzazione dell'esperienza di accesso ad AD FS dell'utente con Azure AD Connect e Powershell."
	services="active-directory"
	documentationCenter=""
	authors="anandyadavmsft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/14/2016"
	ms.author="anandy"/>

# Gestione e personalizzazione di AD FS con Azure AD Connect

Questo articolo illustra in modo dettagliato le varie attività relative ad AD FS che è possibile eseguire con Azure AD Connect e altre attività comuni di AD FS che potrebbero essere necessarie per la configurazione di una farm AD FS completa.

## Gestione di AD FS

Azure AD Connect offre diverse operazioni relative ad AD FS che possono essere eseguite con la procedura guidata di Azure AD Connect con un intervento minimo dell'utente. Al termine dell'installazione guidata di Azure AD Connect, è possibile eseguire nuovamente la procedura guidata per eseguire attività aggiuntive.

### Ripristino del trust

Azure AD Connect può controllare lo stato corrente della relazione di trust tra AD FS e Azure AD e intraprendere le azioni necessarie per ripristinare il trust. Seguire questa procedura per ripristinare la relazione di trust tra Azure AD e AD FS.

Selezionare **Ripristino del trust di AAD e AD FS** dall'elenco di attività disponibili.

![](media\active-directory-aadconnect-federation-management\RepairADTrust1.PNG)

Nella pagina **Connessione ad Azure AD** specificare le credenziali di amministratore globale per Azure AD e fare clic su Avanti.

![](media\active-directory-aadconnect-federation-management\RepairADTrust2.PNG)

Nella pagina **Credenziali di accesso remoto** specificare le credenziali di amministratore di dominio.

![](media\active-directory-aadconnect-federation-management\RepairADTrust3.PNG)

Dopo aver fatto clic su Avanti, Azure AD Connect verifica l'integrità del certificato e visualizza gli eventuali problemi.

![](media\active-directory-aadconnect-federation-management\RepairADTrust4.PNG)

La pagina **Pronto per la configurazione** mostra l'elenco di azioni che verranno eseguite per ripristinare il trust.

![](media\active-directory-aadconnect-federation-management\RepairADTrust5.PNG)

Fare clic su Installa per iniziare a ripristinare il trust.

>[AZURE.NOTE] Azure AD Connect può solo ripristinare o intraprendere azioni sui certificati autofirmati. I certificati di terze parti non possono essere ripristinati da Azure AD Connect.

### Aggiunta di un nuovo server AD FS

> [AZURE.NOTE] Per aggiungere un server AD FS tramite Azure AD Connect è necessario il file PFX del certificato. Sarà quindi possibile eseguire questa operazione solo se la farm AD FS è stata configurata con Azure AD Connect.

Selezionare **Distribuzione di un server federativo aggiuntivo** e fare clic su Avanti.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer1.PNG)

Nella pagina **Connessione ad Azure AD** specificare le credenziali di amministratore globale per Azure AD e fare clic su Avanti.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer2.PNG)

Specificare le credenziali di amministratore di dominio nella pagina successiva.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer3.PNG)

Nella pagina successiva Azure AD Connect richiede la password del file PFX specificato durante la configurazione della nuova farm AD FS con Azure AD Connect. Fare clic su Immettere la password per specificare la password del file PFX.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer4.PNG)

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer5.PNG)

Nella pagina successiva specificare il nome o l'indirizzo IP di uno o più server da aggiungere alla farm AD FS.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer6.PNG)

Fare clic su Avanti e scorrere la pagina Configura finale. Al termine dell'aggiunta dei server alla farm AD FS da parte di Azure AD Connect, sarà possibile verificare la connettività.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer7.PNG)

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer8.PNG)

### Aggiunta di un nuovo server WAP AD FS

> [AZURE.NOTE] Per aggiungere un server WAP tramite Azure AD Connect è necessario il file PFX del certificato. Sarà quindi possibile eseguire questa operazione solo se la farm AD FS è stata configurata con Azure AD Connect.

Selezionare **Distribuisci il proxy applicazione Web (nessuno attualmente configurato)** dall'elenco di attività disponibili.

![](media\active-directory-aadconnect-federation-management\WapServer1.PNG)

Nella pagina successiva specificare le credenziali di amministratore globale di Azure.

![](media\active-directory-aadconnect-federation-management\wapserver2.PNG)

Viene quindi visualizzata la pagina **Specificare il certificato SSL**, in cui è necessario fornire la password per il file PFX specificato durante la configurazione della farm AD FS con Azure AD Connect.

![](media\active-directory-aadconnect-federation-management\WapServer3.PNG)

![](media\active-directory-aadconnect-federation-management\WapServer4.PNG)

Nella pagina successiva aggiungere il server da aggiungere come WAP. Dato che il server WAP potrebbe anche non essere aggiunto al dominio, la procedura guidata richiede le credenziali amministrative per il server da aggiungere.

![](media\active-directory-aadconnect-federation-management\WapServer5.PNG)

Nella pagina **Credenziali attendibilità proxy** specificare le credenziali amministrative per configurare l'attendibilità del proxy e accedere al server primario nella farm AD FS.

![](media\active-directory-aadconnect-federation-management\WapServer6.PNG)

Nella pagina **Pronto per la configurazione** la procedura guidata mostra l'elenco di azioni che verranno eseguite.

![](media\active-directory-aadconnect-federation-management\WapServer7.PNG)

Fare clic su Installa per terminare la configurazione. Al termina della configurazione, la procedura guidata offre la possibilità di verificare la connettività ai server. Fare clic su Verifica per verificare la connettività.

![](media\active-directory-aadconnect-federation-management\WapServer8.PNG)

### Aggiungere un nuovo dominio federato

Usando Azure AD Connect è facile aggiungere un nuovo dominio per la federazione con Azure AD. Azure AD Connect non solo aggiunge il nuovo dominio per la federazione, ma modifica le regole attestazioni in modo che riflettano correttamente l'autorità di certificazione quando sono presenti più domini federati con Azure AD.

Per aggiungere un nuovo dominio federato, selezionare l'attività **Aggiunta di un altro dominio di Azure AD**.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain1.PNG)

Nella pagina successiva della procedura guidata specificare le credenziali di amministratore globale per Azure AD.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain2.PNG)

Nella pagina Credenziali di accesso remoto specificare le credenziali di amministratore di dominio.

![](media\active-directory-aadconnect-federation-management\additionaldomain3.PNG)

Nella pagina successiva la procedura guidata mostra un elenco di domini di Azure AD con cui si vuole attuare la federazione della directory locale. Scegliere il dominio dall'elenco.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain4.PNG)

Dopo aver scelto il dominio, la procedura guidata fornisce informazioni appropriate sulle altre azioni che verranno eseguite e sull'impatto della configurazione. In alcuni casi, se si seleziona un dominio non ancora verificato in Azure AD, la procedura guidata fornisce informazioni che consentono di verificare il dominio. Per altre informazioni su come verificare il dominio, vedere [Aggiungere e verificare un nome di dominio personalizzato in Azure Active Directory](active-directory-add-domain-add-verify-general.md).

La pagina **Pronto per la configurazione** mostra l'elenco di azioni che verranno eseguite da Azure AD Connect. Fare clic su Installa per terminare la configurazione.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain5.PNG)

## Personalizzazione di AD FS

Le sezioni seguenti forniscono informazioni dettagliate su come eseguire alcune attività comuni che potrebbero essere richieste per la personalizzazione della pagina di accesso ad AD FS.

### Aggiungere un'illustrazione o il logo personalizzato della società

Per modificare il logo della società visualizzato nella pagina di accesso, usare la sintassi e il cmdlet di Windows PowerShell riportati di seguito.

> [AZURE.NOTE] Le dimensioni consigliate per il logo sono 260 x 35 a 96 DPI, con una dimensione file non superiore a 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [AZURE.NOTE] Il parametro TargetName è obbligatorio. Il tema predefinito che rilasciato con AD FS è denominato default.
 

### Aggiungere una descrizione di accesso

Per aggiungere una descrizione della pagina di accesso alla pagina stessa, usare la sintassi e il cmdlet di Windows PowerShell riportati di seguito.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

### Modificare le regole attestazioni per AD FS

AD FS offre la possibilità di specificare regole personalizzate per il rilascio delle attestazioni. Supporta un linguaggio di attestazione avanzato che consente di creare regole attestazioni personalizzate. Per altre informazioni, vedere il relativo [articolo](https://technet.microsoft.com/library/dd807118.aspx).

Le sezioni seguenti illustrano come scrivere regole personalizzate per alcuni scenari relativi alla federazione di AD FS e Azure AD.

#### ID non modificabile in base alla presenza di un valore nell'attributo

Azure AD Connect consente di specificare un attributo da usare come ancoraggio di origine durante la sincronizzazione degli oggetti con Azure AD. Si potrebbe voler rilasciare un'attestazione ID non modificabile se il valore nell'attributo personalizzato non è vuoto. Ad esempio, si supponga di aver selezionato ms-ds-consistencyguid come attributo per l'ancoraggio di origine e che si voglia rilasciare l'ID non modificabile come ms-ds-consistencyguid nel caso in cui il valore dell'attributo non sia vuoto. In caso contrario verrà rilasciato objectGuid come ID non modificabile. È possibile costruire il set di regole attestazioni personalizzate come descritto di seguito:

**Regola 1: attributi della query**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

In questa regola si esegue semplicemente una query sui valori di ms-ds-consistencyguid e objectguid per l'utente da Active Directory. Sostituire il nome dell'archivio con il nome appropriato disponibile nella distribuzione di AD FS e il tipo di attestazioni con il tipo appropriato presente nella federazione e definito per objectguid e ms-ds-consistencyguid. Nell'esempio i tipi di attestazione personalizzati sono stati definiti nell'ambiente di test.

L'uso di "add" anziché "issue" permette di evitare di aggiungere un rilascio per l'entità e di usare semplicemente i valori come valori intermedi. L'attestazione verrà rilasciata in una regola successiva, dopo aver stabilito quale valore usare come ID non modificabile.

**Regola 2: verificare la presenza di ms-ds-consistencyguid per l'utente**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Questa regola definisce semplicemente un flag temporaneo "idflag", che è impostato su "useguid" se non è presente un ms-ds-concistencyguid popolato per l'utente. Questo perché AD FS non ammette attestazioni vuote. Quando si aggiungono le attestazioni http://contoso.com/ws/2016/02/identity/claims/objectguid e http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid nella regola 1, l'attestazione msdsconsistencyguid è disponibile SOLO se il valore è popolato per l'utente. In caso contrario, AD FS lo considera come un valore vuoto e lo rimuove immediatamente. Tutti gli oggetti hanno objectguid, quindi l'attestazione è sempre presente dopo l'esecuzione della regola 1.

**Regola 3: rilasciare ms-ds-consistencyguid come ID non modificabile, se presente** c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"] => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Si tratta di un controllo EXIST implicito. Se il valore per l'attestazione esiste, viene emesso come ID non modificabile. Si noti che nell'esempio viene emessa l'attestazione nameidentifier. Sarà necessario sostituirla con il tipo di attestazione appropriato per l'ID non modificabile nel proprio ambiente.

**Regola 4: rilasciare objectguid come ID non modificabile se ms-ds-consistencyGuid non è presente**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

Questa regola esegue una semplice verifica del flag temporaneo "idflag" e, in base al valore, decide se rilasciare o meno l'attestazione.

> [AZURE.NOTE] La sequenza delle regole è importante.

<!---HONumber=AcomDC_0427_2016-->