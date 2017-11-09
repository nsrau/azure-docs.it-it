---
title: "Gestire più ambienti in Service Fabric | Microsoft Docs"
description: "Le applicazioni di Service Fabric possono essere eseguite su cluster le cui dimensioni variano da un solo computer a molte migliaia. In alcuni casi è possibile che si voglia configurare l'applicazione in modo diverso per i diversi ambienti. Questo articolo illustra come definire diversi parametri dell'applicazione per ogni ambiente,"
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: mikkelhegn
ms.openlocfilehash: 671cc9b0f7b7b37fcf5b052f7e34bc98e66b2838
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-application-parameters-for-multiple-environments"></a>Gestire i parametri dell'applicazione per più ambienti
I cluster di Azure Service Fabric possono essere creati usando un solo computer fino a molte migliaia. Anche se i file binari dell'applicazione possono essere eseguiti senza modifiche in questa ampia gamma di ambienti, spesso è consigliabile configurare l'applicazione in modo diverso, in base al numero di computer in cui si esegue la distribuzione.

Ad esempio, considerare `InstanceCount` per un servizio senza stato. Quando si eseguono applicazioni in Azure, in genere è consigliabile impostare questo parametro sul valore speciale "-1". Questa configurazione garantisce che il servizio sia in esecuzione in ogni nodo del cluster (o in ogni nodo nel tipo di nodo se è stato impostato un vincolo di posizionamento). Non è tuttavia appropriata per un cluster con un singolo computer, perché non è possibile avere più processi in ascolto sullo stesso endpoint in un singolo computer. Al contrario, `InstanceCount` viene in genere impostato su "1".

## <a name="specifying-environment-specific-parameters"></a>Definizione dei parametri specifici dell'ambiente
La soluzione per questo problema di configurazione è costituita da un set di servizi predefiniti con parametri e file di parametri dell'applicazione che compilano i valori dei parametri per un determinato ambiente. I parametri predefiniti per servizi e applicazioni vengono configurati all'interno dei manifesti dei servizi e delle applicazioni. La definizione dello schema per i file ServiceManifest.xml e ApplicationManifest.xml viene installata con gli strumenti e l'SDK di Service Fabric in *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

### <a name="default-services"></a>Servizi predefiniti
Le applicazioni di infrastruttura di servizi sono costituite da una raccolta di istanze del servizio. Anche se è possibile creare un'applicazione vuota e quindi tutte le istanze del servizio dinamicamente, la maggior parte delle applicazioni include un set di servizi di base che devono essere sempre creati quando si creano istanze dell'applicazione. Questi servizi sono detti "servizi predefiniti" e vengono specificati nel manifesto dell'applicazione, con i segnaposto per la configurazione specifica dei singoli ambienti racchiusi tra parentesi quadre:

```xml
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
```

Ogni parametro denominato deve essere definito nell'elemento Parameters del manifesto dell'applicazione:

```xml
    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>
```

L'attributo DefaultValue specifica il valore da usare in assenza di un parametro più specifico per un determinato ambiente.

> [!NOTE]
> Non tutti i parametri di istanza del servizio sono idonei alla configurazione specifica per il singolo ambiente. Nell'esempio precedente i valori LowKey e HighKey per lo schema di partizionamento del servizio sono definiti in modo esplicito per tutte le istanze del servizio, perché l'intervallo di partizione è una funzione del dominio di dati, non dell'ambiente.
> 
> 

### <a name="per-environment-service-configuration-settings"></a>Impostazioni della configurazione del servizio specifica per il singolo ambiente
Il [modello applicativo di Service Fabric](service-fabric-application-model.md) consente ai servizi di includere pacchetti di configurazione che contengono coppie chiave-valore personalizzate leggibili in fase di esecuzione. È anche possibile differenziare i valori di queste impostazioni in base all'ambiente, specificando un valore `ConfigOverride` nel manifesto dell'applicazione.

Si supponga di avere la seguente impostazione nel file Config\Settings.xml per il servizio `Stateful1`:

```xml
  <Section Name="MyConfigSection">
     <Parameter Name="MaxQueueSize" Value="25" />
  </Section>
```
Per eseguire l'override di questo valore per una coppia applicazione/ambiente specifica, creare un valore `ConfigOverride` durante l'importazione del manifesto del servizio nel manifesto dell'applicazione.

```xml
  <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>
```
Questo parametro può quindi essere configurato dall'ambiente, come illustrato in precedenza, dichiarandolo nella sezione Parameters del manifesto dell'applicazione e definendo i valori specifici per il singolo ambiente nel file di parametri dell'applicazione.

