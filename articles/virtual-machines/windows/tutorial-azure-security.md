---
title: Gestione della sicurezza delle VM Windows con il Centro sicurezza di Azure | Microsoft Docs
description: 'Esercitazione: gestione della sicurezza delle VM con il Centro sicurezza di Azure'
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/01/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: b6fc4e710de740caf78dd1dcebc5432824add06c
ms.contentlocale: it-it
ms.lasthandoff: 05/03/2017

---
# <a name="monitor-vm-security-with-the-azure-security-center"></a>Monitorare la sicurezza delle VM con il Centro sicurezza di Azure

Il Centro sicurezza di Azure offre visibilità sulla configurazione delle risorse di Azure in relazione alle attività legate alla sicurezza. Include inoltre funzionalità integrate di monitoraggio della sicurezza che consentono di rilevare minacce che altrimenti passerebbero inosservate. Questa esercitazione presenta una panoramica del Centro sicurezza di Azure e illustra come usarlo con le macchine virtuali di Azure.   

## <a name="security-center-overview"></a>Panoramica del Centro sicurezza di Azure

Il Centro sicurezza di Azure aiuta a identificare i potenziali problemi di configurazione e le minacce specifiche alla sicurezza delle VM. Consente, ad esempio, l'identificazione di VM senza gruppi di sicurezza di rete, con dischi non crittografati e l'individuazione di attacchi di forza bruta al protocollo RDP. Queste informazioni vengono presentate nel dashboard del Centro sicurezza di Azure sotto forma di grafici di facile lettura.

Per accedere al dashboard di Centro sicurezza di Azure, fare clic su **Centro sicurezza** nel riquadro di spostamento di sinistra nel portale di Azure. Il dashboard offre una vista di alto livello dell'integrità delle risorse, gli avvisi di protezione e le raccomandazioni sulla configurazione. Da questo dashboard è possibile visualizzare l'integrità della sicurezza dell'ambiente Azure, accedere a una serie di raccomandazioni valide e visualizzare lo stato corrente degli avvisi di minaccia. Ognuno di questi grafici di alto livello può essere espanso e mostrare così maggiori dettagli sull'area di interesse.

![Dashboard del Centro sicurezza di Azure](./media/tutorial-azure-security/asc-dash.png)

La funzione del Centro sicurezza di Azure va oltre l'analisi dei dati, fornendo anche raccomandazioni per risolvere i problemi rilevati. Se ad esempio una macchina virtuale è stata distribuita senza un gruppo di sicurezza di rete associato, il Centro visualizza una raccomandazione che include i passaggi per risolvere il problema. Le raccomandazioni offrono inoltre la possibilità di risolvere il problema automaticamente senza uscire dal contesto del Centro sicurezza di Azure.  

![Raccomandazioni](./media/tutorial-azure-security/recommendations.png)

## <a name="configure-data-collection"></a>Configurare la raccolta dei dati

Per ottenere la visibilità sulle configurazioni di sicurezza delle macchine virtuali, è necessario prima configurare la raccolta dei dati nel Centro sicurezza di Azure. Ciò comporta l'abilitazione della raccolta dei dati e la creazione di un account di archiviazione di Azure per contenere i dati raccolti. 

1. Nel dashboard del Centro sicurezza di Azure fare clic su **Criteri di sicurezza** e selezionare la sottoscrizione in uso. 
2. In **Raccolta di dati** selezionare *On* (Abilitata).
3. Fare clic su **Scegliere un account di archiviazione** e crearne uno nuovo. Selezionare **OK** al termine dell'operazione.
4. Fare clic su **Salva** nel pannello **Criteri di sicurezza**. 

L'agente di raccolta dati del Centro sicurezza di Azure verrà installato in tutte le macchine virtuali e inizierà la raccolta dei dati. 

## <a name="configure-security-policy"></a>Configurare i criteri di sicurezza

I criteri di sicurezza definiscono gli elementi dei criteri di sicurezza per i quali vengono raccolti i dati e vengono proposte raccomandazioni. Per impostazione predefinita, le risorse di Azure vengono valutate rispetto a tutti gli elementi dei criteri. È possibile disabilitare singoli elementi di criteri a livello globale per tutte le risorse di Azure oppure disabilitarli a livello di gruppo di risorse. Questa configurazione consente di applicare criteri di sicurezza diversi a set diversi di risorse di Azure. Per altre informazioni sui criteri di sicurezza del Centro sicurezza di Azure, vedere [Impostare i criteri di sicurezza nel Centro sicurezza di Azure](../../security-center/security-center-policies.md). 

Per configurare i criteri di sicurezza per tutte le risorse di Azure:

1. Nel dashboard del Centro sicurezza di Azure fare clic su **Criteri di sicurezza** e selezionare la sottoscrizione in uso. 
2. Fare clic su **Prevention policy** (Criteri di protezione).
3. Abilitare o disabilitare gli elementi di criteri che devono essere applicati a tutte le risorse di Azure.
4. Fare clic su **OK** al termine dell'operazione.
5. Fare clic su **Salva** nel pannello **Criteri di sicurezza**. 

Per configurare i criteri per un gruppo di risorse specifico, seguire la stessa procedura, ma, invece di selezionare la sottoscrizione nel pannello dei criteri di sicurezza, selezionare un gruppo di risorse. Quando si configurano i criteri, selezionare *Unique* (Univoci) in **Ereditarietà**. Se si vuole disabilitare la raccolta dei dati per un gruppo di risorse specifico, anche questa configurazione può essere effettuata qui.

