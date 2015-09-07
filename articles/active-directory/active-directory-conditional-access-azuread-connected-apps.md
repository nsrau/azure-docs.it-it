<properties
	pageTitle="Anteprima dell'accesso condizionale di Azure per app SaaS| Microsoft Azure"
	description="L'accesso condizionale in Azure AD consente la configurazione di una regola di accesso con autenticazione a più fattori per ogni applicazione e la possibilità di bloccare l'accesso agli utenti su una rete non affidabile."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="femila"/>

# Anteprima dell'accesso condizionale di Azure per app SaaS

L'accesso condizionale di Azure per app SaaS è disponibile per l'anteprima pubblica. La versione di anteprima consente la configurazione di una regola di accesso con autenticazione a più fattori per ogni applicazione e la possibilità di bloccare l'accesso agli utenti su una rete non affidabile.

La regola di autenticazione a più fattori può essere applicata a tutti gli utenti assegnati all'applicazione oppure solo agli utenti inclusi in gruppi di sicurezza specificati. Gli utenti possono essere esclusi dal requisito di autenticazione a più fattori se accedono all'applicazione da un indirizzo IP all'interno della rete dell'organizzazione. Queste funzionalità saranno disponibili per i clienti che hanno acquistato una licenza di Azure Active Directory Premium.

## Prerequisiti dello scenario
* Sottoscrizione di Azure Active Directory Premium

* Tenant Azure Active Directory federato o gestito

* Autenticazione a più fattori (MFA) abilitata per i tenant federati.

## Problemi noti di questa versione di anteprima
Questa versione di anteprima si applica alle applicazioni SaaS federate preintegrate, alle applicazioni che usano l'accesso Single-Sign On basato su password, applicazioni registrate, sviluppate e line-of-business e Proxy applicazione di Azure AD. Altre applicazioni sono ancora in fase di abilitazione.

##Configurare le regole di accesso per ogni applicazione

Questa sezione descrive come configurare le regole di accesso per ogni applicazione.

1. Accedere al portale di Microsoft Azure come Amministratore.
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

![Impostazione delle regole di accesso condizionale con MFA](./media/active-directory-conditional-access/conditionalaccess-saas-apps.jpg)

##Regole di accesso condizionale con MFA
Se un utente è stato configurato usando la funzionalità di autenticazione a più fattori per utente, questa impostazione per l'utente avrà la precedenza rispetto alle regole di autenticazione a più fattori per l'app. Di conseguenza, un utente configurato per l'autenticazione a più fattori per utente dovrà eseguire l'autenticazione a più fattori anche se è stato escluso dalle regole di autenticazione a più fattori per l'applicazione. Altre informazioni sull'autenticazione a più fattori e le impostazioni per utente.

###Opzioni delle regole di accesso
La versione di anteprima corrente supporta le opzioni seguenti:

* **Richiedi autenticazione a più fattori**: questa opzione consente agli utenti a cui si applicano le regole di accesso di completare l'autenticazione a più fattori prima di accedere all'applicazione a cui si applicano i criteri.

* **Richiedi autenticazione a più fattori quando non al lavoro**: con questa opzione un utente che accede da un indirizzo IP attendibile non deve eseguire l'autenticazione a più fattori. Gli intervalli di indirizzi IP attendibili possono essere configurati nella pagina delle impostazioni dell'autenticazione a più fattori.

* **Blocca l'accesso quando non al lavoro**: con questa opzione un utente che non accede da un indirizzo IP attendibile verrà bloccato. Gli intervalli di indirizzi IP attendibili possono essere configurati nella pagina delle impostazioni dell'autenticazione a più fattori.

###Impostazione dello stato delle regole
Lo stato delle regole di accesso permette di attivare o disattivare le regole. Quando le regole di accesso sono disattivate, il requisito di autenticazione a più fattori non viene applicato.

### Valutazione delle regole di accesso

Quando un utente accede a un'applicazione federata che utilizza OAuth 2.0, OpenID Connect, SAML o WS-Federation, vengono valutate le regole di accesso. Le regole di accesso vengono valutate anche quando con OAuth 2.0 e OpenID Connect viene usato un token di aggiornamento per acquisire un token di accesso. Se la valutazione dei criteri non riesce quando si usa un token di aggiornamento, viene restituito l'errore invalid\_grant, che indica che l'utente deve eseguire di nuovo l'autenticazione al client. Configurare i servizi federativi per fornire l'autenticazione a più fattori

Per tenant federati, l’autenticazione a più fattori (MFA) potrebbe eseguita da Azure Active Directory o dal server ADFS locale.

Per impostazione predefinita, MFA viene eseguita in una pagina ospitata da Azure Active Directory. Per configurare MFA in locale, la proprietà -SupportsMFA deve essere impostata su true in Azure Active Directory, mediante il modulo di Azure AD per Windows PowerShell.

L'esempio seguente illustra come abilitare MFA in locale usando il [cmdlet Set-MsolDomainFederationSettings cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx) nel tenant contoso.com:

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

Oltre a impostare questo flag, l'istanza di AD FS del tenant federato deve essere configurata in modo da eseguire l'autenticazione a più fattori. Seguire le istruzioni per la distribuzione locale di Azure Multi-Factor Authentication.

<!---HONumber=August15_HO9-->