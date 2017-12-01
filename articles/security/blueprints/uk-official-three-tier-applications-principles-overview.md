---
title: Automazione dei progetti di Azure - Panoramica dei principi di sicurezza cloud del National Cyber Security Centre
description: Automazione dei progetti di Azure - Panoramica dei principi di sicurezza cloud del National Cyber Security Centre
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 49652fd9-b8c6-4a88-bc5e-0b58a0260ed6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2017
ms.author: jomolesk
ms.openlocfilehash: 0be18e2c2354ea8f766eb48db793c906e565a201
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2017
---
# <a name="national-cyber-security-centre-cloud-security-principles-overview"></a>Panoramica dei principi di sicurezza cloud del National Cyber Security Centre


> [!NOTE]
> Questi principi di sicurezza vengono definiti dal National Cyber Security Centre del Regno Unito. Fare riferimento alla [documentazione di NCSC](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) per informazioni sulle procedure e indicazioni di test per ogni principio di sicurezza.



## <a name="ncsc-cloud-security-principle-1"></a>Principio di sicurezza cloud NCSC 1
### <a name="data-in-transit-protection"></a>Protezione dei dati in transito
I dati degli utenti in transito sulle reti devono essere adeguatamente protetti da possibili manomissioni o intercettazioni.

Questo obiettivo deve essere ottenuto tramite una combinazione di:

- protezione di rete, impedendo all'autore dell'attacco di intercettare i dati
- crittografia, impedendo all'autore dell'attacco di leggere i dati


**Responsabilità:** `Shared`


|||
|---|---|
| **Cliente** | Questo progetto di Azure configura le risorse in modo che comunichino solo tramite protocolli protetti. Il componente WAF del gateway applicazione è configurato in modo da accettare comunicazioni da usi esterni attraverso HTTPS/TLS e comunicare con il pool di back-end solo tramite HTTPS/TLS. I servizi Desktop remoto sono configurati per l'uso di connessioni protette. Per proteggere il traffico Web tra AppGateway e Azure viene usata una rete VPN. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Azure usa il protocollo TLS (Transport Layer Security) 1.2 standard con chiavi di crittografia RSA/SHA256 a 2048 bit, come consigliato da CESG/NCSC, per crittografare le comunicazioni sia tra il cliente e il cloud sia internamente tra data center e sistemi Azure. Quando gli amministratori usano il portale di Microsoft Azure per gestire il servizio per l'organizzazione, ad esempio, i dati trasmessi tra il portale e il dispositivo dell'amministratore vengono inviati tramite un canale TLS crittografato. Quando un utente di posta elettronica si connette a Outlook.com usando un Web browser standard, la connessione HTTPS fornisce un canale sicuro per la ricezione e l'invio di messaggi di posta elettronica.<br /> <br /> Azure offre ai clienti un'ampia gamma di opzioni per la protezione del traffico e dei dati personali. Le funzionalità di gestione dei certificati integrate in Azure offrono la flessibilità necessaria per poter configurare certificati e chiavi di crittografia per sistemi di gestione, singoli servizi, sessioni shell (SSH) protette, connessioni VPN (Virtual Private Network), connessioni Desktop remoto e altre funzioni. <br /><br /> Gli sviluppatori possono usare i provider di servizi di crittografia (CSP) incorporati in Microsoft .NET Framework per accedere ad algoritmi AES (Advanced Encryption Standard) e alla funzionalità SHA-2 (Secure Hash Algorithm) per gestire operazioni come la convalida di firme digitali. Azure Key Vault consente ai clienti di proteggere segreti e chiavi crittografiche archiviandole in moduli di protezione hardware (HSM). |


 ## <a name="ncsc-cloud-security-principle-2"></a>Principio di sicurezza cloud NCSC 2
### <a name="asset-protection-and-resilience"></a>Resilienza e protezione di asset
I dati degli utenti, nonché gli asset che li contengono o li elaborano, devono essere protetti da rischi di manomissione fisica, perdita, danneggiamento o requisizione.

Gli aspetti da considerare sono:

1. Posizione fisica e giurisdizione legale
2. Sicurezza dei data center
3. Protezione dei dati inattivi
4. Pulizia dei dati
5. Smaltimento delle apparecchiature
6. Disponibilità e resilienza fisica


 ## <a name="ncsc-cloud-security-principle-21"></a>Principio di sicurezza cloud NCSC 2.1
### <a name="physical-location-and-legal-jurisdiction"></a>Posizione fisica e giurisdizione legale
Per comprendere le condizioni legali in cui è possibile accedere ai dati anche senza il consenso dell'utente, è necessario identificare le posizioni in cui i dati vengono archiviati, elaborati e gestiti.
È inoltre necessario capire come vengono applicati i controlli di gestione dei dati nel servizio rispetto alla normativa vigente nel Regno Unito. Una protezione inadeguata dei dati degli utenti, infatti, può comportare sanzioni giuridiche o amministrative o un grave danno alla reputazione.


**Responsabilità:** `Customer`

> [!NOTE]
> I servizi di Azure vengono distribuiti a livello di area e i clienti possono configurare determinati servizi di Azure in modo che archivino i dati dei clienti solo in un'area specifica. Microsoft Azure fornisce un elenco dei data center disponibili globalmente per fornire elevati livelli di disponibilità e affidabilità su scala globale. Tutti i data center di Azure hanno ottenuto la certificazione ISO/IEC 27001:2013. L'area geografica Regno Unito è costituita da 2 aree: Regno Unito meridionale e Regno Unito occidentale.

|||
|---|---|
| **Cliente** | Questo progetto di Azure chiede all'amministratore in quale area geografica vuole distribuire le risorse di Azure. Le aree consigliate per la distribuzione sono Regno Unito meridionale e Regno Unito occidentale. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Non applicabile |


 ## <a name="ncsc-cloud-security-principle-22"></a>Principio di sicurezza cloud NCSC 2.2
### <a name="datacentre-security"></a>Sicurezza dei data center
Nei luoghi usati per fornire i servizi cloud è necessario garantire la protezione fisica da accessi non autorizzati, manomissioni, furti o riconfigurazioni dei sistemi. Una protezione non adeguata può comportare infatti la divulgazione, la modifica o la perdita di dati.


**Responsabilità:** `Microsoft Azure`


|||
|---|---|
| **Cliente** | I clienti non hanno accesso fisico ad alcuna risorsa di sistema nei data center di Azure; le misure di protezione per la sicurezza dei data center vengono implementate e gestite da Microsoft Azure. Questo principio viene ereditato da Microsoft Azure. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure implementa questo principio per conto dei clienti. Microsoft Azure viene eseguito in strutture Microsoft distribuite geograficamente e condivide spazi e utilità con altri servizi online Microsoft. Ogni struttura è progettata per essere operativa 24 ore su 24, 7 giorni su 7 e adotta varie misure standard per proteggere le operazioni da interruzioni dell'alimentazione o di rete e intrusioni fisiche. Questi data center sono conformi agli standard del settore (ad esempio ISO 27001) per la sicurezza fisica e la disponibilità. Vengono gestiti, monitorati e amministrati da personale operativo Microsoft. <br /> <br /> I clienti di Azure hanno quindi la garanzia che i controlli di sicurezza fisica vengano eseguiti su tutti i data center di Azure grazie alla certificazione ottenuta dai data center per lo standard ISO/IEC 27001:2013. L'area geografica Regno Unito è costituita da 2 aree: Regno Unito meridionale e Regno Unito occidentale. |


 ## <a name="ncsc-cloud-security-principle-23"></a>Principio di sicurezza cloud NCSC 2.3
