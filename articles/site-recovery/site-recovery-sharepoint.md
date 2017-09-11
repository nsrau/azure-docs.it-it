---
title: Eseguire la replica di un'applicazione di SharePoint multilivello con Azure Site Recovery | Microsoft Docs
description: "Questo articolo descrive come eseguire la replica di un'applicazione di SharePoint multilivello usando le funzionalità di Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: sutalasi
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: 55323df68715c80d5e8535199cd739921a3baad9
ms.contentlocale: it-it
ms.lasthandoff: 08/31/2017

---
# <a name="replicate-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Eseguire la replica di un'applicazione di SharePoint multilivello per il ripristino di emergenza con Azure Site Recovery

Questo articolo descrive dettagliatamente come proteggere un'applicazione di SharePoint usando [Azure Site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Panoramica

Microsoft SharePoint è una potente applicazione che può aiutare un gruppo o un reparto a organizzare e condividere informazioni collaborando. SharePoint offre portali Intranet, gestione di documenti e file, collaborazione, social network, reti Extranet, siti Web, ricerca di livello aziendale e business intelligence. Include inoltre funzionalità di integrazione dei sistemi, integrazione dei processi e automazione dei flussi di lavoro. In genere, le organizzazioni considerano SharePoint un'applicazione di livello 1 sensibile al tempo di inattività e alla perdita dei dati.

Oggi Microsoft SharePoint non include funzionalità di ripristino di emergenza predefinite. Indipendentemente dal tipo e dall'entità di un evento di emergenza, il ripristino comporta l'uso di un data center di standby in cui ripristinare la farm. I data center di standby sono necessari per gli scenari in cui sistemi ridondanti locali e backup non sono in grado di eseguire il ripristino da interruzioni nel data center primario.

Una buona soluzione di ripristino di emergenza deve consentire la modellazione di piani di ripristino basati su architetture di applicazioni complesse, come SharePoint. Deve anche permettere di aggiungere passaggi personalizzati per gestire i mapping delle applicazioni tra diversi livelli e quindi fornire il failover con un solo clic e un RTO inferiore nel caso di un evento di emergenza.

Questo articolo descrive dettagliatamente come proteggere un'applicazione di SharePoint usando [Azure Site Recovery](site-recovery-overview.md). Questo articolo descrive le procedure consigliate per la replica di un'applicazione di SharePoint in Azure, l'esecuzione di un'esercitazione sul ripristino di emergenza e il failover dell'applicazione in Azure.

È possibile guardare il video di seguito sul ripristino di un'applicazione multilivello in Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario comprendere i concetti illustrati di seguito:

