---
title: Crittografia dei dati inattivi di Microsoft Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica della crittografia dei dati inattivi di Microsoft Azure, con una descrizione delle funzionalità e considerazioni generali.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/07/2019
ms.author: barclayn
ms.openlocfilehash: d0974b98975b8f7d09760be964024f92e9690a4e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65596377"
---
# <a name="azure-data-encryption-at-rest"></a>Crittografia dei dati inattivi di Azure

Microsoft Azure include strumenti per proteggere i dati in base alle esigenze di sicurezza e conformità dell'azienda. Questo documento è incentrato su:

- come proteggere i dati inattivi in Microsoft Azure,
- i vari componenti che compongono l'implementazione di protezione dei dati,
- i pro e i contro dei vari principali approcci alla protezione della gestione.

La crittografia dei dati inattivi è un requisito di sicurezza comune. In Azure, le organizzazioni possono crittografare i dati inattivi senza rischi o senza sostenere il costo di una soluzione di gestione delle chiavi personalizzata. Le organizzazioni hanno la possibilità di lasciare che sia Azure a gestire completamente la crittografia dei dati inattivi. Sono inoltre disponibili varie opzioni per gestire con precisione la crittografia o le chiavi di crittografia.

## <a name="what-is-encryption-at-rest"></a>Che cos'è la crittografia dei dati inattivi?

La crittografia dei dati inattivi consiste nella codifica crittografica (crittografia) dei dati quando questi vengono resi persistenti. La crittografia dei dati inattivi di Azure usa la crittografia simmetrica per crittografare e decrittografare rapidamente grandi quantità di dati in base a un semplice modello concettuale:

- Viene usata una chiave di crittografia simmetrica per crittografare i dati quando vengono scritti nella risorsa di archiviazione.
- La chiave di crittografia viene usata per decrittografare i dati durante la loro preparazione per l'uso in memoria.
- I dati possono essere partizionati ed è possibile usare chiavi diverse per ogni partizione.
- Le chiavi devono essere archiviate in una posizione sicura con controllo di accesso basato su identità e criteri di controllo. Le chiavi di crittografia dati vengono spesso crittografate con la crittografia asimmetrica per limitare ulteriormente l'accesso.

A livello pratico, gli scenari di gestione e di controllo delle chiavi, nonché le garanzie di scalabilità e disponibilità, richiedono costrutti aggiuntivi. Di seguito sono descritti i concetti e i componenti relativi alla crittografia dei dati inattivi di Microsoft Azure.

## <a name="the-purpose-of-encryption-at-rest"></a>Scopo della crittografia dei dati inattivi

La crittografia dei dati inattivi offre la protezione dei dati per i dati archiviati (inattivi). Gli attacchi contro i dati inattivi includono i tentativi di ottenere l'accesso fisico all'hardware in cui sono archiviati i dati e quindi compromettere i dati contenuti. In un attacco di questo tipo, l'unità disco rigido del server potrebbe essere manipolata durante le attività di manutenzione, consentendo a un utente malintenzionato di rimuovere il disco rigido. L'autore dell'attacco potrà quindi inserire il disco rigido in un computer sotto il proprio controllo per tentare di accedere ai dati.

La crittografia dei dati inattivi è progettata per impedire a un utente malintenzionato di accedere ai dati non crittografati, garantendo che i dati siano crittografati quando sono su disco. Se un utente malintenzionato ottiene un disco rigido con i dati crittografati ma non le chiavi di crittografia, l'autore dell'attacco deve decodificare la crittografia per riuscire a leggere i dati. Questo tipo di attacco è molto più complesso e richiede maggiori risorse rispetto all'accesso a dati non crittografati su un disco rigido. Per questo motivo, la crittografia dei dati inattivi è estremamente utile e costituisce un requisito di alta priorità per molte organizzazioni.

La crittografia dei dati inattivi può anche essere necessaria per i requisiti di conformità e governance dei dati dell'organizzazione. Regolamenti governativi e di settore, come HIPAA, PCI e FedRAMP, definiscono specifiche misure di sicurezza relativi ai requisiti di protezione e crittografia dei dati. La crittografia dei dati inattivi è una misura obbligatoria necessaria per la conformità ad alcune di queste normative.

