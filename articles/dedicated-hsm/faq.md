---
title: Domande frequenti - HSM dedicato di Azure | Microsoft Docs
description: Domande frequenti che riguardano diversi argomenti relativi a HSM dedicato di Azure
services: dedicated-hsm
author: johncdawson
manager: barbkess
tags: azure-resource-manager
ms.custom: mvc
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 5/8/2019
ms.author: barclayn
ms.openlocfilehash: b73b6bdc0158591565281ca2e86a9a474c4196d9
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467733"
---
# <a name="frequently-asked-questions-faq"></a>Domande frequenti

Risposte alle domande frequenti su HSM dedicato di Microsoft Azure.

## <a name="the-basics"></a>Nozioni di base

### <a name="q-what-is-a-hardware-security-module-hsm"></a>D: Che cos'è un modulo di protezione hardware (HSM)?

Un modulo di protezione hardware (HSM) è un dispositivo di elaborazione fisico usato per proteggere e gestire chiavi crittografiche. Le chiavi archiviate nei moduli di protezione hardware possono essere usate per operazioni di crittografia. Il materiale delle chiavi rimane protetto all'interno di moduli hardware antimanomissione. Il modulo di protezione hardware consente di usare le chiavi solo alle applicazioni autenticate e autorizzate. Il materiale della chiave non lascia mai i limiti di protezione del modulo di protezione hardware.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>D: Che cos'è l'offerta HSM dedicato di Azure?

HSM dedicato di Azure è un servizio basato su cloud che fornisce moduli di protezione hardware ospitati in data center di Azure connessi direttamente alla rete virtuale dei clienti. Questi moduli di protezione hardware sono appliance di rete dedicate (Gemalto SafeNet Network HSM 7 modello A790). Vengono distribuiti direttamente nello spazio di indirizzi IP privato del cliente e Microsoft non ha alcun accesso alla funzionalità di crittografia dei moduli di protezione hardware. Solo il cliente ha il controllo amministrativo e crittografico completo sui dispositivi. I clienti sono responsabili della gestione del dispositivo e possono ottenere log attività completi direttamente dai propri dispositivi. I moduli di protezione hardware dedicati consentono ai clienti di rispettare i requisiti di conformità e normativi, tra cui FIPS 140-2 Livello 3, HIPAA, PCI-DSS, eIDAS e molti altri.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>D: Che hardware viene usato per HSM dedicato?

