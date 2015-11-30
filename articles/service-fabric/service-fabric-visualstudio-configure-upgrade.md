<properties
   pageTitle="Configurare l'aggiornamento di un'applicazione di Infrastruttura di servizi | Microsoft Azure"
   description="Informazioni su come configurare le impostazioni per l'aggiornamento di un'applicazione di Infrastruttura di servizi con Microsoft Visual Studio."
   services="service-fabric"
   documentationCenter="na"
   authors="cawaMS"
   manager="paulyuk"
   editor="tglee" />
<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="10/08/2015"
   ms.author="cawaMS" />

# Configurare l'aggiornamento di un'applicazione di Infrastruttura di servizi in Visual Studio

Gli strumenti di Infrastruttura di servizi di Visual Studio forniscono supporto per l'aggiornamento per la pubblicazione nei cluster locali o remoti. Esistono due vantaggi per l'aggiornamento dell'applicazione a una versione più recente invece di sostituire l'applicazione durante il test e il debug: in primo luogo i dati dell'applicazione non verranno persi durante l'aggiornamento, e in secondo luogo, viene raggiunta l’elevata disponibilità del sistema e non vi sarà nessuna interruzione del servizio durante l'aggiornamento se vi sono sufficienti istanze del servizio distribuite nei domini di aggiornamento. Durante l'aggiornamento, possono essere eseguiti test in un'applicazione.

## Parametri necessari per l'aggiornamento

Esistono due tipi di distribuzione tra cui è possibile scegliere: regolare o aggiornamento. Una distribuzione regolare cancella qualsiasi informazione di distribuzioni precedenti e i dati nel cluster, mentre una distribuzione di un aggiornamento li conserva. Quando si aggiorna un'applicazione di Infrastruttura di servizi in Visual Studio, è necessario fornire parametri di aggiornamento dell’applicazione e criteri per il controllo dell’integrità. I parametri di aggiornamento dell'applicazione consentono di controllare l'aggiornamento, mentre i criteri di controllo dell’integrità determinano se l'aggiornamento ha avuto o non esito positivo. Vedere [Aggiornamento di un'applicazione di Infrastruttura di servizi: parametri di aggiornamento](service-fabric-application-upgrade-parameters.md) per altri dettagli.

Sono disponibili tre modalità di aggiornamento: *Monitored*, *UnmonitoredAuto*, e *Manual*.

  - Un aggiornamento *Monitored* consente di automatizzare l’aggiornamento e il controllo dell’integrità dell’applicazione.

  - Un aggiornamento* UnmonitoredAuto* consente di automatizzare l'aggiornamento, ma ignora il controllo dell’integrità dell'applicazione.

  - Quando si esegue un aggiornamento *Manual*, è necessario aggiornare manualmente ogni dominio di aggiornamento.

Ogni modalità di aggiornamento richiede diversi set di parametri. Vedere [Parametri di aggiornamento dell'applicazione](service-fabric-application-upgrade-parameters.md) per ulteriori informazioni sulle opzioni di aggiornamento disponibili.

## Aggiornamento di un'applicazione di Infrastruttura di servizi in Visual Studio

Se si utilizza gli strumenti di Infrastruttura di servizi di Visual Studio per aggiornare un'applicazione di Infrastruttura di servizi, è possibile specificare un processo di pubblicazione per un aggiornamento anziché una distribuzione normale spuntando la casella di controllo **Aggiorna l'applicazione**.

### Per configurare i parametri di aggiornamento

1. Fare clic sul pulsante **Impostazioni** accanto alla casella di controllo. La finestra di dialogo **Modifica parametri di aggiornamento** verrà visualizzata. La finestra di dialogo **Modifica parametri di aggiornamento** supporta le modalità di aggiornamento *Monitored*, *UnmonitoredAuto* e *UnmonitoredManual* modalità di aggiornamento.

2. Selezionare la modalità di aggiornamento che si desidera utilizzare e quindi compilare la griglia dei parametri.

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

3. Se si seleziona la modalità di aggiornamento *UnmonitoredManual*, è necessario avviare manualmente una console di PowerShell per continuare e completare il processo di aggiornamento. Fare riferimento a [Aggiornamento di un'applicazione di infrastruttura di servizi: argomenti avanzati](service-fabric-application-upgrade-advanced.md) per informazioni sul funzionamento dell’aggiornamento manuale

## Aggiornare un'applicazione tramite PowerShell

È possibile utilizzare i cmdlet PowerShell per aggiornare un'applicazione di Infrastruttura di servizi. Vedere [Esercitazione sull'aggiornamento di un'applicazione di Infrastruttura di servizi](service-fabric-application-upgrade-tutorial.md) e [Start-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/mt125975.aspx) per informazioni dettagliate.

## Specificare un criterio di controllo dell’integrità nel file manifesto dell'applicazione

Ogni servizio in un'applicazione di Infrastruttura di servizi può disporre di propri parametri di criteri di integrità che sostituiscono i valori predefiniti. È possibile fornire questi valori di parametro nel file manifesto dell'applicazione.

Nell'esempio seguente viene illustrato come applicare un criterio di controllo di integrità univoco per ogni servizio nel manifesto dell'applicazione.

```
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
## Passaggi successivi
Per ulteriori informazioni sulla distribuzione di un'applicazione, vedere [Distribuire un'applicazione esistente in Infrastruttura di servizi di Azure](service-fabric-deploy-existing-app.md).

<!---HONumber=Nov15_HO4-->