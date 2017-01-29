---
title: Creare zone e set di record DNS in DNS di Azure con .NET SDK | Documentazione Microsoft
description: Come creare zone e set di record DNS in DNS di Azure con .NET SDK.
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: 5d73d1203faf485d715354e68ce2ccde32562611
ms.openlocfilehash: c0fb0be8da1c0ca48a4d43ea027d30a0bc17fe30

---

# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Creare zone e set di record DNS con .NET SDK

È possibile automatizzare le operazioni per creare, eliminare o aggiornare zone, set di record e record DNS usando DNS SDK con la libreria di gestione DNS per .NET. Un progetto completo di Visual Studio è disponibile [qui](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True).

## <a name="create-a-service-principal-account"></a>Creare un account dell'entità servizio

L'accesso a livello di programmazione alle risorse di Azure viene in genere concesso tramite un account dedicato, non tramite le proprie credenziali utente. Questi account dedicati sono denominati 'account dell'entità servizio'. Per usare il progetto di esempio Azure SDK per DNS, è prima necessario creare un account dell'entità servizio e assegnargli le autorizzazioni corrette.

1. Seguire [queste istruzioni](../azure-resource-manager/resource-group-authenticate-service-principal.md) per creare un account dell'entità servizio. Il progetto di esempio Azure SDK per DNS presuppone che l'autenticazione sia basata su password.
2. Creare un gruppo di risorse seguendo [questa procedura](../azure-resource-manager/resource-group-template-deploy-portal.md).
3. Usare il Controllo degli accessi in base al ruolo di Azure per concedere all'account dell'entità servizio le autorizzazioni 'DNS Zone Contributor' per il gruppo di risorse. A questo scopo, seguire [questa procedura](../active-directory/role-based-access-control-configure.md).
4. Se si usa il progetto di esempio Azure SDK per DNS, modificare il file 'program.cs' come segue:

   * Inserire i valori corretti per tenantId, clientId (noto anche come ID account), secret (password dell'account dell'entità servizio) e subscriptionId, come indicato nel passaggio 1.
   * Immettere il nome del gruppo di risorse scelto nel passaggio 2.
   * Immettere un nome a scelta per la zona DNS.

## <a name="nuget-packages-and-namespace-declarations"></a>Pacchetti NuGet e dichiarazioni degli spazi dei nomi

Per usare Azure .NET SDK per DNS è necessario installare il pacchetto NuGet **Azure DNS Management Library** e altri pacchetti necessari di Azure.

1. In **Visual Studio**aprire un progetto o un nuovo progetto.
2. Passare a **Strumenti** **>** **Gestione pacchetti NuGet** **>** **Gestisci pacchetti NuGet per la soluzione**.
3. Fare clic su **Sfoglia**, selezionare la casella di controllo **Includi versione preliminare** e digitare **Microsoft.Azure.Management.Dns** nella casella di ricerca.
4. Selezionare il pacchetto e fare clic su **Installa** per aggiungerlo al progetto di Visual Studio.
5. Ripetere il processo precedente per installare anche i seguenti pacchetti: **Microsoft.Rest.ClientRuntime.Azure.Authentication** e **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Aggiungere le dichiarazioni dello spazio dei nomi

Aggiungere le seguenti dichiarazioni dello spazio dei nomi

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>Inizializzare il client di gestione DNS

*DnsManagementClient* contiene i metodi e le proprietà necessari per gestire le zone e i set di record DNS.  Il codice seguente accede all'account dell'entità servizio e crea un oggetto DnsManagementClient.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Creare o aggiornare una zona DNS

Per creare una zona DNS, viene prima creato un oggetto "Zone" per contenere i parametri della zona. Poiché le zone DNS non sono collegate a un'area specifica, la posizione viene impostata su 'global'. In questo esempio, alla zona viene anche aggiunto un ['tag' di Azure Resource Manager](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) .

Per creare o aggiornare effettivamente la zona in DNS di Azure, l'oggetto "Zone" contenente i parametri della zona viene passato al metodo *DnsManagementClient.Zones.CreateOrUpdateAsyc* .

> [!NOTE]
> DnsManagementClient supporta tre modalità di funzionamento: sincrona ('CreateOrUpdate'), asincrona ('CreateOrUpdateAsync') o asincrona con accesso alla risposta HTTP ('CreateOrUpdateWithHttpMessagesAsync').  È possibile scegliere una modalità qualsiasi a seconda delle esigenze dell'applicazione.

