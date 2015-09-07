<properties 
	pageTitle="Azure AD Connect - Accesso utente"
	description="Accesso utente Azure Connect AD per le impostazioni personalizzate."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>



# Opzioni di accesso utente di Azure AD Connect

Azure AD Connect consente agli utenti di accedere alle risorse cloud e locali utilizzando le stesse password. È possibile abilitare questa opzione in diversi modi.


### Sincronizzazione delle password
Con la sincronizzazione delle password, gli hash delle password degli utenti vengono sincronizzati da Active Directory locale ad Azure AD. Quando le password vengono modificate o reimpostate localmente, le nuove password vengono sincronizzate immediatamente in Azure AD in modo che gli utenti possano utilizzare sempre la stessa password per le risorse cloud come avviene localmente. Le password non vengono mai inviate ad Azure AD né archiviate in Azure AD in testo non crittografato. La sincronizzazione delle password può essere utilizzata insieme al writeback delle password per consentire la reimpostazione autonoma delle password del servizio in Azure AD.

<center>![Cloud](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[Ulteriori informazioni sulla sincronizzazione delle password](https://msdn.microsoft.com/library/azure/dn246918.aspx)


### Federazione che utilizza un ADFS nuovo o esistente nella farm di Windows Server 2012 R2
Grazie all’accesso federato, gli utenti possono accedere ai servizi basati su Azure AD con le loro password locali e, se utilizzano la rete aziendale, senza dover immettere nuovamente le password. L’opzione di federazione con ADFS consente di distribuire un ADFS nuovo o di specificarne uno esistente nella farm Windows Server 2012 R2. Se si sceglie di specificare una farm esistente, Azure AD Connect configurerà l’attendibilità tra la farm e Azure AD in modo che gli utenti possano effettuare l’accesso.

<center>![Cloud](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### Per distribuire la federazione con ADFS in Windows Server 2012 R2, è necessario quanto segue
Se si distribuisce una nuova farm:

- Un server Windows Server 2012 R2 per il server federativo.
- Un server Windows Server 2012 R2 per il ruolo Proxy applicazione Web.
- Un file PFX con un certificato SSL per il nome del servizio federativo desiderato, ad esempio fs.contoso.com.

Se si distribuisce una nuova farm o si utilizza una farm esistente:

- Credenziali di amministratore locale nei server federativi in uso.
- Credenziali di amministratore locale in qualsiasi server di gruppi di lavoro (non inserito in un dominio) in cui si intende distribuire il ruolo Proxy applicazione Web.
- Il computer in cui viene eseguita la procedura guidata deve essere in grado di connettersi a qualsiasi altro computer sul quale si desidera installare ADFS o il Proxy applicazione Web tramite Gestione remota Windows.

#### Accedere utilizzando una versione precedente di ADFS o una soluzione di terze parti
Se è stato già configurato l’accesso al cloud tramite una versione precedente di ADFS (ad esempio ADFS 2.0) o un provider di federazione di terze parti, è possibile ignorare la configurazione dell’accesso utenti tramite Azure AD Connect. In tal modo sarà possibile ottenere la sincronizzazione più recente e altre funzionalità di Azure AD Connect continuando comunque a utilizzare la soluzione esistente per l'accesso.

### Scelta di un metodo di accesso utente per l'organizzazione
Per la maggior parte delle organizzazioni che desiderano semplicemente abilitare l'accesso utente a Office 365, alle applicazioni SaaS e ad altre risorse basate su AD Azure, è consigliabile l'opzione di sincronizzazione password predefinita. Alcune organizzazioni, tuttavia, hanno particolari motivi per l'utilizzo di un’opzione di accesso federato, ad esempio ADFS. Di seguito ne sono elencati alcuni:

- L'organizzazione dispone già di ADFS o di un provider di federazione di terze parti distribuito
- I criteri di protezione vietano la sincronizzazione gli hash delle password nel cloud
- Quando si accede alle risorse del cloud da computer appartenenti al dominio nella rete aziendale, è necessario che gli utenti non vadano incontro ad alcun problema nell’uso dell’SSO (niente richieste di password aggiuntive)
- Sono necessarie alcune funzionalità specifiche di ADFS
	- Autenticazione a più fattori in locale utilizzando un provider di terze parti o smart card (informazioni sui provider di autenticazione a più fattori di terze parti per ADFS in Windows Server 2012 R2)
	- Funzionalità di integrazione di Active Directory, ad esempio il blocco degli account soft o criteri relativi alle ore di lavoro e alla password in Active Directory
	- Accesso condizionale alle risorse sia locali che nel cloud mediante la registrazione del dispositivo, il join Azure AD o i criteri Intune MDM
 

<!---HONumber=August15_HO9-->