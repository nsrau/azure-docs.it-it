---
title: Distribuire un'applicazione di Azure Service Fabric in un cluster da Visual Studio | Microsoft Docs
description: Informazioni su come distribuire un'applicazione in un cluster da Visual Studio
services: service-fabric
documentationcenter: .net
-author: mikkelhegn
-manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/21/2018
ms.author: mikkelhegn
ms.custom: mvc
ms.openlocfilehash: 21c991a4e3f9ae19a4ad4a96427fdc1c91c55a1c
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="tutorial-deploy-an-application-to-a-service-fabric-cluster-in-azure"></a>Esercitazione: Distribuire un'applicazione in un cluster di Service Fabric in Azure
Questa esercitazione è la seconda parte di una serie e illustra come distribuire un'applicazione di Azure Service Fabric in un nuovo cluster in Azure direttamente da Visual Studio.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Creare un cluster da Visual Studio
> * Distribuire un'applicazione in un cluster remoto usando Visual Studio


In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * [Creare un'applicazione di Service Fabric .NET](service-fabric-tutorial-create-dotnet-app.md)
> * Distribuire l'applicazione in un cluster remoto
> * [Configurare l'integrazione continua e la distribuzione continua usando Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Configurare il monitoraggio e la diagnostica per l'applicazione](service-fabric-tutorial-monitoring-aspnet.md)


## <a name="prerequisites"></a>prerequisiti
Prima di iniziare questa esercitazione:
- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Installare Visual Studio 2017](https://www.visualstudio.com/) e installare i carichi di lavoro **Sviluppo di Azure** e **Sviluppo ASP.NET e Web**.
- [Installare Service Fabric SDK](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Scaricare l'applicazione di voto di esempio
Se non si è creata l'applicazione di voto di esempio nella [prima parte di questa serie di esercitazioni](service-fabric-tutorial-create-dotnet-app.md), è possibile scaricarla. In una finestra di comando eseguire il comando seguente per clonare il repository dell'app di esempio nel computer locale.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="deploy-the-sample-application"></a>Distribuire l'applicazione di esempio

### <a name="select-a-service-fabric-cluster-to-which-to-publish"></a>Selezionare un cluster di Service Fabric per la pubblicazione
Ora che l'applicazione è pronta, è possibile distribuirla in un cluster direttamente da Visual Studio.

Per la distribuzione sono disponibili due opzioni:
- Creare un cluster da Visual Studio. Questa opzione consente di creare un cluster sicuro direttamente da Visual Studio con le configurazioni preferite. Questo tipo di cluster è la soluzione ideale per scenari di test, in cui è possibile creare il cluster e quindi eseguirvi direttamente la pubblicazione all'interno di Visual Studio.
- Eseguire la pubblicazione in un cluster esistente nella sottoscrizione.

In questa esercitazione si seguirà la procedura per creare un cluster da Visual Studio. Per le altre opzioni, è possibile copiare e incollare l'endpoint di connessione o sceglierlo dalla sottoscrizione.
> [!NOTE]
> Molti servizi usano il proxy inverso per comunicare tra loro. Nei cluster creati da Visual Studio e nei party cluster, il proxy inverso è abilitato per impostazione predefinita.  Se si usa un cluster esistente, è necessario [abilitare il proxy inverso nel cluster](service-fabric-reverseproxy.md#setup-and-configuration).

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Distribuire l'app nel cluster di Service Fabric

1. Fare clic con il pulsante destro del mouse sul progetto di applicazione in Esplora soluzioni e scegliere **Pubblica**.

2. Accedere con l'account Azure per poter avere accesso alle sottoscrizioni. Se si usa un party cluster, questo passaggio è facoltativo.

3. Selezionare l'elenco a discesa per **Endpoint connessione** e quindi l'opzione "<Create New Cluster...>".
    
    ![Finestra di dialogo Pubblica](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)
    
4. Nella finestra di dialogo per la creazione del cluster modificare le impostazioni seguenti:

    1. Specificare il nome del cluster nel campo "Nome del cluster", nonché la sottoscrizione e la località da usare.
    2. Facoltativamente, è possibile modificare il numero di nodi. Per impostazione predefinita vengono usati tre nodi, il numero minimo necessario per testare gli scenari di Service Fabric.
    3. Selezionare la scheda "Certificato". In questa scheda digitare una password che verrà usata per proteggere il certificato del cluster. Questo certificato consente di rendere sicuro il cluster. È anche possibile modificare il percorso in cui si vuole salvare il certificato. Visual Studio può anche importare automaticamente il certificato, perché questo passaggio è obbligatorio per la pubblicazione dell'applicazione nel cluster.
    4. Selezionare la scheda "Dettagli macchina virtuale". Specificare la password che si vuole usare per le macchine virtuali (VM) che costituiscono il cluster. Il nome utente e la password possono essere usati per la connessione remota alle VM. Si deve anche selezionare una dimensione di macchina virtuale ed è possibile modificare l'immagine di VM, se necessario.
    5. Facoltativamente, nella scheda "Avanzate" è possibile modificare l'elenco delle porte da aprire nel servizio di bilanciamento del carico che verrà creato con il cluster. È anche possibile aggiungere una chiave di Application Insights esistente a cui indirizzare i file di log dell'applicazione.
    6. Al termine della modifica delle impostazioni, selezionare il pulsante "Crea". La creazione del cluster richiede alcuni minuti. Il completamento dell'operazione verrà indicato nella finestra di output.
    
    ![Finestra di dialogo per la creazione del cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

4. Quando il cluster che si vuole usare è pronto, fare clic con il pulsante destro del mouse sul progetto di applicazione e scegliere **Pubblica**.

    Al termine della pubblicazione, dovrebbe essere possibile inviare una richiesta all'applicazione tramite un browser.

5. Aprire il browser preferito, digitare l'indirizzo del cluster (l'endpoint di connessione senza le informazioni sulla porta, ad esempio win1kw5649s.westus.cloudapp.azure.com).

    Deve apparire lo stesso risultato visualizzato quando si esegue l'applicazione in locale.

    ![Risposta API dal cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un cluster da Visual Studio
> * Distribuire un'applicazione in un cluster remoto usando Visual Studio

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Impostare l'integrazione continua usando Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
