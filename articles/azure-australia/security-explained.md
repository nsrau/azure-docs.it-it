---
title: Informazioni sulla sicurezza in Azure Australia
description: Informazioni più richieste sulle aree australiane e che soddisfano i requisiti specifici dei criteri, delle normative e delle normative del governo australiano.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 212d14e31c152e50c216f2f34fb225c29fe054b5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571432"
---
# <a name="azure-australia-security-explained"></a>Informazioni sulla sicurezza in Azure Australia

Questo articolo illustra alcune delle domande comuni e le aree di interesse per gli enti pubblici australiani che esaminano, progettano e distribuiscono in Microsoft Azure Australia.

## <a name="irap-and-certified-cloud-services-list-ccsl-documents"></a>Documenti IRAP e certificati CCSL (Cloud Services List)

L'australiano Cyber Security Center (ACSC) fornisce una lettera di certificazione, un report di certificazione e una guida per i consumatori per il servizio quando viene aggiunto al CCSL.

Microsoft è attualmente elencato in CCSL per Azure, Office 365 e Dynamics 365 CRM.

Microsoft rende disponibili i documenti di certificazione di controllo, valutazione e ACSC per clienti e partner in una pagina specifica dell'Australia del [portale Microsoft Service Trust](https://aka.ms/au-irap).

## <a name="dissemination-limiting-markers-dlm-and-protected-certification"></a>Marcatori limitazione della diffusione (DLM) e certificazione protetta

Il processo di utilizzo dei sistemi, inclusi i servizi cloud, approvati per l'utilizzo da parte delle organizzazioni governative, viene definito nel [manuale della protezione delle informazioni (ISM)](https://acsc.gov.au/infosec/ism/) prodotto e pubblicato da Australian Cyber Security Center (ACSC). L'australiano Cyber Security Centre (ACSC) è l'entità all'interno di ASD responsabile della sicurezza informatica e della certificazione cloud.

Il processo di approvazione prevede due passaggi:

1. Valutazione della sicurezza (IRAP): un processo in cui i professionisti registrati valutano i sistemi, i servizi e le soluzioni rispetto ai controlli tecnici nell'ISM e valutano se i controlli sono stati implementati in modo efficace. La valutazione identifica anche eventuali rischi specifici che l'autorità di approvazione deve prendere in considerazione prima di emettere un'approvazione per operare (ATO).

1. Approvazione per il funzionamento: processo in cui un dirigente senior di un'agenzia governativa riconosce formalmente e accetta il rischio residuo di un sistema per le informazioni elaborate, archiviate e comunica.  Un input per questo processo è la valutazione della sicurezza.

La valutazione dei servizi di Azure a livello protetto indica che l'implementazione dei controlli di sicurezza necessari per l'archiviazione e l'elaborazione dei dati protetti e sottostanti è stata confermata e funziona in modo efficiente.

## <a name="australian-data-classification-changes"></a>Modifiche alla classificazione dei dati australiani

