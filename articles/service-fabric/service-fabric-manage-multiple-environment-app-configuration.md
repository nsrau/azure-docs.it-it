<properties
   pageTitle="Gestire più ambienti nell'infrastruttura di servizi | Microsoft Azure"
   description="Le applicazioni di infrastruttura di servizi possono essere eseguite su cluster con dimensioni comprese tra un computer e migliaia di computer. In alcuni casi è possibile che si voglia configurare l'applicazione in modo diverso per i diversi ambienti. Questo articolo illustra come definire diversi parametri dell'applicazione per ogni ambiente,"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/25/2015"
   ms.author="seanmck"/>

# Gestione dei parametri dell'applicazione per più ambienti

I cluster di infrastruttura di servizi possono essere creati usando da uno a molte migliaia di computer. Anche se i file binari dell'applicazione possono essere eseguiti senza modifiche nei diversi ambienti disponibili, è spesso possibile che si voglia configurare l'applicazione in modo diverso, in base al numero di computer in cui si effettua la distribuzione.

Come semplice esempio, si consideri `InstanceCount` per un servizio senza stato. Durante l'esecuzione in Azure, si imposterà in genere questo parametro sul valore speciale -1, per assicurare che il servizio sia in esecuzione in ogni nodo del cluster. Questa configurazione non è appropriata per un cluster con un singolo computer, tuttavia, perché non è possibile avere più processi in attesa sullo stesso endpoint in un singolo computer. In genere si imposta invece `InstanceCount` su 1.

## Definizione dei parametri specifici dell'ambiente

La soluzione per questo problema consiste in un set di servizi predefiniti con parametri e di file di parametri dell'applicazione che compilano i valori relativi ai parametri per un determinato ambiente.

### Servizi predefiniti

Le applicazioni di infrastruttura di servizi sono costituite da una raccolta di istanze del servizio. Anche se è possibile creare un'applicazione vuota e quindi creare dinamicamente tutte le istanze del servizio, la maggior parte delle applicazioni ha un set di servizi di base che devono essere creati sempre quando si creano istanze dell'applicazione. Questi servizi sono definiti "servizi predefiniti" e vengono specificati nel manifesto dell'applicazione, con segnaposto relativi alla configurazione specifica per il singolo ambiente inclusi in parentesi supplementari:

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

>[AZURE.NOTE]Non tutti i parametri di istanza del servizio sono idonei alla configurazione specifica per il singolo ambiente. Nell'esempio precedente i valori LowKey e HighKey per lo schema di partizionamento del servizio vengono definiti in modo esplicito per tutte le istanze del servizio, perché l'intervallo di partizione è una funzione del dominio di dati, non dell'ambiente.


### Impostazioni della configurazione del servizio specifica per il singolo ambiente

Il [modello applicativo di infrastruttura di servizi](service-fabric-application-model.md) consente ai servizi di includere pacchetti di configurazione che contengono coppie di chiave-valore personalizzate, che possono essere lette in fase di esecuzione. È anche possibile differenziare i valori di queste impostazioni in base all'ambiente, specificando un valore `ConfigOverride` nel manifesto dell'applicazione.

Si supponga che il file Config\\Settings.xml per il servizio `Stateful1` includa l'impostazione seguente:


    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>

Per eseguire l'override di questo valore per una coppia specifica di applicazione/ambiente, creare un valore `ConfigOverride` durante l'importazione del manifesto del servizio nel manifesto dell'applicazione.

    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>

Questo parametro può essere configurato mediante l'ambiente, come illustrato in precedenza, mediante la dichiarazione nella sezione Parameters del manifesto dell'applicazione e mediante la definizione di valori specifici per il singolo ambiente nel file di parametri dell'applicazione.

>[AZURE.NOTE]Nel caso delle impostazioni di configurazione del servizio, è possibile impostare il valore di una chiave in tre posizioni, ovvero nel pacchetto di configurazione del servizio, nel manifesto dell'applicazione e nel file di parametri dell'applicazione. L'infrastruttura di servizi effettuerà sempre la scelta prima di tutto dal file di parametri dell'applicazione, quindi dal manifesto dell'applicazione e infine dal pacchetto di configurazione.


### File di parametri dell'applicazione

Il progetto di applicazione di infrastruttura di servizi può includere uno o più file di parametri dell'applicazione, ognuno dei quali definisce valori specifici per i parametri definiti nel manifesto dell'applicazione:

    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>

Per impostazione predefinita, una nuova applicazione include due file di parametri, denominati Local.xml e Cloud.xml:

![File di parametri dell'applicazione in Esplora soluzioni][app-parameters-solution-explorer]

Per creare un nuovo file di parametri, è sufficiente copiare e incollare un file esistente e specificare un nuovo nome.

## Identificazione di parametri specifici per il singolo ambiente durante la distribuzione

In fase di distribuzione è necessario scegliere il file di parametri appropriato da applicare con l'applicazione. È possibile eseguire questa operazione tramite la finestra di dialogo Pubblica in Visual Studio o in PowerShell.

### Distribuzione da Visual Studio

È possibile scegliere dall'elenco di file di parametri disponibili durante la pubblicazione dell'applicazione in Visual Studio.

![Scelta di un file di parametri nella finestra di dialogo Pubblica][publishdialog]

### Distribuzione da PowerShell

Lo script `DeployCreate-FabricApplication.ps1` di PowerShell accetta un file di parametri come parametro.

    ./DeployCreate-FabricApplication -ApplicationPackagePath <app_package_path> -ApplicationDefinitionFilePath <app_instance_definition_path>

## Passaggi successivi

Per altre informazioni su alcuni concetti fondamentali illustrati in questo argomento, vedere [Informazioni tecniche su Infrastruttura di servizi](service-fabric-technical-overview.md). Per informazioni su altre funzionalità di gestione delle app disponibili in Visual Studio, vedere [Usare Visual Studio per semplificare la scrittura e la gestione delle applicazioni dell’infrastruttura di servizi](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]: ./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png

<!---HONumber=AcomDC_1203_2015-->