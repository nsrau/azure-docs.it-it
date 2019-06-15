---
title: Proteggere l'architettura di elaborazione di Azure
description: Questa architettura di riferimento per un'architettura di rete Perimetrale a livello aziendale Usa Appliance virtuali di rete e altro ancora. Questa architettura è stata progettata per soddisfare il dipartimento della difesa Secure Cloud Computing architettura funzionale. Può essere utilizzato anche per qualsiasi organizzazione. Questo riferimento include due opzioni automatizzati che usano Appliance Citrix o F5.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: 017a26d5672f666d4d8eaf629a0f53fe0cfe517f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65963235"
---
# <a name="secure-azure-computing-architecture"></a>Proteggere l'architettura di elaborazione di Azure

Dati Dipartimento della difesa (DoD) gli utenti che distribuiscono i carichi di lavoro in Azure hanno richiesto informazioni aggiuntive configurare le reti virtuali protette e configurare gli strumenti di sicurezza e i servizi che vengono stabiliti dalle procedure consigliate e gli standard DoD. 

Difesa informazioni sistema Agency (DISA) pubblicato il [documento di requisiti funzionali Secure Cloud Computing Architecture (SCCA) (FRD)](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf) nel 2017. SCCA descrive gli obiettivi funzionali per la protezione di difesa informazioni sistema della rete (DISN) e cloud commerciale di punti di connessione provider. SCCA descrive anche come proprietari mission sicuri cloud le applicazioni in corrispondenza del limite di connessione. Tutte le entità che si connette al cloud commerciale DoD devono seguire le linee guida stabilite nel FRD SCCA.
 
Il SCCA comprende quattro componenti:
 
- Punto di accesso di Cloud di limiti (BCAP)
- Virtual Datacenter Security Stack (VDSS)
- Data Center virtuale di Managed Service (programmi VDM)
- Trusted Cloud Credential Manager (TCCM) 

Microsoft ha sviluppato una soluzione che soddisfi i requisiti di SCCA per carichi di lavoro sia IL4 IL5 eseguiti in Azure. Questa soluzione specifici di Azure viene chiamata proteggere Azure Computing Architecture (SACA). I clienti che distribuiscono SACA sono conformi i FRD SCCA. È possibile abilitare i clienti DoD spostare i carichi di lavoro in Azure dopo che sono connessi.

Architetture e materiale sussidiario SCCA sono specifiche per i clienti DoD, ma le ultime revisioni ai clienti civili help SACA conformarsi alle indicazioni di connessione (TIC) internet attendibile. Ultime revisioni aiuta anche i clienti commerciali che vogliono implementare una rete Perimetrale sicura per proteggere i propri ambienti Azure.


## <a name="secure-cloud-computing-architecture-components"></a>Proteggere i componenti dell'architettura di Cloud Computing

### <a name="bcap"></a>BCAP

Lo scopo del BCAP consiste nella protezione di DISN da attacchi che hanno origine nell'ambiente cloud. BCAP esegue la prevenzione e rilevamento delle intrusioni. inoltre filtra il traffico non autorizzato. Questo componente può essere con percorso condiviso con altri componenti del SCCA. È consigliabile distribuire questo componente usando l'hardware fisico. Nella tabella seguente sono elencati i requisiti di sicurezza BCAP.

#### <a name="bcap-security-requirements"></a>Requisiti di sicurezza BCAP

