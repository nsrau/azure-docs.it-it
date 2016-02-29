<properties
   pageTitle="Gestire più ambienti nell'infrastruttura di servizi | Microsoft Azure"
   description="Le applicazioni di Service Fabric possono essere eseguite su cluster le cui dimensioni variano da un solo computer a molte migliaia. In alcuni casi è possibile che si voglia configurare l'applicazione in modo diverso per i diversi ambienti. Questo articolo illustra come definire diversi parametri dell'applicazione per ogni ambiente,"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/25/2015"
   ms.author="seanmck"/>

# Gestire i parametri dell'applicazione per più ambienti

I cluster di Azure Service Fabric possono essere creati usando un solo computer fino a molte migliaia. Anche se i file binari dell'applicazione possono essere eseguiti senza modifiche in questa varietà di ambienti, spesso è consigliabile configurare l'applicazione in modo diverso, in base al numero di computer in cui si esegue la distribuzione.

Come semplice esempio, si consideri `InstanceCount` per un servizio senza stato. Quando si eseguono applicazioni in Azure, in genere è consigliabile impostare questo parametro sul valore speciale "-1", per assicurare che il servizio sia eseguito in ogni nodo del cluster. Questa configurazione non è tuttavia appropriata per un cluster con un singolo computer, perché non è possibile avere più processi in ascolto sullo stesso endpoint in un singolo computer. In genere si imposta invece `InstanceCount` su 1.

## Definizione dei parametri specifici dell'ambiente

La soluzione per questo problema di configurazione è costituita da un set di servizi predefiniti con parametri e file di parametri dell'applicazione che compilano i valori dei parametri per un determinato ambiente.

### Servizi predefiniti

Le applicazioni di infrastruttura di servizi sono costituite da una raccolta di istanze del servizio. Anche se è possibile creare un'applicazione vuota e quindi tutte le istanze del servizio dinamicamente, la maggior parte delle applicazioni include un set di servizi di base che devono essere sempre creati quando si creano istanze dell'applicazione. Questi servizi sono detti "servizi predefiniti" e vengono specificati nel manifesto dell'applicazione, con i segnaposto per la configurazione specifica dei singoli ambienti racchiusi tra parentesi quadre:

    <DefaultServices>
        <Service Name="Stateful1">
            <StatefulService
                ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

                <UniformInt64Partition
                    PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808"
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

>[AZURE.NOTE] Non tutti i parametri di istanza del servizio sono idonei alla configurazione specifica per il singolo ambiente. Nell'esempio precedente i valori LowKey e HighKey per lo schema di partizionamento del servizio sono definiti in modo esplicito per tutte le istanze del servizio, perché l'intervallo di partizione è una funzione del dominio di dati, non dell'ambiente.


### Impostazioni della configurazione del servizio specifica per il singolo ambiente

Il [modello applicativo di Service Fabric](service-fabric-application-model.md) consente ai servizi di includere pacchetti di configurazione che contengono coppie chiave-valore personalizzate leggibili in fase di esecuzione. È anche possibile differenziare i valori di queste impostazioni in base all'ambiente, specificando un valore `ConfigOverride` nel manifesto dell'applicazione.

Si supponga che il file Config\\Settings.xml per il servizio `Stateful1` includa l'impostazione seguente:


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

>[AZURE.NOTE] Nel caso delle impostazioni di configurazione del servizio, è possibile impostare il valore di una chiave in tre posizioni, ovvero nel pacchetto di configurazione del servizio, nel manifesto dell'applicazione e nel file di parametri dell'applicazione. Service Fabric sceglierà sempre prima di tutto dal file di parametri dell'applicazione, se specificato, quindi dal manifesto dell'applicazione e infine dal pacchetto di configurazione.


### File di parametri dell'applicazione

Il progetto di applicazione di Service Fabric può includere uno o più file di parametri dell'applicazione, ognuno dei quali definisce i valori specifici per i parametri definiti nel manifesto dell'applicazione:

    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>

Per impostazione predefinita, una nuova applicazione include due file di parametri dell'applicazione, denominati Local.xml e Cloud.xml:

![File di parametri dell'applicazione in Esplora soluzioni][app-parameters-solution-explorer]

Per creare un nuovo file di parametri, è sufficiente copiarne e incollarne uno esistente e specificare un nuovo nome.

## Identificazione di parametri specifici per il singolo ambiente durante la distribuzione

In fase di distribuzione è necessario scegliere il file di parametri appropriato da usare con l'applicazione. È possibile eseguire questa operazione nella finestra di dialogo Pubblica in Visual Studio o in PowerShell.

### Eseguire una distribuzione da Visual Studio

Si può scegliere dall'elenco dei file di parametri disponibili quando si pubblica l'applicazione in Visual Studio.

![Scelta di un file di parametri nella finestra di dialogo Pubblica][publishdialog]

### Distribuire da PowerShell

Lo script di PowerShell `DeployCreate-FabricApplication.ps1` accetta un file di parametri come parametro.

    ./DeployCreate-FabricApplication -ApplicationPackagePath <app_package_path> -ApplicationDefinitionFilePath <app_instance_definition_path>

## Passaggi successivi

Per altre informazioni su alcuni concetti di base illustrati in questo argomento, vedere [Panoramica tecnica di Service Fabric](service-fabric-technical-overview.md). Per informazioni su altre funzionalità di gestione delle app disponibili in Visual Studio, vedere [Usare Visual Studio per semplificare la scrittura e la gestione delle applicazioni di Service Fabric](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]: ./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png

<!---HONumber=AcomDC_0218_2016-->