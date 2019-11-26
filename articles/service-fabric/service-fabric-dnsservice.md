---
title: Servizio DNS di Azure Service Fabric | Microsoft Docs
description: Usare il servizio DNS di Service Fabric per individuare microservizi dall'interno del cluster.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/20/2018
ms.author: atsenthi
ms.openlocfilehash: 707fc9f073e37d60c6c6fca8e9a8392b2550da9f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229298"
---
# <a name="dns-service-in-azure-service-fabric"></a>Servizio DNS in Azure Service Fabric
Il servizio DNS è un servizio di sistema facoltativo che è possibile abilitare nel cluster per individuare altri servizi usando il protocollo DNS. 

Molti servizi, in particolare quelli in contenitore, sono indirizzabili tramite un URL preesistente. È preferibile poterli risolvere mediante il protocollo DNS standard, anziché mediante il protocollo Service Fabric Naming Service. Il servizio DNS consente di eseguire il mapping di nomi DNS a nomi di servizi e di conseguenza di risolvere gli indirizzi IP degli endpoint. Questa funzionalità mantiene la portabilità dei servizi in contenitori su diverse piattaforme e può semplificare gli scenari "lift-and-shift", consentendo di usare gli URL di servizi esistenti anziché dover riscrivere il codice per usare il protocollo Naming Service. 

Il servizio DNS esegue il mapping dei nomi DNS ai nomi di servizi, che vengono quindi risolti dal servizio Naming per restituire l'endpoint di servizio. Il nome DNS per il servizio viene fornito al momento della creazione. Nel diagramma seguente viene illustrato il funzionamento del servizio DNS per i servizi senza stato.

![Endpoint del servizio](./media/service-fabric-dnsservice/stateless-dns.png)

A partire da Service Fabric versione 6.3, il protocollo DNS di Service Fabric è stato esteso per includere uno schema di indirizzamento dei servizi con stato partizionati. Queste estensioni consentono di risolvere specifici indirizzi IP delle partizioni usando una combinazione del nome DNS del servizio con stato e il nome della partizione. Sono supportati tutti e tre gli schemi di partizione:

- Partizionamento denominato
- Partizionamento per intervalli
- Partizionamento singleton

Nel diagramma seguente viene illustrato il funzionamento del servizio DNS per i servizi con stato partizionati.

![endpoint di servizi con stato](./media/service-fabric-dnsservice/stateful-dns.png)

