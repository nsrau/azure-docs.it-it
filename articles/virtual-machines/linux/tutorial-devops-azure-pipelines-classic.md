---
title: 'Esercitazione: Configurare le distribuzioni in sequenza per le macchine virtuali Linux di Azure'
description: Questa esercitazione illustra come configurare una pipeline di recapito continuo (CD) che aggiorna in modo incrementale un gruppo di macchine virtuali Linux di Azure usando la strategia di distribuzione in sequenza
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
ms.openlocfilehash: 75888b1ebbda33891296fe0b54c5d204955e32a3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82113485"
---
# <a name="tutorial---configure-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>Esercitazione: Configurare la strategia di distribuzione in sequenza per le macchine virtuali Linux di Azure

Azure DevOps è un servizio integrato di Azure che automatizza ogni parte del processo di DevOps con l'integrazione continua e il recapito continuo per qualsiasi risorsa di Azure.
Indipendentemente dalle risorse usate dall'app (macchine virtuali, app Web, Kubernetes o altro), è possibile implementare funzionalità quali infrastruttura come codice, integrazione continua, test continuo, recapito continuo e monitoraggio continuo con Azure e Azure DevOps.  

![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 


## <a name="iaas---configure-cicd"></a>IaaS - Configurare CI/CD 
Azure Pipelines offre un set completo di strumenti di automazione CI/CD per le distribuzioni in macchine virtuali. È possibile configurare una pipeline di recapito continuo per una macchina virtuale di Azure direttamente dal portale di Azure. Questo documento contiene i passaggi associati alla configurazione di una pipeline CI/CD per eseguire distribuzioni in sequenza in più macchine virtuali tramite il portale di Azure. È anche possibile esaminare altre strategie, ad esempio [canary](https://aka.ms/AA7jdrz) e [blu-verde](https://aka.ms/AA83fwu), che sono supportate per impostazione predefinita dal portale di Azure. 


**Configurare CI/CD su macchine virtuali**

Le macchine virtuali possono essere aggiunte come destinazioni in un [gruppo di distribuzione](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) ed essere specificate come destinazione per un'operazione di aggiornamento di più computer. Dopo la distribuzione, la **cronologia di distribuzione** all'interno del gruppo di distribuzione fornisce la tracciabilità dalla macchina virtuale alla pipeline e quindi al commit. 
 

**Distribuzioni in sequenza**: una distribuzione in sequenza sostituisce le istanze della versione precedente di un'applicazione con le istanze della nuova versione dell'applicazione in un set fisso di macchine virtuali (set in sequenza) in ogni iterazione. Di seguito viene illustrato in dettaglio come configurare un aggiornamento in sequenza su macchine virtuali.  
È possibile configurare gli aggiornamenti in sequenza nelle proprie "**macchine virtuali**" all'interno del portale di Azure usando l'opzione di recapito continuo. 

Ecco la procedura dettagliata. 
1. Accedere al portale di Azure e passare a una macchina virtuale. 
2. Nel riquadro sinistro della VM passare al menu  **Recapito continuo** , quindi fare clic su  **Configura**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. Nel pannello di configurazione fare clic su "Organizzazione di Azure DevOps" per selezionare un account esistente o crearne uno. Selezionare quindi il progetto in cui si vuole configurare la pipeline.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Un gruppo di distribuzione è un set logico di computer di destinazione della distribuzione che rappresentano gli ambienti fisici, ad esempio "Dev", "Test", "UAT" e "Production". È possibile creare un nuovo gruppo di distribuzione o selezionarne uno esistente. 
5. Selezionare la pipeline di compilazione che pubblica il pacchetto da distribuire nella macchina virtuale. Si noti che il pacchetto pubblicato deve includere uno script di distribuzione _deploy.ps1_ o _deploy.sh_ nella cartella `deployscripts` nella radice del pacchetto. Questo script di distribuzione verrà eseguito dalla pipeline Azure DevOps in fase di esecuzione.
6. Selezionare la strategia di distribuzione desiderata. In questo caso selezionare "Rolling" (In sequenza).
7. Facoltativamente, è possibile assegnare al computer un tag relativo al ruolo, ad esempio "Web", "DB" e così via. Ciò consentirà di specificare come destinazione solo le VM con un determinato ruolo.
8. Fare clic su **OK** nella finestra di dialogo per configurare la pipeline di recapito continuo. 
9. Al termine, si avrà una pipeline di recapito continuo configurata per la distribuzione nella macchina virtuale.  


   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)
10. Si noterà che la distribuzione nella macchina virtuale è in corso. È possibile fare clic sul collegamento per passare alla pipeline. Fare clic su **Versione-1** per visualizzare la distribuzione. In alternativa, è possibile fare clic su **Modifica** per modificare la definizione della pipeline di versione. 
11. Se è necessario configurare più VM, ripetere i passaggi da 2 a 4 per le altre VM da aggiungere al gruppo di distribuzione. Si noti che se si seleziona un gruppo di distribuzione per cui esiste già un'esecuzione della pipeline, la VM verrà aggiunta al gruppo di distribuzione senza che vengano create nuove pipeline. 
12. Al termine, fare clic sulla definizione della pipeline, passare all'organizzazione di Azure DevOps e fare clic su **Modifica** per modificare la pipeline di versione. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)
13. Fare clic sul collegamento **1 processo, 1 attività** nella fase **sviluppo**. Fare clic sulla fase **Distribuzione**.
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)
14. Nel riquadro di configurazione sulla destra è possibile specificare il numero di computer da distribuire in parallelo in ogni iterazione. Se si vuole eseguire la distribuzione in più computer contemporaneamente, è possibile specificarlo in termini di percentuale usando il dispositivo di scorrimento.  

15. Per impostazione predefinita, l'attività Execute Deploy Script (Esegui script di distribuzione) eseguirà lo script di distribuzione _deploy.ps1_ o _deploy.sh_ che si trova nella cartella 'deployscripts' nella directory radice del pacchetto pubblicato.  
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Altre strategie di distribuzione

- [Configurare la strategia di distribuzione canary](https://aka.ms/AA7jdrz)
- [Configurare la strategia di distribuzione blu-verde](https://aka.ms/AA83fwu)

 
## <a name="azure-devops-project"></a>Progetto DevOps di Azure 
Imparare a usare Azure in modo efficace è più facile che mai.
 
Con DevOps Projects bastano tre passaggi per iniziare a eseguire la propria applicazione in qualsiasi servizio di Azure: selezionare un linguaggio dell'applicazione, un runtime e un servizio di Azure.
 
[Altre informazioni](https://azure.microsoft.com/features/devops-projects/ )
 
## <a name="additional-resources"></a>Risorse aggiuntive 
- [Distribuire un'app ASP.NET in Macchine virtuali di Azure usando Azure DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementare la distribuzione continua dell'app in un set di scalabilità di macchine virtuali di Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
