<properties
   pageTitle="Case study Azure del database SQL di Microsoft Azure - GEP | Microsoft Azure"
   description="Informazioni su come GEP usa il database SQL per raggiungere un maggior numero di clienti in tutto il mondo e per ottenere maggiore efficienza"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/08/2016"
   ms.author="carlrab"/>


# <a name="azure-gives-gep-global-reach-and-greater-efficiency"></a>Azure offre a GEP copertura globale e maggiore efficienza

![Logo GEP](./media/sql-database-implementation-gep/geplogo.png)

GEP offre software e servizi che consentono ai responsabili dell'approvvigionamento in tutto il mondo di massimizzare l'impatto sulle operazioni, sulle strategie e sulle prestazioni finanziarie dell'azienda. Oltre ai servizi gestiti e di consulenza, l'azienda offre SMART by GEP®, una piattaforma software completa per l'approvvigionamento basata su cloud. GEP ha tuttavia riscontrato problemi nel tentativo di supportare soluzioni come SMART by GEP con il proprio data center locale: sono stati necessari importanti investimenti e i requisiti normativi in altri paesi hanno reso gli investimenti necessari un ostacolo in più. Con la migrazione al cloud, GEP ha liberato risorse IT che potevano trascurare le operazioni IT e concentrarsi maggiormente sullo sviluppo di nuove fonti di valore per i clienti in tutto il mondo.

## <a name="expanding-services-and-growth-by-using-azure"></a>Espansione dei servizi e crescita tramite Azure

I clienti SMART by GEP gradiscono le funzionalità e la semplicità d'uso della piattaforma; i clienti possono gestire i processi da qualsiasi luogo, in qualsiasi momento e da qualsiasi dispositivo: portatile, tablet o telefono. Grazie al passaggio a Microsoft Azure, GEP ha potuto accogliere la rapida crescita e soddisfare il potenziale di espansione su nuovi mercati. Secondo quanto riferito dal Vice presidente del reparto Tecnologia di GEP, Dhananjay Nagalkar, "Microsoft Azure ha svolto un ruolo chiave nel successo di GEP, consentendo di aumentare rapidamente i servizi con agilità e fornendo datacenter regionali che permettono di soddisfare le esigenze normative dei nostri clienti in tutto il mondo".

## <a name="the-limitations-of-a-do-it-yourself-datacenter"></a>I limiti di un datacenter fai da te

Nel 2013, i responsabili di GEP hanno riconosciuto la necessità di un modo per garantire scalabilità e prestazioni con l'aumento della base dei clienti. Nagalkar ha spiegato: "Per soddisfare tale richiesta con i nostri datacenter esistenti, dovremmo espandere notevolmente l'infrastruttura e le risorse IT. L'investimento e il tempo necessari sarebbero enormi". Le macchine virtuali e fisiche locali richiedono ampia configurazione, gestione, scalabilità, backup e applicazione di patch a un livello che comporterebbe costi proibitivi per GEP. Le soluzioni cloud, invece, offrono semplicità e praticità che consentono a GEP di concentrarsi più sullo sviluppo che sulla gestione di operazioni IT di grande volume e in continua crescita. Nagalkar sapeva che GEP avrebbe potuto ridurre i costi relativi ad acquisto, configurazione e gestione dell'infrastruttura tramite la migrazione al cloud.

GEP necessitava anche di un modo per superare le barriere normative che impedivano l'espansione su alcuni mercati del mondo. Per molti dei potenziali clienti europei di GEP, la conformità alle normative richiedeva l'archiviazione dei dati nelle rispettive aree geografiche locali. Tuttavia, non sarebbe stato facile per GEP creare più datacenter. "I costi di manodopera IT e gli investimenti infrastrutturali in tutto il mondo avrebbero avuto un impatto significativo sui margini", spiega Nagalkar. "Di conseguenza, eravamo letteralmente costretti a rifiutare potenziali clienti che richiedevano l'archiviazione locale dei dati".

Nagalkar sapeva che una soluzione cloud poteva essere la risposta a questo dilemma. Se GEP avesse potuto trasferire i servizi su un provider di cloud con presenza globale, avrebbe potuto soddisfare meglio le esigenze di conformità normativa e di prestazioni dei clienti, archiviando i dati nei percorsi fisici dei clienti.