> [!NOTE]
> Nel caso delle impostazioni di configurazione del servizio, è possibile impostare il valore di una chiave in tre posizioni, ovvero nel pacchetto di configurazione del servizio, nel manifesto dell'applicazione e nel file di parametri dell'applicazione. Service Fabric sceglierà sempre prima di tutto dal file di parametri dell'applicazione, se specificato, quindi dal manifesto dell'applicazione e infine dal pacchetto di configurazione.
> 
> 

### <a name="setting-and-using-environment-variables"></a>Impostazione e uso delle variabili di ambiente 
È possibile specificare e impostare le variabili di ambiente nel file ServiceManifest.xml e quindi sostituire tali impostazioni nel file ApplicationManifest.xml per ogni istanza.
L'esempio seguente illustra due variabili di ambiente, una con un valore impostato e l'altra sostituita. È possibile usare i parametri dell'applicazione per impostare i valori delle variabili di ambiente in modo analogo a come verrebbero usati per le sostituzioni di configurazione.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
    <EnvironmentVariables>
      <EnvironmentVariable Name="MyEnvVariable" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentVariables>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
```
Per sostituire le variabili di ambiente nel file ApplicationManifest.xml, fare riferimento al pacchetto di codice in ServiceManifest con l'elemento `EnvironmentOverrides`.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="MyCode">
      <EnvironmentVariable Name="MyEnvVariable" Value="mydata"/>
    </EnvironmentOverrides>
  </ServiceManifestImport>
 ``` 
 Dopo aver creato l'istanza del servizio denominato, è possibile accedere alle variabili di ambiente dal codice. In C# è possibile ad esempio eseguire queste operazioni

```csharp
    string EnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

### <a name="service-fabric-environment-variables"></a>Variabili di ambiente di Service Fabric
Service Fabric dispone di variabili di ambiente predefinite, impostate per ciascuna istanza di servizio. Di seguito viene riportato l'elenco completo delle variabili di ambiente. Quelle in grassetto verranno usate nel servizio, le altre sono usate dal runtime di Service Fabric. 

* Fabric_ApplicationHostId
* Fabric_ApplicationHostType
* Fabric_ApplicationId
* **Fabric_ApplicationName**
* Fabric_CodePackageInstanceId
* **Fabric_CodePackageName**
* **Fabric_Endpoint_[NomeDelServizio]TypeEndpoint**
* **Fabric_Folder_App_Log**
* **Fabric_Folder_App_Temp**
* **Fabric_Folder_App_Work**
* **Fabric_Folder_Application**
* Fabric_NodeId
* **Fabric_NodeIPOrFQDN**
* **Fabric_NodeName**
* Fabric_RuntimeConnectionAddress
* Fabric_ServicePackageInstanceId
* Fabric_ServicePackageName
* Fabric_ServicePackageVersionInstance
* FabricPackageFileName

Il codice seguente mostra come elencare le variabili di ambiente di Service Fabric
 ```csharp
    foreach (DictionaryEntry de in Environment.GetEnvironmentVariables())
    {
        if (de.Key.ToString().StartsWith("Fabric"))
        {
            Console.WriteLine(" Environment variable {0} = {1}", de.Key, de.Value);
        }
    }
```
Di seguito sono riportati esempi di variabili di ambiente per un tipo di applicazione chiamato `GuestExe.Application` con un tipo di servizio denominato `FrontEndService`, in esecuzione sul computer di sviluppo locale.

* **Fabric_ApplicationName = fabric:/GuestExe.Application**
* **Fabric_CodePackageName = Code**
* **Fabric_Endpoint_FrontEndServiceTypeEndpoint = 80**
* **Fabric_NodeIPOrFQDN = localhost**
* **Fabric_NodeName = _Node_2**

### <a name="application-parameter-files"></a>File di parametri dell'applicazione
Il progetto di applicazione di Service Fabric può includere uno o più file di parametri dell'applicazione, ognuno dei quali definisce i valori specifici per i parametri definiti nel manifesto dell'applicazione:

```xml
    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="3" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>
```
Per impostazione predefinita, una nuova applicazione include tre file di parametro dell'applicazione, denominati Local.1Node.xml, Local.5Node.xml e Cloud.xml:

![File di parametri dell'applicazione in Esplora soluzioni][app-parameters-solution-explorer]

Per creare un file di parametri, è sufficiente copiarne e incollarne uno esistente e specificare un nuovo nome.

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
