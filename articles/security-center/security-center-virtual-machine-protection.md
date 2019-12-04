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
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 4a6d733b490edd892136f6febcc90c29a5a865e1
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766804"
---
# <a name="protect-your-machines-and-applications"></a>Proteggi le tue macchine e le tue applicazioni
Quando il Centro sicurezza identifica le potenziali vulnerabilità di sicurezza, vengono creati consigli che guidano l'utente nel processo di configurazione dei controlli necessari. 

Questo articolo illustra la pagina **calcolo e app** della sezione sicurezza delle risorse del Centro sicurezza di Azure. Vengono inoltre descritte alcune delle raccomandazioni che verranno visualizzate.

Per un elenco completo delle raccomandazioni per i servizi di calcolo e app, vedere le raccomandazioni relative a [calcolo e app](recommendations-compute-and-apps.md).

## <a name="view-the-security-of-your-compute-and-apps-resources"></a>Visualizzazione della sicurezza delle risorse di calcolo e app

![Dashboard del Centro sicurezza](./media/security-center-virtual-machine-recommendations/overview.png)

Per visualizzare lo stato delle risorse di calcolo e app, selezionare **calcolo & app** in **risorse** nell'intestazione laterale del Centro sicurezza. Sono disponibili le schede seguenti:

* **Panoramica**: elenca le raccomandazioni per tutte le risorse di calcolo e app, nonché lo stato di sicurezza corrente 

