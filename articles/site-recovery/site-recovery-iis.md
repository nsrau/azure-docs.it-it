---
title: Eseguire la replica di un'applicazione Web multilivello basata su IIS usando Azure Site Recovery | Microsoft Docs
description: Informazioni su come eseguire la replica di macchine virtuali Web farm IIS usando Azure Site Recovery.
services: site-recovery
author: nsoneji
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: nisoneji
ms.openlocfilehash: 1ed0184ac76b5fb3e607458559327da5e8fe90c5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="replicate-a-multi-tier-iis-based-web-application"></a>Eseguire la replica di un'applicazione Web multilivello basata su IIS

Il software applicativo è il motore della produttività aziendale di un'organizzazione. Le diverse applicazioni Web possono essere usate per scopi diversi in un'organizzazione. Alcune applicazioni, ad esempio quelle usate per l'elaborazione delle retribuzioni, le applicazioni finanziarie e i siti Web orientati ai clienti, possono avere un ruolo critico in un'organizzazione. Per evitare perdite di produttività, è importante per l'organizzazione che queste applicazioni siano costantemente operative. La costante disponibilità di queste applicazioni deve soprattutto aiutare a evitare danni al marchio o all'immagine o dell'organizzazione.

Le applicazioni Web critiche vengono in genere configurate come applicazioni multilivello in cui Web, database e applicazione risiedono in livelli diversi. Oltre a essere distribuite in vari livelli, le applicazioni possono usare più server in ogni livello per bilanciare il carico del traffico. I mapping tra i vari livelli e nel server Web, inoltre, possono essere basati su indirizzi IP statici. In caso di failover, alcuni di questi mapping devono essere aggiornati, in particolare se sono stati configurati più siti Web nel server Web. Se le applicazioni Web usano SSL, è necessario aggiornare anche i binding dei certificati.

I metodi di ripristino tradizionali senza replica prevedono il backup di diversi file di configurazione, delle impostazioni del registro, dei binding, dei componenti personalizzati (COM o .NET), dei contenuti e dei certificati. I file vengono ripristinati attraverso una procedura manuale. I metodi di ripristino tradizionali di backup e il ripristino manuale dei file sono complessi, soggetti a errori e non scalabili. Ci si potrebbe, ad esempio, dimenticare facilmente di eseguire il backup dei certificati. A quel punto, dopo failover, l'unica soluzione sarebbe quella di acquistare nuovi certificati per il server.

Una buona soluzione di ripristino di emergenza supporta la modellazione di piani di ripristino per le architetture di applicazione complesse. Deve anche permettere di aggiungere passaggi personalizzati nel piano di ripristino per gestire i mapping delle applicazioni tra i livelli. In una situazione di emergenza, i mapping delle applicazioni offrono una soluzione semplice e sicura che consente di abbassare l'obiettivo RTO.

Questo articolo descrive come proteggere un'applicazione Web basata su Internet Information Services (IIS) usando [Azure Site Recovery](site-recovery-overview.md). L'articolo illustra le procedure consigliate per la replica di un'applicazione Web a tre livelli basata su IIS in Azure. Spiega anche come eseguire un'analisi di ripristino di emergenza e come effettuare il failover dell'applicazione in Azure.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di conoscere le procedure per eseguire le attività seguenti:

