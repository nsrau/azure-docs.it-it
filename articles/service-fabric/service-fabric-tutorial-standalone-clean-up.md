---
title: Esercitazione sulla pulizia del cluster autonomo di Service Fabric - Azure Service Fabric | Microsoft Docs
description: In questa esercitazione viene illustrato come pulire il cluster autonomo
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 67334a0e8ae3e6dcca86830cd088e6e446331aee
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306704"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Esercitazione: Eseguire la pulizia di un cluster autonomo

I cluster autonomi di Service Fabric offrono la possibilità di scegliere il proprio ambiente e creare un cluster come parte dell'approccio "qualsiasi sistema operativo, qualsiasi cloud" adottato da Service Fabric. In questa serie di esercitazioni viene creato un cluster autonomo ospitato in AWS e viene installata un'applicazione al suo interno.

Questa è la quarta di una serie di esercitazioni. Questa parte dell'esercitazione illustra come pulire le risorse AWS create per ospitare il cluster di Service Fabric.

Nella quarta parte della serie si apprenderà come:

> [!div class="checklist"]
> * Pulire un cluster di Service Fabric
> * Pulire le risorse AWS

## <a name="clean-up-service-fabric-cluster"></a>Pulire un cluster di Service Fabric

* Eseguire RDP nell'istanza EC2 usata per installare Service Fabric
* Aprire PowerShell
* Sostituire la directory con la cartella estratta nella seconda esercitazione.
* Per rimuovere le istanze di Service Fabric, eseguire il comando seguente:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

* `Y` quando richiesto; in caso di esito positivo l'output avrà un aspetto simile al seguente, con gli indirizzi IP sostituiti in:

```powershell
Best Practices Analyzer completed successfully.
Removing configuration from machine 172.31.21.141
Removing configuration from machine 172.31.27.1
Removing configuration from machine 172.31.20.163
Configuration removed from machine 172.31.21.141
Configuration removed from machine 172.31.27.1
Configuration removed from machine 172.31.20.163
The cluster is successfully removed.
```

## <a name="clean-up-aws-resources"></a>Pulire le risorse AWS

* Effettuare l'accesso all'account AWS
* Accedere alla Console EC2.
* Selezionare i tre nodi creati nella prima parte dell'esercitazione.
* Fare clic su **Actions** (Azioni) > **Instance State** (Stato istanza) > **Terminate** (Termina)

## <a name="next-steps"></a>Passaggi successivi

Nella quarta parte della serie è stato descritto come pulire le risorse create nei passaggi precedenti.

> [!div class="checklist"]
> * Pulire le risorse

> [!div class="nextstepaction"]
> [Torna all'inizio](service-fabric-tutorial-standalone-create-infrastructure.md)