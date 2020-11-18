---
title: Distribuire il modello di Machine Learning in SQL Edge di Azure usando ONNX
description: Nella terza delle tre parti di questa esercitazione di SQL Edge di Azure sulla stima delle impurità del minerale di ferro si eseguiranno i modelli di Machine Learning ONNX in SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 9e5bb037b88b7c370e31d05c2d20fc6f558a8b39
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422196"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>Distribuire il modello di Machine Learning in SQL Edge di Azure usando ONNX 

Nella terza delle tre parti di questa esercitazione sulla stima delle impurità del minerale di ferro in SQL Edge di Azure si eseguiranno le operazioni seguenti:

1. Usare Azure Data Studio per connettersi al database SQL nell'istanza di SQL Edge di Azure.
2. Stimare le impurità del minerale di ferro con ONNX in SQL Edge di Azure.

## <a name="key-components"></a>Componenti chiave

1. La soluzione usa un valore predefinito di 500 millisecondi tra ogni messaggio inviato all'hub Edge. Tale valore può essere modificato nel file **Program.cs** 
   ```json
   TimeSpan messageDelay = configuration.GetValue("MessageDelay", TimeSpan.FromMilliseconds(500));
   ```
2. La soluzione ha generato un messaggio con gli attributi seguenti. Aggiungere o rimuovere gli attributi in base ai requisiti. 
```json
{
    timestamp 
    cur_Iron_Feed
    cur_Silica_Feed 
    cur_Starch_Flow 
    cur_Amina_Flow 
    cur_Ore_Pulp_pH
    cur_Flotation_Column_01_Air_Flow
    cur_Flotation_Column_02_Air_Flow
    cur_Flotation_Column_03_Air_Flow
    cur_Flotation_Column_04_Air_Flow
    cur_Flotation_Column_01_Level
    cur_Flotation_Column_02_Level
    cur_Flotation_Column_03_Level
    cur_Flotation_Column_04_Level
    cur_Iron_Concentrate
}
```

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance-to-train-deploy-and-test-the-ml-model"></a>Connettersi al database SQL nell'istanza di SQL Edge di Azure per il training, la distribuzione e il test del modello di Machine Learning

1. Aprire Azure Data Studio.

2. Nella scheda di **benvenuto** avviare una nuova connessione con i dettagli seguenti:

   |_Campo_|_Valore_|
   |-------|-------|
   |Tipo di connessione| Microsoft SQL Server|
   |Server|Indirizzo IP pubblico indicato nella macchina virtuale creata per questa demo|
   |Username|sa|
   |Password|Password complessa usata durante la creazione dell'istanza di SQL Edge di Azure|
   |Database|Predefinito|
   |Gruppo di server|Predefinito|
   |Nome (facoltativo)|Specificare un nome facoltativo|

3. Fare clic su **Connetti**

4. Nella sezione **File** aprire **/DeploymentScripts/MiningProcess_ONNX.jpynb** nella cartella in cui sono stati clonati i file di progetto nel computer.

5. Impostare il kernel su Python 3.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di modelli ONNX in SQL Edge di Azure, vedere [Machine Learning e IA con ONNX in SQL Edge](onnx-overview.md).