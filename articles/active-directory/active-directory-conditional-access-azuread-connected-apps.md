<properties
	pageTitle="Accesso condizionale di Azure per app SaaS| Microsoft Azure"
	description="L'accesso condizionale in Azure AD consente la configurazione di una regola di accesso con autenticazione a più fattori per ogni applicazione e la possibilità di bloccare l'accesso agli utenti su una rete non affidabile. "
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>  

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/26/2016"
	ms.author="markvi"/>  

# Introduzione all'accesso condizionale di Azure Active Directory

L'accesso condizionale di Azure Active Directory per app [SaaS](https://azure.microsoft.com/overview/what-is-saas/) e app connesse ad Azure AD consente di configurare l'accesso condizionale in base a un gruppo, una posizione e alla sensibilità dell'applicazione.

Con l'accesso condizionale basato sulla sensibilità dell'applicazione è possibile configurare regole di accesso Multi-Factor Authentication (MFA) per ogni applicazione. L'autenticazione MFA per ogni applicazione consente di bloccare l'accesso per gli utenti non collegati a una rete attendibile. Le regole MFA possono essere applicate a tutti gli utenti assegnati all'applicazione oppure solo agli utenti inclusi in gruppi di sicurezza specifici. Gli utenti possono essere esclusi dal requisito di autenticazione MFA se accedono all'applicazione da un indirizzo IP all'interno della rete dell'organizzazione.

Queste funzionalità saranno disponibili per i clienti che hanno acquistato una licenza di Azure Active Directory Premium.

## Prerequisiti dello scenario
* Licenza di Azure Active Directory Premium

* Tenant di Azure Active Directory federato o gestito

* I tenant federati richiedono l'abilitazione dell'autenticazione Multi-Factor Authentication.

## Configurare le regole di accesso per ogni applicazione

Questa sezione descrive come configurare le regole di accesso per ogni applicazione.

1. Accedere al portale di Azure classico utilizzando un account amministratore globale in Azure AD.
2. Nel riquadro sinistro selezionare **Active Directory**.
3. Selezionare la propria directory nella scheda Directory.
4. Selezionare la scheda **Applicazioni**.
5. Selezionare l'applicazione per cui sarà impostata la regola.
6. Selezionare la scheda **Configura**.
7. Scorrere verso il basso fino alla sezione delle regole di accesso. Selezionare la regola di accesso da usare.
8. Specificare gli utenti ai quali sarà applicata la regola.
9. Abilitare i criteri impostando **Abilitato su Sì**.

##Informazioni sulle regole di accesso

Questa sezione contiene una descrizione dettagliata delle regole di accesso supportate nell'accesso condizionale alle applicazioni di Azure.

### Definizione degli utenti a cui applicare le regole di accesso

Per impostazione predefinita, i criteri verranno applicati a tutti gli utenti che hanno accesso all'applicazione. Tuttavia, è anche possibile limitare i criteri agli utenti membri dei gruppi di sicurezza specificati. Il pulsante **Aggiungi gruppo** permette di selezionare uno o più gruppi a cui applicare la regola di accesso nella finestra di dialogo di selezione dei gruppi. Questa finestra di dialogo può essere utilizzata anche per rimuovere i gruppi selezionati. Una volta selezionate le regole da applicare ai gruppi, le regole di accesso verranno applicate solo agli utenti che appartengono a uno dei gruppi di sicurezza specificati.

È anche possibile escludere in modo esplicito i gruppi di sicurezza dai criteri selezionando l'opzione relativa alle **eccezioni** e quindi specificando uno o più gruppi. Gli utenti membri di un gruppo nell'elenco delle **eccezioni** non saranno soggetti al requisito di Multi-Factor Authentication, anche se sono membri di un gruppo a cui si applica la regola di accesso. La regola di accesso mostrata di seguito impone a tutti gli utenti del gruppo Responsabili di usare l'autenticazione a più fattori quando accedono all'applicazione.

![Impostazione delle regole di accesso condizionale con MFA](./media/active-directory-conditional-access-azuread-connected-apps/conditionalaccess-saas-apps.png)  

## Regole di accesso condizionale con MFA
Se un utente è stato configurato usando la funzionalità di autenticazione Multi-Factor Authentication per utente, questa impostazione per l'utente andrà ad aggiungersi alle regole di Multi-Factor Authentication dell'app. Di conseguenza, un utente configurato per l'autenticazione a più fattori per utente dovrà eseguire l'autenticazione a più fattori anche se è stato escluso dalle regole di autenticazione a più fattori per l'applicazione. Altre informazioni sull'autenticazione a più fattori e le impostazioni per utente.

### Opzioni delle regole di accesso
Sono supportate le opzioni seguenti:

* **Richiedi autenticazione a più fattori**: gli utenti a cui si applicano le regole di accesso dovranno completare l'autenticazione a più fattori prima di accedere all'applicazione a cui si applica il criterio.

* **Richiedi autenticazione a più fattori quando non al lavoro**: un utente che accede da un indirizzo IP attendibile non deve eseguire l'autenticazione a più fattori. Gli intervalli di indirizzi IP attendibili possono essere configurati nella pagina Impostazioni di autenticazione a più fattori.

* **Blocca l'accesso quando non al lavoro**: un utente che non accede da un indirizzo IP attendibile verrà bloccato. Gli intervalli di indirizzi IP attendibili possono essere configurati nella pagina Impostazioni di autenticazione a più fattori.

### Impostazione dello stato delle regole
Lo stato delle regole di accesso permette di attivare o disattivare le regole. Quando le regole di accesso sono disattivate, il requisito dell'autenticazione a più fattori non viene applicato.

### Valutazione delle regole di accesso

Quando un utente accede a un'applicazione federata che utilizza OAuth 2.0, OpenID Connect, SAML o WS-Federation, vengono valutate le regole di accesso. Le regole di accesso vengono valutate anche quando OAuth 2.0 e OpenID Connect usano un token di aggiornamento per acquisire un token di accesso. Se la valutazione dei criteri ha esito negativo quando si usa un token di aggiornamento, viene restituito l'errore **invalid\_grant** che indica che l'utente deve eseguire di nuovo l'autenticazione al client.

###Configurare i servizi federativi per fornire l'autenticazione a più fattori

Per tenant federati, MFA potrebbe essere eseguita da Azure Active Directory o dal server AD FS locale.

Per impostazione predefinita, MFA viene eseguita in una pagina ospitata da Azure Active Directory. Per configurare MFA in locale è necessario impostare la proprietà **–SupportsMFA** su **true** in Azure Active Directory usando il modulo di Azure AD per Windows PowerShell.

L'esempio seguente illustra come abilitare MFA in locale usando il [cmdlet Set-MsolDomainFederationSettings cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx) nel tenant contoso.com:

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

Oltre a impostare questo flag, l'istanza di AD FS del tenant federato deve essere configurata in modo da eseguire l'autenticazione a più fattori. Seguire le istruzioni per la [distribuzione locale di Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## Articoli correlati

- [Protezione dell'accesso a Office 365 e ad altre app connesse ad Azure Active Directory](active-directory-conditional-access.md)
- [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0928_2016-->