![Matrice dei requisiti BCAP](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

Lo scopo del VDSS consiste nella protezione delle applicazioni mission-owner DoD ospitate in Azure. Il SCCA VDSS esegue la maggior parte delle operazioni di sicurezza. Essa condotte in relazione il controllo del traffico per proteggere le applicazioni eseguite in Azure. Questo componente può essere fornito all'interno dell'ambiente Azure.

#### <a name="vdss-security-requirements"></a>Requisiti di sicurezza VDSS

![Matrice dei requisiti VDSS](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

Lo scopo di programmi VDM consiste nel fornire la protezione dell'host e i data center servizi condivisi. Le funzioni di programmi VDM possono eseguire nell'hub del SCCA oppure il proprietario missione possibile distribuire parti di esso nella propria sottoscrizione di Azure specifica. Questo componente può essere fornito all'interno dell'ambiente Azure.

#### <a name="vdms-security-requirements"></a>Requisiti di sicurezza di programmi VDM

![Matrice dei requisiti di programmi VDM](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM è un ruolo di business. Questo utente è responsabile della gestione di SCCA. I compiti sono le seguenti: 

- Definire piani e i criteri di accesso all'account per l'ambiente cloud. 
- Assicurarsi che Gestione delle identità e accesso funziona correttamente. 
- Mantenere il piano di gestione delle credenziali Cloud. 

Questa persona è nominata dal responsabile dell'autorizzazione. Il BCAP VDSS e programmi VDM forniscono le funzionalità necessarie di TCCM a svolgere le proprie.

#### <a name="tccm-security-requirements"></a>Requisiti di sicurezza TCCM

![Matrice dei requisiti TCCM](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>Componenti SACA e considerazioni sulla pianificazione 

L'architettura di riferimento SACA è progettato per distribuire i componenti VDSS e programmi VDM in Azure e abilitare il TCCM. Questa architettura è modulare. Tutte le parti di VDSS e programmi VDM possono risiedere in un hub centralizzato. Alcuni controlli possono essere soddisfatti nel spazio mission-owner o addirittura in locale. Microsoft consiglia di condivisione percorso i componenti VDSS e programmi VDM in una rete virtuale centrale in grado di connettersi tramite tutti i proprietari di importanza. Il diagramma seguente mostra questa architettura: 


![Diagramma dell'architettura di riferimento SACA](media/sacav2generic.png)

Quando si pianifica la strategia di conformità SCCA e l'architettura tecnica, prendere in considerazione i seguenti argomenti dall'inizio perché ciò influisce sulla ogni cliente. I problemi seguenti hanno ideare clienti DoD e tendono a rallentare a pianificazione e l'esecuzione. 

#### <a name="which-bcap-will-your-organization-use"></a>Quali BCAP userà l'organizzazione?
   - DISA BCAP:
        - DISA ha due BCAPs operative di pentagono e Camp Roberts, autorità di certificazione. Una terza il futuro è prevista in linea a breve. 
        - BCAPs di DISA che tutto avere circuiti Azure ExpressRoute per Azure, che può essere usato dai clienti DoD per la connettività. 
        - DISA dispone di una sessione di peering Microsoft a livello aziendale per i clienti DoD che desidera eseguire la sottoscrizione al software Microsoft come strumenti un servizio (SaaS), ad esempio Office 365. Utilizzando il BCAP DISA, è possibile abilitare la connettività e il peering all'istanza di SACA. 
    - Compilare il proprio BCAP:
        - Questa opzione richiede di lease di spazio in un centro dati con percorso condiviso e configurare un circuito ExpressRoute in Azure. 
        - Questa opzione richiede un'approvazione aggiuntiva. 
        - A causa di un'approvazione aggiuntiva e una compilazione fisica orizzontale, questa opzione richiede più tempo. 
    - È consigliabile usare il BCAP DISA. Questa opzione è immediatamente disponibile, è la ridondanza incorporata e con i clienti che operano su di esso attualmente nell'ambiente di produzione.
- Spazio di IP instradabile DoD:
    - È necessario usare lo spazio degli IP instradabile DoD di confine. L'opzione per usare NAT per la connessione di tali spazi a spazio IP privato in Azure è disponibile.
    - Contattare il dipartimento della difesa informazioni Center scheda di rete per ottenere spazio di IP. È necessario come parte dell'invio del processo di approvazione del sistema/di rete (SNAP) con DISA. 
    - Se si prevede di utilizzare NAT per la connessione di spazio di indirizzi privato in Azure, è necessario un minimo di/24 subnet dello spazio degli indirizzi assegnato dall'interfaccia di rete per ogni area in cui si prevede di distribuire SACA.
- Ridondanza:
    - Distribuire un'istanza SACA almeno due aree. Nel cloud, DoD di distribuirlo in entrambe le aree DoD disponibili.
    - Connettersi a almeno due BCAPs attraverso i circuiti ExpressRoute separati. Entrambe le connessioni ExpressRoute possono quindi essere collegate all'istanza di ogni area geografica SACA. 
- Requisiti di specifiche del componente DoD:
    - L'organizzazione dispone di requisiti specifici di fuori di requisiti SCCA? Alcune organizzazioni hanno requisiti di indirizzi IP specifici.
- SACA è un'architettura modulare:
    - Usare solo i componenti che necessari per l'ambiente. 
        - Distribuire Appliance virtuali di rete in un singolo livello o a più livelli.
        - Usare gli indirizzi IP integrato o bring-your-own gli indirizzi IP.
- Livello di impatto DoD di applicazioni e dati:
    - Se vi è una possibilità delle applicazioni in esecuzione in aree geografiche Microsoft IL5, compilare l'istanza SACA nel IL5. L'istanza può essere utilizzata davanti alle applicazioni IL4 e IL5. Un'istanza di IL4 SACA davanti a un'applicazione IL5 molto probabilmente non riceverà il riconoscimento.

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>Il fornitore di appliance virtuali di rete utilizzerà per VDSS?
Come accennato in precedenza, è possibile compilare questo riferimento SACA usando un'ampia gamma di dispositivi e servizi di Azure. Microsoft ha automatizzato modelli di soluzioni per distribuire l'architettura SACA con F5 e Citrix. Queste soluzioni sono descritte nella sezione seguente.

#### <a name="which-azure-services-will-you-use"></a>Quali servizi di Azure verrà usato?
- Sono disponibili servizi di Azure che possono soddisfare i requisiti per la funzionalità ID log analitica e protezione basata su host. È possibile che alcuni servizi non sono disponibili a livello generale nelle aree IL5 Microsoft. In questo caso, si potrebbe essere necessario usare gli strumenti di terze parti, se questi servizi di Azure non soddisfano i requisiti. Esaminare l'opportunità di utilizzare dagli strumenti nativi di Azure e gli strumenti che con cui hai dimestichezza.
- È consigliabile usare tanti strumenti nativi Azure possibili. Si sta compilate con particolare attenzione alla sicurezza cloud e si integrano facilmente con il resto della piattaforma Azure. Usare gli strumenti nativi di Azure nell'elenco seguente per soddisfare i diversi requisiti di SCCA:

    - [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Insieme di credenziali chiave Azure](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Gateway applicazione Azure](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Firewall di Azure](https://docs.microsoft.com/azure/firewall/overview) 
    - [Porta d'ingresso Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Gruppi di sicurezza di Azure](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- Ridimensionamento
    - È necessario completare un esercizio di ridimensionamento. Esaminare il numero di connessioni simultanee, che è possibile tramite l'istanza SACA e i requisiti di velocità effettiva di rete. 
    - Questo passaggio è fondamentale. Consente di ridimensionare le macchine virtuali e identificare le licenze necessarie di fornitori diversi che è utilizzare nell'istanza di SACA. 
    - Impossibile eseguire un'analisi dei costi ottimale senza questo esercizio di ridimensionamento. Dimensionamento corretto consente inoltre di ottenere prestazioni ottimali. 


## <a name="most-common-deployment-scenario"></a>Scenario più comune di distribuzione

 Molti clienti Microsoft sono passati tramite la distribuzione completa o al minimo le fasi di pianificazione dei propri ambienti SACA. Le proprie esperienze rivelate approfondite lo scenario di distribuzione più comune. Il diagramma seguente illustra l'architettura più comune: 


![Diagramma dell'architettura di riferimento SACA](media/sacav2commonscenario.png) 


Come può notare dal diagramma, i clienti DoD in genere sottoscrivono due delle BCAPs baseline DISA. Uno di questi si trova sulla costa occidentale degli Stati e altri vantaggi della costa orientale. Un peer privato di ExpressRoute è abilitato per Azure in ogni posizione DISA BCAP. Questi peer di ExpressRoute vengono quindi collegati al gateway di rete virtuali nelle aree DoD East e Azure centrali DoD. Un'istanza SACA viene distribuita nelle aree DoD East e Azure centrali DoD. Tutto il traffico in ingresso e in uscita flussi tramite quest'ultimo da e verso la connessione ExpressRoute il BCAP DISA.

Le applicazioni mission-owner quindi scegliere le aree di Azure in cui si prevede di distribuire le proprie applicazioni. Usano per la connessione di rete virtuale della propria applicazione per la rete virtuale SACA peering di rete virtuale. Quindi si forza tunnel tutto il traffico attraverso l'istanza VDSS.

Questa architettura è consigliabile poiché soddisfi i requisiti di SCCA. È facilmente scalabile e a disponibilità elevata. Semplifica inoltre la distribuzione e gestione.

## <a name="automated-saca-deployment-options"></a>Opzioni di distribuzione automatizzate SACA

 Come accennato in precedenza, Microsoft ha collaborato con fornitori di due per creare un modello di infrastruttura SACA automatico. Entrambi i modelli di distribuiscono componenti di Azure seguenti: 

- Rete virtuale SACA
    - Subnet di gestione
        - Questa subnet è in cui vengono distribuiti servizi e macchine virtuali di gestione, noto anche come finestre di salto.
        - Subnet di programmi VDM
            - Questa subnet è in cui vengono distribuiti le macchine virtuali e i servizi utilizzati per programmi VDM.
        - Subnet e non attendibile
            - Sono queste subnet in cui vengono distribuiti dispositivi di rete.
        - Subnet gateway
            - Questa subnet è in cui viene distribuito il ExpressRoute Gateway.
- Macchine virtuali di gestione jump finestra
    - E vengono usati per la gestione fuori banda dell'ambiente.
- Appliance virtuali di rete
    - Si usa entrambi Citrix o F5 basato sul modello di cui si distribuisce.
- Indirizzi IP pubblici
    - E vengono usati per il front-end fino a quando non viene portato online ExpressRoute. Questi indirizzi IP vengono convertite in back-end spazio di indirizzi privato di Azure.
- Tabelle di route 
    - Applicato durante l'automazione, questi indirizzare tabelle forza tunnel tutto il traffico attraverso l'appliance virtuale.
- Servizi di bilanciamento del carico di Azure - SKU Standard
    - Vengono utilizzati per bilanciare il carico tra i dispositivi.
- Gruppi di sicurezza di rete
    - Vengono utilizzati per controllare quali tipi di traffico possono attraversare per determinati endpoint.


### <a name="citrix-saca-deployment"></a>Distribuzione di Citrix SACA

Un modello di distribuzione di Citrix distribuisce due livelli di Appliance di ADC Citrix a disponibilità elevata. Questa architettura soddisfi i requisiti di VDSS. 

![Diagramma di Citrix SACA](media/citrixsaca.png)


Per la documentazione di Citrix e script di distribuzione, vedere [questo collegamento a GitHub](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca).


 ### <a name="f5-saca-deployment"></a>Distribuzione di F5 SACA

Due modelli di distribuzione di F5 separati interessano due architetture diverse. Il primo modello presenta un solo livello di F5 Appliance in una configurazione a disponibilità elevata attivo-attivo. Questa architettura soddisfi i requisiti per VDSS. Il secondo modello aggiunge un secondo livello di F5s a disponibilità elevata attivo-attivo. Questo secondo livello consente ai clienti di aggiungere i propri indirizzi IP separati da F5 tra i livelli di F5. Non tutti i componenti del dipartimento della difesa hanno indirizzi IP specifici prescritto per l'uso. In tal caso, l'unico livello di F5 Appliance funziona per la maggior parte perché tale architettura include gli indirizzi IP nei dispositivi F5.

![Diagramma di F5 SACA](media/f5saca.png)

Per la documentazione di F5 e script di distribuzione, vedere [questo collegamento a GitHub](https://github.com/f5devcentral/f5-azure-saca).












