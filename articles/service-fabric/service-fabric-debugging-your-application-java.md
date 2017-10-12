---
title: Eseguire il debug dell'applicazione Azure Service Fabric in Eclipse | Microsoft Docs
description: "Migliorare l'affidabilità e le prestazioni dei servizi sviluppandoli ed eseguendone il debug in Eclipse in un cluster di sviluppo locale."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2017
ms.author: vturecek;mikhegn
ms.openlocfilehash: f3bcee3794de35005bd387ecfae7e6707f3cb5ee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Eseguire il debug dell'applicazione Service Fabric di Java con Eclipse
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
> 

1. Avviare un cluster di sviluppo locale seguendo la procedura descritta nell'articolo [Configurazione dell'ambiente di sviluppo di Service Fabric](service-fabric-get-started-linux.md).

2. Aggiornare l'elemento entryPoint.sh del servizio di cui eseguire il debug in modo che avvii il processo Java con i parametri di debug remoto. Questo file si trova nel percorso seguente: ``ApplicationName\ServiceNamePkg\Code\entrypoint.sh``. In questo esempio la porta 8001 è impostata per il debug.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=y -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Aggiornare il manifesto dell'applicazione impostando il numero di istanze o di repliche per il servizio di cui eseguire il debug su 1. Questa impostazione evita che si verifichino conflitti per la porta usata per il debug. Per i servizi senza stato, ad esempio, impostare ``InstanceCount="1"``, mentre per i servizi con stato impostare la destinazione e le dimensioni minime del set di repliche su 1 nel modo seguente: `` TargetReplicaSetSize="1" MinReplicaSetSize="1"``.

4. Distribuire l'applicazione.

5. Nell'IDE di Eclipse selezionare **Run (Esegui) -> Debug Configurations (Configurazioni di debug) -> Remote Java Application (Applicazione Java remota) e le proprietà di connessione di input** e impostare le proprietà come segue:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Impostare punti di interruzione nei punti desiderati ed eseguire il debug dell'applicazione.

Se l'applicazione si arresta in modo anomalo, è possibile abilitare elementi core dump. Eseguire ``ulimit -c`` in una shell e, se viene restituito 0, gli elementi core dump non sono abilitati. Per abilitare elementi core dump illimitati, eseguire questo comando: ``ulimit -c unlimited``. È anche possibile verificare lo stato usando il comando ``ulimit -a``.  Se si vuole aggiornare il percorso di generazione degli elementi core dump, eseguire ``echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern``. 

### <a name="next-steps"></a>Passaggi successivi

* [Raccogliere log con Diagnostica di Azure](service-fabric-diagnostics-how-to-setup-lad.md).
* [Monitorare e diagnosticare servizi in una configurazione di sviluppo con computer locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).
