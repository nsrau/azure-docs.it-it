---
title: 'Esercitazione: Configurare le distribuzioni canary per le macchine virtuali Linux di Azure'
description: Questa esercitazione illustra come configurare una pipeline di distribuzione continua (CD). Questa pipeline aggiorna un gruppo di macchine virtuali Linux di Azure usando la strategia di distribuzione blu-verde.
author: moala
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: f349ff62fe211f0610341864a4c7528ee6bfe9c5
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961528"
---
# <a name="tutorial---configure-the-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>Esercitazione: Configurare la strategia di distribuzione blu-verde per le macchine virtuali Linux di Azure

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infrastruttura distribuita come servizio (IaaS, Infrastructure as a Service) - Configurare CI/CD

Azure Pipelines offre un set completo di strumenti di automazione CI/CD per le distribuzioni in macchine virtuali. È possibile configurare una pipeline di distribuzione continua per una VM di Azure dal portale di Azure.

Questo articolo illustra come configurare una pipeline CI/CD basata sulla strategia blu-verde per le distribuzioni in più computer. Il portale di Azure supporta anche altre strategie, come le distribuzioni [in sequenza](./tutorial-devops-azure-pipelines-classic.md) e [canary](./tutorial-azure-devops-canary-strategy.md).

### <a name="configure-cicd-on-virtual-machines"></a>Configurare CI/CD nelle macchine virtuali

È possibile aggiungere macchine virtuali come destinazioni a un [gruppo di distribuzione](/azure/devops/pipelines/release/deployment-groups) e quindi configurare l'invio di aggiornamenti a più VM. Dopo la distribuzione nei computer, aprire la visualizzazione **Cronologia distribuzioni** all'interno di un gruppo di distribuzione. Questa visualizzazione consente di seguire la traccia dalla macchina virtuale alla pipeline e quindi al commit.

### <a name="blue-green-deployments"></a>Distribuzioni blu-verde

Una distribuzione blu-verde riduce il tempo di inattività grazie a un ambiente di standby identico. In qualsiasi momento è attivo un solo ambiente.

Mentre ci si prepara una nuova versione, si completa la fase finale di test nell'ambiente verde. Una volta che il software è in esecuzione nell'ambiente verde, commutare il traffico in modo che tutte le richieste in ingresso vengano indirizzate in tale ambiente. L'ambiente blu è ora inattivo.

Con l'opzione della distribuzione continua, è possibile configurare le distribuzioni blu-verde nelle macchine virtuali dal portale di Azure. Ecco la procedura dettagliata:

1. Accedere al portale di Azure e passare a una macchina virtuale.
1. Nel riquadro all'estrema sinistra delle impostazioni della VM, selezionare **Recapito continuo**. Selezionare quindi **Configura**.

   ![Riquadro recapito continuo con il pulsante Configura](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. Nel pannello di configurazione fare clic su **Organizzazione di Azure DevOps** per selezionare un account esistente o crearne uno. Selezionare quindi il progetto in cui configurare la pipeline.  

   ![Pannello Recapito continuo](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Un gruppo di distribuzione è un set logico di computer di destinazione che rappresentano gli ambienti fisici, ad esempio Dev, Test, UAT e Production. È possibile creare un nuovo gruppo di distribuzione o selezionarne uno esistente.
1. Selezionare la pipeline di compilazione che pubblica il pacchetto da distribuire nella macchina virtuale. Il pacchetto pubblicato deve includere uno script di distribuzione denominato deploy.ps1 o deploy.sh nella cartella deployscripts nella radice. La pipeline esegue questo script di distribuzione.
1. In **Deployment strategy** (Strategia di distribuzione) selezionare **Blue-Green** (Blu-verde).
1. Aggiungere il tag "blu" o "verde" alle VM da includere nelle distribuzioni blu-verde. Se la VM è destinata a un ruolo di standby, assegnarle il tag "verde". In caso contrario, contrassegnarla con il tag "blu".

   ![Pannello Recapito continuo, con la strategia di distribuzione blu-verde selezionata](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

1. Selezionare **OK** per configurare la pipeline di distribuzione continua per la macchina virtuale.

   ![Pipeline blu-verde](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

1. Vengono visualizzati i dettagli della distribuzione per la macchina virtuale. È possibile selezionare il collegamento per passare alla pipeline di versione in Azure DevOps. Nella pipeline di versione selezionare **Modifica** per visualizzare la configurazione della pipeline. La pipeline presenta queste tre fasi:

   1. Questa è una fase del gruppo di distribuzione. Le applicazioni vengono distribuite in VM di standby, contrassegnate con il tag "verde".
   1. In questa fase la pipeline è sospesa e attende l'intervento manuale per riprendere l'esecuzione. Gli utenti possono riprendere l'esecuzione della pipeline dopo aver verificato manualmente la stabilità della distribuzione nelle VM contrassegnate con il tag "verde".
   1. Questa fase scambia i tag "blu" e "verde" nelle VM. In questo modo si garantisce che le VM con versioni precedenti dell'applicazione ora siano contrassegnate con il tag "verde". Durante l'esecuzione successiva della pipeline, le applicazioni verranno distribuite a queste VM.

      ![Riquadro Gruppo di distribuzione per l'attività di distribuzione blu-verde](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

1. Per impostazione predefinita, l'attività Execute Deploy Script (Esegui script di distribuzione) esegue lo script di distribuzione deploy.ps1 o deploy.sh. Lo script si trova nella cartella deployscripts della cartella radice del pacchetto pubblicato. Assicurarsi che la pipeline di compilazione selezionata pubblichi la distribuzione nella cartella radice del pacchetto.

   ![Il riquadro Artefatti che mostra deploy.sh nella cartella deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Altre strategie di distribuzione

- [Configurare la strategia di distribuzione in sequenza](./tutorial-devops-azure-pipelines-classic.md)
- [Configurare la strategia di distribuzione canary](./tutorial-azure-devops-canary-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

È possibile iniziare a usare Azure facilmente. Con DevOps Projects bastano tre passaggi per iniziare a eseguire la propria applicazione in qualsiasi servizio di Azure selezionando:

- Un linguaggio dell'applicazione
- Un runtime
- Un servizio di Azure

[Altre informazioni](https://azure.microsoft.com/features/devops-projects/)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Eseguire la distribuzione in macchine virtuali di Azure con Azure DevOps Projects](../../devops-project/azure-devops-project-vms.md)
- [Implementare la distribuzione continua dell'app in un set di scalabilità di macchine virtuali di Azure](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)