1. [Replica di una macchina virtuale in Azure](site-recovery-vmware-to-azure.md)
2. Come [progettare una rete di ripristino](site-recovery-network-design.md)
3. [Esecuzione di un failover di test in Azure](site-recovery-test-failover-to-azure.md)
4. [Esecuzione di un failover in Azure](site-recovery-failover.md)
5. Come [replicare un controller di dominio](site-recovery-active-directory.md)
6. Come [replicare SQL Server](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>Architettura di SharePoint

SharePoint può essere distribuito in uno o più server usando topologie a livelli e ruoli del server per implementare una progettazione di farm in grado di soddisfare finalità e obiettivi specifici. Una tipica server farm di SharePoint con esigenze e dimensioni elevate che supporta molti utenti simultanei ed elementi di contenuto usa il raggruppamento dei servizi come parte della strategia di scalabilità. Questo approccio comporta l'esecuzione di servizi su server dedicati, il raggruppamento dei servizi e quindi la scalabilità orizzontale dei server come gruppi. La topologia seguente mostra il raggruppamento di servizi e server per una server farm di SharePoint a tre livelli. Per indicazioni dettagliate sulle diverse topologie di SharePoint, fare riferimento alla documentazione e alle architetture delle linee di prodotti di SharePoint. Altre informazioni sulla distribuzione di SharePoint 2013 sono disponibili in [questo documento](https://technet.microsoft.com/en-us/library/cc303422.aspx).



![Modello di distribuzione 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Supporto di Site Recovery

Ai fini di questo articolo sono state usate macchine virtuali VMware con Windows Server 2012 R2 Enterprise. Sono stati usati SharePoint 2013 Enterprise Edition e SQL Server 2014 Enterprise Edition. Poiché la replica di Site Recovery è indipendente dall'applicazione, i consigli inclusi in questo articolo saranno validi anche per gli scenari seguenti.

### <a name="source-and-target"></a>Origine e destinazione

**Scenario** | **In un sito secondario** | **In Azure**
--- | --- | ---
**Hyper-V** | Sì | Sì
**VMware** | Sì | Sì
**Server fisico** | Sì | Sì

### <a name="sharepoint-versions"></a>Versioni di SharePoint
Sono supportate le versioni di SharePoint Server seguenti.

* SharePoint Server 2013 Standard
* SharePoint Server 2013 Enterprise
* SharePoint Server 2016 Standard
* SharePoint Server 2016 Enterprise

### <a name="things-to-keep-in-mind"></a>Aspetti da considerare

Se si usa un cluster basato su dischi condivisi come qualsiasi livello nell'applicazione, non sarà possibile usare Site Recovery per eseguire la replica delle macchine virtuali. È possibile usare la replica nativa fornita dall'applicazione e quindi un [piano di ripristino](site-recovery-create-recovery-plans.md) per eseguire il failover di tutti i livelli.

## <a name="replicating-virtual-machines"></a>Replica di macchine virtuali

Seguire [queste linee guida](site-recovery-vmware-to-azure.md) per avviare la replica delle macchine virtuali in Azure.

* Al termine della replica, in ogni macchina virtuale di ogni livello assicurarsi di selezionare lo stesso set di disponibilità in "Elementi replicati > Impostazioni > Proprietà > Calcolo e rete". Ad esempio, se il livello Web include tre VM, assicurarsi che le tre VM siano tutte configurate per fare parte dello stesso set di disponibilità in Azure.

    ![Impostazione del set di disponibilità](./media/site-recovery-sharepoint/select-av-set.png)

* Per le linee guida sulla protezione di Active Directory e DNS, fare riferimento al documento [Proteggere Active Directory e DNS con Azure Site Recovery](site-recovery-active-directory.md).

* Per le linee guida sulla protezione del livello database in esecuzione in SQL Server, fare riferimento al documento [Proteggere SQL Server](site-recovery-active-directory.md).

## <a name="networking-configuration"></a>Configurazione delle impostazioni di rete

### <a name="network-properties"></a>Proprietà di rete

* Per le VM di livello app e Web, configurare le impostazioni di rete nel portale di Azure in modo che le VM siano collegate alla rete di ripristino di emergenza corretta dopo il failover.

    ![Selezione della rete](./media/site-recovery-sharepoint/select-network.png)


* Se si usa un indirizzo IP statico, specificare l'indirizzo IP che dovrà essere usato dalla macchina virtuale nel campo **IP di destinazione**

    ![Impostazione dell'IP statico](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>DNS e routing del traffico

Per i siti con connessione Internet, [creare un profilo di Gestione traffico di tipo "Priorità"](../traffic-manager/traffic-manager-create-profile.md) nella sottoscrizione di Azure. Configurare quindi il DNS e il profilo di Gestione traffico nel modo seguente.


| **Dove** | **Origine** | **Destinazione**|
| --- | --- | --- |
| DNS pubblico | DNS pubblico per siti di SharePoint <br/><br/> Esempio: sharepoint.contoso.com | Gestione traffico <br/><br/> contososharepoint.trafficmanager.net |
| DNS locale | sharepointonprem.contoso.com | IP pubblico nella farm locale |


Nel profilo di Gestione traffico [creare gli endpoint primario e di ripristino](../traffic-manager/traffic-manager-configure-priority-routing-method.md). Usare l'endpoint esterno per l'endpoint locale e l'indirizzo IP pubblico per l'endpoint di Azure. Assicurarsi che la priorità sia impostata come più alta per l'endpoint locale.

Ospitare una pagina di test su una porta specifica, ad esempio la porta 800, nel livello Web di SharePoint in modo che Gestione traffico possa rilevare automaticamente la disponibilità dopo il failover. Questa è una soluzione alternativa per i casi in cui non è possibile abilitare l'autenticazione anonima in uno dei siti di SharePoint.

[Configurare il profilo di Gestione traffico](../traffic-manager/traffic-manager-configure-priority-routing-method.md) con le impostazioni seguenti.

* Metodo di routing: "Priorità"
* Durata DNS: "30 secondi"
* Impostazioni di monitoraggio degli endpoint: se l'autenticazione anonima può essere abilitata, è possibile fornire l'endpoint di un sito Web specifico. In alternativa, è possibile usare una pagina di test su una porta specifica, ad esempio la porta 800.

## <a name="creating-a-recovery-plan"></a>Creazione di un piano di ripristino

Un piano di ripristino consente di definire la sequenza di failover di vari livelli in un'applicazione multilivello, conservando la coerenza dell'applicazione. Seguire questa procedura durante la creazione di un piano di ripristino per un'applicazione Web multilivello. [Altre informazioni sulla creazione di un piano di ripristino](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Aggiunta di macchine virtuali a gruppi di failover

1. Creare un piano di ripristino aggiungendo le VM di livello app e Web.
2. Fare clic su "Personalizza" per raggruppare le VM. Per impostazione predefinita, tutte le VM fanno parte di "Gruppo 1".

    ![Personalizzazione del piano di ripristino](./media/site-recovery-sharepoint/rp-groups.png)

3. Creare un altro gruppo (Gruppo 2) e spostare le VM di livello Web nel nuovo gruppo. Le VM di livello app devono fare parte di "Gruppo 1", mentre le VM di livello Web devono fare parte di "Gruppo 2". In questo modo, le VM di livello app vengono avviate per prime, seguite dalle VM di livello Web.


### <a name="adding-scripts-to-the-recovery-plan"></a>Aggiunta di script al piano di ripristino

È possibile distribuire gli script di Azure Site Recovery usate più comunemente nell'account di Automazione facendo clic sul pulsante "Distribuisci in Azure" di seguito. Quando si usa uno script pubblicato, assicurarsi di seguire le istruzioni nello script.

[![Distribuzione in Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Aggiungere uno script precedente all'azione a "Gruppo 1" per il gruppo di disponibilità SQL. Usare lo script "ASR-SQL-FailoverAG" pubblicato negli script di esempio. Assicurarsi di seguire le istruzioni nello script e apportare le modifiche necessarie nello script nel modo appropriato.

    ![Aggiunta dello script per il gruppo di disponibilità - Passaggio 1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Aggiunta dello script per il gruppo di disponibilità - Passaggio 2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Aggiungere uno script successivo all'azione per collegare un servizio di bilanciamento del carico nelle macchine virtuali sottoposte a failover di livello Web (Gruppo 2). Usare lo script "ASR-AddSingleLoadBalancer" pubblicato negli script di esempio. Assicurarsi di seguire le istruzioni nello script e apportare le modifiche necessarie nello script nel modo appropriato.

    ![Aggiunta dello script per il bilanciamento del carico - Passaggio 1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Aggiunta dello script per il bilanciamento del carico - Passaggio 2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Aggiungere un passaggio manuale per aggiornare i record DNS perché puntino alla nuova farm in Azure.

    * Per i siti con connessione Internet, non è necessario alcun aggiornamento dei record DNS dopo il failover. Seguire i passaggi descritti nella sezione delle linee guida per le impostazioni di rete per configurare Gestione traffico. Se il profilo di Gestione traffico è stato configurato come descritto nella sezione precedente, aggiungere uno script per aprire una porta fittizia (porta 800 nell'esempio) nella VM di Azure.

    * Per i siti interni, aggiungere un passaggio manuale per aggiornare il record DNS perché punti al nuovo indirizzo IP del servizio di bilanciamento del carico della VM di livello Web.

4. Aggiungere un passaggio manuale per ripristinare l'applicazione di ricerca da un backup o avviare un nuovo servizio di ricerca.

5. Per il ripristino dell'applicazione del servizio di ricerca da un backup, seguire i passaggi seguenti.

    * Questo metodo presuppone che sia stato eseguito un backup dell'applicazione del servizio di ricerca prima dell'evento di emergenza e che il backup sia disponibile nel sito di ripristino di emergenza.
    * A questo scopo, è possibile pianificare in tutta semplicità il backup (ad esempio, una volta al giorno) e usare una procedura di copia per spostare il backup nel sito di ripristino di emergenza. Le procedure di copia possono includere programmi basati su script come AzCopy (Azure Copy) o la configurazione di DFSR (Distributed File Services Replication).
    * Quando la farm di SharePoint è in esecuzione, passare ad Amministrazione centrale, quindi a "Backup e ripristino" e selezionare Ripristina. Il ripristino interroga il percorso di backup specificato. Potrebbe essere necessario aggiornare il valore. Selezionare il backup dell'applicazione del servizio di ricerca che si vuole ripristinare.
    * La ricerca viene ripristinata. Tenere presente che l'operazione di ripristino si aspetta di trovare la stessa topologia, ovvero lo stesso numero di server, e le stesse lettere di unità assegnate ai server. Per altre informazioni, vedere il documento [Ripristinare le applicazioni del servizio di ricerca in SharePoint 2013](https://technet.microsoft.com/library/ee748654.aspx).


6. Per avviare il sistema con una nuova applicazione del servizio di ricerca, seguire i passaggi seguenti.

    * Questo metodo presuppone che nel sito di ripristino di emergenza sia disponibile un backup del database di amministrazione della ricerca.
    * Poiché gli altri database dell'applicazione del servizio di ricerca non vengono replicati, devono essere ricreati. A questo scopo, passare ad Amministrazione centrale ed eliminare l'applicazione del servizio di ricerca. Eliminare i file di indice in tutti i server che ospitano l'indice di ricerca.
    * Ricreare l'applicazione del servizio di ricerca perché vengano ricreati i database. È consigliabile aver preparato uno script che ricrea questa applicazione di servizio, perché non è possibile eseguire tutte le operazioni tramite l'interfaccia utente grafica. Ad esempio, l'impostazione del percorso dell'unità degli indici e la configurazione della topologia di ricerca sono possibili solo usando i cmdlet di PowerShell per SharePoint. Usare il cmdlet di Windows PowerShell Restore-SPEnterpriseSearchServiceApplication e specificare il database di amministrazione della ricerca Search_Service__DB fornito dal log e replicato. Questo cmdlet fornisce la configurazione, lo schema, le proprietà gestite, le regole e le origini della ricerca e crea un set predefinito degli altri componenti.
    * Una volta ricreata l'applicazione del servizio di ricerca, è necessario avviare una ricerca per indicizzazione completa per ogni origine di contenuto per ripristinare il servizio di ricerca. Verranno perse alcune informazioni di analisi dalla farm locale, ad esempio i suggerimenti per la ricerca.

7. Al termine di tutti i passaggi, salvare il piano di ripristino. Il piano di ripristino finale avrà un aspetto simile al seguente.

    ![Piano di ripristino salvato](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Esecuzione di un failover di test
Seguire [queste linee guida](site-recovery-test-failover-to-azure.md) per eseguire un failover di test.

1.  Accedere al portale di Azure e selezionare l'insieme di credenziali di Servizi di ripristino.
2.  Fare clic sul piano di ripristino creato per l'applicazione di SharePoint.
3.  Fare clic su 'Failover di test'.
4.  Selezionare il punto di recupero e la rete virtuale di Azure per avviare il processo di failover di test.
5.  Quando l'ambiente secondario è disponibile, è possibile eseguire le convalide.
6.  Al termine delle convalide, è possibile fare clic su "Pulizia failover di test" nel piano di ripristino perché l'ambiente di failover di test venga pulito.

Per le linee guida su come eseguire il failover di test per Active Directory e DNS, fare riferimento al documento [Considerazioni sul failover di test per Active Directory e DNS](site-recovery-active-directory.md#test-failover-considerations).

Per le linee guida su come eseguire il failover di test per gruppi di disponibilità SQL AlwaysOn, fare riferimento al documento [Esecuzione di un failover di test per SQL AlwaysOn](site-recovery-sql.md#steps-to-do-a-test-failover).

## <a name="doing-a-failover"></a>Esecuzione di un failover
Seguire [queste linee guida](site-recovery-failover.md) per eseguire un failover.

1.  Accedere al portale di Azure e selezionare l'insieme di credenziali di Servizi di ripristino.
2.  Fare clic sul piano di ripristino creato per l'applicazione di SharePoint.
3.  Fare clic su 'Failover'.
4.  Selezionare il punto di recupero per avviare il processo di failover.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulla [replica di altre applicazioni](site-recovery-workload.md) con Site Recovery.