Oltre a soddisfare i requisiti normativi e di conformità, la crittografia dei dati inattivi rappresenta una misura di protezione e difesa avanzata. Microsoft Azure offre una piattaforma conforme per servizi, applicazioni e dati. Offre inoltre funzionalità complete per la sicurezza fisica e delle strutture, il controllo di accesso ai dati e il controllo. Tuttavia, è importante prevedere ulteriori misure di sicurezza "sovrapposte" in caso di inadeguatezza delle altre misure di protezione e la crittografia dei dati inattivi rappresenta una di queste misure di sicurezza.

Microsoft si impegna a mettere a disposizione opzioni per la crittografia dei dati inattivi per i servizi cloud e offre ai clienti il controllo delle chiavi di crittografia e dei log di uso delle chiavi. Microsoft ha inoltre in progetto di applicare la crittografia dei dati inattivi a tutti i dati dei clienti per impostazione predefinita.

## <a name="azure-encryption-at-rest-components"></a>Componenti della crittografia dei dati inattivi di Azure

Come descritto in precedenza, l'obiettivo della crittografia dei dati inattivi è crittografare i dati resi persistenti su disco con una chiave di crittografia segreta. Per raggiungere tale obiettivo, sono necessarie funzionalità per la creazione, l'archiviazione, il controllo dell'accesso e la gestione delle chiavi di crittografia. Anche se i dettagli possono variare, i servizi per la crittografia dei dati inattivi di Azure possono essere descritti nei termini illustrati nel diagramma seguente.