Il 1 ° ottobre 2018 il reparto del procuratore generale ha annunciato le modifiche apportate al Framework di sicurezza dei criteri di protezione (PSPF), in particolare un nuovo sistema informativo riservato [e classificato](https://www.protectivesecurity.gov.au/information/sensitive-classified-information/Pages/default.aspx).

![Classificazioni PSPF rivedute](media/pspf-classifications.png)

Queste modifiche sono interessate da tutte le agenzie e organizzazioni australiane che operano con la PSPF. Il cambiamento principale che influisca sulle certificazioni IRAP/CCSL attuali è il ritiro dei limiti di divulgazione (DLM). Contrassegno **ufficiale: Sensitive** sostituisce i vari DLMs usati per la protezione delle informazioni riservate. La modifica presenta anche tre marcatori di gestione delle informazioni che possono essere applicati a tutte le informazioni ufficiali a tutti i livelli di sensibilità e classificazione. La classificazione **protetta** rimane invariata.

Il termine Unclassified viene rimosso dal nuovo sistema e il termine non ufficiale viene applicato alle informazioni non governative, sebbene non richieda un contrassegno formale.

## <a name="choosing-an-azure-region-for-my-official-sensitive-and-protected-workloads"></a>Scelta di un'area di Azure per il mio ufficiale: Carichi di lavoro sensibili e protetti

Il sito **ufficiale di Azure: I** servizi sensibili e **protetti** certificati vengono distribuiti in tutte e quattro le aree dei data center australiani (Australia orientale, Australia sudorientale, Australia centrale e Australia centrale 2); Tuttavia, il report di certificazione emesso dal ACSC consiglia di distribuire i dati **protetti** nelle aree di Azure per enti pubblici in Canberra se è disponibile un servizio. Informazioni più dettagliate sui servizi di Azure certificati **protetti** sono disponibili [nella pagina relativa all'Australia sulla STP](https://aka.ms/au-irap).

>[!NOTE]
>Microsoft consiglia di distribuire i dati governativi di tutte le sensibilità e le classificazioni nelle aree dell'Australia centrale e dell'Australia centrale 2, in quanto sono progettate e gestite in modo specifico per le esigenze degli enti pubblici.

Altre informazioni sulla natura speciale delle aree australiane di Azure sono disponibili in [aree Australia centrale di Azure](https://azure.microsoft.com/global-infrastructure/australia/).

## <a name="how-microsoft-separates-classified-and-official-data"></a>Come Microsoft separa i dati classificati e ufficiali

Microsoft gestisce Azure e Office 365 in Australia come se tutti i dati fossero sensibili e/o classificati, elevando i controlli di sicurezza a tale barra alta.

L'infrastruttura che supporta Azure è potenzialmente utile per i dati di più classificazioni.  Tuttavia, come presupposto è che i dati dei clienti siano classificati, i controlli appropriati sono disponibili. Microsoft ha adottato un comportamento di sicurezza di base per i servizi conformi ai requisiti di PSPF per archiviare ed elaborare informazioni riservate **protette** .

Per assicurare ai clienti che un tenant in Azure non è a rischio di altri tenant, Microsoft implementa controlli completi di difesa in profondità.

Oltre alle funzionalità implementate all'interno della piattaforma Microsoft Azure, i controlli configurabili del cliente aggiuntivi, ad esempio la crittografia con chiavi gestite dal cliente, la virtualizzazione nidificata e l'accesso amministrativo JIT, possono ridurre ulteriormente il rischio. All'interno delle aree di Azure per enti pubblici di Canberra, un processo per l'elenco degli elementi consentiti formale solo australiano & Nuova Zelanda per enti pubblici e organizzazioni di infrastrutture critiche nazionali è in atto. Questo cloud della community fornisce garanzie aggiuntive alle organizzazioni sensibili ai rischi per i cotenant.

Il report Microsoft Azure certificazione **protetta** conferma che questi controlli sono efficaci per l'archiviazione e l'elaborazione dei dati classificati **protetti** e il relativo isolamento.

## <a name="relevance-of-irapccsl-to-state-government-and-critical-infrastructure-providers"></a>Pertinenza di IRAP/CCSL per lo stato di enti pubblici e provider di infrastruttura critici

Molti provider di infrastrutture governativi e di importanza critica incorporano i requisiti del governo federale nel Framework di sicurezza e di garanzia. Queste **organizzazioni gestiscono**anche il **sito ufficiale, ufficiale: Sensibili** e una certa quantità di dati classificati **protetti** , dall'interazione con il governo federale o dal proprio diritto.

Il governo australiano si concentra sempre più sui criteri e la legislazione sulla protezione dei dati non governativi che influiscano fondamentalmente sulla sicurezza e sulla prosperità economica dell'Australia. Di conseguenza, le aree di Azure in Australia e la certificazione CCSL sono rilevanti per tutti i settori.

![Settori di infrastruttura critici](media/nci-sectors.png)

Le certificazioni Microsoft dimostrano che i servizi di Azure sono stati sottoposti a una valutazione completa, rigorosa e formale delle protezioni per la sicurezza e che sono stati approvati per la gestione di dati estremamente sensibili.

## <a name="location-and-control-of-microsoft-data-centres"></a>Posizione e controllo dei Data Center Microsoft

Si tratta di un requisito obbligatorio per enti pubblici e infrastrutture critiche per la conoscenza esplicita della posizione e della proprietà dei Data Center per i servizi cloud che elaborano i dati.  Microsoft è un provider di servizi cloud con iperscalabilità in cui vengono fornite informazioni dettagliate su queste località e proprietà.

Le aree di Microsoft Azure Australia (Australia centrale e Australia centrale 2) vengono gestite all'interno delle strutture dei data center CDC.  La proprietà dei data center CDC è controllata in Australia con la proprietà del 48% del Commonwealth Pensione Corporation, il 48% di proprietà di infratl (una sede NZ, doppio australiano e una nuova Zelanda Stock Exchange, elenco a lungo termine dell'infrastruttura a lungo termine) e la gestione australiana del 4%.  

La gestione dei data center CDC prevede garanzie contrattuali con il governo australiano che limita il trasferimento futuro di proprietà e controllo. Questa trasparenza della supply chain e della proprietà attraverso la partnership di Microsoft con i datacenter CDC è in linea con i principi dell' [intera strategia di hosting per enti pubblici](https://www.dta.gov.au/our-projects/whole-government-hosting-strategy) e la definizione di un data **Center sovrano certificato**.

## <a name="the-azure-services-that-are-included-in-the-current-ccsl-certification"></a>I servizi di Azure inclusi nella certificazione CCSL corrente

Nel 2017 giugno, i servizi ACSC Certified 41 di Azure per l'archiviazione e l'elaborazione dei **dati in unclassifiedd: Livello** DLM. Ad aprile 2018, il 24 di questi servizi venivano certificati per i dati classificati **protetti** .

La disponibilità dei servizi di Azure certificati da ACSC nelle aree di Azure in Australia è la seguente (quelli in grassettosono certificati presso Protected):

|Aree geografiche dell'Australia centrale per Azure|Servizi non a livello di area e altre aree|
|---|---|
|Gestione API, gateway app, Servizi per le applicazioni, **automazione, portale di Azure, backup, batch, servizi cloud**, Cosmos DB, Hub eventi, **ExpressRoute**, HDInsight, **Key Vault**, Load Balancer, log Analytics, **a più fattori Autenticazione**, cache Redis, **Gestione risorse, bus di servizio, Service Fabric, Site Recovery, database SQL, archiviazione**, gestione traffico, **macchine virtuali, rete virtuale, gateway VPN**|**Azure Active Directory**, rete CDN, Data Catalog, **importazione/esportazione, Information Protection, hub**Internet, Machine Learning, servizi multimediali, **Hub di notifica**, Power bi, **Centro sicurezza, utilità di pianificazione**, ricerca, analisi di flusso|
|

Microsoft pubblica una [Panoramica della conformità Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/44/Microsoft%20Azure%20Compliance%20Offerings.pdf) che elenca tutti i servizi inclusi nell'ambito per tutti i processi di conformità e valutazione globali, governativi, di settore e regionali che Azure passa attraverso, tra cui IRAP/CCSL.

## <a name="azure-service-not-listed-or-assessed-at-a-lower-level-than-i-need"></a>Il servizio di Azure non è elencato o valutato a un livello inferiore rispetto a quanto necessario

Servizi non certificati o certificati presso **il sito ufficiale: Sensibile** ma non **protetto**, può essere usato insieme o come parte di una soluzione che ospita dati **protetti** purché i servizi siano:
1. non archiviare o elaborare dati **protetti** decrittografati
1. è stata completata una valutazione dei rischi e il servizio è stato approvato per archiviare manualmente i dati **protetti** .

Se si vuole usare un servizio che non è incluso in CCSL per archiviare ed elaborare i dati **ufficiali** , è possibile, ma per ISM è necessario inviare una notifica al ACSC per la scrittura, prima di immettere o rinnovare un contratto con un provider di servizi cloud.

Tutti i servizi usati da un'agenzia per i carichi di lavoro **protetti** devono comunque essere valutati e approvati in linea con i processi descritti in ISM e il processo di valutazione IRAP gestito dall'Agenzia nella [strategia DTA Secure Cloud](https://www.dta.gov.au/files/cloud-strategy/secure-cloud-strategy.pdf).

![Processo di certificazione della strategia cloud protetta DTA](media/certification.png)

Microsoft valuta costantemente i nostri servizi per garantire che la piattaforma sia protetta e adatta ai propri scopi per l'uso da parte del governo australiano, quindi rivolgersi a Microsoft se è necessaria assistenza per un servizio che non si trova attualmente nella CCSL di **protezione**.

Poiché Microsoft dispone di una vasta gamma di Servizi certificati sulla CCSL sia per il **DLM non classificato** che per le classificazioni **protette** , ISM richiede la valutazione IRAP dei servizi almeno ogni due anni. Microsoft accetta una valutazione annuale, che è anche la possibilità di includere servizi aggiuntivi da considerare.

## <a name="certified-protected-gateway-in-azure"></a>Gateway protetto certificato in Azure

Microsoft non gestisce un gateway Internet sicuro certificato per enti pubblici a causa di restrizioni relative al numero di firme consentite nel programma di consolidamento del gateway.  Tuttavia, le funzionalità previste e necessarie di un SIG possono essere configurate all'interno Microsoft Azure.

Attraverso la certificazione **protetta** dei servizi di Azure, acsc offre raccomandazioni specifiche per le agenzie per la connessione ad Azure e quando si implementa la segmentazione di rete tra domini di sicurezza, ad esempio tra **protected** e Internet. Questi consigli includono l'uso di gruppi di sicurezza di rete, firewall e reti private virtuali.  Il ACSC consiglia l'uso di un appliance del gateway virtuale. In Azure sono disponibili diverse appliance virtuali che hanno un equivalente fisico nell'elenco di prodotti con valutazione ASD oppure sono state valutate rispetto ai profili di protezione dei criteri comuni e sono elencate nel portale dei criteri comuni. Questi prodotti vengono riconosciuti da ASD come firmatario della convenzione di riconoscimento dei criteri comuni (CCRA).

Microsoft ha prodotto materiale sussidiario sull'implementazione di funzionalità basate su Azure che forniscono le funzioni di sicurezza necessarie per proteggere il confine tra domini di sicurezza diversi, che, quando combinati, formano l'equivalente a un SIG certificato. Sono disponibili numerosi partner che possono contribuire alla progettazione e all'implementazione di queste funzionalità, oltre a una serie di soluzioni partner disponibili che fanno lo stesso.

## <a name="security-clearances-and-citizenship-of-microsoft-support-personnel"></a>Spazi di sicurezza e cittadinanza del personale del supporto tecnico Microsoft

Microsoft gestisce i servizi a livello globale con il personale di sicurezza sottoposto a screening e con formazione.  Il personale che ha accesso fisico non accompagnato alle strutture di Sydney e Melbourne dispone di autorizzazioni di sicurezza di base per il governo australiano. Il personale nelle aree dell'Australia centrale e dell'Australia centrale 2 presenta autorizzazioni minime di vagliatura 1 (NV1) (in base alle esigenze dei dati **segreti** ). Questo garantisce ai clienti un'ulteriore garanzia che il personale all'interno dei data center che opera in Azure è altamente attendibile.

Microsoft dispone di un criterio di accesso in base zero con accesso concesso tramite un sistema di amministrazione just-in-time e just-enough in base ai controlli degli accessi in base al ruolo. Nella maggior parte dei casi, gli amministratori non necessitano di accesso o privilegi ai dati dei clienti per la risoluzione dei problemi e la gestione del servizio.  L'automazione e la creazione di script di attività per l'esecuzione remota negano la necessità di accesso diretto ai dati dei clienti.

Il reparto dell'avvocato generale ha confermato che i criteri e le procedure di sicurezza del personale di Microsoft in Azure sono coerenti con l'intento di PSPF l'accesso alle informazioni in INFOSEC-9.

## <a name="storing-international-traffic-of-arms-regulations-itar-or-export-administration-regulations-ear-data"></a>Archiviazione del traffico internazionale delle normative sulle armi (ITAR) o dei dati delle normative di esportazione (EAR)

I controlli tecnici di Azure che assistono i clienti che soddisfano gli obblighi per i dati controllati dall'esportazione sono gli stessi a livello globale in Azure. Si tratta di un aspetto importante, perché non è presente un Framework di valutazione e certificazione formale per i dati controllati dall'esportazione.

Per gli enti pubblici degli Stati Uniti per la difesa per Azure Government e Office 365, abbiamo messo a disposizione misure aggiuntive contrattuali ed elaborate per supportare i clienti soggetti a controlli di esportazione. Le clausole contrattuali aggiuntive e il supporto nazionale garantito e l'amministrazione delle aree di Azure non sono destinate all'Australia.

Ciò non significa che non è possibile usare Azure in Australia per ITAR/EAR, ma è necessario comprendere chiaramente le restrizioni imposte tramite la licenza di esportazione ed è necessario implementare protezioni aggiuntive per soddisfare tali obblighi prima di usare Azure per archiviare dati. Ad esempio, potrebbe essere necessario compilare la nazionalità come attributo in Azure Active Directory, utilizzare Azure Information Protection per applicare le regole di crittografia sui dati e limitarle solo a Microsoft e alle altre nazionalità incluse nella licenza di esportazione. crittografare tutti i dati in locale prima di archiviarli in Azure, usando la chiave del cliente o contenere la propria chiave per i dati di ITAR e l'elenco viene eseguito......

Poiché ITAR non è una certificazione formale, è necessario comprendere quali sono le restrizioni e le limitazioni associate alla licenza di esportazione e quindi usare se sono presenti controlli sufficienti in Azure per soddisfare tali requisiti. In questo caso, uno dei problemi da prendere in considerazione è l'accesso da parte dei tecnici che non possono essere una cittadinanza approvata per la licenza di esportazione.

## <a name="next-steps"></a>Passaggi successivi

Vedere l'articolo sul [gateway VPN di Azure](vpn-gateway.md) per la configurazione conforme a ISM e l'implementazione della connettività VPN ad Azure Australia
