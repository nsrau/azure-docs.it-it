---
title: Creare un ambiente cluster di Service Fabric in Azure DevTest LabsCreate a Service Fabric cluster environment in Azure DevTest Labs
description: Informazioni su come creare un ambiente con un cluster Service Fabric indipendente e avviare e arrestare il cluster usando le pianificazioni.
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
ms.date: 01/16/2020
ms.author: enewman
ms.openlocfilehash: 71793b81d8735c80881fc25a9b7ec31bc4fc6762
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170333"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Creare un ambiente con cluster di Service Fabric indipendente in Azure DevTest LabsCreate an environment with self-contained Service Fabric cluster in Azure DevTest Labs
Questo articolo fornisce informazioni su come creare un ambiente con un cluster di Service Fabric indipendente in Azure DevTest Labs.This article provides information on how to create an environment with a self-contained Service Fabric cluster in Azure DevTest Labs. 

## <a name="overview"></a>Panoramica
DevTest Labs può creare ambienti di test autonomi come definito dai modelli di Gestione risorse di Azure.DevTest Labs can create self-contained test environments as defined by Azure Resource Management templates. Questi ambienti contengono risorse IaaS, ad esempio macchine virtuali, sia risorse PaaS, ad esempio Service Fabric.These environments contain both IaaS resources, like virtual machines, and PaaS resources, like Service Fabric. DevTest Labs consente di gestire le macchine virtuali in un ambiente fornendo comandi per controllare le macchine virtuali. Questi comandi consentono di avviare o arrestare una macchina virtuale in base a una pianificazione. Analogamente, DevTest Labs consente anche di gestire i cluster di Service Fabric in un ambiente. È possibile avviare o arrestare un cluster di Service Fabric in un ambiente manualmente o tramite una pianificazione.

## <a name="create-a-service-fabric-cluster"></a>Creare un cluster di Service Fabric
Service Fabric clusters are created using environments in DevTest Labs. Ogni ambiente è definito da un modello di Azure Resource Manager in un repository Git.Each environment is defined by an Azure Resource Manager template in a Git repository. Il [repository Git pubblico](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) per DevTest Labs contiene il modello Gestione risorse per creare un cluster di Service Fabric nella cartella [ServiceFabric-Cluster.](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) 

1. Creare innanzitutto un lab in Lab di Azure devTest usando le istruzioni nell'articolo seguente: [Creare un lab.](devtest-lab-create-lab.md) Si noti che l'opzione **Ambienti pubblici** è **attivata** per impostazione predefinita. 
2. Verificare che il provider di Service Fabric sia registrato per l'abbonamento attenendosi alla procedura seguente:
    1. Selezionare **Abbonamenti** nel menu di navigazione **Subscription** a sinistra, quindi selezionare
    2. Nella pagina **Sottoscrizione** selezionare **Provider di risorse** nella sezione **Impostazioni** del menu a sinistra. 
    3. Se **Microsoft.ServiecFabric** non è registrato, selezionare **Registra**. 
3. Nella pagina **DevTest Labs** selezionare **+ Aggiungi** sulla barra degli strumenti. 
    
    ![Pulsante Aggiungi sulla barra degli strumenti](./media/create-environment-service-fabric-cluster/add-button.png)
