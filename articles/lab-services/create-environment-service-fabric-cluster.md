---
title: Creare un ambiente con un cluster di Service Fabric in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come creare un ambiente con un cluster autonomo di Service Fabric e avviare e arrestare il cluster usando le pianificazioni.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: EMaher
manager: femila
editor: spelluru
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: enewman
ms.openlocfilehash: 9848f197800c391285c4065685b910685f0ac64b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60312190"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Creare un ambiente con cluster autonomo di Service Fabric in Azure DevTest Labs
Questo articolo fornisce informazioni su come creare un ambiente con un cluster autonomo di Service Fabric in Azure DevTest Labs. 

## <a name="overview"></a>Panoramica
DevTest Labs possono creare ambienti di test self-contained in base a modelli di Azure Resource Manager. Questi ambienti contengono entrambe le risorse IaaS, ad esempio le macchine virtuali e risorse PaaS, ad esempio Service Fabric. DevTest Labs consente di gestire le macchine virtuali in un ambiente, fornendo i comandi per controllare le macchine virtuali. Questi comandi consentono di avviare o arrestare una macchina virtuale in una pianificazione. Analogamente, DevTest Labs può anche consentono di gestire i cluster di Service Fabric in un ambiente. È possibile avviare o arrestare un cluster di Service Fabric in un ambiente manualmente o tramite una pianificazione.

## <a name="create-a-service-fabric-cluster"></a>Creare un cluster di Service Fabric
Cluster di Service Fabric vengono creati utilizzando gli ambienti in DevTest Labs. Ogni ambiente è definito da un modello di Azure Resource Manager in un repository Git. Il [pubblica repository Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) per DevTest Labs contiene il modello di Resource Manager per creare un cluster di Service Fabric nel [Cluster ServiceFabric](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) cartella. 

1. In primo luogo, creare un lab in Azure DevTest Labs usando le istruzioni nell'articolo seguente: [Creare un lab](devtest-lab-create-lab.md). Si noti che il **ambienti pubblici** consiste **su** per impostazione predefinita. 
2. Verificare che il provider di Service Fabric è registrato per la sottoscrizione seguendo questa procedura:
    1. Selezionare **abbonamenti** nel menu di spostamento a sinistra e selezionare il **sottoscrizione**
    2. Nel **sottoscrizione** pagina, selezionare **provider di risorse** nel **impostazioni** sezione nel menu a sinistra. 
    3. Se **Microsoft.ServiecFabric** non è registrato, selezionare **registrare**. 
3. Nella pagina **DevTest Labs** selezionare **+ Aggiungi** sulla barra degli strumenti. 
    
    ![Pulsante Aggiungi nella barra degli strumenti](./media/create-environment-service-fabric-cluster/add-button.png)
