---
title: 'Esercitazione: Configurare le distribuzioni canary per le macchine virtuali Linux di Azure'
description: Questa esercitazione illustra come configurare una pipeline di distribuzione continua (CD). Questa pipeline aggiorna un gruppo di macchine virtuali Linux di Azure usando la strategia di distribuzione canary.
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
ms.openlocfilehash: e0fb26896b79fb23bb0f784c0f23aa3af0593c22
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871864"
---
# <a name="tutorial---configure-the-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Esercitazione: Configurare la strategia di distribuzione canary per le macchine virtuali Linux di Azure

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infrastruttura distribuita come servizio (IaaS, Infrastructure as a Service) - Configurare CI/CD

Azure Pipelines offre un set completo di strumenti di automazione CI/CD per le distribuzioni in macchine virtuali. È possibile configurare una pipeline di distribuzione continua per una VM di Azure dal portale di Azure.

Questo articolo illustra come configurare una pipeline CI/CD basata sulla strategia canary per le distribuzioni in più computer. Il portale di Azure supporta anche altre strategie, come le distribuzioni [in sequenza](https://aka.ms/AA7jlh8) e [blu-verde](https://aka.ms/AA83fwu).

### <a name="configure-cicd-on-virtual-machines"></a>Configurare CI/CD nelle macchine virtuali

È possibile aggiungere macchine virtuali come destinazioni a un [gruppo di distribuzione](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) e quindi configurare l'invio di aggiornamenti a più VM. Dopo la distribuzione nei computer, aprire la visualizzazione **Cronologia distribuzioni** all'interno di un gruppo di distribuzione. Questa visualizzazione consente di seguire la traccia dalla macchina virtuale alla pipeline e quindi al commit.

### <a name="canary-deployments"></a>Distribuzioni canary

Una distribuzione canary riduce il rischio con una lenta implementazione delle modifiche in un sottoinsieme limitato di utenti. Man mano che si acquisisce maggiore familiarità con la nuova versione, è possibile rilasciarla a più server dell'infrastruttura e indirizzarvi più utenti.

Con l'opzione della distribuzione continua, è possibile configurare le distribuzioni canary nelle macchine virtuali dal portale di Azure. Ecco la procedura dettagliata:

1. Accedere al portale di Azure e passare a una macchina virtuale.
1. Nel riquadro all'estrema sinistra delle impostazioni della VM, selezionare **Recapito continuo**. Selezionare quindi **Configura**.

   ![Riquadro recapito continuo con il pulsante Configura](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. Nel pannello di configurazione fare clic su **Organizzazione di Azure DevOps** per selezionare un account esistente o crearne uno. Selezionare quindi il progetto in cui configurare la pipeline.  

   ![Pannello Recapito continuo](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Un gruppo di distribuzione è un set logico di computer di destinazione che rappresentano gli ambienti fisici, ad esempio Dev, Test, UAT e Production. È possibile creare un nuovo gruppo di distribuzione o selezionarne uno esistente.
1. Selezionare la pipeline di compilazione che pubblica il pacchetto da distribuire nella macchina virtuale. Il pacchetto pubblicato deve includere uno script di distribuzione denominato deploy.ps1 o deploy.sh nella cartella deployscripts nella radice. La pipeline esegue questo script di distribuzione.
1. In **Deployment strategy** (Strategia di distribuzione) selezionare **Canary**.
1. Aggiungere un tag "canary" alle VM che devono far parte delle distribuzioni canary. Aggiungere un tag "prod" alle VM che fanno parte delle distribuzioni effettuate dopo il completamento di quella canary. I tag consentono di indicare come destinazione solo le macchine virtuali con un ruolo specifico.

   ![Pannello Recapito continuo, con la strategia di distribuzione canary selezionata](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

1. Selezionare **OK** per configurare la pipeline di distribuzione continua per la macchina virtuale.

   ![Pipeline canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)

1. Vengono visualizzati i dettagli della distribuzione per la macchina virtuale. È possibile selezionare il collegamento per passare alla pipeline di versione in Azure DevOps. Nella pipeline di versione selezionare **Modifica** per visualizzare la configurazione della pipeline. La pipeline presenta queste tre fasi:

   1. Questa è una fase del gruppo di distribuzione. Le applicazioni vengono distribuite alle VM contrassegnate con il tag "canary".
   1. In questa fase la pipeline è sospesa e attende l'intervento manuale per riprendere l'esecuzione.
   1. Anche questa è una fase di gruppo di distribuzione. L'aggiornamento è stato distribuito alle VM contrassegnate dal tag "prod".

      ![Riquadro Gruppo di distribuzione per l'attività di distribuzione canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

1. Prima di riprendere l'esecuzione della pipeline, verificare che almeno una macchina virtuale sia contrassegnata come "prod". Nella terza fase della pipeline, le applicazioni vengono distribuite solo alle VM con il tag "prod".

1. Per impostazione predefinita, l'attività Execute Deploy Script (Esegui script di distribuzione) esegue lo script di distribuzione deploy.ps1 o deploy.sh. Lo script si trova nella cartella deployscripts della cartella radice del pacchetto pubblicato. Assicurarsi che la pipeline di compilazione selezionata pubblichi la distribuzione nella cartella radice del pacchetto.

   ![Il riquadro Artefatti che mostra deploy.sh nella cartella deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Altre strategie di distribuzione
- [Configurare la strategia di distribuzione in sequenza](https://aka.ms/AA7jlh8)
- [Configurare la strategia di distribuzione blu-verde](https://aka.ms/AA83fwu)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

È possibile iniziare a usare Azure facilmente. Con DevOps Projects bastano tre passaggi per iniziare a eseguire la propria applicazione in qualsiasi servizio di Azure selezionando:

- Un linguaggio dell'applicazione
- Un runtime
- Un servizio di Azure

[Altre informazioni](https://azure.microsoft.com/features/devops-projects/)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Eseguire la distribuzione in macchine virtuali di Azure con Azure DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementare la distribuzione continua dell'app in un set di scalabilità di macchine virtuali di Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
