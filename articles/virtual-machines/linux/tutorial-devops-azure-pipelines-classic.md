---
title: 'Esercitazione: DevOps integrato per IaaS e PaaS in Azure'
description: Questa esercitazione illustra come configurare l'integrazione continua (CI) e la distribuzione continua (CD) di un'app in VM di Azure usando le pipeline di Azure.
author: ushan
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/16/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: 4159bf27c39087926d982552c49c606f3484de77
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885906"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>Esercitazione: DevOps integrato per IaaS e PaaS in Azure

Le soluzioni end-to-end di Azure permettono ai team di implementare procedure consigliate della metodologia DevOps in ogni fase del ciclo di vita delle applicazioni: pianificazione, sviluppo, distribuzione e utilizzo. 

Di seguito sono illustrati alcuni dei servizi di Azure che semplificano i carichi di lavoro cloud e possono essere combinati per consentire scenari incredibilmente efficaci.
Queste tecnologie, nelle mani delle persone e rafforzate dai processi, consentono ai team di offrire un valore costante ai clienti. 

- Azure: https://portal.azure.com - Portale per la creazione di carichi di lavoro cloud. Consente di gestire e monitorare praticamente tutto, da semplici app Web fino a complesse applicazioni cloud. 
- Azure DevOps: https://dev.azure.com - Offre un insieme di servizi di sviluppo moderni per pianificare in modo più intelligente, migliorare la collaborazione e velocizzare la distribuzione. 
- Azure Machine Learning Studio: https://ml.azure.com - Consente di preparare i dati e di eseguire il training e distribuire i modelli di Machine Learning. 
 

