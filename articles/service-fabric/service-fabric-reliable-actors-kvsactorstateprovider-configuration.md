---
title: Modificare le impostazioni di KVSActorStateProvider nei microservizi di Azure | Documentazione Microsoft
description: Informazioni sulla configurazione di attori con stato di tipo KVSActorStateProvider in Service Fabric di Azure.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: 
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/29/2017
ms.author: sumukhs
ms.translationtype: Human Translation
ms.sourcegitcommit: f7edee399717ecb96fb920d0a938da551101c9e1
ms.openlocfilehash: 9610c37111bf8fd36c1eaea4f48e46953661aacf
ms.contentlocale: it-it
ms.lasthandoff: 01/24/2017


---
<a id="configuring-reliable-actors--kvsactorstateprovider" class="xliff"></a>
# Configurare KVSActorStateProvider di Reliable Actors
È possibile modificare la configurazione predefinita di KVSActorStateProvider cambiando il file settings.xml generato nella radice del pacchetto di Microsoft Visual Studio all'interno della cartella Config per l'attore specificato.

Il runtime di Service Fabric di Azure cerca i nomi di sezione predefiniti nel file settings.xml e utilizza i valori di configurazione durante la creazione dei componenti di runtime sottostanti.

> [!NOTE]
> **Non** eliminare o modificare i nomi di sezione delle configurazioni seguenti nel file settings.xml generato nella soluzione Visual Studio.
> 
> 

<a id="replicator-security-configuration" class="xliff"></a>
## Configurazione della sicurezza del replicatore
Le configurazioni della sicurezza del replicatore consentono di proteggere il canale di comunicazione usato durante la replica. Questo significa che i servizi non potranno visualizzare l'uno il traffico di replica dell'altro, garantendo la sicurezza dei dati a disponibilità elevata.
Per impostazione predefinita, una sezione di configurazione della sicurezza vuota non abilita la sicurezza della replica.

<a id="section-name" class="xliff"></a>
### Nome della sezione
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

<a id="replicator-configuration" class="xliff"></a>
## Configurazione del replicatore
Le configurazioni del replicatore riguardano il replicatore responsabile di garantire l'elevata affidabilità dello stato del provider di stato degli attori.
La configurazione predefinita viene generata dal modello di Visual Studio e dovrebbe essere sufficiente. Questa sezione descrive le configurazioni aggiuntive che sono disponibili per ottimizzare il replicatore.

<a id="section-name" class="xliff"></a>
### Nome della sezione
&lt;ActorName&gt;ServiceReplicatorConfig

<a id="configuration-names" class="xliff"></a>
### Nomi delle configurazioni
| Nome | Unità | Valore predefinito | Osservazioni |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Secondi |0,015 |Periodo di tempo per cui il replicatore, dopo aver ricevuto un'operazione, attende presso il replicatore secondario prima di inviare un acknowledgement al replicatore principale. Gli altri acknowledgement relativi alle operazioni elaborate all'interno di questo intervallo vengono inviati come risposta unica. |
| ReplicatorEndpoint |N/D |Nessun valore predefinito: parametro obbligatorio |Indirizzo IP e porta che il replicatore principale/secondario userà per comunicare con altri replicatori nel set di repliche. Deve fare riferimento a un endpoint di risorsa TCP nel manifesto del servizio. Per sapere di più sulla definizione delle risorse dell'endpoint nel manifesto del servizio, vedere [Specificare le risorse in un manifesto del servizio](service-fabric-service-manifest-resources.md) . |
| RetryInterval |Secondi |5 |Periodo di tempo dopo il quale il replicatore trasmette nuovamente un messaggio se non riceve l'acknowledgement di un'operazione. |
| MaxReplicationMessageSize |Byte |50 MB |Dimensione massima dei dati di replica che è possibile trasmettere in un singolo messaggio. |
| MaxPrimaryReplicationQueueSize |Numero di operazioni |1024 |Numero massimo di operazioni nella coda principale. Un'operazione viene liberata quando il replicatore principale riceve un acknowledgement da tutti i replicatori secondari. Questo valore deve essere maggiore di 64 ed essere una potenza di 2. |
| MaxSecondaryReplicationQueueSize |Numero di operazioni |2048 |Numero massimo di operazioni nella coda secondaria. Un'operazione viene liberata quando il relativo stato viene reso altamente disponibile tramite persistenza. Questo valore deve essere maggiore di 64 ed essere una potenza di 2. |

<a id="store-configuration" class="xliff"></a>
## Configurazione dell'archivio
Le configurazioni dell'archivio vengono usate per configurare l'archivio locale che consente di rendere persistente lo stato replicato.
La configurazione predefinita viene generata dal modello di Visual Studio e dovrebbe essere sufficiente. Questa sezione descrive le configurazioni aggiuntive che sono disponibili per ottimizzare l'archivio locale.

<a id="section-name" class="xliff"></a>
### Nome della sezione
&lt;ActorName&gt;ServiceLocalStoreConfig

<a id="configuration-names" class="xliff"></a>
### Nomi delle configurazioni
| Nome | Unità | Valore predefinito | Osservazioni |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |Millisecondi |200 |Intervallo massimo di invio in batch per i commit durevoli nell'archivio locale. |
| MaxVerPages |Numero di pagine |16384 |Numero massimo di pagine della versione nel database dell'archivio locale. Determina il numero massimo di transazioni in sospeso. |

<a id="sample-configuration-file" class="xliff"></a>
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
<a id="remarks" class="xliff"></a>
## Osservazioni
Il parametro BatchAcknowledgementInterval controlla la latenza di replica. Il valore '0' determina la latenza più bassa possibile a scapito della velocità effettiva, in quanto è necessario inviare ed elaborare una maggiore quantità di messaggi di acknowledgement, ciascuno dei quali contenente un numero minore di acknowledgement.
Più alto è il valore di BatchAcknowledgementInterval, maggiore sarà la velocità effettiva di replica complessiva, ma con una latenza delle operazioni più elevata. Questo ha un impatto diretto sulla latenza dei commit delle transazioni.


