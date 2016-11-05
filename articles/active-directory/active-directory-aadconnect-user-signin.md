---
title: Azure AD Connect - Accesso utente | Microsoft Docs
description: Accesso utente Azure Connect AD per le impostazioni personalizzate.
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: billmath

---
# <a name="azure-ad-connect-user-sign-on-options"></a>Opzioni di accesso utente di Azure AD Connect
Azure AD Connect consente agli utenti di accedere alle risorse cloud e locali utilizzando le stesse password.  È possibile abilitare questa opzione in diversi modi.

## <a name="choosing-a-user-sign-in-method"></a>Scelta di un metodo di accesso utente
Per la maggior parte delle organizzazioni che desiderano semplicemente abilitare l'accesso utente a Office 365, alle applicazioni SaaS e ad altre risorse basate su AD Azure, è consigliabile l'opzione di sincronizzazione password predefinita.
Alcune organizzazioni, tuttavia, hanno particolari motivi per l'utilizzo di un’opzione di accesso federato, ad esempio ADFS.  Di seguito ne sono elencati alcuni:

* L'organizzazione dispone già di ADFS o di un provider di federazione di terze parti distribuito
* I criteri di protezione vietano la sincronizzazione gli hash delle password nel cloud
* Quando si accede alle risorse del cloud da computer appartenenti al dominio nella rete aziendale, è necessario che gli utenti non vadano incontro ad alcun problema nell’uso dell’SSO (niente richieste di password aggiuntive)
* Sono necessarie alcune funzionalità specifiche di ADFS
  * Autenticazione a più fattori in locale utilizzando un provider di terze parti o smart card (informazioni sui provider di autenticazione a più fattori di terze parti per ADFS in Windows Server 2012 R2)
  * Funzionalità di integrazione di Active Directory, ad esempio il blocco degli account soft o criteri relativi alle ore di lavoro e alla password in Active Directory
  * Accesso condizionale alle risorse sia locali che nel cloud mediante la registrazione del dispositivo, il join Azure AD o i criteri Intune MDM

### <a name="password-synchronization"></a>Sincronizzazione delle password
Con la sincronizzazione delle password, gli hash delle password degli utenti vengono sincronizzati da Active Directory locale ad Azure AD.  Quando le password vengono modificate o reimpostate localmente, le nuove password vengono sincronizzate immediatamente in Azure AD in modo che gli utenti possano utilizzare sempre la stessa password per le risorse cloud come avviene localmente.  Le password non vengono mai inviate ad Azure AD né archiviate in Azure AD in testo non crittografato.
La sincronizzazione delle password può essere utilizzata insieme al writeback delle password per consentire la reimpostazione autonoma delle password del servizio in Azure AD.

