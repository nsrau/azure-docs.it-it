---
title: Sicurezza di Azure per enti pubblici | Documentazione Microsoft
description: Presenta una panoramica dei servizi disponibili in Azure per enti pubblici
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: c3645bda-bf35-4232-a54d-7a0bfab2d594
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: a9cc2bc044caddec63645932067f7a346945db78
ms.openlocfilehash: 11d0a67a4c368b50e2f2e8648c4fb2db8e0421c2


---
# <a name="security"></a>Sicurezza
## <a name="principles-for-securing-customer-data-in-azure-government"></a>Principi per la protezione dei dati dei clienti in Azure per enti pubblici
Azure Government offre una gamma di funzionalità e servizi che è possibile usare per creare soluzioni cloud per soddisfare le esigenze di gestione dei dati regolamentati o controllati. Una soluzione dei clienti compatibile non è altro che l'efficace implementazione delle funzionalità predefinite di Azure per enti pubblici, associate a una procedura di sicurezza dei dati affidabile.

Quando si ospita una soluzione in Azure per enti pubblici, Microsoft gestisce molti di questi requisiti a livello di infrastruttura cloud.

Il diagramma seguente illustra il modello di difesa avanzata di Azure. Ad esempio, Microsoft offre la protezione di base da attacchi DDoS per l'infrastruttura cloud, unitamente alle funzionalità dei clienti, come le appliance di sicurezza, per le esigenze riguardanti gli attacchi DDoS specifiche delle applicazioni dei clienti.

![testo alternativo](./media/azure-government-Defenseindepth.png)

Questa pagina descrive i principi fondamentali per la protezione dei servizi e delle applicazioni e offre indicazioni e procedure consigliate su come applicare questi principi. In altre parole, illustra in che modo è consigliabile che i clienti usino al meglio Azure per enti pubblici per soddisfare gli obblighi e le responsabilità necessari per una soluzione che gestisce informazioni soggette alla normativa ITAR.

 Ecco i principi globali per la protezione dei dati dei clienti:

* Protezione dei dati mediante la crittografia
* Gestione dei segreti
* Isolamento per limitare l'accesso ai dati

### <a name="protecting-customer-data-using-encryption"></a>Protezione dei dati dei clienti mediante la crittografia
La mitigazione dei rischi e la conformità agli obblighi normativi determinano la crescente attenzione verso la crittografia dei dati e la relativa importanza. Usare un'implementazione di crittografia efficace per migliorare le attuali misure di sicurezza della rete e dell'applicazione e ridurre il rischio complessivo dell'ambiente cloud.

#### <a name="encryption-at-rest"></a>Crittografia di dati inattivi
La crittografia dei dati inattivi si applica alla protezione del contenuto dei clienti conservato nello spazio di archiviazione su disco. Esistono diversi modi possibili a questo scopo:

#### <a name="storage-service-encryption"></a>Crittografia del servizio di archiviazione
La crittografia del servizio di archiviazione viene abilitata a livello di account di archiviazione, determinando la crittografia automatica di BLOB in blocchi e BLOB di pagine al momento della scrittura nella risorsa di archiviazione di Azure. Quando si leggono i dati dall'archiviazione di Azure, verranno decrittografati dal servizio di archiviazione prima di essere restituiti. Usare questo approccio per proteggere i dati senza dover modificare o aggiungere codice alle applicazioni.

#### <a name="client-side-encryption"></a>Crittografia lato client
La crittografia lato client è incorporata nelle librerie client di archiviazione per .NET e Java, che possono utilizzare le API dell'insieme di credenziali delle chiavi di Azure, rendendone semplice l'implementazione. Usare l'insieme di credenziali delle chiavi di Azure per ottenere l'accesso ai segreti nello stesso insieme di credenziali delle chiavi Azure per utenti specifici usando Azure Active Directory.

#### <a name="encryption-in-transit"></a>Crittografia in transito
La crittografia di base disponibile per la connettività ad Azure per enti pubblici supporta il protocollo TLS (Transport Level Security) 1.2 e i certificati X.509. Per le connessioni di rete in modalità infrastruttura tra i data center di Azure per enti pubblici vengono usati anche gli algoritmi di crittografia FIPS (Federal Information Processing Standard) 140-2 livello 1.  Le macchine virtuali Windows Server 2012 R2 e Windows 8 e versioni successive e le condivisioni file di Azure possono usare SMB 3.0 per la crittografia tra la macchina virtuale e la condivisione file. Usare la crittografia lato client per crittografare i dati prima che siano trasferiti nella risorsa di archiviazione in un'applicazione client e per decrittografarli dopo il trasferimento dalla risorsa di archiviazione.

