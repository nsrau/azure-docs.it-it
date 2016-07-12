<properties
	pageTitle="Anteprima dell'accesso condizionale di Azure per app SaaS| Microsoft Azure"
	description="L'accesso condizionale in Azure AD consente la configurazione di una regola di accesso con autenticazione a più fattori per ogni applicazione e la possibilità di bloccare l'accesso agli utenti su una rete non affidabile. "
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2016"
	ms.author="femila"/>

# Guida introduttiva all'accesso condizionale ad Azure AD  

L'accesso condizionale di Azure Active Directory per app SaaS e app connesse di Azure AD è disponibile per l'anteprima pubblica. L'anteprima consente di configurare l'accesso condizionale in base al gruppo, alla posizione e alla sensibilità dell'applicazione.

Le regole di Multi-Factor Authentication possono essere applicate a tutti gli utenti assegnati all'applicazione oppure solo agli utenti inclusi in gruppi di sicurezza specifici. Con l'accesso condizionale in base alla sensibilità dell'applicazione è possibile configurare regole di accesso Multi-Factor Authentication per ogni applicazione, per bloccare l'accesso agli utenti su una rete non affidabile. Gli utenti possono essere esclusi dal requisito di autenticazione a più fattori se accedono all'applicazione da un indirizzo IP all'interno della rete dell'organizzazione.

Queste funzionalità saranno disponibili per i clienti che hanno acquistato una licenza di Azure Active Directory Premium.

## Prerequisiti dello scenario
* Licenza di Azure Active Directory Premium

* Tenant di Azure Active Directory federato o gestito

* I tenant federati richiedono l'abilitazione dell'autenticazione Multi-Factor Authentication.

## Problemi noti di questa versione di anteprima
Questa versione di anteprima si applica alle applicazioni SaaS federate preintegrate, alle applicazioni che usano l'accesso Single Sign-On basato su password, applicazioni registrate, sviluppate e line-of-business e Proxy applicazione di Azure AD. Altre applicazioni sono ancora in fase di abilitazione.

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

Questa sezione contiene una descrizione dettagliata delle regole di accesso supportate nella versione di anteprima dell'accesso condizionale alle applicazioni di Azure.

### Definizione degli utenti a cui applicare le regole di accesso

Per impostazione predefinita, i criteri verranno applicati a tutti gli utenti che hanno accesso all'applicazione. Tuttavia, è anche possibile limitare i criteri agli utenti membri dei gruppi di sicurezza specificati. Il pulsante **Aggiungi gruppo** permette di selezionare uno o più gruppi a cui applicare la regola di accesso nella finestra di dialogo di selezione dei gruppi. Questa finestra di dialogo può essere utilizzata anche per rimuovere i gruppi selezionati. Una volta selezionate le regole da applicare ai gruppi, le regole di accesso verranno applicate solo agli utenti che appartengono a uno dei gruppi di sicurezza specificati.

È anche possibile escludere in modo esplicito i gruppi di sicurezza dai criteri selezionando l'opzione relativa alle eccezioni e quindi specificando uno o più gruppi. Gli utenti membri di un gruppo nell'elenco delle eccezioni non saranno soggetti al requisito di autenticazione a più fattori, anche se sono membri di un gruppo a cui si applica la regola di accesso. La regola di accesso mostrata di seguito impone a tutti gli utenti del gruppo Responsabili di usare l'autenticazione a più fattori quando accedono all'applicazione.

![Impostazione delle regole di accesso condizionale con MFA](./media/active-directory-conditional-access/conditionalaccess-saas-apps.png)

## Regole di accesso condizionale con MFA
Se un utente è stato configurato usando la funzionalità di autenticazione Multi-Factor Authentication per utente, questa impostazione per l'utente andrà ad aggiungersi alle regole di Multi-Factor Authentication dell'app. Di conseguenza, un utente configurato per l'autenticazione a più fattori per utente dovrà eseguire l'autenticazione a più fattori anche se è stato escluso dalle regole di autenticazione a più fattori per l'applicazione. Altre informazioni sull'autenticazione a più fattori e le impostazioni per utente.

### Opzioni delle regole di accesso
La versione di anteprima corrente supporta le opzioni seguenti:

* **Richiedi autenticazione a più fattori**: con questa opzione, gli utenti a cui si applicano le regole di accesso dovranno completare la procedura Multi-Factor Authentication prima di accedere all'applicazione a cui si applica il criterio.

* **Richiedi autenticazione a più fattori quando non al lavoro**: con questa opzione, un utente che accede da un indirizzo IP attendibile non deve eseguire la procedura Multi-Factor Authentication. Gli intervalli di indirizzi IP attendibili possono essere configurati nella pagina delle impostazioni dell'autenticazione Multi-Factor Authentication oppure utilizzando gli appositi intervalli di indirizzi IP pubblici nella scheda di configurazione della directory.

* **Blocca l'accesso quando non al lavoro**: con questa opzione, un utente che non accede da un indirizzo IP attendibile verrà bloccato. Gli intervalli di indirizzi IP attendibili possono essere configurati nella pagina Impostazioni di autenticazione a più fattori.

### Impostazione dello stato delle regole
Lo stato delle regole di accesso permette di attivare o disattivare le regole. Quando le regole di accesso sono disattivate, il requisito di autenticazione Multi-Factor Authentication non viene applicato.

### Valutazione delle regole di accesso

Quando un utente accede a un'applicazione federata che utilizza OAuth 2.0, OpenID Connect, SAML o WS-Federation, vengono valutate le regole di accesso. Inoltre, vengono valutate le regole di accesso quando OAuth 2.0 e OpenID si collegano quando viene utilizzato un token di aggiornamento per acquisire un token di accesso. Se la valutazione dei criteri non riesce quando si usa un token di aggiornamento, viene restituito l'errore invalid\_grant, che indica che l'utente deve eseguire di nuovo l'autenticazione al client. Configurare i servizi federativi per fornire l'autenticazione a più fattori

Per tenant federati, l’autenticazione a più fattori (MFA) potrebbe eseguita da Azure Active Directory o dal server ADFS locale.

Per impostazione predefinita, MFA viene eseguita in una pagina ospitata da Azure Active Directory. Per configurare l'MFA locale è necessario impostare la proprietà –SupportsMFA su **true** in Azure Active Directory usando il modulo di Azure AD per Windows PowerShell.

L'esempio seguente illustra come abilitare MFA in locale usando il [cmdlet Set-MsolDomainFederationSettings cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx) nel tenant contoso.com:

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

Oltre a impostare questo flag, l'istanza di AD FS del tenant federato deve essere configurata in modo da eseguire l'autenticazione a più fattori. Seguire le istruzioni per la distribuzione locale di Azure Multi-Factor Authentication.

## Articoli correlati

- [Protezione dell'accesso a Office 365 e ad altre app connesse ad Azure Active Directory](active-directory-conditional-access.md)
- [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0629_2016-->