Microsoft ha collaborato con Gemalto per fornire il servizio HSM dedicato di Azure. Lo specifico dispositivo usato è il [SafeNet Luna Network HSM 7 modello A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Questo dispositivo non solo fornisce firmware convalidato FIPS 140-2 Livello 3, ma offre anche bassa latenza, prestazioni elevate e capacità elevata tramite 10 partizioni. 

### <a name="q-what-is-an-hsm-used-for"></a>D: Per cosa viene usato un modulo di protezione hardware?

I moduli di protezione hardware servono per l'archiviazione delle chiavi crittografiche usate per la funzionalità di crittografia, tra cui SSL (Secure Socket Layer), crittografia di dati, infrastruttura a chiave pubblica (PKI), DRM (Digital Rights Management) e firma di documenti.

### <a name="q-how-does-dedicated-hsm-work"></a>D: Come funziona un modulo di protezione hardware dedicato?

I clienti possono effettuare il provisioning di moduli di protezione hardware in aree specifiche usando PowerShell o l'interfaccia della riga di comando. Il cliente specifica a quale rete virtuale verranno connessi i moduli di protezione hardware e, dopo il provisioning, questi saranno disponibili nella subnet dedicata in corrispondenza degli indirizzi IP assegnati nello spazio indirizzi IP privato del cliente. I clienti possono quindi connettersi ai moduli di protezione hardware usando lo strumento SSH per la gestione e l'amministrazione delle appliance, per configurare le connessioni client HSM, inizializzare moduli di protezione hardware, creare partizioni, definire e assegnare ruoli tra cui responsabile delle partizioni, responsabile della crittografia e utente della crittografia. Il cliente userà poi gli strumenti, l'SDK o il software del client HSM forniti da Gemalto per eseguire operazioni crittografiche dalle rispettive applicazioni.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>D: Quale software viene fornito con il servizio HSM dedicato?

Gemalto fornisce tutto il software per il dispositivo HSM dopo il provisioning da parte di Microsoft. Il software è disponibile presso il [portale di assistenza clienti di Gemalto](https://supportportal.gemalto.com/csm/). I clienti che usano il servizio HSM dedicato devono registrarsi per il supporto Gemalto e avere un ID cliente che consenta l'accesso e il download del software pertinente. Il software client supportato è la versione 7.2, compatibile con il firmware convalidato FIPS 140-2 Livello 3 versione 7.0.3. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>D: HSM dedicato di Azure offre l'autenticazione basata su password e basata su PED?

Attualmente il servizio HSM dedicato di Azure fornisce solo moduli di protezione hardware con autenticazione basata su password.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>D: Il servizio HSM dedicato di Azure ospiterà i moduli di protezione hardware degli utenti?

Microsoft offre solo il modulo di protezione hardware Gemalto SafeNet Luna Network mediante il servizio HSM dedicato e non può ospitare dispositivi forniti dai clienti.

### <a name="q-does-azure-dedicated-hsm-support-payment-pinetf-features"></a>D: Comporta il pagamento di supporto di hardware di Azure dedicato funzionalità (PIN/MET)?

Il servizio HSM dedicato di Azure usa dispositivi SafeNet Luna Network HSM 7 (modello A790). Questi dispositivi non supportano le certificazioni o le funzionalità specifiche di HSM per i pagamenti (come PIN o ETF). Se si desidera il servizio di hardware di Azure dedicate per il supporto in futuro il pagamento da moduli di protezione hardware, passare i commenti e suggerimenti al rappresentante Microsoft.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>D: Quali aree di Azure è disponibile nel modulo di protezione hardware dedicato?

A partire dalla fine 2019 marzo, modulo di protezione hardware dedicato è disponibile in 14 aree elencate di seguito. Altre aree sono pianificate e possono essere illustrate tramite il rappresentante dell'Account Microsoft.

* Stati Uniti orientali
* Stati Uniti orientali 2
* Stati Uniti occidentali
* Stati Uniti centro-meridionali
* Asia sud-orientale
* Asia orientale
* Europa settentrionale
* Europa occidentale
* Regno Unito meridionale
* Regno Unito occidentale
* Canada centrale
* Canada orientale
* Australia orientale
* Australia sud-orientale

## <a name="interoperability"></a>Interoperabilità

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>D: In che modo l'applicazione si connette a un modulo di protezione hardware dedicato?

Si usano gli strumenti, l'SDK o il software del client HSM forniti da Gemalto per eseguire operazioni crittografiche dalle proprie applicazioni. Il software è disponibile presso il [portale di assistenza clienti di Gemalto](https://supportportal.gemalto.com/csm/). I clienti che usano il servizio HSM dedicato devono registrarsi per il supporto Gemalto e avere un ID cliente che consenta l'accesso e il download del software pertinente.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>D: Un'applicazione può connettersi al servizio HSM dedicato da una rete virtuale diversa all'interno di una stessa area o tra aree diverse?

Sì, è necessario usare il [peering reti virtuali](../virtual-network/virtual-network-peering-overview.md) all'interno di un'area per stabilire la connettività tra reti virtuali. Per la connettività tra più aree, è necessario usare il servizio [Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>D: È possibile sincronizzare HSM dedicato con moduli di protezione hardware locali?

Sì, è possibile sincronizzare i moduli di protezione hardware locali con HSM dedicato. Per stabilire la connettività con la rete locale si può usare la connettività [VPN da punto a punto o da punto a sito](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>D: È possibile crittografare i dati usati da altri servizi di Azure con chiavi archiviate in HSM dedicato?

 No. I moduli di protezione hardware dedicati di Azure sono accessibili solo dall'interno della propria rete virtuale.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>D: È possibile importare chiavi da un modulo di protezione hardware locale esistente a HSM dedicato?

Sì, se si hanno moduli di protezione hardware Gemalto SafeNet locali. Esistono vari metodi. Vedere la documentazione del modulo di protezione hardware Gemalto.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>D: Quali sono i sistemi operativi supportati dal software client HSM dedicato?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Virtuali: VMware, hyperv, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>D: Come si configura l'applicazione client per creare una configurazione a disponibilità elevata con più partizioni da più moduli di protezione hardware?

Per ottenere la disponibilità elevata, è necessario impostare la configurazione dell'applicazione client HSM in modo da usare partizioni da ogni modulo di protezione hardware. Vedere la documentazione del software client HSM Gemalto.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>D: Quali meccanismi di autenticazione sono supportati dal servizio HSM dedicato?

HSM dedicato di Azure usa appliance SafeNet Network HSM 7 (modello A790), che supportano l'autenticazione basata su password.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>D: Quali SDK, API e software client sono disponibili per l'uso con HSM dedicato?

PKCS#11, Java (JCA/JCE), Microsoft CAPI e CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>D: È possibile importare/eseguire la migrazione di chiavi da moduli di protezione hardware Luna 5/6 a moduli di protezione hardware dedicati di Azure?

Sì. Consultare la guida alla migrazione di Gemalto. 

## <a name="using-your-hsm"></a>Uso del modulo di protezione hardware

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>D: Come si decide se usare Azure Key Vault o HSM dedicato di Azure?

HSM dedicato di Azure è la scelta più appropriata per le aziende che eseguono la migrazione ad applicazioni locali di Azure che usano moduli di protezione hardware. HSM dedicato offre un'opzione per eseguire la migrazione di un'applicazione con modifiche minime. Se le operazioni di crittografia vengono eseguite nel codice dell'applicazione in esecuzione in una macchina virtuale o un'app Web di Azure, possono usare HSM dedicato. In generale, può usare HSM dedicato il software con licenza a strappo eseguito in modelli IaaS (infrastruttura distribuita come servizio) che supportano i moduli di protezione hardware come archivio chiavi, ad esempio Gateway applicazione o Gestione traffico per SSL senza chiave, ADCS (Servizi certificati Active Directory) o strumenti PKI simili, strumenti/applicazioni usati per la firma di documenti o la firma del codice oppure SQL Server (IaaS) configurato con TDE (Transparent Data Encryption) con chiave master in un modulo di protezione hardware che usa un provider EKM (Extensible Key Management). Azure Key Vault è adatto per le applicazioni cloud native o per la crittografia di dati inattivi, in cui i dati del cliente vengano elaborati in scenari PaaS (piattaforma distribuita come servizio) o SaaS (software come un servizio), ad esempio Customer Key di Office 365, Azure Information Protection, Crittografia dischi di Azure, crittografia di Azure Data Lake Store con chiave gestita dal cliente, crittografia di Archiviazione di Azure con chiave gestita dal cliente e SQL di Azure con chiave gestita dal cliente.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>D: Quali sono gli scenari di utilizzo ottimali per HSM dedicato di Azure?

HSM dedicato di Azure è ideale per gli scenari di migrazione, vale a dire se si esegue la migrazione in Azure di applicazioni locali che già usano moduli di protezione hardware. Questo offre un'opzione a impatto ridotto per la migrazione in Azure con modifiche minime all'applicazione. Se le operazioni di crittografia vengono eseguite nel codice dell'applicazione in esecuzione in una macchina virtuale o un'app Web di Azure, è possibile usare HSM dedicato. In generale, può usare HSM dedicato il software con licenza a strappo eseguito in modelli IaaS (infrastruttura distribuita come servizio) che supportano i moduli di protezione hardware come archivio chiavi, ad esempio:

* Gateway applicazione o Gestione traffico per SSL senza chiave
* ADCS (Servizi certificati Active Directory)
* Strumenti PKI simili
* Strumenti/applicazioni usate per la firma di documenti
* Firma del codice
* SQL Server (IaaS) configurato con TDE (Transparent Data Encryption) con chiave master in un modulo di protezione hardware che usa un provider EKM (Extensible Key Management)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>D: Un modulo di protezione hardware dedicato può essere usato con Customer Key di Office 365, Azure Information Protection, Azure Data Lake Store, Crittografia dischi, crittografia di Archiviazione di Azure, Azure SQL TDE?

 No. Il provisioning di HSM dedicato viene effettuato direttamente nello spazio di indirizzi IP privato del cliente, dunque non è accessibile da altri servizi di Azure o Microsoft.

## <a name="administration-access-and-control"></a>Amministrazione, accesso e controllo

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>D: Il cliente ottiene controllo esclusivo completo sui moduli di protezione hardware con HSM dedicato?

Sì. Ogni appliance HSM è completamente dedicata a un singolo cliente e nessun altro ha il controllo amministrativo su di essa una volta effettuato il provisioning e cambiata la password dell'amministratore.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>D: Quale livello di accesso ha Microsoft sui moduli di protezione hardware degli utenti?

Microsoft non ha alcun controllo amministrativo o crittografico sui moduli di protezione hardware. Ha accesso a livello di monitoraggio attraverso una connessione alla porta seriale per recuperare dati di telemetria di base, ad esempio temperatura e integrità dei componenti. Questo consente a Microsoft di fornire la notifica tempestiva di eventuali problemi di integrità. Se necessario, il cliente può disabilitare questo account.

### <a name="q-what-is-the-tenantadmin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-safenet-hsms"></a>D: Che cos'è l'account "tenantadmin" Microsoft Usa, sono abituato all'utente amministratore in corso "admin" in moduli HSM SafeNet?

Il dispositivo HSM viene fornito con un utente predefinita dell'amministratore con la relativa password predefinito normale. Microsoft non desidera avere le password predefinite in uso, mentre qualsiasi dispositivo si trova in un pool in attesa di eseguire il provisioning da parte dei clienti. Ciò non soddisfano i requisiti di sicurezza restrittivi. Per questo motivo, abbiamo impostato una password complessa che viene eliminata in fase di provisioning. Inoltre, in fase di provisioning è creare un nuovo utente del ruolo di amministratore chiamato "tenantadmin". A questo utente è la password predefinita, i clienti di modificare il come la prima azione al primo accesso al dispositivo appena sottoposti a provisioning. Questo processo assicura livelli di protezione elevati e mantenga nostra aspirazione unico controllo amministrativo per i clienti. Si noti che l'utente "tenantadmin" consente di reimpostare la password dell'utente amministratore, se un cliente si preferisce usare tale account. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>D: Microsoft o chiunque in Microsoft può accedere alle chiavi in HSM dedicato?

 No. Microsoft non ha alcun accesso alle chiavi archiviate nei moduli di protezione hardware dedicati allocati ai clienti.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>D: I clienti possono eseguire l'aggiornamento del software/firmware nei moduli di protezione hardware ad essi allocati?

Per ottenere il supporto migliore, è consigliabile non aggiornare il software/firmware nel modulo di protezione hardware. Tuttavia, il cliente ha il controllo amministrativo completo, incluso l'aggiornamento del software/firmware se sono richieste funzionalità specifiche di versioni del firmware diverse. Prima di apportare modifiche occorre comprenderne le implicazioni, perché ad esempio potrebbero incidere sullo stato di convalida FIPS. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>D: Come si gestisce HSM dedicato?

È possibile gestire i moduli di protezione hardware dedicati accedendovi tramite SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>D: Come si gestiscono le partizioni in HSM dedicato?

Per gestire le partizioni e i moduli di protezione hardware si usa il software client HSM Gemalto.

### <a name="q-how-do-i-monitor-my-hsm"></a>D: Come si monitora il modulo di protezione hardware?

Un cliente ha accesso completo ai log attività del proprio modulo di protezione hardware tramite SNMP e syslog. Per ricevere i log o gli eventi dai moduli di protezione hardware dovrà configurare un server syslog o SNMP.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>D: È possibile ottenere log di accesso completi di tutte le operazioni del modulo di protezione hardware da HSM dedicato?

Sì. È possibile inviare i log dall'appliance HSM a un server syslog

## <a name="high-availability"></a>Disponibilità elevata

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>D: È possibile configurare la disponibilità elevata nella stessa area o in più aree?

Sì. L'impostazione e la configurazione della disponibilità elevata vengono eseguite nel software client HSM fornito da Gemalto. Moduli di protezione hardware dalla stessa rete virtuale o altre reti virtuali nella stessa area o in aree o in moduli di protezione hardware locale connesso a una rete virtuale usando site-to-site o VPN da punto a punto possono essere aggiunti alla stessa configurazione a disponibilità elevata. Si noti che ciò consente di sincronizzare solo il materiale della chiave e gli elementi di configurazione non specifiche, ad esempio i ruoli.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>D: È possibile aggiungere moduli di protezione hardware dalla rete locale a un gruppo di disponibilità elevata con HSM dedicato di Azure?

Sì. I moduli devono soddisfare i requisiti di disponibilità elevata per SafeNet Luna Network HSM 7.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>D: È possibile aggiungere moduli di protezione hardware Luna 5/6 da reti locali a un gruppo di disponibilità elevata con HSM dedicato di Azure?

 No.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>D: Quanti moduli di protezione hardware è possibile aggiungere alla stessa configurazione con disponibilità elevata da una singola applicazione?

16 membri di un gruppo di disponibilità elevata è sotto gone, full-limitazione test con risultati eccellenti.

## <a name="support"></a>Supporto

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>D: Qual è il contratto di servizio per il servizio HSM dedicato?

Non è possibile garantire tempi di attività specifico fornito per il servizio modulo di protezione hardware dedicati. Microsoft garantirà l'accesso a livello di rete al dispositivo e quindi sono applicabili i contratti di servizio standard della rete di Azure.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>D: Come vengono protetti i moduli di protezione hardware in HSM dedicato di Azure?

I data center di Azure dispongono di controlli di sicurezza fisici e procedurali completi. Oltre a questo, i moduli di protezione hardware dedicati sono ospitati in un'area del data center con accesso soggetto a restrizioni. Queste aree hanno ulteriori controlli di accesso fisico e videocamere di sorveglianza per una maggiore sicurezza.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>D: Cosa accade se si verifica un evento di violazione della sicurezza o di manomissione dell'hardware?

Il servizio HSM dedicato usa appliance SafeNet Network HSM 7. Queste appliance supportano il rilevamento di manomissioni fisiche e logiche. Se si verificasse un evento di manomissione, i moduli di protezione hardware verrebbero automaticamente impostati su zero.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>D: Come ci si può assicurare che le chiavi in HSM dedicato non vadano perse a causa di un errore o un attacco dannoso dall'interno?

È altamente consigliabile usare un dispositivo di backup HSM locale per eseguire regolari backup periodici dei moduli di protezione hardware a scopi di ripristino di emergenza. È necessario usare una connessione peer-to-peer o VPN da sito a sito a una workstation locale connessa al dispositivo HSM di backup.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>D: Come si può ottenere assistenza per HSM dedicato?

Il supporto è fornito da Microsoft e Gemalto.  Se si dispone di un problema con l'accesso di rete o l'hardware, genera una richiesta di supporto con Microsoft e, se si verifica un problema con la configurazione di modulo di protezione hardware, software e lo sviluppo di applicazioni, generare una richiesta di supporto con Gemalto. Se si verifica un problema non determinato, generare una richiesta di supporto con Microsoft e quindi può essere avviato il Gemalto come necessario. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-safenet-luna-7-hsm"></a>D: Come ottenere il client software, documentazione e l'accesso al materiale sussidiario sull'integrazione per il modulo di protezione hardware di SafeNet Luna 7?

Dopo la registrazione per il servizio, un ID cliente Gemalto sarà a condizione che consente la registrazione nel portale di supporto clienti di Gemalto. Ciò consentirà l'accesso a tutti i software e la documentazione, nonché l'abilitazione di richieste di supporto direttamente con Gemalto.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>D: Se viene individuata una vulnerabilità di sicurezza e Gemalto rilascia una patch, chi è responsabile dell'aggiornamento e dell'applicazione di patch per il sistema operativo o il firmware?

Microsoft non ha la possibilità di connettersi ai moduli di protezione hardware allocati ai clienti. I clienti devono eseguire l'aggiornamento e applicare le patch ai propri moduli di protezione hardware.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>D: Cosa accade se è necessario riavviare il modulo di protezione hardware?

Il modulo di protezione hardware è un'opzione di riavvio della riga di comando, tuttavia, si verificano problemi di blocco di riavvio in modo intermittente e per questo motivo, è consigliabile per l'approccio più sicuro il riavvio di generare una richiesta di supporto con Microsoft affinché il dispositivo fisicamente riavviato. 

## <a name="cryptography-and-standards"></a>Crittografia e standard

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>D: È sicuro archiviare le chiavi di crittografia per i dati più importanti in HSM dedicato?

Sì, HSM dedicato effettua il provisioning di appliance SafeNet Network HSM 7, che usano moduli di protezione hardware convalidati FIPS 140-2 Livello 3. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>D: Quali chiavi di crittografia e algoritmi sono supportati da HSM dedicato?

Il servizio HSM dedicato effettua il provisioning di appliance SafeNet Network HSM 7. Queste supportano un'ampia gamma di tipi di algoritmi e chiavi di crittografia, tra cui: Supporto completo di Suite B

* Asimmetrica:
  * RSA
  * DSA
  * Diffie-Hellman
  * Curva ellittica
  * Crittografia (ECDSA, ECDH, Ed25519, ECIES) con curve denominate, definite dall'utente e Brainpool, KCDSA
* Simmetrica:
  * AES-GCM
  * Triple DES
  * DES
  * ARIA, SEED
  * RC2
  * RC4
  * RC5
  * CAST
  * Hash/Digest del messaggio/HMAC: SHA-1, SHA-2, SM3
  * Derivazione chiave: SP800-108 modalità contatore
  * Wrapping della chiave: SP800-38F
  * Generazione di numeri casuali: DRBG approvato FIPS 140-2 (SP 800-90 modalità CTR), conforme a BSI DRG.4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>D: HSM dedicato è convalidato FIPS 140-2 Livello 3?

Sì. Il servizio HSM dedicato effettua il provisioning di appliance SafeNet Network HSM 7, che usano moduli di protezione hardware convalidati FIPS 140-2 Livello 3.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>D: Cosa occorre fare per assicurarsi di usare HSM dedicato in modalità convalidata FIPS 140-2 Livello 3?

Il servizio HSM dedicato effettua il provisioning di appliance SafeNet Luna Network HSM 7. Queste appliance usano moduli di protezione hardware convalidati in base agli standard FIPS 140-2 Livello 3. La configurazione predefinita distribuita, il sistema operativo e il firmware sono anch'essi convalidati FIPS. Non è richiesta alcuna azione per la conformità a FIPS 140-2 Livello 3.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>D: Come può un cliente assicurarsi che quando viene effettuato il deprovisioning di un modulo di protezione hardware tutto il materiale delle chiavi venga cancellato?

Prima di richiedere il deprovisioning, il cliente deve impostare su zero il modulo di protezione hardware usando gli strumenti del client HSM forniti da Gemalto.

## <a name="performance-and-scale"></a>Prestazioni e scalabilità

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>D: Quante operazioni di crittografia al secondo sono supportate con HSM dedicato?

HSM dedicato effettua il provisioning di appliance SafeNet Network HSM 7 (modello A790). Ecco un riepilogo delle prestazioni massime per alcune operazioni: 

* RSA-2048: 10.000 transazioni al secondo
* ECC P256: 20.000 transazioni al secondo
* AES-GCM: 17.000 transazioni al secondo

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>D: Quante partizioni è possibile creare in HSM dedicato?

Il modello di 7 di HSM SafeNet Luna che a790 usato include una licenza per 10 partizioni nel costo del servizio. Il dispositivo ha un limite di 100 partizioni e aggiunta di partizioni fino a questo limite potrebbe incorrere in costi di licenza aggiuntivi e richiedono l'installazione di un nuovo file di licenza nel dispositivo.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>D: Quante chiavi possono essere supportate in HSM dedicato?

Il numero massimo di chiavi è una funzione della memoria disponibile. Il modello SafeNet Luna 7 A790 in uso è 32MB di memoria. I numeri seguenti sono applicabili a coppie di chiavi anche se si usa le chiavi asimmetriche.

* RSA-2048 - 19.000
* ECC-P256 - 91.000

La capacità varia in base agli specifici attributi delle chiavi impostati nel modello di generazione chiavi e al numero di partizioni.