## <a name="finding-smooth-skies-in-the-cloud"></a>Alla ricerca di cieli limpidi nel cloud

Nagalkar e il suo team hanno esaminato diverse opzioni di cloud, ma la maggior parte è costituita da soluzioni basate su Infrastruttura come servizio (IaaS), che avrebbero comportato per GEP un notevole investimento di risorse IT per la configurazione e la gestione del servizio. La soluzione Piattaforma come servizio (PaaS) di Azure si è rivelata molto più adatta.

"Con Azure, GEP non deve occuparsi della gestione dei database, della configurazione delle macchine virtuali, dell'applicazione di patch o di altre attività di gestione dell'infrastruttura", ha dichiarato Nagalkar. "Possiamo invece concentrare le risorse su ciò che ci riesce meglio: sfruttare le nostre competenze nell'approvvigionamento per compilare software che offrano ai nostri clienti vantaggi reali". 

Difatti, la transizione ad Azure ha consentito a GEP di ridurre il personale operativo IT, creando contemporaneamente maggiore funzionalità per i clienti.

Grazie all'uso dei datacenter di Azure in tutto il mondo, GEP ha potuto estendere facilmente la copertura in Europa e Asia. I datacenter globali consentono a GEP di eseguire agilmente la scalabilità e soddisfare le esigenze dei clienti che richiedono archiviazione dati in locale, riducendo la latenza e rispettando i requisiti normativi.

## <a name="smart-by-gep-architecture"></a>Architettura di SMART by GEP

GEP ha compilato SMART by GEP in Azure partendo da zero. La motivazione fondamentale per GEP era la maggiore scalabilità, il minor tempo di inattività e la riduzione dei costi di manutenzione che avrebbe potuto riscontrare con il database SQL di Azure rispetto ai risultati che avrebbe potuto ottenere in locale. Tuttavia, dopo il trasferimento nel cloud, GEP ha identificato nuove opportunità di sviluppo nel cloud, quali la creazione rapida di prototipi e una progettazione snella per rispondere meglio alle esigenze dei clienti. Lo sviluppo in Azure consente a GEP di dimenticare i problemi relativi alle licenze software che gli sviluppatori potrebbero riscontrare in locale. Il nucleo costitutivo di SMART by GEP è il database SQL di Azure, sebbene GEP usi numerosi altri servizi Azure per continuare a migliorare rapidamente e facilmente SMART by GEP.

![Architettura di SMART by GEP](./media/sql-database-implementation-gep/figure1.png) Figura 1. Architettura di SMART by GEP

## <a name="structured-data"></a>Dati strutturati

Nel cuore dell'applicazione SMART by GEP risiedono le istanze di database SQL di Azure che alimentano la soluzione di gestione dell'approvvigionamento aziendale. Quando GEP ha progettato SMART by GEP, ha ritenuto che il database SQL di Azure fosse ideale per la sua architettura, che avrebbe consentito all'azienda di ottenere il massimo livello di protezione dei dati e di soddisfare i requisiti normativi. GEP usa dei diversi livelli di protezione dei dati offerti da database SQL di Azure, tra cui:

- Crittografia dei dati inattivi tramite Transparent Data Encryption.
- Protezione dell'autenticazione tramite l'integrazione di database SQL di Azure con Azure Active Directory.
- Limitazione dell'accesso a un sottoinsieme appropriato di dati con protezione a livello di riga.
- Mascheramento dei dati in tempo reale tramite criteri.
- Rilevamento degli eventi del database tramite il Controllo del database SQL di Azure.

> "Possiamo usare tutte queste opzioni senza apportare alcuna modifica importante al codice e con un impatto minimo sulle prestazioni", ha affermato Nagalkar.

