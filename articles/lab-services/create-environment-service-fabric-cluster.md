---
title: Creare un ambiente con un cluster di Service Fabric in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come creare un ambiente con un cluster Service Fabric autonomo e avviare e arrestare il cluster usando le pianificazioni.
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
ms.openlocfilehash: 1e192a2b27c9d617e43a56766431a0f40e87a752
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325248"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Creare un ambiente con un cluster Service Fabric autonomo in Azure DevTest Labs
Questo articolo fornisce informazioni su come creare un ambiente con un cluster Service Fabric autonomo in Azure DevTest Labs. 

## <a name="overview"></a>Panoramica
DevTest Labs può creare ambienti di test autonomi come definito dai modelli di Azure Resource Manager. Questi ambienti contengono sia risorse IaaS, ad esempio macchine virtuali, che risorse PaaS, ad esempio Service Fabric. DevTest Labs consente di gestire le macchine virtuali in un ambiente fornendo comandi per controllare le macchine virtuali. Questi comandi offrono la possibilità di avviare o arrestare una macchina virtuale in base a una pianificazione. Analogamente, DevTest Labs può anche aiutare a gestire i cluster Service Fabric in un ambiente. È possibile avviare o arrestare un cluster Service Fabric in un ambiente sia manualmente che tramite una pianificazione.

## <a name="create-a-service-fabric-cluster"></a>Creare un cluster di Service Fabric
I cluster Service Fabric vengono creati usando gli ambienti in DevTest Labs. Ogni ambiente è definito da un modello di Azure Resource Manager in un repository git. Il [repository git pubblico](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) per DevTest Labs contiene il modello di gestione risorse per creare un cluster Service Fabric nella cartella [ServiceFabric-cluster](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) . 

1. Per prima cosa, creare un Lab in Azure DevTest Labs usando le istruzioni riportate nell'articolo seguente: [Creare un Lab](devtest-lab-create-lab.md). Si noti che l'opzione **ambienti pubblici** è **attiva** per impostazione predefinita. 
2. Verificare che il provider di Service Fabric sia registrato per la sottoscrizione attenendosi alla procedura seguente:
    1. Selezionare **sottoscrizioni** nel menu di spostamento a sinistra e selezionare la **sottoscrizione**
    2. Nella pagina **sottoscrizione** selezionare provider di **risorse** nella sezione **Impostazioni** nel menu a sinistra. 
    3. Se **Microsoft. ServiecFabric** non è registrato, selezionare **registra**. 
3. Nella pagina **DevTest Labs** selezionare **+ Aggiungi** sulla barra degli strumenti. 
    
    ![Pulsante Aggiungi sulla barra degli strumenti](./media/create-environment-service-fabric-cluster/add-button.png)
