<properties
	pageTitle="Informazioni sull'iscrizione self-service per Azure | Microsoft Azure"
	description="Una panoramica dell'iscrizione self-service per Azure, come gestire il processo di iscrizione e come."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="01/05/2016"
	ms.author="stevenpo"/>


# Informazioni sull'iscrizione self-service per Azure

In questo argomento viene illustrato il processo di iscrizione self-service (talvolta noto come abbonamento virale) e come acquisire un nome di dominio DNS.

## Perché usare l'iscrizione self-service?

- Acquisire più velocemente clienti su servizi da loro richiesti.
- Creare offerte (virali) basate sulla posta elettronica per un servizio.
- Creare flussi di iscrizione basati sulla posta elettronica che rapidamente consentono agli utenti di creare identità usando i relativi alias di posta elettronica di lavoro facili da ricordare.
- I tenant di Azure non gestiti possono aumentare e diventare tenant gestiti in un secondo momento ed essere riutilizzati per altri servizi.

## Termini e definizioni

+ **Iscrizione self-service**: si tratta del metodo per cui un utente si iscrive a un servizio cloud e ha un'identità creata automaticamente in Azure Active Directory (AD) basata sul loro dominio di posta elettronica.
+ **Tenant di Azure non gestito**: si tratta della directory in cui viene creata tale identità. Un tenant non gestito è una directory priva di un amministratore globale.
+ **Utente verificato per la posta elettronica**: si tratta di un tipo di account utente in Azure AD. Un utente che dispone di un'identità creata automaticamente a seguito dell'iscrizione per un'offerta self-service è noto come utente di posta elettronica verificato. Un utente di posta elettronica verificato è un membro regolare di una directory contrassegnata con creationmethod=EmailVerified.

## Esperienza utente

Si supponga, ad esempio, un utente il cui indirizzo di posta elettronica Dan@BellowsCollege.com riceve file riservati tramite posta elettronica. I file sono stati protetti da Azure Rights Management (Azure RMS). Tuttavia l'organizzazione di Dan, Bellows College, non è iscritta ad Azure RMS né ha distribuito Active Directory RMS. In questo caso, Dan può iscriversi con un abbonamento gratuito a RMS per utenti singoli per leggere i file protetti.

Se Dan è il primo utente con un indirizzo di posta elettronica di BellowsCollege.com a iscriversi a questo offerta self-service, viene creato un tenant non gestito per BellowsCollege.com in Azure AD. Se altri utenti del dominio BellowsCollege.com si iscrivono a questa offerta o a un'offerta self-service simile, disporranno anche di account utente di posta elettronica verificati creati nello stesso tenant non gestito in Azure.

## Esperienza amministratore

Un amministratore a cui appartiene il nome di dominio DNS di un tenant di Azure non gestito può assumere o unire il tenant dopo aver dimostrato di essere il proprietario. Nelle sezioni successive viene illustrata l'esperienza di amministratore in modo più dettagliato; in ogni caso qui di seguito è riportato un riepilogo:

- Quando si usa un tenant di Azure non gestito, si diventa semplicemente l'amministratore globale del tenant non gestito. In alcuni casi si tratta di un'acquisizione interna.
- Quando si esegue l'unione di un tenant di Azure non gestito, è possibile aggiungere il nome di dominio DNS del tenant non gestito a quello Azure gestito e viene creato un mapping di utenti a risorse in modo che gli utenti possano continuare ad accedere ai servizi senza interruzioni. In alcuni casi si tratta di un'acquisizione esterna.

## Cosa viene creato in Azure Active Directory?

#### Tenant

- Viene creato un tenant di Azure Active Directory per il dominio, un singolo tenant per dominio.
- La directory del tenant di Azure AD non dispone di alcun amministratore globale.

#### Utenti

- Per ogni utente che effettua l'iscrizione viene creato un oggetto utente nel tenant di Azure AD.
- Ogni oggetto utente viene contrassegnato come virale.
- Ogni utente è autorizzato ad accedere al servizio al quale si è iscritto.

### Come è possibile richiedere un tenant di Azure AD self-service per un dominio di cui si è proprietari?

È possibile richiedere un tenant di Azure AD self-service eseguendo la convalida del dominio. La convalida del dominio dimostra che si è proprietari del dominio mediante la creazione di record DNS.

Esistono due modi per eseguire acquisizioni DNS di un tenant di Azure AD:

- acquisizione interna (l'amministratore individua un tenant di Azure non gestito e intende trasformarlo in un tenant gestito)
- acquisizione esterna (l'amministratore tenta di aggiungere un nuovo dominio al tenant di Azure gestito)

È possibile essere interessati alla convalida della proprietà personale di un dominio perché si sta prendendo un tenant non gestito dopo l'iscrizione self-service da parte di un utente oppure è possibile aggiungere un nuovo dominio a un tenant gestito esistente. Ad esempio, si dispone di un dominio denominato contoso.com e si vuole aggiungere un nuovo dominio denominato contoso.co.uk o contoso.uk.

## Che cos'è l'acquisizione di dominio?  

In questa sezione viene descritto come convalidare la proprietà di un dominio

### Cos'è la convalida del dominio e perché viene usata?

Per eseguire operazioni su un tenant, Azure AD richiede la convalida della proprietà del dominio DNS. La convalida del dominio consente di attestare il tenant e di alzare il livello del tenant self-service a quello di un tenant gestito oppure di unire il tenant self-service in un tenant gestito esistente.

## Esempi di convalida del dominio

Esistono due modi per eseguire acquisizioni DNS di un tenant:

+ acquisizione interna (ad esempio, un amministratore individua un tenant self-service e non gestito e intende trasformarlo in un tenant gestito)
+ acquisizione esterna (ad esempio, l'amministratore cerca di aggiungere un nuovo dominio al tenant gestito)

### Acquisizione interna: alzare il livello di un tenant self-service e non gestito a quello di un tenant gestito

Quando si esegue l'operazione di acquisizione interna, il tenant viene convertito da tenant non gestito a tenant gestito. È necessario completare la convalida del nome di dominio DNS, in cui si crea un record MX o un record TXT nella zona DNS. Questa azione:

+ Convalida la proprietà del dominio
+ Rende il tenant gestito
+ Rende l'utente amministratore globale del tenant

Si supponga, ad esempio, che un amministratore IT di Bellows College rileva che utenti della scuola si sono iscritti alle offerte self-service. In qualità di proprietario registrato del nome DNS BellowsCollege.com, l'amministratore IT può convalidare la proprietà del nome DNS in Azure e quindi assumere il tenant non gestito. Quest'ultimo diventa quindi un tenant gestito e all'amministratore IT viene assegnato il ruolo di amministratore globale per la directory BellowsCollege.com.

### Acquisizione esterna: unire un tenant self-service in un tenant gestito esistente

In un'acquisizione esterna, si dispone già di un tenant gestito e si vuole che tutti gli utenti e gruppi di un tenant non gestito partecipino a quel tenant gestito, invece possedere due tenant separati.

In qualità di amministratore di un tenant gestito si aggiunge un dominio e accade che questo dominio abbia un tenant non gestito associato.

Si supponga, ad esempio, di essere un amministratore IT e di disporre già di un tenant gestito per Contoso.com, il nome di dominio registrato per la propria organizzazione. Si scopre che gli utenti dell'organizzazione hanno eseguito l'iscrizione self-service per un'offerta usando il nome di dominio di posta elettronica user@contoso.co.uk, ovvero un altro nome di dominio dell'organizzazione di cui si è proprietari. Attualmente gli utenti dispongono di account in un tenant contoso.co.uk non gestito.

Non si vogliono gestire due tenant separati, pertanto si unisce il tenant non gestito per contoso.co.uk al tenant gestito IT esistente per contoso.com.

L'acquisizione esterna segue lo stesso processo di convalida DNS dell'acquisizione interna. Differenza: utenti e servizi vengono rimappati al tenant IT gestito.

#### Qual è l'impatto dell'esecuzione di un'acquisizione esterna?

Con un'acquisizione esterna viene creato un mapping di utenti a risorse in modo che gli utenti possono continuare ad accedere ai servizi senza interruzioni. Molte applicazioni, tra cui RMS per utenti singoli, gestiscono anche il mapping degli utenti alle risorse e gli utenti possono continuare ad accedere a questi servizi senza alcuna modifica. Se un'applicazione non gestisce il mapping degli utenti alle risorse in modo efficace, l'acquisizione esterna potrebbe essere bloccata in modo esplicito per impedire agli utenti un'esperienza deludente.

#### Supporto di acquisizione del tenant in base al servizio

I servizi seguenti supportano attualmente l'acquisizione:

- RMS


L'acquisizione verrà presto supportata dai servizi seguenti:

- PowerBI

I servizi seguenti non la supportano e richiedono un'ulteriore azione da parte dell'amministratore per la migrazione dei dati utente dopo un'acquisizione esterna.

- SharePoint/OneDrive


## Come eseguire un'acquisizione del nome di dominio DNS

Sono disponibili alcune opzioni per la modalità di esecuzione della convalida di un dominio (ed eseguire un'acquisizione se lo si desidera):

1.  Portale di gestione di Azure

	Un'acquisizione viene attivata eseguendo l'aggiunta di un dominio. Se esiste già un tenant per il dominio, è possibile eseguire un'acquisizione esterna.

	Accedere al portale di Azure mediante le proprie credenziali. Passare al tenant esistente e quindi ad **Aggiungi dominio**.

2.  Office 365

	È possibile usare le opzioni sulla pagina [Gestisci domini](https://support.office.com/article/Navigate-to-the-Office-365-Manage-domains-page-026af1f2-0e6d-4f2d-9b33-fd147420fac2/) in Office 365 per lavorare con i domini e i record DNS. Vedere [Verificare il dominio in Office 365](https://support.office.com/article/Verify-your-domain-in-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611/).

3.  Windows PowerShell

	I passaggi seguenti sono necessari per eseguire una convalida mediante Windows PowerShell.

	Passaggio |	Cmdlet da usare
	-------	| -------------
	Creare un oggetto credential | Get-Credential
	Connettersi ad Azure AD | Connect-MsolService
	Ottenere un elenco di domini | Get-MsolDomain
	Creare una richiesta di verifica | Get-MsolDomainVerificationDns
	Creare record DNS | Eseguire questa operazione sul server DNS
	Verificare la richiesta di verifica | Confirm-MsolEmailVerifiedDomain

Ad esempio:

1. Connettersi ad Azure AD usando le credenziali usate per rispondere all'offerta self-service: import-module MSOnline $msolcred = get-credential connect-msolservice -credential $msolcred

2. Ottenere un elenco di domini:

	Get-MsolDomain

3. Eseguire quindi il cmdlet Get-MsolDomainVerificationDns per creare una richiesta di verifica:

	Get-MsolDomainVerificationDns –DomainName *your\_domain\_name* –Mode DnsTxtRecord

	Ad esempio:

	Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord

4. Copiare il valore (la richiesta di verifica) restituita da questo comando.

	Ad esempio:

	MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9

5. Nello spazio dei nomi DNS pubblico creare un record txt DNS che contiene il valore copiato nel passaggio precedente.

	Il nome per questo record è quello del dominio padre, pertanto se si crea questo record di risorse usando il ruolo DNS di Windows Server, lasciare il nome record vuoto e incollare solo il valore nella casella di testo

6. Eseguire il cmdlet Confirm-MsolDomain per verificare la richiesta di verifica:

	Confirm-MsolEmailVerifiedDomain -DomainName *your\_domain\_name*

	ad esempio:

	Confirm-MsolEmailVerifiedDomain -DomainName contoso.com

Una richiesta di verifica riporta al prompt senza errori.

## Come controllare le impostazioni di self-service?

Gli amministratori attualmente dispongono di due controlli self-service. Possono controllare:

- Se gli utenti possono partecipare al tenant tramite posta elettronica.
- Se gli utenti possono ottenere autonomamente una licenza per applicazioni e servizi.


### Come è possibile controllare queste funzionalità?

Un amministratore può configurare queste funzionalità usando questi parametri del cmdlet Set-MsolCompanySettings di Azure AD:

+ **AllowEmailVerifiedUsers** controlla se un utente può creare o partecipare a un tenant non gestito. Se tale parametro è impostato su $false, nessun utente verificato per la posta elettronica può aggiungere il tenant.
+ **AllowAdHocSubscriptions** controlla la possibilità per gli utenti di eseguire l'iscrizione self-service. Se tale parametro è impostato su $false, nessun utente può eseguire l'iscrizione self-service.


### Come operano congiuntamente i controlli?

Questi due parametri possono essere usati insieme per definire un controllo più preciso sull'iscrizione self-service. Ad esempio, il comando seguente consentirà agli utenti di eseguire l'iscrizione self-service, ma solo se tali utenti dispongono già di un account in Azure AD (in altre parole, gli utenti, per i quali sarebbe necessario che venisse creato un account di posta elettronica verificato, non possono eseguire l'iscrizione self-service):

	Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true

Il diagramma di flusso seguente illustra tutte le diverse combinazioni di questi parametri e le condizioni risultanti per il tenant e l'iscrizione self-service.

![][1]

Per ulteriori informazioni ed esempi su come usare questi parametri, vedere [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx).

## Vedere anche

-  [Come installare e configurare Azure PowerShell](../powershell-install-configure/)

-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)

-  [Informazioni di riferimento sui cmdlet di Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx)

-  [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png

<!---HONumber=AcomDC_0107_2016-->