### <a name="data-at-rest-protection"></a>Protezione dei dati inattivi
Per garantire che i dati non siano resi disponibili a parti non autorizzate con accesso fisico all'infrastruttura, i dati degli utenti contenuti nel servizio devono essere protetti indipendentemente dal supporto di archiviazione in cui si trovano. Senza l'applicazione di misure appropriate, è possibile che i dati vengano inavvertitamente diffusi su supporti scartati, smarriti o rubati.


**Responsabilità:** `Shared`

> [!NOTE]
> Tutte le soluzioni di crittografia che Microsoft Azure offre ai clienti si integrano facilmente con Azure Key Vault, che consente una gestione semplificata delle chiavi di crittografia.

|||
|---|---|
| **Cliente** | La riservatezza e l'integrità di tutti i file di archiviazione BLOB distribuiti da questo progetto di Azure sono protetti tramite l'uso della crittografia del servizio Archiviazione di Azure (SSE, Storage Service Encryption). SSE protegge i dati inattivi all'interno degli account di archiviazione di Azure usando la crittografia AES a 256 bit. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Azure offre un'ampia gamma di funzionalità di crittografia, fornendo ai clienti la flessibilità di scegliere la soluzione che meglio rispondono alle loro esigenze. Azure Key Vault consente ai clienti di mantenere in modo più semplice e conveniente il controllo sulle chiavi usate dalle applicazioni e dai servizi cloud per crittografare i dati. Crittografia dischi di Azure permette inoltre ai clienti di crittografare le macchine virtuali, mentre la crittografia del servizio Archiviazione di Azure consente di crittografare tutti i dati inseriti nell'account di archiviazione di un cliente. |


 ## <a name="ncsc-cloud-security-principle-24"></a>Principio di sicurezza cloud NCSC 2.4
### <a name="data-sanitisation"></a>Pulizia dei dati
Il processo di provisioning, migrazione e deprovisioning delle risorse non dovrebbe concludersi con un accesso non autorizzato ai dati dell'utente.

Una pulizia dei dati non adeguata può determinare le conseguenze seguenti:

- I dati degli utenti vengono mantenuti dal provider di servizi per un periodo illimitato
- I dati degli utenti risultano accessibili ad altri utenti del servizio nel momento in cui vengono riusate le risorse
- I dati degli utenti vengono smarriti o diffusi in supporti scartati, smarriti o rubati.


**Responsabilità:** `Microsoft Azure`


|||
|---|---|
| **Cliente** | Microsoft Azure offre garanzie contrattuali in merito all'eliminazione permanente dei dati in Azure. Questo principio viene quindi ereditato da Microsoft Azure. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure segue il processo di eliminazione NIST SP800-88r1 con la classificazione dei dati allineata a FIPS 199 Moderate. Per le unità che lo supportano, NIST offre un approccio di tipo "cancellazione sicura" (tramite il firmware dell'unità disco rigido). Per i dischi rigidi che non possono essere cancellati, Microsoft li distrugge e rende impossibile il recupero delle informazioni che contenevano (ad esempio, li disintegra, li sminuzza, li polverizza o li brucia). Il metodo di smaltimento più appropriato dipende dal tipo di asset. Vengono comunque mantenuta la documentazione relativa alla distruzione. Tutti i servizi di Microsoft Azure usano servizi approvati per l'archiviazione dei contenuti multimediali e la gestione dello smaltimento. <br />  <br /> Alla scadenza o al termine di una sottoscrizione a un servizio, il cliente può contattare Microsoft e indicare se: <br /><br /> (1) Disattivare l'account del cliente e quindi eliminare i dati del cliente; oppure <br /> (2) Mantenere i dati archiviati nel servizio online in un account con funzioni limitate per almeno 90 giorni dopo la scadenza o la chiusura della sottoscrizione del cliente ("periodo di conservazione"), in modo che il cliente possa ancora estrarre i dati. Alla scadenza del periodo di conservazione, Microsoft disabiliterà l'account del cliente ed eliminerà tutti i dati. Eventuali copie memorizzate nella cache o di backup verranno eliminate entro 30 giorni dalla fine del periodo di conservazione. |


 ## <a name="ncsc-cloud-security-principle-25"></a>Principio di sicurezza cloud NCSC 2.5
### <a name="equipment-disposal"></a>Smaltimento delle apparecchiature
Quando le apparecchiature usate per fornire un servizio raggiungono la fine della loro vita utile, devono essere eliminate in modo che non compromettano la sicurezza del servizio o i dati degli utenti archiviati nel servizio.


**Responsabilità:** `Microsoft Azure`


|||
|---|---|
| **Cliente** | I clienti non hanno accesso fisico ad alcuna risorsa di sistema nei data center di Azure; le procedure di smaltimento delle apparecchiature vengono implementate e gestite da Microsoft Azure. Questo principio viene ereditato da Microsoft Azure. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure implementa questo principio per conto dei clienti. Al termine del ciclo di vita di un sistema, il personale operativo Microsoft segue rigorosi processi di gestione dei dati e di eliminazione dell'hardware per garantire che nessun componente hardware in cui possano essere contenuti dati dei clienti sia reso disponibile a parti non fidate. Microsoft Azure segue il processo di eliminazione NIST SP800-88r1 con la classificazione dei dati allineata a FIPS 199 Moderate. Per le unità che lo supportano, NIST offre un approccio di tipo "cancellazione sicura" (tramite il firmware dell'unità disco rigido). Per i dischi rigidi che non possono essere cancellati, Microsoft li distrugge e rende impossibile il recupero delle informazioni che contenevano (ad esempio, li disintegra, li sminuzza, li polverizza o li brucia). Il metodo di smaltimento più appropriato dipende dal tipo di asset. Vengono comunque mantenuta la documentazione relativa alla distruzione. Tutti i servizi di Microsoft Azure usano servizi approvati per l'archiviazione dei contenuti multimediali e la gestione dello smaltimento. |


 ## <a name="ncsc-cloud-security-principle-26"></a>Principio di sicurezza cloud NCSC 2.6
### <a name="physical-resilience-and-availability"></a>Disponibilità e resilienza fisica
I servizi hanno vari livelli di resilienza da cui dipende la loro capacità di funzionare correttamente in caso di errori, attacchi o eventi imprevisti. Un servizio senza garanzie di disponibilità può diventare non disponibile, potenzialmente per periodi prolungati, indipendentemente dall'impatto prodotto sull'azienda.


**Responsabilità:** `Shared`

> [!NOTE]
> Se il cliente configura Microsoft Azure in modo appropriato abilitando Azure Site Recovery e servizi di archiviazione dati alternativi in data center posizionati in altre aree geografiche, Microsoft Azure può supportare la continuità operativa delle risorse distribuite dal cliente.

