---
title: Come eseguire il servizio di gestione dei certificati di OPC Vault in modo sicuro - Azure Documenti Microsoft
description: Viene descritto come eseguire il servizio di gestione dei certificati di OPC Vault in modo sicuro in Azure ed esamina altre linee guida sulla sicurezza da considerare.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 88f8188779c5fb6b3cd07c67e9f35a6b8f9ad97d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271707"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>Eseguire il servizio di gestione dei certificati OPC Vault in modo sicuro

Questo articolo illustra come eseguire il servizio di gestione dei certificati di OPC Vault in modo sicuro in Azure ed esamina altre linee guida sulla sicurezza da considerare.

## <a name="roles"></a>Ruoli

### <a name="trusted-and-authorized-roles"></a>Ruoli attendibili e autorizzati

Il microservizio OPC Vault consente ai ruoli distinti di accedere a varie parti del servizio.

> [!IMPORTANT]
> Durante la distribuzione, lo script aggiunge solo l'utente che esegue lo script di distribuzione come utente per tutti i ruoli. Per una distribuzione di produzione, è consigliabile esaminare questa assegnazione di ruolo e riconfigurarla in modo appropriato seguendo le linee guida riportate di seguito. Questa attività richiede l'assegnazione manuale di ruoli e servizi nel portale applicazioni aziendali di Azure Active Directory (Azure AD).

### <a name="certificate-management-service-roles"></a>Ruoli del servizio di gestione dei certificati

Il microservizio OPC Vault definisce i ruoli seguenti:

- **Lettore:** per impostazione predefinita, qualsiasi utente autenticato nel tenant dispone dell'accesso in lettura. 
  - Accesso in lettura alle applicazioni e alle richieste di certificati. Può elencare ed eseguire query per le applicazioni e le richieste di certificato. Anche le informazioni sull'individuazione dei dispositivi e i certificati pubblici sono accessibili con accesso in lettura.
- **Writer**: Il ruolo Writer viene assegnato a un utente per aggiungere autorizzazioni di scrittura per determinate attività. 
  - Accesso in lettura/scrittura alle applicazioni e alle richieste di certificati. Può registrare, aggiornare e annullare la registrazione delle applicazioni. Può creare richieste di certificato e ottenere chiavi private e certificati approvati. È inoltre possibile eliminare le chiavi private.
- **Responsabile approvazione:** il ruolo Responsabile approvazione viene assegnato a un utente per approvare o rifiutare le richieste di certificato. Il ruolo non include altri ruoli.
  - Oltre al ruolo Approvatore per accedere all'API di microservizio di OPC Vault, l'utente deve disporre anche dell'autorizzazione di firma della chiave in Archiviazione delle chiavi di Azure per poter firmare i certificati.
  - Il ruolo Writer e Approvatore deve essere assegnato a utenti diversi.
  - Il ruolo principale dell'approvatore è l'approvazione della generazione e il rifiuto delle richieste di certificato.