<center>![Cloud](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[Ulteriori informazioni sulla sincronizzazione delle password](active-directory-aadconnectsync-implement-password-synchronization.md)

### <a name="federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm"></a>Federazione che utilizza un ADFS nuovo o esistente nella farm di Windows Server 2012 R2
Grazie all’accesso federato, gli utenti possono accedere ai servizi basati su Azure AD con le loro password locali e, se utilizzano la rete aziendale, senza dover immettere nuovamente le password.  L’opzione di federazione con ADFS consente di distribuire un ADFS nuovo o di specificarne uno esistente nella farm Windows Server 2012 R2.  Se si sceglie di specificare una farm esistente, Azure AD Connect configurerà l’attendibilità tra la farm e Azure AD in modo che gli utenti possano effettuare l’accesso.

<center>![Cloud](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="to-deploy-federation-with-ad-fs-in-windows-server-2012-r2,-you-will-need-the-following"></a>Per distribuire la federazione con ADFS in Windows Server 2012 R2, è necessario quanto segue
Se si distribuisce una nuova farm:

* Un server Windows Server 2012 R2 per il server federativo.
* Un server Windows Server 2012 R2 per il ruolo Proxy applicazione Web.
* Un file PFX con un certificato SSL per il nome del servizio federativo desiderato, ad esempio fs.contoso.com.

Se si distribuisce una nuova farm o si utilizza una farm esistente:

* Credenziali di amministratore locale nei server federativi in uso.
* Credenziali di amministratore locale in qualsiasi server di gruppi di lavoro (non inserito in un dominio) in cui si intende distribuire il ruolo Proxy applicazione Web.
* Il computer in cui viene eseguita la procedura guidata deve essere in grado di connettersi a qualsiasi altro computer sul quale si desidera installare ADFS o il Proxy applicazione Web tramite Gestione remota Windows.

#### <a name="sign-on-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Accedere utilizzando una versione precedente di ADFS o una soluzione di terze parti
Se è stato già configurato l’accesso al cloud tramite una versione precedente di ADFS (ad esempio ADFS 2.0) o un provider di federazione di terze parti, è possibile ignorare la configurazione dell’accesso utenti tramite Azure AD Connect.  In tal modo sarà possibile ottenere la sincronizzazione più recente e altre funzionalità di Azure AD Connect continuando comunque a utilizzare la soluzione esistente per l'accesso.

## <a name="user-sign-in-and-user-principal-name-(upn)"></a>Accesso utente e nome dell'entità utente (UPN)
### <a name="understanding-user-principal-name"></a>Informazioni sul nome dell'entità utente
In Active Directory il suffisso UPN predefinito è il nome DNS del dominio in cui viene creato l'account utente. Nella maggior parte dei casi si tratta del nome di dominio registrato come dominio dell'organizzazione su Internet. Tuttavia, è possibile aggiungere altri suffissi UPN usando Domini e trust di Active Directory.

L'UPN dell'utente è in formato username@domai. Ad esempio, per l'utente John del dominio di Active Directory contoso.com l'UPN potrebbe essere john@contoso.com. L'UPN dell'utente è basato su RFC 822. Anche se UPN  e posta elettronica condividono lo stesso formato, il valore UPN per un utente non è necessariamente uguale all'indirizzo di posta elettronica dell'utente.

### <a name="user-principal-name-in-azure-ad"></a>Nome dell'entità utente in Azure AD
La procedura guidata di Azure AD Connect usa l'attributo userPrincipalName o, nelle installazioni personalizzate, consente di specificare l'attributo da usare in locale come nome dell'entità utente in Azure AD. Questo è il valore che verrà usato per l'accesso ad Azure AD. Se il valore dell'attributo del nome dell'entità utente non corrisponde a un dominio verificato in Azure AD, Azure AD lo sostituisce con un valore .onmicrosoft.com predefinito.

Ogni directory in Azure Active Directory include un nome di dominio predefinito nel formato contoso.onmicrosoft.com che consente di iniziare a usare Azure o altri servizi Microsoft. È possibile migliorare e semplificare l'esperienza di accesso usando i domini personalizzati. Per informazioni sui nomi di dominio personalizzato in Azure AD e sulla verifica dei domini, vedere [Aggiungere un nome di dominio personalizzato ad Azure Active Directory](active-directory-add-domain.md#add-your-custom-domain-name-to-azure-active-directory)

## <a name="azure-ad-sign-in-configuration"></a>Configurazione dell'accesso ad Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Configurazione dell'accesso ad Azure AD con Azure AD Connect
L'esperienza di accesso ad Azure AD dipende dalla possibilità per Azure AD di associare il suffisso del nome dell'entità utente dell'utente da sincronizzare a uno dei domini personalizzati verificati nella directory di Azure AD. Azure AD Connect offre una guida alla  configurazione delle impostazioni di accesso in Azure AD, in modo che per l'utente l'esperienza di accesso nel cloud sia simile all'esperienza locale. Azure AD Connect elenca i suffissi UPN definiti per i domini, tenta di associarli a un dominio personalizzato in Azure AD e indica l'azione appropriata da eseguire.
Nella pagina di accesso di Azure sono riportati i suffissi UPN definiti per l'istanza locale di Active Directory ed è visualizzato lo stato corrispondente per ogni suffisso. I valori dello stato possono essere i seguenti:

* Verificato: Azure AD Connect ha rilevato un dominio verificato corrispondente in Azure AD e non è necessaria alcuna azione
* Non verificato: Azure AD Connect ha rilevato un dominio corrispondente in Azure AD ma non è verificato. È necessario verificare il dominio personalizzato per garantire che il suffisso UPN degli utenti non venga sostituito dal suffisso predefinito onmicrosoft.com dopo la sincronizzazione.
* Non aggiunto: Azure AD Connect non ha rilevato un dominio personalizzato corrispondente al suffisso UPN. È necessario aggiungere e verificare un dominio personalizzato corrispondente al suffisso UPN per garantire che il suffisso UPN degli utenti non venga sostituito dal suffisso predefinito onmicrosoft.com dopo la sincronizzazione.

Nella pagina di accesso di Azure AD sono elencati i suffissi UPN definiti per il servizio Active Directory locale e il dominio personalizzato corrispondente in Azure AD con lo stato attuale della verifica. Nell'installazione personalizzata, è ora possibile selezionare l'attributo per il nome dell'entità utente nella pagina di **accesso ad Azure AD** .

![Pagina di accesso di Azure AD](.\\media\\active-directory-aadconnect-user-signin\\custom_azure_sign_in.png)

È possibile fare clic sul pulsante di aggiornamento per recuperare nuovamente lo stato più recente dei domini personalizzati da Azure AD.

### <a name="selecting-attribute-for-user-principal-name-in-azure-ad"></a>Selezione dell'attributo per il nome dell'entità utente in Azure AD
UserPrincipalName: l'attributo userPrincipalName verrà usato dagli utenti per l'accesso ad Azure AD e Office 365. I domini utilizzati, noti anche come suffisso UPN, devono essere verificati in Azure AD prima che gli utenti vengano sincronizzati. Si consiglia di mantenere l'attributo userPrincipalName predefinito. Se questo attributo non è instradabile e non può essere verificato, è possibile selezionarne un altro, ad esempio posta elettronica, come attributo contenente l'ID di accesso. Questo attributo è noto come ID alternativo. Il valore dell'attributo ID alternativo deve essere conforme allo standard RFC822. È possibile usare un ID alternativo sia con una soluzione di accesso Single Sign-On tramite password che Single Sign-On federativo.

> [!NOTE]
> L'uso di un ID alternativo non è compatibile con tutti i carichi di lavoro di Office 365. Per altre informazioni, vedere [Configurazione dell'ID di accesso alternativo](https://technet.microsoft.com/library/dn659436.aspx).
> 
> 

#### <a name="different-custom-domain-states-and-effect-on-azure-sign-in-experience"></a>Stati diversi del dominio personalizzato ed effetto sull'esperienza di accesso ad Azure
È molto importante comprendere la relazione tra gli stati dei domini personalizzati nella directory di Azure AD e i suffissi UPN definiti a livello locale. Di seguito sono illustrate le diverse esperienze di accesso possibili in Azure quando si imposta la sincronizzazione usando AD Azure Connect.

Per le informazioni seguenti, si supponga di usare il suffisso UPN contoso.com nella directory locale come parte dell'UPN, ad esempio user@contoso.com.

###### <a name="express-settings-/-password-synchronization"></a>Impostazioni rapide / Sincronizzazione delle password
| Stato | Effetto sull'esperienza di accesso degli utenti in Azure |
|:---:|:--- |
| Non aggiunto |In questo caso non è stato aggiunto alcun dominio personalizzato per contoso.com nella directory di Azure AD. Gli utenti che usano UPN locali con suffisso @contoso.com, non potranno usare il proprio UPN locale per eseguire l'accesso ad Azure. Dovranno invece usare un nuovo UPN disponibile in Azure AD aggiungendo il suffisso per la directory predefinita di Azure AD. Ad esempio, se si sincronizzano gli utenti con la directory di Azure AD azurecontoso.onmicrosoft.com, all'utente locale user@contoso.com verrà assegnato l'UPN user@azurecontoso.onmicrosoft.com |
| Non verificato |In questo caso il dominio personalizzato contoso.com viene aggiunto nella directory di Azure AD, ma non è ancora verificato. Se si procede con la sincronizzazione degli utenti senza verificare il dominio, agli utenti verrà assegnato un nuovo UPN da Azure AD esattamente come accade nello scenario "Non aggiunto". |
| Verified |In questo caso il dominio personalizzato contoso.com è già stato aggiunto e verificato in Azure AD per il suffisso UPN. Gli utenti potranno usare i propri nomi di entità utente locali, ad esempio user@contoso.com,, per accedere ad Azure dopo la sincronizzazione con Azure AD |

###### <a name="ad-fs-federation"></a>Federazione AD FS
Non è possibile creare una federazione con il dominio predefinito. onmicrosoft.com in Azure AD o un dominio personalizzato non verificato in Azure AD. Quando si esegue la procedura guidata di  Azure AD Connect, se si seleziona un dominio non verificato per creare una federazione con Azure AD Connect verrà richiesto di creare i record necessari dove è ospitato il servizio DNS per il dominio. Per altre informazioni, vedere [qui](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Se si seleziona l'opzione di accesso utente come "Federazione con AD FS", è necessario avere un dominio personalizzato per continuare con la creazione di una federazione in Azure AD. Ai fini di questa discussione, significa che si dovrà aggiungere un dominio personalizzato contoso.com nella directory di Azure AD.

| Stato | Effetto sull'esperienza di accesso degli utenti in Azure |
|:---:|:--- |
| Non aggiunto |In questo caso Azure AD Connect non è in grado di trovare un dominio personalizzato corrispondente per il suffisso UPN contoso.com nella directory di Azure AD. È necessario aggiungere un dominio personalizzato contoso.com se è necessario che gli utenti accedano usando AD FS con il proprio UPN locale, ad esempio user@contoso.com. |
| Non verificato |In questo caso Azure AD Connect richiederà i dettagli appropriati sul modo in cui si potrà verificare il dominio in una fase successiva |
| Verified |In questo caso è possibile procedere con la configurazione senza ulteriori azioni |

## <a name="changing-user-sign-in-method"></a>Modifica del metodo di accesso utente
È possibile modificare il metodo di accesso utente da Federazione a Sincronizzazione password usando le attività disponibili in Azure AD Connect dopo la configurazione iniziale di Azure AD Connect con la procedura guidata. Eseguire nuovamente la procedura guidata di Azure AD Connect; verrà visualizzato un elenco di attività che è possibile eseguire. Selezionare **Cambia l'accesso utente** dall'elenco di attività. 

![Cambia l'accesso utente](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Nella pagina successiva viene richiesto di inserire le credenziali per Azure AD.

![Connessione ad Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

Nella pagina **Accesso utente** selezionare **Sincronizzazione password**. La directory verrà modificata da federata a gestita.

![Connettersi ad Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin3.png)

> [!NOTE]
> Se si passa solo temporaneamente alla sincronizzazione delle password, selezionare l'opzione **Non convertire gli account utente**. Se l'opzione non viene selezionata, ogni utente verrà convertito in federato, operazione che potrà richiedere diverse ore.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

Per altre informazioni, vedere [Azure AD Connect: Concetti relativi alla progettazione](active-directory-aadconnect-design-concepts.md)

<!--HONumber=Oct16_HO2-->


