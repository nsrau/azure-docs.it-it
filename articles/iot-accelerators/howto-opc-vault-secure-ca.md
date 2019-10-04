---
title: Come eseguire il servizio di gestione certificati dell'insieme di credenziali OPC in modo sicuro-Azure | Microsoft Docs
description: Viene descritto come eseguire il servizio di gestione certificati dell'insieme di credenziali OPC in modo sicuro in Azure e vengono esaminate altre linee guida sulla sicurezza da prendere in considerazione.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 88f8188779c5fb6b3cd07c67e9f35a6b8f9ad97d
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200090"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>Eseguire il servizio di gestione certificati dell'insieme di credenziali OPC in modo sicuro

Questo articolo illustra come eseguire il servizio di gestione certificati dell'insieme di credenziali OPC in modo sicuro in Azure ed esamina le altre linee guida per la sicurezza da prendere in considerazione.

## <a name="roles"></a>Ruoli

### <a name="trusted-and-authorized-roles"></a>Ruoli attendibili e autorizzati

Il microservizio dell'insieme di credenziali OPC consente ai ruoli distinti di accedere a varie parti del servizio.

> [!IMPORTANT]
> Durante la distribuzione, lo script aggiunge solo l'utente che esegue lo script di distribuzione come utente per tutti i ruoli. Per una distribuzione di produzione, è necessario esaminare questa assegnazione di ruolo e riconfigurare in modo appropriato seguendo le linee guida riportate di seguito. Questa attività richiede l'assegnazione manuale dei ruoli e dei servizi nel portale delle applicazioni aziendali Azure Active Directory (Azure AD).

### <a name="certificate-management-service-roles"></a>Ruoli del servizio Gestione certificati

Il microservizio dell'insieme di credenziali OPC definisce i ruoli seguenti:

- **Lettore**: Per impostazione predefinita, qualsiasi utente autenticato nel tenant ha accesso in lettura. 
  - Accesso in lettura alle applicazioni e alle richieste di certificati. Consente di elencare ed eseguire query per le applicazioni e le richieste di certificati. Inoltre, le informazioni di individuazione dei dispositivi e i certificati pubblici sono accessibili con accesso in lettura.
- **Writer**: Il ruolo writer viene assegnato a un utente per aggiungere le autorizzazioni di scrittura per determinate attività. 
  - Accesso in lettura/scrittura alle applicazioni e alle richieste di certificati. Consente di registrare, aggiornare e annullare la registrazione delle applicazioni. Può creare richieste di certificati e ottenere chiavi private e certificati approvati. Consente inoltre di eliminare le chiavi private.
- **Responsabile approvazione**: Il ruolo responsabile approvazione viene assegnato a un utente per approvare o rifiutare le richieste di certificati. Il ruolo non include altri ruoli.
  - Oltre al ruolo responsabile approvazione per accedere all'API del microservizio dell'insieme di credenziali OPC, l'utente deve avere anche l'autorizzazione per la firma della chiave in Azure Key Vault per poter firmare i certificati.
  - Il ruolo writer e responsabile approvazione deve essere assegnato a utenti diversi.
  - Il ruolo principale del responsabile approvazione è l'approvazione della generazione e del rifiuto delle richieste di certificati.