Con il database SQL di Azure, GEP dispone automaticamente di maggiori funzionalità di ripristino di emergenza che potrebbero essere state economicamente progettate in locale a causa delle funzionalità di tolleranza di errore integrate nel database SQL di Azure. GEP usa la funzionalità di replica geografica attiva nel database SQL di Azure, insieme a più repliche secondarie online, attive e leggibili (Gruppi di disponibilità Always On) in aree geografiche diverse, in modo da formare coppie a disponibilità elevata. La replica dei dati di SMART by GEP nelle diverse aree significa che, in caso di emergenza a livello di area, GEP è in grado di ripristinare facilmente i dati dei clienti tramite un obiettivo minimo del punto di ripristino (RPO) e un obiettivo del tempo di ripristino (RTO).

Ogni cliente SMART by GEP dispone di due istanze del database SQL di Azure: uno per l'elaborazione di transazioni online (OLTP) e uno per l'analisi, ad esempio l'analisi della spesa e dei report del cliente. I pool di database elastici del database SQL di Azure consentono a GEP di gestire migliaia di database in tutto il mondo al fine di gestire richieste impreviste di risorse del database. I pool elastici forniscono a GEP un mezzo per garantire che i database dei clienti siano in grado di eseguire la scalabilità, se necessaria, senza provisioning eccessivo o insufficiente, consentendo contestualmente il controllo dei costi. In aggiunta, poiché si tratta di un servizio PaaS, GEP ottiene tutte le nuove funzionalità del database SQL di Azure tramite aggiornamenti automatici.

## <a name="unstructured-and-semi-structured-data"></a>Dati non strutturati e semistrutturati

Tuttavia, alcuni dati dei clienti SMART by GEP necessitano di un'archiviazione strutturata con meno rigidità. Per questo tipo di dati, GEP usa Archiviazione BLOB di Azure, Archiviazione tabelle di Azure e Cache Redis di Azure. L'archiviazione BLOB di Azure ospita gli allegati che gli utenti SMART by GEP caricano nell'applicazione. È questa la posizione in cui SMART by GEP archivia anche i contenuti statici, ad esempio i fogli con stile a cascata (CSS) e i file JavaScript.

GEP archivia i dati non rivolti ai clienti, come i dati di accesso a SMART by GEP, in Archiviazione tabelle di Azure, che offre a GEP un'efficace e conveniente archiviazione illimitata e tempi di recupero rapidi, senza la necessità di impostare uno schema per i dati. GEP usa Cache Redis di Azure per le cache master.

## <a name="authentication-and-routing"></a>Autenticazione e routing

Il Servizio di controllo di accesso di Azure (ACS) fornisce agli utenti di SMART by GEP un'ampia gamma di opzioni per l'accesso al software. Azure ACS può eseguire la federazione con qualsiasi provider di identità che scambia dati di autenticazione tramite Security Assertion Markup Language (SAML), ad esempio Servizi di dominio Active Directory, Identità Ping, OneLogin o SiteMinder. In questo modo GEP può implementare l'accesso Single Sign-On (SSO) per i clienti senza preoccuparsi di memorizzare le credenziali utente e di gestire i criteri delle password dei clienti.

Dopo aver eseguito l'accesso, i clienti possono disporre di risorse aziendali corrette in SMART by GEP. GEP usa Gestione traffico di Azure per reindirizzare e bilanciare il carico delle richieste provenienti dai dispositivi mobili degli utenti e dalle sessioni del browser.

## <a name="other-azure-services"></a>Altri servizi di Azure

GEP usa numerosi altri servizi di Azure per rendere SMART by GEP reattivo alle esigenze dei clienti. GEP usa i servizi cloud di Azure (ruoli Web e di lavoro) per ospitare presentazioni di applicazioni e servizi di logica aziendale protetti. I servizi cloud consentono agli sviluppatori di gestire l'infrastruttura come codice (IAC) e distribuire nuove applicazioni SMART by GEP in una frazione del tempo richiesto con i datacenter locali, senza alcun coinvolgimento del reparto IT. Gli sviluppatori GEP possono usare l'ambiente di gestione temporanea dei servizi cloud per testare le nuove versioni senza conseguenze sulla distribuzione di produzione corrente. Dopo il test, GEP usa le funzionalità di scambio degli indirizzi VIP dei servizi cloud di Azure per spostare il codice di gestione temporanea sullo slot di produzione entro un minuto, riducendo i tempi di inattività della distribuzione.

