<properties 
   pageTitle="Sicurezza in StorSimple" 
   description="Descrive le funzionalità di sicurezza e privacy che proteggono il servizio, il dispositivo e i dati StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="AdinaH" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="03/19/2015"
   ms.author="v-sharos"/>

# Sicurezza in StorSimple 

## Informazioni generali

La sicurezza è una delle principali preoccupazioni per chi ha intenzione di adottare una nuova tecnologia, soprattutto se usata con dati riservati o proprietari. Quando si valutano tecnologie diverse, è necessario tenere in considerazione l'aumento dei rischi e dei costi per la protezione dei dati. Microsoft Azure StorSimple offre una soluzione sia di sicurezza che di privacy per la protezione dei dati, che aiuta a garantire: 

- **Riservatezza** - Solo le entità autorizzate possono visualizzare i dati. 
- **Integrità** - Solo le entità autorizzate possono modificare o eliminare i dati.

La soluzione Microsoft Azure StorSimple è costituita da quattro componenti principali che interagiscono tra loro:

- **Servizio StorSimple Manager ospitato in Microsoft Azure** - Servizio di gestione usato per la configurazione e il provisioning del dispositivo StorSimple.
- **Dispositivo StorSimple device** - Dispositivo fisico installato nel data center. Tutti gli host e i client che generano dati si connettono al dispositivo StorSimple e il dispositivo gestisce i dati e li sposta nel cloud Azure come necessario.
- **Client/host connessi al dispositivo** - Client nell'infrastruttura che si connettono al dispositivo StorSimple e generano i dati da proteggere.
- **Archiviazione cloud** - Posizione nel cloud Azure in cui vengono archiviati i dati.

Le sezioni seguenti descrivono le funzionalità di sicurezza di StorSimple che consentono di proteggere i singoli componenti e i relativi dati archiviati. Include inoltre un elenco di domande e risposte relative alla sicurezza di Microsoft Azure StorSimple.

## Protezione del servizio StorSimple Manager

StorSimple Manager è un servizio di gestione ospitato in Microsoft Azure che consente di gestire tutti i dispositivi StorSimple installati all'interno di un'organizzazione. Per accedere al servizio StorSimple Manager usare le credenziali aziendali nel portale di gestione di Azure con un Web browser. 

Per l'accesso al servizio StorSimple Manager, l'organizzazione deve disporre di una sottoscrizione Azure in cui sia incluso StorSimple. La sottoscrizione determina la disponibilità delle funzionalità nel portale di Azure. Se l'organizzazione non dispone ancora di una sottoscrizione di Azure e si desidera ottenere informazioni al riguardo, vedere [Iscrizione ad Azure come organizzazione](../sign-up-organization.md). 

