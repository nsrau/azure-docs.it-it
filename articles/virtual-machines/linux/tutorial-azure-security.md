---
title: Centro sicurezza di Azure e macchine virtuali Linux in Azure | Microsoft Docs
description: Informazioni sulla sicurezza per macchine virtuali Linux in Azure con il Centro sicurezza di Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/07/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6b791b2e1dbaffc90145c325dea7a85bd8abd98c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="monitor-virtual-machine-security-by-using-azure-security-center"></a>Monitorare la sicurezza delle macchine virtuali con il Centro sicurezza di Azure

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

![Raccomandazioni](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>Configurare la raccolta dei dati

Per ottenere la visibilità sulle configurazioni di sicurezza delle macchine virtuali, è necessario prima configurare la raccolta dei dati nel Centro sicurezza. Ciò comporta l'abilitazione della raccolta dei dati e la creazione di un account di archiviazione di Azure per contenere i dati raccolti. 

1. Nel dashboard del Centro sicurezza fare clic su **Criteri di sicurezza** e selezionare la sottoscrizione in uso. 
2. Per **Raccolta di dati** selezionare **On** (Abilitata).
3. Per creare un account di archiviazione, selezionare **Scegliere un account di archiviazione**. Quindi selezionare **OK**.
4. Nel pannello **Criteri di sicurezza** selezionare **Salva**. 

L'agente di raccolta dati del Centro sicurezza viene quindi installato in tutte le macchine virtuali e la raccolta dei dati ha inizio. 

## <a name="set-up-a-security-policy"></a>Configurare un criterio di sicurezza

I criteri di sicurezza vengono usati per definire gli elementi per cui il Centro sicurezza raccoglie i dati e genera raccomandazioni. È possibile applicare criteri di sicurezza diversi per set di risorse di Azure diversi. Sebbene per impostazione predefinita le risorse di Azure vengono valutate per tutti gli elementi dei criteri, è possibile disattivare gli elementi di criteri individuali per tutte le risorse di Azure o per un gruppo di risorse. Per informazioni approfondite sui criteri di sicurezza del Centro sicurezza, vedere [Impostare i criteri di sicurezza nel Centro sicurezza di Azure](../../security-center/security-center-policies.md). 

Per configurare i criteri di sicurezza per tutte le risorse di Azure:

1. Nel dashboard del Centro sicurezza selezionare **Criteri di sicurezza** e quindi la sottoscrizione in uso.
2. Selezionare **Prevention policy** (Criteri di protezione).
3. Attivare o disattivare gli elementi dei criteri che si desidera applicare a tutte le risorse di Azure.
4. Al termine della selezione delle impostazioni, selezionare **OK**.
5. Nel pannello **Criteri di sicurezza** selezionare **Salva**. 

Per configurare un criterio per un gruppo di risorse specifico:

1. Nel dashboard del Centro sicurezza selezionare **Criteri di sicurezza** e quindi un gruppo di risorse.
2. Selezionare **Prevention policy** (Criteri di protezione).
3. Attivare o disattivare gli elementi dei criteri che si desidera applicare al gruppo di risorse.
4. In **EREDITARIETÀ** selezionare **Univoco**.
5. Al termine della selezione delle impostazioni, selezionare **OK**.
6. Nel pannello **Criteri di sicurezza** selezionare **Salva**.  

È anche possibile disattivare la raccolta dei dati per un gruppo di risorse specifico in questa pagina.

Nell'esempio seguente sono stati creati criteri univoci per un gruppo di risorse denominato *myResoureGroup*. In questi criteri sono state disabilitate le raccomandazioni relative alla crittografia del disco e al firewall dell'applicazione Web.

![Criteri univoci](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Visualizzare lo stato della configurazione delle VM

Dopo aver attivato la raccolta dei dati e impostato un criterio di sicurezza, il Centro sicurezza inizia a generare avvisi e raccomandazioni. Man mano che le macchine virtuali vengono distribuite, viene installato l'agente di raccolta dati. Il Centro sicurezza viene quindi popolato con i dati per le nuove macchine virtuali. Per informazioni dettagliate sullo stato di configurazione delle macchine virtuali, vedere [Protezione delle macchine virtuali nel Centro sicurezza di Azure](../../security-center/security-center-virtual-machine-recommendations.md). 

Man mano che i dati vengono raccolti, i dati relativi all'integrità delle risorse di ogni macchina virtuale e delle risorse di Azure correlate vengono aggregati. Le informazioni vengono mostrate in un grafico di facile lettura. 

Per visualizzare l'integrità risorse:

1.  Nel dashboard del Centro sicurezza, in **Integrità della sicurezza delle risorse** selezionare **Calcolo**. 
2.  Nel pannello **Calcolo** selezionare **Macchine virtuali**. Questa visualizzazione riepiloga lo stato della configurazione di tutte le macchine virtuali.

![Stato del calcolo](./media/tutorial-azure-security/compute-health.png)

Per visualizzare tutte le raccomandazioni per una macchina virtuale, selezionare la macchina virtuale. Le raccomandazioni e le correzioni sono descritte più dettagliatamente nella sezione successiva di questa esercitazione.

## <a name="remediate-configuration-issues"></a>Risolvere i problemi di configurazione

Quando il Centro sicurezza inizia a popolarsi con i dati di configurazione, le raccomandazioni vengono generate in base ai criteri di sicurezza configurati. Se ad esempio è stata configurata una macchina virtuale senza un gruppo di sicurezza di rete associato, viene generata una raccomandazione per crearne uno. 

Per visualizzare un elenco di tutte le raccomandazioni: 

1. Nel dashboard del Centro sicurezza selezionare **Raccomandazioni**.
2. Selezionare una raccomandazione specifica. Viene visualizzato un elenco di tutte le risorse per cui si applica la raccomandazione.
3. Per applicare una raccomandazione, selezionare una risorsa specifica. 
4. Seguire le istruzioni per la procedura di correzione. 

Il Centro sicurezza offre in molti casi le procedure pratiche per applicare una raccomandazione senza uscire dal Centro sicurezza. Nell'esempio seguente il Centro sicurezza rileva un gruppo di sicurezza di rete che dispone di una regola in entrata senza restrizioni. Nella pagina di raccomandazione è possibile selezionare il pulsante **Modifica le regole in ingresso**. Viene visualizzata l'interfaccia utente necessaria per modificare la regola. 

![Raccomandazioni](./media/tutorial-azure-security/remediation.png)

Le raccomandazioni risolte vengono contrassegnate come tali. 

## <a name="view-detected-threats"></a>Visualizzare le minacce rilevate

Oltre alle raccomandazioni per la configurazione delle risorse, il Centro sicurezza mostra avvisi di rilevamento di minacce. La funzionalità di avviso di sicurezza aggrega i dati raccolti da ogni macchina virtuale, dai log di rete di Azure e dalle soluzioni partner collegate per rilevare le minacce alla sicurezza per le risorse di Azure. Per informazioni dettagliate sulle funzionalità di rilevamento delle minacce nel Centro sicurezza, vedere [Funzionalità di rilevamento del Centro sicurezza di Azure](../../security-center/security-center-detection-capabilities.md).

Per accedere alla funzionalità degli avvisi di sicurezza, è necessario aumentare il piano tariffario del Centro sicurezza da *Gratuito* a *Standard*. Quando si passa a questo piano tariffario superiore, è disponibile una **versione di prova gratuita** di 30 giorni. 

Per modificare il piano tariffario:  

1. Nel dashboard del Centro sicurezza fare clic su **Criteri di sicurezza** e selezionare la sottoscrizione in uso.
2. Selezionare **Piano tariffario**.
3. Selezionare il nuovo piano e quindi **Seleziona**.
4. Nel pannello **Criteri di sicurezza** selezionare **Salva**. 

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

