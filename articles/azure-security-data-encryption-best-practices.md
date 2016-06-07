<properties
   pageTitle="Procedure consigliate per la sicurezza dei dati e la crittografia | Microsoft Azure"
   description="Questo articolo presenta una serie di procedure consigliate per la sicurezza dei dati e la crittografia usando le funzionalità integrate di Azure."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/23/2016"
   ms.author="yuridio"/>

#Procedure consigliate per la sicurezza dei dati e la crittografia in Azure

Uno degli aspetti fondamentali della protezione dei dati nel cloud consiste nel tenere conto dei possibili stati in cui possono trovarsi i dati e dei controlli disponibili per tale stato. Per quanto concerne le procedure consigliate per la sicurezza dei dati e la crittografia in Azure, le raccomandazioni riguarderanno gli stati dei dati seguenti:

- Inattivi: sono inclusi tutti gli oggetti, i contenitori e i tipi di archiviazione di informazioni esistenti in forma statica nei supporti fisici, siano essi dischi magnetici o dischi ottici.

- In transito: quando i dati vengono trasferiti dati tra componenti, posizioni o programmi, ad esempio sulla rete, attraverso un bus di servizio (da locale a cloud e viceversa, incluse le connessioni ibride come ExpressRoute) oppure durante un processo di input/output, sono considerati in movimento.

In questo articolo verrà illustrato un insieme di procedure consigliate per la crittografia e la sicurezza dei dati di Azure, derivate dalla nostra esperienza con la sicurezza dei dati e la crittografia in Azure e dalle esperienze di altri clienti.

Per ogni procedura consigliata verrà illustrato:

- Qual è la procedura consigliata
- Il motivo per cui si vuole abilitare tale procedura consigliata
- Quale potrebbe essere il risultato se non fosse possibile abilitare la procedura consigliata
- Alternative possibili alla procedura consigliata
- Come imparare ad abilitare la procedura consigliata

Il presente articolo sulle procedure consigliate per la sicurezza dei dati e la crittografia in Azure si basa su un parere condiviso, nonché sulle capacità e sui set di funzionalità della piattaforma Azure esistenti al momento della scrittura. Le opinioni e le tecnologie cambiano nel tempo e questo articolo verrà aggiornato regolarmente per riflettere tali modifiche.

Le procedure consigliate per la sicurezza dei dati e la crittografia in Azure illustrate in questo articolo includono:

- Applicare l'autenticazione a più fattori
- Usare il controllo degli accessi in base al ruolo
- Crittografare le macchine virtuali di Azure
- Usare i moduli di protezione hardware
- Gestire con workstation sicure
- Abilitare la crittografia dei dati SQL
- Proteggere i dati in transito
- Applicare la crittografia dei dati a livello di file


## Applicare l'autenticazione a più fattori

Il primo passo per accedere ai dati e controllarli in Microsoft Azure consiste nell'autenticare l'utente. [Azure Multi-Factor Authentication (MFA)](./multi-factor-authentication/multi-factor-authentication.md) è un metodo per verificare l'identità dell'utente usando un metodo diverso rispetto alla semplice combinazione di nome utente e password. Questo metodo di autenticazione contribuisce a salvaguardare l'accesso a dati e applicazioni rispondendo alla richiesta degli utenti di poter usare un processo di accesso semplice.

Quando si abilita Azure MFA per gli utenti, si aggiunge un secondo livello di sicurezza agli accessi e alle transazioni degli utenti. In questo caso, una transazione potrebbe accedere a un documento che si trova in un file server o in SharePoint Online. Grazie a Azure MFA, i reparti IT possono anche ridurre la probabilità di accesso ai dati dell'organizzazione usando credenziali compromesse.

Ad esempio, se Azure MFA viene applicato per gli utenti e configurato per l'uso di una telefonata o di un messaggio di testo come verifica, qualora le credenziali dell'utente fossero compromesse un autore di attacchi non riuscirebbe ad accedere alle risorse, non avendo accesso al telefono dell'utente. Le organizzazioni che non aggiungono questo livello supplementare di protezione dell'identità sono più vulnerabili agli attacchi con furto di credenziali, con conseguente rischio di compromissione dei dati.

In alternativa, le organizzazioni che vogliono mantenere il controllo di autenticazione in locale possono usare il [server Microsoft Azure Multi-Factor Authentication](./multi-factor-authentication/multi-factor-authentication-get-started-server.md), detto anche MFA locale. Questo metodo consentirà comunque di applicare l'autenticazione a più fattori, mantenendo il server MFA in locale.

