---
title: Progetto per l'elaborazione dei pagamenti di Azure - Requisiti per sistemi sicuri
description: Requisito 6 di PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 79889fdb-52d2-42db-9117-6e2f33d501e0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 24c8d90d3fec27258165472e99ba3d36ffcba733
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="secure-system-requirements-for-pci-dss-compliant-environments"></a>Requisiti per sistemi sicuri per gli ambienti conformi a PCI DSS 
## <a name="pci-dss-requirement-6"></a>Requisito 6 di PCI DSS

**Sviluppare e gestire sistemi e applicazioni sicuri**

> [!NOTE]
> Questi requisiti sono definiti dal [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) nell'ambito di [PCI Data Security Standard (DSS) versione 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Per informazioni sulle procedure di test e indicazioni per ogni requisito, vedere lo standard PCI DSS.

Gli utenti malintenzionati sfruttano le vulnerabilità della sicurezza per ottenere l'accesso con privilegi ai sistemi. Molte di queste vulnerabilità vengono risolte tramite le patch per la sicurezza distribuite dai fornitori, che devono essere installate dalle entità che gestiscono i sistemi. Tutti i sistemi devono disporre di tutte le patch software appropriate per essere protetti dallo sfruttamento e dalla compromissione dei dati di titolari di carte da parte di utenti malintenzionati e tramite software dannoso.

> [!NOTE]
> Con patch software appropriate si intendono le patch valutate e testate a sufficienza per stabilire che non sono in conflitto con configurazioni di sicurezza esistenti. Per le applicazioni sviluppate internamente, è possibile evitare numerose vulnerabilità usando processi di sviluppo di sistema standard e tecniche di scrittura del codice sicure.

## <a name="pci-dss-requirement-61"></a>Requisito 6.1 di PCI DSS

**6.1** Stabilire un processo per identificare le vulnerabilità di sicurezza, usando fonti esterne attendibili per raccogliere informazioni sulle vulnerabilità di sicurezza e assegnare una classificazione dei rischi (ad esempio, "alto", "medio" o "basso") alle nuove vulnerabilità di sicurezza individuate.

> [!NOTE]
> Le classificazioni dei rischi devono essere basate su procedure consigliate del settore, oltre a tenere conto dell'impatto potenziale. Ad esempio, i criteri per la classificazione delle vulnerabilità possono tenere conto del punteggio base CVSS e/o della classificazione del fornitore e/o dei tipi di sistemi interessati. 
> 
> I metodi per la valutazione delle vulnerabilità e l'assegnazione delle classificazioni dei rischi variano in base all'ambiente e alla strategia di valutazione dei rischi di un'organizzazione. Le classificazioni dei rischi devono perlomeno identificare tutte le vulnerabilità considerate "ad alto rischio" per l'ambiente. Oltre alla classificazione dei rischi, le vulnerabilità possono essere considerate "critiche" se costituiscono una minaccia imminente per l'ambiente, hanno impatto su sistemi critici e/o possono condurre a potenziali compromissioni se non risolte. Sono esempi di sistemi critici i sistemi di sicurezza, i dispositivi e i sistemi esposti al pubblico, i database e altri sistemi che archiviano, elaborano o trasmettono i dati di titolari di carte.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Sono state definite e implementate procedure per l'analisi delle vulnerabilità negli host dell'hypervisor entro il limite dell'ambito. L'analisi delle vulnerabilità viene eseguita nei sistemi operativi server, nei database e nei dispositivi di rete con gli strumenti di analisi delle vulnerabilità appropriati. Le analisi delle vulnerabilità vengono eseguite come minimo con frequenza trimestrale. Microsoft Azure si rivolge a ispettori indipendenti per eseguire test di penetrazione del limite di Microsoft Azure. Vengono anche eseguiti regolarmente simulazioni con red team e i risultati vengono usati per apportare miglioramenti della sicurezza. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore riduce il rischio di vulnerabilità della sicurezza tramite un gateway applicazione con WAF e il set di regole OWASP abilitati. Per altre informazioni, vedere [PCI Guidance - Mitigation of the Risk of Security Vulnerabilities](payment-processing-blueprint.md#application-gateway) (Linee guida per PCI - Mitigazione dei rischi delle vulnerabilità di sicurezza).|



