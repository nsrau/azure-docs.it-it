---
title: Come eseguire il servizio di gestione certificati dell'insieme di credenziali OPC in modo sicuro-Azure | Microsoft Docs
description: Viene descritto come eseguire il servizio di gestione certificati dell'insieme di credenziali OPC in modo sicuro in Azure e altre linee guida di sicurezza da prendere in considerazione.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f35836f60fae11c0955c128e96a4cea188681942
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997670"
---
# <a name="how-to-run-the-opc-vault-certificate-management-service-securely"></a>Come eseguire il servizio di gestione certificati dell'insieme di credenziali OPC in modo sicuro

Questo articolo illustra come eseguire il servizio di gestione certificati dell'insieme di credenziali OPC in modo sicuro in Azure e altre linee guida per la sicurezza da prendere in considerazione.

## <a name="roles"></a>Ruoli

### <a name="trusted-and-authorized-roles"></a>Ruoli attendibili e autorizzati

Il microservizio dell'insieme di credenziali OPC è configurato in modo da consentire ai ruoli distinti di accedere a diverse parti del servizio.

> [!IMPORTANT]
> Durante la distribuzione, lo script aggiunge solo l'utente che esegue lo script di distribuzione come utente per tutti i ruoli.
> Questa assegnazione di ruolo deve essere esaminata per una distribuzione di produzione e riconfigurata in modo appropriato seguendo le linee guida riportate di seguito.
> Questa attività richiede l'assegnazione manuale dei ruoli e dei servizi nel portale applicazioni aziendali Azure AD.

### <a name="certificate-management-service-roles"></a>Ruoli del servizio Gestione certificati

Il microservizio dell'insieme di credenziali OPC definisce i ruoli seguenti:

- **Lettore**: Per impostazione predefinita, qualsiasi utente autenticato nel tenant ha accesso in lettura. 
  - Accesso in lettura alle applicazioni e alle richieste di certificati. Consente di elencare ed eseguire query per le applicazioni e le richieste di certificati. Inoltre, le informazioni di individuazione dei dispositivi e i certificati pubblici sono accessibili con accesso in lettura.
- **Writer**: Il ruolo writer viene assegnato a un utente per aggiungere le autorizzazioni di scrittura per determinate attività. 
  - Accesso in lettura/scrittura alle applicazioni e alle richieste di certificati. Consente di registrare, aggiornare e annullare la registrazione delle applicazioni. Può creare richieste di certificati e ottenere chiavi private e certificati approvati. Consente inoltre di eliminare le chiavi private.
- **Responsabile approvazione**: Il ruolo responsabile approvazione viene assegnato a un utente per approvare o rifiutare le richieste di certificati. Il ruolo non include altri ruoli.
  - Oltre al ruolo responsabile approvazione per accedere all'API del microservizio dell'insieme di credenziali OPC, l'utente deve avere anche l'autorizzazione per la firma della chiave in Key Vault per poter firmare i certificati.
  - Il ruolo writer e responsabile approvazione deve essere assegnato a utenti diversi.
  - Il ruolo principale del responsabile approvazione è l'approvazione della generazione e del rifiuto delle richieste di certificati.