Poiché il servizio StorSimple Manager è ospitato in Azure, viene protetto dalle funzionalità di sicurezza di Azure. Per altre informazioni sulle funzionalità di sicurezza fornite da Microsoft Azure, andare in [Centro protezione di Microsoft Azure](http://azure.microsoft.com/support/trust-center/security/).

## Protezione del dispositivo StorSimple

Il dispositivo StorSimple è un dispositivo di archiviazione ibrido locale contenente unità SSD e unità disco rigido, oltre a controller ridondanti e funzionalità di failover automatico. I controller gestiscono la suddivisione in livelli dell'archiviazione, inserendo i dati attualmente usati (o hot data) nella risorsa di archiviazione locale (nel dispositivo StorSimple o nei server locali) e spostando nel cloud i dati usati meno di frequente.

## Controllare l'accesso a StorSimple mediante la registrazione del dispositivo

Solo i dispositivi StorSimple autorizzati possono aggiungere il servizio StorSimple Manager creato nella sottoscrizione di Azure. 

Per autorizzare un dispositivo, è necessario registrarlo con il servizio StorSimple Manager inserendo la chiave di registrazione del servizio. La chiave di registrazione del servizio è un codice casuale a 128 bit generato nel portale. Per informazioni su come ottenere una chiave di registrazione del servizio, vedere [Passaggio 2: Ottenere la chiave di registrazione del servizio](https://msdn.microsoft.com/library/azure/dn772346.aspx).

> [AZURE.NOTE] La chiave di registrazione del servizio è un codice lungo contenente più di 100 caratteri. Si consiglia di copiare la chiave e salvarla in un file di testo in un percorso sicuro per poterla usare per autorizzare altri dispositivi, se necessario.
> 
> * Se si perde la chiave di registrazione del servizio dopo aver registrato il primo dispositivo, è possibile generare una nuova chiave dal servizio StorSimple Manager. L'operazione non avrà effetto sul funzionamento dei dispositivi esistenti. 
> * Un dispositivo, dopo essere stato registrato, usa i token per comunicare con Microsoft Azure. La chiave di registrazione del servizio non viene usata dopo la registrazione del dispositivo.


## Proteggere la soluzione StorSimple con le password

Le password sono un componente importante della sicurezza dei computer e vengono usate di frequente nella soluzione Microsoft Azure StorSimple per garantire che i dati siano accessibili solo a utenti autorizzati. StorSimple consente di configurare le seguenti password:

- Password di Windows PowerShell per StorSimple
- Password amministratore del dispositivo StorSimple
- Password di destinazione e iniziatore CHAP (Challenge Handshake Authentication Protocol)
- Password di Gestione snapshot StorSimple

### Password amministratore di Windows PowerShell per StorSimple e del dispositivo StorSimple

Windows PowerShell per StorSimple è un'interfaccia della riga di comando che può essere usata per gestire il dispositivo StorSimple. Windows PowerShell per StorSimple include funzionalità che permettono di registrare il dispositivo, configurare l'interfaccia di rete nel dispositivo, installare determinati tipi di aggiornamento, risolvere i problemi del dispositivo tramite l'accesso alla sessione di supporto e modificare lo stato del dispositivo. È possibile accedere a Windows PowerShell per StorSimple connettendosi alla console seriale o usando la comunicazione remota di Windows PowerShell. 

La comunicazione remota di PowerShell può essere stabilita su HTTPS o HTTP. Se la gestione remota su HTTPS è abilitata, sarà necessario scaricare il certificato di gestione remota dal dispositivo e installarlo nel client remoto. 

> [AZURE.IMPORTANT] 

> * La gestione remota è disattivata per impostazione predefinita. Per abilitarla, è possibile usare il servizio StorSimple Manager. Come procedura consigliata per la sicurezza, è opportuno abilitare l'accesso remoto solo per il periodo di tempo in cui è effettivamente necessario.
> * Se si cambia la password, assicurarsi di notificarlo a tutti gli utenti di accesso remoto per evitare una perdita imprevista della connettività.
> Il servizio StorSimple Manager non può recuperare le password esistenti, ma solo reimpostarle. È consigliabile archiviare tutte le password in un luogo sicuro per non dover reimpostare una password nel caso la si dimentichi. Se è necessario reimpostare una password, assicurarsi di notificarlo a tutti gli utenti prima di reimpostarla. 
> * È possibile accedere all'interfaccia di Windows PowerShell usando una connessione seriale al dispositivo. È anche possibile accedervi in modalità remota usando HTTP o HTTPS, che offrono una maggiore sicurezza. HTTPS offre un livello più elevato di sicurezza rispetto a una connessione seriale o HTTP. Per usare HTTPS, tuttavia, prima è necessario installare un certificato nel computer client che accederà al dispositivo. È possibile scaricare il certificato di accesso remoto dalla pagina di configurazione del dispositivo nel servizio StorSimple Manager. Se il certificato per l'accesso remoto viene perso, è necessario scaricare un nuovo certificato e propagarlo a tutti i client autorizzati a usare la gestione remota.

Dopo aver usato Windows PowerShell per StorSimple per connettersi al dispositivo, sarà necessario immettere la password amministratore per accedere al dispositivo.

### Password di destinazione e iniziatore CHAP (Challenge Handshake Authentication Protocol)

CHAP è uno schema di autenticazione usato dal dispositivo StorSimple per convalidare l'identità dei client remoti. La verifica si basa su una password condivisa. CHAP può essere unidirezionale o bidirezionale (reciproco). Con CHAP unidirezionale, la destinazione (il dispositivo StorSimple) autentica un iniziatore (host). Per l'autenticazione CHAP reciproca è necessario che la destinazione autentichi l'iniziatore e viceversa. Azure StorSimple può essere configurato per l'utilizzo di entrambi i metodi.

> [AZURE.IMPORTANT] 
> 
> * Il nome dell'utente CHAP deve contenere meno di 233 caratteri.
> * La password CHAP deve essere compresa tra 12 e 16 caratteri. Se si prova a usare un nome utente o una password più lunga, si verificherà un errore di autenticazione sull'host Windows.
> * Non è possibile usare la stessa password sia per l'iniziatore CHAP che per la destinazione CHAP.
> * Dopo aver impostato la password, è possibile cambiarla, ma non recuperarla. Se la password viene cambiata, assicurarsi di notificarlo a tutti gli utenti di accesso remoto in modo che riescano a connettersi al dispositivo StorSimple.

### Password di Gestione snapshot StorSimple

Gestione snapshot StorSimple è uno snap-in MMC (Microsoft Management Console) che usa gruppi di volumi e il Servizio Copia shadow del volume di Windows per generare backup coerenti con l'applicazione. È possibile usare Gestione snapshot StorSimple anche per creare pianificazioni di backup e clonare o ripristinare volumi.

Quando si configura un dispositivo per usare Gestione snapshot StorSimple, sarà necessario immettere la password di Gestione snapshot StorSimple. Questa password viene impostata in Windows PowerShell per StorSimple durante la registrazione. La password può essere impostata e cambiata anche dal servizio StorSimple Manager. Questa password autentica il dispositivo con Gestione snapshot StorSimple.

> [AZURE.IMPORTANT] <ul><li>Questa password deve essere composta da 14 o 15 caratteri e contenere 3 o più elementi di una combinazione di lettere maiuscole, lettere minuscole, numeri e caratteri speciali.</li><li>Dopo aver impostato la password di Gestione snapshot StorSimple, è possibile cambiarla, ma non recuperarla. Se si cambia la password, assicurarsi di notificarla a tutti gli utenti remoti.</li></ul>


### Procedure consigliate per le password

Si consiglia di usare le linee guida seguenti per assicurarsi che le password di Azure StorSimple siano complesse e protette:

- Cambiare le password ogni tre mesi.
- Usare password complesse. Per altre informazioni, vedere Creare password complesse.
- Usare sempre password diverse per ogni meccanismo di accesso. Ogni password specificata deve essere univoca.
- Non condividere le password con nessuno che non sia autorizzato ad accedere al dispositivo StorSimple.
- Non parlare di una password davanti ad altri e non accennare al formato di una password.
- Se si sospetta che un account o una password sia stata compromessa, segnalarlo al reparto sicurezza delle informazioni.
- Considerare tutte le password come informazioni sensibili e riservate. 

## Protezione dei dati di StorSimple

Questa sezione descrive le funzionalità di sicurezza di Azure StorSimple per i dati archiviati e in transito.

Come descritto in altre sezioni, per autorizzare e autenticare gli utenti in modo da consentire loro l'accesso alla soluzione Microsoft Azure StorSimple è previsto l'uso di password. Un'altra considerazione sulla sicurezza riguarda la protezione dei dati dagli utenti non autorizzati durante il trasferimento da un sistema di archiviazione a un altro e durante l'archiviazione. Le sezioni seguenti descrivono le funzionalità di protezione dei dati fornite con Azure StorSimple.

> [AZURE.NOTE] La deduplicazione fornisce una protezione aggiuntiva per i dati archiviati nel dispositivo StorSimple e nell'archiviazione di Microsoft Azure. Quando i dati vengono deduplicati, gli oggetti dati vengono archiviati separatamente dai metadati usati per la mappatura e l'accesso: non è disponibile un contesto a livello di archiviazione per ricostruire i dati in base alla struttura del volume, al file system o al nome file.

## Proteggere il flusso di dati attraverso il servizio

Lo scopo principale del servizio StorSimple Manager è di gestire e configurare il dispositivo StorSimple. Il servizio StorSimple Manager viene eseguito in Microsoft Azure. Si usa il portale di gestione di Windows Azure per immettere i dati di configurazione del dispositivo e quindi Microsoft Azure usa il servizio StorSimple Manager per inviare i dati al dispositivo. Il servizio StorSimple Manager usa un sistema di coppie di chiavi asimmetriche per garantire che, nel caso in cui venga compromesso il servizio di Azure, non vengano compromesse anche le informazioni archiviate. Il sistema di chiavi asimmetriche aiuta a proteggere il flusso di dati attraverso il sistema come segue:

1. Un certificato di crittografia dei dati che usa una coppia di chiavi pubblica e privata asimmetriche viene generato nel dispositivo e usato per proteggere i dati. Le chiavi vengono generate quando viene registrato il primo dispositivo. 
2. Le chiavi del certificato di crittografia dei dati vengono esportate in un file pfx (Personal Information Exchange) protetto dalla chiave DEK del servizio, che è una chiave a 128 bit complessa generata in modo casuale dal dispositivo durante la registrazione.
3. La chiave pubblica del certificato è resa disponibile in modo sicuro per il servizio StorSimple Manager e la chiave privata rimane con il dispositivo.
4. L'immissione di dati nel servizio viene crittografata con la chiave pubblica e decrittografata con la chiave privata archiviata nel dispositivo, in modo che il servizio di Azure non riesca a decrittografare il flusso di dati verso il dispositivo.

> [AZURE.IMPORTANT]
> 
> * La chiave DEK del servizio viene generata solo nel primo dispositivo registrato con il servizio. Ogni altro dispositivo registrato in seguito con il servizio deve usare la stessa chiave DEK del servizio. È molto importante creare una copia di questa chiave e salvarla in una posizione sicura. Si consiglia di archiviare una copia della chiave DEK del servizio in modo che sia accessibile a una persona autorizzata e possa essere facilmente comunicata all'amministratore del dispositivo.
> * Per modificare la chiave DEK del servizio e il corrispondente certificato di crittografia dei dati, selezionare l'opzione Modifica della chiave DEK del servizio nel dashboard del servizio. Per modificare le chiavi di crittografia, è necessario che ogni dispositivo venga aggiornato con la nuova chiave. Quindi, è consigliabile modificare la chiave quando tutti i dispositivi sono online. Se i dispositivi sono offline, le rispettive chiavi possono essere modificate in un altro momento. I dispositivi con chiavi scadute potranno ancora eseguire i backup, ma non potranno ripristinare i dati finché le chiavi non verranno aggiornate. Per altre informazioni, vedere [Dashboard servizi](https://msdn.microsoft.com/library/azure/dn772326.aspx).
> * Per essere certi che la sicurezza dei dati non venga compromessa, è necessario usare un dispositivo StorSimple fisico per modificare la chiave DEK del servizio.
> * Se si perde la chiave DEK del servizio, il personale di supporto Microsoft può aiutare a recuperarla purché almeno un dispositivo sia online. È consigliabile modificare la chiave DEK del servizio dopo averla recuperata. Per istruzioni, vedere [Modificare la chiave DEK del servizio](https://msdn.microsoft.com/library/azure/8158cbe9-1f26-4513-a031-49f88bb3d481#sec01).
> * La chiave DEK del servizio e il certificato di crittografia dei dati non scadono. Tuttavia è consigliabile modificare regolarmente la chiave DEK del servizio per evitare che venga compromessa.</li></ul>


## Proteggere i dati inattivi

Il dispositivo StorSimple gestisce i dati archiviandoli in più livelli localmente e nel cloud, in base alla frequenza d'uso. Tutti i computer host connessi al dispositivo inviano dati al dispositivo, che li sposta quindi nel cloud, se necessario. I dati vengono trasferiti dal dispositivo al cloud con il protocollo iSCSI. Ogni dispositivo ha una destinazione iSCSI che copre tutti i volumi condivisi del dispositivo. Tutti i dati vengono crittografati prima di essere inviati all'archiviazione cloud. Per garantire la sicurezza e l'integrità dei dati spostati nel cloud, Azure StorSimple consente di definire le chiavi di crittografia per l'archiviazione cloud come segue:

- Si specifica la chiave di crittografia per l'archiviazione cloud quando si crea un contenitore dei volumi. La chiave non può essere modificata o aggiunta in seguito. 
- Tutti i volumi in un contenitore condividono la stessa chiave di crittografia. Se si vuole una formato di crittografia diverso per un volume specifico, è consigliabile creare un nuovo contenitore per ospitare tale volume.
- Quando si immette la chiave di crittografia per l'archiviazione cloud nel servizio StorSimple Manager, la chiave viene crittografata con la parte pubblica della chiave DEK del servizio e quindi inviata al dispositivo.
- La chiave di crittografia per l'archiviazione cloud non viene archiviata nel servizio ed è nota solo al dispositivo.
- Specificare una chiave di crittografia per l'archiviazione cloud è facoltativo. È possibile inviare i dati crittografati nell'host al dispositivo.

## Proteggere i dati mediante gli account di archiviazione

Ogni sottoscrizione di Microsoft Azure può creare uno o più account di archiviazione. Un account di archiviazione fornisce uno spazio dei nomi univoco per lavorare con i dati archiviati nel cloud di Azure. L'accesso a un account di archiviazione è controllato dalle chiavi di sottoscrizione e accesso associate all'account di archiviazione. 

Quando si crea un account di archiviazione, Microsoft Azure genera due chiavi di accesso alle risorse di archiviazione da 512 bit, una delle quali viene usata per l'autenticazione quando il dispositivo StorSimple accede all'account di archiviazione. Una sola di queste chiavi è in uso. L'altra chiave è tenuta di riserva per far ruotare periodicamente le chiavi. Per far ruotare le chiavi, si rende attiva la chiave secondaria e quindi si elimina la chiave primaria. È quindi possibile creare una nuova chiave da usare durante la rotazione successiva. Per motivi di sicurezza, molti data center richiedono la rotazione delle chiavi. 

> [AZURE.IMPORTANT] 
> 
> Per la rotazione delle chiavi, è opportuno seguire queste procedure consigliate:
> 
> * Far ruotare regolarmente le chiavi dell'account di archiviazione per impedire a utenti non autorizzati di accedere all'account di archiviazione.
> * L'amministratore di Azure deve modificare o rigenerare periodicamente la chiave primaria o secondaria usando la sezione Archiviazione del portale di gestione per accedere direttamente all'account di archiviazione.


## Proteggere i dati mediante la crittografia

StorSimple usa i seguenti algoritmi di crittografia per proteggere i dati archiviati o trasmessi tra i vari componenti della soluzione StorSimple.

| Algoritmo | Lunghezza chiave | Protocolli/applicazioni/commenti |
| --------- | ---------- | ------------------------------- |
| RSA       | 2048       | RSA PKCS 1 v1.5 viene usato dal portale di gestione per crittografare i dati di configurazione inviati al dispositivo: ad esempio, le credenziali dell'account di archiviazione, la configurazione del dispositivo StorSimple e le chiavi di crittografia per l'archiviazione cloud. |
| AES       | 256        | AES con CBC viene usato per crittografare la parte pubblica della chiave DEK del servizio prima che venga inviata al portale di gestione dal dispositivo StorSimple. Viene usato anche dal dispositivo StorSimple per crittografare i dati prima che vengano inviati all'account di archiviazione cloud. |
| RSA       | 2048       | RSA PKCS 1 v1.7 viene usato dal portale di gestione per crittografare la parte pubblica della chiave DEK del servizio prima di archiviarla nella risorsa di archiviazione del portale locale. |


## Domande frequenti (FAQ)

Di seguito sono riportate alcune domande e risposte relative alla sicurezza e a Microsoft Azure StorSimple.

**D:** Il mio servizio è compromesso. Quali sono i passaggi successivi da eseguire?

**R:** È necessario modificare subito la chiave DEK del servizio e le chiavi dell'account di archiviazione per l'account usato per suddividere in livelli i dati. Per istruzioni, vedere: 

- [Modificare la chiave DEK del servizio](https://msdn.microsoft.com/library/azure/8158cbe9-1f26-4513-a031-49f88bb3d481#sec01)
- [Rotazione delle chiavi degli account di archiviazione](https://msdn.microsoft.com/library/azure/1747f56e-858a-4cfe-a020-949d7db23b8b#rotate)

**D:** Ho un nuovo dispositivo StorSimple per cui è richiesta la chiave di registrazione del servizio. Come posso ottenerla?

**R:** Questa chiave è stato creata al momento della creazione del servizio StorSimple Manager. Quando si usa il servizio StorSimple Manager per connettersi al dispositivo, è possibile usare la pagina di avvio rapido del servizio per visualizzare o rigenerare la chiave di registrazione del servizio. La generazione di una nuova chiave di registrazione del servizio non influisce sui dispositivi registrati esistenti. Per istruzioni, vedere:

- [Visualizzare o rigenerare la chiave di registrazione del servizio](https://msdn.microsoft.com/library/azure/8158cbe9-1f26-4513-a031-49f88bb3d481#BKMK_viewsrk)

**D:** Ho perso la chiave DEK del servizio. Cosa devo fare?

**R:** Contattare il supporto tecnico Microsoft che può accedere a una sessione di supporto sul dispositivo e aiutare a recuperare la chiave. Subito dopo aver recuperato la chiave, è consigliabile modificare la chiave DEK del servizio per garantire che la nuova chiave sia nota solo all'utente. Per istruzioni, vedere:

- [Modificare la chiave DEK del servizio](https://msdn.microsoft.com/library/azure/8158cbe9-1f26-4513-a031-49f88bb3d481#sec01)

**D:** Ho autorizzato un dispositivo per la modifica della chiave DEK del servizio, ma il processo di modifica non è stato avviato. Cosa devo fare?

**R:** Se il periodo di timeout è scaduto, sarà necessario autorizzare nuovamente il dispositivo per la modifica della chiave DEK del servizio e riavviare il processo.

**D:** Ho modificato la chiave DEK del servizio, ma non sono riuscito ad aggiornare gli altri dispositivi entro le 4 ore. Devo avviare nuovamente il processo?

**R:** Il periodo di 4 ore è solo per l'avvio del processo di modifica. Dopo aver avviato il processo di aggiornamento sul dispositivo StorSimple autorizzato, l'autorizzazione è valida fino all'aggiornamento di tutti gli altri dispositivi.

**D:** L'amministratore di StorSimple ha lasciato l'azienda. Cosa devo fare?

**R:** Modificare e reimpostare le password che consentono di accedere al dispositivo StorSimple e modificare la chiave DEK del servizio per garantire che le nuove informazioni non siano note al personale non autorizzato. Per istruzioni, vedere:

- [Configurare Gestione snapshot StorSimple](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec08)
- [Configurare la password amministratore del dispositivo](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec09)
- [Configurare la gestione remota](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec10)
- [Configurare l'iniziatore dell'autenticazione CHAP](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec06)
- [Configurare la destinazione dell'autenticazione CHAP](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec07)

**D:** Vorrei fornire la password di Gestione snapshot StorSimple a un host che si connette al dispositivo StorSimple, ma la password non è disponibile. Cosa posso fare?

**R:** Se la password è stata dimenticata, crearne una nuova. Informare quindi tutti gli utenti esistenti che la password è stata modificata e che devono aggiornare i client per usare la nuova password. Per istruzioni, vedere:

- [Configurare Snapshot Manager](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec08)
- [Autenticazione di un dispositivo](https://msdn.microsoft.com/library/azure/dn790537.aspx)

**D:** Il certificato per l'accesso remoto a Windows PowerShell per StorSimple è stato modificato sul dispositivo. Come posso aggiornare i client di accesso remoto?

**R:** È possibile scaricare il nuovo certificato dal servizio StorSimple Manager, quindi inviarlo per l'installazione nell'archivio certificati dei client di accesso remoto. Per istruzioni, vedere:

- [Cmdlet Import-Certificate](https://technet.microsoft.com/library/hh848630.aspx)

**D:** Se il servizio StorSimple Manager risulta compromesso, i miei dati sono protetti?

**R:** Quando vengono visualizzati nel Web browser, i dati di configurazione del servizio sono sempre crittografati con la chiave pubblica dell'utente. Poiché il servizio non può accedere alla chiave privata, non potrà visualizzare alcun dato. Se il servizio StorSimple Manager è compromesso, l'impatto è nullo perché nel servizio non è archiviata alcuna chiave.

**D:** Se un utente riesce ad accedere al certificato di crittografia dei dati, i miei dati verranno compromessi?

**R:** Microsoft Azure archivia la chiave DEK del cliente (file pfx) in un formato crittografato. Poiché il file pfx è crittografato e il servizio StorSimple non ha la chiave DEK del servizio per decrittografarlo, il semplice accesso al file pfx non espone alcun dato segreto.

**D:** Cosa accade se un ente pubblico chiede i miei dati a Microsoft?

**R:** Poiché tutti i dati sono crittografati nel servizio e la chiave privata è archiviata con il dispositivo, l'ente pubblico deve richiedere i dati al cliente. 

## Passaggi successivi

[Introduzione al dispositivo fisico](https://msdn.microsoft.com/library/azure/dn772410.aspx)


<!--HONumber=52--> 