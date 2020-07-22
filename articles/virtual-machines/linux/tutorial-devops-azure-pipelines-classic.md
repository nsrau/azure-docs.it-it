---
title: 'Esercitazione: Configurare le distribuzioni in sequenza per le macchine virtuali Linux di Azure'
description: Questa esercitazione illustra come configurare una pipeline di distribuzione continua (CD). Questa pipeline aggiorna in modo incrementale un gruppo di macchine virtuali Linux di Azure usando la strategia di distribuzione in sequenza.
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
ms.openlocfilehash: b8eec0d95144c012d3034e3f2c7c6e8adc921651
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86510175"
---
# <a name="tutorial---configure-the-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>Esercitazione: Configurare la strategia di distribuzione in sequenza per le macchine virtuali Linux di Azure

Azure DevOps è un servizio integrato di Azure che automatizza ogni parte del processo di DevOps per qualsiasi risorsa di Azure. Indipendentemente dalle risorse usate dall'app (macchine virtuali, app Web, Kubernetes o altro), è possibile implementare funzionalità come infrastruttura distribuita codice (IaaC), integrazione continua, test continui, distribuzione continua e monitoraggio continuo con Azure e Azure DevOps.

![Il portale di Azure con l'opzione Azure DevOps selezionata in Servizi](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png)

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infrastruttura distribuita come servizio (IaaS, Infrastructure as a Service) - Configurare CI/CD

Azure Pipelines offre un set completo di strumenti di automazione CI/CD per le distribuzioni in macchine virtuali. È possibile configurare una pipeline di distribuzione continua per una VM di Azure dal portale di Azure.

Questo articolo illustra come configurare una pipeline CI/CD per le distribuzioni in sequenza in più computer dal portale di Azure. Il portale di Azure supporta anche altre strategie, come le distribuzioni [canary](https://aka.ms/AA7jdrz) e [blu-verde](https://aka.ms/AA83fwu).

### <a name="configure-cicd-on-virtual-machines"></a>Configurare CI/CD nelle macchine virtuali

È possibile aggiungere macchine virtuali come destinazioni a un [gruppo di distribuzione](/azure/devops/pipelines/release/deployment-groups) e quindi configurare l'invio di aggiornamenti a più VM. Dopo la distribuzione nei computer, aprire la visualizzazione **Cronologia distribuzioni** all'interno di un gruppo di distribuzione. Questa visualizzazione consente di seguire la traccia dalla macchina virtuale alla pipeline e quindi al commit.

### <a name="rolling-deployments"></a>Distribuzioni in sequenza

In ogni iterazione una distribuzione in sequenza sostituisce le istanze della versione precedente di un'applicazione. Le sostituisce con istanze della nuova versione in un set fisso di computer (set in sequenza). La procedura dettagliata seguente illustra come configurare un aggiornamento in sequenza per le macchine virtuali.

Con l'opzione della distribuzione continua, è possibile configurare aggiornamenti in sequenza per le macchine virtuali all'interno del portale di Azure. Ecco la procedura dettagliata:

1. Accedere al portale di Azure e passare a una macchina virtuale.
1. Nel riquadro all'estrema sinistra delle impostazioni della VM, selezionare **Recapito continuo**. Selezionare quindi **Configura**.

   ![Riquadro recapito continuo con il pulsante Configura](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. Nel pannello di configurazione fare clic su **Organizzazione di Azure DevOps** per selezionare un account esistente o crearne uno. Selezionare quindi il progetto in cui configurare la pipeline.  

   ![Pannello Recapito continuo](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Un gruppo di distribuzione è un set logico di computer di destinazione che rappresentano gli ambienti fisici, ad esempio Dev, Test, UAT e Production. È possibile creare un nuovo gruppo di distribuzione o selezionarne uno esistente.
1. Selezionare la pipeline di compilazione che pubblica il pacchetto da distribuire nella macchina virtuale. Il pacchetto pubblicato deve includere uno script di distribuzione denominato deploy.ps1 o deploy.sh nella cartella deployscripts nella radice. La pipeline esegue questo script di distribuzione.
1. In **Deployment strategy** (Strategia di distribuzione) selezionare **Rolling** (In sequenza).
1. Facoltativamente, è possibile assegnare a ogni computer un tag relativo al ruolo, ad esempio "web" e "db". Questi tag consentono di indicare come destinazione solo le macchine virtuali con un ruolo specifico.
1. Selezionare **OK** per configurare la pipeline di distribuzione continua.
1. Al termine della configurazione, è disponibile una pipeline di distribuzione continua per la macchina virtuale.  

   ![Pannello Recapito continuo che mostra la cronologia delle distribuzioni](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)

1. Vengono visualizzati i dettagli della distribuzione per la macchina virtuale. È possibile selezionare il collegamento per passare alla pipeline, **Release-1** per visualizzare la distribuzione oppure **Modifica** per modificare la definizione della pipeline di versione.

1. Se si configurano più VM, ripetere i passaggi da 2 a 4 per aggiungere altre macchine virtuali al gruppo di distribuzione. Se si seleziona un gruppo di distribuzione in cui è già in esecuzione una pipeline, le VM vengono aggiunte a questo. Non vengono create nuove pipeline.
1. Al termine della configurazione, selezionare la definizione della pipeline, passare all'organizzazione di Azure DevOps e selezionare **Modifica** per modificare la pipeline di versione.

   ![Modifica della pipeline in sequenza](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)

1. Selezionare **1 processo, 1 attività** nella fase **Dev**. Selezionare la fase **Distribuisci**.

   ![Attività della pipeline in sequenza con l'attività di distribuzione selezionata](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)

1. Nel riquadro di configurazione all'estrema destra è possibile specificare il numero di computer da distribuire in parallelo in ogni iterazione. Se si vuole eseguire la distribuzione in più computer contemporaneamente, è possibile specificarne il numero come percentuale usando il dispositivo di scorrimento.  

1. Per impostazione predefinita, l'attività Execute Deploy Script (Esegui script di distribuzione) esegue lo script di distribuzione deploy.ps1 o deploy.sh. Lo script si trova nella cartella deployscripts della cartella radice del pacchetto pubblicato.

   ![Il riquadro Artefatti che mostra deploy.sh nella cartella deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Altre strategie di distribuzione

- [Configurare la strategia di distribuzione canary](https://aka.ms/AA7jdrz)
- [Configurare la strategia di distribuzione blu-verde](https://aka.ms/AA83fwu)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

È possibile iniziare a usare Azure facilmente. Con DevOps Projects bastano tre passaggi per iniziare a eseguire la propria applicazione in qualsiasi servizio di Azure selezionando:

- Un linguaggio dell'applicazione
- Un runtime
- Un servizio di Azure
 
[Altre informazioni](https://azure.microsoft.com/features/devops-projects/)
 
## <a name="additional-resources"></a>Risorse aggiuntive

- [Eseguire la distribuzione in macchine virtuali di Azure con Azure DevOps Projects](../../devops-project/azure-devops-project-vms.md)
- [Implementare la distribuzione continua dell'app in un set di scalabilità di macchine virtuali di Azure](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
