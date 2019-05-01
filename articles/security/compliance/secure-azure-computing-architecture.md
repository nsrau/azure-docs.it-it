---
title: Proteggere l'architettura di calcolo Azure
description: Si tratta di un'architettura di riferimento per un'architettura di rete Perimetrale a livello aziendale, che usano Appliance virtuali di rete e altri strumenti. Questa architettura è stata progettata per soddisfare il dipartimento della difesa Secure Cloud Computing architettura funzionale. Tuttavia, può essere sfruttata per qualsiasi organizzazione. Questo riferimento include due opzioni automatizzate usando le Appliance di Citrix o F5.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: f2e3d72db3f29dbc6d03b3259acb18daf684fb12
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917598"
---
# <a name="secure-azure-computing-architecture"></a>Proteggere l'architettura di calcolo Azure

Linee guida di configurazione di reti virtuali protette e la configurazione di strumenti di sicurezza e i servizi stipulati dal dipartimento della difesa standard e procedure consigliate hanno richiesto un numero sempre maggiore di clienti DoD distribuiscono carichi di lavoro in Azure. DISA pubblicato il [documento di requisiti funzionali Secure Cloud Computing Architecture (SCCA)](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf) nel 2017. SCCA descrive gli obiettivi funzionali per la protezione di difesa informazioni sistema della rete (DISN) e connessione al Provider di Cloud commerciale di punta e la modalità di importanza ai proprietari di applicazioni cloud in sicurezza in corrispondenza del limite di connessione. Si è obbligatoria che tutte le entità che si connette al cloud commerciale DoD segue le linee guida stabilite nel FRD SCCA.
 
Esistono quattro componenti del SCCA. Il punto di accesso di Cloud di limiti (BCAP), Stack di sicurezza di Data Center virtuale (VDSS), Data Center virtuale (programmi VDM) di servizi gestiti e attendibili Gestione credenziali Cloud (TCCM). Microsoft ha sviluppato una soluzione che soddisfa i requisiti di SCCA per i carichi di lavoro sia IL4 IL5 in esecuzione in Azure. Questa soluzione di Azure specifica viene chiamata proteggere Azure Computing Architecture (SACA). I clienti che distribuiscono SACA saranno conformi i FRD SCCA e consentiranno ai clienti DoD di spostare i carichi di lavoro in Azure una volta connessi. 

Mentre le architetture e materiale sussidiario SCCA sono specifiche per i clienti DoD, le revisioni più recente da SACA consentirà inoltre civili i clienti di essere conformi alle indicazioni di connessione (TIC) internet attendibile, oltre che i clienti commerciali che vogliono implementare una rete Perimetrale sicura per proteggere i propri ambienti azure.


## <a name="secure-cloud-computing-architecture-components"></a>Proteggere i componenti dell'architettura di Cloud Computing

**BCAP**

Lo scopo del BCAP avviene per impedire la DISN attacchi provenienti nell'ambiente cloud. BCAP verrà eseguire prevenzione e rilevamento delle intrusioni, nonché filtrare il traffico non autorizzato. Questo componente può essere con percorso condiviso con altri componenti del SCCA. È consigliabile che questo componente viene distribuito usando l'hardware fisico. Di seguito si noterà che l'elenco dei requisiti di sicurezza BCAP.

***Requisiti di sicurezza BCAP***

![Matrice dei requisiti BCAP](media/bcapreqs.png)


**VDSS**

Lo scopo del VDSS consiste nella protezione delle applicazioni di DoD Mission proprietario che sono ospitate in Azure. Il SCCA VDSS esegue la maggior parte delle operazioni di sicurezza. Eseguirà il controllo del traffico per proteggere le applicazioni in esecuzione in Azure. Questo componente può essere fornito all'interno dell'ambiente Azure.

***Requisiti di sicurezza VDSS***

![Matrice dei requisiti VDSS](media/vdssreqs.png)

**VDMS**