* [Replicare una macchina virtuale in Azure](vmware-azure-tutorial.md)
* [Progettare una rete di ripristino](site-recovery-network-design.md)
* [Eseguire un failover di test in Azure](site-recovery-test-failover-to-azure.md)
* [Eseguire un failover in Azure](site-recovery-failover.md)
* [Replicare un controller di dominio](site-recovery-active-directory.md)
* [Replicare SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Modelli di distribuzione
Un'applicazione Web basata su IIS segue in genere uno dei modelli di distribuzione descritti di seguito:

**Modello di distribuzione 1**

Web farm basata su IIS con Application Request Routing (ARR), server IIS e Microsoft SQL Server.

![Diagramma di una Web farm basata su IIS che include tre livelli](./media/site-recovery-iis/deployment-pattern1.png)

**Modello di distribuzione 2**

Web farm basata su IIS con Application Request Routing (ARR), server IIS, server applicazioni e Microsoft SQL Server.

![Diagramma di una Web farm basata su IIS che include quattro livelli](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Supporto di Site Recovery

Ai fini di questo articolo sono state usate macchine virtuali VMware con IIS 7.5 su Windows Server 2012 R2 Enterprise. Poiché la replica di Site Recovery non è specifica dell'applicazione, i consigli riportati in questo articolo si applicano negli scenari elencati nella tabella seguente e per versioni diverse di IIS.

### <a name="source-and-target"></a>Origine e destinazione

Scenario | In un sito secondario | In Azure
--- | --- | ---
Hyper-V | Sì | Sì
VMware | Sì | Sì
Server fisico | No  | Sì
Azure|ND|Sì

## <a name="replicate-virtual-machines"></a>Replicare le macchine virtuali

Per avviare la replica di tutte le macchine virtuali Web farm basate su IIS in Azure, seguire le istruzioni descritte in [Esecuzione di un failover di test in Azure con Site Recovery](site-recovery-test-failover-to-azure.md).

Se si usa un indirizzo IP statico, è possibile specificare l'indirizzo IP che dovrà essere usato dalla macchina virtuale. Per impostare l'indirizzo IP, passare a **Compute and Network settings** > **TARGET IP** (Impostazioni Calcolo e rete, IP DESTINAZIONE).

![Screenshot che illustra come impostare l'indirizzo IP di destinazione nel riquadro Calcolo e rete di Site Recovery](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Creare un piano di ripristino
Un piano di ripristino supporta la sequenziazione di vari livelli in un'applicazione multilivello durante un failover. La sequenziazione aiuta a mantenere la coerenza delle applicazioni. Quando si crea un piano di ripristino per un'applicazione Web multilivello, completare la procedura descritta in [Creare un piano di ripristino con Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Aggiungere macchine virtuali a gruppi di failover
Una tipica applicazione Web IIS multilivello include i componenti seguenti:
* Un livello database che dispone di macchine virtuali SQL.
* Il livello Web, che è costituito da un server IIS e un livello applicazione. 

Aggiungere macchine virtuali in gruppi diversi in base al livello:

1. Creare un piano di ripristino. Aggiungere le macchine virtuali del livello database nel Gruppo 1. In questo modo si assicura che le macchine virtuali del livello database vengano arrestate per ultime e avviate per prime.
1. Aggiungere le macchine virtuali del livello applicazione nel Gruppo 2. Si assicura così che le macchine virtuali del livello applicazione vengano avviate dopo quelle del livello database.
1. Aggiungere le macchine virtuali del livello Web nel Gruppo 3. Si assicura così che le macchine virtuali del livello Web vengano avviate dopo quelle del livello applicazione.
1. Aggiungere le macchine virtuali del bilanciamento del carico nel Gruppo 4. In questo modo si assicura che le macchine virtuali del livello bilanciamento del carico vengano avviate dopo quelle del livello Web.

Per altre informazioni, vedere [Personalizzare il piano di ripristino](site-recovery-runbook-automation.md#customize-the-recovery-plan).


### <a name="add-a-script-to-the-recovery-plan"></a>Aggiungere uno script al piano di ripristino
Per far sì che la Web farm IIS funzioni correttamente, potrebbe essere necessario eseguire alcune operazioni nelle macchine virtuali di Azure dopo il failover o il failover di test. È possibile automatizzare alcune operazioni successive al failover. È, ad esempio, possibile aggiornare la voce DNS, modificare un'associazione del sito o modificare una stringa di connessione aggiungendo gli script corrispondenti al piano di ripristino. L'articolo [Add a VMM script to a recovery plan](site-recovery-how-to-add-vmmscript.md) (Aggiungere uno script VMM a un piano di ripristino) illustra come impostare le attività automatizzate tramite uno script.

#### <a name="dns-update"></a>Aggiornamento del DNS
Se il DNS è configurato per l'aggiornamento DNS dinamico, le macchine virtuali aggiornano in genere il DNS con il nuovo indirizzo IP all'avvio. Se si vuole aggiungere un passaggio esplicito per l'aggiornamento del DNS con i nuovi indirizzi IP delle macchine virtuali, aggiungere uno [script per l'aggiornamento degli indirizzi IP nel DNS](https://aka.ms/asr-dns-update) come azione dopo il failover nei gruppi del piano di ripristino.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Stringa di connessione nel file web.config di un'applicazione
La stringa di connessione specifica il database con cui comunica il sito Web. Se la stringa di connessione contiene il nome della macchina virtuale del database, non sono necessari altri passaggi dopo il failover. L'applicazione può comunicare automaticamente con il database. Se l'indirizzo IP della macchina virtuale del database viene conservato, non è necessario aggiornare la stringa di connessione. 

Se la stringa di connessione fa riferimento alla macchina virtuale del database usando un indirizzo IP, la stringa deve essere aggiornata dopo il failover. La stringa di connessione seguente, ad esempio, punta al database con l'indirizzo IP 127.0.1.2:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Per aggiornare la stringa di connessione nel livello Web, aggiungere uno [script di aggiornamento della connessione IIS](https://aka.ms/asr-update-webtier-script-classic) dopo il Gruppo 3 nel piano di ripristino.

#### <a name="site-bindings-for-the-application"></a>Binding del sito per l'applicazione
Ogni sito è costituito da informazioni di binding. Tali informazioni includono il tipo di binding, l'indirizzo IP in cui il server IIS ascolta le richieste per il sito, il numero di porta e i nomi host per il sito. Durante il failover, potrebbe essere necessario aggiornare questi binding se viene modifico l'indirizzo IP associato.

> [!NOTE]
>
> Se è stato selezionato **Non assegnati** per il binding del sito, non è necessario aggiornare questo binding dopo il failover. Anche se l'indirizzo IP associato a un sito non viene modificato dopo il failover, non è necessario aggiornare il binding del sito. La conservazione dell'indirizzo IP dipende dall'architettura di rete e dalle subnet assegnate ai siti primario e di ripristino e il relativo aggiornamento potrebbe quindi non essere fattibile per l'organizzazione.

![Screenshot che mostra l'impostazione del binding SSL](./media/site-recovery-iis/sslbinding.png)

Se è stato associato l'indirizzo IP a un sito, è necessario aggiornare tutti i binding del sito con il nuovo indirizzo IP. Per modificare i binding del sito, è possibile aggiungere uno [script di aggiornamento del livello Web IIS](https://aka.ms/asr-web-tier-update-runbook-classic) dopo il Gruppo 3 nel piano di ripristino.

#### <a name="update-the-load-balancer-ip-address"></a>Aggiornare l'indirizzo IP del servizio di bilanciamento del carico
Se è presente una macchina virtuale Application Request Routing, aggiungere uno [script di failover ARR IIS](https://aka.ms/asr-iis-arrtier-failover-script-classic) dopo il Gruppo 4 per aggiornare l'indirizzo IP.

#### <a name="ssl-certificate-binding-for-an-https-connection"></a>Binding di certificati SSL per una connessione HTTPS
I siti Web possono avere un certificato SSL associato che consente una comunicazione sicura tra il server Web e il browser dell'utente. Se il sito Web ha una connessione HTTPS e un binding HTTPS del sito per l'indirizzo IP del server IIS con un binding di certificati SSL, è necessario aggiungere un nuovo binding del sito per il certificato con l'indirizzo IP della macchina virtuale IIS dopo il failover.

Il certificato SSL può essere emesso per questi componenti:

* Nome di dominio completo del sito Web
* Nome del server
* Certificato con caratteri jolly per il nome di dominio  
* Indirizzo IP. Se il certificato SSL viene emesso per l'indirizzo IP del server IIS, è necessario emettere un altro certificato SSL per l'indirizzo IP del server IIS nel sito di Azure. Deve inoltre essere creato un binding SSL aggiuntivo per questo certificato. Per questo motivo, è consigliabile non usare un certificato SSL emesso in base all'indirizzo IP. Questa opzione viene usata meno e verrà presto deprecata in base alle nuove modifiche del forum dell'autorità/browser di certificazione.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Aggiornare la dipendenza tra il livello Web e il livello applicazione
Se è presente una dipendenza specifica dell'applicazione basata sull'indirizzo IP delle macchine virtuali, è necessario aggiornare questa dipendenza dopo il failover.

## <a name="run-a-test-failover"></a>Eseguire un failover di test

1. Nel portale di Azure selezionare l'insieme di credenziali di Servizi di ripristino.
2. Selezionare il piano di ripristino creato per la Web farm IIS.
3. Selezionare **Failover di test**.
4. Per avviare il processo di failover di test, selezionare il punto di recupero e la rete virtuale di Azure.
5. Quando l'ambiente secondario diventa disponibile, è possibile eseguire le convalide.
6. Al termine delle convalide, selezionare **Convalide complete** per pulire l'ambiente di failover di test.

Per altre informazioni, vedere [Failover di test in Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Eseguire un failover

1. Nel portale di Azure selezionare l'insieme di credenziali di Servizi di ripristino.
1. Selezionare il piano di ripristino creato per la Web farm IIS.
1. Selezionare **Failover**.
1. Per avviare il processo di failover, selezionare il punto di recupero.

Per altre informazioni, vedere [Failover in Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla [replica di altre applicazioni](site-recovery-workload.md) con Site Recovery.
