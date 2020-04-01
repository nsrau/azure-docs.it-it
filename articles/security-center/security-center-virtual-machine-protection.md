---
title: Proteggi le tue macchine e le tue applicazioni
description: Questo documento illustra le raccomandazioni presenti nel Centro sicurezza che facilitano la protezione delle macchine virtuali, dei computer, delle app Web e degli ambienti di Servizio app di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: 93dde2e873a00303dcb8563caed4d56dbf11cc12
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435640"
---
# <a name="protect-your-machines-and-applications"></a>Proteggi le tue macchine e le tue applicazioni
Quando il Centro sicurezza di Azure identifica potenziali vulnerabilità della sicurezza, crea suggerimenti che guidano l'utente attraverso il processo di configurazione dei controlli necessari per la protezione e la protezione delle risorse.

Questo articolo illustra la pagina **Calcolo e app** della sezione relativa alla sicurezza delle risorse del Centro sicurezza.

Per un elenco completo dei consigli che potresti visualizzare in questa pagina, vedi Suggerimenti per [il calcolo e le app.](recommendations-reference.md#recs-computeapp)


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>Visualizzare la sicurezza delle risorse di elaborazione e app

[![Dashboard del Centro sicurezza](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

Per visualizzare lo stato delle risorse di elaborazione e delle app, nel riquadro sinistro del Centro sicurezza selezionare **Calcola & app.** Sono disponibili le seguenti schede:

* **Panoramica**: elenca i consigli per tutte le risorse di calcolo e app, nonché il loro stato di sicurezza corrente 

* [**Macchine virtuali e server:**](#vms-and-computers)elenca i consigli per le macchine virtuali, i computer e lo stato di sicurezza corrente di

* Set di [**scalabilità delle macchine virtuali:**](#vmscale-sets)elenca i consigli per i set di scalabilità, 

* [**Servizi cloud:**](#cloud-services)elenca i consigli per i ruoli Web e di lavoro monitorati dal Centro sicurezza

* [**Servizi app:**](#app-services)elenca i consigli per gli ambienti del servizio app e lo stato di sicurezza corrente di ogni

* [**Contenitori**](#containers): elenca i consigli per i contenitori e la valutazione della sicurezza delle loro configurazioni

* **Risorse di calcolo:** elenca i suggerimenti per le risorse di calcolo, ad esempio i cluster di Service Fabric e gli hub eventi

### <a name="whats-in-each-tab"></a>Cosa c'è in ogni scheda?

Ogni scheda ha più sezioni e in ogni sezione è possibile eseguire il drill-down per visualizzare ulteriori dettagli sull'elemento visualizzato.

In ogni scheda verranno inoltre visualizzati suggerimenti per le risorse pertinenti nell'ambiente monitorato. La prima colonna elenca la raccomandazione, la seconda mostra il numero totale di risorse interessate e la terza mostra la gravità del problema.

Ogni raccomandazione è inoltre associata a una serie di azioni che è possibile eseguire dopo averla selezionata. Se ad esempio si seleziona **Aggiornamenti di sistema mancanti**, verrà visualizzato il numero di macchine virtuali e computer senza patch e il livello di gravità per l'aggiornamento mancante.

> [!NOTE]
> I consigli per la sicurezza sono gli stessi di quelli nella pagina **Raccomandazioni,** ma qui vengono filtrati in base al tipo di risorsa specifico selezionato. Per altre informazioni su come risolvere i suggerimenti, vedere [Implementazione di consigli](security-center-recommendations.md)sulla sicurezza nel Centro sicurezza di Azure.For more information about how to resolve recommendations, see Implementing security recommendations in Azure Security Center .
>




### <a name="vms-and-servers"></a><a name="vms-and-computers"></a>Macchine virtuali e server
La sezione Macchine virtuali e computer offre una panoramica di tutti i consigli di sicurezza per le macchine virtuali e i computer. Sono inclusi quattro tipi di macchine:

![Computer non Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Computer non Azure.

![VM di Azure Resource Manager](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) VM di Azure Resource Manager.

![VM classica di Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) VM classica di Azure.

![VM identificate dall'area di lavoro](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) VM identificate solo dall'area di lavoro che fa parte della sottoscrizione visualizzata. Sono incluse le macchine virtuali di altre sottoscrizioni che segnalano all'area di lavoro in questa sottoscrizione e le macchine virtuali installate con l'agente diretto di Operations Manager e che non hanno un ID risorsa.

L'icona visualizzata sotto ogni raccomandazione consente di identificare rapidamente le macchine virtuali e i computer che richiedono attenzione e il tipo di raccomandazione. È anche possibile usare i filtri per cercare all'interno dell'elenco in base al **tipo di risorsa** e alla **gravità**.

Per eseguire il drill down delle raccomandazioni di sicurezza per ogni macchina virtuale, fare clic sulla macchina virtuale.
Qui sono visualizzasti i dettagli sulla sicurezza relativi alla macchina virtuale o al computer. Nella parte inferiore, è possibile visualizzare l'azione consigliata e la gravità di ogni problema.

[![Servizi cloud](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="virtual-machine-scale-sets"></a><a name="vmscale-sets"></a>Set di scalabilità di macchine virtuali
Il Centro sicurezza rileva automaticamente se si dispone di set di scalabilità e consiglia di installare l'agente di Log Analytics su di essi.

Per installare l'agente di Log Analytics: 

1. Selezionare la raccomandazione **Install the monitoring agent on virtual machine scale set. ** (Installare l'agente di monitoraggio nel set di scalabilità di macchine virtuali). Si otterrà un elenco dei set di scalabilità non monitorati.

1. Selezionare un set di scalabilità non integro. Seguire le istruzioni per installare l'agente di monitoraggio mediante un'area di lavoro popolata esistente o crearne una nuova. Assicurarsi di impostare il [piano tariffario](security-center-pricing.md) dell'area di lavoro se non è impostata.

   ![Installare Microsoft Monitoring Agent](./media/security-center-virtual-machine-recommendations/install-mms.png)

Per impostare i nuovi set di scalabilità per l'installazione automatica dell'agente di Log Analytics:
1. Passare a Criteri di Azure e fare clic su **Definizioni**.

1. Cercare i criteri Distribuire l'agente Log Analytics per i set di **scalabilità** delle macchine virtuali Windows e fare clic su di esso.

1. Fare clic su **Assegna**.

1. Impostare l'**ambito** e l'**area di lavoro Log Analytics** e fare clic su **Assegna**.

Se si desidera impostare tutti i set di scalabilità esistenti per installare l'agente di Log Analytics, in Criteri di Azure passare a **Correzione** e applicare i criteri esistenti ai set di scalabilità esistenti.





### <a name="cloud-services"></a><a name="cloud-services"></a>Servizi cloud
Per i servizi cloud viene creata una raccomandazione quando la versione del sistema operativo non è aggiornata.

![Servizi cloud](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

In uno scenario in cui si dispone di un suggerimento, seguire i passaggi nella raccomandazione per aggiornare il sistema operativo. Quando è disponibile un aggiornamento, si avrà un avviso (rosso o arancione - a seconda della gravità del problema). Per una spiegazione completa di questa raccomandazione, fare clic su **Aggiorna versione del sistema operativo** nella colonna **DESCRIPTION.**






### <a name="app-services"></a><a name="app-services"></a>Servizi app
Per visualizzare le informazioni sul servizio app, è necessario essere nel piano tariffario Standard del Centro sicurezza e abilitare il servizio app nella sottoscrizione. Per istruzioni sull'abilitazione di questa funzionalità, vedere Proteggere il servizio app con il Centro sicurezza di [Azure.For](security-center-app-services.md)instructions on enabling this feature, see Protect App Service with Azure Security Center .

Nella sezione **Servizi app**, è riportato un elenco degli ambienti del Servizio app e il riepilogo dell'integrità eseguita sulla base della valutazione del Centro sicurezza.

![Servizi app](./media/security-center-virtual-machine-recommendations/app-services.png)

Sono disponibili tre tipi di servizi applicativi:

![Ambiente dei Servizi app](./media/security-center-virtual-machine-recommendations/ase.png) Ambiente dei Servizi app

![Applicazione Web](./media/security-center-virtual-machine-recommendations/web-app.png) Applicazione Web

![Applicazione di funzione](./media/security-center-virtual-machine-recommendations/function-app.png) Applicazione di funzione

Se si seleziona un'applicazione Web, viene aperta una visualizzazione di riepilogo con tre schede:

   - **Raccomandazioni:** in base alle valutazioni eseguite dal Centro sicurezza non riuscite.
   - **Valutazioni superate**: elenco di valutazioni superate eseguite dal Centro sicurezza.
   - **Valutazioni non disponibili**: elenco di valutazioni non eseguito a causa di un errore o poiché la raccomandazione non è rilevante per il Servizio app specifico

   Nella sezione **Raccomandazioni** è presente un elenco di consigli per l'applicazione selezionata, con l'indicazione del livello di gravità per ogni consiglio.

   ![Consigli per Servizi app](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Selezionare una raccomandazione per ottenerne la descrizione e l'elenco delle risorse non integre, integre e non analizzate.

   - La colonna **Valutazioni superate** mostra un elenco di valutazioni superate. Il livello di gravità di tali valutazioni è sempre di colore verde.

   - Selezionare una valutazione superata dall'elenco per visualizzarne la descrizione e l'elenco di risorse non integre, integre e non analizzate. È presente una scheda per le risorse di tipo non integro, ma tale elenco è sempre vuoto perché la valutazione è superata.





### <a name="containers"></a><a name="containers"></a>Contenitori

Quando si apre la scheda **Contenitori,** a seconda dell'ambiente, è possibile che venga visualizzato uno dei tre tipi di risorse:

![Host contenitore](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) Host contenitore - Macchine virtuali in esecuzione dockerContainer hosts - VMs running docker 

![Servizio Kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) Cluster del servizio Azure Kubernetes (AKS)Azure Kubernetes Service (AKS) clusters

![Registro contenitori](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) Registro del Registro di sistema contenitori di Azure: viene visualizzato solo quando si è nel piano tariffario standard e quando è abilitato il pacchetto del registro contenitori di Azure.Azure Container Registry (ACR) registries - Shown only when you're on the standard pricing tier and when you have the Azure Container Registry bundle enabled.

Per istruzioni su come utilizzare le funzionalità di sicurezza dei [contenitori, vedere Monitoraggio della sicurezza dei contenitori.](monitor-container-security.md)

I vantaggi del pacchetto del Registro di sistema del contenitore di Azure sono illustrati [qui](azure-container-registry-integration.md)

I vantaggi del pacchetto Kubernetes Services sono spiegati [qui](azure-kubernetes-service-integration.md)

[![Scheda Contenitori](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

Per visualizzare i suggerimenti per una risorsa specifica nell'elenco, fare clic su tale risorsa.

#### <a name="visibility-into-container-registries"></a>Visibilità nei registri dei container

Ad esempio, facendo clic sul registro ACR asc-demo dall'elenco mostrato nella figura sopra conduce a questa pagina dei dettagli:

[![Raccomandazioni per un registro ACR specifico](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>Visibilità dei contenitori ospitati in macchine virtuali Linux IaaS

Quando si fa clic su una delle macchine virtuali in esecuzione docker, verrà visualizzata la pagina dei dettagli con le informazioni relative ai contenitori nel computer, ad esempio la versione Docker e il numero di immagini in esecuzione nell'host.

![Consigli per una macchina virtuale in esecuzione dockerRecommendations for a VM running docker](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>Raccomandazioni per la sicurezza basate sul benchmark CIS per Docker

Il Centro sicurezza analizza le configurazioni di Docker e mostra le configurazioni errate, fornendo un elenco di tutte le regole non riuscite che sono state valutate. Visualizza inoltre linee guida per consentire di risolvere rapidamente questi problemi e risparmiare tempo. Il Centro sicurezza valuta costantemente le configurazioni di Docker e ne visualizza lo stato più recente.

![Scheda dei contenitori](./media/security-center-virtual-machine-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle raccomandazioni che si applicano ad altri tipi di risorse di Azure, vedere gli articoli seguenti:To learn more about recommendations that apply to other Azure resource types, see the following articles:

* [Elenco di riferimento completo dei consigli per la sicurezza del Centro sicurezza di Azure](recommendations-reference.md)
* [Monitoraggio dell'identità e dell'accesso nel Centro sicurezza di Azure](security-center-identity-access.md)
* [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md)
* [Protezione del servizio SQL di Azure nel Centro sicurezza di Azure](security-center-sql-service-recommendations.md)