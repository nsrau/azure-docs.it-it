---
title: Monitoraggio della sicurezza nel Centro sicurezza di Azure | Documentazione di Microsoft
description: "Questo articolo illustra come iniziare a usare le funzionalità di monitoraggio nel Centro sicurezza di Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.author: yurid
ms.openlocfilehash: 04f2dd3dcaa44a243cb9620ab8192e15aae82a01
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="security-health-monitoring-in-azure-security-center"></a>Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure
Questo articolo descrive come usare le funzionalità di monitoraggio nel Centro sicurezza di Azure per verificare la conformità ai criteri.

## <a name="what-is-security-health-monitoring"></a>Che cos'è il monitoraggio dello stato di sicurezza?
Spesso si pensa al monitoraggio come l'osservazione e l'attesa di un evento al fine di reagire alla situazione verificatasi. Per monitoraggio della sicurezza si intende una strategia proattiva per il controllo delle risorse, al fine di identificare i sistemi non conformi agli standard o alle procedure consigliate della società.

## <a name="monitoring-security-health"></a>Monitoraggio dello stato di sicurezza
Dopo avere abilitato i [criteri di sicurezza](security-center-policies.md) per le risorse di una sottoscrizione, il Centro sicurezza analizza la sicurezza delle risorse per identificare le potenziali vulnerabilità. Le informazioni sulla configurazione di rete sono disponibili immediatamente. A seconda del numero di macchine virtuali e computer con l'agente installato, potrebbe essere necessaria almeno un'ora per raccogliere informazioni sulle macchine virtuali e la configurazione dei computer, ad esempio lo stato di aggiornamento della sicurezza e la configurazione del sistema operativo. Nella sezione **Prevenzione** è possibile visualizzare lo stato di sicurezza delle risorse ed eventuali problemi rilevati. Anche nel riquadro **Raccomandazioni** è disponibile l'elenco dei problemi riscontrati.

Per altre informazioni su come applicare le raccomandazioni, leggere l'articolo sull'[implementazione delle raccomandazioni sulla sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).

Nella sezione **Prevenzione** è possibile monitorare lo stato di sicurezza delle risorse. Nell'esempio seguente, si noterà che il riquadro di ogni risorsa, ovvero Calcolo, Rete, Storage & data (Archiviazione e dati) e Applicazione indica il numero totale di problemi identificati.

![Riquadro Integrità sicurezza delle risorse](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)


### <a name="monitor-compute"></a>Monitorare le risorse di calcolo
Quando si fa clic sul riquadro **Calcolo** vengono visualizzate tre schede:

- **Panoramica**: monitoraggio e raccomandazioni.
- **VM e computer**: elenco di tutte le macchine virtuali, dei computer e stato di sicurezza corrente.
- **Servizi cloud**: elenco di tutti i ruoli Web e di lavoro monitorati dal Centro sicurezza.

![Aggiornamento di sistema mancante per la macchina virtuale](./media/security-center-monitoring/security-center-monitoring-fig1-sep2017.png)

In ogni scheda possono essere presenti più sezioni ed è possibile selezionare in ogni sezione una singola opzione per visualizzare altri dettagli sulla procedura consigliata per risolvere il problema.