DNS di Azure supporta la concorrenza ottimistica, definita [Etags](dns-getstarted-create-dnszone.md). In questo esempio, specificando "*" per l'intestazione 'If-None-Match' si indica a DNS di Azure di creare una zona DNS se non già esistente.  La chiamata non riesce se esiste già una zona con il nome specificato nel gruppo di risorse indicato.

```cs
// Create zone parameters
var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"

// Create a Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
dnsZoneParams.Tags = new Dictionary<string, string>();
dnsZoneParams.Tags.Add("dept", "finance");

// Create the actual zone.
// Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
// Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
// Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");
```

## <a name="create-dns-record-sets-and-records"></a>Creare set di record e record DNS

I record DNS vengono gestiti come set di record. Un set di record è dato da un insieme di record con lo stesso nome e tipo di record all'interno di una zona.  Il nome del set di record è relativo al nome della zona, non al nome DNS completo.

Per creare o aggiornare un set di record, un oggetto di parametri "RecordSet" viene creato e passato a *DnsManagementClient.RecordSets.CreateOrUpdateAsync*. Come nel caso delle zone DNS, sono disponibili tre modalità di funzionamento: sincrona ('CreateOrUpdate'), asincrona ('CreateOrUpdateAsync') o asincrona con accesso alla risposta HTTP ('CreateOrUpdateWithHttpMessagesAsync').

Come nel caso delle zone DNS, le operazioni sui set di record includono il supporto per la concorrenza ottimistica.  In questo esempio, poiché né 'If-Match', né 'If-None-Match' sono specificati, viene sempre creato il set di record.  Questa chiamata sovrascrive qualsiasi set di record con lo stesso nome e tipo di record in questa zona DNS.

```cs
// Create record set parameters
var recordSetParams = new RecordSet();
recordSetParams.TTL = 3600;

// Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
recordSetParams.ARecords = new List<ARecord>();
recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

// Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
recordSetParams.Metadata = new Dictionary<string, string>();
recordSetParams.Metadata.Add("user", "Mary");

// Create the actual record set in Azure DNS
// Note: no ETAG checks specified, will overwrite existing record set if one exists
var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);
```

## <a name="get-zones-and-record-sets"></a>Ottenere zone e set di record

I metodi *DnsManagementClient.Zones.Get* e *DnsManagementClient.RecordSets.Get* recuperano rispettivamente singole zone e set di record. I set di record vengono identificati dal tipo, dal nome, dalla zona e dal gruppo di risorse in cui si trovano. Le zone vengono identificate dal nome e dal gruppo di risorse in cui si trovano.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Aggiornare un set di record esistente

Per aggiornare un set di record DNS esistente, recuperare prima il set di record, aggiornarne il contenuto, quindi inviare le modifiche.  In questo esempio viene specificato l'elemento 'Etag' del set di record recuperato nel parametro 'If-Match'. La chiamata non riesce se un'operazione simultanea ha modificato il set di record nel frattempo.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Elencare zone e set di record

Per elencare le zone usare i metodi *DnsManagementClient.Zones.List*, che supportano l'elenco di tutte le zone in un gruppo di risorse o tutte le zone in una sottoscrizione di Azure (in più gruppi di risorse). Per elencare i set di record usare i metodi *DnsManagementClient.RecordSets.List*, che supportano un elenco di tutti i set di record in una zona o solo i set di record di un tipo specifico.

Quando si elencano le zone e i set di record, i risultati potrebbero essere impaginati.  L'esempio seguente illustra come scorrere le pagine dei risultati. Vengono usate dimensioni della pagina artificialmente ridotte pari a '2' per forzare il paging; nella pratica questo parametro deve essere omesso e verranno usate le dimensioni della pagina predefinite.

```cs
// Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
// In practice, to improve performance you would use a large page size or just use the system default
int recordSets = 0;
var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
recordSets += page.Count();

while (page.NextPageLink != null)
{
    page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
    recordSets += page.Count();
}
```

## <a name="next-steps"></a>Passaggi successivi

Scaricare il [progetto di esempio Azure .NET SDK per DNS](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), che include altri esempi dell'uso di Azure .NET SDK per DNS, tra cui alcuni esempi per altri tipi di record DNS.



<!--HONumber=Dec16_HO4-->