- **Amministratore**: Il ruolo di amministratore viene assegnato a un utente per gestire i gruppi di certificati. Il ruolo non supporta il ruolo responsabile approvazione, ma include il ruolo writer.
  - L'amministratore può gestire i gruppi di certificati, modificare la configurazione e revocare i certificati dell'applicazione emettendo un nuovo CRL.
  - Idealmente, i ruoli writer, responsabile approvazione e amministratore vengono assegnati a utenti diversi. Per una maggiore sicurezza, un utente con il ruolo di responsabile approvazione o amministratore necessita anche dell'autorizzazione per la firma delle chiavi nell'insieme di credenziali delle chiavi per emettere certificati o per rinnovare un certificato CA dell'emittente.
  - Oltre al ruolo di amministrazione del microservizio, il ruolo include anche, ma non è limitato a:
    - Responsabile dell'amministrazione dell'implementazione delle procedure di sicurezza della CA.
    - Gestione della generazione, della revoca e della sospensione dei certificati. 
    - Gestione del ciclo di vita delle chiavi crittografiche (ad esempio, il rinnovo delle chiavi CA dell'autorità emittente).
    - Installazione, configurazione e manutenzione dei servizi che funzionano con la CA.
    - Funzionamento giornaliero dei servizi. 
    - Backup e ripristino del database e della CA.

### <a name="other-role-assignments"></a>Altre assegnazioni di ruolo

Quando si esegue il servizio, devono essere considerati e assegnati anche i ruoli seguenti:

- Proprietario aziendale del contratto di approvvigionamento dei certificati con l'autorità di certificazione radice esterna, nel caso in cui il proprietario acquisti certificati da una CA esterna o operi una CA subordinata a una CA esterna.
- Sviluppo e convalida dell'autorità di certificazione.
- Revisione dei record di controllo.
- Il personale che aiuta a supportare la CA o a gestire le strutture fisiche e cloud, ma non è direttamente attendibile per eseguire operazioni della CA, viene definito come nel ruolo autorizzato. È anche possibile documentare il set di attività che il ruolo autorizzato deve eseguire.

### <a name="memberships-of-trusted-and-authorized-roles-must-be-reviewed-annually"></a>Le appartenenze di ruoli attendibili e autorizzati devono essere riviste annualmente

L'appartenenza ai ruoli attendibili e autorizzati deve essere esaminata almeno ogni trimestre per garantire che il set di persone (per i processi manuali) o le identità del servizio (per i processi automatizzati) in ogni ruolo venga mantenuto al minimo.

### <a name="certificate-issuance-process-must-enforce-role-separation-between-certificate-requester-and-approver"></a>Il processo di rilascio del certificato deve applicare la separazione dei ruoli tra il richiedente e il responsabile approvazione

Il processo di rilascio del certificato deve applicare la separazione dei ruoli tra il richiedente del certificato e i ruoli del responsabile approvazione del certificato (persone o sistemi automatici). Il rilascio del certificato deve essere autorizzato da un ruolo responsabile approvazione certificati che verifica che il richiedente del certificato sia autorizzato a ottenere i certificati. Le persone che detengono il ruolo di responsabile approvazione del certificato devono essere una persona formalmente autorizzata.

### <a name="privileged-role-management-must-restrict-access-and-be-reviewed-quarterly"></a>La gestione dei ruoli con privilegi deve limitare l'accesso ed essere rivista trimestralmente

L'assegnazione di ruoli con privilegi, ad esempio l'autorizzazione dell'appartenenza del gruppo Administrators e responsabili approvazione, deve essere limitata a un set limitato di personale autorizzato. Le modifiche ai ruoli con privilegi devono avere accesso revocato entro 24 ore. Infine, le assegnazioni di ruolo con privilegi devono essere esaminate su base trimestrale ed è necessario rimuovere le assegnazioni non necessarie o scadute.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>I ruoli con privilegi devono usare l'autenticazione a due fattori

Multi-factor authentication (autenticazione a due fattori, autenticazione a più fattori o TFA) deve essere usato per gli accessi interattivi di responsabili approvazione e amministratori al servizio.

## <a name="certificate-service-operation-guidelines"></a>Linee guida per le operazioni del servizio certificati

### <a name="operational-contacts"></a>Contatti operativi

Il servizio certificati deve disporre di un piano di risposta di sicurezza aggiornato sul file, che contiene contatti dettagliati di risposta agli eventi imprevisti.

### <a name="security-updates"></a>Aggiornamenti della sicurezza

Tutti i sistemi devono essere monitorati e aggiornati in modo continuo con gli aggiornamenti della sicurezza più recenti/conformità della patch.

> [!IMPORTANT]
> Il repository GitHub del servizio dell'insieme di credenziali OPC viene costantemente aggiornato con le patch di sicurezza. Gli aggiornamenti su GitHub devono essere monitorati e gli aggiornamenti vengono applicati al servizio a intervalli regolari.

### <a name="security-monitoring"></a>Monitoraggio della protezione

Sottoscrivere o implementare il monitoraggio della sicurezza appropriato, ad esempio sottoscrivendo una soluzione di monitoraggio centrale, ad esempio il Centro sicurezza di Azure, la soluzione di monitoraggio O365, e configurarla in modo appropriato per assicurarsi che gli eventi di sicurezza vengano trasmessi al soluzione di monitoraggio.

> [!IMPORTANT]
> Per impostazione predefinita, il servizio dell'insieme di credenziali OPC viene distribuito con il [applicazione Azure Insights](https://docs.microsoft.com/azure/azure-monitor/app/devops) come soluzione di monitoraggio. Si consiglia vivamente di aggiungere una soluzione di sicurezza come il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) .

### <a name="assess-security-of-open-source-software-components"></a>Valutazione della sicurezza dei componenti software open source

Tutti i componenti open source utilizzati all'interno di un prodotto o di un servizio devono essere esenti da vulnerabilità di sicurezza moderata o superiore.

> [!IMPORTANT]
> Il repository GitHub del servizio dell'insieme di credenziali OPC sta analizzando tutti i componenti durante le compilazioni di integrazione continua per le vulnerabilità. Gli aggiornamenti su GitHub devono essere monitorati e gli aggiornamenti vengono applicati al servizio a intervalli regolari.

### <a name="maintain-an-inventory"></a>Gestire un inventario

È necessario mantenere un inventario degli asset per tutti gli host di produzione (incluse le macchine virtuali persistenti), i dispositivi, tutti gli intervalli di indirizzi IP interni, i VIP e i nomi di dominio DNS pubblici. Questo inventario deve essere aggiornato con l'aggiunta o la rimozione di un sistema, di un indirizzo IP del dispositivo, di un indirizzo VIP o di un dominio DNS pubblico entro 30 giorni.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Inventario della distribuzione di produzione del microservizio dell'insieme di credenziali di Azure OPC predefinito: 

In **Azure**:
- **App Service Plan** (Piano di servizio app): Piano di servizio app per gli host del servizio. S1 predefinito.
- **Servizio app** per microservizio: Host del servizio dell'insieme di credenziali OPC.
- **Servizio app** per l'applicazione di esempio: L'host dell'applicazione di esempio dell'insieme di credenziali OPC.
- **Standard Vault**: Per archiviare i segreti e le chiavi di Cosmos DB per i servizi Web.
- **Archivio delle credenziali Premium**: Per ospitare le chiavi CA dell'autorità emittente, per il servizio di firma, per la configurazione dell'insieme di credenziali e l'archiviazione delle chiavi private dell'applicazione.
- **Cosmos DB**: Database per richieste di certificati e applicazioni. 
- **Application Insights**: (facoltativo) soluzione di monitoraggio per l'applicazione e il servizio Web.
- **Registrazione dell'applicazione AzureAD**: Una registrazione per l'applicazione di esempio, il servizio e il modulo perimetrale.

Per i servizi cloud tutti i nomi host, i gruppi di risorse, i nomi delle risorse, l'ID sottoscrizione, TenantId usati per distribuire il servizio devono essere documentati. 

In **IOT Edge** o in un **server di IOT Edge**locale:
- **Modulo di IOT Edge**dell'insieme di credenziali OPC: Per supportare un server di individuazione globale della rete aziendale OPC UA. 

Per i dispositivi IoT Edge è necessario documentare i nomi host e gli indirizzi IP. 

### <a name="document-the-certification-authorities-cas"></a>Documentare le autorità di certificazione (CAs)

La documentazione della gerarchia CA deve contenere tutte le CA gestite, incluse tutte le autorità di certificazione subordinate correlate, CA padre e CA radice, anche quando non sono gestite dal servizio. È possibile fornire un set completo di tutti i certificati CA non scaduti anziché la documentazione formale.

> [!NOTE]
> L'applicazione di esempio dell'insieme di credenziali OPC supporta il download di tutti i certificati usati e prodotti nel servizio per la documentazione.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>Documentare i certificati emessi da tutte le autorità di certificazione (CAs)

Per la documentazione è necessario fornire un set completo di tutti i certificati emessi negli ultimi 12 mesi.

> [!NOTE]
> L'applicazione di esempio dell'insieme di credenziali OPC supporta il download di tutti i certificati usati e prodotti nel servizio per la documentazione.

### <a name="document-the-sop-for-securely-deleting-cryptographic-keys"></a>Documentare il SOP per l'eliminazione sicura delle chiavi crittografiche

L'eliminazione della chiave può essere eseguita solo raramente durante il ciclo di vita di una CA. questo è il motivo per cui a nessun utente è stato assegnato il diritto Delete dell'insieme di credenziali delle chiavi e perché non sono presenti API esposte per eliminare un certificato CA dell'emittente La procedura manuale standard per l'eliminazione sicura delle chiavi crittografiche di autorità di certificazione è disponibile solo accedendo direttamente all'insieme di credenziali delle chiavi nel portale di Azure ed eliminando il gruppo di certificati nell'insieme di credenziali delle chiavi. Per assicurarsi che l'eliminazione temporanea dell'insieme di credenziali per [l'eliminazione temporanea](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) venga disabilitata.

## <a name="certificates"></a>Certificati

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>I certificati devono essere conformi al profilo certificato minimo

Il servizio dell'insieme di credenziali OPC è un'autorità di certificazione (CAs) online che rilascia i certificati di entità di fine ai sottoscrittori.
Il microservizio dell'insieme di credenziali OPC segue queste linee guida nell'implementazione predefinita.

- Tutti i certificati devono includere i seguenti campi X. 509 come specificato di seguito:
  - Il contenuto del campo versione deve essere V3. 
  - Il contenuto del campo serialNumber deve includere almeno 8 byte di entropia ottenuti da un generatore di numeri casuali FIPS (Federal Information Processing Standards) 140 approvato.<br>
    > [!IMPORTANT]
    > Il numero di serie dell'insieme di credenziali OPC è per impostazione predefinita 20 byte e viene ottenuto dal generatore di numeri casuali di crittografia del sistema operativo. Il generatore di numeri casuali è approvato da FIPS 140 nei dispositivi Windows, ma non nelle versioni Linux. Questo fatto deve essere preso in considerazione quando si sceglie una distribuzione del servizio, che usa VM Linux o contenitori Docker Linux, in cui la tecnologia OpenSSL sottostante non è stata approvata da FIPS 140.
  - I campi issuerUniqueID e subjectUniqueID non devono essere presenti.
  - I certificati dell'entità finale devono essere identificati con l'estensione dei vincoli di base in conformità a IETF RFC 5280.
  - Il campo pathLenConstraint deve essere impostato su 0 per il certificato della CA emittente. 
  - L'estensione utilizzo chiavi avanzato deve essere presente e contenere il set minimo di identificatori di oggetti di utilizzo chiave (OID). Non è necessario specificare l'OID anyExtendedKeyUsage (2.5.29.37.0). 
  - L'estensione del punto di distribuzione CRL (CDP) deve essere presente nel certificato CA dell'emittente.<br>
    > [!IMPORTANT]
    > L'estensione del punto di distribuzione CRL (CDP) è presente nei certificati della CA dell'insieme di credenziali OPC, tuttavia i dispositivi OPC UA usano metodi personalizzati per distribuire i CRL.
  - L'estensione di accesso alle informazioni sull'autorità deve essere presente nei certificati del Sottoscrittore.<br>
    > [!IMPORTANT]
    > L'estensione di accesso alle informazioni sull'autorità è presente nei certificati del sottoscrittore dell'insieme di credenziali OPC, tuttavia i dispositivi OPC UA usano metodi personalizzati per distribuire le informazioni della CA dell'autorità
- È necessario utilizzare algoritmi asimmetrici approvati, lunghezze delle chiavi, funzioni hash e modalità di riempimento.
  - **RSA** e **SHA-2** sono gli unici algoritmi supportati (*).
  - RSA può essere usato per la crittografia, lo scambio di chiave e la firma.
  - La crittografia RSA deve usare solo le modalità di riempimento OAEP, RSA-KEM o RSA-PSS.
  - Sono necessarie lunghezze di chiave > = 2048 bit.
  - Usare la famiglia SHA-2 di algoritmi hash (SHA256, SHA384 e SHA512).
  - Le chiavi CA radice RSA con una durata tipica > = 20 anni devono avere una lunghezza di 4096 bit o superiore.
  - Le chiavi CA dell'autorità emittente RSA devono essere di almeno 2048 bit; Se la data di scadenza del certificato della CA è successiva alla 2030, la chiave della CA deve essere 4096 bit o superiore.
- Durata del certificato
  - Certificati CA radice: Il periodo di validità massimo del certificato per le autorità di certificazione radice non deve superare i 25 anni.
  - CA Sub o certificati CA autorità emittente online: Il periodo di validità massimo del certificato per le CA online ed emette solo i certificati del Sottoscrittore non deve superare i sei anni. Per queste autorità di certificazione, la chiave di firma privata correlata non deve essere utilizzata per più di tre anni per emettere nuovi certificati.<br>
    > [!IMPORTANT]
    > Il certificato dell'autorità emittente così come viene generato nel microservizio dell'insieme di credenziali OPC predefinito senza CA radice esterna viene considerato come una CA in linea con i rispettivi requisiti e durate. La durata predefinita viene impostata su cinque anni con una lunghezza della chiave > = 2048.
  - Tutte le chiavi asimmetriche devono avere una durata massima di cinque anni, una durata consigliata di un anno.<br>
    > [!IMPORTANT]
    > Per impostazione predefinita, la durata dei certificati dell'applicazione rilasciata con l'insieme di credenziali OPC ha una durata di due anni e deve essere sostituita ogni anno. 
  - Ogni volta che un certificato viene rinnovato, viene rinnovato con una nuova chiave.
- Estensioni specifiche di OPC UA nei certificati dell'istanza dell'applicazione
  - L'estensione subjectAltName include l'URI dell'applicazione e i nomi host, che possono includere anche indirizzi FQDN, IPv4 e IPv6.
  - L'utilizzo dei dati include digitalSignature, nonripudio, keyEncipherment e dataEncipherment.
  - Il extendedKeyUsage include serverAuth e/o clientAuth.
  - AuthorityKeyIdentifier è specificato nei certificati firmati.

### <a name="certificate-authority-ca-keys-and-certificates-must-meet-minimum-requirements"></a>Le chiavi e i certificati dell'autorità di certificazione (CA) devono soddisfare i requisiti minimi

- **Chiavi private**: Le chiavi **RSA** devono essere di almeno 2048 bit; Se la data di scadenza del certificato della CA è successiva alla 2030, la chiave della CA deve essere 4096 bit o superiore.
- **Durata**: Il periodo di validità massimo del certificato per le CA online ed emette solo i certificati del Sottoscrittore non deve superare i sei anni. Per queste autorità di certificazione, la chiave di firma privata correlata non deve essere utilizzata per più di tre anni per emettere nuovi certificati.

### <a name="ca-keys-are-protected-using-hardware-security-modules-hsm"></a>Le chiavi CA sono protette mediante moduli di protezione hardware (HSM)

- OpcVault USA Azure Key Vault Premium e le chiavi sono protette da moduli di protezione hardware FIPS 140-2 Level 2. 

I moduli crittografici usati da Key Vault, moduli di protezione hardware o software, dispongono di convalida FIPS (Federal Information Processing Standards).<br>
Le chiavi create o importate come protette da HSM vengono elaborate all'interno di un modulo di protezione hardware, convalidate in FIPS 140-2 Level 2.<br>
Le chiavi create o importate come protette da software vengono elaborate all'interno di moduli crittografici convalidati per FIPS 140-2 livello 1.

## <a name="operational-practices"></a>Procedure operative

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Documentare e gestire le procedure PKI operative standard per la registrazione dei certificati

Documentare e gestire le procedure operative standard (SOP) per il rilascio dei certificati da CAs, tra cui: 
- Modalità di identificazione e autenticazione del Sottoscrittore 
- Il modo in cui la richiesta di certificato viene elaborata e convalidata (se applicabile, include anche la modalità di elaborazione delle richieste di rinnovo del certificato e rekey) 
- Modalità di distribuzione dei certificati emessi ai sottoscrittori 

Il SOP del microservizio dell'insieme di credenziali OPC è descritto nella [Panoramica](overview-opc-vault-architecture.md) e [come gestire](howto-opc-vault-manage.md) i documenti. Le procedure seguono le specifiche di architettura unificata OPC parte 12: Individuazione e servizi globali.


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Documentare e gestire le procedure PKI operative standard per la revoca dei certificati

Il processo di revoca dei certificati è descritto nella [Panoramica](overview-opc-vault-architecture.md) e [come gestire](howto-opc-vault-manage.md) i documenti.
    
### <a name="document-certification-authority-ca-key-generation-ceremony"></a>Cerimonia di generazione della chiave dell'autorità di certificazione del documento (CA) 

La generazione delle chiavi dell'autorità di certificazione nel microservizio dell'insieme di credenziali OPC è stata semplificata grazie all'archiviazione sicura nell'insieme di credenziali delle chiavi di Azure e descritta nella documentazione relativa [alla gestione](howto-opc-vault-manage.md) .

Tuttavia, quando viene utilizzata un'autorità di certificazione radice esterna, una cerimonia di generazione delle chiavi dell'autorità di certificazione (CA) deve soddisfare i requisiti seguenti:

La cerimonia di generazione delle chiavi dell'autorità di certificazione deve essere eseguita su uno script documentato che include almeno gli elementi seguenti: 
1. Definizione di ruoli e responsabilità dei partecipanti
2. Approvazione per il comportamento della cerimonia di generazione delle chiavi CA
3. Hardware crittografico e materiali di attivazione richiesti per la cerimonia
4. Preparazione hardware (incluso l'aggiornamento e la disconnessione delle informazioni su asset/configurazione)
5. Installazione del sistema operativo
6. Passaggi specifici eseguiti durante la cerimonia di generazione delle chiavi dell'autorità di certificazione, ad esempio: 
7. Installazione e configurazione dell'applicazione CA
8. Generazione di chiavi CA
9. Backup della chiave CA
10. Firma del certificato CA
9. Importazione di chiavi firmate nel modulo di protezione hardware protetto del servizio.
11. Arresto del sistema CA
12. Preparazione dei materiali per l'archiviazione


## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire in modo sicuro l'insieme di credenziali OPC, ecco il passaggio successivo suggerito:

> [!div class="nextstepaction"]
> [Proteggere i dispositivi OPC UA con l'insieme di credenziali OPC](howto-opc-vault-secure.md)