Per altre informazioni sull'autenticazione a più fattori di Azure, vedere [Introduzione ad Azure Multi-Factor Authentication nel cloud](./multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## Usare il controllo degli accessi in base al ruolo
Limitare l'accesso in base al [principio di necessità](https://en.wikipedia.org/wiki/Need_to_know) e al [principio di privilegio minimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege) in materia di sicurezza. Questo è fondamentale per le organizzazioni che intendono applicare criteri di sicurezza per l'accesso ai dati. Il controllo degli accessi in base al ruolo di Azure consente di assegnare autorizzazioni a utenti, gruppi e applicazioni in un determinato ambito. L'ambito di un'assegnazione di ruolo può essere una sottoscrizione, un gruppo di risorse o una singola risorsa.

Si possono sfruttare i [ruoli predefiniti del controllo degli accessi in base al ruolo](./active-directory/role-based-access-built-in-roles.md) in Azure per assegnare privilegi agli utenti. Si consiglia di usare *Collaboratore Account di archiviazione* per gli operatori cloud che devono gestire gli account di archiviazione e il ruolo *Collaboratore Account di archiviazione classico* per gestire gli account di archiviazione classici. Per quanto riguarda gli operatori di cloud che hanno necessità di gestire le macchine virtuali e l'account di archiviazione, è consigliabile aggiungerli al ruolo *Collaboratore Macchina virtuale*.

Le organizzazioni che non applicano il controllo di accesso ai dati con funzionalità come Controllo degli accessi in base al ruolo potrebbero concedere più privilegi del necessario ai propri utenti. Questo può comportare la compromissione dei dati perché ad alcuni utenti potrebbe essere concesso un accesso ai dati di cui non dovrebbero disporre.

Per altre informazioni sui ruoli di Azure, leggere l'articolo [Controllo degli accessi in base al ruolo di Azure](./active-directory/role-based-access-control-configure.md).

## Crittografare le macchine virtuali di Azure
Per molte organizzazioni, [la crittografia dei dati inattivi](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) è un passaggio obbligatorio per assicurare la privacy dei dati, la conformità e la sovranità dei dati. Crittografia dischi di Azure consente agli amministratori IT di crittografare i dischi delle macchine virtuali IaaS Windows e Linux. Crittografia dischi di Azure si basa sulla funzionalità BitLocker standard di settore disponibile in Windows e sulla funzionalità DM-Crypt di Linux per offrire la crittografia del volume per i dischi dei dati e del sistema operativo.

Crittografia dischi di Azure contribuisce alla protezione dei dati e al rispetto degli impegni dell'organizzazione a livello di sicurezza e conformità. La crittografia permette anche alle organizzazioni di ridurre i rischi correlati all'accesso non autorizzato ai dati. È anche consigliabile crittografare le unità prima di scrivervi dati sensibili.

Assicurarsi di crittografare il volume di avvio e i volumi dei dati della macchina virtuale per proteggere i dati inattivi nell'account di archiviazione di Azure. Proteggere le chiavi di crittografia e i segreti sfruttando l'[insieme di credenziali delle chiavi di Azure](./key-vault/key-vault-whatis.md).

Per le istanze di Windows Server locali, prendere in considerazione le seguenti procedure consigliate per la crittografia:

- Usare [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) per la crittografia dei dati
- Archiviare le informazioni di ripristino in Servizi di dominio Active Directory.
- Se si teme che le chiavi di BitLocker siano state compromesse, è consigliabile formattare l'unità per rimuovere tutte le istanze dei metadati di BitLocker dall'unità oppure decrittografare e crittografare nuovamente l'intera unità.

Le organizzazioni che non applicano la crittografia dei dati hanno maggiori probabilità di esposizione a problemi di integrità dei dati, ad esempio il furto di dati da parte di utenti non autorizzati o malintenzionati e l'accesso non autorizzato a dati non crittografati con account compromessi. Oltre a questi rischi, le aziende che sono tenute a rispettare normative di settore devono dimostrare di operare in modo conforme e di implementare i controlli di sicurezza appropriati per aumentare la sicurezza dei dati.

Per altre informazioni su Crittografia dischi di Azure, leggere l'articolo [Crittografia dischi di Azure per le macchine virtuali IaaS Windows e Linux](azure-security-disk-encryption.md).

## Usare i moduli di protezione hardware

Le soluzioni di crittografia di settore usano le chiavi private per crittografare i dati. È quindi fondamentale che queste chiavi vengano archiviate in modo sicuro. La gestione delle chiavi diventa parte integrante della protezione dei dati perché verrà impiegata per archiviare le chiavi private usate per crittografare i dati.

Crittografia dischi di Azure usa l'[insieme di credenziali delle chiavi di Azure](https://azure.microsoft.com/services/key-vault/) per facilitare il controllo e la gestione delle chiavi di crittografia dei dischi e dei segreti nella sottoscrizione dell'insieme di credenziali delle chiavi, assicurando al tempo stesso che tutti i dati nei dischi delle macchine virtuali siano crittografati quando inattivi nell'archiviazione di Azure. È opportuno usare l'insieme di credenziali delle chiavi di Azure per controllare le chiavi e l'utilizzo di criteri.

