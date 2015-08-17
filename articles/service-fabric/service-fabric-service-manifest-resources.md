<properties
   pageTitle="Risorse del manifesto del servizio di Infrastruttura di servizi"
   description="Come descrivere le risorse in un manifesto del servizio"
   services="service-fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/23/2015"
   ms.author="sumukhs"/>

# Risorse del manifesto del servizio

## Panoramica

Il manifesto del servizio consente alle risorse di essere utilizzate dal servizio per essere dichiarato/modificato senza modificare il codice compilato. L'architettura di servizi supporta la configurazione delle risorse dell'endpoint del servizio. È possibile controllare l'accesso alle risorse specificate nel manifesto del servizio tramite SecurityGroup nel manifesto dell'applicazione. La dichiarazione delle risorse consente a queste risorse essere modificate in fase di distribuzione tempo invece di richiedere al servizio di introdurre un nuovo meccanismo di configurazione.

## Endpoint

Quando una risorsa dell'endpoint viene definita nel manifesto del servizio, Infrastruttura di servizi assegna le porte dall'intervallo di porte riservate dell'applicazione. Inoltre, i servizi possono richiedere anche una porta specifica in una risorsa. Alle repliche del servizio in esecuzione sui diversi nodi del cluster possono essere assegnati diversi numeri di porta, mentre le repliche dello stesso servizio in esecuzione nello stesso nodo condividono la stessa porta. Tali porte possono essere utilizzate per le repliche del servizio per vari scopi, come la replica, l'attesa delle richieste del client e così via.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint" Protocol="http"/>
    <Endpoint Name="ServiceInputEndpoint" Protocol="http" Port="80"/>
    <Endpoint Name="ReplicatorEndpoint" Protocol="tcp"/>
  </Endpoints>
</Resources>
```

Fare riferimento alla [configurazione dei servizi affidabili con stato](../Service-Fabric/service-fabric-reliable-services-configuration.md) per informazioni sugli endpoint di riferimento dal file di impostazioni del pacchetto di configurazione (settings.xml).

## Esempio
Il seguente manifesto del servizio definisce 1 risorsa di endpoint TCP e 2 risorse di endpoint HTTP nell'elemento &lt;Resources&gt;.

Gli endpoint HTTP vengono automaticamente inseriti nell'elenco di controllo di accesso da Infrastruttura di servizi.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="SP1" Version="V1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Description>Test Service</Description>
  <ServiceTypes>
    <StatefulServiceType ServiceTypeName="PersistType" HasPersistedState="true" />
  </ServiceTypes>
  <CodePackage Name="CP1" Version="V1">
    <EntryPoint>
      <ExeHost>
        <Program>CB\Code.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="CP1.Config0" Version="V1" />
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http"/>
      <Endpoint Name="ServiceInputEndpoint" Protocol="http" Port="80"/>
      <Endpoint Name="ReplicatorEndpoint" Protocol="tcp"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
 

<!---HONumber=August15_HO6-->