![Componenti](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

La posizione di archiviazione delle chiavi di crittografia e il controllo dell'accesso a queste chiavi è essenziale per un modello di crittografia dei dati inattivi. Le chiavi devono essere estremamente protette ma gestibili dagli utenti specificati e disponibili per servizi specifici. Per i servizi di Azure, la soluzione di archiviazione delle chiavi consigliata è Azure Key Vault, che fornisce un'esperienza di gestione comune per tutti i servizi. Le chiavi sono archiviate e gestite in insiemi di credenziali delle chiavi e l'accesso a un insieme di credenziali delle chiavi può essere assegnato a utenti o servizi. Azure Key Vault supporta la creazione di chiavi da parte dei clienti o l'importazione di chiavi dei clienti per l'uso in scenari con chiavi di crittografia gestite dal cliente.

### <a name="azure-active-directory"></a>Azure Active Directory

Le autorizzazioni per l'uso delle chiavi archiviate in Azure Key Vault (per la gestione o per accedervi per la crittografia e la decrittografia dei dati inattivi) possono essere assegnate agli account di Azure Active Directory.

### <a name="key-hierarchy"></a>Gerarchia delle chiavi

Viene usata più di una chiave di crittografia in un'implementazione della crittografia per i dati inattivi. La crittografia asimmetrica è utile per stabilire l'attendibilità e l'autenticazione necessarie per la gestione e l'accesso alle chiavi. La crittografia simmetrica è più efficiente per la crittografia e la decrittografia in blocco, perché consente una crittografia più avanzata e prestazioni migliori. Limitare l'uso di una sola chiave di crittografia riduce il rischio di compromissione della chiave e il costo della ripetizione della crittografia quando è necessario sostituire una chiave. I modelli di crittografia dei dati inattivi usano una gerarchia di chiavi composta dai tipi di chiavi seguenti:

- **Chiave DEK (Data Encryption Key)** : una chiave AES256 simmetrica usata per crittografare una partizione o un blocco di dati.  Una singola risorsa può avere diverse partizioni e più chiavi DEK. La crittografia di ogni blocco di dati con una chiave diversa rende più complessi gli attacchi di crittoanalisi. È necessario l'accesso alle chiavi DEK per il provider di risorse o l'istanza dell'applicazione che esegue la crittografia e la decrittografia di un blocco specifico. Quando una chiave DEK viene sostituita con una nuova chiave, devono essere nuovamente crittografati con la nuova chiave solo i dati nel blocco associato.
- **Chiave KEK (Key Encryption Key)** : una chiave asimmetrica usata per crittografare le chiavi di crittografia. L'uso di una chiave di crittografia delle chiavi consente di crittografare e controllare le stesse chiavi di crittografia dati. L'entità che ha accesso alla chiave KEK può essere diversa dall'entità che richiede la chiave DEK. Un'entità può gestire l'accesso alla chiave DEK per limitare l'accesso di ogni chiave DEK a una partizione specifica. Poiché è necessaria la chiave KEK per decrittografare le chiavi DEK, la chiave KEK è di fatto un singolo punto che consente di eliminare in modo efficace le chiavi DEK eliminando la chiave KEK.

Le chiavi di crittografia dei dati, crittografate con le chiavi di crittografia delle chiavi, sono archiviate separatamente e solo un'entità che ha accesso alla chiave di crittografia delle chiavi può ottenere le chiavi di crittografia dei dati crittografate con tale chiave. Sono supportati diversi modelli di archiviazione delle chiavi. Ogni modello verrà illustrato in modo più dettagliato nella sezione successiva.

## <a name="data-encryption-models"></a>Modelli di crittografia dei dati

Comprendere i diversi modelli di crittografia, e i relativi vantaggi e svantaggi, è fondamentale per capire come i vari provider di risorse in Azure implementano la crittografia dei dati inattivi. Queste definizioni sono condivise tra tutti i provider di risorse in Azure per garantire un linguaggio e una tassonomia comuni.

Esistono tre scenari per la crittografia sul lato server:

- Crittografia sul lato server con chiavi gestite dal servizio
  - I provider di risorse di Azure eseguono le operazioni di crittografia e decrittografia
  - Microsoft gestisce le chiavi
  - Funzionalità cloud complete

- Crittografia lato server con chiavi gestite dal cliente in Azure Key Vault
  - I provider di risorse di Azure eseguono le operazioni di crittografia e decrittografia
  - Il cliente controlla le chiavi tramite Azure Key Vault
  - Funzionalità cloud complete

- Crittografia lato server con chiavi gestite dal cliente su hardware controllato dal cliente
  - I provider di risorse di Azure eseguono le operazioni di crittografia e decrittografia
  - Il cliente controlla le chiavi su hardware controllato dal cliente
  - Funzionalità cloud complete

Per la crittografia lato client, considerare quanto segue:

- I servizi di Azure non possono visualizzare i dati decrittografati
- I clienti mantengono e archiviano le chiavi in locale o in altri archivi sicuri. Le chiavi non sono disponibili per i servizi di Azure
- Funzionalità cloud ridotte

I modelli di crittografia supportati in Azure sono suddivisi in due gruppi principali: “crittografia lato client” e “crittografia lato server” come riportato in precedenza. Indipendentemente dal modello di crittografia dei dati inattivi in uso, per i servizi di Azure è sempre consigliabile usare un trasporto protetto, ad esempio TLS o HTTPS. La crittografia a livello di trasporto deve quindi essere gestita dal protocollo di trasporto e non deve rappresentare un fattore determinante per la scelta del modello di crittografia dei dati inattivi da usare.

### <a name="client-encryption-model"></a>Modello di crittografia client

Il modello di crittografia client fa riferimento alla crittografia che viene eseguita all'esterno del provider di risorse o da Azure dal servizio o dall'applicazione chiamante. La crittografia può essere eseguita dall'applicazione del servizio in Azure o da un'applicazione in esecuzione nel data center del cliente. In entrambi i casi, quando si usa questo modello di crittografia, il provider di risorse di Azure riceve un blob di dati crittografato senza alcuna possibilità di decrittografare i dati o avere accesso alle chiavi di crittografia. In questo modello la gestione delle chiavi viene eseguita dal servizio o dall'applicazione chiamante ed è opaca al servizio di Azure.

![Client](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Modello di crittografia lato server

I modelli di crittografia lato server fanno riferimento alla crittografia che viene eseguita dal servizio di Azure. In questo modello, le operazioni di crittografia e decrittografia sono eseguite dal provider di risorse. Archiviazione di Azure può ad esempio ricevere i dati in operazioni di testo normale ed esegue internamente la crittografia e la decrittografia. Il provider di risorse può usare chiavi di crittografia gestite da Microsoft o dal cliente, a seconda della configurazione specificata.

![Server](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Modelli di gestione delle chiavi per la crittografia lato server

Ognuno dei modelli di crittografia dei dati inattivi lato server implica caratteristiche distintive per la gestione delle chiavi. Questo include le posizioni e le modalità di creazione e archiviazione delle chiavi di crittografia, nonché i modelli di accesso e le procedure di rotazione delle chiavi.

#### <a name="server-side-encryption-using-service-managed-keys"></a>Crittografia lato server con chiavi gestite dal servizio

Per molti clienti, il requisito essenziale consiste nel garantire che i dati siano crittografati ogni volta che sono inattivi. La crittografia sul lato server tramite le chiavi gestite dal servizio rende possibile questo modello, consentendo ai clienti di contrassegnare le specifiche risorse (account di archiviazione, database SQL e così via) per la crittografia e lasciare a Microsoft tutti gli aspetti di gestione delle chiavi, come il rilascio, la rotazione e il backup. La maggior parte dei servizi di Azure che supportano la crittografia dei dati inattivi in genere supporta questo modello di offload della gestione delle chiavi di crittografia in Azure. Il provider di risorse di Azure crea le chiavi, le inserisce in un archivio protetto e le recupera quando necessario. Questo significa che il servizio ha l'accesso completo alle chiavi e il pieno controllo della gestione del ciclo di vita delle credenziali.

![gestito](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig4.png)

La crittografia sul lato server con chiavi gestite dal servizio consente pertanto di soddisfare rapidamente l'esigenza di implementare la crittografia dei dati inattivi con un sovraccarico limitato per il cliente. Quando disponibile, un cliente apre il portale di Azure per la sottoscrizione e il provider di risorse di destinazione e seleziona una casella per indicare che vuole che i dati vengano crittografati. In alcuni manager delle risorse la crittografia lato server con chiavi gestite dal servizio è attiva per impostazione predefinita.

La crittografia sul lato server con chiavi gestite da Microsoft implica che il servizio ha accesso completo all'archiviazione e gestisce le chiavi. Anche se alcuni clienti potrebbero voler gestire le chiavi perché pensano di garantire una maggiore sicurezza, durante la valutazione di questo modello è importante tenere conto del costo e del rischio associato a una soluzione personalizzata di archiviazione delle chiavi. In molti casi, un'organizzazione può stabilire che i vincoli di risorse o i rischi di una soluzione locale potrebbero essere maggiori rispetto al rischio associato alla gestione nel cloud delle chiavi di crittografia dei dati inattivi.  Questo modello, tuttavia, potrebbe non essere sufficiente per le organizzazioni che hanno l'esigenza di controllare la creazione o il ciclo di vita delle chiavi di crittografia oppure per fare in modo che la gestione delle chiavi di crittografia di un servizio venga eseguita da personale diverso da quello che gestisce il servizio (ad esempio, separando la gestione delle chiavi dal modello di gestione generale per il servizio).

##### <a name="key-access"></a>Accesso alle chiavi

Quando si usa la crittografia sul lato server con chiavi gestite dal servizio, la creazione delle chiavi, l'archiviazione e l'accesso al servizio sono gestiti dal servizio. In genere, i principali provider di risorse di Azure archiviano le chiavi DEK in un archivio vicino ai dati e rapidamente disponibile e accessibile, mentre le chiavi KEK sono archiviate in un archivio interno protetto.

**Vantaggi**

- Configurazione semplice
- Microsoft gestisce la rotazione, il backup e la ridondanza delle chiavi
- Il cliente non deve sostenere il costo associato all'implementazione o il rischio di uno schema di gestione delle chiavi personalizzato.

**Svantaggi**

- Nessun controllo del cliente sulle chiavi di crittografia (specifica della chiave, ciclo di vita, revoca e così via)
- Nessuna possibilità di separare la gestione delle chiavi dal modello generale di gestione per il servizio

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Crittografia lato server con chiavi gestite dal cliente in Azure Key Vault

Per gli scenari in cui il requisito prevede di crittografare i dati inattivi e controllare le chiavi di crittografia, i clienti possono usare la crittografia sul lato server con chiavi gestite dal cliente in Azure Key Vault. Alcuni servizi possono archiviare solo la chiave KEK radice in Azure Key Vault e archiviano la chiave DEK crittografata in un percorso interno più vicino ai dati. In questo scenario, i clienti possono usare le proprie chiavi nell'insieme di credenziali delle chiavi (BYOK, Bring Your Own Key) o generare nuove chiavi e usarle per crittografare le risorse desiderate. Mentre il provider di risorse esegue le operazioni di crittografia e decrittografia, usa la chiave configurata come chiave radice per tutte le operazioni di crittografia.

##### <a name="key-access"></a>Accesso alle chiavi

Il modello di crittografia sul lato server con chiavi gestite dal cliente in Azure Key Vault prevede che il servizio di accesso alle chiavi possa eseguire la crittografia e la decrittografia in base alle esigenze. Le chiavi per la crittografia dei dati inattivi vengono rese accessibili a un servizio tramite un criterio di controllo di accesso che concede all'identità del servizio l'accesso per ricevere la chiave. Un servizio di Azure in esecuzione per conto di una sottoscrizione associata può essere configurato con un'identità all'interno della sottoscrizione. Il servizio può eseguire l'autenticazione di Azure Active Directory e ricevere un token di autenticazione che lo identifica come un servizio che opera per conto della sottoscrizione. Il token può quindi essere presentato all'insieme di credenziali delle chiavi per ottenere una chiave a cui è stato consentito l'accesso.

Per le operazioni con chiavi di crittografia, può essere concesso l'accesso a un'identità del servizio per qualsiasi delle operazioni seguenti: decrypt, encrypt, unwrapkey, wrapkey, verify, sign, get, list, update, create, import, delete, backup e restore.

Per ottenere una chiave da usare per la crittografia o la decrittografia dei dati inattivi, l'identità del servizio con cui verrà eseguita l'istanza del servizio Resource Manager deve disporre di UnwrapKey (per ottenere la chiave per la decrittografia) e WrapKey (per inserire una chiave nell'insieme di credenziali delle chiavi al momento della creazione di una nuova chiave).

>[!NOTE]
>Per altri dettagli sull'autorizzazione dell'insieme di credenziali delle chiavi, vedere la pagina Proteggere l'insieme di credenziali delle chiavi nella [documentazione di Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

**Vantaggi**

- Controllo completo delle chiavi in uso: le chiavi di crittografia vengono gestite nell'insieme di credenziali delle chiavi del cliente, sotto il controllo del cliente.
- Possibilità di crittografare più servizi in uno master
- Possibilità di separare la gestione delle chiavi dal modello generale di gestione per il servizio
- Possibilità di definire il servizio e la posizione delle chiavi tra diverse aree geografiche

**Svantaggi**

- Il cliente ha la piena responsabilità per la gestione dell'accesso alle chiavi
- Il cliente ha la piena responsabilità per la gestione del ciclo di vita delle chiavi
- Sovraccarico aggiuntivo per l'installazione e la configurazione

#### <a name="server-side-encryption-using-service-managed-keys-in-customer-controlled-hardware"></a>Crittografia lato server con chiavi gestite dal servizio su hardware controllato dal cliente

Alcuni servizi di Azure consentono il modello di gestione delle chiavi HYOK (Host Your Own Key). Questa modalità di gestione è utile negli scenari in cui è necessario crittografare i dati inattivi e gestire le chiavi presenti in un repository proprietario al di fuori del controllo di Microsoft. In questo modello il servizio deve recuperare la chiave da un sito esterno. Ciò ha effetto sulle garanzie di prestazioni e disponibilità e la configurazione risulta più complessa. Inoltre, poiché il servizio non ha accesso alla chiave DEK durante le operazioni di crittografia e decrittografia, le garanzie di sicurezza complessiva di questo modello sono simili allo scenario in cui le chiavi sono gestite dal cliente in Azure Key Vault.  Di conseguenza, questo modello non è appropriato per la maggior parte delle organizzazioni, a meno che non siano presenti specifici requisiti di gestione delle chiavi. A causa di queste limitazioni, la maggior parte dei servizi di Azure non supporta la crittografia lato server con chiavi gestite dal server su hardware controllato dal cliente.

##### <a name="key-access"></a>Accesso alle chiavi

Quando si usa la crittografia sul lato server con chiavi gestite dal servizio su hardware controllato dal cliente, le chiavi vengono mantenute in un sistema configurato dal cliente. I servizi di Azure che supportano questo modello forniscono un sistema per stabilire una connessione sicura a un archivio delle chiavi fornito dal cliente.

**Vantaggi**

- Controllo completo della chiave radice in uso: le chiavi di crittografia vengono gestite tramite un archivio fornito dal cliente
- Possibilità di crittografare più servizi in uno master
- Possibilità di separare la gestione delle chiavi dal modello generale di gestione per il servizio
- Possibilità di definire il servizio e la posizione delle chiavi tra diverse aree geografiche

**Svantaggi**

- Piena responsabilità per l'archiviazione di chiavi, sicurezza, prestazioni e disponibilità
- Piena responsabilità per la gestione dell'accesso alle chiavi
- Piena responsabilità per la gestione del ciclo di vita delle chiavi
- Significativi costi di installazione, configurazione e manutenzione
- Maggiore dipendenza dalla disponibilità della rete tra il data center del cliente e i data center di Azure.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Crittografia dei dati inattivi nei servizi cloud Microsoft

I servizi cloud Microsoft vengono usati in tutti e tre i modelli cloud: IaaS, PaaS, SaaS. Di seguito sono riportati alcuni esempi di come si integrano in ciascun modello:

- Servizi software, denominati SaaS (Software as a Service), in cui l'applicazione è fornita dal cloud, come ad esempio Office 365.
- Servizi della piattaforma, con cui i clienti sfruttano il cloud nelle proprie applicazioni, che usano il cloud per elementi come le funzionalità di archiviazione, analisi e bus di servizio.
- Servizi di infrastruttura, o IaaS (Infrastructure as a Service), in cui i clienti distribuiscono sistemi operativi e applicazioni ospitati nel cloud ed eventualmente sfruttano altri servizi cloud.

### <a name="encryption-at-rest-for-saas-customers"></a>Crittografia dei dati inattivi per i clienti SaaS

Per i clienti SaaS (Software as a Service) in genere la crittografia dei dati inattivi è abilitata o disponibile in ogni servizio. Office 365 offre diverse opzioni con cui i clienti possono verificare o abilitare la crittografia dei dati inattivi. Per informazioni sui servizi di Office 365, vedere [Crittografia in Office 365](https://docs.microsoft.com/office365/securitycompliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Crittografia dei dati inattivi per i clienti PaaS

I dati dei clienti PaaS (Platform as a Service) in genere si trovano in un ambiente di esecuzione dell'applicazione e qualsiasi provider di risorse di Azure usato per archiviare i dati dei clienti. Per visualizzare le opzioni disponibili per la crittografia dei dati inattivi, esaminare la tabella seguente per le piattaforme di archiviazione e per le applicazioni in uso. Se supportati, vengono forniti collegamenti a istruzioni sull'abilitazione della crittografia dei dati inattivi per ogni provider di risorse.

### <a name="encryption-at-rest-for-iaas-customers"></a>Crittografia dei dati inattivi per i clienti IaaS

I clienti IaaS (Infrastructure as a Service) possono avere un'ampia gamma di servizi e applicazioni in uso. I servizi IaaS possono abilitare la crittografia dei dati inattivi nelle macchine virtuali e nei dischi rigidi virtuali ospitati in Azure usando Crittografia dischi di Azure.

#### <a name="encrypted-storage"></a>Archiviazione crittografata

Come le soluzioni PaaS, quelle IaaS possono sfruttare altri servizi di Azure che archiviano dati inattivi crittografati. In questi casi, è possibile abilitare il supporto per la crittografia dei dati inattivi come specificato da ogni servizio di Azure in uso. Nella tabella seguente sono enumerate le principali piattaforme per l'archiviazione, i servizi e le applicazioni, insieme al modello di crittografia dei dati inattivi supportato. Se supportati, vengono forniti collegamenti a istruzioni sull'abilitazione della crittografia dei dati inattivi.

#### <a name="encrypted-compute"></a>Calcolo crittografato

Una soluzione completa di crittografia dei dati inattivi richiede che i dati non vengano mai resi persistenti in formato non crittografato. Mentre sono in uso, in un server che esegue il caricamento dei dati in memoria, i dati possono essere resi persistenti in locale in vari modi, tra cui il file di paging di Windows, un dump di arresto anomalo del sistema e qualsiasi registrazione che l'applicazione può eseguire. Per assicurarsi che questi dati inattivi vengano crittografati, le applicazioni IaaS possono usare Crittografia dischi di Azure su una macchina virtuale IaaS di Azure (Windows o Linux) e un disco virtuale.

#### <a name="custom-encryption-at-rest"></a>Crittografia dei dati inattivi personalizzata

È consigliabile che, quando possibile, le applicazioni IaaS sfruttino Crittografia dischi di Azure e le opzioni di crittografia dei dati inattivi fornite da qualsiasi servizio di Azure in uso. In alcuni casi, ad esempio per requisiti di crittografia irregolari o l'archiviazione non basata su Azure, uno sviluppatore di un'applicazione IaaS potrebbe avere bisogno di implementare autonomamente la crittografia dei dati inattivi. Gli sviluppatori di soluzioni IaaS possono ottimizzare l'integrazione con la gestione di Azure le aspettative dei clienti sfruttando determinati componenti di Azure. In particolare, gli sviluppatori devono usare il servizio Azure Key Vault per fornire l'archiviazione protetta delle chiavi, nonché offrire ai clienti opzioni di gestione delle chiavi coerenti con quelle della maggior parte dei servizi della piattaforma Azure. Le soluzioni personalizzate devono anche usare le identità dei servizi gestiti di Azure per consentire agli account del servizio di accedere alle chiavi di crittografia. Per informazioni per gli sviluppatori su Azure Key Vault e le identità dei servizi gestiti, vedere i rispettivi SDK.

## <a name="azure-resource-providers-encryption-model-support"></a>Supporto per il modello di crittografia dei provider di risorse di Azure

Ogni servizio di Microsoft Azure supporta uno o più modelli di crittografia dei dati inattivi. Per alcuni servizi, tuttavia, uno o più dei modelli di crittografia potrebbero non essere applicabili. I servizi che supportano scenari con chiavi gestite dal cliente potrebbero supportare solo un subset dei tipi di chiavi supportati da Azure Key Vault per le chiavi di crittografia della chiave. I servizi possono inoltre rilasciare il supporto per questi scenari e per i tipi di chiave con pianificazioni diverse. In questa sezione viene descritto il supporto per la crittografia dei dati inattivi al momento della stesura del presente documento per ognuno dei principali servizi di archiviazione dati di Azure.

### <a name="azure-disk-encryption"></a>Crittografia dischi di Azure

Tutti i clienti che usano le funzionalità IaaS (Infrastructure as a Service) di Azure possono ottenere la crittografia dei dati inattivi per le macchine virtuali IaaS e i dischi tramite Crittografia dischi di Azure. Per altre informazioni su Crittografia dischi di Azure, vedere la [documentazione di Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

#### <a name="azure-storage"></a>Archiviazione di Azure

Tutti i servizi di Archiviazione di Azure (archiviazione BLOB, archiviazione code, archiviazione tabelle e File di Azure) supportano la crittografia lato server da inattivi, con alcuni servizi che supportano le chiavi gestite dal cliente e la crittografia lato client.  

- Lato server: tutti i servizi di archiviazione di Azure abilitano la crittografia lato server per impostazione predefinita con chiavi gestite dal servizio, in modo trasparente all'applicazione. Per altre informazioni, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](https://docs.microsoft.com/azure/storage/storage-service-encryption). Archiviazione BLOB di Azure e File di Azure supportano anche chiavi RSA a 2048 bit gestite dal cliente in Azure Key Vault. Per altre informazioni, vedere [Crittografia del servizio di archiviazione di Azure con chiavi gestite dal cliente in Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).
- Lato client: BLOB di Azure, tabelle e code di Azure supportano la crittografia lato client. Quando usano la crittografia lato client, i clienti crittografano i dati e li caricano come un BLOB crittografato. La gestione delle chiavi viene eseguita dal cliente. Per altre informazioni, vedere [Crittografia lato client e Azure Key Vault per Archiviazione di Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-client-side-encryption).

#### <a name="azure-sql-database"></a>Database SQL di Azure

Database SQL di Azure attualmente supporta la crittografia dei dati inattivi per gli scenari di crittografia sul lato client e sul lato del servizio gestito da Microsoft.

Il supporto per la crittografia server viene attualmente fornito tramite la funzionalità di SQL denominata Transparent Data Encryption. Quando un cliente di Database SQL di Azure abilita TDE, le chiavi vengono create e gestite automaticamente. La crittografia dei dati inattivi può essere abilitata a livello di database e di server. A partire da giugno 2017, la funzionalità [Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) è abilitata per impostazione predefinita nei nuovi database creati. Database SQL di Azure supporta chiavi RSA a 2048 bit gestite dal cliente in Azure Key Vault. Per altre informazioni, vedere [Transparent Data Encryption con supporto BYOK (Bring Your Own Key) per il database SQL di Azure e Azure SQL Data Warehouse](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

La crittografia lato client dei dati di Database SQL di Azure è supportata tramite la funzionalità [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx). Always Encrypted usa una chiave creata e archiviata dal client. I clienti possono archiviare la chiave master in un archivio certificati di Windows, in Azure Key Vault o in un modulo di protezione hardware locale. Tramite SQL Server Management Studio, gli utenti SQL possono scegliere la chiave da usare per crittografare ogni colonna.

|                                  |                    | **Modello di crittografia e gestione delle chiavi** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **Lato server con chiave gestita dal servizio**     | **Lato server con chiave gestita dal cliente in Key Vault**             | **Lato client con chiave gestita dal client**      |
| **Intelligenza artificiale e Machine Learning**      |                    |                    |                    |
| Ricerca di Azure                     | Yes                | -                  | -                  |
| Servizio Azure Machine Learning   | Yes                | -                  | -                  |
| Azure Machine Learning Studio    | Yes                | Anteprima, RSA a 2048 bit | -               |
| Power BI                         | Yes                | -                  | -                  |
| **Analisi**                    |                    |                    |                    |
| Analisi di flusso di Azure           | Yes                | -                  | -                  |
| Hub eventi                       | Yes                | -                  | -                  |
| Azure Analysis Services          | Yes                | -                  | -                  |
| Azure Data Catalog               | Yes                | -                  | -                  |
| HDInsight                        | Yes                | Anteprima per Apache Kafka, tutte le lunghezze RSA | -                  |
| Data factory di Azure               | Yes                | -                  | -                  |
| Archivio Azure Data Lake            | Yes                | Sì, RSA a 2048 bit  | -                  |
| **Calcolo**                      |                    |                    |                    |
| Macchine virtuali                 | -                  | Sì, RSA a 2048 bit  | -                  |
| Set di scalabilità di macchine virtuali        | -                  | Sì, RSA a 2048 bit  | -                  |
| **Database**                    |                    |                    |                    |
| SQL Server nelle macchine virtuali   | Yes                | Sì, RSA a 2048 bit  | Yes                |
| Database SQL di Azure               | Yes                | Sì, RSA a 2048 bit  | Yes                |
| Azure SQL Data Warehouse         | Yes                | Sì, RSA a 2048 bit  | Yes                |
| SQL Server Stretch Database      | Yes                | Sì, RSA a 2048 bit  | Yes                |
| Archiviazione tabelle                    | Yes                | -                  | Yes                |
| Azure Cosmos DB                  | Yes                | -                  | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps                     | Yes                | -                  | Yes                |
| Azure Repos                      | Yes                | -                  | Yes                |
| **Identità**                     |                    |                    |                    |
| Azure Active Directory           | Yes                | -                  | -                  |
| Servizi di dominio Azure Active Directory | Yes          | Sì, RSA a 2048 bit  | -                  |
| **Integrazione**                  |                    |                    |                    |
| Bus di servizio                      | Yes                | -                  | Yes                |
| Griglia di eventi                       | Yes                | -                  | -                  |
| Gestione API                   | Yes                | -                  | -                  |
| **Servizi IoT**                 |                    |                    |                    |
| Hub IoT                          | -                  | -                  | Yes                |
| **Gestione e Governance**    |                    |                    |                    |
| Azure Site Recovery              | Yes                | Sì, RSA a 2048 bit  | Yes                |
| **Media**                        |                    |                    |                    |
| Servizi multimediali                   | Yes                | -                  | Yes                |
| **Archiviazione**                      |                    |                    |                    |
| Archiviazione BLOB                     | Yes                | Sì, RSA a 2048 bit  | Yes                |
| Archiviazione su disco                     | Yes                | -                  | -                  |
| Archiviazione su dischi gestiti             | Yes                | -                  | -                  |
| Archiviazione file                     | Yes                | Sì, RSA a 2048 bit  | -                  |
| Archiviazione code                    | Yes                | -                  | Yes                |
| Avere vFXT                       | Yes                | -                  | -                  |
| Spazio di archiviazione                  | Yes                | Sì, RSA a 2048 bit  | -                  |
| StorSimple                       | Yes                | -                  | Yes                |
| Backup di Azure                     | Yes                | -                  | Yes                |
| Data Box                         | Yes                | -                  | Yes                |

## <a name="conclusion"></a>Conclusioni

La protezione dei dati dei clienti archiviati nei servizi di Azure è di importanza fondamentale per Microsoft. Per tutti i servizi di Azure ospitati è prevista la disponibilità di opzioni di crittografia dei dati inattivi. Servizi fondamentali come Archiviazione di Azure, Database SQL di Azure e alcuni servizi di intelligence e analisi offrono già opzioni di crittografia dei dati inattivi. Alcuni di questi servizi supportano le chiavi controllate dal cliente e la crittografia lato client, nonché le chiavi e la crittografia gestite dal servizio. Microsoft sta apportando notevoli miglioramenti alla disponibilità della crittografia dei dati inattivi nei servizi di Microsoft Azure e nei prossimi mesi sono previste nuove opzioni in versione di anteprima e per la disponibilità generale.
