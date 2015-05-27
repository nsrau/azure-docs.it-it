
<properties 
    pageTitle="Configurare Active Directory per Azure RemoteApp" 
    description="Informazioni su come configurare Active Directory per lavorare con Azure RemoteApp." 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/28/2015" 
    ms.author="elizapo" />



# Configurare Active Directory per Azure RemoteApp


Per una raccolta ibrida di RemoteApp, è necessario configurare un'infrastruttura di dominio Active Directory locale e un tenant di Azure Active Directory con l'integrazione della directory (e, facoltativamente Single Sign-On). Inoltre, è necessario creare alcuni oggetti Active Directory nella directory locale. Usare le informazioni seguenti per configurare Active Directory locale e Azure AD, quindi integrare i due.

## Configurare Active Directory locale
Iniziare configurando Active Directory locale. È necessario identificare il suffisso del dominio UPN da usare e quindi creare oggetti Active Directory per RemoteApp.

Se non si è ancora aggiunto Servizi di dominio Active Directory al proprio ambiente Windows Server 2012 R2, consultare [queste istruzioni](https://technet.microsoft.com/library/cc731053.aspx) per informazioni su come eseguire tale operazione.
### Esaminare e configurare il suffisso di dominio UPN
Se la foresta non è configurata con un suffisso UPN che corrisponde al dominio di Azure AD che verrà usato per RemoteApp, sarà necessario aggiungerne uno. Determinare se il suffisso necessario è configurato:


1. Avviare Utenti e computer di Active Directory.
2.	Espandere il nome di dominio, quindi fare clic su **Utenti**.
3.	Fare clic con il pulsante destro del mouse su **Amministratore**, quindi fare clic su **Proprietà**.
4.	Nella scheda **Account** esaminare i nomi UPN configurati per il dominio nel campo **Nome accesso utente**.
5.	Se non viene visualizzato un suffisso che corrisponde al nome di dominio da usare per la raccolta RemoteApp, eseguire le operazioni seguenti:
	1.	Avviare Domini e trust di Active Directory.
	2.	Fare clic con il pulsante destro del mouse su **Domini e trust di Active Directory**, quindi fare clic su **Proprietà**.
	3.	Esaminare l'elenco di suffissi.
	4.	Digitare il nome di dominio completo nella casella e fare clic su **Aggiungi**, quindi su **OK**. Esempio: contoso.com. 

		NON includere il carattere "@" nel suffisso in questo caso.

Da questo momento, quando si creano nuovi utenti, è possibile selezionare il nuovo suffisso dal nome di accesso utente. Inoltre, è possibile modificare il suffisso per gli utenti esistenti nella scheda Account delle proprietà degli utenti.

Per altre informazioni, vedere [Aggiungere suffissi del nome dell'entità utente](http://technet.microsoft.com/library/cc772007.aspx).

### Creare gli oggetti per RemoteApp in Active Directory
RemoteApp necessita di due oggetti in Active Directory locale:


- Un account del servizio per fornire l'accesso alle risorse di dominio ai programmi RemoteApp unendo gli endpoint di Host sessione Desktop remoto al dominio locale.
- Un'unità organizzativa per contenere oggetti computer di RemoteApp. L'uso dell'unità organizzativa è consigliabile (ma non obbligatorio) per isolare gli account e i criteri che si useranno con RemoteApp.

Usare le informazioni seguenti per creare ognuno di questi oggetti.

#### Creare l'account del servizio:


1. Avviare Utenti e computer di Active Directory.
2.	Fare clic con il pulsante destro del mouse su **Utenti**, quindi fare clic su **Nuovo > Utente**.
3.	Immettere un nome utente e una password per l'account del servizio RemoteApp.

	**Nota:** le informazioni relative a questo account saranno necessarie quando si creerà la raccolta RemoteApp.

#### Creare una nuova unità organizzativa


1. In Utenti e computer di Active Directory, fare clic sul dominio. Fare clic su **Nuovo > Unità organizzativa**.
2. Aggiungere l'account del servizio creato per RemoteApp a questa nuova unità organizzativa.

	Per eseguire questa operazione, trovare l'account del servizio che si è creato. Fare clic sull'account con il pulsante destro del mouse, quindi fare clic su **Sposta**. Selezionare la nuova unità organizzativa, quindi fare clic su **OK**.


1. Concedere all'account del servizio RemoteApp le autorizzazioni per aggiungere ed eliminare computer a questa unità organizzativa:
	1. Dallo snap-in Utenti e computer di Active Directory fare clic su **Visualizza > Funzionalità avanzate**. Verrà aggiunta la scheda **Sicurezza** alle informazioni relative alle proprietà.
	2. Fare clic con il pulsante destro del mouse sull'unità organizzativa del servizio RemoteApp, quindi fare clic su **Proprietà**.
	3. Nella scheda **Sicurezza** fare clic su **Aggiungi**. Selezionare l'oggetto utente dell'account del servizio RemoteApp, quindi fare clic su **OK**.
	4. Fare clic su **Advanced**.
	5. Nella scheda **Autorizzazioni** selezionare l'account del servizio RemoteApp, quindi fare clic su **Modifica**.
	6. Selezionare **Questo oggetto e tutti i discendenti** nel campo **Si applica a**.
	7. Nel campo **Autorizzazioni** selezionare **Consenti** accanto a Crea oggetti computer ed Elimina oggetti computer, quindi fare clic su **OK**. 
	8. Fare clic su **OK** nelle due finestre rimanenti.


## Configurare Azure Active Directory
Ora che Active Directory locale è stato configurato, passare ad Azure AD. È necessario creare un dominio personalizzato che corrisponda al suffisso di dominio UPN per il dominio locale e impostare l'integrazione di directory. La raccolta ibrida supporta solo gli account Azure Active Directory che sono stati sincronizzati (mediante uno strumento come DirSync) da una distribuzione di Windows Server Active Directory, e, più in particolare, sincronizzati con l'opzione di sincronizzazione password oppure con la federazione di Active Directory Federation Services (ADFS) configurata.

Usare le informazioni seguenti per configurare Azure Active Directory


- [Aggiungere il proprio dominio](http://technet.microsoft.com/library/hh969247.aspx): usare queste informazioni per aggiungere un dominio che corrisponda al suffisso di dominio UPN del dominio Active Directory locale.
- [Integrazione directory](http://technet.microsoft.com/library/jj573653.aspx): usare queste informazioni per scegliere un'opzione di integrazione directory: [DirSync con sincronizzazione password](http://technet.microsoft.com/library/dn441214.aspx) o [DirSync con federazione](http://technet.microsoft.com/library/dn441213.aspx).

È anche possibile configurare l'[autenticazione a più fattori](http://technet.microsoft.com/library/dn249466.aspx) (MFA, Multi-Factor Authentication).

## Problemi durante la configurazione della sincronizzazione della directory?

Se si riscontrano problemi durante la configurazione della sincronizzazione della directory, verificare quanto segue:

- La versione dello strumento Sincronizzazione directory di Azure è la più recente. 
-	Nel portale di gestione in **Active Directory -> Directory predefinita -> Domini** il dominio personalizzato è già stato aggiunto (ad esempio mydomain.com) ed è stato impostato come primario.
-	In **Active Directory -> Directory predefinita -> Utenti** si aggiunge un nuovo utente in tale dominio (ad esempio myAzureSyncUser@mydomain.com).
-	Nel dominio in Active Directory è stato aggiunto un nuovo utente di dominio ed è stato inserito nel gruppo Enterprise Admins (ad esempio myDomainSyncUser@mydomain.com).

Avviare ora lo strumento Sincronizzazione directory di Azure e usare le credenziali **myAzureSyncUser@mydomain.com**per la prima richiesta (Credenziali di amministratore di Microsoft Azure Active Directory), quindi usare **myDomainSyncUser@mydomain.com** per la seconda richiesta.

<!--HONumber=54-->