* [**VM e computer**](#vms-and-computers): elenca le raccomandazioni per le macchine virtuali, i computer e lo stato di sicurezza corrente di ogni

* [**Set di scalabilità di macchine virtuali**](#vmscale-sets): elenca le raccomandazioni per i set di scalabilità, 

* [**Servizi cloud**](#cloud-services): elenca le raccomandazioni per i ruoli Web e di lavoro monitorati dal centro sicurezza

* [**Servizi app**](#app-services): elenca le raccomandazioni per gli ambienti del servizio app e lo stato di sicurezza corrente di ogni

* **Contenitori**: elenca le raccomandazioni per i contenitori e la valutazione della sicurezza delle configurazioni

* **Risorse di calcolo**: elenca le raccomandazioni per le risorse di calcolo, ad esempio cluster Service Fabric e hub eventi

### <a name="whats-in-each-tab"></a>Che cosa è in ogni scheda?

Ogni scheda include più sezioni e in ogni sezione è possibile eseguire il drill-down per visualizzare ulteriori dettagli sull'elemento visualizzato.

In ogni scheda vengono visualizzate anche le raccomandazioni per le risorse rilevanti nell'ambiente monitorato. Nella prima colonna viene elencata la raccomandazione, la seconda indica il numero totale di risorse interessate e la terza indica la gravità del problema.

Ogni raccomandazione è inoltre associata a una serie di azioni che è possibile eseguire dopo averla selezionata. Se ad esempio si seleziona **Aggiornamenti di sistema mancanti**, verrà visualizzato il numero di macchine virtuali e computer senza patch e il livello di gravità per l'aggiornamento mancante.

> [!NOTE]
> Le raccomandazioni sulla sicurezza sono le stesse della pagina **raccomandazioni** , ma in questo caso vengono filtrate per il tipo di risorsa specifico selezionato. Per altre informazioni su come risolvere le raccomandazioni, vedere [implementazione delle raccomandazioni di sicurezza nel centro sicurezza di Azure](security-center-recommendations.md).
>

### <a name="vms-and-computers"></a>VM e computer
La sezione VM e computer offre una panoramica di tutte le raccomandazioni sulla sicurezza per le macchine virtuali e i computer. Sono inclusi quattro tipi di computer:

![Computer non Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Computer non Azure.

![VM di Azure Resource Manager](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) VM di Azure Resource Manager.

![VM classica di Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) VM classica di Azure.

![VM identificate dall'area di lavoro](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) VM identificate solo dall'area di lavoro che fa parte della sottoscrizione visualizzata. Sono incluse le macchine virtuali di altre sottoscrizioni che inviano report all'area di lavoro in questa sottoscrizione e le VM installate con Operations Manager agente diretto e non hanno ID risorsa.

L'icona visualizzata sotto ogni raccomandazione consente di identificare rapidamente le macchine virtuali e i computer che richiedono attenzione e il tipo di raccomandazione. È anche possibile usare i filtri per cercare all'interno dell'elenco in base al **tipo di risorsa** e alla **gravità**.

Per eseguire il drill down delle raccomandazioni di sicurezza per ogni macchina virtuale, fare clic sulla macchina virtuale.
Qui sono visualizzasti i dettagli sulla sicurezza relativi alla macchina virtuale o al computer. Nella parte inferiore è possibile vedere l'azione consigliata e la gravità di ogni problema.
![Servizi cloud](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Servizi cloud
Per i servizi cloud viene creata una raccomandazione quando la versione del sistema operativo non è aggiornata.

![Servizi cloud](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

In uno scenario in cui è presente una raccomandazione, seguire i passaggi indicati nella raccomandazione per aggiornare il sistema operativo. Quando è disponibile un aggiornamento, verrà generato un avviso (rosso o arancione, a seconda della gravità del problema). Per una spiegazione completa di questa raccomandazione, fare clic su **Aggiorna versione sistema operativo** nella colonna **Descrizione** .

### <a name="app-services"></a>Servizi app
Per visualizzare le informazioni sul servizio app, è necessario avere il piano tariffario standard del Centro sicurezza e abilitare il servizio app nella sottoscrizione. Per istruzioni sull'abilitazione di questa funzionalità, vedere [proteggere il servizio app con il Centro sicurezza di Azure](security-center-app-services.md).


Nella sezione **Servizi app**, è riportato un elenco degli ambienti del Servizio app e il riepilogo dell'integrità eseguita sulla base della valutazione del Centro sicurezza.

![Servizi app](./media/security-center-virtual-machine-recommendations/app-services.png)

Sono disponibili tre tipi di servizi per le applicazioni:

![Ambiente dei Servizi app](./media/security-center-virtual-machine-recommendations/ase.png) Ambiente dei Servizi app

![Applicazione Web](./media/security-center-virtual-machine-recommendations/web-app.png) Applicazione Web

![Applicazione di funzione](./media/security-center-virtual-machine-recommendations/function-app.png) Applicazione di funzione

Se si seleziona un'applicazione Web, viene aperta una visualizzazione di riepilogo con tre schede:

   - **Suggerimenti**: basati sulle valutazioni eseguite dal centro sicurezza non riuscite.
   - **Valutazioni superate**: elenco di valutazioni superate eseguite dal Centro sicurezza.
   - **Valutazioni non disponibili**: elenco di valutazioni non eseguito a causa di un errore o poiché la raccomandazione non è rilevante per il Servizio app specifico

   Nella sezione **Raccomandazioni** è presente un elenco di consigli per l'applicazione selezionata, con l'indicazione del livello di gravità per ogni consiglio.

   ![Consigli per Servizi app](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Selezionare una raccomandazione per ottenerne la descrizione e l'elenco delle risorse non integre, integre e non analizzate.

   - La colonna **valutazioni passate** Mostra un elenco di valutazioni passate. Il livello di gravità di tali valutazioni è sempre di colore verde.

   - Selezionare una valutazione superata dall'elenco per visualizzarne la descrizione e l'elenco di risorse non integre, integre e non analizzate. È presente una scheda per le risorse di tipo non integro, ma tale elenco è sempre vuoto perché la valutazione è superata.

### <a name="vmscale-sets"></a>Set di scalabilità di macchine virtuali
Il Centro sicurezza rileva automaticamente se sono presenti set di scalabilità e consiglia di installare il Microsoft Monitoring Agent su di essi.

Per installare Microsoft Monitoring Agent: 

1. Selezionare la raccomandazione **Install the monitoring agent on virtual machine scale set.**  (Installare l'agente di monitoraggio nel set di scalabilità di macchine virtuali). Si otterrà un elenco dei set di scalabilità non monitorati.

1. Selezionare un set di scalabilità non integro. Seguire le istruzioni per installare l'agente di monitoraggio mediante un'area di lavoro popolata esistente o crearne una nuova. Assicurarsi di impostare il [piano tariffario](security-center-pricing.md)dell'area di lavoro se non è impostato.

   ![Installare Microsoft Monitoring Agent](./media/security-center-virtual-machine-recommendations/install-mms.png)

Per impostare nuovi set di scalabilità per installare automaticamente i Microsoft Monitoring Agent:
1. Passare a Criteri di Azure e fare clic su **Definizioni**.

1. Cercare i criteri **distribuisci log Analytics Agent per i set di scalabilità di macchine virtuali Windows** e fare clic su di esso.

1. Fare clic su **Assegna**.

1. Impostare l'**ambito** e l'**area di lavoro Log Analytics** e fare clic su **Assegna**.

Se si desidera impostare tutti i set di scalabilità esistenti affinché installino Microsoft Monitoring Agent, in Criteri di Azure passare a **Correzione** e applicare i criteri esistenti ai set di scalabilità esistenti.


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui suggerimenti applicabili ad altri tipi di risorse di Azure, vedere gli articoli seguenti:

* [Monitoraggio dell'identità e dell'accesso nel Centro sicurezza di Azure](security-center-identity-access.md)
* [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md)
* [Protezione del servizio SQL di Azure nel Centro sicurezza di Azure](security-center-sql-service-recommendations.md)