Le porte dinamiche non sono supportate dal servizio DNS. Per risolvere i servizi esposti su porte dinamiche, usare il [servizio proxy inverso](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>Abilitazione del servizio DNS
> [!NOTE]
> Il servizio DNS per i servizi di Service Fabric non è ancora supportato in Linux.

Quando si crea un cluster tramite il portale, il servizio DNS è abilitato per impostazione predefinita nella casella di controllo **Includi servizio DNS** nel menu **Configurazione cluster**:

![Abilitazione del servizio DNS tramite il portale](./media/service-fabric-dnsservice/enable-dns-service.png)

Se non si usa il portale per creare il cluster o se si sta aggiornando un cluster esistente, è necessario abilitare il servizio DNS in un modello:

- Per distribuire un nuovo cluster è possibile usare i [modelli di esempio](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) oppure creare un modello di Resource Manager personalizzato. 
- Per aggiornare un cluster esistente, è possibile passare al gruppo di risorse del cluster nel portale e fare clic su **Script di automazione** per lavorare con un modello che rispecchia lo stato corrente del cluster e delle altre risorse del gruppo. Per maggiori informazioni, vedere [Esportare il modello da un gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template).

Quando si dispone di un modello, è possibile abilitare il servizio DNS seguendo questa procedura:

1. Verificare che `apiversion` sia impostato su `2017-07-01-preview` o versione successiva per la risorsa `Microsoft.ServiceFabric/clusters` e, se non lo è, aggiornarlo come illustrato nell'esempio seguente:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. A questo punto abilitare il servizio DNS in uno dei modi seguenti:

   - Per abilitare il servizio DNS con le impostazioni predefinite, aggiungerlo alla sezione `addonFeatures` all'interno della sezione `properties`, come illustrato nell'esempio seguente:

        ```json
          "properties": {
            ...
            "addonFeatures": [
              "DnsService"
              ],
            ...
          }
        ```

   - Per abilitare il servizio con impostazioni diverse da quelle predefinite, aggiungere una sezione `DnsService` alla sezione `fabricSettings` all'interno della sezione `properties`. In questo caso, non è necessario aggiungere DnsService a `addonFeatures`. Per altre informazioni sulle proprietà che è possibile impostare per il servizio DNS, vedere [Impostazioni del servizio DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).

       ```json
           "properties": {
             ...  
             "fabricSettings": [
               ...
               {
                 "name": "DnsService",
                 "parameters": [
                   {
                     "name": "IsEnabled",
                     "value": "true"
                   },
                   {
                     "name": "PartitionSuffix",
                     "value": "--"
                   },
                   {
                     "name": "PartitionPrefix",
                     "value": "--"
                   }
                 ]
               },
               ...
              ]
            }
       ```
3. Dopo avere aggiornato il modello di cluster con le modifiche, applicarle e consentire il completamento dell'aggiornamento. Al termine dell'aggiornamento, il servizio di sistema DNS viene avviato nel cluster. Il nome del servizio è `fabric:/System/DnsService`, e sarà possibile trovarlo nella sezione **Sistema** del servizio in Service Fabric Explorer. 

> [!NOTE]
> Quando si aggiorna DNS da disabilitato ad abilitato, Service Fabric Explorer possibile che non rifletta il nuovo stato. Per risolvere il problemi, riavviare i nodi modificando il UpgradePolicy nel modello di Azure Resource Manager. Per ulteriori informazioni, vedere il [riferimento al modello di Service Fabric](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2019-03-01/clusters/applications) .

> [!NOTE]
> L'abilitazione del servizio DNS durante lo sviluppo in un computer locale sostituirà alcune impostazioni DNS. Se si verificano problemi di connessione a Internet, controllare le impostazioni DNS.

## <a name="setting-the-dns-name-for-your-service"></a>Impostazione del nome DNS del servizio
È possibile impostare un nome DNS per i servizi scegliendo tra il modo dichiarativo per i servizi predefiniti nel file ApplicationManifest.xml oppure tramite i comandi di PowerShell.

Il nome DNS per il servizio può essere risolto in tutto il cluster ed è quindi importante garantire l'univocità del nome DNS in tutto il cluster. 

È consigliabile utilizzare uno schema di denominazione `<ServiceDnsName>.<AppInstanceName>`, ad esempio `service1.application1`. Se un'applicazione viene distribuita usando Docker Compose, i nomi DNS vengono automaticamente assegnati ai servizi utilizzando questo schema di denominazione.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Impostazione del nome DNS per un servizio predefinito nel file ApplicationManifest.xml
Aprire il progetto in Visual Studio o nell'editor preferito, quindi aprire il file ApplicationManifest.xml. Passare alla sezione relativa ai servizi predefiniti e per ciascuno di esso aggiungere l'attributo `ServiceDnsName`. Nell'esempio seguente viene mostrato come impostare il nome DNS del servizio su `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
Dopo aver distribuito l'applicazione, l'istanza del servizio in Service Fabric Explorer visualizza il nome DNS di tale istanza, come mostrato nella figura seguente: 

![Endpoint del servizio](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

Nell'esempio seguente il nome DNS per un servizio con stato viene impostato su `statefulsvc.app`. Il servizio usa uno schema di partizionamento denominato. Si noti che i nomi di partizioni sono in minuscolo. Questo è un requisito per le partizioni che verranno usate come destinazione nelle query DNS; per altre informazioni, vedere [Effettuare query DNS su una partizione del servizio con stato](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#preview-making-dns-queries-on-a-stateful-service-partition).

```xml
    <Service Name="Stateful1" ServiceDnsName="statefulsvc.app" />
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="2" MinReplicaSetSize="2">
        <NamedPartition>
         <Partition Name="partition1" />
         <Partition Name="partition2" />
        </NamedPartition>
      </StatefulService>
    </Service>
```

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Impostazione del nome DNS per un servizio mediante PowerShell
È possibile impostare il nome DNS di un servizio al momento della creazione usando il comando `New-ServiceFabricService` di PowerShell. Nell'esempio seguente viene creato un nuovo servizio senza stato con il nome DNS `service1.application1`

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>[Anteprima] Effettuare query DNS su una partizione del servizio con stato
A partire da Service Fabric versione 6.3, il servizio DNS di Service Fabric supporta le query per le partizioni del servizio.

Per le partizioni che verranno usate nelle query DNS, si applicano le restrizioni di denominazione seguenti:

   - I nomi di partizioni devono essere compatibili con il servizio DNS.
   - I nomi di partizioni con più etichette (che includono il punto, '.', nel nome) non devono essere usati.
   - I nomi di partizioni devono essere in minuscolo.

Le query DNS che usano come destinazione una partizione vengono formattate come indicato di seguito:

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
Dove:

- *First-Label-Of-Partitioned-Service-DNSName* è la prima parte del nome DNS del servizio.
- *artitionPrefix* è un valore che può essere impostato nella sezione DnsService del manifesto del cluster o tramite il modello di Resource Manager del cluster. Il valore predefinito è "--". Per altre informazioni, vedere [Impostazioni del servizio DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Target-Partition-Name* è il nome della partizione. 
- *PartitionSuffix* è un valore che può essere impostato nella sezione DnsService del manifesto del cluster o tramite il modello di Resource Manager del cluster. Il valore predefinito è una stringa vuota. Per altre informazioni, vedere [Impostazioni del servizio DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Remaining-Partitioned-Service-DNSName* è la parte rimanente del nome DNS del servizio.

Gli esempi seguenti illustrano le query DNS per i servizi partizionati in esecuzione in un cluster con impostazioni predefinite per `PartitionPrefix` e `PartitionSuffix`: 

- Per risolvere la partizione "0" di un servizio con nome DNS `backendrangedschemesvc.application` che usa uno schema di partizionamento per intervalli, usare `backendrangedschemesvc-0.application`.
- Per risolvere la "prima" partizione di un servizio con nome DNS `backendnamedschemesvc.application` che usa uno schema di partizionamento denominato, usare `backendnamedschemesvc-first.application`.

Il servizio DNS restituisce l'indirizzo IP della replica primaria della partizione. Se non viene specificata una partizione, il servizio restituisce l'indirizzo IP della replica primaria di una partizione selezionata in modo casuale.

## <a name="using-dns-in-your-services"></a>Uso del protocollo DNS nei servizi
Se si distribuisce più di un servizio, è possibile trovare gli endpoint di altri servizi con cui comunicare usando un nome DNS. Il servizio DNS funziona per i servizi senza stato e, in Service Fabric versione 6.3 e successiva, per i servizi con stato. Per i servizi con stato in esecuzione sulle versioni di Service Fabric precedenti alla versione 6.3, è possibile usare il [servizio proxy inverso](./service-fabric-reverseproxy.md) predefinito in modo che le chiamate HTTP siano dirette a una particolare partizione del servizio. 

Le porte dinamiche non sono supportate dal servizio DNS. Per risolvere i servizi su porte dinamiche, è possibile usare il servizio proxy inverso.

Il codice seguente illustra come chiamare un servizio senza stato tramite DNS. Si tratta semplicemente di una normale chiamata HTTP in cui si specificano il nome DNS; la porta ed eventuali percorsi facoltativi come parte dell'URL.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

Il codice seguente mostra una chiamata su una partizione specifica di un servizio con stato. In questo caso, il nome DNS contiene il nome della partizione (partition1). La chiamata presuppone un cluster con i valori predefiniti per `PartitionPrefix` e `PartitionSuffix`.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service2-partition1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="known-issues"></a>Problemi noti
* Nelle versioni di Service Fabric 6.3 e successive, le ricerche DNS di nomi di servizi contenenti un segno meno nel nome DNS presentano un problema. Per altre informazioni su questo problema, seguire questo [problema GitHub](https://github.com/Azure/service-fabric-issues/issues/1197). La correzione di questo problema è prevista nel prossimo aggiornamento della versione 6.3. 

* Il servizio DNS per i servizi di Service Fabric non è ancora supportato in Linux. In Linux è supportato il servizio DNS per i contenitori. La risoluzione manuale tramite client Fabric/ServicePartitionResolver è l'alternativa disponibile.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla comunicazione tra i servizi all'interno del cluster, vedere [Connettersi e comunicare con i servizi in Service Fabric](service-fabric-connect-and-communicate-with-services.md)

