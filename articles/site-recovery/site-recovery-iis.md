---
title: Eseguire la replica di un&quot;applicazione Web multilivello basata su IIS usando Azure Site Recovery | Microsoft Docs
description: Questo articolo illustra come eseguire la replica di macchine virtuali della Web farm IIS usando Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: nsoneji
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: b23624fc7e82af1cb593a1aedd138ae0d6637ae7
ms.lasthandoff: 03/29/2017


---
# <a name="replicate-a-multi-tier-iis-based-web-application-using-azure-site-recovery"></a>Eseguire la replica di un'applicazione Web multilivello basata su IIS usando Azure Site Recovery

## <a name="overview"></a>Panoramica


Il software applicativo è il motore della produttività aziendale di un'organizzazione. Le diverse applicazioni Web possono essere usate per scopi diversi in un'organizzazione. Alcune di queste, come le applicazioni per l'elaborazione delle retribuzioni, le applicazioni finanziarie e i siti Web rivolti ai clienti possono essere fondamentali. Per l'organizzazione è importante che queste applicazioni siano sempre operative per prevenire la perdita di produttività e, aspetto ancora più importante, evitare eventuali danni all'immagine del marchio.

Le applicazioni Web critiche vengono in genere configurate come applicazioni multilivello, con il Web, il database e l'applicazione in livelli diversi. Oltre a essere distribuite in vari livelli, le applicazioni possono usare più server in ogni livello per bilanciare il carico del traffico. I mapping tra i vari livelli e nel server Web possono essere basati su indirizzi IP statici. In caso di failover, alcuni di questi mapping dovranno essere aggiornati, in particolare se sono stati configurati più siti Web nel server Web. Nel caso di applicazioni Web che usano SSL sarà necessario aggiornare le associazioni dei certificati.

I metodi di ripristino di base tradizionali senza replica prevedono il backup di diversi file di configurazione, impostazioni del registro, binding, componenti personalizzati (COM o .NET), contenuti e certificati, con il recupero dei file tramite una serie di passaggi manuali. Queste tecniche sono chiaramente complesse, soggette a errori e non scalabili. È ad esempio facile dimenticare di eseguire il backup dei certificati e dover quindi necessariamente acquistare nuovi certificati per il server dopo il failover.

Una soluzione di ripristino di emergenza valida deve consentire la modellazione dei piani di ripristino per le architetture di applicazioni complesse descritte in precedenza e consentire anche l'aggiunta di passaggi personalizzati per gestire i mapping delle applicazioni tra i vari livelli, fornendo una soluzione rapida e sicura in caso di emergenza, con un RTO inferiore.


Questo articolo descrive come proteggere un'applicazione Web basata su IIS con [Azure Site Recovery](site-recovery-overview.md). Questo articolo illustra le procedure consigliate per la replica di un'applicazione Web a tre livelli basata su IIS in Azure, come eseguire un'analisi di ripristino di emergenza e come eseguire il failover dell'applicazione in Azure. 

 
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario comprendere i concetti illustrati di seguito:

