---
title: Proteggere l'architettura di elaborazione di Azure
description: Questa architettura di riferimento per un'architettura di rete perimetrale a livello aziendale USA appliance virtuali di rete e altri strumenti. Questa architettura è stata progettata per soddisfare i requisiti funzionali per l'architettura di cloud computing protetta del reparto difesa. E può essere usato anche per qualsiasi organizzazione. Questo riferimento include due opzioni automatiche che usano le appliance Citrix o F5.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: 3a27eac3d4609f1054b0ef6a9417fe2f1ca53ae4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656635"
---
# <a name="secure-azure-computing-architecture"></a>Proteggere l'architettura di elaborazione di Azure

Dati I clienti del dipartimento della difesa (DoD) che distribuiscono carichi di lavoro in Azure hanno richiesto indicazioni per la configurazione di reti virtuali sicure e la configurazione degli strumenti e dei servizi di sicurezza previsti dagli standard e dalla pratica DoD. 

Defense Information System Agency (DISA) ha pubblicato il [documento sui requisiti funzionali SCCA (Secure Cloud Computing Architecture) (FRD)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/pdf/SCCA_FRD_v2-9.pdf) in 2017. SCCA descrive gli obiettivi funzionali per la protezione dei punti di connessione di DISN (Defense Information System Network) e del provider di servizi cloud commerciali. SCCA descrive anche il modo in cui i proprietari della missione proteggono le applicazioni cloud al limite della connessione. Ogni entità DoD che si connette al cloud commerciale deve seguire le linee guida stabilite nella SCCA FRD.
 
Il SCCA ha quattro componenti:
 
- Punto di accesso cloud limite (BCAP)
- Stack di sicurezza Virtual Datacenter (VDSS)
- Servizio gestito Virtual Datacenter (VDMS)
- Gestione credenziali cloud attendibile (TCCM) 

Microsoft ha sviluppato una soluzione che soddisfa i requisiti di SCCA per i carichi di lavoro IL4 e IL5 eseguiti in Azure. Questa soluzione specifica di Azure è denominata Secure Azure Computing Architecture (SACA). I clienti che distribuiscono SACA sono conformi alla FRD SCCA. Possono consentire ai clienti di DoD di spostare i carichi di lavoro in Azure dopo che sono connessi.

Le linee guida e le architetture di SCCA sono specifiche per i clienti di DoD, ma le revisioni più recenti di SACA aiutano i clienti civili a rispettare le linee guida per la connessione Internet attendibile. Le revisioni più recenti consentono inoltre ai clienti commerciali che vogliono implementare una rete perimetrale sicura per proteggere i propri ambienti Azure.


## <a name="secure-cloud-computing-architecture-components"></a>Componenti dell'architettura di cloud computing sicuro

### <a name="bcap"></a>BCAP

Lo scopo di BCAP è quello di proteggere il DISN da attacchi originati dall'ambiente cloud. BCAP esegue il rilevamento e la prevenzione delle intrusioni. Filtra anche il traffico non autorizzato. Questo componente può avere un percorso condiviso con altri componenti di SCCA. Si consiglia di distribuire questo componente usando l'hardware fisico. I requisiti di sicurezza di BCAP sono elencati nella tabella seguente.

#### <a name="bcap-security-requirements"></a>Requisiti di sicurezza di BCAP