Lo scopo di programmi VDM consiste nel fornire la protezione dell'host, nonché i data center servizi condivisi. Le funzioni di programmi VDM possono eseguire nell'hub del SCCA oppure il proprietario missione possibile distribuire parti di esso nella propria sottoscrizione di Azure specifica. Questo componente può essere fornito all'interno dell'ambiente Azure.

***Requisiti di sicurezza di programmi VDM***

![Matrice dei requisiti di programmi VDM](media/vdmsreqs.png)


**TCCM**

TCCM è un ruolo di business. Questo utente sarà responsabile per la gestione di SCCA. I compiti includono la definizione di piani e i criteri per l'accesso all'account nell'ambiente cloud, garantendo l'identità e gestione degli accessi stia funzionando correttamente e per mantenere la gestione delle credenziali Cloud prevede. Questa persona è designata dall'autorizzazione ufficiale. Il BCAP VDSS e programmi VDM fornirà le funzionalità necessarie per il TCCM eseguire la propria funzione di processo.

***Requisiti di sicurezza TCCM***

![Matrice dei requisiti TCCM](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>Componenti SACA e considerazioni sulla pianificazione 

L'architettura di riferimento SACA è progettato per distribuire i componenti VDSS e programmi VDM in azure, nonché di abilitare il TCCM. Questa architettura è modulare, il che significa che tutte le parti di VDSS e programmi VDM può risiedere in un hub centralizzato o alcuni dei controlli possono essere soddisfatti nello spazio di proprietario di importanza o addirittura in locale. Del nostro team Microsoft consiglia di condividere il percorso dei componenti VDSS e programmi VDM in una centrale virtuale Net in grado di connettersi tramite tutti i proprietari di importanza. Il diagramma seguente illustra l'architettura consigliata. 


![Diagramma dell'architettura di riferimento SACA](media/sacav2generic.png)

Quando si pianifica la strategia di conformità SCCA e l'architettura tecnica, esistono molti aspetti da considerare. È importante che gli argomenti seguenti vengono prese in esame sin dall'inizio, perché ogni cliente dovrà vengono illustrati questi scenari. Gli argomenti seguenti sono stati problemi che hanno ideare clienti DoD reali e tendono a rallentare la pianificazione e l'esecuzione. 

- Quali BCAP userà l'organizzazione?
    - DISA BCAP
        - DISA ha due BCAPs operativa al Pentagon e CA Roberts Camp, con una terza presto online. 
        - BCAPs di DISA che tutto avere circuiti di ExpressRoute in Azure, che può essere sfruttata dai clienti DoD per la connettività. 
        - DISA ha già una sessione di Peering Microsoft a livello aziendale per i clienti DoD che desidera eseguire la sottoscrizione agli strumenti Microsoft SaaS, ad esempio Office 365. Usando DISA BCAP, è possibile abilitare la connettività e il peering all'istanza di SACA. 
    - Compilare il proprio BCAP
        - Ciò richiede spazio in un centro dati con percorso condiviso di lease e configurare un circuito ExpressRoute in Azure. 
        - Questa opzione richiederanno un'approvazione aggiuntiva 
        - A causa di un'approvazione aggiuntiva e una compilazione fisica orizzontale, questa opzione richiede più tempo. 
    - È consigliabile utilizzare il BCAP DISA. Questa opzione è immediatamente disponibile, è compilato in ridondanza e dispone già di clienti che operano su di esso attualmente nell'ambiente di produzione.
- Spazio degli IP instradabile DoD
    - È necessario usare lo spazio degli IP instradabile DoD di confine. L'opzione per NAT quelli a spazio IP privato in Azure è disponibile.  
    - Contattare il dipartimento della difesa NIC per ottenere spazio di IP, sarà necessaria durante l'invio di SNAP con DISA. 
    - Se si prevede di NAT allo spazio di indirizzi privati in Azure, è necessario un minimo di/24 subnet dello spazio degli indirizzi assegnato dall'interfaccia di rete per ogni area in cui si prevede di distribuire SACA. 
- Ridondanza 
    - Si consiglia di distribuire un'istanza SACA in almeno due aree. In cloud DoD, ciò significa che viene distribuita in entrambe le aree DoD disponibili. 
    - È inoltre consigliabile connettersi almeno due BCAPs attraverso i circuiti ExpressRoute separati. Entrambe le route Express può quindi essere collegate all'istanza di ogni area geografica SACA. 
- Requisiti specifici del componente DoD
    - L'organizzazione dispone di requisiti specifici di fuori di requisiti SCCA? (Alcune organizzazioni hanno requisiti specifici di indirizzi IP)
- SACA è un'architettura modulare  
    - Usare solo i componenti necessari per l'ambiente. 
        - Distribuire Appliance virtuali di rete in un singolo livello o a più livelli
        - Gli indirizzi IP integrato o portare i propri indirizzi IP
- Livello di impatto DoD di applicazioni e dati
    - Se vi è una possibilità delle applicazioni in esecuzione in altre aree IL5, è consigliabile creare l'istanza SACA nel IL5. L'istanza può essere utilizzata davanti IL4 applicazioni nonché IL5. Tuttavia, un'istanza di IL4 SACA davanti a un'applicazione IL5 molto probabilmente non riceverà il riconoscimento. 
- Qual è il fornitore Appliance di rete virtuale si userà per VDSS?
    - Come accennato in precedenza, questo riferimento SACA può essere compilato utilizzando un'ampia gamma di dispositivi e servizi di Azure. Tuttavia, sono disponibili modelli di soluzione automatizzata per distribuire l'architettura SACA con F5 e Citrix. Queste soluzioni verranno trattate in dettaglio più avanti. 
- Quali servizi di Azure verrà usato?
    - Sono disponibili servizi di Azure che possono soddisfare i requisiti per il log analitica, protezione basata su host e la funzionalità ID. Tuttavia, è possibile che alcuni servizi non sono disponibili a livello generale in altre aree IL5. Ciò potrebbe causare la necessità di usare alcuni strumenti di terze parti 3rd se questi servizi di Azure non soddisfano il requisito. È necessario esaminare dagli strumenti sono dimestichezza e l'opportunità di utilizzare dagli strumenti nativi di Azure. 
    - È consigliabile usare tante nativa gli strumenti di Azure come possibili non appena vengono creati con particolare attenzione alla sicurezza cloud e si integrano facilmente con il resto della piattaforma Azure. Seguito è riportato un elenco degli strumenti nativi di Azure che possono essere sfruttate per soddisfare i diversi requisiti di SCCA. 
        - [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview )
        - [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) 
        - [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
        - [Insieme di credenziali chiave Azure](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
        - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
        - [Gateway applicazione](https://docs.microsoft.com/azure/application-gateway/overview)
        - [Firewall di Azure](https://docs.microsoft.com/azure/firewall/overview) 
        - [Porta d'ingresso Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
        - [Gruppi di sicurezza](https://docs.microsoft.com/azure/virtual-network/security-overview)
        - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
        - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- Ridimensionamento
    - Sarà necessario completare un esercizio di ridimensionamento. È necessario esaminare il numero di connessioni simultanee, che è possibile tramite l'istanza SACA, nonché i requisiti di velocità effettiva di rete. 
    - Si tratta di un passaggio critico come che verrà consentono di ridimensionare le macchine virtuali, nonché contribuire a identificare le licenze che verrà richiesta di fornitori diversi, che si userà nella propria istanza SACA. 
    - Non è possibile eseguire un'analisi dei costi ottimale senza questo esercizio di ridimensionamento, è anche importante assicurarsi che tutto ciò che viene ridimensionato in modo corretto per consentire prestazioni ottimali. 


## <a name="most-common-deployment-scenario"></a>Scenario più comune di distribuzione

Microsoft dispone di numerosi clienti che hanno già stata eseguita la versione completa di distribuzione o almeno fasi dei propri ambienti SACA di pianificazione. Ciò ha permesso di comprendere lo scenario di distribuzione più comune. Il diagramma seguente illustra l'architettura più comune. 


![Diagramma dell'architettura di riferimento SACA](media/sacav2commonscenario.png) 


Come può notare dal diagramma, i clienti DoD in genere sottoscrivono due delle BCAPs baseline DISA, uno di questi vantaggi della costa occidentale e altri vantaggi della costa orientale. Un peer privato di ExpressRoute è abilitato per Azure in ogni posizione DISA BCAP. Questi peer di ExpressRoute vengono quindi collegati al Gateway di rete virtuale nel DoD East e centrale aree DoD di Azure. Un'istanza SACA viene distribuita nella regione DoD East e DoD di Azure centrale e tutte in ingresso e in uscita flussi di traffico attraverso di esso da e verso la connessione Expressroute il BCAP DISA. 

Mission-proprietario delle applicazioni, quindi scegliere quali gli acquisti di Azure che verrà distribuito le relative applicazioni in e usare Peering reti virtuali per connettere l'applicazione 's rete virtuale a rete virtuale SACA. Forzare tutto il traffico attraverso l'istanza VDSS il Tunneling. 

Questa architettura è altamente consigliata da Microsoft, perché soddisfano i requisiti di SCCA, è facilmente scalabile, a disponibilità elevata e semplifica la distribuzione e gestione.

## <a name="automated-saca-deployment-options"></a>Opzioni di distribuzione automatizzate SACA

 Si è accennato in precedenza che Microsoft ha collaborato con fornitori di due per creare un modello di infrastruttura SACA automatico. Entrambi i modelli distribuirà i seguenti componenti di Azure. 

- Rete virtuale SACA
    - Subnet di gestione
        - In cui vengono distribuiti servizi e macchine virtuali di gestione (jumpbox)
        - VDMS Subnet
            - In cui vengono distribuiti le macchine virtuali e i servizi utilizzati per programmi VDM
        - Subnet e non attendibile 
            - In cui vengono distribuiti dispositivi di rete
        - Subnet gateway
            - In cui verrà distribuito il ExpressRoute Gateway
- Macchine virtuali di gestione Jump finestra
    - Utilizzato per Out of Band Management dell'ambiente.
- Appliance virtuali di rete
    - Citrix o F5, a seconda del modello distribuzione.
- Indirizzi IP pubblici
    - Utilizzato per front-end fino a quando non viene portato online ExpressRoute. Questi indirizzi IP verrà convertito nel back-end spazio di indirizzi privato di Azure
- Tabelle di route 
    - Applicata durante l'automazione, queste tabelle di route force-tunnel tutto il traffico attraverso l'appliance virtuale
- Servizi di bilanciamento del carico di Azure - SKU Standard
    - Questi vengono usati per bilanciare il carico tra i dispositivi
- Gruppi di sicurezza di rete
    - Utilizzata per controllare quali tipi di traffico possono attraversare per determinati endpoint


**Distribuzione di Citrix SACA**

Citrix ha creato un modello di distribuzione che consente di distribuire due livelli di Appliance di ADC Citrix a disponibilità elevata. Questa architettura soddisfi i requisiti di VDSS. 

![Diagramma di Citrix SACA](media/citrixsaca.png)


Vedere la documentazione e script di distribuzione è reperibile [qui.](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca)


 **Distribuzione di F5 SACA**

F5 ha creato due modelli di distribuzione separata che coprono due architetture diverse. Il primo di un solo livello di F5 Appliance in una configurazione a disponibilità elevata attivo-attivo. Questa architettura soddisfi i requisiti per VDSS. Il secondo aggiunge un secondo livello di F5s a disponibilità elevata attivo-attivo. Lo scopo di questo secondo livello è destinato ai clienti di aggiungere i propri indirizzi IP separati da F5 tra i livelli di F5. Non tutti i componenti del dipartimento della difesa hanno indirizzi IP specifici prescritto per l'uso. Se è questo il caso, l'unico livello di Appliance F5 funzionerà per più dopo che l'architettura include gli indirizzi IP nei dispositivi F5.  

![Diagramma di Citrix SACA](media/f5saca.png)

Documentazione di F5 e script di distribuzione è reperibile [qui.](https://github.com/f5devcentral/f5-azure-saca) 