1. [Replica di una macchina virtuale in Azure](site-recovery-vmware-to-azure.md)
1. Come [progettare una rete di ripristino](site-recovery-network-design.md)
1. [Esecuzione di un failover di test in Azure](./site-recovery-test-failover-to-azure.md)
1. [Esecuzione di un failover in Azure](site-recovery-failover.md)
1. Come [replicare un controller di dominio](site-recovery-active-directory.md)
1. Come [replicare SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Modelli di distribuzione
Un'applicazione Web basata su IIS segue in genere uno dei modelli di distribuzione seguenti:

**Modello di distribuzione 1 ** Web farm basata su IIS con Application Request Routing (ARR), server IIS e Microsoft SQL Server. 

![Modello di distribuzione](./media/site-recovery-iis/deployment-pattern1.png)

**Modello di distribuzione 2** Web farm basata su IIS con Application Request Routing (ARR), server IIS, server applicazioni e Microsoft SQL Server. 


![Modello di distribuzione](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Supporto di Site Recovery

Ai fini di questo articolo sono state usate macchine virtuali VMware con server IIS versione 7.5 in Windows Server 2012 R2 Enterprise. La replica di Site Recovery è indipendente dall'applicazione, quindi si prevede che le indicazioni fornite in questo articolo funzionino anche per gli scenari seguenti e per versioni diverse di IIS.

### <a name="source-and-target"></a>Origine e destinazione

**Scenario** | **In un sito secondario** | **In Azure**
--- | --- | ---
**Hyper-V** | Sì | Sì
**VMware** | Sì | Sì
**Server fisico** | No | Sì

## <a name="replicate-virtual-machines"></a>Replicare le macchine virtuali

Seguire [queste linee guida](site-recovery-vmware-to-azure.md) per avviare la replica di tutte le macchine virtuali della Web farm IIS in Azure. 

Se si usa un indirizzo IP statico, specificare l'indirizzo IP che la macchina virtuale dovrà usare nell'impostazione [**IP di destinazione**](./site-recovery-replicate-vmware-to-azure.md#view-and-manage-vm-properties) di Calcolo e rete. 

![IP di destinazione](./media/site-recovery-active-directory/dns-target-ip.png)


## <a name="creating-a-recovery-plan"></a>Creazione di un piano di ripristino

Un piano di ripristino consente di definire la sequenza di failover di vari livelli in un'applicazione multilivello, conservando la coerenza dell'applicazione. Seguire questa procedura durante la creazione di un piano di ripristino per un'applicazione Web multilivello.  [Altre informazioni sulla creazione di un piano di ripristino](./site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Aggiunta di macchine virtuali a gruppi di failover
Una tipica applicazione Web IIS multilivello è costituita da un livello di database con le macchine virtuali SQL, il livello Web costituito da un server IIS e un livello di applicazione. Aggiungere tutte queste macchine virtuali a gruppi differenti in base al livello come descritto di seguito. [Altre informazioni sulla personalizzazione dei piani di ripristino](site-recovery-runbook-automation.md#customize-the-recovery-plan).

1. Creare un piano di ripristino. Aggiungere le macchine virtuali del livello database nel gruppo 1 in modo che vengano arrestate per ultime e aperte per prime. 

1. Aggiungere le macchine virtuali del livello applicazione nel gruppo 2 in modo che vengano aperte dopo l'apertura del livello database.

1. Aggiungere le macchine virtuali del livello Web nel gruppo 3 in modo che vengano aperte dopo l'apertura del livello applicazione.

1. Aggiungere le macchine virtuali di bilanciamento del carico nel gruppo 4 in modo che vengano aperte dopo l'apertura del livello Web.


### <a name="adding-scripts-to-the-recovery-plan"></a>Aggiunta di script al piano di ripristino
Potrebbe essere necessario eseguire alcune operazioni nelle macchine virtuali di Azure dopo il failover o il failover di test per far sì che la Web farm IIS funzioni correttamente. È possibile automatizzare le operazioni dopo il failover come l'aggiornamento della voce DNS e la modifica del binding del sito e della stringa di connessione aggiungendo script corrispondenti nel piano di ripristino come indicato di seguito. [Altre informazioni sull'aggiunta di script al piano di ripristino](./site-recovery-create-recovery-plans.md#add-scripts).

#### <a name="dns-update"></a>Aggiornamento del DNS
Se il DNS è configurato per l'aggiornamento dinamico, le macchine virtuali aggiornano in genere il DNS con il nuovo indirizzo IP all'avvio. Se si vuole aggiungere un passaggio esplicito per l'aggiornamento del DNS con i nuovi indirizzi IP delle macchine virtuali, aggiungere questo [script per l'aggiornamento degli indirizzi IP nel DNS](https://aka.ms/asr-dns-update) come post-azione nei gruppi del piano di ripristino.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Stringa di connessione nel file web.config di un'applicazione
La stringa di connessione specifica il database con cui comunica il sito Web. 

Se la stringa di connessione contiene il nome della macchina virtuale del database, non saranno necessari altri passaggi dopo il failover e l'applicazione potrà comunicare automaticamente con il database. Se l'indirizzo IP della macchina virtuale del database viene conservato, non sarà necessario aggiornare la stringa di connessione. Se la stringa di connessione fa riferimento alla macchina virtuale del database usando un indirizzo IP, dovrà essere aggiornata dopo il failover. Ad esempio, la stringa di connessione seguente punta al database con l'indirizzo IP 127.0.1.2 

        <?xml version="1.0" encoding="utf-8"?> 
        <configuration> 
        <connectionStrings> 
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" /> 
        </connectionStrings> 
        </configuration>

È possibile aggiornare la stringa di connessione nel livello Web aggiungendo lo [script di aggiornamento della connessione IIS](https://aka.ms/asr-update-webtier-script-classic) dopo il gruppo 3 nel piano di ripristino.

#### <a name="site-bindings-for-the-application"></a>Binding del sito per l'applicazione
Ogni sito è costituito da informazioni che includono il tipo di binding, l'indirizzo IP in cui il server IIS ascolta le richieste per il sito, il numero di porta e i nomi host per il sito. Al momento di un failover potrebbe essere necessario aggiornare questi binding se viene apportata una modifica all'indirizzo IP associato. 

> [!NOTE]
> 
> Se è stato selezionato "Non assegnati" per il binding del sito come nell'esempio seguente, non è necessario aggiornare questo binding dopo il failover. Se l'indirizzo IP associato a un sito non viene modificato dopo il failover, non è necessario aggiornare il binding del sito. La conservazione dell'indirizzo IP dipende dall'architettura di rete e dalle subnet assegnate ai siti primari e di ripristino e potrebbe quindi non essere fattibile per l'organizzazione. 

![Binding SSL](./media/site-recovery-iis/sslbinding.png)

Se è stato associato l'indirizzo IP a un sito, è necessario aggiornare tutti i binding del sito con il nuovo indirizzo IP. È possibile aggiungere lo [script di aggiornamento del livello Web IIS](https://aka.ms/asr-web-tier-update-runbook-classic) dopo il gruppo 3 nel piano di ripristino per modificare i binding del sito. 


#### <a name="update-load-balancer-ip-address"></a>Aggiornare l'indirizzo IP del servizio di bilanciamento del carico
Se è presente una macchina virtuale Application Request Routing, aggiungere lo [script di failover ARR IIS](https://aka.ms/asr-iis-arrtier-failover-script-classic) dopo il gruppo 4 per aggiornare l'indirizzo IP.

#### <a name="the-ssl-cert-binding-for-an-https-connection"></a>Associazione di certificati SSL per una connessione HTTPS
I siti Web possono avere un certificato SSL associato che consente una comunicazione sicura tra il server Web e il browser dell'utente. Se il sito Web ha una connessione HTTPS e un binding HTTPS del sito per l'indirizzo IP del server IIS con un'associazione di certificati SSL, sarà necessario aggiungere un nuovo binding del sito per il certificato con l'indirizzo IP della macchina virtuale IIS dopo il failover. 

Il certificato SSL può essere emesso per 

a) Il nome di dominio completo del sito Web<br>
b) Il nome del server<br>
c) Un certificato con caratteri jolly per il nome di dominio<br>
d) Un indirizzo IP. Se il certificato SSL viene emesso per l'indirizzo IP del server IIS, sarà necessario emettere un altro certificato SSL per l'indirizzo IP del server IIS nel sito di Azure e sarà necessario creare un'altra associazione SSL per questo certificato. È quindi consigliabile non usare un certificato SSL emesso per un indirizzo IP. Si tratta di un'opzione meno usata e verrà presto deprecata in base alle nuove modifiche di CA/Browser Forum.

#### <a name="update-the-dependency-between-the-web-and-the-application-tier"></a>Aggiornare la dipendenza tra il livello Web e il livello applicazione
Se è presente una dipendenza specifica dell'applicazione basata sull'indirizzo IP delle macchine virtuali, è necessario aggiornare questa dipendenza dopo il failover.

## <a name="doing-a-test-failover"></a>Esecuzione di un failover di test
Seguire [queste linee guida](site-recovery-test-failover-to-azure.md) per eseguire un failover di test.

1.    Accedere al portale di Azure e selezionare l'insieme di credenziali di Servizi di ripristino.
1.    Fare clic sul piano di ripristino creato per la Web farm IIS.
1.    Fare clic su 'Failover di test'.
1.    Selezionare il punto di recupero e la rete virtuale di Azure per avviare il processo di failover di test.
1.    Quando l'ambiente secondario è disponibile, è possibile eseguire le convalide.
1.    Al termine delle convalide è possibile selezionare 'Convalide complete' per pulire l'ambiente di failover di test.

## <a name="doing-a-failover"></a>Esecuzione di un failover
Seguire [queste linee guida](site-recovery-failover.md) quando si esegue un failover.

1.    Accedere al portale di Azure e selezionare l'insieme di credenziali di Servizi di ripristino.
1.    Fare clic sul piano di ripristino creato per la Web farm IIS.
1.    Fare clic su 'Failover'.
1.    Selezionare il punto di recupero per avviare il processo di failover.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulla [replica di altre applicazioni](site-recovery-workload.md) con Site Recovery. 

