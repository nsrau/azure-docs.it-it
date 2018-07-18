---
title: Monitoraggio della sicurezza nel Centro sicurezza di Azure | Documentazione di Microsoft
description: Questo articolo illustra come iniziare a usare le funzionalità di monitoraggio nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2018
ms.author: terrylan
ms.openlocfilehash: 434b73d4625f86fab195dbda1fed9c841791f5b6
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37099460"
---
# <a name="security-health-monitoring-in-azure-security-center"></a>Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure
Questo articolo descrive come usare le funzionalità di monitoraggio nel Centro sicurezza di Azure per verificare la conformità ai criteri.

## <a name="what-is-security-health-monitoring"></a>Che cos'è il monitoraggio dello stato di sicurezza?
Spesso si pensa al monitoraggio come l'osservazione e l'attesa di un evento al fine di reagire alla situazione verificatasi. Per monitoraggio della sicurezza si intende una strategia proattiva per il controllo delle risorse, al fine di identificare i sistemi non conformi agli standard o alle procedure consigliate della società.

## <a name="monitoring-security-health"></a>Monitoraggio dello stato di sicurezza
Dopo avere abilitato i [criteri di sicurezza](security-center-policies.md) per le risorse di una sottoscrizione, il Centro sicurezza analizza la sicurezza delle risorse per identificare le potenziali vulnerabilità. Le informazioni sulla configurazione di rete sono disponibili immediatamente. A seconda del numero di macchine virtuali e computer con l'agente installato, potrebbe essere necessaria almeno un'ora per raccogliere informazioni sulle macchine virtuali e la configurazione dei computer, ad esempio lo stato di aggiornamento della sicurezza e la configurazione del sistema operativo. Nella sezione **Prevenzione** è possibile visualizzare lo stato di sicurezza delle risorse ed eventuali problemi rilevati. Anche nel riquadro **Raccomandazioni** è disponibile l'elenco dei problemi riscontrati.

Per altre informazioni su come applicare le raccomandazioni, leggere l'articolo sull'[implementazione delle raccomandazioni sulla sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).

In **Monitoraggio dell'integrità delle risorse** è possibile monitorare lo stato di sicurezza delle risorse. Nell'esempio seguente, è possibile notare che il riquadro di ogni risorsa (Risorse di calcolo e app, Rete, Sicurezza dei dati e Identità e accesso) indica il numero totale dei problemi identificati.

![Riquadro Integrità sicurezza delle risorse](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)


### <a name="monitor-compute--apps"></a>Monitorare le risorse di calcolo e le app
Per altre informazioni, vedere [Protezione delle macchine virtuali nel Centro sicurezza di Azure](security-center-virtual-machine-recommendations.md).

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

Nella visualizzazione della topologia il primo livello include [Reti virtuali](../virtual-network/virtual-networks-overview.md), [Gateway di rete virtuale](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) e [Rete virtuale (versione classica)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Il secondo livello include le subnet e il terzo livello include le macchine virtuali appartenenti alle subnet. La colonna destra include lo stato attuale del gruppo di sicurezza di rete per queste risorse, come illustrato nell'esempio seguente:

![Stato del gruppo di sicurezza di rete nella sezione Topologia di rete](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

La parte inferiore del pannello include le raccomandazioni per questa macchina virtuale, simili a quanto illustrato in precedenza. È possibile fare clic su una raccomandazione per ottenere altre informazioni o per applicare la configurazione o il controllo di sicurezza necessario.

### <a name="monitor-data-security"></a>Monitorare la sicurezza dei dati

Quando si fa clic su **Sicurezza dei dati** nella sezione **Prevenzione**, si apre il pannello **Risorse dati** con consigli relativi a SQL e all'archiviazione. Il pannello include anche [raccomandazioni](security-center-sql-service-recommendations.md) sullo stato di integrità generale del database. Per altre informazioni sulla crittografia per l'archiviazione, vedere [Enable encryption for Azure storage account in Azure Security Center](security-center-enable-encryption-for-storage-account.md) (Abilitare la crittografia per l'account di archiviazione di Azure nel Centro sicurezza di Azure).

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

### <a name="monitor-identity--access"></a>Monitorare identità e accesso

Per altre informazioni, vedere [Monitorare identità e accesso nel Centro sicurezza di Azure](security-center-identity-access.md).

## <a name="see-also"></a>Vedere anche 
Questo articolo descrive come usare le funzionalità di monitoraggio nel Centro sicurezza di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): informazioni su come configurare le impostazioni di sicurezza nel Centro sicurezza di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): informazioni su come monitorare l'integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure.