Azure DevOps è un servizio integrato di Azure che automatizza ogni parte del processo di DevOps con l'integrazione continua e il recapito continuo per qualsiasi risorsa di Azure.
Indipendentemente dalle risorse usate dall'app (macchine virtuali, app Web, Kubernetes o altro), è possibile implementare funzionalità quali infrastruttura come codice, integrazione continua, test continuo, recapito continuo e monitoraggio continuo con Azure e Azure DevOps.  
![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 
 
 
## <a name="iaas---configure-cicd"></a>IaaS - Configurare CI/CD 
Azure Pipelines offre un set completo di strumenti di automazione CI/CD per le distribuzioni in macchine virtuali. È possibile configurare una pipeline di recapito continuo per una macchina virtuale di Azure direttamente dal portale di Azure. Questo documento contiene i passaggi associati alla configurazione di una pipeline CI/CD per eseguire distribuzioni in più macchine virtuali tramite il portale di Azure. 


**Configurare CI/CD su macchine virtuali**

Le macchine virtuali possono essere aggiunte come destinazioni in un [gruppo di distribuzione](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) ed essere specificate come destinazione per un'operazione di aggiornamento di più computer. In base ai requisiti, è possibile scegliere una qualsiasi delle strategie di distribuzione predefinite, _Rolling_ (In sequenza), _Canary_ o _Blue-Green_ (Blu-verde), oppure personalizzarle ulteriormente. Dopo la distribuzione, le visualizzazioni della cronologia di distribuzione all'interno dei gruppi di distribuzione offrono la tracciabilità dalla VM alla pipeline e quindi al commit. 
 
**Distribuzioni in sequenza**: una distribuzione in sequenza sostituisce le istanze della versione precedente di un'applicazione con le istanze della nuova versione dell'applicazione in un set fisso di macchine virtuali (set in sequenza) in ogni iterazione. Di seguito viene illustrato in dettaglio come configurare un aggiornamento in sequenza su macchine virtuali.  
È possibile configurare gli aggiornamenti in sequenza nelle proprie "**macchine virtuali**" all'interno del portale di Azure usando l'opzione di recapito continuo. 

Ecco la procedura dettagliata. 
1. Accedere al portale di Azure e passare a una macchina virtuale. 
2. Nel riquadro sinistro della VM passare al menu  **Recapito continuo** , quindi fare clic su  **Configura**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. Nel pannello di configurazione fare clic su "Organizzazione di Azure DevOps" per selezionare un account esistente o crearne uno. Selezionare quindi il progetto in cui si vuole configurare la pipeline.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Un gruppo di distribuzione è un set logico di computer di destinazione della distribuzione che rappresentano gli ambienti fisici, ad esempio "Dev", "Test", "UAT" e "Production". È possibile creare un nuovo gruppo di distribuzione o selezionarne uno esistente. 
5. Selezionare la pipeline di compilazione che pubblica il pacchetto da distribuire nella macchina virtuale. Si noti che il pacchetto pubblicato deve includere uno script di distribuzione _deploy.ps1_ o _deploy.sh_ nella cartella _deployscripts_ nella radice del pacchetto. Questo script di distribuzione verrà eseguito dalla pipeline Azure DevOps in fase di esecuzione.
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

15. Per impostazione predefinita, l'attività Execute Deploy Script (Esegui script di distribuzione) eseguirà lo script di distribuzione _deploy.ps1_ o _deploy.sh_ che si trova nella cartella _deployscripts_ nella directory radice del pacchetto pubblicato.
  
**Distribuzioni canary**: una distribuzione canary riduce il rischio con una lenta implementazione delle modifiche in un sottoinsieme limitato di utenti. Man mano che si acquisisce maggiore familiarità con la nuova versione, è possibile iniziare a rilasciarla a più server dell'infrastruttura e indirizzarvi più utenti. È possibile configurare le distribuzioni canary nelle proprie "**macchine virtuali**" con il portale di Azure usando l'opzione di recapito continuo. Ecco la procedura dettagliata. 
1. Accedere al portale di Azure e passare a una macchina virtuale 
2. Seguire i passaggi da 2 a 7 descritti nella sezione **Distribuzioni in sequenza** per aggiungere più VM al gruppo di distribuzione. Nell'elenco a discesa per la strategia di distribuzione selezionare "Canary".
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

3. Aggiungere il tag "canary" alle VM da includere nelle distribuzioni canary e il tag "prod" alle VM incluse nelle distribuzioni dopo il completamento della distribuzione canary.
4. Al termine, si avrà una pipeline di recapito continuo configurata per la distribuzione nella macchina virtuale.
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


5. Come descritto nella sezione **Distribuzioni in sequenza**, è possibile fare clic su **Modifica** per la pipeline di versione in Azure DevOps per visualizzare la configurazione della pipeline. La pipeline è costituita da 3 fasi: la prima riguarda il gruppo di distribuzione ed esegue la distribuzione nelle VM con tag _canary_. La seconda fase sospende la pipeline e attende l'intervento manuale per riprendere l'esecuzione. Quando l'utente è soddisfatto della stabilità della distribuzione canary, può riprendere l'esecuzione della pipeline. Verrà quindi eseguita la terza fase che effettua la distribuzione nelle VM con tag _prod_. ![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)



**Distribuzioni di tipo blu-verde**: una distribuzione di tipo blu-verde riduce il tempo di inattività con un ambiente di standby identico. Uno degli ambienti è attivo in qualsiasi momento. Mentre si prepara una nuova versione, si esegue la fase finale di test nell'ambiente verde. Una volta che il software è in esecuzione nell'ambiente verde, commutare il traffico in modo che tutte le richieste in ingresso vengano indirizzate all'ambiente verde. L'ambiente blu è ora inattivo.
È possibile configurare le distribuzioni di tipo blu-verde nelle proprie "**macchine virtuali**" tramite il portale di Azure usando l'opzione di recapito continuo. 

Ecco la procedura dettagliata.

1. Accedere al portale di Azure e passare a una macchina virtuale 
2. Seguire i passaggi da 2 a 7 descritti nella sezione **Distribuzioni in sequenza** per aggiungere più VM al gruppo di distribuzione. Aggiungere il tag "blu" o "verde" alle VM da includere nelle distribuzioni di tipo blu-verde. Se la VM è destinata a un ruolo di standby, è necessario assegnarle il tag "verde".
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

4. Al termine, si avrà una pipeline di recapito continuo configurata per la distribuzione nella macchina virtuale.
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

5. Come illustrato nella sezione **Distribuzioni in sequenza**, è possibile fare clic su **Modifica** per la pipeline di versione in Azure DevOps per visualizzare la configurazione della pipeline. La pipeline è costituita da 3 fasi: la prima riguarda il gruppo di distribuzione ed esegue la distribuzione nelle VM con tag _verde_ (VM di standby). La seconda fase sospende la pipeline e attende l'intervento manuale per riprendere l'esecuzione. Quando l'utente è soddisfatto della stabilità della distribuzione, può reindirizzare il traffico alle VM con tag _verde_ e riprendere l'esecuzione della pipeline, che scambierà quindi i tag _blu_ e _verde_ nelle VM. In questo modo, le VM con la versione meno recente dell'applicazione saranno contrassegnate con il tag _verde_ e alla successiva esecuzione della pipeline verrà eseguita la distribuzione in tali VM.
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

6. Si noti che per questa strategia di distribuzione è necessario che siano presenti almeno una VM con tag "blu" e una con tag "verde". Prima di riprendere l'esecuzione della pipeline nel passaggio dell'intervento manuale verificare che sia presente almeno una VM con tag _blu_.





 
## <a name="azure-devops-project"></a>Progetto DevOps di Azure 
Imparare a usare Azure in modo efficace è più facile che mai.
 
Con DevOps Projects bastano tre passaggi per iniziare a eseguire la propria applicazione in qualsiasi servizio di Azure: selezionare un linguaggio dell'applicazione, un runtime e un servizio di Azure.
 
[Altre informazioni](https://azure.microsoft.com/features/devops-projects/ )
 
## <a name="additional-resources"></a>Risorse aggiuntive 
- [Distribuire un'app ASP.NET in Macchine virtuali di Azure usando Azure DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementare la distribuzione continua dell'app in un set di scalabilità di macchine virtuali di Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