Per ridurre la latenza dell'applicazione, GEP usa la rete per la distribuzione di contenuti (CDN) di Azure per archiviare il contenuto statico nell'archiviazione BLOB di Azure (ad esempio file CSS e JavaScript) sui server perimetrali vicini agli utenti. GEP usa il bus di servizio di Azure per supportare modelli di architettura delle applicazioni che vanno dalla pubblicazione-sottoscrizione, alla segregazione responsiva delle query dei comandi (CQRS) parziale fino all'architettura a più livelli con regime di controllo libero e comunicazione asincrona. GEP usa i servizi multimediali di Azure per migliorare il supporto tecnico ai clienti. GEP ha ritenuto di poter facilmente pubblicare video a supporto degli utenti su Servizi multimediali di Azure. Questi video rispondono a domande comuni degli utenti per migliorare la soddisfazione degli utenti di SMART by GEP e scaricare al contempo il carico di lavoro dello staff del supporto ai clienti di GEP.

Per inviare le migliaia di messaggi di posta elettronica transazionali generati su base giornaliera da SMART by GEP, l'azienda usa l'API .NET di SendGrid integrata con Azure. Per gli sviluppatori GEP, si tratta di un'operazione semplice: il componente aggiuntivo SendGrid per Azure è disponibile direttamente sul marketplace di Azure. Gli sviluppatori GEP possono configurare SMART by GEP usando il pacchetto NuGet di SendGrid in Microsoft Visual Studio. Il reparto IT di GEP può monitorare il traffico di posta elettronica SendGrid del software direttamente da Azure.

Infine, SMART by GEP impiega macchine virtuali di Azure, il servizio IaaS di Azure, per ospitare le applicazioni e i servizi che non hanno funzionalità al momento della progettazione, da sostituire con soluzioni Software come servizio (SaaS) o PaaS. Ad esempio, GEP ospita servizi API di integrazione in macchine virtuali per l'integrazione business-to-business (B2B) con sistemi Enterprise Resource Planning (ERP) locali degli utenti, come SAP, Oracle, PeopleSoft, JD Edwards, Microsoft Dynamics GP e Lawson, nonché soluzioni SaaS dei clienti per lo scambio efficiente di documenti di approvvigionamento, ad esempio le fatture.

> "La compilazione di SMART by GEP nel cloud di Microsoft Azure ha completamente eliminato la necessità di un reparto IT locale, non solo per GEP ma anche per le operazioni di approvvigionamento dei nostri clienti". 

> - Dhananjay Nagalkar, Vice presidente di Soluzioni tecnologiche

## <a name="expand-customer-satisfaction-without-expanding-it"></a>Aumentare la soddisfazione dei clienti senza ampliare il reparto IT

Dopo la migrazione dai datacenter locali ad Azure e la compilazione di SMART by GEP da zero sulla piattaforma Azure, GEP ha aumentato la scalabilità e la flessibilità senza la necessità di espandere il personale IT o l'infrastruttura. Infatti, l'azienda non ha aggiunto risorse IT negli ultimi quattro anni. Il pratico modello PaaS di Azure ha consentito a GEP di ridurre la spesa per il supporto ai fornitori e per le operazioni di gestione. Di conseguenza, GEP ha potuto concentrare le risorse sullo sviluppo di software. Lo sviluppo nel cloud consente agli sviluppatori GEP di testare rapidamente le nuove idee senza la necessità di dedicare tempo al coordinamento con il reparto IT o senza doversi preoccupare dei requisiti di licenza locali. Il database SQL di Azure aiuta GEP a garantire ai clienti prestazioni e servizi sempre eccellenti.
 
## <a name="more-information"></a>Altre informazioni

- Home page di GEP: [GEP](http://www.gep.com)
- Smart by GEP: [Smart by GEP](http://www.smartbygep.com)

##<a name="gep-contributors"></a>Collaboratori GEP

- Huzaifa Matawala, Amministratore aggiunto - progettista, GEP
- Sathyan Narasingh, Engineering Manager, GEP
- Deepa Velukutty, Progettista database, GEP



<!--HONumber=Oct16_HO2-->