#### <a name="best-practices-for-encryption"></a>Procedure consigliate per la crittografia
* Macchine virtuali IaaS: usare la Crittografia dischi di Azure. Attivare Crittografia del servizio di archiviazione per crittografare i file VHD usati per eseguire il backup di questi dischi nella risorsa di archiviazione di Azure, ma vengono crittografati solo dati appena scritti. Ciò significa che se si crea una macchina virtuale e quindi si abilita Crittografia del servizio di archiviazione nell'account di archiviazione che contiene il file VHD, verranno crittografate solo le modifiche, non il file VHD originale.
* Crittografia lato client: è il metodo più sicuro per crittografare i dati, perché li crittografa prima della trasmissione e crittografa i dati inattivi. Richiede tuttavia l'aggiunta di codice alle applicazioni tramite l'archiviazione, un'operazione che si potrebbe non voler eseguire. In questi casi, è possibile usare HTTPS per i dati in transito e Crittografia del servizio di archiviazione per crittografare i dati inattivi. La crittografia lato client comporta anche un carico maggiore sul client ed è necessario considerare questo aspetto nei piani di scalabilità, soprattutto se viene crittografata e trasferita una grande quantità di dati.

### <a name="protecting-customer-data-by-managing-secrets"></a>Protezione dei dati dei clienti tramite la gestione dei segreti
La gestione delle chiavi di protezione è fondamentale della protezione dei dati nel cloud. I clienti devono cercare di semplificare la gestione delle chiavi e mantenere il controllo delle chiavi usate dai servizi e dalle applicazioni cloud per crittografare i dati.

#### <a name="best-practices-for-managing-secrets"></a>Procedure consigliate per la gestione dei segreti
* Usare l'insieme di credenziali delle chiavi per ridurre al minimo il rischio che i segreti vengano esposti tramite file di configurazione codificati, script o nel codice sorgente. L'insieme di credenziali delle chiavi di Azure consente di crittografare le chiavi, ad esempio le chiavi di crittografia per Crittografia dischi di Azure, e i segreti, ad esempio le password, archiviandoli nei moduli di protezione hardware convalidati per FIPS 140-2 livello 2. Per una maggiore sicurezza, è possibile importare o generare le chiavi in questi moduli di protezione hardware.
* È opportuno che il codice dell'applicazione e i modelli contengano solo i riferimenti URI ai segreti, ovvero i segreti effettivi non sono presenti nei repository di codice sorgente, configurazione o codice. In tal modo si impediscono gli attacchi di phishing chiave nei repository interni o esterni, ad esempio harvest-bot in GitHub.
* Utilizzare controlli degli accessi in base al ruolo sicuri all'interno dell'insieme di credenziali delle chiavi. Se un operatore attendibile lascia la società o si trasferisce in un nuovo gruppo all'interno della società, è consigliabile impedirgli di accedere ai segreti.