|||
|---|---|
| **Cliente** | Il cliente è responsabile della definizione di un sito di archiviazione alternativo. L'istruzione di implementazione del controllo del cliente deve prevedere la possibilità per il cliente di mantenere l'operatività anche in caso di evento imprevisto. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Per garantire disponibilità e resilienza, Microsoft Azure dispone di data center approvati dal National Cyber Security Centre (NCSC) del Regno Unito in varie località geografiche (Regno Unito meridionale e Regno Unito occidentale). Sarà responsabilità del cliente riservare capacità in un'area alternativa usando il servizio Azure Site Recovery. Al termine della configurazione di Azure Site Recovery, Azure avvierà e interromperà i servizi del cliente in un processo di transizione senza soluzione di continuità al sito di elaborazione alternativo. |


 ## <a name="ncsc-cloud-security-principle-3"></a>Principio di sicurezza cloud NCSC 3
### <a name="separation-between-users"></a>Separazione tra gli utenti
Un utente del servizio malintenzionato o compromesso non deve poter influire sul servizio o sui dati di un altro utente.

Sulla separazione degli utenti incidono i fattori seguenti:

- il luogo in cui vengono implementati i controlli di separazione, che dipende in larga misura dal modello di servizio (ad esempio IaaS, PaaS, SaaS)
- l'utente con cui si condivide il servizio, che dipende dal modello di distribuzione (ad esempio cloud pubblico, privato o community)
- il livello di garanzia disponibile nell'implementazione dei controlli di separazione.

> [!NOTE]
> In un servizio IaaS è opportuno tenere presente la separazione fornita dai componenti di calcolo, di archiviazione e di rete. I servizi SaaS e PaaS basati su IaaS possono ereditare alcune delle proprietà di separazione dell'infrastruttura IaaS sottostante.

**Responsabilità:** `Microsoft Azure`