3. Nella pagina **Scegliere una base** selezionare Cluster lab di Service **Fabric** nell'elenco. 

    ![Selezionare Service Fabric Lab Cluster nell'elencoSelect Service Fabric Lab Cluster in the list](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Nella pagina **Configura impostazioni** eseguire le operazioni seguenti: 
    1. Specificare un **nome** per **l'ambiente**cluster. Si tratta del nome del gruppo di risorse in Azure in cui verrà creato il cluster di Service Fabric.This is the name of the resource group in Azure in which the Service Fabric cluster is going to be created. 
    2. Selezionare il **sistema operativo (OS)** per le macchine virtuali del cluster. Il valore predefinito è: **Windows**.
    3. Specificare un nome per **l'amministratore** del cluster. 
    4. Specificare una **password** per l'amministratore. 
    5. Per il **certificato,** immettere le informazioni sul certificato come stringa con codifica Base64. Per creare un certificato, eseguire la procedura seguente:To create a certificate, do the following steps:
        1. Scaricare il file **Create-ClusterCertificate.ps1** dal [repository Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). In alternativa, clonare il repository sul computer. 
        2. Avviare **PowerShell**. 
        3. Eseguire il file **ps1** utilizzando il comando `.\Create-ClusterCertificate.ps1`. Viene visualizzato un file di testo aperto nel blocco note con le informazioni necessarie per compilare i campi relativi ai certificati in questa pagina. . 
    6. Immettere la **password per il certificato**.
    7. Specificare **l'identificazione personale** per il certificato.
    8. Selezionare **Aggiungi** nella pagina **Configura impostazioni.** 

        ![Configurare le impostazioni del cluster](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Dopo aver creato il cluster, viene visualizzato un gruppo di risorse con il nome dell'ambiente fornito nel passaggio precedente. Quando si espande, viene visualizzato il cluster Di Service Fabric in esso. Se lo stato del gruppo di risorse è bloccato in **Creazione**, selezionare **Aggiorna** sulla barra degli strumenti. L'ambiente cluster Service Fabric crea un cluster a 5 nodi 1-nodetype in Linux o Windows.The **Service Fabric cluster** environment creates a 5-node 1-nodetype cluster on either Linux or Windows.

    Nell'esempio seguente **mysfabricclusterrg** è il nome del gruppo di risorse creato in modo specifico per il cluster Service Fabric. È importante notare che gli ambienti lab sono indipendenti all'interno del gruppo di risorse in cui vengono creati. Ciò significa che il modello che definisce l'ambiente, che può accedere solo alle risorse all'interno del gruppo di risorse appena creato o delle [reti virtuali configurate per l'utilizzo da parte del lab.](devtest-lab-configure-vnet.md) Questo esempio precedente crea tutte le risorse necessarie nello stesso gruppo di risorse.

    ![Cluster creato](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Avviare o arrestare il cluster
È possibile avviare o arrestare il cluster dalla pagina DevTest Lab stessa o dalla pagina Cluster di Service Fabric fornita da DevTest Labs. 

### <a name="from-the-devtest-lab-page"></a>Dalla pagina DevTest Lab
È possibile avviare o arrestare il cluster nella pagina DevTest Lab per il lab. 

1. Selezionare tre punti (...) per il cluster di Service Fabric, come illustrato nell'immagine seguente:Select three **puntis (...)** for the Service Fabric cluster as shown in the following image: 

    ![Avviare e arrestare i comandi per il clusterStart and stop commands for the cluster](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. Vengono visualizzati due comandi nel menu di scelta rapida per **avviare** e **arrestare** il cluster. Il comando start avvia tutti i nodi di un cluster. Il comando stop arresta tutti i nodi di un cluster. Una volta arrestato un cluster, il cluster Service Fabric stesso rimane in uno stato pronto, ma non sono disponibili nodi fino a quando il comando di avvio non viene riemesso nel cluster in lab.

    Quando si usa un cluster di Service Fabric in un ambiente di test, è necessario tenere presenti alcune considerazioni. La reidratazione del cluster di Service Fabric dopo il riavvio dei nodi potrebbe richiedere del tempo. Per mantenere i dati dall'arresto all'avvio, i dati devono essere salvati su un disco gestito collegato alla macchina virtuale. Quando si utilizza un disco gestito collegato, è consigliabile che si tratti di problemi di prestazioni solo per gli ambienti di test. Se non viene eseguito il backup del disco utilizzato per l'archiviazione dei dati, i dati vengono persi quando viene emesso il comando stop nel cluster.

### <a name="from-the-service-fabric-cluster-page"></a>Dalla pagina Cluster di Service Fabric 
Esiste un altro modo per avviare o arrestare il cluster. 

1. Selezionare il cluster di Service Fabric nella visualizzazione albero della pagina DevTest Lab.Select your Service Fabric cluster in the tree view on the DevTest Lab page. 

    ![Selezionare il cluster](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. Nella pagina Cluster di **Service Fabric** per il cluster vengono visualizzati i comandi sulla barra degli strumenti per avviare o arrestare il cluster. 

    ![Avviare o arrestare i comandi nella pagina Cluster di Service FabricStart or stop commands in the Service Fabric Cluster page](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Configurare la pianificazione dell'avvio automatico e dell'arresto automatico
I cluster di Service Fabric possono anche essere avviati o arrestati in base a una pianificazione. Questa esperienza è simile all'esperienza per le macchine virtuali in un lab. Per risparmiare, per impostazione predefinita, ogni cluster creato in un laboratorio viene arrestato automaticamente al momento definito dai criteri di [arresto](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)lab. È possibile eseguire l'override specificando se il cluster deve essere arrestato o specificando l'ora in cui il cluster viene arrestato. 

![Pianificazioni esistenti per l'avvio e l'arresto automatico](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Scegliere la pianificazione di avvio automatico
Per acconsentire esplicitamente alla pianificazione di avvio, procedere come segue:

1. Selezionare **Avvio automatico** nel menu a sinistra
2. Selezionare **Attivato** per Consenti la pianificazione dell'avvio automatico di **questo cluster dell'infrastruttura**del servizio. Questa pagina è abilitata solo se il proprietario del lab ha consentito agli utenti di avviare automaticamente le macchine virtuali o i cluster di Service Fabric.This page is enabled only if the lab owner has allowed users to autostart their virtual machines or Service Fabric clusters.
3. Sulla barra degli strumenti selezionare **Salva**. 

    ![Pagina stella automatica](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Configurare le impostazioni di arresto automatico 
Per modificare le impostazioni per l'arresto, attenersi alla seguente procedura:

1. Selezionare **Spegnimento automatico** nel menu a sinistra. 
2. In questa pagina è possibile disattivare l'arresto automatico selezionando **Disattivato** per **Abilitato**. 
3. Se è stata selezionata l'opzione **Attivato** per **Abilitato**, attenersi alla seguente procedura:
    1. Specificare **l'ora** di arresto.
    2. Specificare il **fuso orario** per l'ora. 
    3. Specificare se si desidera che DevTest Labs **invii notifiche** prima dell'arresto automatico. 
    4. Se è stata selezionata l'opzione **Sì** per l'opzione di notifica, specificare l'URL e/o **l'indirizzo** di posta elettronica **del webhook** per l'invio delle notifiche. 
    5. Sulla barra degli strumenti selezionare **Salva**.

        ![Pagina di arresto automatico](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Per visualizzare i nodi nel cluster di Service FabricTo view nodes in the Service Fabric cluster
La pagina del cluster di Service Fabric visualizzata nei passaggi precedenti è specifica della pagina DevTest Labs. Non mostra i nodi nel cluster. Per ulteriori informazioni sul cluster, attenersi alla seguente procedura:

1. Nella pagina **Lab DevTest** per il lab selezionare il **gruppo di risorse** nella visualizzazione albero nella sezione Macchine **virtuali** personali.

    ![Selezionare il gruppo di risorse](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Nella pagina **Gruppo di** risorse vengono visualizzate tutte le risorse del gruppo di risorse in un elenco. Selezionare il **cluster Di Service Fabric** dall'elenco. 

    ![Selezionare il cluster nell'elenco](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Viene visualizzata la pagina **Cluster di Service Fabric** per il cluster. Questa è la pagina fornita da Service Fabric. Vengono visualizzate tutte le informazioni sui cluster, ad esempio nodi, tipi di nodo e così via.

    ![Home page del cluster dell'infrastruttura di servizioService Fabric Cluster home page](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate sugli ambienti, vedere gli articoli seguenti:See the following articles for details about environments: 

- [Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurare e usare ambienti pubblici in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