- **Amministratore:** il ruolo Amministratore viene assegnato a un utente per gestire i gruppi di certificati. Il ruolo non supporta il ruolo Approvatore, ma include il ruolo Writer.
  - L'amministratore può gestire i gruppi di certificati, modificare la configurazione e revocare i certificati dell'applicazione inviando un nuovo elenco di revoche di certificati (CRL).
  - Idealmente, i ruoli Writer, Approvatore e Amministratore vengono assegnati a utenti diversi. Per una maggiore sicurezza, un utente con il ruolo Di approvazione o Amministratore necessita anche dell'autorizzazione per la firma delle chiavi nell'insieme di credenziali delle chiavi, per emettere certificati o per rinnovare un certificato CA dell'autorità di certificazione dell'autorità emittente.
  - Oltre al ruolo di amministrazione microservizio, il ruolo include, ma non è limitato a:In addition to the microservice administration role, the role includes, but is't limited to:
    - Responsabilità dell'amministrazione dell'implementazione delle procedure di protezione della CA.
    - Gestione della generazione, revoca e sospensione dei certificati. 
    - Gestione del ciclo di vita della chiave crittografica (ad esempio, il rinnovo delle chiavi CA dell'autorità emittente).
    - Installazione, configurazione e manutenzione dei servizi che gestiscono la CA.
    - Funzionamento quotidiano dei servizi. 
    - Backup e ripristino di CA e database.

### <a name="other-role-assignments"></a>Altre assegnazioni di ruolo

Considerare inoltre i ruoli seguenti quando si esegue il servizio:

- Proprietario dell'azienda del contratto di approvvigionamento dei certificati con l'autorità di certificazione radice esterna (ad esempio, quando il proprietario acquista certificati da una CA esterna o gestisce una CA subordinata a una CA esterna).
- Sviluppo e convalida dell'autorità di certificazione.
- Revisione dei record di controllo.
- Il personale che supporta la CA o gestisce le strutture fisiche e cloud, ma non è direttamente attendibile per eseguire le operazioni CA, è nel ruolo *autorizzato.* Deve essere documentato anche l'insieme dei compiti che le persone che svolgono nel ruolo autorizzato.

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>Esaminare le appartenenze ai ruoli attendibili e autorizzati trimestralmente

Esaminare l'appartenenza ai ruoli attendibili e autorizzati almeno trimestralmente. Assicurarsi che il set di persone (per i processi manuali) o le identità del servizio (per i processi automatizzati) in ogni ruolo sia ridotto al minimo.

### <a name="role-separation-between-certificate-requester-and-approver"></a>Separazione dei ruoli tra il richiedente del certificato e l'approvatore

Il processo di rilascio del certificato deve applicare la separazione dei ruoli tra il richiedente del certificato e i ruoli dell'approvatore del certificato (persone o sistemi automatizzati). Il rilascio del certificato deve essere autorizzato da un ruolo di approvatore del certificato che verifica che il richiedente del certificato sia autorizzato a ottenere i certificati. Le persone che detengono il ruolo di approvatore del certificato devono essere una persona formalmente autorizzata.

### <a name="restrict-assignment-of-privileged-roles"></a>Limitare l'assegnazione di ruoli con privilegi

È consigliabile limitare l'assegnazione di ruoli con privilegi, ad esempio l'autorizzazione dell'appartenenza al gruppo Administrators e Responsabili approvazione, a un set limitato di personale autorizzato. Tutte le modifiche al ruolo con privilegi devono avere accesso revocato entro 24 ore. Infine, esaminare le assegnazioni di ruolo con privilegi su base trimestrale e rimuovere eventuali assegnazioni non necessarie o scadute.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>I ruoli con privilegi devono utilizzare l'autenticazione a due fattori

Utilizzare l'autenticazione a più fattori (denominata anche autenticazione a due fattori) per gli accessi interattivi di responsabili approvazione e amministratori al servizio.

## <a name="certificate-service-operation-guidelines"></a>Linee guida per l'operazione del servizio certificati

### <a name="operational-contacts"></a>Contatti operativi

Il servizio certificati deve avere un piano di risposta alla sicurezza aggiornato in archivio, che contiene i contatti di risposta agli incidenti operativi dettagliati.

### <a name="security-updates"></a>Aggiornamenti per la sicurezza

Tutti i sistemi devono essere continuamente monitorati e aggiornati con gli ultimi aggiornamenti di sicurezza.

> [!IMPORTANT]
> Il repository GitHub del servizio OPC Vault viene continuamente aggiornato con le patch di sicurezza. Monitorare questi aggiornamenti e applicarli al servizio a intervalli regolari.

### <a name="security-monitoring"></a>Monitoraggio della protezione

Sottoscrivere o implementare un monitoraggio della sicurezza appropriato. Ad esempio, sottoscrivere una soluzione di monitoraggio centrale (ad esempio Centro sicurezza di Azure o soluzione di monitoraggio di Office 365) e configurarla in modo appropriato per garantire che gli eventi di sicurezza vengano trasmessi alla soluzione di monitoraggio.

> [!IMPORTANT]
> Per impostazione predefinita, il servizio OPC Vault viene distribuito con [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/devops) come soluzione di monitoraggio. L'aggiunta di una soluzione di sicurezza come [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) è altamente consigliata.

### <a name="assess-the-security-of-open-source-software-components"></a>Valutare la sicurezza dei componenti software open source

Tutti i componenti open source utilizzati all'interno di un prodotto o servizio devono essere privi di vulnerabilità di sicurezza moderate o superiori.

> [!IMPORTANT]
> Durante le build di integrazione continua, il repository GitHub del servizio OPC Vault analizza tutte le vulnerabilità. Monitorare questi aggiornamenti su GitHub e applicarli al servizio a intervalli regolari.

### <a name="maintain-an-inventory"></a>Gestire un inventario

Gestire un inventario degli asset per tutti gli host di produzione (incluse le macchine virtuali persistenti), i dispositivi, tutti gli intervalli di indirizzi IP interni, gli indirizzi VIP e i nomi di dominio DNS pubblici. Ogni volta che si aggiunge o rimuove un sistema, un indirizzo IP del dispositivo, un indirizzo VIP o un dominio DNS pubblico, è necessario aggiornare l'inventario entro 30 giorni.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Inventario della distribuzione di produzione di microservizi di Azure OPC Vault predefinita 

In Azure:
- **Piano di servizio app:** piano di servizio dell'app per gli host del servizio. Predefinito S1.
- **Servizio app** per microservizio: l'host del servizio OPC Vault.
- **Servizio app** per l'applicazione di esempio: host dell'applicazione di esempio OPC Vault.App Service for sample application: The OPC Vault sample application host.
- **Key Vault Standard**: Per archiviare i segreti e le chiavi di Azure Cosmos DB per i servizi Web.Key Vault Standard : To store secrets and Azure Cosmos DB keys for the web services.
- **Key Vault Premium**: Per ospitare le chiavi della CA dell'autorità di certificazione dell'autorità emittente, per il servizio di firma e per la configurazione dell'insieme di credenziali e l'archiviazione delle chiavi private dell'applicazione.
- **Azure Cosmos DB:** database per le richieste di applicazioni e certificati. 
- **Application Insights:** soluzione di monitoraggio (facoltativo) per il servizio Web e l'applicazione.
- **Registrazione dell'applicazione Azure AD:** registrazione per l'applicazione di esempio, il servizio e il modulo perimetrale.

Per i servizi cloud, devono essere documentati tutti i nomi host, i gruppi di risorse, i nomi delle risorse, gli ID sottoscrizione e gli ID tenant usati per distribuire il servizio. 

In Azure IoT Edge o in un server perimetrale IoT locale:In Azure IoT Edge or a local IoT Edge server:
- **Modulo OPC Vault IoT Edge**: Per supportare una rete di fabbrica OPC UA Global Discovery Server. 

Per i dispositivi IoT Edge, i nomi host e gli indirizzi IP devono essere documentati. 

### <a name="document-the-certification-authorities-cas"></a>Documentare le Autorità di certificazione (CA)

La documentazione della gerarchia della CA deve contenere tutte le CA gestite. Sono incluse tutte le CA subordinate correlate, le CA padre e le CA radice, anche quando non sono gestite dal servizio. Anziché la documentazione formale, è possibile fornire un set completo di tutti i certificati CA non scaduti.

> [!NOTE]
> L'applicazione di esempio OPC Vault supporta il download di tutti i certificati utilizzati e prodotti nel servizio per la documentazione.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>Documentare i certificati emessi da tutte le Autorità di certificazione (CA)

Fornire un set completo di tutti i certificati emessi negli ultimi 12 mesi.

> [!NOTE]
> L'applicazione di esempio OPC Vault supporta il download di tutti i certificati utilizzati e prodotti nel servizio per la documentazione.

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>Documentare la procedura operativa standard per eliminare in modo sicuro le chiavi crittografiche

Durante la durata di una CA, l'eliminazione delle chiavi può verificarsi solo raramente. Questo è il motivo per cui a nessun utente è assegnato il diritto di eliminazione del certificato dell'insieme di credenziali delle chiavi e per questo non sono esposte API per eliminare un certificato CA dell'autorità di certificazione dell'autorità di certificazione dell'autorità emittente. La procedura operativa standard manuale per l'eliminazione sicura delle chiavi crittografiche dell'autorità di certificazione è disponibile solo accedendo direttamente all'insieme di credenziali delle chiavi nel portale di Azure.The manual standard operating procedure for securely deleting certification authority cryptographic keys is only available by directly accessing Key Vault in the Azure portal. È inoltre possibile eliminare il gruppo di certificati nell'insieme di credenziali delle chiavi. Per garantire l'eliminazione immediata, disattivare la funzionalità di [eliminazione temporanea dell'insieme di](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) credenziali delle chiavi.

## <a name="certificates"></a>Certificati

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>I certificati devono essere conformi al profilo certificato minimo

Il servizio OPC Vault è una CA online che rilascia i certificati di entità finale agli abbonati. Il microservizio OPC Vault segue queste linee guida nell'implementazione predefinita.

- Tutti i certificati devono includere i seguenti campi X.509, come specificato di seguito:
  - Il contenuto del campo versione deve essere v3. 
  - Il contenuto del campo serialNumber deve includere almeno 8 byte di entropia ottenuti da un generatore di numeri casuali approvato DA 140 FIPS (Federal Information Processing Standards).<br>
    > [!IMPORTANT]
    > Il numero di serie di OPC Vault è per impostazione predefinita di 20 byte e viene ottenuto dal generatore di numeri casuali crittografici del sistema operativo. Il generatore di numeri casuali è FIPS 140 approvato su dispositivi Windows, ma non su Linux. Considerare questo aspetto quando si sceglie una distribuzione del servizio che utilizza macchine virtuali Linux o contenitori docker Linux, in cui la tecnologia sottostante OpenSSL non è approvata FIPS 140.
  - I campi issuerUniqueID e subjectUniqueID non devono essere presenti.
  - I certificati di entità finale devono essere identificati con l'estensione dei vincoli di base, in conformità con IETF RFC 5280.
  - Il campo pathLenConstraint deve essere impostato su 0 per il certificato della CA di emissione. 
  - L'estensione Utilizzo chiave esteso deve essere presente e deve contenere il set minimo di oID (Extended Key Usage Object Identifier). Non è necessario specificare l'OID anyExtendedKeyUsage (2.5.29.37.0). 
  - L'estensione del punto di distribuzione CRL (CDP) deve essere presente nel certificato della CA dell'autorità emittente.<br>
    > [!IMPORTANT]
    > L'estensione CDP è presente nei certificati Ca del Vault OPC. Tuttavia, i dispositivi OPC UA utilizzano metodi personalizzati per distribuire i CRL.
  - L'estensione Accesso alle informazioni sull'autorità deve essere presente nei certificati del sottoscrittore.<br>
    > [!IMPORTANT]
    > L'estensione Authority Information Access è presente nei certificati degli abbonati OPC Vault. Tuttavia, i dispositivi OPC UA utilizzano metodi personalizzati per distribuire le informazioni della CA dell'autorità emittente.
- È necessario utilizzare algoritmi asimmetrici approvati, lunghezze di chiave, funzioni hash e modalità di riempimento.
  - RSA e SHA-2 sono gli unici algoritmi supportati.
  - RSA può essere utilizzato per la crittografia, lo scambio di chiavi e la firma.
  - La crittografia RSA deve utilizzare solo le modalità di riempimento OAEP, RSA-KEM o RSA-PSS.
  - Le lunghezze delle chiavi maggiori o uguali a 2048 bit sono obbligatorie.
  - Utilizzare la famiglia di algoritmi hash SHA-2 (SHA256, SHA384 e SHA512).
  - Le chiavi CA radice RSA con una durata tipica maggiore o uguale a 20 anni devono essere 4096 bit o superiore.
  - Le chiavi DELLA CA dell'autorità di certificazione rsA Issuer devono essere di almeno 2048 bit. Se la data di scadenza del certificato CA è successiva al 2030, la chiave CA deve essere a 4096 bit o superiore.
- Durata del certificato
  - Certificati CA radice: il periodo di validità massimo del certificato per le CA radice non deve superare i 25 anni.
  - Certificati CA secondari o Ca dell'autorità di certificazione emittente online: il periodo di validità massima dei certificati per le CA online che sono online ed emettono solo i certificati del sottoscrittore non deve superare i 6 anni. Per queste CA, la chiave di firma privata correlata non deve essere utilizzata per più di 3 anni per emettere nuovi certificati.<br>
    > [!IMPORTANT]
    > Il certificato dell'autorità emittente, così come viene generato nel microservizio OPC Vault predefinito senza CA radice esterna, viene considerato come una Sub CA online, con i rispettivi requisiti e durate. La durata predefinita è impostata su 5 anni, con una lunghezza della chiave maggiore o uguale a 2048.The default lifetime is set to 5 years, with a key length greater than or equal to 2048.
  - Tutte le chiavi asimmetriche devono avere una durata massima di 5 anni e una durata consigliata di 1 anno.<br>
    > [!IMPORTANT]
    > Per impostazione predefinita, la durata dei certificati di applicazione emessi con OPC Vault ha una durata di 2 anni e deve essere sostituita ogni anno. 
  - Ogni volta che un certificato viene rinnovato, viene rinnovato con una nuova chiave.
- Estensioni specifiche dell'UA OPC nei certificati dell'istanza dell'applicazioneOPC UA-specific extensions in application instance certificates
  - L'estensione subjectAltName include l'URI e i nomi host dell'applicazione. Questi possono includere anche gli indirizzi FQDN, IPv4 e IPv6.
  - KeyUsage include digitalSignature, non Repudiation, keyEncipherment e dataEncipherment.
  - ExtendedKeyUsage include serverAuth e clientAuth.
  - AuthorityKeyIdentifier è specificato nei certificati firmati.

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>Le chiavi CA e i certificati devono soddisfare i requisiti minimi

- **Chiavi private**: Le chiavi RSA devono essere di almeno 2048 bit. Se la data di scadenza del certificato CA è successiva al 2030, la chiave CA deve essere a 4096 bit o superiore.
- **Durata**: il periodo di validità massima del certificato per le CA online che emettono solo i certificati del sottoscrittore non deve superare i 6 anni. Per queste CA, la chiave di firma privata correlata non deve essere utilizzata per più di 3 anni per emettere nuovi certificati.

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>Le chiavi CA sono protette tramite moduli di sicurezza hardware

OpcVault usa La chiave di Azure Vault Premium e le chiavi sono protette da FIPS 140-2 Level 2 Hardware Security Modules (HSM). 

I moduli di crittografia utilizzati da Key Vault, sia HSM che software, sono convalidati FIPS. Le chiavi create o importate come protette da HSM vengono elaborate all'interno di un HSM, convalidate in FIPS 140-2 Livello 2. Le chiavi create o importate come software-protected vengono elaborate all'interno di moduli crittografici convalidati in FIPS 140-2 Livello 1.

## <a name="operational-practices"></a>Pratiche operative

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Documentare e gestire le procedure PKI operative standard per la registrazione dei certificati

Documentare e gestire procedure operative standard (SOP) per il modo in cui le CA emettono i certificati, tra cui: 
- Modalità di autenticazione e autenticazione del sottoscrittore. 
- Modalità di elaborazione e convalida della richiesta di certificato (se applicabile, include anche la modalità di elaborazione del rinnovo del certificato e delle richieste di rekey). 
- Modalità di distribuzione dei certificati rilasciati ai sottoscrittori. 

Il microservizio OPC Vault SOP è descritto [nell'architettura OPC Vault](overview-opc-vault-architecture.md) e gestire [il servizio certificati OPC Vault](howto-opc-vault-manage.md). Le procedure seguono "OPC Unified Architecture Specification Part 12: Discovery and Global Services".


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Documentare e gestire le procedure PKI operative standard per la revoca dei certificati

Il processo di revoca dei certificati è descritto [nell'architettura di OPC Vault](overview-opc-vault-architecture.md) e nella [gestione del servizio certificati OPC Vault](howto-opc-vault-manage.md).
    
### <a name="document-ca-key-generation-ceremony"></a>Cerimonia di generazione dei tasti CA dei documenti 

La generazione della chiave della CA dell'autorità di certificazione dell'autorità di certificazione dell'autorità emittente nel microservizio oPC Vault è semplificata, grazie all'archiviazione sicura in Azure Key Vault.The Issuer CA key generation in the OPC Vault microservice is simplified, due of the secure storage in Azure Key Vault. Per ulteriori informazioni, vedere [Gestire il servizio certificati OPC Vault](howto-opc-vault-manage.md).

Tuttavia, quando si utilizza un'autorità di certificazione root esterna, una cerimonia di generazione della chiave CA deve rispettare i requisiti seguenti.

La cerimonia di generazione dei tasti CA deve essere eseguita su uno script documentato che include almeno i seguenti elementi: 
- Definizione dei ruoli e delle responsabilità dei partecipanti.
- Approvazione per lo svolgimento della cerimonia di generazione dei tasti CA.
- Hardware crittografico e materiali di attivazione necessari per la cerimonia.
- Preparazione dell'hardware (inclusi l'aggiornamento e l'autorizzazione delle informazioni di configurazione).
- Installazione del sistema operativo.
- Passaggi specifici eseguiti durante la cerimonia di generazione dei tasti CA, ad esempio: 
  - Installazione e configurazione dell'applicazione CA.
  - Generazione di chiavi CA.
  - Backup della chiave CA.
  - Firma del certificato CA.
  - Importazione delle chiavi firmate nell'HSM protetto del servizio.
  - Arresto del sistema CA.
  - Preparazione dei materiali per lo stoccaggio.


## <a name="next-steps"></a>Passaggi successivi

Ora che hai imparato a gestire in modo sicuro OPC Vault, puoi:

> [!div class="nextstepaction"]
> [Proteggere i dispositivi OPC UA con OPC Vault](howto-opc-vault-secure.md)