Esistono molti rischi correlati alla mancanza di controlli di sicurezza appropriati per proteggere le chiavi private usate per crittografare i dati. Se utenti malintenzionati hanno accesso alle chiavi private possono decrittografare i dati e potrebbero accedere a informazioni riservate.

Per saperne di più sulle raccomandazioni generali per la gestione dei certificati in Azure, leggere l'articolo relativo alle [procedure consigliate e sconsigliate per la gestione dei certificati in Azure](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/).

Per altre informazioni sull'insieme di credenziali delle chiavi di Azure, leggere [Introduzione all'insieme di credenziali delle chiavi di Azure](./key-vault/key-vault-get-started.md).

## Gestire con workstation sicure

La maggior parte degli attacchi prende di mira l'utente finale, per cui l'endpoint diventa uno dei principali punti di attacco. Se un utente malintenzionato compromette l'endpoint, può usare le credenziali dell'utente per accedere ai dati dell'organizzazione. Nella maggior parte dei casi, gli attacchi agli endpoint sfruttano il fatto che gli utenti finali sono amministratori delle workstation locali.

È possibile ridurre questi rischi usando una workstation di gestione sicura. Si consiglia di usare una [workstation di accesso con privilegi (PAW)](https://technet.microsoft.com/library/mt634654.aspx) per ridurre la superficie di attacco nella workstation. Queste workstation di gestione sicure consentono di limitare alcuni di questi attacchi e contribuiscono a proteggere i dati. Assicurarsi di usare il sistema PAW per proteggere e bloccare la workstation. Si tratta di un passaggio importante per applicare garanzie di sicurezza di alto livello per la protezione di account, attività e dati sensibili.

L'assenza di protezione degli endpoint potrebbe mettere a rischio i dati, quindi è importante applicare criteri di sicurezza in tutti i dispositivi che consentono di utilizzare i dati, indipendentemente dal fatto che i dati si trovino nel cloud o in locale.

Per altre informazioni sulle workstation di accesso con privilegi, leggere l'articolo sulla [protezione dell'accesso con privilegi](https://technet.microsoft.com/library/mt631194.aspx).

## Abilitare la crittografia dei dati SQL

La funzionalità [Transparent Data Encryption (TDE) del database SQL di Azure](https://msdn.microsoft.com/library/dn948096.aspx) consente di proteggersi da attività dannose eseguendo in tempo reale la crittografia e la decrittografia dei database, dei backup associati e dei file di log delle transazioni inattivi, senza dover apportare modifiche all'applicazione. TDE esegue la crittografia dell'archiviazione di un intero database usando una chiave simmetrica detta "chiave di crittografia del database".

Anche quando l'intera risorsa di archiviazione è crittografata, è molto importante crittografare anche il database stesso. Si tratta di un'implementazione della strategia di difesa approfondita per la protezione dati. Se si usa il [database SQL di Azure](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) e si vuole proteggere dati riservati, ad esempio il numero della carta di credito o il codice fiscale, è possibile crittografare i database con la crittografia AES a 256 bit con convalida FIPS 140-2, che soddisfa i requisiti di molti standard di settore come HIPAA e PCI.

È importante comprendere che i file correlati all'[estensione del pool di buffer](https://msdn.microsoft.com/library/dn133176.aspx) non vengono crittografati quando un database è crittografato con TDE. È necessario usare gli strumenti di crittografia a livello di file system come BitLocker o [Encrypting File System](https://technet.microsoft.com/library/cc700811.aspx) (EFS) per i file correlati all'estensione del pool di buffer.

Dato che un utente autorizzato, ad esempio un amministratore della sicurezza o un amministratore di database, può accedere ai dati anche se il database è crittografato con TDE, è consigliabile seguire anche le raccomandazioni seguenti:

- Autenticazione di SQL a livello di database
- Autenticazione di Azure AD con i ruoli di Controllo degli accessi in base al ruolo
- Gli utenti e le applicazioni dovrebbero usare account separati per l'autenticazione. In questo modo è possibile limitare le autorizzazioni concesse a utenti e applicazioni e ridurre i rischi di attività dannose
- Implementare la sicurezza a livello di database usando i ruoli predefiniti del database, ad esempio db\_datareader o db\_datawriter, oppure creare ruoli personalizzati per l'applicazione per concedere autorizzazioni esplicite su determinati oggetti di database

Le organizzazioni che non usano la crittografia a livello di database potrebbero essere più esposte ad attacchi che potrebbero compromettere i dati presenti nel database SQL.

Per altre informazioni sulla crittografia TDE di SQL, leggere l'articolo [Transparent Data Encryption con il database SQL di Azure](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## Proteggere i dati in transito

La protezione dei dati in transito deve essere una parte essenziale della strategia di protezione dati. Visto che i dati transiteranno in modo bidirezionale tra molte posizioni, in generale si raccomanda di usare sempre i protocolli SSL/TLS per lo scambio di dati tra posizioni diverse. In alcuni casi è consigliabile isolare l'intero canale di comunicazione tra l'infrastruttura locale e cloud con una rete privata virtuale (VPN).

Per lo spostamento dei dati tra l'infrastruttura locale e Azure, è opportuno considerare le misure di protezione appropriate, ad esempio HTTPS o VPN.

Per le organizzazioni che hanno bisogno di proteggere l'accesso ad Azure da più workstation locali, usare una [VPN da sito a sito di Azure](./vpn-gateway/vpn-gateway-site-to-site-create.md).

Per le organizzazioni che hanno bisogno di proteggere l'accesso ad Azure da una workstation locale, usare una [VPN da punto a sito](./vpn-gateway/vpn-gateway-point-to-site-create.md).

Set di dati più grandi possono essere spostati su un collegamento WAN ad alta velocità dedicato, ad esempio [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Se si decide di usare ExpressRoute, è possibile anche crittografare i dati a livello di applicazione usando [SSL/TLS](https://support.microsoft.com/kb/257591) o altri protocolli per una maggiore protezione.

Se si interagisce con Archiviazione di Azure tramite il portale di Azure, tutte le transazioni hanno luogo tramite HTTPS. È possibile usare anche l'[API REST di archiviazione](https://msdn.microsoft.com/library/azure/dd179355.aspx) su HTTPS per interagire con [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) e il [database SQL di Azure](https://azure.microsoft.com/services/sql-database/).

Le organizzazioni che non riescono a proteggere i dati in transito sono più vulnerabili agli attacchi di tipo [man-in-the-middle](https://technet.microsoft.com/library/gg195821.aspx), [eavesdropping](https://technet.microsoft.com/library/gg195641.aspx) e hijack della sessione. Questi attacchi possono essere il primo passo per ottenere l'accesso ai dati riservati.

Per altre informazioni sull'opzione VPN di Azure, leggere l'articolo [Pianificazione e progettazione per il gateway VPN](./vpn-gateway/vpn-gateway-plan-design.md).

## Applicare la crittografia dei dati a livello di file

Un altro livello di protezione che può incrementare la sicurezza per i dati consiste nel crittografare il file stesso, indipendentemente da dove si trova.

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) usa criteri di crittografia, identità e autorizzazione per proteggere i file e la posta elettronica. Azure RMS opera su più dispositivi, tra cui telefoni, tablet e PC, applicando una protezione all'interno e all'esterno dell'organizzazione. Questa funzionalità è resa possibile dal fatto che Azure RMS aggiunge un livello di protezione che rimane insieme ai dati, anche quando fuoriescono dai confini dell'organizzazione.

Quando si usa Azure RMS per proteggere i file, si usa una crittografia standard di settore con pieno supporto di [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Quando si usa Azure RMS per proteggere i dati, la protezione rimane associata al file anche se questo viene copiato in una risorsa di archiviazione che non è sotto il controllo dell'IT, ad esempio un servizio di archiviazione cloud. Lo stesso accade per i file condivisi tramite posta elettronica: il file viene protetto come allegato di un messaggio di posta elettronica, con istruzioni su come aprire l'allegato protetto.

Quando si pianifica l'adozione di Azure RMS, si consiglia quanto segue:

- Installare l'[app RMS sharing](https://technet.microsoft.com/library/dn339006.aspx). Questa app si integra con le applicazioni Office installando un componente aggiuntivo di Office che offre agli utenti un modo semplice per proteggere direttamente i file.
- Configurare le applicazioni e i servizi per supportare Azure RMS
- Creare [modelli personalizzati](https://technet.microsoft.com/library/dn642472.aspx) che rispecchiano i requisiti aziendali, ad esempio un modello per i dati riservati da applicare in tutti i messaggi di posta elettronica correlati a informazioni riservate.

Le organizzazioni che sono carenti a livello di [classificazione dei dati](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) e protezione dei dati potrebbero essere più soggette alla perdita di dati. In assenza di un'adeguata protezione per i file, le organizzazioni non riusciranno a ottenere informazioni di business dettagliate, monitorare i possibili abusi e impedire l'accesso non autorizzato ai file.

Per altre informazioni su Azure RMS, leggere l'articolo [Introduzione a Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).

<!---HONumber=AcomDC_0525_2016-->