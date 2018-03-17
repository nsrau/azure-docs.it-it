---
title: In questa esercitazione, si crea una macchina virtuale di Azure Stack usando un modello | Documenti Microsoft
description: Viene descritto come utilizzare il ASDK per creare una macchina virtuale utilizzando un modello predfined e un modello personalizzato di GitHub.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: ec44fc879abfe2a457e80f27db972ac4d7570dbd
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>Esercitazione: creare una macchina virtuale usando un modello della community
Come un operatore di Stack di Azure o un utente, è possibile creare una macchina virtuale utilizzando [modelli personalizzati di avvio rapido GitHub](https://github.com/Azure/AzureStack-QuickStart-Templates) anziché distribuzione uno manualmente tramite il marketplace dello Stack di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Informazioni sui modelli di avvio rapido di Azure Stack 
> * Creare una macchina virtuale usando un modello personalizzato di GitHub
> * Avviare minikube e installare un'applicazione

## <a name="learn-about-azure-stack-quickstart-templates"></a>Informazioni sui modelli di avvio rapido di Azure Stack
Modelli di avvio rapido di Azure dello Stack vengono archiviati nel [repository pubblico di modelli di avvio rapido AzureStack](https://github.com/Azure/AzureStack-QuickStart-Templates) su GitHub. Questo repository contiene modelli di distribuzione Azure Resource Manager che sono stati testati con Microsoft Azure Stack Development Kit (ASDK). È possibile utilizzarli per semplificare la valutazione dello Stack di Azure e utilizzare l'ambiente ASDK. 

Nel corso del tempo molti utenti GitHub hanno contribuito al repository, risultante in una vasta raccolta di modelli di distribuzione più di 400. Questo archivio è un ottimo punto di partenza per ottenere una migliore comprensione del modo in cui è possibile distribuire diversi tipi di ambienti allo Stack di Azure. 

>[!IMPORTANT]
> Alcuni di questi modelli vengono creati dai membri della community e non da Microsoft. Ogni modello è concesso su licenza con un contratto di licenza dal relativo proprietario, non da Microsoft. Microsoft non è responsabile di questi modelli e non schermata per la sicurezza, compatibilità o prestazioni. Community modelli non sono supportati in alcun programma di supporto Microsoft o un servizio e vengono resi disponibili "Com'è" senza garanzia di alcun tipo.

Se si desidera contribuiscono i modelli di gestione risorse di Azure in GitHub, sarà necessario apportare il contributo per il [repository di azure-Guida introduttiva-modelli](https://github.com/Azure/AzureStack-QuickStart-Templates).

Per ulteriori informazioni sul repository GitHub e su come contribuire a esso, vedere la [file Leggimi del repository](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md). 


## <a name="create-a-vm-using-a-custom-github-template"></a>Creare una macchina virtuale usando un modello personalizzato di GitHub
In questa esercitazione di esempio, il [101-vm-linux-minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) modello di avvio rapido di Azure Stack viene usato per distribuire una macchina virtuale Ubuntu 16.04 nel AzureStack esecuzione Minikube per gestire un cluster kubenetes.

Minikube è uno strumento che rende più semplice eseguire Kubernetes in locale. Minikube viene eseguito un cluster a nodo singolo Kubernetes all'interno di una macchina virtuale per gli utenti vogliono provare Kubernetes o sviluppare con esso quotidiane. Supporta una semplice, da un nodo Kubernetes cluster in esecuzione in una VM Linux. È il modo più veloce e più semplice per ottenere un cluster Kubernetes completamente funzionale in esecuzione. Consente agli sviluppatori di sviluppare e testare le distribuzioni di applicazioni basate su Kubernetes sul proprio computer locale. L'architettura di VM Minikube esegue in locale sia Master e componenti di tale nodo agente:
- Componenti di nodo principale, ad esempio Server dell'API dell'utilità di pianificazione ed etcd Server vengono eseguiti in un unico processo Linux chiamato LocalKube.
- Componenti di tale nodo Agent vengono eseguiti all'interno dei contenitori docker esattamente come sono verrebbe eseguito in un nodo agente normale. Dal punto di vista di distribuzione dell'applicazione, è indifferente quando l'applicazione viene distribuita in un Minikube o regolare Kubernetes cluster.

Questo modello consente di installare i componenti seguenti:

- Macchina virtuale LTS Ubuntu 16.04
- [Docker-CE](https://download.docker.com/linux/ubuntu) 
- [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
- [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
- xFCE4
- xRDP

> [!IMPORTANT]
> L'immagine VM Ubuntu (Ubuntu Server 16.04 LTS in questo esempio) già avere ricevuto [aggiunto nel Marketplace Azure Stack](asdk-marketplace-item.md) prima di iniziare la procedura seguente.

1.  Fare clic su **+ nuovo** > **Custom** > **distribuzione modello**.

    ![](media/asdk-create-vm-template/1.PNG) 

2. Fare clic su **modifica modelli**.

   ![](media/asdk-create-vm-template/2.PNG) 

3.  Fare clic su **modello di avvio rapido**.

       ![](media/asdk-create-vm-template/3.PNG)

4. Selezionare **101-vm-linux-minikube** tra i modelli disponibili tramite il **selezionare un modello** elenco a discesa elenco e quindi fare clic su **OK**.  

   ![](media/asdk-create-vm-template/4.PNG)

5. Se si desidera apportare modifiche al modello JSON è possibile eseguire questa operazione, se non o al termine, fare clic su **salvare** per chiudere la finestra di dialogo Modifica modello.

   ![](media/asdk-create-vm-template/5.PNG) 

6.  Fare clic su **parametri**compilare o modificare i campi disponibili in base alle esigenze e quindi fare clic su **OK**. Scegliere la sottoscrizione da utilizzare, creare o scegliere un nome di gruppo di risorse esistente e quindi fare clic su **crea** per avviare la distribuzione modello.

       ![](media/asdk-create-vm-template/6.PNG)

7. Scegliere la sottoscrizione da utilizzare, creare o scegliere un nome di gruppo di risorse esistente e quindi fare clic su **crea** per avviare la distribuzione modello.

   ![](media/asdk-create-vm-template/7.PNG)

8. La distribuzione di gruppo di risorse potrebbe impiegare diversi minuti per creare la VM basate su modelli personalizzata. È possibile monitorare lo stato dell'installazione tramite le notifiche e dalle proprietà del gruppo di risorse. 

   ![](media/asdk-create-vm-template/8.PNG)

   >[!NOTE]
   > La macchina virtuale verrà eseguito al termine della distribuzione. 

## <a name="start-minikube-and-install-an-application"></a>Avviare minikube e installare un'applicazione
Ora che le VM Linux è stata creata correttamente, è possibile accedere per avviare minikube e installare un'applicazione. 

1. Al termine della distribuzione, fare clic su **Connetti** per visualizzare l'indirizzo IP pubblico che verrà utilizzato per connettersi alla VM Linux. 

   ![](media/asdk-create-vm-template/9.PNG)

2. Da un prompt dei comandi con privilegi elevati, eseguire **mstsc.exe** per aprire connessione Desktop remoto e connettersi all'indirizzo IP pubblico di Linux VM individuato nel passaggio precedente. Quando viene richiesto di accedere a xRDP, utilizzare le credenziali specificate durante la creazione della macchina virtuale.

   ![](media/asdk-create-vm-template/10.PNG)

3. Aprire emulatore Terminal e immettere i seguenti comandi per avviare minikube:

    >    `sudo minikube start --vm-driver=none`
    >   
    >    `sudo minikube addons enable dashboard`
    >    
    >    `sudo minikube dashboard --url`

   ![](media/asdk-create-vm-template/11.PNG)

4. Aprire il web browser e visitare l'indirizzo di dashboard kubernetes. Complimenti, ora completamente installata sia funzionante kubernetes utilizzando minikube.

   ![](media/asdk-create-vm-template/12.PNG)

5. Se si desidera distribuire un'applicazione di esempio, visitare la pagina di documentazione ufficiale di kubernetes, ignorare la sezione "Creazione del Cluster Minikube" perché è già stato creato uno precedente. È sufficiente passare alla sezione "Creazione dell'applicazione di Node. js" in https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Informazioni sui modelli di avvio rapido di Azure Stack 
> * Creare una macchina virtuale usando un modello personalizzato di GitHub
> * Avviare minikube e installare un'applicazione


> [!div class="nextstepaction"]
> [Informazioni sulle attività di valutazione avanzate](asdk-advanced-eval.md)