Per altre informazioni, vedere la [documentazione pubblica sull'insieme di credenziali delle chiavi di Azure](../key-vault/index.md).

### <a name="isolation-to-restrict-data-access"></a>Isolamento per limitare l'accesso ai dati
L'isolamento riguarda l'uso di limiti, segmentazione e contenitori per limitare l'accesso ai dati solo a utenti, servizi e applicazioni autorizzati. Ad esempio, la separazione tra i tenant è un meccanismo di sicurezza essenziali per le piattaforme cloud multi-tenant, come Microsoft Azure. L'isolamento logico consente di evitare che un tenant interferisca con le operazioni di qualsiasi altro tenant.

#### <a name="environment-isolation"></a>Isolamento dell'ambiente
L'ambiente di Azure per enti pubblici è un'istanza separata dal resto della rete Microsoft. L'isolamento viene ottenuto attraverso una serie di controlli fisici e logici che includono:

* Protezione delle barriere fisiche tramite fotocamere e dispositivi biometrici.
* Uso di credenziali specifiche e dell'autenticazione a più fattori da parte del personale Microsoft che richiede l'accesso logico all'ambiente di produzione.
* Tutta l'infrastruttura dei servizi per Azure per enti pubblici si trova all'interno degli Stati Uniti.

#### <a name="per-customer-isolation"></a>Isolamento per ogni cliente
Azure implementa il controllo di accesso alla rete e la separazione tramite l'isolamento VLAN, gli ACL, i servizi di bilanciamento del carico e i filtri IP

I clienti possono isolare ulteriormente le proprie risorse tra sottoscrizioni, gruppi di risorse, reti virtuali e subnet.

## <a name="screening"></a>Screening
L'accreditamento FedRAMP di livello 4 di impatto elevato e DoD (Department of Defense) annunciato di recente ha aumentato il livello di sicurezza e conformità nell'ambiente di Azure per enti pubblici.

Microsoft sta ora esaminando tutti gli operatori in base al National Agency Check with Law and Credit (NACLC), secondo quanto definito nella sezione 5.6.2.2 del documento DoD Cloud Computing Security Requirements Guide (SRG):

> [!NOTE]
> L'indagine di fondo minima richiesta per il personale CSP che ha accesso a informazioni di livello 4 e 5 in base alla definizione "sensibili non critiche", ad esempio con posizioni di livello ADP-2 del Dipartimento della difesa statunitense, è un controllo National Agency Check with Law and Credit (NACLC) (per subappaltatori che gestiscono informazioni "sensibili non critiche") o un'indagine Moderate Risk Background Investigation (MBI) per la designazione di una posizione a "rischio moderato".
> 
> 

La tabella seguente contiene il riepilogo dell'attuale screening svolto da Microsoft per gli operatori di Azure per enti pubblici:

| Screening e controlli per Azure per enti pubblici | Description |
| --- | --- |
| Cittadinanza statunitense |Verifica della cittadinanza statunitense. |
| Controllo per il cloud Microsoft (ogni due anni) |Ricerca dei codici fiscali, controllo della fedina penale, elenco Office of Foreign Assets Control (OFAC), elenco Bureau of Industry and Security (BIS) ed elenco Office of Defense Trade Controls Debarred Persons. |
| National Agency Check with Law and Credit (NACLC) (ogni cinque anni) |Aggiunge un controllo delle impronte digitali rispetto ai database dell'FBI. Per altre informazioni, vedere il <a href="https://www.opm.gov/investigations/background-investigations/federal-investigations-notices/1997/fin97-02/">sito dell'Office Personnel Management</a>. |
| <a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/CJIS"> Criminal Justice Information Services (CJIS) </a> |CJIS è uno screening per enti pubblici nazionali, locali e dell'FBI che elabora i record delle impronte digitali e convalida le fedine penali del personale operativo che potrebbe avere accesso a informazioni di giustizia penale critiche.  Ogni stato svolge il proprio controllo di fondo e la successiva approvazione di tutti i dipendenti che potenzialmente accedono a informazioni di giustizia penale. |

Per il personale operativo di Azure, valgono i principi di accesso seguenti:

* I compiti sono chiaramente definiti, con responsabilità separate per richiesta, approvazione e distribuzione delle modifiche.
* L'accesso avviene tramite interfacce definite che hanno funzionalità specifiche.
* L'accesso è di tipo JIT (Just-In-Time) e viene concesso solo in base a ogni evento imprevisto o per un evento di manutenzione specifico e sempre per una durata limitata.
* L'accesso è basato su regole, con ruoli definiti a cui vengono assegnate solo le autorizzazioni necessarie per la risoluzione dei problemi.

Gli standard di screening includono la convalida della cittadinanza statunitense di tutto il personale operativo e del supporto tecnico di Microsoft prima di concedere l'accesso ai sistemi ospitati da Azure per enti pubblici. Il personale del supporto tecnico che deve trasferire dati usa funzionalità sicure all'interno di Azure per enti pubblici. Il trasferimento dati sicuro richiede un set separato di credenziali di autenticazione per ottenere l'accesso. Per accedere ai metadati di sistema, ad esempio, il personale operativo usa strumenti di gestione interni basati sul Web, API di sola lettura ed elevazione JIT.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni aggiuntive e aggiornamenti, sottoscrivere il <a href="https://blogs.msdn.microsoft.com/azuregov/">blog di Microsoft Azure per enti pubblici. </a>




<!--HONumber=Nov16_HO3-->


