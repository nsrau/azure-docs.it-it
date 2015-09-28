<properties
	pageTitle="Accesso condizionale per le applicazioni pubblicate con Proxy dell'applicazione AD Azure"
	description="Viene descritto come configurare l'accesso condizionale per le applicazioni pubblicate per l'accesso in remoto tramite Proxy dell’applicazione AD Azure."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2015"
	ms.author="rkarlin"/>

# Utilizzo di access condizionale
> [AZURE.NOTE]Il proxy di applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).

È ora possibile abilitare le regole di accesso per garantire l'accesso condizionale a utenti e gruppi di accesso alle applicazioni pubblicate tramite Proxy dell'applicazione. Ciò consente di: - Richiedere l’autenticazione a più fattori per ogni applicazione - richiedere l'autenticazione a più fattori solo quando gli utenti non sono al lavoro - Impedire agli utenti l'accesso all'applicazione quando non sono al lavoro

Queste regole possono essere applicate a tutti gli utenti e gruppi o solo a utenti e gruppi specifici. Per impostazione predefinita, la regola verrà applicata a tutti gli utenti che dispongono dell'accesso all'applicazione. Tuttavia la regola può essere limitata agli utenti che sono membri di gruppi di sicurezza specificati. Quando un utente accede a un'applicazione federata che utilizza OAuth 2.0, OpenID Connect, SAML o WS-Federation, vengono valutate le regole di accesso. Inoltre, vengono valutate le regole di accesso quando OAuth 2.0 e OpenID si collegano quando viene utilizzato un token di aggiornamento per acquisire un token di accesso.

## Prerequisiti per l'accesso condizionale

- Sottoscrizione di Azure Active Directory Premium 
- Un tenant di Azure Active Directory federato o gestito 
- I tenant federata richiedono che l'autenticazione a più fattori (MFA) sia attivata 

![](http://i.imgur.com/rv28onQ.png)

## Configurare l'autenticazione a più fattori per applicazione
1. Accedere come amministratore al portale di gestione di Azure.
2. Passare ad Active Directory e selezionare la directory in cui si desidera abilitare il proxy dell'applicazione.
3. Fare clic su **Applicazioni** e scorrere verso il basso fino alla sezione **Regole di accesso**. La sezione regole di accesso vengono visualizzate solo per le applicazioni pubblicate con il Proxy dell'applicazione che utilizza l'autenticazione federata.
4. Attivare la regola selezionando **Abilitare le regole di accesso** su **On**.
5. Specificare gli utenti e i gruppi a cui verranno applicate le regole. Utilizzare il tasto **Aggiungi gruppo** per selezionare uno o più gruppi a cui verrà applicata la regola di accesso. Questa finestra di dialogo può essere utilizzata anche per rimuovere i gruppi selezionati. Quando si selezionano le regole da applicare ai gruppi, le regole di accesso possono essere imposte solo agli utenti che appartengono a uno dei gruppi di sicurezza specificati. <br> Per escludere in modo esplicito i gruppi di protezione dalla regola, controllare **Tranne** e specificare uno o più gruppi. Gli utenti che sono membri di un gruppo nell'elenco Tranne non dovranno eseguire l'autenticazione a più fattori. <br>Se un utente è stato configurato utilizzando la funzionalità di autenticazione a più fattori per utente, questa impostazione avrà la precedenza sulle regole dell’autenticazione a più fattori dell’applicazione. Ciò significa che a un utente che è stato configurato per l'autenticazione a più fattori per utente verrà richiesto di eseguire l'autenticazione a più fattori, anche se è stato esentato dalle regole di autenticazione a più fattori dell'applicazione. [Ulteriori informazioni sulle impostazioni dell’autenticazione a più fattori per utente](../multi-factor-authentication/multi-factor-authentication.md). 
6. Selezionare la regola di accesso che si desidera impostare:
- **Richiedere l’autenticazione a più fattori**: Agli utenti a cui si applicano le regole di accesso verrà richiesta l'autenticazione a più fattori prima di accedere all'applicazione a cui viene applicata la regola.
- **Richiedere l’autenticazione a più fattori quando non è al lavoro**: Gli utenti che tentano di accedere all'applicazione da un indirizzo IP attendibile non dovranno eseguire l'autenticazione a più fattori. Gli intervalli di indirizzi IP attendibili possono essere configurati nella pagina Impostazioni di autenticazione a più fattori.
- **Bloccare l'accesso quando non si è al lavoro**: gli utenti che tentano di accedere all'applicazione dall'esterno della rete aziendale non saranno in grado di accedere all'applicazione.


## Configurazione di autenticazione a più fattori per servizi di federazione
Per tenant federati, l’autenticazione a più fattori (MFA) potrebbe eseguita da Azure Active Directory o dal server ADFS locale. Per impostazione predefinita, verrà eseguita l'autenticazione a più fattori in qualsiasi pagina ospitata da Azure Active Directory. Per configurare l'autenticazione a più fattori in locale, eseguire Windows PowerShell e utilizzare la proprietà – SupportsMFA per impostare il modulo di Azure AD. Nell'esempio seguente viene illustrato come abilitare l'autenticazione a più fattori in locale utilizzando il [cmdlet Set-MsolDomainFederationSettings](https://msdn.microsoft.com/library/azure/dn194088.aspx) del tenant contoso.com: `Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true ` Oltre a impostare questo flag, l'istanza di tenant federato ADFS deve essere configurata per eseguire l'autenticazione a più fattori. Seguire le istruzioni per la [Distribuzione di Microsoft Azure Multi-Factor Authentication in locale](..multi-factor-authentication-get-started-server.md).
## Risorse aggiuntive

* [Iscriversi ad Azure come organizzazione](..sign-up-organization.md)
* [Identità di Azure](..fundamentals-identity.md)

<!---HONumber=Sept15_HO3-->