![Matrice requisiti BCAP](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

Lo scopo di VDSS è quello di proteggere le applicazioni DoD Mission-Owner ospitate in Azure. VDSS esegue la maggior parte delle operazioni di sicurezza in SCCA. Esegue l'ispezione del traffico per proteggere le applicazioni in esecuzione in Azure. Questo componente può essere fornito all'interno dell'ambiente Azure.

#### <a name="vdss-security-requirements"></a>Requisiti di sicurezza di VDSS

![Matrice requisiti VDSS](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

Lo scopo di VDMS è fornire la sicurezza dell'host e i servizi data center condivisi. Le funzioni di VDMS possono essere eseguite nell'hub della SCCA o il proprietario della missione può distribuirne parti nella propria sottoscrizione di Azure specifica. Questo componente può essere fornito all'interno dell'ambiente Azure.

#### <a name="vdms-security-requirements"></a>Requisiti di sicurezza di VDMS

![Matrice requisiti VDMS](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM è un ruolo di business. Questo individuo è responsabile della gestione del SCCA. Le loro mansioni sono: 

- Definire i piani e i criteri per l'accesso dell'account all'ambiente cloud. 
- Assicurarsi che la gestione delle identità e dell'accesso funzioni correttamente. 
- Gestire il piano di gestione delle credenziali cloud. 

Questo utente viene designato dall'autorizzazione ufficiale. BCAP, VDSS e VDMS forniscono le funzionalità necessarie al TCCM per eseguire il proprio lavoro.

#### <a name="tccm-security-requirements"></a>Requisiti di sicurezza di TCCM

![Matrice requisiti TCCM](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>Considerazioni sulla pianificazione e sui componenti di SACA 

L'architettura di riferimento SACA è progettata per distribuire i componenti VDSS e VDMS in Azure e per abilitare il TCCM. Questa architettura è modulare. Tutti i componenti di VDSS e VDMS possono risiedere in un hub centralizzato. Alcuni controlli possono essere soddisfatti nello spazio del proprietario della missione o anche in locale. Microsoft consiglia di condividere il percorso dei componenti VDSS e VDMS in una rete virtuale centrale a cui tutti i proprietari delle missioni possono connettersi. Il diagramma seguente illustra questa architettura: 


![Diagramma dell'architettura di riferimento di SACA](media/sacav2generic.png)

Quando si pianifica la strategia compliancy di SCCA e l'architettura tecnica, considerare gli argomenti seguenti dall'inizio, perché hanno effetto su tutti i clienti. I problemi seguenti sono stati rilevati con i clienti DoD e tendono a rallentare la pianificazione e l'esecuzione. 

#### <a name="which-bcap-will-your-organization-use"></a>Quali BCAP utilizzeranno l'organizzazione?
   - DISA BCAP:
        - DISA ha due BCAPs operative al Pentagono e a Camp Roberts, CA. Un terzo è pianificato per essere presto online. 
        - I BCAPs di DISA hanno tutti circuiti ExpressRoute di Azure, che possono essere usati dai clienti DoD per la connettività. 
        - DISA ha una sessione di peering Microsoft a livello aziendale per i clienti DoD che vogliono sottoscrivere gli strumenti di Microsoft Software as a Service (SaaS), ad esempio Office 365. Usando il BCAP DISA, è possibile abilitare la connettività e il peering per l'istanza di SACA. 
    - Crea BCAP personalizzati:
        - Questa opzione richiede il lease dello spazio in un data center con percorso condiviso e la configurazione di un circuito ExpressRoute in Azure. 
        - Questa opzione richiede un'approvazione aggiuntiva. 
        - A causa dell'approvazione aggiuntiva e di una compilazione fisica, questa opzione richiede più tempo. 
    - Si consiglia di usare DISA BCAP. Questa opzione è prontamente disponibile, presenta una ridondanza incorporata e dispone di clienti che operano attualmente in ambiente di produzione.
- Spazio IP instradabile DoD:
    - È necessario usare lo spazio di indirizzi IP instradabile DoD al perimetro. È disponibile l'opzione per usare NAT per connettere gli spazi allo spazio IP privato in Azure.
    - Per ottenere lo spazio IP, contattare il centro informazioni sulla rete DoD (NIC). È necessario come parte dell'invio del processo di sistema/di approvazione rete (SNAP) con DISA. 
    - Se si prevede di usare NAT per connettere lo spazio degli indirizzi privato in Azure, è necessaria almeno una subnet/24 dello spazio indirizzi assegnato dalla scheda di interfaccia di rete per ogni area in cui si prevede di distribuire SACA.
- Ridondanza:
    - Distribuire un'istanza di SACA in almeno due aree. Nel cloud DoD la distribuzione viene distribuita in entrambe le aree DoD disponibili.
    - Connettersi ad almeno due BCAPs tramite circuiti ExpressRoute distinti. Entrambe le connessioni ExpressRoute possono quindi essere collegate all'istanza SACA di ogni area. 
- Requisiti specifici del componente DoD:
    - L'organizzazione presenta requisiti specifici al di fuori dei requisiti di SCCA? Alcune organizzazioni hanno requisiti specifici per gli indirizzi IP.
- SACA è un'architettura modulare:
    - Usare solo i componenti necessari per l'ambiente. 
        - Distribuire appliance virtuali di rete in un singolo livello o multilivello.
        - Usare IP integrati o indirizzi IP Bring-your-own.
- Livello di effetto DoD delle applicazioni e dei dati:
    - Se è possibile eseguire applicazioni nelle aree Microsoft IL5, compilare l'istanza di SACA in IL5. L'istanza può essere usata davanti alle applicazioni IL4 e IL5. Un'istanza di IL4 SACA davanti a un'applicazione IL5 probabilmente non riceverà l'accredito.

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>Quale fornitore di appliance virtuale di rete si userà per VDSS?
Come indicato in precedenza, è possibile compilare questo riferimento SACA usando un'ampia gamma di appliance e servizi di Azure. Microsoft dispone di modelli di soluzione automatizzati per distribuire l'architettura SACA con F5 e Citrix. Queste soluzioni sono descritte nella sezione seguente.

#### <a name="which-azure-services-will-you-use"></a>Quali servizi di Azure si useranno?
- Sono disponibili servizi di Azure che possono soddisfare i requisiti per la funzionalità di log Analytics, la protezione basata su host e gli ID. È possibile che alcuni servizi non siano disponibili a livello generale nelle aree Microsoft IL5. In questo caso, potrebbe essere necessario usare strumenti di terze parti se questi servizi di Azure non sono in grado di soddisfare i requisiti. Esaminare gli strumenti con cui si ha familiarità e la possibilità di usare gli strumenti nativi di Azure.
- Si consiglia di usare il maggior numero possibile di strumenti nativi di Azure. Sono compilati con la sicurezza del cloud e si integrano facilmente con il resto della piattaforma Azure. Usare gli strumenti nativi di Azure nell'elenco seguente per soddisfare i diversi requisiti di SCCA:

    - [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Insieme di credenziali chiave Azure](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Gateway applicazione Azure](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Firewall di Azure](https://docs.microsoft.com/azure/firewall/overview) 
    - [Sportello anteriore di Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Gruppi di sicurezza di Azure](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Protezione DDoS di Azure](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Sentinella di Azure](https://docs.microsoft.com/azure/sentinel/overview)
- Ridimensionamento
    - È necessario completare un esercizio di ridimensionamento. Esaminare il numero di connessioni simultanee che potrebbero essere presenti attraverso l'istanza di SACA e i requisiti di velocità effettiva della rete. 
    - Questo passaggio è fondamentale. Consente di ridimensionare le macchine virtuali e identificare le licenze richieste dai diversi fornitori usati nell'istanza di SACA. 
    - Non è possibile eseguire un'analisi dei costi corretta senza questo esercizio di ridimensionamento. Il dimensionamento corretto consente inoltre di ottenere prestazioni ottimali. 


## <a name="most-common-deployment-scenario"></a>Scenario di distribuzione più comune

 Molti clienti Microsoft hanno superato la distribuzione completa o almeno le fasi di pianificazione dei propri ambienti SACA. Le loro esperienze hanno rivelato informazioni dettagliate sullo scenario di distribuzione più comune. Il diagramma seguente mostra l'architettura più comune: 


![Diagramma dell'architettura di riferimento di SACA](media/sacav2commonscenario.png) 


Come si può notare dal diagramma, i clienti di DoD in genere sottoscrivono due dei BCAPs DISA. Uno di questi vive sulla costa occidentale e l'altro vive sulla costa orientale. Un peer privato ExpressRoute è abilitato in Azure in ogni località BCAP DISA. Questi peer ExpressRoute vengono quindi collegati al gateway di rete virtuale nelle aree di Azure DoD East e DoD Central. Un'istanza di SACA viene distribuita nelle aree di Azure DoD East e DoD Central. Tutti i flussi di traffico in ingresso e in uscita passano attraverso di essa da e verso la connessione ExpressRoute al BCAP DISA.

Le applicazioni mission-Owner scelgono quindi le aree di Azure in cui si prevede di distribuire le proprie applicazioni. Usano il peering di rete virtuale per connettere la rete virtuale dell'applicazione alla rete virtuale SACA. Quindi forzano il tunneling di tutto il traffico attraverso l'istanza di VDSS.

Questa architettura è consigliata perché soddisfa i requisiti di SCCA. È altamente disponibile e facilmente scalabile. Semplifica inoltre la distribuzione e la gestione.

## <a name="automated-saca-deployment-options"></a>Opzioni di distribuzione automatizzata di SACA

 Come indicato in precedenza, Microsoft ha collaborato con due fornitori per creare un modello di infrastruttura SACA automatizzato. Entrambi i modelli distribuiscono i componenti di Azure seguenti: 

- Rete virtuale SACA
    - Subnet di gestione
        - Questa subnet è il punto in cui vengono distribuite le macchine virtuali e i servizi di gestione, detti anche jump box.
        - Subnet VDMS
            - Questa subnet è il punto in cui vengono distribuite le macchine virtuali e i servizi utilizzati per VDMS.
        - Subnet attendibili e non attendibili
            - Queste subnet sono dove vengono distribuite le appliance virtuali.
        - Subnet del gateway
            - Questa subnet è la posizione in cui viene distribuito il gateway ExpressRoute.
- Macchine virtuali della Jump box di gestione
    - Sono usati per la gestione fuori banda dell'ambiente.
- Appliance virtuali di rete
    - Si usa Citrix o F5 in base al modello distribuito.
- IP pubblici
    - Vengono usati per il front-end fino a quando ExpressRoute non viene portato online. Questi indirizzi IP vengono convertiti nello spazio di indirizzi privato di Azure back-end.
- Tabelle route 
    - Applicato durante l'automazione, le tabelle di route forzano il tunneling di tutto il traffico attraverso l'appliance virtuale.
- Azure load balancers-SKU standard
    - Sono usati per il bilanciamento del carico del traffico tra le appliance.
- Gruppi di sicurezza di rete
    - Vengono usati per controllare i tipi di traffico che possono essere attraversati da determinati endpoint.


### <a name="citrix-saca-deployment"></a>Distribuzione di Citrix SACA

Un modello di distribuzione Citrix distribuisce due livelli di appliance Citrix ADC a disponibilità elevata. Questa architettura soddisfa i requisiti di VDSS. 

![Diagramma Citrix SACA](media/citrixsaca.png)


Per la documentazione Citrix e lo script di distribuzione, vedere [questo collegamento di GitHub](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca).


 ### <a name="f5-saca-deployment"></a>F5 distribuzione SACA

Due modelli di distribuzione F5 distinti coprono due architetture diverse. Il primo modello ha un solo livello di appliance F5 in una configurazione a disponibilità elevata Active-Active. Questa architettura soddisfa i requisiti di VDSS. Il secondo modello aggiunge un secondo livello di F5S a disponibilità elevata Active-Active. Questo secondo livello consente ai clienti di aggiungere indirizzi IP distinti da F5 tra i livelli F5. Non tutti i componenti DoD hanno indirizzi IP specifici prescritti per l'uso. In tal caso, il singolo livello di appliance F5 funziona per la maggior parte perché tale architettura include gli IP sui dispositivi F5.

![Diagramma SACA F5](media/f5saca.png)

Per la documentazione di F5 e lo script di distribuzione, vedere [questo collegamento di GitHub](https://github.com/f5devcentral/f5-azure-saca).