Nell'esempio seguente sono stati creati criteri univoci per un gruppo di risorse denominato *myResoureGroup*. In questi criteri sono state disabilitate sia le raccomandazioni relative alla crittografia del disco che quelle relative al firewall dell'applicazione Web.

![Criteri univoci](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Visualizzare lo stato della configurazione delle VM

Dopo che è stata abilitata la raccolta dei dati e sono stati configurati i criteri di sicurezza, il Centro sicurezza di Azure inizia a fornire avvisi e raccomandazioni. Quando le VM vengono distribuite, l'agente di raccolta dati viene installato e il Centro sicurezza di Azure viene popolato con i dati per queste nuove VM. Per altre informazioni sullo stato della configurazione delle VM, vedere [Protezione delle macchine virtuali nel Centro sicurezza di Azure](../../security-center/security-center-virtual-machine-recommendations.md). 

Man mano che i dati vengono raccolti, i dati relativi all'integrità delle risorse di ogni VM e delle risorse di Azure correlate vengono aggregati e presentati sotto forma di un grafico di facile lettura. Per visualizzare l'integrità delle risorse, tornare al dashboard del Centro sicurezza di Azure. In **Resource security health** (Integrità sicurezza risorse) fare clic su **Compute** (Calcola). Nel pannello **Compute** (Calcola) fare quindi clic su **Virtual machines** (Macchine virtuali). Questa visualizzazione riepiloga lo stato della configurazione di tutte le VM.

![Stato del calcolo](./media/tutorial-azure-security/compute-health.png)

Quando si seleziona una macchina virtuale, vengono visualizzate tutte le raccomandazioni inerenti. La sezione successiva di questa esercitazione descrive in dettaglio queste raccomandazioni.

## <a name="remediate-configuration-issues"></a>Risolvere i problemi di configurazione

Quando il Centro sicurezza di Azure inizia da popolarsi con i dati di configurazione, visualizza raccomandazioni in base ai criteri di sicurezza configurati. Se ad esempio è stata configurata una macchina virtuale senza un gruppo di sicurezza di rete associato, viene visualizzata una raccomandazione per crearne uno. Per visualizzare un elenco di tutte le raccomandazioni: 

1. Nel dashboard del Centro sicurezza di Azure fare clic su **Raccomandazioni**.
3. Se si seleziona una raccomandazione specifica, viene visualizzato un pannello con un elenco di tutte le risorse a cui si applica tale raccomandazione.
4. Selezionare una risorsa specifica interessata.
5. Seguire le istruzioni su schermo per i passaggi di risoluzione. 

Il Centro sicurezza di Azure fornisce in molti casi le procedure per applicare la raccomandazione senza uscire dal contesto del Centro. Nell'esempio seguente è stato rilevato un gruppo di sicurezza di rete in cui è presente una regola in entrata senza restrizioni. Da questa raccomandazione è possibile selezionare il pulsante **Modifica le regole in ingresso**, il quale visualizza l'interfaccia utente necessaria per modificare la regola. 

![Raccomandazioni](./media/tutorial-azure-security/remediation.png)

Le raccomandazioni risolte vengono contrassegnate come tali. 

## <a name="view-detected-threats"></a>Visualizzare le minacce rilevate

Oltre alle raccomandazioni per la configurazione delle risorse, il Centro sicurezza di Azure visualizza anche avvisi di rilevamento di minacce. La funzionalità di avviso di sicurezza aggrega i dati raccolti da ogni VM, i log di rete di Azure e le soluzioni partner connesse per rilevare le minacce alla sicurezza delle risorse di Azure. Per altre informazioni sulle funzionalità di rilevamento delle minacce nel Centro sicurezza di Azure, vedere [Funzionalità di rilevamento del Centro sicurezza di Azure](../../security-center/security-center-detection-capabilities.md).

Per accedere alla funzionalità degli avvisi di sicurezza, è necessario aumentare il piano tariffario del Centro sicurezza di Azure da *Gratuito** a *Standard**. Il piano tariffario Standard consente di usufruire di una **versione di valutazione gratuita** di 30 giorni. Per modificare il piano tariffario:  

1. Nel dashboard del Centro sicurezza di Azure fare clic su **Criteri di sicurezza** e selezionare la sottoscrizione in uso.
2. Fare clic su **Piano tariffario**.
3. Selezionare un nuovo livello e fare clic su **Seleziona**.
5. Fare clic su **Salva** nel pannello **Criteri di sicurezza**. 

Dopo che il nuovo piano è stato abilitato, il grafico degli avvisi di sicurezza inizia a popolarsi man mano che vengono rilevate minacce alla sicurezza.

![Avvisi di sicurezza](./media/tutorial-azure-security/security-alerts.png)

Selezionare un avviso per visualizzare informazioni quali una descrizione della minaccia, l'ora del rilevamento, i tentativi di minaccia e la soluzione consigliata. In questo esempio è stato rilevato un attacco di forza bruta RDP con 294 tentativi di attacco al protocollo RDP e viene fornita una soluzione consigliata.

![Attacco RDP](./media/tutorial-azure-security/rdp-attack.png)
