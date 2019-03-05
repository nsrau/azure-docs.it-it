---
title: Distribuire un'app .NET esistente in un contenitore per Service Fabric Mesh | Microsoft Docs
description: Aggiungere il supporto di Service Fabric Mesh a un'app .NET esistente
services: service-fabric-mesh
keywords: distribuire in un contenitore service fabric mesh
author: dkkapur
ms.author: dekapur
ms.date: 11/08/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: bb4aacafaa90f16370bc097b5feab7791def4006
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806947"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>Distribuire un'app .NET esistente in un contenitore per Service Fabric Mesh

Questo articolo illustra come aggiungere il supporto dell'orchestrazione dei contenitori di Service Fabric Mesh a un'app .NET esistente.

In Visual Studio 2017 è possibile aggiungere il supporto della containerizzazione in progetti ASP.NET e Console che usano la versione completa di .NET Framework.

> [!NOTE]
> I progetti .NET **Core** non sono attualmente supportati.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* Assicurarsi di aver [configurato l'ambiente di sviluppo](service-fabric-mesh-howto-setup-developer-environment-sdk.md). A questo scopo occorre aver installato il runtime di Service Fabric, l'SDK, Docker e Visual Studio 2017 e aver creato un cluster locale.

## <a name="open-an-existing-net-app"></a>Aprire un'app .NET esistente

Aprire l'app a cui si vuole aggiungere il supporto dell'orchestrazione dei contenitori.

Se si vuole provare un esempio, è possibile usare il codice di esempio [eShop](https://github.com/MikkelHegn/ContainersSFLab). Il resto dell'articolo presuppone che si usi quel progetto, ma è possibile applicare queste procedure anche a un progetto personalizzato.

Creare una copia del progetto **eShop**:

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

Dopo averlo scaricato, in Visual Studio 2017 aprire **ContainersSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.sln**.

## <a name="add-container-support"></a>Aggiungere il supporto dei contenitori
 
Aggiungere il supporto dell'orchestrazione dei contenitori a un progetto ASP.NET o Console esistente usando Service Fabric Mesh con la procedura seguente:

In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto (nell'esempio **eShopLegacyWebForms**) e quindi scegliere **Aggiungi** > **Supporto per l'agente di orchestrazione del contenitore**.
Viene visualizzata la finestra di dialogo **Aggiungi supporto per l'agente di orchestrazione del contenitore**.

![Finestra di dialogo Aggiungi supporto per l'agente di orchestrazione del contenitore di Visual Studio](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

Selezionare **Service Fabric Mesh** dall'elenco a discesa e fare clic su **OK**.

Lo strumento verifica quindi che Docker sia installato, aggiunge un Dockerfile al progetto e scarica un'immagine Docker per il progetto.  
Alla soluzione viene aggiunto un progetto di applicazione Service Fabric Mesh. Contiene i profili di pubblicazione e i file di configurazione di Service Fabric Mesh. Il nome corrisponde al nome del progetto con "Application" concatenato alla fine, ad esempio **eShopLegacyWebFormsApplication**. 

Nel nuovo progetto Mesh sono presenti due cartelle che è opportuno conoscere:
- **App Resources** (Risorse dell'app), che contiene file YAML che descrivono risorse Mesh aggiuntive, come la rete.
- **Service Resources** (Risorse del servizio), che contiene un file service.yaml che descrive la modalità di esecuzione dell'app prevista alla distribuzione.

Dopo aver aggiunto il supporto dell'orchestrazione dei contenitori all'app, è possibile premere **F5** per eseguire il debug dell'app .NET nel cluster di Service Fabric Mesh locale. Di seguito è illustrata l'app ASP.NET eShop in esecuzione in un cluster di Service Fabric Mesh: 

![App eShop](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

A questo punto è possibile pubblicare l'app in Azure Service Fabric Mesh.

## <a name="next-steps"></a>Passaggi successivi

Vedere come pubblicare un'app nel mesh Service Fabric: [Esercitazione - Distribuire un'applicazione mesh Service Fabric](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)