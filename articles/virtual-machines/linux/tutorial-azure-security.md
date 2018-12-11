---
title: 'Esercitazione: usare il Centro sicurezza di Azure per le macchine virtuali Linux in Azure | Microsoft Docs'
description: In questa esercitazione vengono descritte le funzionalità del Centro sicurezza di Azure che consentono di proteggere le macchine virtuali Linux in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/11/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: ba23e9b8ac79a0a2a3899332bfc373665cf561af
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52841167"
---
# <a name="tutorial-use-azure-security-center-to-monitor-linux-virtual-machines"></a>Esercitazione: usare il Centro sicurezza di Azure per monitorare le macchine virtuali Linux

Il Centro sicurezza di Azure consente di ottenere visibilità nelle procedure per la sicurezza delle risorse di Azure. Il Centro sicurezza offre il monitoraggio della sicurezza integrato. Consente di rilevare minacce che altrimenti non verrebbero rilevate. Questa esercitazione illustra il Centro sicurezza di Azure e come:
 
> [!div class="checklist"]
> * configurare la raccolta dei dati
> * configurare i criteri di sicurezza
> * visualizzare e risolvere i problemi di integrità della configurazione
> * esaminare le minacce rilevate

## <a name="security-center-overview"></a>Panoramica del Centro sicurezza di Azure

Il Centro sicurezza aiuta a identificare i potenziali problemi di configurazione e le minacce specifiche alla sicurezza delle macchina virtuale. Questi possono includere macchine virtuali mancanti dei gruppi di sicurezza di rete, dischi non crittografati e attacchi di forza bruta Remote Desktop Protocol (RDP). Queste informazioni vengono mostrate nel dashboard del Centro sicurezza sotto forma di grafici di facile lettura.

Per accedere al dashboard del Centro sicurezza, nel portale di Azure selezionare nel menu **Centro sicurezza**. Nel dashboard è possibile visualizzare l'integrità della sicurezza dell'ambiente Azure, accedere a una serie di raccomandazioni valide e visualizzare lo stato corrente degli avvisi di minaccia. È possibile espandere ogni grafico di alto livello per visualizzare altri dettagli.

![Dashboard del Centro sicurezza](./media/tutorial-azure-security/asc-dash.png)

Il Centro sicurezza va oltre l'individuazione dei dati per offrire raccomandazioni sui problemi rilevati. Ad esempio, se una macchina virtuale è stata distribuita senza un gruppo di sicurezza di rete collegato, il Centro sicurezza consente di visualizzare una raccomandazione, con una procedura di correzione da eseguire. Offre la correzione automatica senza lasciare il contesto del Centro sicurezza.  

