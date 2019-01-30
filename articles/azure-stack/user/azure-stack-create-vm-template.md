---
title: In questa esercitazione si crea una macchina virtuale di Azure Stack usando un modello | Microsoft Docs
description: Viene descritto come usare il ASDK per creare una macchina virtuale usando un modello predefinito e un modello personalizzato di GitHub.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/13/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: 9ccdea6ca0bf46bbc19e5a7e19ac2d3527138345
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241781"
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>Esercitazione: creare una macchina virtuale usando un modello della community

Un utente o un operatore di Azure Stack, è possibile creare una macchina virtuale usando [modelli di avvio rapido di GitHub personalizzati](https://github.com/Azure/AzureStack-QuickStart-Templates) invece di distribuire uno manualmente dal marketplace di Azure Stack.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare i modelli di avvio rapido di Azure Stack 
> * Creare una macchina virtuale usando un modello personalizzato di GitHub
> * Avviare minikube e installare un'applicazione

## <a name="azure-stack-quickstart-templates"></a>Modelli di avvio rapido di Azure Stack

I modelli di avvio rapido di Azure Stack vengono archiviati nel [pubblica repository dei modelli di Azure Stack QuickStart](https://github.com/Azure/AzureStack-QuickStart-Templates) su GitHub. Questo repository contiene modelli di distribuzione Azure Resource Manager che sono stati testati con Microsoft Azure Stack Development Kit (ASDK). È possibile utilizzarli per renderne più semplice per valutare Azure Stack e usare l'ambiente ASDK. 

Nel corso del tempo numerosi utenti GitHub hanno contribuito al repository, risultante in una raccolta di modelli di distribuzione di più di 400. Questo repository è un ottimo punto di partenza per ottenere una migliore comprensione del modo in cui è possibile distribuire diversi tipi di ambienti in Azure Stack. 

>[!IMPORTANT]
> Alcuni di questi modelli vengono creati da membri della community e non da Microsoft. Ogni modello è concesso in licenza con un contratto di licenza dal proprietario, non da Microsoft. Microsoft non è responsabile di questi modelli e non ne verifica la sicurezza, compatibilità o prestazioni. Modelli della community non sono supportati in alcun programma di supporto tecnico Microsoft o un servizio e vengono resi disponibili "Com'è," senza garanzia di alcun tipo.

Se si vuole contribuire con i modelli di Azure Resource Manager da GitHub, apportare il contributo per i [AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates) repository. Per altre informazioni su come contribuire ad esso e questo repository, vedere la [file Leggimi](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md). 

## <a name="create-a-vm-using-a-custom-github-template"></a>Creare una macchina virtuale usando un modello personalizzato di GitHub

In questa esercitazione di esempio, il [101-vm-linux-minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) viene usato il modello di avvio rapido di Azure Stack per distribuire una macchina virtuale di Ubuntu 16.04 nello Stack di Azure in esecuzione Minikube per gestire un cluster Kubernetes.

Minikube è uno strumento che rende più semplice eseguire Kubernetes in locale. Minikube esegue un cluster Kubernetes a nodo singolo all'interno di una macchina virtuale, che consente di provare a Kubernetes o svilupparlo quotidiane. Supporta un cluster Kubernetes semplice, un nodo in esecuzione in una VM Linux. Minikube è il modo più semplice e rapido per ottenere un cluster Kubernetes completamente funzionale in esecuzione. Consente agli sviluppatori di sviluppare e testare le distribuzioni di applicazioni basate su Kubernetes sul proprio computer locale. A livello di architettura, la VM Minikube esegue in locale sia Master componenti dell'agente del nodo:

- Componenti del nodo master, ad esempio Server dell'API, utilità di pianificazione, e [etcd Server](https://coreos.com/etcd/) vengono eseguiti in un singolo processo di Linux denominato LocalKube.
- Componenti del nodo agente vengono eseguiti all'interno di contenitori docker esattamente come si verificavano su un nodo dell'agente normale. Dal punto di vista di distribuzione dell'applicazione, non vi è alcuna differenza tra quando l'applicazione viene distribuita in un Minikube o in un cluster Kubernetes regolare.

Questo modello installa i componenti seguenti:

- Ubuntu 16.04 LTS VM
- [Docker-CE](https://download.docker.com/linux/ubuntu) 
- [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
- [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
- xFCE4
- xRDP

> [!IMPORTANT]
> La VM Ubuntu immagine (Ubuntu Server 16.04 LTS in questo esempio) deve già essere stata aggiunta nel Marketplace di Azure Stack prima di eseguire questi passaggi.

1.  Selezionare **+ crea una risorsa**, quindi **Custom**, quindi **distribuzione modello**.

    ![](media/azure-stack-create-vm-template/1.PNG) 

2. Selezionare **modifica modello**.

    ![](media/azure-stack-create-vm-template/2.PNG) 

3.  Selezionare **modello di avvio rapido**.

    ![](media/azure-stack-create-vm-template/3.PNG)

4. Selezionare **101-vm-linux-minikube** tra i modelli disponibili tramite il **selezionare un modello** elenco a discesa elenco e quindi fare clic su **OK**.  

    ![](media/azure-stack-create-vm-template/4.PNG)

5. Se si desidera apportare modifiche al modello JSON è possibile farlo. In caso contrario, o al termine dell'esercitazione, selezionare **salvare** per chiudere la **modifica modello** finestra di dialogo.

    ![](media/azure-stack-create-vm-template/5.PNG) 

6.  Selezionare **parametri**compilare o modificare i campi disponibili in base alle esigenze e quindi fare clic su **OK**. Scegliere la sottoscrizione da usare, creare o scegliere un nome di gruppo di risorse esistente e quindi selezionare **Create** per avviare la distribuzione del modello.

    ![](media/azure-stack-create-vm-template/6.PNG)

7. Scegliere la sottoscrizione da usare, creare o scegliere un nome di gruppo di risorse esistente e quindi selezionare **Create** per avviare la distribuzione del modello.

    ![](media/azure-stack-create-vm-template/7.PNG)

8. Distribuzione del gruppo di risorse richiede alcuni minuti per creare la macchina virtuale personalizzata basata su modello. È possibile monitorare lo stato dell'installazione tramite le notifiche e dalle proprietà del gruppo di risorse. 

    ![](media/azure-stack-create-vm-template/8.PNG)

    >[!NOTE]
    > La macchina virtuale verrà eseguito al termine della distribuzione. 

## <a name="start-minikube-and-install-an-application"></a>Avviare Minikube e installare un'applicazione

Ora che la VM Linux è stata creata correttamente, è possibile accedere per avviare Minikube e installare un'applicazione. 

1. Al termine della distribuzione, selezionare **Connect** per visualizzare l'indirizzo IP pubblico che verrà usato per connettersi alla VM Linux. 

    ![](media/azure-stack-create-vm-template/9.PNG)

2. Da un prompt dei comandi con privilegi elevati, eseguire **mstsc.exe** per aprire connessione Desktop remoto e connettersi alla VM Linux indirizzo IP pubblico individuato nel passaggio precedente. Quando viene richiesto di accedere a xRDP, usare le credenziali specificate durante la creazione della macchina virtuale.

    ![](media/azure-stack-create-vm-template/10.PNG)

3. Aprire l'emulatore di terminale e immettere i comandi per avviare Minikube seguenti:

    ```shell
    sudo minikube start --vm-driver=none
    sudo minikube addons enable dashboard
    sudo minikube dashboard --url
    ```

    ![](media/azure-stack-create-vm-template/11.PNG)

4. Aprire il web browser e visitare l'indirizzo del dashboard di Kubernetes. Congratulazioni, ora è completamente funzionanti installazione Kubernetes usando Minikube!

    ![](media/azure-stack-create-vm-template/12.PNG)

5. Per distribuire un'applicazione di esempio, visitare la pagina della documentazione ufficiale di Kubernetes e ignorare la sezione "Creazione del Cluster di Minikube" perché è già stato creato uno precedente. Passare alla sezione "Creare l'applicazione Node. js" in https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Informazioni sui modelli di avvio rapido di Azure Stack 
> * Creare una macchina virtuale usando un modello personalizzato di GitHub
> * Avviare minikube e installare un'applicazione