- **Amministratore**: Il ruolo di amministratore viene assegnato a un utente per gestire i gruppi di certificati. Il ruolo non supporta il ruolo responsabile approvazione, ma include il ruolo writer.
  - L'amministratore può gestire i gruppi di certificati, modificare la configurazione e revocare i certificati dell'applicazione emettendo un nuovo elenco di revoche di certificati (CRL).
  - Idealmente, i ruoli writer, responsabile approvazione e amministratore vengono assegnati a utenti diversi. Per una maggiore sicurezza, un utente con il ruolo di responsabile approvazione o amministratore deve avere anche l'autorizzazione per la firma della chiave in Key Vault, per emettere certificati o per rinnovare un certificato CA dell'emittente.
  - Oltre al ruolo di amministrazione del microservizio, il ruolo include, ma non è limitato a:
    - Responsabilità dell'amministrazione dell'implementazione delle procedure di sicurezza della CA.
    - Gestione della generazione, della revoca e della sospensione dei certificati. 
    - Gestione del ciclo di vita delle chiavi crittografiche (ad esempio, il rinnovo delle chiavi CA dell'autorità emittente).
    - Installazione, configurazione e manutenzione dei servizi che funzionano con la CA.
    - Funzionamento giornaliero dei servizi. 
    - Backup e ripristino del database e della CA.

### <a name="other-role-assignments"></a>Altre assegnazioni di ruolo

Quando si esegue il servizio, prendere in considerazione anche i ruoli seguenti:

- Proprietario aziendale del contratto di approvvigionamento dei certificati con l'autorità di certificazione radice esterna, ad esempio quando il proprietario acquista i certificati da una CA esterna o esegue una CA subordinata a una CA esterna.
- Sviluppo e convalida dell'autorità di certificazione.
- Revisione dei record di controllo.
- Il personale che aiuta a supportare la CA o a gestire le strutture fisiche e cloud, ma non è direttamente attendibile per eseguire operazioni CA, si trova nel ruolo *autorizzato* . È anche possibile documentare il set di attività che il ruolo autorizzato deve eseguire.

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>Verifica l'appartenenza di ruoli attendibili e autorizzati trimestralmente

Verificare l'appartenenza dei ruoli trusted e Authorized almeno ogni trimestre. Verificare che il set di persone (per i processi manuali) o le identità del servizio (per i processi automatizzati) in ogni ruolo venga mantenuto al minimo.

### <a name="role-separation-between-certificate-requester-and-approver"></a>Separazione dei ruoli tra il richiedente del certificato e il responsabile approvazione

Il processo di rilascio del certificato deve applicare la separazione dei ruoli tra il richiedente del certificato e i ruoli del responsabile approvazione del certificato (persone o sistemi automatizzati). Il rilascio del certificato deve essere autorizzato da un ruolo responsabile approvazione certificati che verifica che il richiedente del certificato sia autorizzato a ottenere i certificati. Le persone che detengono il ruolo di responsabile approvazione del certificato devono essere una persona formalmente autorizzata.

### <a name="restrict-assignment-of-privileged-roles"></a>Limitare l'assegnazione di ruoli con privilegi

È consigliabile limitare l'assegnazione di ruoli con privilegi, ad esempio l'autorizzazione dell'appartenenza del gruppo Administrators e responsabili approvazione, a un set limitato di personale autorizzato. Le modifiche ai ruoli con privilegi devono avere accesso revocato entro 24 ore. Infine, esaminare le assegnazioni di ruolo con privilegi su base trimestrale e rimuovere eventuali assegnazioni non necessarie o scadute.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>I ruoli con privilegi devono usare l'autenticazione a due fattori

Usare la funzionalità di autenticazione a più fattori (detta anche autenticazione a due fattori) per gli accessi interattivi dei responsabili approvazione e degli amministratori al servizio.

## <a name="certificate-service-operation-guidelines"></a>Linee guida per le operazioni del servizio certificati

### <a name="operational-contacts"></a>Contatti operativi

Il servizio certificati deve disporre di un piano di risposta di sicurezza aggiornato sul file, che contiene contatti dettagliati di risposta agli eventi imprevisti.

### <a name="security-updates"></a>Aggiornamenti della sicurezza

Tutti i sistemi devono essere monitorati e aggiornati continuamente con gli aggiornamenti della sicurezza più recenti.

> [!IMPORTANT]
> Il repository GitHub del servizio dell'insieme di credenziali OPC viene costantemente aggiornato con le patch di sicurezza. Monitorare questi aggiornamenti e applicarli al servizio a intervalli regolari.

### <a name="security-monitoring"></a>Monitoraggio della protezione

Sottoscrivere o implementare il monitoraggio della sicurezza appropriato. Ad esempio, sottoscrivere una soluzione di monitoraggio centrale, ad esempio il Centro sicurezza di Azure o la soluzione di monitoraggio Office 365, e configurarla in modo appropriato per assicurarsi che gli eventi di sicurezza vengano trasmessi alla soluzione di monitoraggio.

> [!IMPORTANT]
> Per impostazione predefinita, il servizio dell'insieme di credenziali OPC viene distribuito con [applicazione Azure Insights](https://docs.microsoft.com/azure/azure-monitor/app/devops) come soluzione di monitoraggio. Si consiglia vivamente di aggiungere una soluzione di sicurezza come il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) .

### <a name="assess-the-security-of-open-source-software-components"></a>Valutazione della sicurezza dei componenti software open source

Tutti i componenti open source utilizzati all'interno di un prodotto o di un servizio devono essere esenti da vulnerabilità di sicurezza moderata o superiore.

> [!IMPORTANT]
> Durante le compilazioni di integrazione continua, il repository GitHub del servizio dell'insieme di credenziali OPC analizza tutti i componenti per individuare eventuali vulnerabilità. Monitora questi aggiornamenti su GitHub e applicali al servizio a intervalli regolari.

### <a name="maintain-an-inventory"></a>Gestire un inventario

Mantenere un inventario degli asset per tutti gli host di produzione (incluse le macchine virtuali persistenti), i dispositivi, tutti gli intervalli di indirizzi IP interni, i VIP e i nomi di dominio DNS pubblici. Ogni volta che si aggiunge o si rimuove un sistema, un indirizzo IP del dispositivo, un indirizzo VIP o un dominio DNS pubblico, è necessario aggiornare l'inventario entro 30 giorni.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Inventario della distribuzione di produzione del microservizio dell'insieme di credenziali di Azure OPC predefinito 

In Azure:
- **App Service Plan** (Piano di servizio app): Piano di servizio app per gli host del servizio. S1 predefinito.
- **Servizio app** per microservizio: Host del servizio dell'insieme di credenziali OPC.
- **Servizio app** per l'applicazione di esempio: L'host dell'applicazione di esempio dell'insieme di credenziali OPC.
- **Key Vault standard**: Per archiviare i segreti e le chiavi di Azure Cosmos DB per i servizi Web.
- **Key Vault Premium**: Per ospitare le chiavi CA dell'autorità emittente, per il servizio di firma e per la configurazione dell'insieme di credenziali e l'archiviazione delle chiavi private dell'applicazione.
- **Azure Cosmos DB**: Database per richieste di certificati e applicazioni. 
- **Application Insights**: (facoltativo) soluzione di monitoraggio per l'applicazione e il servizio Web.
- **Azure ad registrazione dell'applicazione**: Una registrazione per l'applicazione di esempio, il servizio e il modulo perimetrale.

Per i servizi cloud, è necessario documentare tutti i nomi host, i gruppi di risorse, i nomi delle risorse, gli ID sottoscrizione e gli ID tenant usati per distribuire il servizio. 

In Azure IoT Edge o in un server di IoT Edge locale:
- **Modulo di IOT Edge**dell'insieme di credenziali OPC: Per supportare un server di individuazione globale della rete aziendale OPC UA. 

Per i dispositivi IoT Edge, è necessario documentare i nomi host e gli indirizzi IP. 

### <a name="document-the-certification-authorities-cas"></a>Documentare le autorità di certificazione (CAs)

La documentazione della gerarchia CA deve contenere tutte le CA gestite. Sono incluse tutte le autorità di certificazione subordinate correlate, CA padre e CA radice, anche quando non sono gestite dal servizio. Anziché la documentazione formale, è possibile fornire un set completo di tutti i certificati CA non scaduti.

> [!NOTE]
> L'applicazione di esempio dell'insieme di credenziali OPC supporta il download di tutti i certificati usati e prodotti nel servizio per la documentazione.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>Documentare i certificati emessi da tutte le autorità di certificazione (CAs)

Fornire un set completo di tutti i certificati rilasciati negli ultimi 12 mesi.

> [!NOTE]
> L'applicazione di esempio dell'insieme di credenziali OPC supporta il download di tutti i certificati usati e prodotti nel servizio per la documentazione.

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>Documentare la procedura operativa standard per eliminare in modo sicuro le chiavi crittografiche

Durante la durata di una CA, l'eliminazione della chiave potrebbe verificarsi solo raramente. Questo è il motivo per cui a nessun utente è stato assegnato il diritto di eliminazione del certificato Key Vault e perché non sono presenti API esposte per eliminare un certificato CA dell'emittente. La procedura manuale standard per l'eliminazione sicura delle chiavi crittografiche di autorità di certificazione è disponibile solo accedendo direttamente a Key Vault nel portale di Azure. È anche possibile eliminare il gruppo di certificati in Key Vault. Per garantire l'eliminazione immediata, disabilitare la Key Vault funzionalità di [eliminazione](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) temporanea.

## <a name="certificates"></a>Certificati

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>I certificati devono essere conformi al profilo certificato minimo

Il servizio dell'insieme di credenziali OPC è un'autorità di certificazione online che rilascia i certificati di entità di fine ai sottoscrittori. Il microservizio dell'insieme di credenziali OPC segue queste linee guida nell'implementazione predefinita.

- Tutti i certificati devono includere i seguenti campi X. 509, come specificato di seguito:
  - Il contenuto del campo versione deve essere V3. 
  - Il contenuto del campo serialNumber deve includere almeno 8 byte di entropia ottenuti da un generatore di numeri casuali FIPS (Federal Information Processing Standards) 140 approvato.<br>
    > [!IMPORTANT]
    > Il numero di serie dell'insieme di credenziali OPC è per impostazione predefinita 20 byte e viene ottenuto dal generatore di numeri casuali crittografici del sistema operativo. Il generatore di numeri casuali è approvato da FIPS 140 nei dispositivi Windows, ma non in Linux. Tenere presente questo problema quando si sceglie una distribuzione del servizio che usa VM Linux o contenitori Docker Linux, in cui la tecnologia sottostante OpenSSL non è approvato da FIPS 140.
  - I campi issuerUniqueID e subjectUniqueID non devono essere presenti.
  - I certificati dell'entità finale devono essere identificati con l'estensione dei vincoli di base, in base a IETF RFC 5280.
  - Il campo pathLenConstraint deve essere impostato su 0 per il certificato della CA emittente. 
  - È necessario che l'estensione utilizzo chiavi avanzato sia presente e che contenga il set minimo di identificatori di oggetti di utilizzo chiavi estesi (OID). Non è necessario specificare l'OID anyExtendedKeyUsage (2.5.29.37.0). 
  - L'estensione del punto di distribuzione CRL (CDP) deve essere presente nel certificato CA dell'emittente.<br>
    > [!IMPORTANT]
    > L'estensione CDP è presente nei certificati della CA dell'insieme di credenziali OPC. Tuttavia, i dispositivi OPC UA utilizzano metodi personalizzati per distribuire i CRL.
  - L'estensione di accesso alle informazioni sull'autorità deve essere presente nei certificati del Sottoscrittore.<br>
    > [!IMPORTANT]
    > L'estensione di accesso alle informazioni sull'autorità è presente nei certificati del sottoscrittore dell'insieme di credenziali OPC Tuttavia, i dispositivi OPC UA usano metodi personalizzati per distribuire le informazioni sulle autorità di certificazione.
- È necessario utilizzare algoritmi asimmetrici approvati, lunghezze delle chiavi, funzioni hash e modalità di riempimento.
  - RSA e SHA-2 sono gli unici algoritmi supportati.
  - RSA può essere usato per la crittografia, lo scambio di chiave e la firma.
  - La crittografia RSA deve usare solo le modalità di riempimento OAEP, RSA-KEM o RSA-PSS.
  - Sono necessarie lunghezze di chiave maggiori o uguali a 2048 bit.
  - Usare la famiglia SHA-2 di algoritmi hash (SHA256, SHA384 e SHA512).
  - Le chiavi CA radice RSA con una durata tipica maggiore o uguale a 20 anni devono avere una lunghezza di 4096 bit o superiore.
  - Le chiavi CA dell'autorità emittente RSA devono essere di almeno 2048 bit. Se la data di scadenza del certificato della CA è successiva alla 2030, la chiave della CA deve essere 4096 bit o superiore.
- Durata del certificato
  - Certificati CA radice: Il periodo di validità massimo del certificato per le autorità di certificazione radice non deve superare i 25 anni.
  - CA Sub o certificati CA autorità emittente online: Il periodo di validità massimo del certificato per le CA online ed emette solo i certificati del Sottoscrittore non deve superare i 6 anni. Per queste autorità di certificazione, la chiave di firma privata correlata non deve essere usata più di 3 anni per emettere nuovi certificati.<br>
    > [!IMPORTANT]
    > Il certificato dell'autorità emittente, così come viene generato nel microservizio dell'insieme di credenziali OPC predefinito senza CA radice esterna, viene considerato come una CA in linea, con i rispettivi requisiti e durate. La durata predefinita è impostata su 5 anni, con una lunghezza della chiave maggiore o uguale a 2048.
  - Tutte le chiavi asimmetriche devono avere una durata massima di 5 anni e una durata consigliata di 1 anno.<br>
    > [!IMPORTANT]
    > Per impostazione predefinita, la durata dei certificati dell'applicazione rilasciata con l'insieme di credenziali OPC ha una durata di 2 anni e deve essere sostituita ogni anno. 
  - Ogni volta che un certificato viene rinnovato, viene rinnovato con una nuova chiave.
- Estensioni specifiche di OPC UA nei certificati dell'istanza dell'applicazione
  - L'estensione subjectAltName include l'URI dell'applicazione e i nomi host. Questi possono includere anche indirizzi FQDN, IPv4 e IPv6.
  - L'utilizzo dei dati include digitalSignature, nonripudio, keyEncipherment e dataEncipherment.
  - Il extendedKeyUsage include serverAuth e clientAuth.
  - AuthorityKeyIdentifier è specificato nei certificati firmati.

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>Le chiavi e i certificati della CA devono soddisfare i requisiti minimi

- **Chiavi private**: Le chiavi RSA devono essere di almeno 2048 bit. Se la data di scadenza del certificato della CA è successiva alla 2030, la chiave della CA deve essere 4096 bit o superiore.
- **Durata**: Il periodo di validità massimo del certificato per le CA online ed emette solo i certificati del Sottoscrittore non deve superare i 6 anni. Per queste autorità di certificazione, la chiave di firma privata correlata non deve essere usata più di 3 anni per emettere nuovi certificati.

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>Le chiavi CA sono protette mediante moduli di protezione hardware

OpcVault USA Azure Key Vault Premium e le chiavi sono protette da moduli di protezione hardware FIPS 140-2 Level 2. 

I moduli crittografici utilizzati da Key Vault, sia che si tratti di un modulo di protezione hardware o software, vengono convalidati FIPS. Le chiavi create o importate come protette da HSM vengono elaborate all'interno di un modulo di protezione hardware, convalidate in FIPS 140-2 Level 2. Le chiavi create o importate come protette da software vengono elaborate all'interno di moduli crittografici convalidati per FIPS 140-2 livello 1.

## <a name="operational-practices"></a>Procedure operative

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Documentare e gestire le procedure PKI operative standard per la registrazione dei certificati

Documentare e gestire le procedure operative standard (SOP) per il rilascio dei certificati da CAs, tra cui: 
- Modalità di identificazione e autenticazione del Sottoscrittore. 
- Il modo in cui la richiesta di certificato viene elaborata e convalidata (se applicabile, include anche la modalità di elaborazione delle richieste di rinnovo del certificato e rekey). 
- Modalità di distribuzione dei certificati emessi ai sottoscrittori. 

Il SOP del microservizio dell'insieme di credenziali OPC è descritto nell'architettura dell'insieme di credenziali [OPC](overview-opc-vault-architecture.md) e [gestire il servizio certificati dell'](howto-opc-vault-manage.md)insieme di credenziali OPC. Le procedure seguono "OPC Unified Architecture Specification part 12: Individuazione e servizi globali ".


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Documentare e gestire le procedure PKI operative standard per la revoca dei certificati

