---
title: Pulire un cluster autonomo
description: Questa esercitazione illustra come pulire le risorse di AWS o di Azure per il cluster autonomo di Service Fabric.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 0d46e9068a311594f779411c3ccee2b408febb3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842887"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Esercitazione: Pulire il cluster autonomo

I cluster autonomi di Service Fabric offrono la possibilità di scegliere un ambiente personalizzato in cui ospitare Service Fabric. In questa serie di esercitazioni verrà creato un cluster autonomo ospitato in AWS o in Azure e verrà distribuita un'applicazione al suo interno.

Questa è la quarta di una serie di esercitazioni. Questa parte dell'esercitazione illustra come pulire le risorse di AWS o di Azure create per ospitare il cluster di Service Fabric.

In questo articolo si apprenderà come:

> [!div class="checklist"]
> * Rimuovere un cluster di Service Fabric
> * Eliminare le risorse di AWS o di Azure

## <a name="remove-a-service-fabric-cluster"></a>Rimuovere un cluster di Service Fabric

1. Eseguire RDP nella macchina virtuale usata per installare Service Fabric.
2. Aprire PowerShell.
3. Sostituire la directory con la cartella estratta nella seconda esercitazione.
4. Per rimuovere le istanze di Service Fabric, eseguire il comando seguente:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Quando richiesto, immettere `Y`. In caso di esito positivo l'output sarà simile al seguente, con i propri indirizzi IP:

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

## <a name="delete-aws-resources"></a>Eliminare le risorse di AWS

1. Effettuare l'accesso all'account AWS.
2. Accedere alla Console EC2.
3. Selezionare i tre nodi creati nella prima parte dell'esercitazione.
4. Selezionare **Azioni** > **Stato istanza** > **Termina**.

## <a name="delete-azure-resources"></a>Eliminare le risorse di Azure

1. Accedere al portale di Azure.
2. Andare alla sezione **Macchine virtuali**.
3. Selezionare le caselle di spunta per i tre nodi creati nella prima parte dell'esercitazione.
4. Selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come eliminare le risorse create nei passaggi precedenti.

> [!div class="checklist"]
> * Pulire le risorse

> [!div class="nextstepaction"]
> [Torna all'inizio](service-fabric-tutorial-standalone-create-infrastructure.md)