3. Nella pagina **scegliere una base** selezionare **Service Fabric cluster Lab** nell'elenco. 

    ![Selezionare Service Fabric cluster Lab nell'elenco](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Nella pagina **Configura impostazioni** seguire questa procedura: 
    1. Specificare un **nome** per l' **ambiente**cluster. Si tratta del nome del gruppo di risorse in Azure in cui verrà creato il cluster Service Fabric. 
    2. Selezionare il **sistema operativo** per le macchine virtuali del cluster. Il valore predefinito è: **Windows**.
    3. Specificare un nome per l' **amministratore** del cluster. 
    4. Specificare una **password** per l'amministratore. 
    5. Per il **certificato**, immettere le informazioni sul certificato come stringa con codifica Base64. Per creare un certificato, attenersi alla procedura seguente:
        1. Scaricare il file **create-ClusterCertificate. ps1** dal [repository git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). In alternativa, clonare il repository nel computer. 
        2. Avviare **PowerShell**. 
        3. Eseguire il file **ps1** usando il comando `.\Create-ClusterCertificate.ps1`. Viene visualizzato un file di testo aperto nel blocco note con le informazioni necessarie per inserire i campi relativi ai certificati in questa pagina. . 
    6. Immettere la **password per il certificato**.
    7. Specificare l' **identificazione personale** per il certificato.
    8. Selezionare **Aggiungi** nella pagina **Configura impostazioni** . 

        ![Configurare le impostazioni del cluster](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Dopo aver creato il cluster, viene visualizzato un gruppo di risorse con il nome dell'ambiente specificato nel passaggio precedente. Quando si espande, viene visualizzato il cluster Service Fabric. Se lo stato del gruppo di risorse è bloccato durante la **creazione**, selezionare **Aggiorna** sulla barra degli strumenti. L'ambiente **cluster Service Fabric** crea un cluster da 1 nodo a 5 nodi in Linux o Windows.

    Nell'esempio seguente **mysfabricclusterrg** è il nome del gruppo di risorse creato in modo specifico per il cluster Service Fabric. È importante notare che gli ambienti Lab sono autonomi all'interno del gruppo di risorse in cui vengono creati. Significa che il modello che definisce l'ambiente, che può accedere solo alle risorse all'interno del gruppo di risorse appena creato o delle [reti virtuali configurate per essere usate dal Lab](devtest-lab-configure-vnet.md). Questo esempio precedente crea tutte le risorse necessarie nello stesso gruppo di risorse.

    ![Cluster creato](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Avviare o arrestare il cluster
È possibile avviare o arrestare il cluster dalla pagina DevTest Lab o dalla pagina del cluster Service Fabric fornita da DevTest Labs. 

### <a name="from-the-devtest-lab-page"></a>Dalla pagina DevTest Lab
È possibile avviare o arrestare il cluster nella pagina DevTest Lab per il Lab. 

1. Selezionare **tre punti (...)** per il cluster Service Fabric come illustrato nell'immagine seguente: 

    ![Comandi di avvio e arresto per il cluster](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. Vengono visualizzati due comandi nel menu di scelta rapida per **avviare** e **arrestare** il cluster. Il comando avvia avvia tutti i nodi in un cluster. Il comando stop arresta tutti i nodi in un cluster. Quando un cluster viene arrestato, il cluster Service Fabric stesso rimane in uno stato pronto, ma non sono disponibili nodi fino a quando il comando di avvio non viene riemesso nel cluster nel Lab.

    Quando si usa un cluster Service Fabric in un ambiente di testing, è necessario tenere presenti alcune considerazioni. Il riattivazione del cluster Service Fabric potrebbe richiedere del tempo dopo il riavvio dei nodi. Per mantenere i dati dall'arresto all'avvio, è necessario salvare i dati su un disco gestito collegato alla macchina virtuale. L'uso di un disco gestito collegato presenta implicazioni sulle prestazioni, pertanto è consigliabile solo per gli ambienti di test. Se il disco utilizzato per l'archiviazione dei dati non è supportato, i dati andranno perduti quando il comando Interrompi viene emesso nel cluster.

### <a name="from-the-service-fabric-cluster-page"></a>Dalla pagina del cluster Service Fabric 
Esiste un altro modo per avviare o arrestare il cluster. 

1. Selezionare il cluster di Service Fabric nella visualizzazione albero nella pagina DevTest Lab. 

    ![Selezionare il cluster](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. Nella pagina **Service Fabric cluster** per il cluster, è possibile visualizzare i comandi sulla barra degli strumenti per avviare o arrestare il cluster. 

    ![Comandi Avvia o Interrompi nella pagina del cluster Service Fabric](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Configurare la pianificazione di avvio automatico e arresto automatico
È anche possibile avviare o arrestare i cluster di Service Fabric in base a una pianificazione. Questa esperienza è simile all'esperienza per le macchine virtuali in un Lab. Per risparmiare denaro, per impostazione predefinita tutti i cluster creati in un Lab si arrestano automaticamente al momento della definizione dei [criteri di arresto](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)del Lab. È possibile eseguire l'override specificando se il cluster deve essere arrestato o specificando l'ora in cui il cluster viene arrestato. 

![Pianificazioni esistenti per avvio automatico e arresto automatico](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Acconsentire esplicitamente alla pianificazione dell'avvio automatico
Per acconsentire esplicitamente alla pianificazione dell'avvio, seguire questa procedura:

1. Selezionare **avvio automatico** nel menu a sinistra.
2. Selezionare **on** per **consentire a questo cluster di Service fabric di essere pianificato per l'avvio automatico**. Questa pagina è abilitata solo se il proprietario del Lab ha consentito agli utenti di avviare le macchine virtuali o i cluster Service Fabric.
3. Sulla barra degli strumenti selezionare **Salva**. 

    ![Pagina a stella automatica](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Configurare le impostazioni di arresto automatico 
Per modificare le impostazioni per l'arresto, attenersi alla procedura seguente:

1. Selezionare **arresto automatico** nel menu a sinistra. 
2. In questa pagina è possibile rifiutare esplicitamente l'arresto automatico selezionando **disattivato** per **attivato**. 
3. Se è **stata selezionata l'** opzione **attiva**, attenersi alla seguente procedura:
    1. Specificare l' **ora** di arresto.
    2. Specificare il **fuso orario** per l'ora. 
    3. Specificare se si vuole che DevTest Labs invii le **notifiche** prima dell'arresto automatico. 
    4. Se è stato selezionato **Sì** per l'opzione di notifica, specificare l' **URL** del webhook e/o l' **indirizzo di posta elettronica** per inviare le notifiche. 
    5. Sulla barra degli strumenti selezionare **Salva**.

        ![Arresta automaticamente pagina](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Per visualizzare i nodi nel cluster Service Fabric
La pagina del cluster Service Fabric visualizzata nei passaggi precedenti è specifica della pagina DevTest Labs. Non vengono visualizzati i nodi del cluster. Per visualizzare altre informazioni sul cluster, seguire questa procedura:

1. Nella pagina **DevTest Lab** per il Lab selezionare il gruppo di **risorse** nella visualizzazione albero nella sezione **macchine virtuali personali** .

    ![Selezionare il gruppo di risorse](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Nella pagina **gruppo di risorse** vengono visualizzate tutte le risorse nel gruppo di risorse in un elenco. Selezionare il **cluster Service Fabric** dall'elenco. 

    ![Selezionare il cluster nell'elenco](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Viene visualizzata la pagina **Service Fabric cluster** per il cluster. Si tratta della pagina fornita dal Service Fabric. Vengono visualizzate tutte le informazioni sui cluster, ad esempio nodi, tipi di nodo e così via.

    ![home page Service Fabric cluster](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate sugli ambienti, vedere gli articoli seguenti: 

- [Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurare e usare ambienti pubblici in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
