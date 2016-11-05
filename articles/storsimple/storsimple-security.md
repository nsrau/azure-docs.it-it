---
title: Sicurezza in StorSimple | Microsoft Docs
description: Descrive le funzionalità di sicurezza e privacy che proteggono il servizio, il dispositivo e i dati StorSimple.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/03/2016
ms.author: v-sharos

---
# Sicurezza e protezione dei dati di StorSimple
## Panoramica
La sicurezza è una delle principali preoccupazioni per chi ha intenzione di adottare una nuova tecnologia, soprattutto se usata con dati riservati o proprietari. Quando si valutano tecnologie diverse, è necessario tenere in considerazione l'aumento dei rischi e dei costi per la protezione dei dati. Microsoft Azure StorSimple offre una soluzione sia di sicurezza che di privacy per la protezione dei dati, che aiuta a garantire:

* **Riservatezza** – Solo le entità autorizzate possono visualizzare i dati. 
* **Integrità** – Solo le entità autorizzate possono modificare o eliminare i dati.

La soluzione Microsoft Azure StorSimple è costituita da quattro componenti principali che interagiscono tra loro:

* **Servizio StorSimple Manager ospitato in Microsoft Azure** – Servizio di gestione usato per la configurazione e il provisioning del dispositivo StorSimple.
* **Dispositivo StorSimple** – Dispositivo fisico installato nel data center. Tutti gli host e i client che generano dati si connettono al dispositivo StorSimple e il dispositivo gestisce i dati e li sposta nel cloud Azure come necessario.
* **Client/host connessi al dispositivo** – Client nell'infrastruttura che si connettono al dispositivo StorSimple e generano i dati da proteggere.
* **Archiviazione cloud** – Posizione nel cloud di Azure in cui vengono archiviati i dati.

Le sezioni seguenti descrivono le funzionalità di sicurezza di StorSimple che consentono di proteggere i singoli componenti e i relativi dati archiviati. Include inoltre un elenco di domande e risposte relative alla sicurezza di Microsoft Azure StorSimple.

## Protezione del servizio StorSimple Manager
StorSimple Manager è un servizio di gestione ospitato in Microsoft Azure che consente di gestire tutti i dispositivi StorSimple installati all'interno di un'organizzazione. È possibile accedere al servizio StorSimple Manager usando le credenziali aziendali nel portale di Azure classico con un Web browser.

Per l'accesso al servizio StorSimple Manager, l'organizzazione deve disporre di una sottoscrizione Azure in cui sia incluso StorSimple. La sottoscrizione determina le funzionalità con cui accedere nel portale di Azure classico. Se l'organizzazione non dispone ancora di una sottoscrizione di Azure e si desidera ottenere informazioni al riguardo, vedere [Iscrizione ad Azure come organizzazione](../active-directory/sign-up-organization.md).