#### <a name="monitoring-recommendations"></a>Monitoraggio delle raccomandazioni
Questa sezione indica il numero totale di macchine virtuali e computer inizializzati per il provisioning automatico, con i relativi stati correnti. Questo esempio include un consiglio, **Monitoring agent health issues** (Problemi di integrità dell'agente di monitoraggio).  Selezionare questo consiglio.

![Monitoring agent health issues (Problemi di integrità dell'agente di monitoraggio)](./media/security-center-monitoring/security-center-monitoring-fig1-new003-2017.png)

Viene visualizzato **Monitoring agent health issues** (Problemi di integrità dell'agente di monitoraggio). Sono elencati le macchine virtuali e i computer che il Centro sicurezza non può monitorare correttamente. Selezionare una VM o un computer per informazioni dettagliate. **STATO DI MONITORAGGIO** fornisce un motivo che spiega perché il Centro sicurezza non è in grado di eseguire il monitoraggio. Vedere la [Guida alla risoluzione dei problemi del Centro sicurezza](security-center-troubleshooting-guide.md#monitoring-agent-health-issues) per un elenco di valori, descrizioni e procedure di risoluzione dello **STATO DI MONITORAGGIO**.

#### <a name="recommendations"></a>Raccomandazioni
Questa sezione include una serie di [raccomandazioni per ogni macchina virtuale e computer](security-center-virtual-machine-recommendations.md) monitorati dal Centro sicurezza di Azure. La prima colonna elenca la raccomandazione. La seconda colonna mostra il numero totale di macchine virtuali e computer interessati da questa raccomandazione. La terza colonna indica la gravità del problema, come illustrato nello screenshot seguente:

![Raccomandazioni per le macchine virtuali](./media/security-center-monitoring/security-center-monitoring-fig2-sep2017.png)

> [!NOTE]
> Nell'elenco **Network topology** (Topologia di rete) del pannello **Networking Health** (Integrità rete) vengono visualizzate soltanto le macchine virtuali con almeno un endpoint pubblico.
>

Ogni raccomandazione è inoltre associata a una serie di azioni che è possibile eseguire dopo aver fatto clic su di essa. Se ad esempio si fa clic su **Aggiornamenti di sistema mancanti**, verrà visualizzato un elenco di macchine virtuali e computer senza patch e il livello di gravità per l'aggiornamento mancante, come illustrato nello screenshot seguente:

![Aggiornamenti di sistema mancanti per le macchine virtuali](./media/security-center-monitoring/security-center-monitoring-fig9-sep2017.png)

L'elenco **Aggiornamenti di sistema mancanti** visualizza un riepilogo degli aggiornamenti critici in un formato grafico, uno per Windows e uno per Linux. La seconda parte visualizza una tabella con le informazioni seguenti:

* **NOME**: nome dell'aggiornamento mancante.
* **N. di VM e computer**: numero totale di VM e computer cui manca l'aggiornamento.
* **STATO**: stato attuale della raccomandazione:
  * **Aperta**: la raccomandazione non è ancora stata risolta.
  * **In corso**: la raccomandazione è stata applicata alle risorse e non è richiesta alcuna azione da parte dell'utente.
  * **Risolta**: la raccomandazione è stata già completata. Quando il problema è stato risolto, la voce viene visualizzata in grigio.
* **GRAVITÀ**: descrive il livello di gravità della raccomandazione:
  * **Elevata**: esiste una vulnerabilità associata a una risorsa significativa, ad esempio applicazione, macchina virtuale, gruppo di sicurezza di rete, che richiede attenzione.
  * **Media**: sono necessari passaggi aggiuntivi, o non critici, per completare un processo o eliminare una vulnerabilità.
  * **Bassa**: vulnerabilità che è opportuno risolvere, ma non richiede attenzione immediata. Per impostazione predefinita, le raccomandazioni con gravità bassa non appaiono, ma è possibile visualizzarle applicando il filtro corrispondente.

Per visualizzare i dettagli delle raccomandazioni, fare clic sul nome dell'aggiornamento mancante nell'elenco.

![Aggiornamenti di sistema mancanti per una macchina virtuale specifica](./media/security-center-monitoring/security-center-monitoring-fig4-sep2017.png)

> [!NOTE]
> Le raccomandazioni sulla sicurezza contenute qui sono identiche a quelle nel pannello **Raccomandazioni**. Per altre informazioni su come gestire correttamente le raccomandazioni, leggere l'articolo sull'[implementazione delle raccomandazioni sulla sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md). Questo vale non solo per le macchine virtuali e i computer, ma anche per tutte le risorse disponibili nel riquadro **Integrità risorse**.
>

#### <a name="vms--computers-section"></a>Sezione VM e computer
La sezione relativa alle macchine virtuali e ai computer offre una panoramica di tutte le raccomandazioni per macchine virtuali e computer. Ogni colonna rappresenta un set di raccomandazioni, come illustrato nello screenshot seguente:

![Panoramica di tutte le macchine virtuali e delle raccomandazioni](./media/security-center-monitoring/security-center-monitoring-fig5-sep2017.png)

Nell'elenco sono rappresentati quattro tipi di icone, come illustrato in questo elenco:

![icon1](./media/security-center-monitoring/security-center-monitoring-icon1.png) Computer non Azure.

![icon2](./media/security-center-monitoring/security-center-monitoring-icon2.png) VM di Azure Resource Manager.

![icon3](./media/security-center-monitoring/security-center-monitoring-icon3.png) VM classica di Azure.

![icon4](./media/security-center-monitoring/security-center-monitoring-icon4.png) VM identificate solo dall'area di lavoro che fa parte della sottoscrizione visualizzata. Comprende le macchine virtuali di altre sottoscrizioni che inviano report all'area di lavoro in questa sottoscrizione e le macchine virtuali che sono state installate con l'agente SCOM diretto e non hanno un ID di risorsa.

L'icona visualizzata sotto ogni raccomandazione consente di identificare rapidamente le macchine virtuali e i computer che richiedono attenzione e il tipo di raccomandazione. È anche possibile usare l'opzione **Filtro** per selezionare le opzioni da visualizzare in schermata.

![Filtro](./media/security-center-monitoring/security-center-monitoring-fig6-sep2017.png)

Nell'esempio precedente, per una macchina virtuale è disponibile una raccomandazione critica relativa a Endpoint Protection. Per ottenere altre informazioni relative alla macchina virtuale, fare clic su di essa:

![Dettagli della sicurezza delle macchine virtuali](./media/security-center-monitoring/security-center-monitoring-fig7-sep2017.png)

Qui sono visualizzasti i dettagli sulla sicurezza relativi alla macchina virtuale o al computer. Nella parte inferiore è possibile vedere l'azione consigliata e il livello di gravità di ogni problema.

#### <a name="cloud-services-section"></a>Sezione Servizi cloud
Per i servizi cloud viene creata una raccomandazione quando la versione del sistema operativo non è aggiornata, come illustrato nello screenshot seguente:

![Stato di integrità per i servizi cloud](./media/security-center-monitoring/security-center-monitoring-fig1-new006-2017.png)

In uno scenario in cui è presente una raccomandazione, a differenza dell'esempio precedente, è necessario seguire la procedura illustrata nella raccomandazione per aggiornare la versione del sistema operativo. Quando è disponibile un aggiornamento, verrà visualizzato un avviso di tipo rosso o arancione, in base alla gravità del problema. Quando si fa clic su questo avviso in un ruolo Web1 (che esegue Windows Server con l'app Web distribuita automaticamente in IIS) o un ruolo di lavoro1 (che esegue Windows Server con l'app Web distribuita automaticamente in IIS), verranno visualizzati altri dettagli su questa raccomandazione, come illustrato nello screenshot seguente:

![Dettagli del servizio cloud](./media/security-center-monitoring/security-center-monitoring-fig8-new3.png)

Per visualizzare una spiegazione più esaustiva riguardante questa raccomandazione, fare clic su **Aggiorna la versione del sistema operativo** nella colonna **DESCRIZIONE**.

![Raccomandazioni sui servizi cloud](./media/security-center-monitoring/security-center-monitoring-fig8-new4.png)  

### <a name="monitor-virtual-networks"></a>Monitorare le reti virtuali
Quando si fa clic sul riquadro **Rete**, si apre il pannello **Rete** con altri dettagli, come illustrato nello screenshot seguente:

![Pannello Rete](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

#### <a name="networking-recommendations"></a>Raccomandazioni di rete
Come per le informazioni sull'integrità delle risorse delle macchine virtuali, viene visualizzato un elenco riepilogativo dei problemi nella parte superiore e un elenco delle reti monitorate nella parte inferiore.

La sezione dei dettagli sullo stato della rete elenca i problemi di sicurezza potenziali e fornisce [raccomandazioni](security-center-network-recommendations.md). I possibili problemi possono includere:

* Firewall di nuova generazione non installato
* Gruppi di sicurezza di rete nelle subnet non abilitati
* Gruppi di sicurezza di rete nelle macchine virtuali non abilitati
* Limitare l'accesso esterno tramite endpoint esterni pubblici
* Endpoint con connessione Internet integri

Quando si fa clic su una raccomandazione, vengono visualizzati altri dettagli sulla raccomandazione, come illustrato nell'esempio seguente:

![Dettagli di una raccomandazione nel pannello Rete](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

In questo esempio, il pannello **Configura i gruppi di sicurezza di rete mancanti per le subnet** include un elenco di subnet e macchine virtuali prive della protezione dei gruppi di sicurezza di rete. Se si sfa clic sulla subnet a cui applicare il gruppo di sicurezza di rete, viene visualizzato il pannello **Scegli un gruppo di sicurezza di rete**. Qui è possibile selezionare il gruppo di sicurezza di rete più appropriato per la subnet oppure creare un nuovo gruppo di sicurezza di rete.

#### <a name="internet-facing-endpoints-section"></a>Sezione Endpoint con connessione Internet
Nella sezione **Endpoint con connessione Internet** è possibile visualizzare le macchine virtuali attualmente configurate con un endpoint con connessione Internet e il relativo stato corrente.

![Macchine virtuali configurate con stato ed endpoint con connessione Internet](./media/security-center-monitoring/security-center-monitoring-fig10-ga.png)

La tabella include il nome dell'endpoint che rappresenta la macchina virtuale, l'indirizzo IP con connessione Internet, lo stato corrente di gravità del gruppo di sicurezza di rete e del firewall di nuova generazione. Questa tabella è ordinata in base alla gravità:

* Rosso (in alto): priorità elevata e da risolvere immediatamente
* Arancione: priorità media e da risolvere appena possibile
* Verde (ultimo): stato di integrità

#### <a name="networking-topology-section"></a>Sezione Topologia di rete
La sezione **Topologia di rete** include una visualizzazione gerarchica delle risorse, come illustrato nello screenshot seguente:

![Visualizzazione gerarchica di risorse nella sezione Topologia di rete](./media/security-center-monitoring/security-center-monitoring-fig121-new4.png)

Questa tabella è ordinata (macchine virtuali e subnet) in base alla gravità:

* Rosso (in alto): priorità elevata e da risolvere immediatamente
* Arancione: priorità media e da risolvere appena possibile
* Verde (ultimo): stato di integrità

Nella visualizzazione della topologia il primo livello include [Reti virtuali](../virtual-network/virtual-networks-overview.md), [Gateway di rete virtuale](/vpn-gateway/vpn-gateway-site-to-site-create.md) e [Rete virtuale (versione classica)](/virtual-network/virtual-networks-create-vnet-classic-pportal.md). Il secondo livello include le subnet e il terzo livello include le macchine virtuali appartenenti alle subnet. La colonna destra include lo stato attuale del gruppo di sicurezza di rete per queste risorse, come illustrato nell'esempio seguente:

![Stato del gruppo di sicurezza di rete nella sezione Topologia di rete](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

La parte inferiore del pannello include le raccomandazioni per questa macchina virtuale, simili a quanto illustrato in precedenza. È possibile fare clic su una raccomandazione per ottenere altre informazioni o per applicare la configurazione o il controllo di sicurezza necessario.

### <a name="monitor-storage--data"></a>Monitoraggio di archiviazione e dati

Quando si fa clic su **Archiviazione e dati** nella sezione **Prevenzione**, si apre il pannello **Risorse dati** con raccomandazioni relative a SQL e all'archiviazione. Il pannello include anche [raccomandazioni](security-center-sql-service-recommendations.md) sullo stato di integrità generale del database. Per altre informazioni sulla crittografia per l'archiviazione, vedere [Enable encryption for Azure storage account in Azure Security Center](security-center-enable-encryption-for-storage-account.md) (Abilitare la crittografia per l'account di archiviazione di Azure nel Centro sicurezza di Azure).

![Risorse dati](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

In **Raccomandazioni su SQL** è possibile fare clic su qualsiasi raccomandazione e ottenere maggiori dettagli sulle ulteriori azioni per risolvere un problema. L'esempio seguente illustra l'espansione della raccomandazione **Database Auditing & Threat detection on SQL databases** (Controllo database e rilevamento minacce nei database SQL).

![Informazioni dettagliate su una raccomandazione SQL](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

Il pannello **Abilita il controllo e il rilevamento minacce nei database SQL** contiene le informazioni seguenti:

* Elenco di database SQL.
* Server in cui si trovano.
* Informazioni sull'impostazione, ovvero se è stata ereditata dal server o se è univoca in questo database.
* Stato corrente.
* Gravità del problema.

Quando si fa clic sul database per applicare la raccomandazione, viene aperto il pannello **Controllo e rilevamento minacce** come illustrato nella schermata seguente.

![Controllo e rilevamento minacce](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Per abilitare il controllo, selezionare semplicemente **SÌ** nell'opzione **Controllo**.

### <a name="monitor-applications"></a>Monitorare le applicazioni

Se il carico di lavoro di Azure include applicazioni che si trovano nelle [macchine virtuali create tramite Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md) con porte Web esposte (porte TCP 80 e 443), il Centro sicurezza può monitorarle per identificare potenziali problemi di sicurezza, raccomandando le procedure di correzione. Quando si fa clic sul riquadro **Applicazioni**, si apre il pannello **Applicazioni** con una serie di raccomandazioni nella sezione **Raccomandazioni sulle applicazioni**. Il pannello visualizza anche il dettaglio dell'applicazione per host e IP/dominio e indica se è presente una soluzione WAF installata:

![Integrità sicurezza delle applicazioni](./media/security-center-monitoring/security-center-monitoring-fig8-sep2017.png)

Proprio come per le altre raccomandazioni, è possibile fare clic su una raccomandazione per visualizzare altri dettagli sul problema e su come risolverlo. L'esempio illustrato nella figura seguente è un'applicazione identificata come applicazione Web non sicura. Selezionando l'applicazione considerata non sicura, è disponibile l'opzione seguente:

![Dettagli](./media/security-center-monitoring/security-center-monitoring-fig17-ga.png)

Questo pannello visualizza un elenco di tutte le raccomandazioni per l'applicazione. Facendo clic sulla raccomandazione **Aggiungi un web application firewall** si aprirà il pannello **Aggiungi un web application firewall** con opzioni per installare un web application firewall (WAF) di un partner, come illustrato nello screenshot seguente.

![Finestra di dialogo Aggiungi un web application firewall](./media/security-center-monitoring/security-center-monitoring-fig18-ga.png)

## <a name="see-also"></a>Vedere anche 
Questo articolo descrive come usare le funzionalità di monitoraggio nel Centro sicurezza di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): informazioni su come configurare le impostazioni di sicurezza nel Centro sicurezza di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): informazioni su come monitorare l'integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure.