Il processo di revoca dei certificati è descritto nell'architettura dell'insieme di credenziali [OPC](overview-opc-vault-architecture.md) e [gestisce il servizio certificati dell'](howto-opc-vault-manage.md)insieme di credenziali OPC.
    
### <a name="document-ca-key-generation-ceremony"></a>Cerimonia di generazione della chiave CA del documento 

La generazione della chiave dell'autorità di certificazione nel microservizio dell'insieme di credenziali OPC è stata semplificata, a causa dell'archiviazione protetta in Azure Key Vault. Per altre informazioni, vedere [Manage the OPC Vault Certificate Service](howto-opc-vault-manage.md).

Tuttavia, quando si usa un'autorità di certificazione radice esterna, una cerimonia di generazione delle chiavi CA deve rispettare i requisiti seguenti.

La cerimonia di generazione delle chiavi dell'autorità di certificazione deve essere eseguita su uno script documentato che include almeno gli elementi seguenti: 
- Definizione di ruoli e responsabilità del partecipante.
- Approvazione per il comportamento della cerimonia di generazione delle chiavi dell'autorità di certificazione.
- Hardware crittografico e materiali di attivazione necessari per la cerimonia.
- Preparazione hardware (incluso l'aggiornamento delle informazioni di asset/configurazione e l'approvazione).
- Installazione del sistema operativo.
- Passaggi specifici eseguiti durante la cerimonia di generazione delle chiavi dell'autorità di certificazione, ad esempio: 
  - Installazione e configurazione dell'applicazione CA.
  - Generazione della chiave CA.
  - Backup della chiave della CA.
  - Firma del certificato CA.
  - Importazione di chiavi firmate nel modulo di protezione hardware protetto del servizio.
  - Arresto del sistema CA.
  - Preparazione dei materiali per l'archiviazione.


## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire in modo sicuro l'insieme di credenziali OPC, è possibile:

> [!div class="nextstepaction"]
> [Proteggere i dispositivi OPC UA con l'insieme di credenziali OPC](howto-opc-vault-secure.md)