## <a name="pci-dss-requirement-62"></a>Requisito 6.2 di PCI DSS

**6.2** Assicurarsi che tutti i componenti di sistema e software siano protetti da vulnerabilità note tramite l'installazione delle patch di sicurezza applicabili pubblicate dai fornitori. Installare le patch di sicurezza critiche entro un mese dal rilascio.

> [!NOTE]
> Le patch di sicurezza critiche devono essere identificate in base al processo di classificazione dei rischi definito nel requisito 6.1.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure è responsabile di assicurarsi che tutti i dispositivi di rete e tutto il software del sistema operativo hypervisor siano protetti dalle vulnerabilità note tramite l'installazione delle patch di sicurezza applicabili pubblicate dai fornitori. A meno che un cliente non richieda di non usare il servizio, esiste un processo di gestione delle patch per assicurarsi di evitare e correggere tempestivamente le vulnerabilità a livello di sistema operativo. I server di produzione vengono analizzati per convalidare la conformità delle patch su base mensile. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore è una soluzione di servizio PaaS. Azure offre servizi di manutenzione per tutte le patch dei servizi.|



## <a name="pci-dss-requirement-63"></a>Requisito 6.3 di PCI DSS

**6.3** Sviluppare applicazioni software interne ed esterne (incluso l'accesso amministrativo basato su Web alle applicazioni) in modo sicuro, come segue:
- In conformità con lo standard PCI DSS (ad esempio, autenticazione e registrazione protette)
- Sulla base di standard e/o procedure consigliate del settore
- Integrando la sicurezza delle informazioni in tutto il ciclo di vita di sviluppo del software 

> [!NOTE]
> Questo vale per tutto il software sviluppato internamente, così come per il software su misura o personalizzato sviluppato da terze parti.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Le applicazioni e gli endpoint di Microsoft Azure vengono sviluppati in conformità con la metodologia Microsoft Security Development Lifecycle (SDL) che è in linea con i requisiti dello standard DSS. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore è stato progettato tenendo conto delle procedure consigliate del settore per la protezione dei dati di titolari di carte. Le linee guida per la distribuzione includono informazioni dettagliate sulle misure di sicurezza e la registrazione è abilitata.|



### <a name="pci-dss-requirement-631"></a>Requisito 6.3.1 di PCI DSS

**6.3.1** Rimuovere account, ID utente e password delle applicazioni personalizzate o per lo sviluppo e/o per i test prima che le applicazioni diventano attive o vengano rilasciate ai clienti.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Viene eseguita una revisione della sicurezza finale per le versioni principali prima della distribuzione in produzione da parte di un ispettore della sicurezza esterno al team di sviluppo di Azure per assicurarsi che vengano rilasciate solo le applicazioni pronte per gli ambienti di produzione. Nell'ambito di questa revisione finale ci si assicura che tutti gli account di test e i dati di test siano stati rimossi. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore offre un servizio di staging registrato e isolato. Ogni livello di rete ha un gruppo di sicurezza di rete (NSG) dedicato. Per altre informazioni, vedere [PCI Guidance - Network Security Groups](payment-processing-blueprint.md#network-security-groups) (Linee guida per PCI - Gruppi di sicurezza di rete).|



### <a name="pci-dss-requirement-632"></a>Requisito 6.3.2 di PCI DSS

**6.3.2** Verificare il codice personalizzato prima del rilascio in produzione o ai clienti, allo scopo di identificare le potenziali vulnerabilità (tramite processi manuali o automatizzati), tenendo conto dei requisiti seguenti:
- Le modifiche al codice vengono verificate da utenti diversi dall'autore del codice di origine e da utenti esperti delle tecniche di revisione del codice e delle procedure per la scrittura di codice sicuro.
- Le revisioni del codice accertano che il codice sia stato sviluppato in base alle linee guida per la scrittura di codice sicuro
- Le correzioni necessarie vengono implementate prima del rilascio
- I risultati della revisione del codice vengono esaminati e approvati dalla direzione prima del rilascio 

> [!NOTE]
> Questo requisito per le revisioni del codice si applica a tutto il codice personalizzato (interno e pubblico), come parte del ciclo di vita di sviluppo del sistema. 
>
> Le revisioni del codice possono essere eseguite da personale interno esterno o da terze parti competenti. Le applicazioni Web esposte al pubblico sono inoltre soggette a controlli aggiuntivi, per affrontare le continue minacce e vulnerabilità dopo l'implementazione, come definito nel requisito 6.6 di PCI DSS.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Le applicazioni e gli endpoint di Microsoft Azure vengono sviluppati in conformità con la metodologia Microsoft Security Development Lifecycle (SDL). |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore offre un servizio di staging registrato e isolato. Ogni livello di rete ha un gruppo di sicurezza di rete (NSG) dedicato. Per altre informazioni, vedere [PCI Guidance - Network Security Groups](payment-processing-blueprint.md#network-security-groups) (Linee guida per PCI - Gruppi di sicurezza di rete).|



## <a name="pci-dss-requirement-64"></a>Requisito 6.4 di PCI DSS

**6.4** Seguire processi e procedure di controllo delle modifiche per tutte le modifiche dei componenti di sistema. I processi devono includere quanto segue (vedere i requisiti da 6.4.1 a 6.4.6).

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft segue le linee guida NIST in merito alle considerazioni sulla sicurezza per lo sviluppo di software, e nello specifico si attiene al requisito di integrazione della sicurezza delle informazioni nel ciclo di vita dello sviluppo (SDL, Security Development Lifecycle) sin dalle fasi iniziali dello sviluppo del sistema. L'integrazione continua delle procedure di sicurezza in Microsoft SDL consente:<ul><li>Identificazione tempestiva e mitigazione delle vulnerabilità di sicurezza e delle configurazioni errate</li><li>Conoscenza delle potenziali problematiche per la scrittura del codice del software causate dai controlli di sicurezza richiesti</li><li>Identificazione dei servizi di sicurezza condivisi e riutilizzo degli strumenti ottimali per la sicurezza, che consente di migliorare il comportamento di sicurezza tramite metodi e tecniche collaudati</li><li>Applicazione del programma di gestione dei rischi già completo di Microsoft</li></ul>Microsoft Azure ha definito processi consolidati per la gestione di modifiche e rilasci per controllare l'implementazione delle modifiche principali, tra cui:<ul><li>Identificazione e documentazione della modifica pianificata</li><li>Identificazione di obiettivi aziendali, priorità e scenari durante la pianificazione del prodotto</li><li>Specifica del progetto di funzionalità/componente</li><li>Verifica dell'idoneità operativa in base a criteri/elenchi di controllo predefiniti per valutare i rischi e/o l'impatto complessivi</li><li>Test, autorizzazione e gestione delle modifiche in base ai criteri di ingresso/uscita per gli ambienti DEV (sviluppo), INT (test di integrazione), STAGE (pre-produzione) e PROD (produzione), come appropriato. I clienti sono responsabili delle loro applicazioni ospitate in Microsoft Azure.</li></ul> |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | La demo Contoso Webstore offre un servizio di staging registrato e isolato. <br /><br />Ogni livello di rete ha un gruppo di sicurezza di rete (NSG) dedicato. Per altre informazioni, vedere [PCI Guidance - Network Security Groups](payment-processing-blueprint.md#network-security-groups) (Linee guida per PCI - Gruppi di sicurezza di rete).<br /><br />Le modifiche vengono registrate con Operations Management Suite e vengono usati runbook per raccogliere i log. [Operations Management Suite (OMS)](/azure/operations-management-suite/) fornisce una registrazione completa delle modifiche. Le modifiche possono essere controllate e verificate per stabilirne l'accuratezza. Per istruzioni più dettagliate, vedere [PCI Guidance - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing) (Linee guida per PCI - Operations Management Suite).|



### <a name="pci-dss-requirement-641"></a>Requisito 6.4.1 di PCI DSS

**6.4.1** Separare gli ambienti di sviluppo/test dagli ambienti di produzione e imporre la separazione con controlli di accesso.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore offre un servizio di staging registrato e isolato. Ogni livello di rete ha un gruppo di sicurezza di rete (NSG) dedicato. Per altre informazioni, vedere [PCI Guidance - Network Security Groups](payment-processing-blueprint.md#network-security-groups) (Linee guida per PCI - Gruppi di sicurezza di rete).|



### <a name="pci-dss-requirement-642"></a>Requisito 6.4.2 di PCI DSS

**6.4.2** Separazione dei compiti tra gli ambienti di sviluppo/test e produzione

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore offre un servizio di staging registrato e isolato. Ogni livello di rete ha un gruppo di sicurezza di rete (NSG) dedicato. Per altre informazioni, vedere [PCI Guidance - Network Security Groups](payment-processing-blueprint.md#network-security-groups) (Linee guida per PCI - Gruppi di sicurezza di rete).|



### <a name="pci-dss-requirement-643"></a>Requisito 6.4.3 di PCI DSS

**6.4.3** I dati di produzione (PAN attivi) non vengono usati per attività di test o sviluppo.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore non include dati di numeri di account principali (PAN, Primary Account Number) attivi.|



### <a name="pci-dss-requirement-644"></a>Requisito 6.4.4 di PCI DSS

**6.4.4** Rimuovere dati e account di test dai componenti del sistema prima che il sistema diventi attivo o passi in produzione.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore non include account di test.|



### <a name="pci-dss-requirement-645"></a>Requisito 6.4.5 di PCI DSS

**6.4.5** Le procedure di controllo delle modifiche per l'implementazione di patch di sicurezza e modifiche del software devono includere quanto segue:
- **6.5.4.1** Documentazione dell'impatto.
- **6.5.4.2** Documentazione dell'approvazione delle modifiche da parte di parti autorizzate.
- **6.5.4.3** Test delle funzionalità per verificare che la modifica non abbia un impatto negativo sulla sicurezza del sistema.
- **6.5.4.4** Procedure di back-out.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore è una soluzione di servizio PaaS. Azure offre servizi di manutenzione per tutte le patch dei servizi.|



### <a name="pci-dss-requirement-646"></a>Requisito 6.4.6 di PCI DSS

**6.4.6** Dopo il completamento di una modifica significativa, tutti i requisiti di PCI DSS pertinenti devono essere implementati in tutti i sistemi e le reti nuovi o modificati e deve essere aggiornata la documentazione come applicabile.

> [!NOTE]
> Questo requisito è una procedura consigliata fino al 31 gennaio 2018 e in seguito diventerà un requisito.


**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore è una soluzione di servizio PaaS. Azure offre servizi di manutenzione per tutte le patch dei servizi.|



## <a name="pci-dss-requirement-65"></a>Requisito 6.5 di PCI DSS

**6.5** Evitare le vulnerabilità comuni per la scrittura del codice nei processi di sviluppo del software come indicato di seguito:
- Offrire agli sviluppatori almeno ogni anno un corso di formazione sulle tecniche di scrittura del codice aggiornate, che includa informazioni su come evitare le vulnerabilità comuni per la scrittura del codice.
- Sviluppare le applicazioni in base alle linee guida per la scrittura di codice sicuro.

> [!NOTE]
> Le vulnerabilità elencate nei punti da 6.5.1 a 6.5.10 risultavano aggiornate in base alle procedure consigliate del settore alla data di pubblicazione della presente versione di PCI DSS. Tuttavia, man mano che vengono aggiornate le procedure consigliate per la gestione delle vulnerabilità (ad esempio la guida OWASP, l'elenco dei primi 25 errori di programmazione più pericolosi di SANS/CWE, lo standard CERT per la scrittura di codice sicuro e così via), per questi requisiti occorre fare riferimento alle procedure consigliate aggiornate. 
> 
> [!NOTE]
> I requisiti da 6.5.1 a 6.5.6, di seguito, si applicano a tutte le applicazioni (interne o esterne). I requisiti da 6.5.7 a 6.5.10, di seguito, si applicano a tutte le applicazioni Web e alle interfacce applicative (interne o esterne). 

- **6.5.1** Attacchi di tipo injection, in particolare SQL injection. Tenere conto anche di attacchi di tipo injection per i comandi del sistema operativo, LDAP e XPath, nonché altri attacchi intrusivi di questo tipo.
- **6.5.2** Sovraccarichi del buffer
- **6.5.3** Archiviazione crittografica non sicura
- **6.5.4** Comunicazioni non sicure
- **6.5.5** Gestione degli errori non appropriata
- **6.5.6** Tutte le vulnerabilità "ad alto rischio" identificate nel processo di identificazione delle vulnerabilità (come definito nel requisito 6.1 di PCI DSS)
- **6.5.7** Cross-site scripting (XSS)
- **6.5.8** Controllo di accesso non corretto (ad esempio riferimenti a oggetti diretti non sicuri, impossibilità di limitare l'accesso a URL, attraversamento delle directory e impossibilità di limitare l'accesso degli utenti alle funzioni)
- **6.5.9** Richiesta intersito falsa (CSRF, Cross-Site Request Forgery)
- **6.5.10** Compromissione dei sistemi di gestione dell'autenticazione e delle sessioni

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | La demo Contoso Webstore offre indicazioni per lo sviluppo sicuro, un diagramma di flusso dei dati e un modello di rischio per illustrare le tecniche di sviluppo sicuro.|



## <a name="pci-dss-requirement-66"></a>Requisito 6.6 di PCI DSS

**6.6** Per le applicazioni Web rivolte al pubblico, affrontare nuove minacce e vulnerabilità in maniera continuativa e assicurarsi che tali applicazioni siano protette da attacchi noti con uno dei metodi seguenti:

- Verifica delle applicazioni Web rivolte al pubblico tramite strumenti o metodi manuali o automatici di valutazione della sicurezza per le vulnerabilità delle applicazioni, almeno ogni anno e dopo eventuali modifiche. 

   > [!NOTE]
   > Questa valutazione non corrisponde alle analisi delle vulnerabilità eseguite in base al requisito 11.2. 

- Installazione di una soluzione tecnica automatizzata che rileva e impedisce gli attacchi basati su Web (ad esempio, un web-application firewall) prima delle applicazioni Web rivolte al pubblico, per controllare continuamente tutto il traffico.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure esegue test sulle applicazioni Web rivolte al pubblico come parte del processo SDL, prima della distribuzione di tali applicazioni nell'ambiente di produzione. Microsoft analizza inoltre tutte le applicazioni Web rivolte al pubblico nell'ambiente di produzione a intervalli regolari per rilevare eventuali vulnerabilità possibili. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | La soluzione di riferimento riduce il rischio di vulnerabilità della sicurezza tramite un gateway applicazione con WAF e il set di regole OWASP abilitati. Per altre informazioni, vedere [PCI Guidance - Mitigation of the Risk of Security Vulnerabilities](payment-processing-blueprint.md#application-gateway) (Linee guida per PCI - Mitigazione dei rischi delle vulnerabilità di sicurezza).|



## <a name="pci-dss-requirement-67"></a>Requisito 6.7 di PCI DSS

**6.7** Assicurarsi che i criteri di sicurezza e le procedure operative per lo sviluppo e la manutenzione di sistemi e applicazioni sicuri siano documentati, applicati e noti a tutte le parti interessate.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | La soluzione di riferimento riduce il rischio di vulnerabilità della sicurezza tramite un gateway applicazione con WAF e il set di regole OWASP abilitati. Per altre informazioni, vedere [PCI Guidance - Mitigation of the Risk of Security Vulnerabilities](payment-processing-blueprint.md#application-gateway) (Linee guida per PCI - Mitigazione dei rischi delle vulnerabilità di sicurezza).|




