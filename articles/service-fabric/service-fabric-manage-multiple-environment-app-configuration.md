---
title: "Gestire più ambienti in Service Fabric | Microsoft Docs"
description: "Le applicazioni di Service Fabric possono essere eseguite su cluster le cui dimensioni variano da un solo computer a molte migliaia. In alcuni casi è possibile che si voglia configurare l&quot;applicazione in modo diverso per i diversi ambienti. Questo articolo illustra come definire diversi parametri dell&quot;applicazione per ogni ambiente,"
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8cbeaaf6bfba41bbf52f26aa0e408b7b1ac9405e


---
# <a name="manage-application-parameters-for-multiple-environments"></a>Gestire i parametri dell'applicazione per più ambienti
I cluster di Azure Service Fabric possono essere creati usando un solo computer fino a molte migliaia. Anche se i file binari dell'applicazione possono essere eseguiti senza modifiche in questa varietà di ambienti, spesso è consigliabile configurare l'applicazione in modo diverso, in base al numero di computer in cui si esegue la distribuzione.

Ad esempio, considerare `InstanceCount` per un servizio senza stato. Quando si eseguono applicazioni in Azure, in genere è consigliabile impostare questo parametro sul valore speciale "-1", In questo modo si garantisce che il servizio sia in esecuzione in ogni nodo del cluster (o in ogni nodo nel tipo di nodo se è stato impostato un vincolo di posizionamento). Questa configurazione non è tuttavia appropriata per un cluster con un singolo computer, perché non è possibile avere più processi in ascolto sullo stesso endpoint in un singolo computer. Al contrario, `InstanceCount` verrà in genere impostato su "1".

## <a name="specifying-environment-specific-parameters"></a>Definizione dei parametri specifici dell'ambiente
La soluzione per questo problema di configurazione è costituita da un set di servizi predefiniti con parametri e file di parametri dell'applicazione che compilano i valori dei parametri per un determinato ambiente. I parametri predefiniti per servizi e applicazioni vengono configurati all'interno dei manifesti dei servizi e delle applicazioni. La definizione dello schema per i file ServiceManifest.xml e ApplicationManifest.xml viene installata con gli strumenti e l'SDK di Service Fabric in *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

### <a name="default-services"></a>Servizi predefiniti
Le applicazioni di infrastruttura di servizi sono costituite da una raccolta di istanze del servizio. Anche se è possibile creare un'applicazione vuota e quindi tutte le istanze del servizio dinamicamente, la maggior parte delle applicazioni include un set di servizi di base che devono essere sempre creati quando si creano istanze dell'applicazione. Questi servizi sono detti "servizi predefiniti" e vengono specificati nel manifesto dell'applicazione, con i segnaposto per la configurazione specifica dei singoli ambienti racchiusi tra parentesi quadre:

    <DefaultServices>
        <Service Name="Stateful1">
            <StatefulService
                ServiceTypeName="Stateful1Type"
                TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]"
                MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

                <UniformInt64Partition
                    PartitionCount="[Stateful1_PartitionCount]"
                    LowKey="-9223372036854775808"
                    HighKey="9223372036854775807"
                />
        </StatefulService>
    </Service>
  </DefaultServices>

Ogni parametro denominato deve essere definito nell'elemento Parameters del manifesto dell'applicazione:

    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>

L'attributo DefaultValue specifica il valore da usare in assenza di un parametro più specifico per un determinato ambiente.

> [!NOTE]
> Non tutti i parametri di istanza del servizio sono idonei alla configurazione specifica per il singolo ambiente. Nell'esempio precedente i valori LowKey e HighKey per lo schema di partizionamento del servizio sono definiti in modo esplicito per tutte le istanze del servizio, perché l'intervallo di partizione è una funzione del dominio di dati, non dell'ambiente.
> 
> 

### <a name="per-environment-service-configuration-settings"></a>Impostazioni della configurazione del servizio specifica per il singolo ambiente
Il [modello applicativo di Service Fabric](service-fabric-application-model.md) consente ai servizi di includere pacchetti di configurazione che contengono coppie chiave-valore personalizzate leggibili in fase di esecuzione. È anche possibile differenziare i valori di queste impostazioni in base all'ambiente, specificando un valore `ConfigOverride` nel manifesto dell'applicazione.

Si supponga di avere la seguente impostazione nel file Config\Settings.xml per il servizio `Stateful1`:

    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>

Per eseguire l'override di questo valore per una coppia applicazione/ambiente specifica, creare un valore `ConfigOverride` durante l'importazione del manifesto del servizio nel manifesto dell'applicazione.

    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>

Questo parametro può quindi essere configurato dall'ambiente, come illustrato in precedenza, dichiarandolo nella sezione Parameters del manifesto dell'applicazione e definendo i valori specifici per il singolo ambiente nel file di parametri dell'applicazione.

> [!NOTE]
> Nel caso delle impostazioni di configurazione del servizio, è possibile impostare il valore di una chiave in tre posizioni, ovvero nel pacchetto di configurazione del servizio, nel manifesto dell'applicazione e nel file di parametri dell'applicazione. Service Fabric sceglierà sempre prima di tutto dal file di parametri dell'applicazione, se specificato, quindi dal manifesto dell'applicazione e infine dal pacchetto di configurazione.
> 
> 

### <a name="application-parameter-files"></a>File di parametri dell'applicazione
Il progetto di applicazione di Service Fabric può includere uno o più file di parametri dell'applicazione, ognuno dei quali definisce i valori specifici per i parametri definiti nel manifesto dell'applicazione:

    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>

Per impostazione predefinita, una nuova applicazione include tre file di parametro dell'applicazione, denominati Local.1Node.xml, Local.5Node.xml e Cloud.xml:

![File di parametri dell'applicazione in Esplora soluzioni][app-parameters-solution-explorer]

Per creare un nuovo file di parametri, è sufficiente copiarne e incollarne uno esistente e specificare un nuovo nome.

## <a name="identifying-environment-specific-parameters-during-deployment"></a>Identificazione di parametri specifici per il singolo ambiente durante la distribuzione
In fase di distribuzione è necessario scegliere il file di parametri appropriato da usare con l'applicazione. È possibile eseguire questa operazione nella finestra di dialogo Pubblica in Visual Studio o in PowerShell.

### <a name="deploy-from-visual-studio"></a>Eseguire una distribuzione da Visual Studio
Si può scegliere dall'elenco dei file di parametri disponibili quando si pubblica l'applicazione in Visual Studio.

![Scelta di un file di parametri nella finestra di dialogo Pubblica][publishdialog]

### <a name="deploy-from-powershell"></a>Distribuire da PowerShell
Lo script `Deploy-FabricApplication.ps1` di PowerShell incluso nel modello del progetto dell'applicazione accetta un profilo di pubblicazione come parametro e PublishProfile contiene un riferimento al file dei parametri dell'applicazione.

  ```PowerShell
    ./Deploy-FabricApplication -ApplicationPackagePath <app_package_path> -PublishProfileFile <publishprofile_path>
  ```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su alcuni concetti di base illustrati in questo argomento, vedere [Panoramica della terminologia di Service Fabric](service-fabric-technical-overview.md). Per informazioni su altre funzionalità di gestione delle app disponibili in Visual Studio, vedere [Usare Visual Studio per semplificare la scrittura e la gestione delle applicazioni di Service Fabric](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png



<!--HONumber=Nov16_HO3-->


