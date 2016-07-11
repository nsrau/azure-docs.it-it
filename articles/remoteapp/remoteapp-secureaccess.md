
<properties 
    pageTitle="Protezione dell'accesso ad Azure RemoteApp e oltre | Microsoft Azure"
	description="Informazioni su come proteggere l'accesso ad RemoteApp usando l'accesso condizionale in Azure Active Directory"
	services="remoteapp"
	documentationCenter="" 
	authors="piotrci" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/27/2016" 
    ms.author="elizapo" />

# Protezione dell'accesso ad Azure RemoteApp e oltre

In questo articolo verrà illustrato come un amministratore può configurare un canale di accesso sicuro dall'utente finale ad Azure RemoteApp e infine a una risorsa sicura, ad esempio un database SQL o un altro back-end dell'applicazione. L'obiettivo è fare in modo che solo gli utenti autorizzati che soddisfano le condizioni desiderate possano accedere alle applicazioni remote e che il back-end sicuro sia accessibile solo dall'ambiente di Azure RemoteApp controllato e non da altre posizioni.

Le 3 aree principali che l'amministratore deve esaminare sono le seguenti:

![Considerazioni sull'accesso condizionale di Azure RemoteApp](./media/remoteapp-secureaccess/ra-conditionalenvironment.png)

Per informazioni e per le risposte a queste domande, continuare a leggere l'articolo.

## Chi può accedere alla raccolta?
L'amministratore sceglie gli utenti che possono accedere alle applicazioni remote della raccolta. È possibile usare gli account aziendali o dell'istituto di istruzione di Azure Active Directory (Azure AD) (in precedenza denominati, "account aziendali") o gli account Microsoft (ad esempio, @outlook.com). La maggior parte degli scenari aziendali usa account Azure AD che consentono di usare le funzionalità di accesso condizionale illustrate più avanti e costituiscono l'unica scelta per le raccolte appartenenti a un dominio. Nella parte restante dell'articolo si presuppone che si stiano usando gli account Azure AD con Azure RemoteApp.

**Risultati ottenuti:**

Usando gli account Azure AD per controllare l'accesso ad Azure RemoteApp si ottengono due risultati:

1.	Si sa sempre chi può accedere alle applicazioni pubblicate e ai back-end a cui le applicazioni si connettono.
2.	Si controlla l'istanza di Azure AD sottostante per poter creare ed eliminare gli account utente, impostare i criteri password, usare l'autenticazione a più fattori e così via.

## Come si accede alla raccolta? Da dove?
Gli amministratori di solito vogliono definire i criteri per l'accesso a un ambiente Internet pubblico, ad esempio Azure RemoteApp. Vogliono, ad esempio, essere certi che gli utenti che accedono all'ambiente dall'esterno della rete aziendale debbano usare l'autenticazione a più fattori (MFA) per ottenere l'accesso o che vengano completamente bloccati, se necessario.

Gli amministratori di Azure RemoteApp possono usare la funzionalità disponibile in Azure AD Premium per impostare criteri di accesso condizionale per l'ambiente Azure RemoteApp. Possono anche usare le funzionalità avanzate di creazione di report e gli avvisi per monitorare l'accesso all'ambiente.

### Come configurare l'accesso condizionale per Azure RemoteApp
Verrà ora illustrato uno scenario di esempio: l'amministratore di Azure RemoteApp vuole bloccare l'accesso all'ambiente quando gli utenti sono all'esterno della rete aziendale.

>[AZURE.NOTE] Si presuppone che Azure AD sia stato aggiornato al piano Premium e che sia stata creata almeno una raccolta di Azure RemoteApp.

1.	Nel portale di Azure fare clic sulla scheda **Active Directory**, quindi fare clic sulla directory che si vuole configurare.

	Si ricordi che l'accesso condizionale è una proprietà della directory e non di Azure RemoteApp, quindi la configurazione viene completamente eseguita a livello di directory e perciò è necessario essere l'amministratore della directory per apportare queste modifiche.

2.	Fare clic su **Applicazioni** e quindi su **Microsoft Azure RemoteApp** per configurare l'accesso condizionale. Si noti che è possibile configurare l'accesso condizionale per ogni singola applicazione "software come un servizio" nella directory. ![Configurazione dell'accesso condizionale per Azure RemoteApp](./media/remoteapp-secureaccess/ra-conditionalaccessscreen.png)
 

3.	Nella scheda **Configura** impostare **Abilita regole di accesso** come attivo. ![Abilitare le regole di accesso per Azure RemoteApp](./media/remoteapp-secureaccess/ra-enableaccessrules.png)
 

4.	Ora è possibile configurare diverse regole e scegliere a chi applicarle:

	1. Scegliere **Blocca l'accesso quando non al lavoro** per impedire completamente agli utenti di accedere ad Azure RemoteApp al di fuori dell'ambiente specificato.
	2. Fare clic sull'opzione sotto per definire gli intervalli di indirizzi IP che costituiscono la "rete attendibile". Tutti gli altri indirizzi verranno rifiutati.

5.	Testare la configurazione avviando il client Azure RemoteApp da un indirizzo IP non compreso nell'intervallo specificato. Dopo avere eseguito l'accesso con le credenziali di Azure AD, verrà visualizzato un messaggio simile a questo:

![Accesso negato ad Azure RemoteApp](./media/remoteapp-secureaccess/ra-accessdenied.png)
 

### Funzionalità di accesso condizionale future 
Il team di Azure Active Directory sta lavorando alle nuove funzionalità di accesso condizionale. Gli amministratori potranno creare nuovi tipi di regole oltre a quelle basate sui percorsi di rete. Un'anteprima pubblica delle nuove funzionalità sarà presto disponibile.

### Come monitorare l'accesso ad Azure RemoteApp
Un'eccellente funzionalità da usare con l'accesso condizionale è la creazione di report di Azure Active Directory Premium. È possibile usare i report per monitorare chi accede all'ambiente e rilevare le attività sospette.

Ad esempio, è possibile visualizzare i nomi degli utenti che hanno eseguito l'accesso ad Azure RemoteApp, quante volte l'hanno effettuato e quando.

1.	Nel portale di Azure fare clic su **Active Directory** e quindi sulla directory.

2.	Andare alla scheda **Report**.

3.	Nell'elenco di report selezionare **Utilizzo applicazioni** in **Applicazioni integrate**.

	Verranno visualizzate alcune statistiche aggregate per Azure RemoteApp. ![Statistiche di accesso di Azure RemoteApp aggregate](./media/remoteapp-secureaccess/ra-accessstats.png)
 
5.	Fare clic sull'applicazione per visualizzare le informazioni sugli utenti che accedono ad Azure RemoteApp. ![Statistiche di accesso utente per Azure RemoteApp](./media/remoteapp-secureaccess/ra-userstats.png)
 
### Riepilogo
Con Azure Active Directory Premium è possibile configurare regole di accesso ad Azure RemoteApp (e ad altre applicazioni software come un servizio disponibili in Azure AD). Le regole attualmente sono limitate ai criteri basati sui percorsi di rete, ma in futuro verranno estese ad altri aspetti della gestione aziendale.

Azure AD Premium offre anche funzionalità di creazione di report e di monitoraggio che estendono ulteriormente il controllo dell'amministratore sull'ambiente Azure RemoteApp.

## Come verificare che la risorsa sicura sia accessibile solo dall'ambiente Azure RemoteApp?
Nelle sezioni precedenti di questo articolo è stata illustrata la protezione dell'accesso all'ambiente Azure RemoteApp, che è stata realizzata scegliendo gli utenti a cui è consentito l'accesso e configurando regole di accesso per ottenere un controllo maggiore su come possono usare il servizio.

Uno scenario comune per le distribuzioni di Azure RemoteApp è quello in cui le applicazioni remote devono comunicare con una risorsa back-end, ad esempio un database SQL. Questa risorsa è ospitata in locale (ad esempio, in una rete aziendale) o nel cloud (ad esempio, in IaaS di Azure). Gli amministratori spesso vogliono verificare che la risorsa back-end possa essere accessibile solo alle applicazioni distribuite tramite Azure RemoteApp e non, ad esempio, a un'applicazione eseguita direttamente nel PC di un utente e che accede tramite Internet pubblico. Azure RemoteApp viene spesso considerato un ambiente protetto e gestito centralmente e di conseguenza il solo percorso attraverso cui gli utenti possono interagire con la risorsa back-end.

La soluzione è inserire sia l'ambiente Azure RemoteApp che la risorsa sicura nella stessa rete virtuale di Azure. Se la risorsa è in un sito diverso, è possibile stabilire una connessione VPN da sito a sito, ad esempio per creare una rete virtuale che si estende sul data center di Azure e sull'ambiente locale del cliente.

Azure RemoteApp supporta due tipi di distribuzione di raccolte in cui è possibile specificare la propria rete virtuale:

-	Non appartenente a un dominio: le applicazioni avranno una "visuale" delle altre risorse della rete virtuale. Può essere usata, ad esempio, per connettere le applicazioni a un database SQL che usa l'autenticazione SQL (le applicazioni autenticano l'utente direttamente nel database)

-	Appartenente a un dominio: le macchine virtuali usate da Azure RemoteApp appartengono a un controller di dominio della rete virtuale. È utile quando le applicazioni devono eseguire l'autenticazione in un controller di dominio Windows per ottenere l'accesso a una risorsa back-end. ![Raccolta appartenente a un dominio in Azure RemoteApp](./media/remoteapp-secureaccess/ra-domainjoined.png)
 
### Come creare una connessione sicura tra l'ambiente Azure e locale
Le opzioni di configurazione per connettere gli ambienti Azure e locali sono diverse. Qui è disponibile un'ampia panoramica delle opzioni.

Con Azure RemoteApp è necessario configurare prima la rete virtuale e quindi usarla durante il processo di creazione della raccolta.

## Soluzione completa
Il diagramma seguente illustra la soluzione completa in cui è stato creato un canale di accesso sicuro dall'utente finale, attraverso Azure RemoteApp (ARA), alla risorsa backend. ![Proteggere Azure RemoteApp](./media/remoteapp-secureaccess/ra-secureoverview.png) Nella fase 1 sono stati selezionati gli utenti e sono state create le regole di accesso che determinano come accedere ad ARA. Nell'esempio seguente viene consentito l'accesso solo agli utenti che lavorano dalla rete aziendale. Gli utenti non conformi non potranno accedere all'ambiente ARA. Nella "fase 2" la risorsa back-end è stata esposta solo tramite la configurazione di rete virtuale/VPN controllata. Azure RemoteApp è stato inserito nella stessa rete virtuale. Come risultato finale la risorsa è accessibile solo dall'ambiente ARA.

<!---HONumber=AcomDC_0629_2016-->