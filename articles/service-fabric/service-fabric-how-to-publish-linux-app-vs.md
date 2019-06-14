---
title: Informazioni sulla creazione e pubblicazione di.NET Core alle applicazioni di un cluster Linux di Azure Service Fabric remoto | Microsoft Docs
description: Creare e pubblicare.NET Core le app destinate a un cluster Linux remoto da Visual Studio
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: 46d76edbe8cede12e8c7811f43c28a65c1ebaed0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078664"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Usare Visual Studio per creare e pubblicare.NET Core le applicazioni destinate a un cluster Linux di Service Fabric remoto
Con Visual Studio degli strumenti è possibile sviluppare e.NET Core di Service Fabric di pubblicare le applicazioni destinate a un cluster Linux di Service Fabric. La versione del SDK deve essere 3.4 o versione successiva per la distribuzione di.NET Core un'applicazione di destinazione Linux di Service Fabric cluster da Visual Studio.

> [!Note]
> Visual Studio non supporta il debug di applicazioni Service Fabric che hanno come destinazione Linux.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Creare un'applicazione di Service Fabric destinate a.NET Core
1. Avviare Visual Studio come **amministratore**.
2. Creare un progetto con **File -> Nuovo -> progetto**.
3. Nel **nuovo progetto** finestra di dialogo, scegliere **Cloud -> applicazione di Service Fabric**.
![create-application]
4. Denominare l'applicazione e fare clic su **accettabile**.
5. Nel **nuovo servizio Service Fabric** pagina, selezionare il tipo di servizio da creare con il **.NET Core sezione**.
![create-service]

## <a name="deploy-to-a-remote-linux-cluster"></a>Distribuire in un cluster Linux remoto
1. In Esplora soluzioni, fare clic sull'applicazione e selezionare **compilazione**.
![build-application]
2. Una volta completato il processo di compilazione per l'applicazione, fare clic con il pulsante destro sul servizio e selezionare Modifica il **file csproj**.
![edit-csproj]
3. Modificare la proprietà UpdateServiceFabricManifestEnabled da True a **False** se il servizio è un **tipo di progetto attore**. Se l'applicazione non dispone di un servizio actor, andare al passaggio 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> Impostazione UpdateServiceFabricManifestEnabled su false, verrà disabilitare gli aggiornamenti per il file servicemanifest. XML durante una compilazione. Tali modifiche come aggiungere, rimuovere o rinominare il servizio non si rifletteranno in servicemanifest. Xml. Se vengono apportate modifiche è necessario aggiornare che servicemanifest manualmente o temporaneamente impostato su true e compilare il servizio che consente di aggiornare il file servicemanifest. XML e quindi ripristinarla UpdateServiceFabricManifestEnabled eseguire il backup su false.
>

4. Aggiornare il RuntimeIndetifier da win7-x64 per la piattaforma di destinazione nel progetto di servizio.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. Nel file ServiceManifest, aggiornare il programma di punto di ingresso per rimuovere .exe. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. In Esplora soluzioni fare doppio clic sull'applicazione e selezionare **pubblica**. Viene visualizzata la finestra di dialogo **Pubblica**.
7. Nelle **Endpoint di connessione**, selezionare l'endpoint per i cluster Linux di Service Fabric remoto che si desidera di destinazione.
![publish-application]

<!--Image references-->
[create-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[create-service]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[build-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publish-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su [Introduzione a Service Fabric con.NET Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