![Consigli](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>Configurare la raccolta dei dati

Per ottenere la visibilità sulle configurazioni di sicurezza delle macchine virtuali, è necessario prima configurare la raccolta dei dati nel Centro sicurezza. Ciò comporta l'attivazione della raccolta dati, che installa automaticamente Microsoft Monitoring Agent su tutte le macchine virtuali nella sottoscrizione.

1. Nel dashboard del Centro sicurezza fare clic su **Criteri di sicurezza** e selezionare la sottoscrizione in uso. 
2. In **Provisioning automatico** in **Raccolta dati** selezionare **On**.
3. Per **Configurazione dell'area di lavoro predefinita** lasciare impostata l'opzione **Usa le aree di lavoro create dal Centro sicurezza (impostazione predefinita)**.
4. In **Livelli per gli eventi di sicurezza** mantenere l'opzione predefinita **Comune**.
4. Fare clic su **Salva** nella parte superiore della pagina. 

L'agente di raccolta dati del Centro sicurezza viene quindi installato in tutte le macchine virtuali e la raccolta dei dati ha inizio. 

## <a name="set-up-a-security-policy"></a>Configurare un criterio di sicurezza

I criteri di sicurezza vengono usati per definire gli elementi per cui il Centro sicurezza raccoglie i dati e genera raccomandazioni. È possibile applicare criteri di sicurezza diversi per set di risorse di Azure diversi. Sebbene per impostazione predefinita le risorse di Azure vengono valutate per tutti gli elementi dei criteri, è possibile disattivare gli elementi di criteri individuali per tutte le risorse di Azure o per un gruppo di risorse. Per informazioni approfondite sui criteri di sicurezza del Centro sicurezza, vedere [Impostare i criteri di sicurezza nel Centro sicurezza di Azure](../../security-center/security-center-policies.md). 

Per configurare criteri di sicurezza per un'intera sottoscrizione:

1. Nel dashboard del Centro sicurezza selezionare **Criteri di sicurezza** e quindi la sottoscrizione in uso.
2. Nel pannello **Criteri di sicurezza** selezionare **Criteri di sicurezza**. 
3. Nel pannello ** Criteri di sicurezza - Criteri di sicurezza ** attivare o disattivare gli elementi dei criteri da applicare alla sottoscrizione.
4. Al termine della selezione delle impostazioni, selezionare **Salva** nella parte superiore del pannello. 

![Criteri univoci](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Visualizzare lo stato della configurazione delle VM

Dopo aver attivato la raccolta dei dati e impostato un criterio di sicurezza, il Centro sicurezza inizia a generare avvisi e raccomandazioni. Man mano che le macchine virtuali vengono distribuite, viene installato l'agente di raccolta dati. Il Centro sicurezza viene quindi popolato con i dati per le nuove macchine virtuali. Per informazioni dettagliate sullo stato di configurazione delle macchine virtuali, vedere [Protezione delle macchine virtuali nel Centro sicurezza di Azure](../../security-center/security-center-virtual-machine-recommendations.md). 

Man mano che i dati vengono raccolti, i dati relativi all'integrità delle risorse di ogni macchina virtuale e delle risorse di Azure correlate vengono aggregati. Le informazioni vengono mostrate in un grafico di facile lettura. 

Per visualizzare l'integrità risorse:

1.  In **Prevenzione** nel dashboard del Centro sicurezza selezionare **Calcolo**. 
2.  Nel pannello **Calcolo** selezionare **VM e computer**. Questa visualizzazione riepiloga lo stato della configurazione di tutte le macchine virtuali.

![Stato del calcolo](./media/tutorial-azure-security/compute-health.png)

Per visualizzare tutte le raccomandazioni per una macchina virtuale, selezionare la macchina virtuale. 

## <a name="remediate-configuration-issues"></a>Risolvere i problemi di configurazione

Quando il Centro sicurezza inizia a popolarsi con i dati di configurazione, le raccomandazioni vengono generate in base ai criteri di sicurezza configurati. Se ad esempio è stata configurata una macchina virtuale senza un gruppo di sicurezza di rete associato, viene generata una raccomandazione per crearne uno. 

Per visualizzare un elenco di tutte le raccomandazioni: 

1. Nel dashboard del Centro sicurezza selezionare **Raccomandazioni**.
2. Selezionare una raccomandazione specifica. Viene visualizzato un elenco di tutte le risorse per cui si applica la raccomandazione.
3. Per applicare una raccomandazione, selezionare la risorsa. 
4. Seguire le istruzioni per la procedura di correzione. 

Il Centro sicurezza offre in molti casi le procedure pratiche per applicare una raccomandazione senza uscire dal Centro sicurezza. Nell'esempio seguente il Centro sicurezza rileva un gruppo di sicurezza di rete che dispone di una regola in entrata senza restrizioni. Nella pagina di raccomandazione è possibile selezionare il pulsante **Modifica le regole in ingresso**. Viene visualizzata l'interfaccia utente necessaria per modificare la regola. 

![Consigli](./media/tutorial-azure-security/remediation.png)

Le raccomandazioni risolte vengono contrassegnate come tali. 

## <a name="view-detected-threats"></a>Visualizzare le minacce rilevate

Oltre alle raccomandazioni per la configurazione delle risorse, il Centro sicurezza mostra avvisi di rilevamento di minacce. La funzionalità di avviso di sicurezza aggrega i dati raccolti da ogni macchina virtuale, dai log di rete di Azure e dalle soluzioni partner collegate per rilevare le minacce alla sicurezza per le risorse di Azure. Per informazioni dettagliate sulle funzionalità di rilevamento delle minacce nel Centro sicurezza, vedere [Funzionalità di rilevamento del Centro sicurezza di Azure](../../security-center/security-center-detection-capabilities.md).

Per accedere alla funzionalità degli avvisi di sicurezza, è necessario aumentare il piano tariffario del Centro sicurezza da *Gratuito* a *Standard*. Quando si passa a questo piano tariffario superiore, è disponibile una **versione di valutazione gratuita**. 

Per modificare il piano tariffario:  

1. Nel dashboard del Centro sicurezza fare clic su **Criteri di sicurezza** e selezionare la sottoscrizione in uso.
2. Selezionare **Piano tariffario**.
3. Selezionare **Standard** e quindi fare clic su **Salva** nella parte superiore del pannello.


Dopo che il piano tariffario è stato modificato, il grafico degli avvisi di sicurezza inizia a popolarsi man mano che vengono rilevate minacce alla sicurezza.

![Avvisi di sicurezza](./media/tutorial-azure-security/security-alerts.png)

Selezionare un avviso per visualizzare le informazioni. Ad esempio, è possibile visualizzare una descrizione della minaccia, il tempo di rilevamento, tutti i tentativi di minaccia e la correzione consigliata. Nell'esempio seguente è stato rilevato un attacco di forza bruta RDP, con 294 tentativi RDP non riusciti. Viene indicata una risoluzione consigliata.

![Attacco RDP](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione viene configurato il Centro sicurezza di Azure e vengono quindi esaminate le macchine virtuali nel Centro sicurezza. Si è appreso come:

> [!div class="checklist"]
> * Configurare la raccolta dei dati
> * configurare i criteri di sicurezza
> * visualizzare e risolvere i problemi di integrità della configurazione
> * Esaminare le minacce rilevate

Passare all'esercitazione successiva per altre informazioni sulla creazione di una pipeline CI/CD con Jenkins, GitHub e Docker.

> [!div class="nextstepaction"]
> [Creare un'infrastruttura di integrazione continua e di distribuzione continua con Jenkins, GitHub e Docker](tutorial-jenkins-github-docker-cicd.md)