Poiché il servizio StorSimple Manager è ospitato in Azure, viene protetto dalle funzionalità di sicurezza di Azure. Per altre informazioni sulle funzionalità di sicurezza fornite da Microsoft Azure, andare in [Centro protezione Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

## Protezione del dispositivo StorSimple
Il dispositivo StorSimple è un dispositivo di archiviazione ibrido locale contenente unità SSD e unità disco rigido, oltre a controller ridondanti e funzionalità di failover automatico. I controller gestiscono la suddivisione in livelli dell'archiviazione, inserendo i dati attualmente usati (o hot data) nella risorsa di archiviazione locale (nel dispositivo StorSimple o nei server locali) e spostando nel cloud i dati usati meno di frequente.

Solo i dispositivi StorSimple autorizzati possono aggiungere il servizio StorSimple Manager creato nella sottoscrizione di Azure. Per autorizzare un dispositivo, è necessario registrarlo con il servizio StorSimple Manager inserendo la chiave di registrazione del servizio. La chiave di registrazione del servizio è un codice casuale a 128 bit generato nel portale di Azure classico.

![Chiave di registrazione del servizio](./media/storsimple-security/ServiceRegistrationKey.png)

Per informazioni su come ottenere una chiave di registrazione del servizio, vedere [Passaggio 2: Ottenere la chiave di registrazione del servizio](storsimple-deployment-walkthrough.md#step-2-get-the-service-registration-key).

La chiave di registrazione del servizio è un codice lungo contenente più di 100 caratteri. È possibile copiare la chiave e salvarla in un file di testo in un percorso sicuro per poterla usare per autorizzare altri dispositivi, se necessario. Se si perde la chiave di registrazione del servizio dopo aver registrato il primo dispositivo, è possibile generare una nuova chiave dal servizio StorSimple Manager. L'operazione non avrà effetto sul funzionamento dei dispositivi esistenti.

Un dispositivo, dopo essere stato registrato, usa i token per comunicare con Microsoft Azure. La chiave di registrazione del servizio non viene usata dopo la registrazione del dispositivo.

> [!NOTE]
> È consigliabile rigenerare la chiave di registrazione del servizio dopo ogni uso.
> 
> 

## Proteggere la soluzione StorSimple con le password
Le password sono un componente importante della sicurezza dei computer e vengono usate di frequente nella soluzione StorSimple per garantire che i dati siano accessibili solo a utenti autorizzati. StorSimple consente di configurare le seguenti password:

* Password amministratore del dispositivo StorSimple
* Password di destinazione e iniziatore CHAP (Challenge Handshake Authentication Protocol)
* Password di Gestione snapshot StorSimple

### Windows PowerShell per StorSimple e la password amministratore del dispositivo StorSimple
Windows PowerShell per StorSimple è un'interfaccia della riga di comando che può essere usata per gestire il dispositivo StorSimple. Windows PowerShell per StorSimple include funzionalità che permettono di registrare il dispositivo, configurare l'interfaccia di rete nel dispositivo, installare determinati tipi di aggiornamento, risolvere i problemi del dispositivo tramite l'accesso alla sessione di supporto e modificare lo stato del dispositivo. È possibile accedere a Windows PowerShell per StorSimple connettendosi alla console seriale sul dispositivo o usando la comunicazione remota di Windows PowerShell.

La comunicazione remota di PowerShell può essere stabilita su HTTPS o HTTP. Se la gestione remota su HTTPS è abilitata, sarà necessario scaricare il certificato di gestione remota dal dispositivo e installarlo nel client remoto. Per altre informazioni sulla comunicazione remota di PowerShell, vedere [Connettersi in remoto al dispositivo StorSimple](storsimple-remote-connect.md).

Dopo aver usato Windows PowerShell per StorSimple per connettersi al dispositivo, sarà necessario immettere la password amministratore per accedere al dispositivo.

![Password amministratore del dispositivo](./media/storsimple-security/DeviceAdminPW.png)

Tenere presenti le procedure consigliate seguenti:

* La gestione remota è disattivata per impostazione predefinita. Per abilitarla, è possibile usare il servizio StorSimple Manager. Come procedura consigliata per la sicurezza, è opportuno abilitare l'accesso remoto solo per il periodo di tempo in cui è effettivamente necessario.
* Se si cambia la password, assicurarsi di notificarlo a tutti gli utenti di accesso remoto per evitare una perdita imprevista della connettività.
* Il servizio StorSimple Manager non può recuperare le password esistenti, ma solo reimpostarle. È consigliabile archiviare tutte le password in un luogo sicuro per non dover reimpostare una password nel caso la si dimentichi. Se è necessario reimpostare una password, assicurarsi di notificarlo a tutti gli utenti prima di reimpostarla. 

È possibile accedere all'interfaccia di Windows PowerShell usando una connessione seriale al dispositivo. È anche possibile accedervi in modalità remota usando HTTP o HTTPS, che offrono una maggiore sicurezza. HTTPS offre un livello più elevato di sicurezza rispetto a una connessione seriale o HTTP. Per usare HTTPS, tuttavia, prima è necessario installare un certificato nel computer client che accederà al dispositivo. È possibile scaricare il certificato di accesso remoto dalla pagina di configurazione del dispositivo nel servizio StorSimple Manager. Se il certificato per l'accesso remoto viene perso, è necessario scaricare un nuovo certificato e propagarlo a tutti i client autorizzati a usare la gestione remota.

### Password di destinazione e iniziatore CHAP (Challenge Handshake Authentication Protocol)
CHAP è uno schema di autenticazione usato dal dispositivo StorSimple per convalidare l'identità dei client remoti. La verifica si basa su una password condivisa. CHAP può essere unidirezionale o bidirezionale (reciproco). Con CHAP unidirezionale, la destinazione (il dispositivo StorSimple) autentica un iniziatore (host). Per l'autenticazione CHAP reciproca è necessario che la destinazione autentichi l'iniziatore e viceversa. Azure StorSimple può essere configurato per l'utilizzo di entrambi i metodi.

Quando si configura l'autenticazione CHAP, tenere presente quanto segue:

* Il nome dell'utente CHAP deve contenere meno di 233 caratteri.
* La password CHAP deve essere compresa tra 12 e 16 caratteri. Se si prova a usare un nome utente o una password più lunga, si verificherà un errore di autenticazione sull'host Windows.
* Non è possibile usare la stessa password sia per l'iniziatore CHAP che per la destinazione CHAP.
* Dopo aver impostato la password, è possibile cambiarla, ma non recuperarla. Se la password viene cambiata, assicurarsi di notificarlo a tutti gli utenti di accesso remoto in modo che riescano a connettersi al dispositivo StorSimple.

Per altre informazioni sull'autenticazione CHAP e su come configurarla per la soluzione StorSimple, vedere [Configurare CHAP per il dispositivo StorSimple](storsimple-configure-chap.md).

### Password di Gestione snapshot StorSimple
Gestione snapshot StorSimple è uno snap-in MMC (Microsoft Management Console) che usa gruppi di volumi e il Servizio Copia shadow del volume di Windows per generare backup coerenti con l'applicazione. È possibile usare Gestione snapshot StorSimple anche per creare pianificazioni di backup e clonare o ripristinare volumi.

Quando si configura un dispositivo per usare Gestione snapshot StorSimple, è necessario immettere la password di Gestione snapshot StorSimple. Questa password viene impostata in Windows PowerShell per StorSimple durante la registrazione. La password può essere impostata e cambiata anche dal servizio StorSimple Manager. Questa password autentica il dispositivo con Gestione snapshot StorSimple.

![Password di Gestione snapshot StorSimple](./media/storsimple-security/SnapshotMgrPassword.png)

La password di Gestione snapshot StorSimple deve essere composta da 14 o 15 caratteri e contenere 3 o più elementi di una combinazione di lettere maiuscole, lettere minuscole, numeri e caratteri speciali. Dopo aver impostato la password di Gestione snapshot StorSimple, è possibile cambiarla, ma non recuperarla. Se si cambia la password, assicurarsi di notificarla a tutti gli utenti remoti.

Per altre informazioni su Gestione snapshot StorSimple, vedere [Che cos'è Gestione snapshot StorSimple?](storsimple-what-is-snapshot-manager.md).

### Procedure consigliate per le password
È consigliabile usare le linee guida seguenti per assicurarsi che le password di StorSimple siano complesse e protette:

* Cambiare le password ogni tre mesi. La modifica della password viene richiesta ogni anno.
* Usare password complesse. Per altre informazioni, vedere [Creare password più complesse e proteggerle](http://blogs.microsoft.com/cybertrust/2014/08/25/create-stronger-passwords-and-protect-them/).
* Usare sempre password diverse per ogni meccanismo di accesso. Ogni password specificata deve essere univoca.
* Non condividere le password con nessuno che non sia autorizzato ad accedere al dispositivo StorSimple.
* Non parlare di una password davanti ad altri e non accennare al formato di una password.
* Se si sospetta che un account o una password sia stata compromessa, segnalarlo al reparto sicurezza delle informazioni.
* Considerare tutte le password come informazioni sensibili e riservate. 

## Protezione dei dati di StorSimple
Questa sezione descrive le funzionalità di sicurezza di StorSimple per i dati archiviati e in transito.

Come descritto in altre sezioni, per autorizzare e autenticare gli utenti in modo da consentire loro l'accesso alla soluzione StorSimple è previsto l'uso di password. Un'altra considerazione sulla sicurezza riguarda la protezione dei dati dagli utenti non autorizzati durante il trasferimento da un sistema di archiviazione a un altro e durante l'archiviazione. Le sezioni seguenti descrivono le funzionalità di protezione dei dati offerte da StorSimple.

> [!NOTE]
> La deduplicazione fornisce una protezione aggiuntiva per i dati archiviati nel dispositivo StorSimple e nell'archiviazione di Microsoft Azure. Quando i dati vengono deduplicati, gli oggetti dati vengono archiviati separatamente dai metadati usati per la mappatura e l'accesso: non è disponibile un contesto a livello di archiviazione per ricostruire i dati in base alla struttura del volume, al file system o al nome file.
> 
> 

## Proteggere il flusso di dati attraverso il servizio
Lo scopo principale del servizio StorSimple Manager è di gestire e configurare il dispositivo StorSimple. Il servizio StorSimple Manager viene eseguito in Microsoft Azure. Si usa il portale di Azure classico per immettere i dati di configurazione del dispositivo, poi Microsoft Azure usa il servizio StorSimple Manager per inviare i dati al dispositivo. StorSimple usa un sistema di coppie di chiavi asimmetriche per garantire che, nel caso in cui venga compromesso il servizio di Azure, non vengano compromesse anche le informazioni archiviate.

![Crittografia dei dati in esecuzione](./media/storsimple-security/DataEncryption.png)

Il sistema di chiavi asimmetriche aiuta a proteggere il flusso di dati attraverso il sistema come segue:

1. Un certificato di crittografia dei dati che usa una coppia di chiavi pubblica e privata asimmetriche viene generato nel dispositivo e usato per proteggere i dati. Le chiavi vengono generate quando viene registrato il primo dispositivo. 
2. Le chiavi del certificato di crittografia dei dati vengono esportate in un file con estensione pfx (Personal Information Exchange) protetto dalla chiave DEK del servizio, che è una chiave a 128 bit complessa generata in modo casuale dal primo dispositivo durante la registrazione.
3. La chiave pubblica del certificato è resa disponibile in modo sicuro per il servizio StorSimple Manager e la chiave privata rimane con il dispositivo.
4. L'immissione di dati nel servizio viene crittografata con la chiave pubblica e decrittografata con la chiave privata archiviata nel dispositivo, in modo che il servizio di Azure non riesca a decrittografare il flusso di dati verso il dispositivo.

La chiave DEK del servizio viene generata solo nel primo dispositivo registrato nel servizio. Ogni altro dispositivo registrato in seguito con il servizio deve usare la stessa chiave DEK del servizio.

> [!IMPORTANT]
> È molto importante creare una copia di questa chiave e salvarla in una posizione sicura. Si consiglia di archiviare una copia della chiave DEK del servizio in modo che sia accessibile a una persona autorizzata e possa essere facilmente comunicata all'amministratore del dispositivo.
> 
> Se si perde la chiave DEK del servizio, il personale di supporto Microsoft può aiutare a recuperarla purché almeno un dispositivo sia online. È consigliabile modificare la chiave DEK del servizio dopo averla recuperata. Per istruzioni, vedere [Modificare la chiave DEK del servizio](storsimple-service-dashboard.md#change-the-service-data-encryption-key).
> 
> 

Per modificare la chiave DEK del servizio e il corrispondente certificato di crittografia dei dati, selezionare l'opzione **Modifica chiave DEK del servizio** nel dashboard del servizio. Per essere certi che la sicurezza dei dati non venga compromessa, è necessario usare un dispositivo StorSimple fisico per modificare la chiave DEK del servizio. Per modificare le chiavi di crittografia, è necessario che ogni dispositivo venga aggiornato con la nuova chiave. Quindi, è consigliabile modificare la chiave quando tutti i dispositivi sono online. Se i dispositivi sono offline, le rispettive chiavi possono essere modificate in un altro momento. I dispositivi con chiavi scadute potranno ancora eseguire i backup, ma non potranno ripristinare i dati finché le chiavi non verranno aggiornate. Per altre informazioni, vedere l'articolo relativo all'[uso del dashboard del servizio StorSimple Manager](storsimple-service-dashboard.md).

La chiave DEK del servizio e il certificato di crittografia dei dati non scadono. Tuttavia, è consigliabile modificare regolarmente la chiave DEK del servizio per evitare che venga compromessa.

## Proteggere i dati inattivi
Il dispositivo StorSimple gestisce i dati archiviandoli in più livelli localmente e nel cloud, in base alla frequenza d'uso. Tutti i computer host connessi al dispositivo inviano dati al dispositivo, che li sposta quindi nel cloud, se necessario. I dati vengono trasferiti dal dispositivo al cloud in modo sicuro tramite Internet. Ogni dispositivo ha una destinazione iSCSI che copre tutti i volumi condivisi del dispositivo. Tutti i dati vengono crittografati prima di essere inviati all'archiviazione cloud.

![Chiave di crittografia di archiviazione cloud](./media/storsimple-security/CloudStorageEncryption.png)

Per garantire la sicurezza e l'integrità dei dati spostati nel cloud, StorSimple consente di definire le chiavi di crittografia per l'archiviazione cloud come segue:

* Si specifica la chiave di crittografia per l'archiviazione cloud quando si crea un contenitore dei volumi. La chiave non può essere modificata o aggiunta in seguito. 
* Tutti i volumi in un contenitore condividono la stessa chiave di crittografia. Se si vuole una formato di crittografia diverso per un volume specifico, è consigliabile creare un nuovo contenitore per ospitare tale volume.
* Quando si immette la chiave di crittografia per l'archiviazione cloud nel servizio StorSimple Manager, la chiave viene crittografata con la parte pubblica della chiave DEK del servizio e quindi inviata al dispositivo.
* La chiave di crittografia per l'archiviazione cloud non viene archiviata nel servizio ed è nota solo al dispositivo.
* Specificare una chiave di crittografia per l'archiviazione cloud è facoltativo. È possibile inviare i dati crittografati nell'host al dispositivo.

### Procedure di sicurezza aggiuntive
* Suddividere il traffico: isolare la rete SAN iSCSI dal traffico utente in una LAN aziendale distribuendo una rete completamente separata e utilizzando le VLAN dove l’isolamento fisico non è un'opzione. Una rete dedicata per l'archiviazione iSCSI garantisce sicurezza e prestazioni dei dati aziendali critici. Combinare il traffico di archiviazione e il traffico utente su una rete LAN aziendale non è consigliato e può aumentare la latenza e causare errori di rete.
* Per la sicurezza di rete sul lato host, utilizzare le interfacce di rete che supportano il protocollo TCP/IP Offload Engine (TOE). Il TOE riduce il carico della CPU elaborando TCP nella scheda di rete.

## Proteggere i dati mediante gli account di archiviazione
Ogni sottoscrizione di Microsoft Azure può creare uno o più account di archiviazione. Un account di archiviazione fornisce uno spazio dei nomi univoco per lavorare con i dati archiviati nel cloud di Azure. L'accesso a un account di archiviazione è controllato dalle chiavi di sottoscrizione e accesso associate all'account di archiviazione.

Quando si crea un account di archiviazione, Microsoft Azure genera due chiavi di accesso alle risorse di archiviazione da 512 bit, una delle quali viene usata per l'autenticazione quando il dispositivo StorSimple accede all'account di archiviazione. Una sola di queste chiavi è in uso. L'altra chiave è tenuta di riserva per far ruotare periodicamente le chiavi. Per far ruotare le chiavi, si rende attiva la chiave secondaria e quindi si elimina la chiave primaria. È quindi possibile creare una nuova chiave da usare durante la rotazione successiva. Per motivi di sicurezza, molti data center richiedono la rotazione delle chiavi.

Per la rotazione delle chiavi, è opportuno seguire queste procedure consigliate:

* Far ruotare regolarmente le chiavi dell'account di archiviazione per impedire a utenti non autorizzati di accedere all'account di archiviazione.
* L'amministratore di Azure deve modificare o rigenerare periodicamente la chiave primaria o secondaria usando la sezione Archiviazione del portale di Azure classico per accedere direttamente all'account di archiviazione.

## Proteggere i dati mediante la crittografia
StorSimple usa i seguenti algoritmi di crittografia per proteggere i dati archiviati o trasmessi tra i vari componenti della soluzione StorSimple.

| Algoritmo | Lunghezza chiave | Protocolli/applicazioni/commenti |
| --- | --- | --- |
| RSA |2048 |RSA PKCS 1 v1.5 viene usato dal portale di Azure classico per crittografare i dati di configurazione inviati al dispositivo: ad esempio, le credenziali dell'account di archiviazione, la configurazione del dispositivo StorSimple e le chiavi di crittografia per l'archiviazione cloud. |
| AES |256 |AES con CBC viene usato per crittografare la parte pubblica della chiave DEK del servizio prima che venga inviata al portale di Azure classico dal dispositivo StorSimple. Viene usato anche dal dispositivo StorSimple per crittografare i dati prima che vengano inviati all'account di archiviazione cloud. |

## Sicurezza del dispositivo virtuale StorSimple
[!INCLUDE [sicurezza del dispositivo virtuale StorSimple](../../includes/storsimple-virtual-device-security.md)]

## Domande frequenti (FAQ)
Di seguito sono riportate alcune domande e risposte relative alla sicurezza e a Microsoft Azure StorSimple.

**D:** Il servizio è compromesso. Quali sono i passaggi successivi da eseguire?

**R:** È necessario modificare subito la chiave DEK del servizio e le chiavi dell'account di archiviazione per l'account usato per suddividere i dati in livelli. Per istruzioni, vedere:

* [Modificare la chiave DEK del servizio](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
* [Rotazione delle chiavi degli account di archiviazione](storsimple-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**D:** Ho un nuovo dispositivo StorSimple per cui viene richiesta la chiave di registrazione del servizio. Come posso ottenerla?

**R:** Questa chiave è stata creata al momento della creazione del servizio StorSimple Manager. Quando si usa il servizio StorSimple Manager per connettersi al dispositivo, è possibile usare la pagina di avvio rapido del servizio per visualizzare o rigenerare la chiave di registrazione del servizio. La generazione di una nuova chiave di registrazione del servizio non influirà sui dispositivi registrati esistenti. Per istruzioni, vedere:

* [Visualizzare o rigenerare la chiave di registrazione del servizio](storsimple-service-dashboard.md#view-or-regenerate-the-service-registration-key)

**D:** Ho perso la chiave DEK del servizio. Cosa devo fare?

**R:** Contattare il supporto Microsoft. Il personale può accedere a una sessione di supporto nel dispositivo e offrire assistenza per il recupero della chiave (ammesso che almeno un dispositivo sia online). Subito dopo aver ottenuto la chiave DEK del servizio, è consigliabile modificarla per garantire che la nuova chiave sia nota solo all'utente che la modifica. Per istruzioni, vedere:

* [Modificare la chiave DEK del servizio](storsimple-service-dashboard.md#change-the-service-data-encryption-key)

**D:** Ho autorizzato un dispositivo per la modifica della chiave DEK del servizio, ma il processo di modifica della chiave non è stato avviato. Cosa devo fare?

**R:** Se il periodo di timeout è scaduto, sarà necessario autorizzare nuovamente il dispositivo per la modifica della chiave DEK del servizio e riavviare il processo.

**D:** Ho modificato la chiave DEK del servizio, ma non ho potuto aggiornare gli altri dispositivi entro 4 ore. Devo avviare nuovamente il processo?

**R:** Il periodo di tempo di 4 ore si riferisce solo all'avvio della modifica. Dopo aver avviato il processo di aggiornamento sul dispositivo StorSimple autorizzato, l'autorizzazione è valida fino all'aggiornamento di tutti gli altri dispositivi.

**D:** L'amministratore di StorSimple ha lasciato l'azienda. Cosa devo fare?

**R:** Modificare e reimpostare le password che consentono di accedere al dispositivo StorSimple e modificare la chiave DEK del servizio per garantire che le nuove informazioni non siano note al personale non autorizzato. Per istruzioni, vedere:

* [Utilizzare il servizio StorSimple Manager per modificare la password di StorSimple](storsimple-change-passwords.md)
* [Modificare la chiave DEK del servizio](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
* [Configurare CHAP per il dispositivo StorSimple](storsimple-configure-chap.md)

**D:** Vorrei comunicare la password di Gestione snapshot StorSimple a un host che si connette al dispositivo StorSimple, ma la password non è disponibile. In che modo è possibile risolvere questo problema?

**R:** Se si dimentica la password, è necessario crearne una nuova. Informare quindi tutti gli utenti esistenti che la password è stata modificata e che devono aggiornare i client per usare la nuova password. Per istruzioni, vedere:

* [Configurare la password di StorSimple Snapshot Manager](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password)
* [Autenticazione di un dispositivo](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**D:** Il certificato per l'accesso remoto a Windows PowerShell per StorSimple è stato modificato nel dispositivo. Come posso aggiornare i client di accesso remoto?

**R:** È possibile scaricare il nuovo certificato dal servizio StorSimple Manager, quindi inviarlo per l'installazione nell'archivio certificati dei client di accesso remoto. Per istruzioni, vedere:

* [Cmdlet Import-Certificate](https://technet.microsoft.com/library/hh848630.aspx)

**D:** Se il servizio StorSimple Manager risulta compromesso, i miei dati sono protetti?

**R:** I dati di configurazione del servizio sono sempre crittografati con la chiave pubblica quando vengono visualizzati in un Web browser. Poiché il servizio non ha accesso alla chiave privata, il servizio non sarà in grado di visualizzare i dati. La compromissione del servizio StorSimple Manager non ha alcun impatto, perché non sono presenti chiavi archiviate nel servizio StorSimple Manager.

**D:** Se un utente riesce ad accedere al certificato di crittografia dei dati, i miei dati verranno compromessi?

**R:** Microsoft Azure archivia la chiave di crittografia dei dati del cliente (file con estensione .pfx) in formato crittografato. Dato che il file con estensione pfx è crittografato e il servizio StorSimple non ha la chiave DEK del servizio per decrittografarlo, il semplice accesso al file con estensione pfx non espone alcun dato segreto.

**D:** Cosa accade se un ente pubblico chiede i miei dati a Microsoft?

**R:** Poiché tutti i dati sono crittografati nel servizio e la chiave privata è archiviata con il dispositivo, l'ente pubblico deve richiedere i dati al cliente.

## Passaggi successivi
[Distribuire il dispositivo StorSimple](storsimple-deployment-walkthrough.md).

<!---HONumber=AcomDC_0511_2016-->