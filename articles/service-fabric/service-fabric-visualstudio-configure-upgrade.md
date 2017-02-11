---
title: Configurare l&quot;aggiornamento di un&quot;applicazione di Service Fabric | Documentazione Microsoft
description: Informazioni su come configurare le impostazioni per l&quot;aggiornamento di un&quot;applicazione di Infrastruttura di servizi con Microsoft Visual Studio.
services: service-fabric
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: tglee
ms.assetid: 1757ba85-0b7b-4f16-8a23-2ddaa61c86c6
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/18/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 121f1d485e34ba30d3e6c2e5d91da633771e80ab


---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Configurare l'aggiornamento di un'applicazione di Infrastruttura di servizi in Visual Studio
Gli strumenti di Visual Studio per Service Fabric di Azure forniscono supporto per l'aggiornamento per la pubblicazione nei cluster locali o remoti. Esistono due vantaggi per l'aggiornamento dell'applicazione a una versione più recente invece di sostituire l'applicazione durante il test e il debug:

* I dati dell'applicazione non vengono persi durante l'aggiornamento.
* Si garantisce una disponibilità elevata continua per evitare ogni tipo di interruzione del servizio durante l'aggiornamento in presenza di sufficienti istanze del servizio distribuite nei domini di aggiornamento.

Durante l'aggiornamento, possono essere eseguiti test in un'applicazione.

## <a name="parameters-needed-to-upgrade"></a>Parametri necessari per l'aggiornamento
È possibile scegliere tra due tipi di distribuzione: normale o aggiornata. Una distribuzione regolare cancella qualsiasi informazione di distribuzioni precedenti e i dati nel cluster, mentre una distribuzione di un aggiornamento li conserva. Quando si aggiorna un'applicazione di Infrastruttura di servizi in Visual Studio, è necessario fornire parametri di aggiornamento dell’applicazione e criteri per il controllo dell’integrità. I parametri di aggiornamento dell'applicazione consentono di controllare l'aggiornamento, mentre i criteri di controllo dell'integrità determinano se l'aggiornamento è avvenuto correttamente. Vedere l'articolo relativo all' [aggiornamento di un'applicazione di Service Fabric e i parametri di aggiornamento](service-fabric-application-upgrade-parameters.md) per ulteriori dettagli.

Sono disponibili tre modalità di aggiornamento: *Monitored*, *UnmonitoredAuto* e *UnmonitoredManual*.

* Un aggiornamento Monitored consente di automatizzare l'aggiornamento e il controllo dell'integrità dell'applicazione.
* Un aggiornamento UnmonitoredAuto consente di automatizzare l'aggiornamento, ma ignorando il controllo dell'integrità dell'applicazione.
* Quando si esegue un aggiornamento UnmonitoredManual è necessario aggiornare manualmente ogni dominio di aggiornamento.

Ogni modalità di aggiornamento richiede diversi set di parametri. Vedere [Parametri di aggiornamento di un'applicazione](service-fabric-application-upgrade-parameters.md) per conoscere le opzioni di aggiornamento disponibili.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Aggiornamento di un'applicazione di Infrastruttura di servizi in Visual Studio
Se si utilizzano gli strumenti di Service Fabric di Visual Studio per aggiornare un'applicazione di Service Fabric, è possibile specificare un processo di pubblicazione come aggiornamento anziché distribuzione normale selezionando la casella di controllo **Aggiorna applicazione** .

### <a name="to-configure-the-upgrade-parameters"></a>Per configurare i parametri di aggiornamento
1. Fare clic sul pulsante **Impostazioni** accanto alla casella di controllo. La finestra di dialogo **Edit Upgrade Parameters** verrà visualizzata. La finestra di dialogo **Edit Upgrade Parameters** supporta le modalità di aggiornamento Monitored, UnmonitoredAuto e UnmonitoredManual.
2. Selezionare la modalità di aggiornamento che si desidera usare, quindi compilare la griglia dei parametri.

    Ogni parametro ha valori predefiniti. Il parametro facoltativo *DefaultServiceTypeHealthPolicy* accetta un input di tabella hash. Di seguito è riportato un esempio del formato di input di tabella hash per *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* è un altro parametro facoltativo che accetta un input di tabella hash nel formato seguente:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Ecco un esempio reale:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Se si seleziona la modalità di aggiornamento UnmonitoredManual, è necessario avviare manualmente una console di PowerShell per continuare il processo di aggiornamento e completarlo. Fare riferimento a [Aggiornamento di un'applicazione di infrastruttura di servizi: argomenti avanzati](service-fabric-application-upgrade-advanced.md) per scoprire come funziona l'aggiornamento manuale.

## <a name="upgrade-an-application-by-using-powershell"></a>Aggiornare un'applicazione con PowerShell
È possibile utilizzare i cmdlet PowerShell per aggiornare un'applicazione di Infrastruttura di servizi. Per informazioni dettagliate, vedere [Esercitazione sull'aggiornamento di un'applicazione di Service Fabric tramite Visual Studio](service-fabric-application-upgrade-tutorial.md) e [Start-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/mt125975.aspx).

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Specificare un criterio di controllo dell'integrità nel file manifesto dell'applicazione
Ogni servizio in un'applicazione di Infrastruttura di servizi può disporre di propri parametri di criteri di integrità che sostituiscono i valori predefiniti. È possibile fornire questi valori di parametro nel file manifesto dell'applicazione.

Nell'esempio seguente viene illustrato come applicare un criterio di controllo di integrità univoco per ogni servizio nel manifesto dell'applicazione.

```xml
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla distribuzione di un'applicazione, vedere [Distribuire un'applicazione esistente in Infrastruttura di servizi di Azure](service-fabric-deploy-existing-app.md).



<!--HONumber=Nov16_HO3-->