3. Nel **Scegli una base** pagina, selezionare **Cluster di Service Fabric Lab** nell'elenco. 

    ![Selezionare il Cluster di Lab di Service Fabric nell'elenco](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Nel **configurare le impostazioni** pagina, effettuare i passaggi seguenti: 
    1. Specificare una **name** per il cluster **ambiente**. Si tratta del nome del gruppo di risorse in Azure in cui verrà il cluster di Service Fabric da creare. 
    2. Selezionare il **sistema operativo (OS)** per macchine virtuali del cluster. Il valore predefinito è: **Windows**.
    3. Specificare un nome per il **amministratore** per il cluster. 
    4. Specificare una **password** per l'amministratore. 
    5. Per il **certificato**, immettere le informazioni del certificato sotto forma di stringa con codificata Base64. Per creare un certificato, procedere come segue:
        1. Scaricare il **Create ClusterCertificate.ps1** del file dalle [repository Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). In alternativa, clonare il repository nel computer. 
        2. Avviare **PowerShell**. 
        3. Eseguire la **ps1** del file usando il comando `.\Create-ClusterCertificate.ps1`. Viene visualizzato un file di testo aperto in blocco note con le informazioni che necessarie per compilare i campi relativi ai certificati in questa pagina. . 
    6. Immettere il **password per il certificato**.
    7. Specificare il **identificazione personale** per il certificato.
    8. Selezionare **Add** nel **configurare le impostazioni** pagina. 

        ![Configurare le impostazioni del cluster](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Dopo la creazione del cluster, viene visualizzato un gruppo di risorse con il nome dell'ambiente specificato nel passaggio precedente. Quando si espande, viene visualizzato il cluster di Service Fabric in esso. Se lo stato del gruppo di risorse è bloccato allo **Creating**, selezionare **aggiornare** sulla barra degli strumenti. Il **cluster di Service Fabric** environment crea un cluster di 5 nodi. 1-nodetype in Linux o Windows.

    Nell'esempio riportato di seguito **mysfabricclusterrg** è il nome del gruppo di risorse creato in modo specifico per il cluster di Service Fabric. È importante notare che sono completamente indipendenti all'interno del gruppo di risorse in cui vengono creati gli ambienti lab. Indica che il modello che definisce l'ambiente, che può accedere solo alle risorse all'interno del gruppo di risorse appena creato oppure [reti virtuali configurate per essere utilizzato da lab](devtest-lab-configure-vnet.md). In questo esempio precedente crea tutte le risorse necessarie nel gruppo di risorse stesso.

    ![Cluster creato](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Avviare o arrestare il cluster
È possibile avviare o arrestare il cluster da una pagina DevTest Labs stessa o dalla pagina di Cluster di Service Fabric fornito da DevTest Labs. 

### <a name="from-the-devtest-lab-page"></a>Dalla pagina di DevTest Labs
È possibile avviare o arrestare il cluster nella pagina di DevTest Labs per il lab. 

1. Selezionare **sui tre puntini di sospensione (...)**  per il cluster di Service Fabric, come illustrato nell'immagine seguente: 

    ![Avviare e arrestare i comandi per il cluster](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. I due comandi nel menu di scelta rapida visualizzati **avviare** e **arrestare** il cluster. Il comando start avvia tutti i nodi in un cluster. Il comando stop Arresta tutti i nodi del cluster. Una volta arrestato un cluster, il cluster di Service Fabric stesso rimane in uno stato pronto, ma nessun nodo è disponibile fino a quando il comando di avvio viene riemesso nel cluster in lab.

    Esistono alcuni aspetti da considerare quando si usa un cluster di Service Fabric in un ambiente di test. Potrebbe richiedere alcuni minuti per il cluster di Service Fabric reidratare completamente dopo che sono ancora stati riavviati i nodi. Per conservare i dati dall'arresto dell'avvio, è necessario salvare i dati in un disco gestito collegato alla macchina virtuale. Esistono implicazioni sulle prestazioni quando si usa un disco gestito collegato, pertanto è consigliabile solo negli ambienti di test. Se non viene eseguito un disco usato per archiviare i dati, quindi dati vengono persi quando il comando di arresto viene eseguito nel cluster.

### <a name="from-the-service-fabric-cluster-page"></a>Dalla pagina di Cluster di Service Fabric 
È presente un altro modo per avviare o arrestare il cluster. 

1. Selezionare il cluster di Service Fabric nella visualizzazione albero nella pagina di DevTest Labs. 

    ![Selezionare il cluster](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. Nel **Cluster di Service Fabric** pagina per il cluster, vedere comandi sulla barra degli strumenti per avviare o arrestare il cluster. 

    ![Avviare o arrestare i comandi nella pagina del Cluster di Service Fabric](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Configurazione di avvio automatico e pianificazione di arresto automatico
Anche cluster di Service Fabric possono essere avviati o arrestati in base a una pianificazione. Questa esperienza è simile all'esperienza per le macchine virtuali in un lab. Per risparmiare denaro, per impostazione predefinita, ogni cluster creato automaticamente in un lab si arresta al momento definito dal lab [criteri di arresto](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown). È possibile eseguire l'override specificando se il cluster deve essere arrestato o specificando l'ora che il cluster è stato arrestato. 

![Pianificazioni esistenti per l'avvio automatico e di arresto automatico](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Fornire il consenso esplicito per la pianificazione di avvio automatico
Per fornire il consenso esplicito per la pianificazione di avvio, procedere come segue:

1. Selezionare **avvio automatico** nel menu a sinistra
2. Selezionare **sul** per **consentono questo cluster di service fabric di essere pianificati per l'avvio automatico**. Questa pagina è abilitata solo se il proprietario del lab ha consentito agli utenti di avviare automaticamente le macchine virtuali o cluster di Service Fabric.
3. Sulla barra degli strumenti selezionare **Salva**. 

    ![Pagina star automatiche](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Configurare le impostazioni di arresto automatico 
Per modificare le impostazioni per l'arresto, procedere come segue:

1. Selezionare **arresto automatico** nel menu a sinistra. 
2. In questa pagina, è possibile rifiutare esplicitamente l'arresto automatico selezionando **disattivata** per **abilitato**. 
3. Se si è scelto **sul** per **abilitato**, seguire questa procedura:
    1. Specificare il **ora** dell'arresto.
    2. Specificare il **timezone** per il periodo di tempo. 
    3. Specificare se si desidera DevTest Labs per inviare **notifiche** prima dell'arresto automatico. 
    4. Se è stato selezionato **Yes** per l'opzione di notifica, specificare il **URL del Webhook** e/o **indirizzo di posta elettronica** per inviare notifiche. 
    5. Sulla barra degli strumenti selezionare **Salva**.

        ![Arresto di pagina automatica](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Per visualizzare i nodi del cluster di Service Fabric
Pagina del cluster di Service Fabric che è stato illustrato in precedenza nei passaggi è specifica per la pagina di DevTest Labs. Non mostra i nodi del cluster. Per altre informazioni sul cluster, seguire questa procedura:

1. Nel **DevTest Labs** pagina per l'ambiente lab, seleziona la **gruppo di risorse** nella visualizzazione albero nel **macchine virtuali personali** sezione.

    ![Selezionare il gruppo di risorse](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Nel **gruppo di risorse** pagina, noterete che tutte le risorse nel gruppo di risorse in un elenco. Selezionare i **cluster di Service Fabric** dall'elenco. 

    ![Selezionare il cluster nell'elenco](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Viene visualizzato il **Cluster di Service Fabric** pagina per il cluster. Questa è la pagina forniti da Service Fabric. Vedere tutte le informazioni sui cluster, ad esempio nodi, i tipi di nodo e così via.

    ![Pagina iniziale di Cluster di Service Fabric](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti per informazioni dettagliate sugli ambienti: 

- [Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurare e usare gli ambienti pubblici in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
