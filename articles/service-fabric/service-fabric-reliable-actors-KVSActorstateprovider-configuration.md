<properties
   pageTitle="Panoramica della configurazione di KVSActorStateProvider per Reliable Actors di Service Fabric"
   description="Informazioni sulla configurazione di attori con stato di tipo KVSActorStateProvider in Service Fabric"
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/20/2015"
   ms.author="sumukhs"/>

# Configurazione di KVSActorStateProvider di Reliable Actors
È possibile modificare la configurazione predefinita di KVSActorStateProvider cambiando il file "settings.xml" generato nella directory radice del pacchetto di Visual Studio all'interno della cartella "Config" per l'attore specificato.

Il runtime di Service Fabric cerca i nomi di sezione predefiniti nel file "settings.xml" e utilizza i valori di configurazione durante la creazione dei componenti di runtime sottostanti.

> [AZURE.NOTE]**NON** eliminare/modificare i nomi di sezione delle configurazioni seguenti nel file "settings.xml" generato nella soluzione Visual Studio.

## Configurazione della sicurezza del replicatore
Le configurazioni della sicurezza del replicatore consentono di proteggere il canale di comunicazione usato durante la replica. Questo significa che i servizi non saranno in grado di vedere l'uno il traffico di replica dell'altro, garantendo la sicurezza dei dati a disponibilità elevata. Per impostazione predefinita, una sezione di configurazione della sicurezza vuota non abilita la sicurezza della replica.
### Nome della sezione
&lt;NomeAttore&gt;ServiceReplicatorSecurityConfig
### Nomi delle configurazioni
Fare riferimento all'articolo sulla [sicurezza della replica](../service-fabric/service-fabric-replication-security.md).

## Configurazione del replicatore
Le configurazioni del replicatore vengono usate per configurare il servizio di replica responsabile di garantire l'elevata affidabilità dello stato del provider di stato degli attori. La configurazione predefinita viene generata dal modello di Visual Studio e dovrebbe essere sufficiente. Questa sezione descrive le configurazioni aggiuntive che sono disponibili per ottimizzare il replicatore.
### Nome della sezione
&lt;NomeAttore&gt;ServiceReplicatorConfig
### Nomi delle configurazioni

|Nome|Unità|Valore predefinito|Osservazioni|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Secondi|0,05|Periodo di tempo per cui il replicatore, dopo aver ricevuto un'operazione, attende presso il replicatore secondario prima di inviare un acknowledgement al replicatore principale. Gli altri acknowledgement relativi alle operazioni elaborate all'interno di questo intervallo vengono inviati come risposta unica.|
|ReplicatorEndpoint|N/D|N/D - RequiredParameter|Indirizzo IP e porta che il replicatore principale/secondario userà per comunicare con altri replicatori nel set di repliche. Questo dovrebbe fare riferimento a un endpoint di risorsa TCP nel manifesto del servizio. Per altre informazioni sulla definizione delle risorse dell'endpoint nel manifesto del servizio, vedere l'articolo relativo alle [risorse del manifesto del servizio](service-fabric-service-manifest-resources.md). |
|RetryInterval|Secondi|5|Periodo di tempo dopo il quale il replicatore trasmette nuovamente un messaggio se non riceve l'acknowledgement di un'operazione.|
|MaxReplicationMessageSize|Byte|50 MB|Dimensione massima dei dati di replica che è possibile trasmettere in un singolo messaggio.|
|MaxPrimaryReplicationQueueSize|Numero di operazioni|1024|Numero massimo di operazioni nella coda principale. Un'operazione viene liberata dopo che il replicatore principale riceve un acknowledgement da tutti i replicatori secondari. Questo valore deve essere maggiore di 64 ed essere una potenza di 2.|
|MaxSecondaryReplicationQueueSize|Numero di operazioni|2048|Numero massimo di operazioni nella coda secondaria. Un'operazione viene liberata dopo che il relativo stato è stato reso altamente disponibile mediante la persistenza. Questo valore deve essere maggiore di 64 ed essere una potenza di 2.|

## Configurazione dell'archivio
Le configurazioni dell'archivio vengono usate per configurare l'archivio locale che consente di rendere persistente lo stato replicato. La configurazione predefinita viene generata dal modello di Visual Studio e dovrebbe essere sufficiente. Questa sezione descrive le configurazioni aggiuntive che sono disponibili per ottimizzare l'archivio locale.
### Nome della sezione
&lt;NomeAttore&gt;ServiceLocalStoreConfig
### Nomi delle configurazioni

|Nome|Unità|Valore predefinito|Osservazioni|
|----|----|-------------|-------|
|MaxAsyncCommitDelay|Millisecondi|200|Intervallo massimo di invio in batch per i commit durevoli nell'archivio locale.|
|MaxVerPages|Numero di pagine|8192|Numero massimo di pagine della versione nel database dell'archivio locale. Determina il numero massimo di transazioni in sospeso.|

## File di configurazione di esempio

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```
## Osservazioni

BatchAcknowledgementInterval controlla la latenza di replica. Il valore '0' determina la latenza più bassa possibile a scapito della velocità effettiva, in quanto è necessario inviare ed elaborare una maggiore quantità di messaggi di acknowledgement, ciascuno dei quali contenente un numero minore di acknowledgement. Più alto è il valore di BatchAcknowledgementInterval, maggiore sarà la velocità effettiva di replica complessiva, ma con una latenza delle operazioni più elevata. Questo ha un impatto diretto sulla latenza dei commit delle transazioni.
 

<!---HONumber=August15_HO6-->