|||
|---|---|
| **Cliente** | Microsoft Azure garantisce l'isolamento di ogni utente per evitare che un utente malintenzionato o compromesso influisca sul servizio o sui dati di un altro utente. Questo principio viene quindi ereditato da Microsoft Azure. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Poiché i server cloud dei clienti sono virtuali, il paradigma della separazione fisica non è più valido. Microsoft Azure è stato progettato per identificare e contrastare i rischi inerenti in un ambiente multi-tenant. Il carico di lavoro di elaborazione e archiviazione dei dati è logicamente separato tra gli utenti di Azure tramite Active Directory e funzionalità appositamente sviluppate per i servizi multi-tenant. In questo modo, si garantisce che i dati degli utenti archiviati in data center di Azure condivisi non siano accessibili da altre organizzazioni. <br /> <br /> Fondamentale in ogni architettura cloud condivisa è l'isolamento degli utenti, con cui si evita che un utente malintenzionato o compromesso influisca sul servizio o sui dati di un altro utente. <br /> <br /> Per altre informazioni sulla separazione dei tenant Microsoft, vedere la descrizione completa in [Progetto di Azure - Principi di sicurezza cloud NCSC - Matrice di responsabilità dei clienti](https://aka.ms/blueprintuk-gcrm). |


 ## <a name="ncsc-cloud-security-principle-4"></a>Principio di sicurezza cloud NCSC 4
### <a name="governance-framework"></a>Struttura di governance
Il provider del servizio deve avere una struttura di governance della sicurezza in grado di coordinare e indirizzare la gestione del servizio e le informazioni in esso contenute. Qualsiasi controllo tecnico distribuito al di fuori di questa struttura non ha pressoché alcun valore.
La disponibilità di una struttura di governance efficace garantisce infatti che i controlli fisici, tecnici, sulle procedure e sul personale continuino a essere svolti per l'intera durata del servizio. La struttura deve anche adattarsi a eventuali cambiamenti nel servizio, agli sviluppi tecnologici e alla comparsa di nuove minacce.


**Responsabilità:** `Microsoft Azure`


|||
|---|---|
| **Cliente** | Microsoft Azure mantiene una struttura della governance di sicurezza documentata per i servizi di Azure. Questo principio viene quindi ereditato da Microsoft Azure. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | La struttura di conformità Microsoft include una metodologia standard per la definizione dei domini di conformità, con cui si determina quali obiettivi vengono applicati a un determinato team o asset e si acquisisce il modo in cui vengono ottenuti con sufficiente dettaglio gli obiettivi di controllo dei domini nel momento in cui vengono applicati a un set specifico di standard di settore, normative o requisiti aziendali. La struttura associa i controlli a più standard normativi, offrendo a Microsoft la possibilità di progettare e compilare i servizi usando un set comune di controlli e semplificando in questo modo la conformità a un'ampia gamma di normative, così come sono adesso e come si evolveranno nel futuro. <br /> <br /> I processi di conformità Microsoft consentono inoltre ai clienti di raggiungere più facilmente la conformità su più servizi e di soddisfare con efficacia le proprie esigenze in continua evoluzione. Le tecnologie di ottimizzazione della sicurezza, combinate con efficienti processi di conformità, consentono a Microsoft di mantenere ed espandere un'ampia gamma di certificazioni di terze parti, con cui i clienti possono dimostrare l'acquisizione della conformità ai rispettivi clienti, revisori e autorità di controllo. <br /> <br />  Azure è conforme a una vasta gamma di standard di conformità internazionali, regionali e specifici del settore, ad esempio ISO 27001, FedRAMP, SOC 1 e SOC 2. La conformità ai rigidi controlli di sicurezza previsti da questi standard viene verificata da rigorosi controlli di terze parti che dimostrano come i servizi di Azure usino e soddisfino standard del settore, certificazioni, attestazioni e autorizzazioni. <br /> <br /> Azure è stato progettato con una strategia di conformità che consente ai clienti di soddisfare non solo obiettivi aziendali, ma anche normative e standard del settore. La struttura di conformità della sicurezza include fasi di test e controllo, analisi della sicurezza, best practice di gestione dei rischi e analisi dei benchmark di sicurezza per conseguire certificati e attestazioni. <br /> <br /> Per altre informazioni sull'aderenza di Microsoft alle strutture di conformità, vedere la descrizione completa in [Progetto di Azure - Principi di sicurezza cloud NCSC - Matrice di responsabilità dei clienti](https://aka.ms/blueprintuk-gcrm).|


 ## <a name="ncsc-cloud-security-principle-5"></a>Principio di sicurezza cloud NCSC 5
### <a name="operational-security"></a>Sicurezza operativa
Il servizio deve essere eseguito e gestito in modo sicuro per impedire, rilevare o prevenire attacchi. Per ottenere una buona sicurezza operativa non sono necessari processi particolarmente lunghi, costosi, complessi o burocratici.

In questo caso, è necessario considerare i quattro elementi seguenti.

- Gestione della configurazione e delle modifiche: è necessario verificare che le modifiche al sistema siano state correttamente testate e autorizzate. Le modifiche non devono alterare in modo imprevisto le proprietà di sicurezza.
- Gestione delle vulnerabilità: è necessario identificare e attenuare le problematiche di sicurezza nei componenti costitutivi.
- Monitoraggio protettivo: è necessario mettere in atto misure in grado di rilevare eventuali attacchi e attività non autorizzate nel servizio.
- Gestione di eventi imprevisti: verificare l'effettiva possibilità di rispondere a eventi imprevisti e di ripristinare un servizio protetto e disponibile.




 ## <a name="ncsc-cloud-security-principle-51"></a>Principio di sicurezza cloud NCSC 5.1
### <a name="configuration-and-change-management"></a>Gestione della configurazione e delle modifiche
È necessario avere un quadro preciso degli asset che costituiscono il servizio, con le rispettive configurazioni e dipendenze.
Le modifiche che potrebbero compromettere la sicurezza del servizio, quindi, devono essere opportunamente individuate e gestite. Devono essere rilevate anche tutte le modifiche non autorizzate.
Se una modifica non viene gestita in modo efficace, eventuali vulnerabilità di sicurezza possono essere involontariamente introdotte in un servizio. E anche in presenza di una vulnerabilità nota, è possibile che non venga interamente contrastata.


**Responsabilità:** `Shared`


|||
|---|---|
| **Cliente** | I modelli di Azure Resource Manager e le relative risorse che compongono questo progetto di Azure rappresentano una baseline di tipo "configurazione come codice" per l'architettura distribuita. La soluzione viene fornita tramite GitHub, che può essere usato anche per il controllo della configurazione. <br /> <br /> I privilegi degli account di Azure Active Directory vengono implementati mediante il controllo degli accessi in base al ruolo, ovvero assegnando utenti a ruoli specifici e monitorando quindi da vicino gli utenti che possono visualizzare e controllare le risorse distribuite. I privilegi degli account di Active Directory vengono implementati mediante il controllo degli accessi in base al ruolo, ovvero assegnando utenti a gruppi di sicurezza. Questi gruppi controllano le azioni che gli utenti possono eseguire rispetto alla configurazione del sistema operativo. Questi schemi basati sui ruoli possono essere estesi dal cliente per soddisfare le esigenze correlate agli obiettivi. <br /> <br /> Per essere conformi a questo principio, sono necessarie configurazioni aggiuntive da usare nell'ambiente di produzione. Queste configurazioni dovranno quindi far parte del processo di gestione delle modifiche del cliente. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure esamina e aggiorna annualmente le impostazioni di configurazione e le configurazioni di base di dispositivi hardware, software e di rete. Prima di essere inserite nell'ambiente di produzione, le modifiche vengono sviluppate, testate e approvate in un ambiente di sviluppo e/o test. <br /> <br />Microsoft Azure applica le configurazioni di base usando il processo di modifica e rilascio per i componenti software di Microsoft Azure (ad esempio, sistema operativo, infrastruttura, RDFE, XStore e così via) e il processo di configurazione del bootstrap per i componenti di dispositivi hardware e di rete che devono essere introdotti nell'ambiente di produzione di Microsoft Azure, come descritto di seguito. <br /> <br /> Le configurazioni di base richieste per i servizi basati su Azure vengono esaminate dal team di Azure responsabile della sicurezza e della conformità e dai team specifici dei servizi nell'ambito delle attività di test eseguite prima della distribuzione del servizio di produzione. |


 ## <a name="ncsc-cloud-security-principle-52"></a>Principio di sicurezza cloud NCSC 5.2
### <a name="vulnerability-management"></a>Gestione delle vulnerabilità
I provider di servizi devono disporre di un processo di gestione in grado di identificare, valutare e attenuare le vulnerabilità. I servizi per i quali non è previsto un processo di questo tipo diventano rapidamente vulnerabili ad attacchi perpetrati usando strumenti e metodi noti pubblicamente.


**Responsabilità:** `Customer`


|||
|---|---|
| **Cliente** | Il cliente è responsabile di eseguire l'analisi delle vulnerabilità sulle risorse distribuite dal cliente stesso (inclusi sistemi operativi, database, applicazioni e software). La dichiarazione di implementazione del cliente deve indicare gli strumenti usati per eseguire l'analisi delle vulnerabilità. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | La gestione degli aggiornamenti di sicurezza consente di proteggere i sistemi da vulnerabilità note. Azure usa sistemi di distribuzione integrati per gestire la distribuzione e l'installazione degli aggiornamenti di sicurezza per il software Microsoft. Azure può anche attingere alle risorse del Microsoft Security Response centro (MSRC), che identifica, monitorizza, contrasta e risolve problemi di sicurezza e vulnerabilità cloud in qualsiasi momento, ogni giorno dell'anno. |


 ## <a name="ncsc-cloud-security-principle-53"></a>Principio di sicurezza cloud NCSC 5.3
### <a name="protective-monitoring"></a>Monitoraggio protettivo
Un servizio che non monitorizza in modo efficiente attacchi, usi impropri e malfunzionamenti difficilmente sarà in grado di rilevare un attacco (che abbia esito positivo o negativo). Analogamente, non sarà in grado di rispondere tempestivamente a potenziali danneggiamenti di ambienti e dati.


**Responsabilità:** `Customer`


|||
|---|---|
| **Cliente** | Il cliente è responsabile di eseguire il monitoraggio delle risorse distribuite dal cliente stesso (inclusi sistemi operativi, database, applicazioni e software). L'istruzione di implementazione del controllo del cliente deve prevedere i meccanismi da usare per monitorare, rilevare e rispondere ad attacchi, usi impropri e malfunzionamenti. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | La sicurezza in Microsoft Azure prevede requisiti specifici per il monitoraggio attivo. I team dei servizi configurano gli strumenti di monitoraggio attivo sulla base di questi requisiti. Gli strumenti di monitoraggio attivo includono Agente di monitoraggio (MA) e System Centre Operations Manager (SCOM), configurati per inviare avvisi in tempo reale al personale della sicurezza di Microsoft Azure in situazioni in cui è richiesto un intervento immediato. |


 ## <a name="ncsc-cloud-security-principle-54"></a>Principio di sicurezza cloud NCSC 5.4
### <a name="incident-management"></a>Gestione dell'evento imprevisto
A meno che non siano in atto processi di gestione degli eventi imprevisti accuratamente pianificati, è probabile che non vengano prese decisioni adeguate quando si verificano eventi imprevisti, accrescendo potenzialmente l'impatto complessivo sugli utenti.
Questi processi non devono essere complessi o richiedere descrizioni particolarmente dettagliate, ma una corretta gestione degli eventi imprevisti contribuisce a ridurre al minimo l'impatto prodotto sugli utenti da problemi ambientali o di sicurezza e affidabilità di un servizio.


**Responsabilità:** `Shared`

> [!NOTE]
> Nel caso in cui un evento imprevisto di sicurezza di un cliente possa compromettere lo stato di sicurezza di Microsoft Azure, il cliente è tenuto a comunicarlo a Microsoft Azure.

|||
|---|---|
| **Cliente** | Il cliente è responsabile di definire un processo di gestione degli eventi imprevisti per le risorse distribuite dal cliente stesso (inclusi sistemi operativi, database, applicazioni e software). L'istruzione di implementazione del cliente deve prevedere le modalità da usare per segnalare eventi imprevisti e avvisi, supportare reazioni tempestive agli eventi imprevisti e inoltrare le informazioni appropriate a PGA e ad altre organizzazioni HMG. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft ha implementato un processo di gestione degli eventi imprevisti di sicurezza per facilitare una risposta coordinata in caso di eventi imprevisti. <br /> <br /> Se Microsoft viene a conoscenza di accessi non autorizzati a dati di clienti archiviati all'interno di proprie apparecchiature o strutture o di accessi non autorizzati a tali apparecchiature o strutture con conseguente perdita, divulgazione o alterazione dei dati di clienti, Microsoft ha dichiarato che: <br /> <br />   - Informerà tempestivamente il cliente dell'evento imprevisto di sicurezza; <br /> - Esaminerà tempestivamente l'evento imprevisto di sicurezza e fornirà al cliente informazioni dettagliate sull'evento; <br /> - Prenderà misure ragionevoli e immediate per limitare gli effetti e ridurre al minimo i danni derivanti dall'evento imprevisto di sicurezza.  <br />  <br /> È stata definita una struttura di gestione degli eventi imprevisti con un'attenta definizione dei ruoli e assegnazione delle responsabilità. Il team che si occupa della gestione di eventi imprevisti di sicurezza di Windows Azure è responsabile della gestione degli eventi imprevisti di sicurezza, nonché delle procedure di escalation e dell'eventuale coinvolgimento di team specializzati. Gli Azure Operations Manager sono responsabili invece di sovrintendere all'analisi e alla risoluzione degli eventi imprevisti di sicurezza e di privacy con il supporto di altre funzioni aziendali. <br /> <br /> Per altre informazioni sui processi di risposta a eventi imprevisti di Microsoft, vedere la descrizione completa in [Progetto di Azure - Principi di sicurezza cloud NCSC - Matrice di responsabilità dei clienti](https://aka.ms/blueprintuk-gcrm). |


 ## <a name="ncsc-cloud-security-principle-6"></a>Principio di sicurezza cloud NCSC 6
### <a name="personnel-security"></a>Sicurezza del personale
Se il personale del provider di servizi ha accesso ai propri dati e sistemi, è necessario un elevato livello di fiducia nella loro attendibilità. Indagini approfondite, supportate da un adeguato percorso di formazione, riducono il rischio di danni accidentali o intenzionali da parte del personale del provider di servizi.
Il provider di servizi dovrebbe quindi sottoporre il personale a indagini di sicurezza e periodici corsi di formazione sulla sicurezza. Il personale che opera in questi ruoli deve inoltre comprendere le proprie responsabilità. I provider, pertanto, devono chiarire le modalità con cui effettuano le indagini e gestiscono il personale che occupa ruoli privilegiati.


**Responsabilità:** `Shared`


|||
|---|---|
| **Cliente** | Il cliente è responsabile di effettuare indagini e fornire regolarmente percorsi di formazione sulla sicurezza al personale che ha accesso alle risorse distribuite dal cliente stesso. La dichiarazione di implementazione del cliente deve prevedere i criteri di indagine per i vari ruoli e la frequenza delle attività di formazione sulla sicurezza. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Il personale di Microsoft Azure che gestisce i servizi di Azure e fornisce supporto tecnico ai clienti (o ai subappaltatori di Microsoft che si occupano di operazioni sulla piattaforma, risoluzione dei problemi e supporto tecnico) viene sottoposto a indagini svolte da Microsoft (o organismo equivalente) sulle relative esperienze formative e professionali e su eventuali precedenti penali. Queste valutazioni sono conformi ai requisiti della direttiva BPSS/BS7858 del Regno Unito e non includono in modo specifico un controllo di identità formale.  <br /> <br /> Microsoft include sempre disposizioni di non divulgazione nei contratti con dipendenti e subappaltatori. Tutti i dipendenti e i subappaltatori Microsoft interessati prendono parte a un programma di formazione sulla sicurezza sponsorizzato da Microsoft Azure, che li informa sulle responsabilità che hanno in fatto di sicurezza delle informazioni. <br /> <br /> Eventuali membri del personale o subappaltatori che si occupano di servizi di Microsoft Azure sospettati di violare la sicurezza e/o i criteri di sicurezza delle informazioni sono soggetti a un processo investigativo e ad appropriate azioni disciplinari che possono concludersi anche con la risoluzione del rapporto di lavoro. Se le circostanze lo impongono, Microsoft può coinvolgere le forze dell'ordine per un'indagine formale. <br /> <br /> A integrazione del sistema di controlli sulle esperienze passate e sulla formazione in fatto di sicurezza, Microsoft distribuisce combinazioni di controlli preventivi, difensivi e reattivi per proteggersi da eventuali attività di sviluppo e amministrative non autorizzate. Sono previsti, ad esempio, i meccanismi seguenti: <br />  <br /> - Severi controlli sugli accessi ai dati sensibili, tra cui la necessità di eseguire l'autenticazione a due fattori basata su smart card per effettuare operazioni sensibili. <br /> - Combinazioni di controlli che potenziano il rilevamento indipendente di attività dannose. <br /> - Più livelli di monitoraggio, registrazione e creazione di report. |


 ## <a name="ncsc-cloud-security-principle-7"></a>Principio di sicurezza cloud NCSC 7
### <a name="secure-development"></a>Sviluppo sicuro
Devono essere designati e sviluppati servizi in grado di identificare e contrastare le minacce alla sicurezza. I servizi privi di queste caratteristiche possono essere vulnerabili a violazioni di sicurezza che potrebbero compromettere i dati, determinare la perdita del servizio o favorire altre attività dannose.


**Responsabilità:** `Shared`


|||
|---|---|
| **Cliente** | Le macchine virtuali distribuite da questo progetto di Azure eseguono sistemi operativi Windows. Windows fornisce servizi di convalida dell'integrità dei file in tempo reale, di protezione e di ripristino dei file di sistema di base, installati nell'ambito di Windows o di aggiornamenti di sistema di Windows autorizzati, tramite la funzionalità Protezione risorse di Windows, che consente il controllo dell'integrità in tempo reale. <br /> <br /> Windows ha messo a punto le seguenti funzionalità di protezione per impedire l'esecuzione di codice in posizioni con capacità di memoria limitata: NX (No Execute), ASLR (Address Space Layout Randomization) e Protezione esecuzione programmi. <br /> <br /> Questo progetto di Azure distribuisce funzionalità di protezione antimalware basate su host per tutte le macchine virtuali Windows distribuite, implementate tramite Microsoft Windows Defender. Windows Defender è configurato in modo da aggiornare automaticamente sia il motore antimalware sia le firme di protezione nel momento in cui vengono rilasciate nuove versioni. <br /> <br /> Per essere conformi a questo principio, sono necessarie configurazioni aggiuntive da usare nell'ambiente di produzione. Queste configurazioni dovranno quindi far parte del processo di sviluppo sicuro del cliente. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Security Development Lifecycle (SDL) fornisce un efficiente processo di modellazione delle minacce in grado di identificare minacce e vulnerabilità in software e servizi. La modellazione delle minacce è un esercizio di squadra che coinvolge l'Operations Manager, i program/project manager, gli sviluppatori e i tester e rappresenta un'attività di analisi della sicurezza di fondamentale importanza per la progettazione della soluzione. I membri del team usano SDL Threat Modelling Tool per modellare tutti i servizi e i progetti, sia quando vengono realizzati sia quando vengono aggiornati con nuove caratteristiche e funzionalità. I modelli di minacce coprono tutto il codice esposto sulla superficie di attacco e tutto il codice scritto o concesso in licenza da terze parti e prende in considerazione tutti i limiti di trust. Viene usato il sistema STRIDE (Spoofing, Tampering, Repudiation, Information disclosure, Denial of service, Elevation of privilege) per identificare e risolvere le minacce alla sicurezza nelle fasi iniziali del processo di progettazione, prima che possano raggiungere i clienti. |


 ## <a name="ncsc-cloud-security-principle-8"></a>Principio di sicurezza cloud NCSC 8
### <a name="supply-chain-security"></a>Sicurezza della supply chain
Il provider di servizi deve garantire che la supply chain supporti correttamente tutti i principi di sicurezza che il servizio dichiara di implementare.
I servizi cloud si basano spesso su servizi e prodotti di terze parti. Se questo principio non viene messo in pratica, quindi, un'eventuale violazione della supply chain può compromettere la sicurezza del servizio e l'implementazione di altri principi di sicurezza.


**Responsabilità:** `Shared`


|||
|---|---|
| **Cliente** | Il cliente è responsabile di fornire documentazione sulla sicurezza della supply chain per qualsiasi prodotto software acquistato da terze parti e sistema operativo usato nella sottoscrizione di Azure. La dichiarazione di implementazione del cliente deve prevedere l'eccezione in virtù della quale seguire i processi identificati dalla documentazione della supply chain. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Il gruppo Microsoft Cloud Supply Chain (MCSC) è costituito da sei team univoci che contribuiscono alla protezione di Azure da minacce alla supply chain.  <br />  <br /> - Approvvigionamento <br /> - Operazioni dei clienti <br /> - Qualità di distribuzione <br /> - Gestione delle relazioni con i fornitori <br /> - Parti di ricambio <br />  <br /> Per altre informazioni sul gruppo MCSC di Microsoft, vedere la descrizione completa in [Progetto di Azure - Principi di sicurezza cloud NCSC - Matrice di responsabilità dei clienti](https://aka.ms/blueprintuk-gcrm). |


 ## <a name="ncsc-cloud-security-principle-9"></a>Principio di sicurezza cloud NCSC 9
### <a name="secure-user-management"></a>Gestione sicura degli utenti
Il provider deve mettere a disposizione dei clienti gli strumenti necessari per gestire l'uso del servizio in totale sicurezza. Procedure e interfacce di gestione costituiscono una parte essenziale della barriera di sicurezza, impedendo accessi non autorizzati e la possibilità di modificare risorse, applicazioni e dati.

Gli aspetti da considerare sono:

- Autenticazione degli utenti per l'uso delle interfacce di gestione e dei canali di supporto
- Separazione e controllo degli accessi nelle interfacce di gestione



 ## <a name="ncsc-cloud-security-principle-91"></a>Principio di sicurezza cloud NCSC 9.1
### <a name="authentication-of-users-to-management-interfaces-and-within-support-channels"></a>Autenticazione degli utenti per l'uso delle interfacce di gestione e dei canali di supporto
Per mantenere un servizio sicuro, gli utenti devono essere propriamente autenticati prima di poter eseguire attività di gestione, segnalare errori o richiedere modifiche al servizio.
Queste attività possono essere effettuate tramite un portale Web di gestione del servizio o mediante altri canali, ad esempio per telefono o posta elettronica, e includono in genere funzioni come il provisioning di nuovi elementi del servizio, la gestione degli account utente e la gestione dei dati degli utenti.
I provider di servizi devono garantire inoltre che tutte le richieste di gestione che potrebbero avere conseguenze a livello di sicurezza vengano eseguite attraverso canali sicuri e autenticati. Se gli utenti non vengono propriamente autenticati, quindi, è possibile che un impostore porti a termine azioni con privilegi, compromettendo la sicurezza dei dati o del servizio.


**Responsabilità:** `Shared`


|||
|---|---|
| **Cliente** | Quando un amministratore accede al portale di Microsoft Azure per gestire le risorse di Azure relative alla propria organizzazione, i dati trasmessi tra il portale e il dispositivo dell'amministratore vengono inviati tramite un canale TLS (Transport Layer Security) crittografato con chiavi di crittografia RSA/SHA256 a 2048 bit, come consigliato da CESG/NCSC.  <br /> <br /> Questo progetto di Azure usa l'autenticazione di Windows, Desktop remoto e BitLocker. Questi componenti possono essere configurati per basarsi su moduli di crittografia convalidati FIPS 140. <br /> <br /> Questo progetto di Azure applica autorizzazioni di accesso logico tramite il controllo degli accessi in base al ruolo amministrato da Azure Active Directory assegnando utenti a ruoli, da Active Directory assegnando utenti a gruppi di sicurezza e da controlli a livello di sistema operativo Windows. I ruoli di Azure Active Directory assegnati a utenti o gruppi controllano l'accesso logico alle risorse in Azure a livello di risorsa, gruppo o sottoscrizione. I gruppi di sicurezza di Active Directory controllano l'accesso logico a risorse e funzioni a livello di sistema operativo. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | I clienti amministrano le risorse di Azure tramite il portale di Azure, che fornisce accesso a tutte le macchine virtuali, i database, i servizi cloud e le altre risorse configurate per l'account del cliente. L'accesso Web al portale di Azure è protetto tramite connessioni TLS (Transport Layer Security) 1.2 standard con chiavi di crittografia RSA/SHA256 a 2048 bit, come consigliato da CESG/NCSC. Sono previsti infine controlli degli accessi in base al ruolo per consentire ai clienti di fornire un accesso limitato alle risorse di gestione di Azure per utenti e gruppi specifici. |


 ## <a name="ncsc-cloud-security-principle-92"></a>Principio di sicurezza cloud NCSC 9.2
### <a name="separation-and-access-control-within-management-interfaces"></a>Separazione e controllo degli accessi nelle interfacce di gestione
Molti servizi cloud vengono gestiti tramite applicazioni Web o interfacce API. Queste interfacce ricoprono un ruolo essenziale nella sicurezza del servizio. Se gli utenti non sono adeguatamente separati all'interno delle interfacce di gestione, è possibile che un utente comprometta il servizio o modifichi i dati di un altro utente.
Gli account con privilegi amministrativi hanno spesso accesso a grandi volumi di dati. Limitando le autorizzazioni dei singoli utenti ai dati strettamente necessari, è possibile limitare i danni provocati da utenti malintenzionati o da violazioni delle credenziali o dei dispositivi.
Il controllo degli accessi in base al ruolo fornisce un meccanismo per applicare questa misura preventiva e può risultare particolarmente importante per gli utenti che gestiscono distribuzioni di grandi dimensioni.
L'esposizione delle interfacce di gestione a reti meno accessibili (ad esempio, community anziché reti pubbliche) rende inoltre più difficile per gli utenti malintenzionati accedere e attaccare le interfacce, poiché dovrebbero prima ottenere l'accesso a una di queste reti. Nel principio 11 vengono fornite utili informazioni sulla valutazione dei rischi di esposizione delle interfacce a diversi tipi di rete.


**Responsabilità:** `Shared`


|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce un gateway applicazione e funzioni di bilanciamento del carico e configura regole di gruppo di sicurezza di rete per controllare le comunicazioni in corrispondenza dei limiti esterni e tra subnet interne. Le funzionalità utente vengono separate dalle funzionalità di gestione del sistema tramite l'applicazione di controlli di accesso logici e tramite l'architettura del sistema. Le interfacce delle funzionalità di gestione del sistema sono separate dalle interfacce utente. La connettività di gestione viene realizzata interamente tramite un bastion host (jumpbox) protetto situato in una subnet di gestione con regole di gruppo di sicurezza di rete appropriate per limitare l'accesso alle risorse di produzione. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Come descritto nella sezione sulla separazione degli utenti, la separazione è parte integrante di Azure. È possibile usare Azure Active Directory (Azure AD o AAD) per fornire a ogni utente che esegue l'autenticazione al portale di Azure l'accesso alle sole risorse che è autorizzato a visualizzare e gestire. I diversi account cliente, quindi, sono rigorosamente separati l'uno dall'altro anche se vengono gestiti tramite il portale di Azure comune. |


 ## <a name="ncsc-cloud-security-principle-10"></a>Principio di sicurezza cloud NCSC 10
### <a name="identity-and-authentication"></a>Identità e autenticazione
L'accesso alle interfacce del servizio deve essere limitato agli utenti autenticati e autorizzati.
Un'autenticazione non sicura a queste interfacce può infatti consentire un accesso non autorizzato ai sistemi e provocare così il furto o la modifica dei dati, modifiche al servizio o un attacco di tipo Denial of Service.
L'autenticazione, inoltre, deve essere eseguita su canali sicuri. Posta elettronica, HTTP e telefono, ad esempio, sono vulnerabili a intercettazioni e attacchi di ingegneria sociale.


**Responsabilità:** `Shared`


|||
|---|---|
| **Cliente** | Questo progetto di Azure usa Active Directory per la gestione degli account. L'accesso alle risorse distribuito da questo progetto di Azure è protetto da attacchi di tipo replay dalla funzionalità incorporata Kerberos di Azure Active Directory, Active Directory e il sistema operativo Windows. Nell'autenticazione Kerberos l'autenticatore inviato dal client contiene dati aggiuntivi, ad esempio un elenco di indirizzi IP crittografato, timestamp di client e durata del ticket. Se un pacchetto viene riprodotto, viene controllato il timestamp. Se il timestamp è precedente o uguale a quello di un autenticatore precedente, il pacchetto viene rifiutato. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Azure fornisce servizi che consentono di tenere traccia dell'identità, nonché di integrarla con gli archivi di identità eventualmente già in uso. Azure AD è un servizio completo di gestione delle identità e degli accessi per il cloud che consente l'accesso sicuro ai dati presenti nelle applicazioni locali e cloud. Azure AD semplifica anche la gestione di utenti e gruppi combinando servizi di directory core, amministrazione avanzata delle identità, sicurezza e gestione degli accessi alle applicazioni. |


 ## <a name="ncsc-cloud-security-principle-11"></a>Principio di sicurezza cloud NCSC 11
### <a name="external-interface-protection"></a>Protezione dell'interfaccia esterna
Tutte le interfacce esterne o meno attendibili del servizio devono essere identificate e adeguatamente protette.
Se alcune delle interfacce esposte sono private (ad esempio, interfacce di gestione), l'impatto di un'eventuale violazione può essere ancora più rilevante.
È possibile usare modelli diversi per la connessione a servizi cloud che espongono i sistemi aziendali a diversi livelli di rischio.


**Responsabilità:** `Shared`


|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce le risorse in un'architettura con una subnet Web, una subnet di database, una subnet Active Directory e una subnet di gestione separate. Le subnet sono separate logicamente da regole del gruppo di sicurezza di rete applicate alle singole subnet per limitare il traffico tra subnet esclusivamente a quello necessario per le funzionalità di sistema e di gestione (ad esempio, il traffico esterno non può accedere alle subnet del database, di gestione o di Active Directory).  <br /> <br /> Viene inoltre distribuito un gateway applicazione per gestire connessioni esterne ad applicazioni Web distribuite ai clienti. Le connessioni esterne per l'accesso a scopo di gestione sono limitate a un jumpbox (bastion host) distribuito in una subnet di gestione con regole di sicurezza di rete applicate per limitare le connessioni esterne a indirizzi IP autorizzati. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Microsoft si avvale di un metodo denominato "Red Teaming" per migliorare i controlli di sicurezza di Azure ed elaborarli tramite test di penetrazione periodici. Il Red Team è costituito da un gruppo di dipendenti Microsoft a tempo pieno incaricato di eseguire attacchi mirati e persistenti contro l'infrastruttura, le piattaforme e le applicazioni Microsoft, ma non le applicazioni e i dati dei clienti finali. <br /> <br /> Per altre informazioni sul metodo "Red Teaming" di Microsoft e una descrizione delle attività del "Blue Teaming", vedere la descrizione completa in [Progetto di Azure - Principi di sicurezza cloud NCSC - Matrice di responsabilità dei clienti](https://aka.ms/blueprintuk-gcrm).  |


 ## <a name="ncsc-cloud-security-principle-12"></a>Principio di sicurezza cloud NCSC 12
### <a name="secure-service-administration"></a>Amministrazione sicura dei servizi
Ai sistemi usati per l'amministrazione di un servizio cloud viene concesso l'accesso al servizio con privilegi elevati. Un'eventuale violazione di questi sistemi avrebbe quindi conseguenze piuttosto gravi, come la possibilità di aggirare i controlli di sicurezza e rubare o modificare grandi volumi di dati.
La progettazione, l'implementazione e la gestione dei sistemi di amministrazione, pertanto, deve seguire procedure collaudate a livello aziendale, in considerazione del valore che questi rivestono agli occhi degli utenti malintenzionati.


**Responsabilità:** `Shared`


|||
|---|---|
| **Cliente** | Il cliente ha la responsabilità di mettere a disposizione una workstation sicura per l'amministrazione della sottoscrizione di Azure e delle risorse distribuite dal cliente stesso (inclusi sistemi operativi, database, applicazioni e software). La dichiarazione di implementazione del cliente deve prevedere i meccanismi usati per ridurre il rischio di sfruttamento delle risorse distribuite dal cliente. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Il personale operativo di Microsoft Azure è tenuto a usare workstation di amministrazione sicure, denominate SAW o PAW (Privileged Access Workstations, workstation con accesso con privilegi). L'approccio SAW è un'estensione dell'ormai consolidata buona prassi di usare per il personale amministrativo due account diversi per i ruoli di utente e amministratore, assegnando un account amministrativo personale completamente diverso dal normale account utente. Tenendo separati questi account sensibili, l'approccio SAW garantisce l'affidabilità della workstation. |


 ## <a name="ncsc-cloud-security-principle-13"></a>Principio di sicurezza cloud NCSC 13
### <a name="audit-information-for-users"></a>Informazioni di controllo per gli utenti
I clienti devono poter accedere ai record di controllo necessari per monitorare l'accesso al servizio e i dati in esso contenuti. Il tipo di informazioni di controllo disponibili inciderà direttamente sulla capacità di rilevare e contrastare attività dannose o inappropriate entro un intervallo di tempo ragionevole.


**Responsabilità:** `Shared`


|||
|---|---|
| **Cliente** | Gli eventi controllati da questo progetto di Azure includono quelli controllati dai log attività di Azure per le risorse distribuite, dai log a livello del sistema operativo e dai log di Active Directory. Questi log di eventi includono informazioni sufficienti a determinare quando si verificano gli eventi, l'origine dell'evento, il risultato dell'evento e altre informazioni dettagliate che supportano l'analisi degli eventi imprevisti correlati alla sicurezza. I clienti possono selezionare altri eventi da controllare per soddisfare le esigenze correlate agli obiettivi. Per tutte le risorse di Azure sono disponibili log di controllo nel portale di Azure. |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Azure Log Analytics raccoglie i record degli eventi che si verificano nei sistemi e nelle reti di un'organizzazione nel momento stesso in cui avvengono, prima che possano essere manomessi, e consente vari tipi di analisi correlando i dati di più computer. Azure consente ai clienti di eseguire la generazione di eventi di sicurezza e la raccolta da ruoli IaaS e PaaS di Azure in un archivio centrale nelle loro sottoscrizioni. Questi eventi raccolti possono essere esportati in sistemi di gestione di eventi e informazioni di sicurezza locali per il monitoraggio continuo. Dopo il trasferimento dei dati nel servizio di archiviazione, è possibile visualizzare i dati di diagnostica scegliendo tra vari tipi di opzione. <br /> <br /> Le funzionalità di diagnostica integrate in Azure possono semplificare le operazioni di debug. Per le applicazioni distribuite in Azure, sono abilitati per impostazione predefinita alcuni eventi di sicurezza del sistema operativo. I clienti possono aggiungere, rimuovere o modificare gli eventi da controllare personalizzando i criteri di controllo del sistema operativo. <br /> <br /> A livello generale, è piuttosto facile iniziare a raccogliere log usando la funzionalità di inoltro di eventi di Windows o la più avanzata tecnologia Diagnostica di Azure quando vengono distribuite macchine virtuali basate su Windows usando IaaS in Azure. Diagnostica di Azure può essere configurata anche per raccogliere log ed eventi da istanze del ruolo PaaS. Quando si usano macchine virtuali basate su IaaS, un cliente può configurare e abilitare gli eventi di sicurezza desiderati nello stesso modo in cui abilita i server Windows per la registrazione dei controlli nel data center locale. Per le applicazioni Web, è possibile abilitare anche la registrazione delle attività IIS, se questa è l'applicazione e la distribuzione principale in Azure. Per soddisfare i requisiti di sovranità dei dati, i clienti possono sempre archiviare i dati di sicurezza in account di archiviazione dislocati in posizioni geografiche supportate di propria scelta. |


 ## <a name="ncsc-cloud-security-principle-14"></a>Principio di sicurezza cloud NCSC 14
### <a name="secure-use-of-the-service"></a>Utilizzo sicuro del servizio
Se non si usa il servizio in modo adeguato, è possibile che venga compromessa la sicurezza dei servizi cloud e dei dati contenuti al loro interno. Per garantire che i dati siano adeguatamente protetti, quindi, è necessario assumersi determinate responsabilità quando si usa il servizio.
L'entità delle responsabilità varia in base ai modelli di distribuzione del servizio cloud e allo scenario in cui si prevede di usare il servizio. Anche le funzionalità specifiche dei singoli servizi possono essere rilevanti. Ad esempio, il modo in cui una rete di distribuzione di contenuti protegge la chiave privata o il modo in cui un provider di pagamenti cloud rileva transazioni illecite costituiscono importanti considerazioni di sicurezza da aggiungere alle considerazioni generali trattate dai principi di sicurezza del cloud.  
Anche le offerte IaaS e PaaS prevedono responsabilità rilevanti in fatto di sicurezza dei dati e dei carichi di lavoro. Se si acquista un'istanza di calcolo IaaS, ad esempio, si è responsabili dell'installazione di un moderno sistema operativo, della configurazione del sistema operativo in modo sicuro, della distribuzione sicura di qualsiasi applicazione e della gestione dell'istanza tramite l'applicazione di patch o l'esecuzione dei necessari interventi di manutenzione.


**Responsabilità:** `Customer`

> [!NOTE]
> Il cliente può usare il Centro sicurezza di Azure per prevenire, rilevare e gestire le minacce con maggiore visibilità e più controllo sulla sicurezza delle risorse di Azure. Il Centro sicurezza di Azure offre strumenti integrati di monitoraggio della sicurezza e gestione dei criteri per le sottoscrizioni di Azure, aiuta a rilevare le minacce che potrebbero altrimenti passare inosservate ed è compatibile con un ampio ecosistema di soluzioni di sicurezza.

|||
|---|---|
| **Cliente** | I modelli di Azure Resource Manager e le relative risorse che costituiscono questo progetto di Azure adottano un approccio avanzato alla sicurezza. Per essere conformi a questo principio, sono necessarie configurazioni aggiuntive da usare nell'ambiente di produzione (ad esempio, software di gestione dei database, distribuzione di applicazioni Web). |
| **Provider&nbsp;(Microsoft&nbsp;Azure)** | Non applicabile |

## <a name="disclaimer"></a>Dichiarazione di non responsabilità

 - Questo documento è esclusivamente a scopo informativo. MICROSOFT NON RILASCIA ALCUNA GARANZIA, ESPRESSA, IMPLICITA O DI LEGGE, RIGUARDO ALLE INFORMAZIONI CONTENUTE IN QUESTO DOCUMENTO. Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Internet, sono soggette a modifica senza preavviso. I clienti che fanno riferimento a questo documento lo usano a proprio rischio.
 - Questo documento non concede ai clienti diritti legali di proprietà intellettuale per alcun prodotto o soluzione Microsoft.
 - I clienti possono copiare e usare questo documento per scopi di riferimento interni.
 - Alcune indicazioni contenute in questo documento possono comportare un maggiore utilizzo di risorse di dati, rete o calcolo in Azure, aumentando di conseguenza i costi di licenza o di sottoscrizione di Azure per il cliente.
 - Questa architettura è una soluzione di base che i clienti possono adattare ai propri requisiti specifici e che non deve essere usata così com'è in un ambiente di produzione.
 - Questo documento è stato sviluppato come riferimento e non deve essere usato per definire tutti i mezzi attraverso cui un cliente può soddisfare requisiti e normative di conformità specifici. I clienti dovranno richiedere supporto legale all'organizzazione riguardo alle implementazioni approvate.