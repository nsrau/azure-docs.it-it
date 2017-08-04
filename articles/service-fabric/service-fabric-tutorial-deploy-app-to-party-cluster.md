---
title: "Distribuire un'applicazione di Azure Service Fabric in un cluster di entità | Microsoft Docs"
description: "Informazioni su come distribuire un'applicazione in un cluster di entità."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/03/2017
ms.author: mikhegn
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 1fde062d9673a48698b269d79e009c7b179e5934
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="deploy-an-application-to-a-party-cluster-in-azure"></a>Distribuire un'applicazione in un cluster di entità in Azure
In questa esercitazione viene illustrato come distribuire un'applicazione di Azure Service Fabric in un cluster di entità in Azure.

In questa parte dell'esercitazione si apprende come:
> [!div class="checklist"]
> * Distribuire un'applicazione in un cluster remoto usando Visual Studio
> * Rimuovere un'applicazione da un cluster usando Service Fabric Explorer

L'esercitazione è suddivisa in tre articoli e questo è il secondo della serie.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione:
- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Installare Visual Studio 2017](https://www.visualstudio.com/) e installare i carichi di lavoro **Sviluppo di Azure** e **Sviluppo ASP.NET e Web**.
- [Installare Service Fabric SDK](service-fabric-get-started.md)

## <a name="set-up-a-party-cluster"></a>Configurare un cluster di entità
I cluster di entità sono cluster Service Fabric gratuiti e disponibili per un periodo di tempo limitato ospitati in Azure e gestiti dal team di Service Fabric, in cui chiunque può distribuire applicazioni e ottenere informazioni sulla piattaforma. Gratuitamente.

Per accedere a un cluster di entità, passare a questo sito: http://aka.ms/tryservicefabric e seguire le istruzioni per ottenere l'accesso a un cluster. È necessario un account Facebook o GitHub per ottenere l'accesso a un cluster di entità.

> [!NOTE]
> I cluster di entità non sono protetti, quindi le applicazioni e tutti i dati inseriti negli stessi possono essere visibili ad altri utenti. Non distribuire elementi che gli altri utenti non devono vedere. Assicurarsi di leggere tutti i dettagli nelle Condizioni per l'utilizzo.

## <a name="make-your-application-ready-for-deployment"></a>Rendere l'applicazione pronta per la distribuzione
Poiché il servizio API Web ASP.NET Core agisce come front-end per l'applicazione e accetta il traffico esterno, è opportuno associare tale servizio a una porta fissa e conosciuta. La porta è specificata nel file **ServiceManifest.xml** dei servizi.

1. In Esplora soluzioni aprire **WebAPIFrontEnd->PackageRoot->ServiceManifest.xml**.
2. Modificare l'attributo **Port** dell'oggetto **Endpoint** esistente impostandolo su **80** e salvare le modifiche.

## <a name="deploy-the-app-to-the-azure"></a>Distribuire l'app in Azure
Ora che l'applicazione è pronta, è possibile distribuirla nel cluster di entità direttamente da Visual Studio.

1. Fare clic con il pulsante destro del mouse su **MyApplication** in Esplora soluzioni e scegliere **Pubblica**.

    ![Finestra di dialogo Pubblica](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

2. Digitare l'endpoint della connessione del cluster di entità nel campo **Endpoint connessione** e fare clic su **Pubblica**.

    Completata la pubblicazione, dovrebbe essere possibile inviare una richiesta all'applicazione usando un browser.

3. Aprire il browser preferito, digitare l'indirizzo del cluster (l'endpoint di connessione senza le informazioni sulla porta, ad esempio win1kw5649s.westus.cloudapp.azure.com) e aggiungere `/api/values` all'URL.

    Deve apparire lo stesso risultato visualizzato quando si esegue l'applicazione in locale.

    ![Risposta API dal cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="remove-the-application-from-a-cluster-using-service-fabric-explorer"></a>Rimuovere l'applicazione da un cluster usando Service Fabric Explorer
Service Fabric Explorer è un'interfaccia utente grafica che consente di esplorare e gestire le applicazioni in un cluster Service Fabric.

Per rimuovere l'applicazione distribuita nel cluster di entità:

1. Passare a Service Fabric Explorer usando il collegamento indicato nella pagina di iscrizione al cluster di entità. Ad esempio, http://win1kw5649s.westus.cloudapp.azure.com:19080/Explorer/index.html.

2. In Service Fabric Explorer passare al nodo **fabric://MyApplication** nella visualizzazione albero sul lato sinistro.

3. Fare clic sul pulsante **Azione** nel riquadro **Informazioni di base** a destra e scegliere **Elimina applicazione**. Confermare l'eliminazione dell'istanza di applicazione per rimuovere l'istanza dell'applicazione in esecuzione nel cluster.

![Eliminare un'applicazione in Service Fabric Explorer](./media/service-fabric-tutorial-deploy-app-to-party-cluster/delete-application.png)

Le applicazioni vengono distribuite come tipi di applicazioni in un cluster Service Fabric, che consente di avere più istanze e versioni dell'applicazione in esecuzione nel cluster. Dopo avere rimosso l'istanza dell'applicazione in esecuzione, è anche possibile rimuovere il tipo, per completare la pulizia della distribuzione.

Per altre informazioni sul modello di applicazione in Service Fabric, vedere [Modellare un'applicazione in Service Fabric](service-fabric-application-model.md).

1. Passare al nodo **MyApplicationType** nella visualizzazione albero.

2. Fare clic sul pulsante **Azione** nel riquadro **Informazioni di base** a destra e scegliere **Unprovision Type** (Annulla provisioning tipo). Confermare l'annullamento del provisioning del tipo di applicazione.

![Annullare il provisioning del tipo di applicazione in Service Fabric Explorer](./media/service-fabric-tutorial-deploy-app-to-party-cluster/unprovision-type.png)

L'esercitazione è terminata.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Distribuire un'applicazione in un cluster remoto usando Visual Studio
> * Rimuovere un'applicazione da un cluster usando Service Fabric Explorer

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Impostare l'integrazione continua usando Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
