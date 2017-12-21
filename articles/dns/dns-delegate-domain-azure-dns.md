---
title: Delegare il dominio a DNS di Azure | Documentazione Microsoft
description: Informazioni su come modificare la delega di dominio e usare server dei nomi DNS di Azure per fornire hosting di dominio.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.assetid: 257da6ec-d6e2-4b6f-ad76-ee2dde4efbcc
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: kumud
ms.openlocfilehash: 3cb1a70835b5fa2b615f4adac4b0d9ae31074bce
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="delegate-a-domain-to-azure-dns"></a>Delegare un dominio al servizio DNS di Azure

DNS di Azure consente di ospitare una zona DNS e gestire i record DNS per un dominio in Azure. Per consentire l'esecuzione di query DNS in modo che un dominio raggiunga DNS di Azure, il dominio deve essere delegato a DNS di Azure dal dominio padre. Tenere presente che DNS di Azure non è il registrar. Questo articolo illustra come delegare il dominio al servizio DNS di Azure.

Per i domini acquistati da un registrar, il registrar offre la possibilità di configurare questi record NS. Non è necessario essere proprietari di un dominio per creare una zona DNS con questo nome di dominio in DNS di Azure, tuttavia è necessario esserlo per configurare la delega in DNS di Azure con il registrar.

Si supponga, ad esempio, di acquistare il dominio "contoso.net" e di creare una zona con il nome "contoso.net" nel servizio DNS di Azure. In qualità di proprietario del dominio, il registrar offre la possibilità di configurare gli indirizzi del server dei nomi (cioè i record NS) per il dominio. Il registrar archivia questi record NS nel dominio padre, in questo caso ".net". I clienti di tutto il mondo possono quindi essere reindirizzati al dominio nella zona DNS di Azure quando provano a risolvere i record DNS in "contoso.net".

## <a name="create-a-dns-zone"></a>Creare una zona DNS

1. Accedere al portale di Azure.
1. Scegliere **Nuovo > Rete >** dal menu Hub e quindi fare clic su **Zona DNS** per aprire la pagina **Crea zona DNS**.

    ![Zona DNS](./media/dns-domain-delegation/dns.png)

1. Nella pagina **Crea zona DNS** immettere i valori seguenti, quindi fare clic su **Crea**:

   | **Impostazione** | **Valore** | **Dettagli** |
   |---|---|---|
   |**Nome**|contoso.net|Nome della zona DNS|
   |**Sottoscrizione**|[Sottoscrizione]|Selezionare una sottoscrizione in cui creare il gateway applicazione.|
   |**Gruppo di risorse**|**Crea nuovo:** contosoRG|Creare un gruppo di risorse. Il nome del gruppo di risorse deve essere univoco all'interno della sottoscrizione selezionata. Per altre informazioni sui gruppi di risorse, vedere l'articolo [Panoramica di Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Posizione**|Stati Uniti occidentali||

> [!NOTE]
> Il gruppo di risorse indica la località del gruppo di risorse e non ha alcun impatto sulla zona DNS. La posizione della zona DNS è sempre "globale" e non viene visualizzata.

## <a name="retrieve-name-servers"></a>Recuperare i server dei nomi

Prima di poter delegare la zona DNS a DNS Azure è necessario conoscere i nomi dei server dei nomi per la zona. DNS Azure alloca i server dei nomi da un pool ogni volta che viene creata una zona.

1. Dopo la creazione della zona DNS, nel riquadro **Preferiti** del portale di Azure fare clic su **Tutte le risorse**. Fare clic sulla zona DNS **contoso.net** nella pagina **Tutte le risorse**. Se nella sottoscrizione selezionata sono già presenti delle risorse, è possibile immettere **contoso.net** nella casella Filtra per nome per accedere con facilità al gateway applicazione. 

1. Recuperare i server dei nomi dalla pagina della zona DNS. In questo esempio, alla zona "contoso.net" sono stati assegnati i server dei nomi "ns1-01.azure-dns.com", "ns2-01.azure-dns.net", "ns3-01.azure-dns.org" e "ns4-01.azure-dns.info":

 ![Dns-nameserver](./media/dns-domain-delegation/viewzonens500.png)

DNS Azure crea automaticamente i record NS autorevoli nella zona con i server dei nomi assegnati.  Per visualizzare i nomi dei server dei nomi con Azure PowerShell o l'interfaccia della riga di comando di Azure è sufficiente recuperare questi record.

Gli esempi seguenti illustrano anche la procedura per recuperare i server dei nomi per una zona in DNS di Azure con PowerShell e con l'interfaccia della riga di comando di Azure.

### <a name="powershell"></a>PowerShell

```powershell
# The record name "@" is used to refer to records at the top of the zone.
$zone = Get-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -Zone $zone
```

L'esempio seguente corrisponde alla risposta.

```
Name              : @
ZoneName          : contoso.net
ResourceGroupName : contosorg
Ttl               : 172800
Etag              : 03bff8f1-9c60-4a9b-ad9d-ac97366ee4d5
RecordType        : NS
Records           : {ns1-07.azure-dns.com., ns2-07.azure-dns.net., ns3-07.azure-dns.org.,
                    ns4-07.azure-dns.info.}
Metadata          :
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli
az network dns record-set list --resource-group contosoRG --zone-name contoso.net --type NS --name @
```

L'esempio seguente corrisponde alla risposta.

```json
{
  "etag": "03bff8f1-9c60-4a9b-ad9d-ac97366ee4d5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contosoRG/providers/Microsoft.Network/dnszones/contoso.net/NS/@",
  "metadata": null,
  "name": "@",
  "nsRecords": [
    {
      "nsdname": "ns1-07.azure-dns.com."
    },
    {
      "nsdname": "ns2-07.azure-dns.net."
    },
    {
      "nsdname": "ns3-07.azure-dns.org."
    },
    {
      "nsdname": "ns4-07.azure-dns.info."
    }
  ],
  "resourceGroup": "contosoRG",
  "ttl": 172800,
  "type": "Microsoft.Network/dnszones/NS"
}
```

## <a name="delegate-the-domain"></a>Delegare il dominio

Dopo la creazione della zona DNS e il recupero dei server dei nomi, il dominio padre deve essere aggiornato con i server dei nomi di DNS di Azure. Ogni registrar prevede i propri strumenti di gestione DNS per modificare i record del server dei nomi per un dominio. Nella pagina di gestione DNS del registrar, modificare i record NS e sostituirli con quelli creati da DNS di Azure.

Quando si delega un dominio a DNS di Azure, è necessario usare i nomi dei server dei nomi forniti da DNS di Azure. È consigliabile usare sempre tutti e quattro i nomi di server dei nomi, indipendentemente dal nome del dominio. La delega del dominio non richiede il nome del server dei nomi per usare lo stesso dominio di primo livello del dominio locale.

Non usare "glue record" per puntare agli indirizzi IP del server dei nomi DNS di Azure, perché questi indirizzi IP possono cambiare in futuro. Le deleghe che usano nomi dei server dei nomi nella propria zona, definiti a volte "server dei nomi personali", non sono attualmente supportate in DNS di Azure.

## <a name="verify-name-resolution-is-working"></a>Verificare che la risoluzione dei nomi funzioni

Dopo aver completato la delega, è possibile verificare il corretto funzionamento della risoluzione dei nomi usando uno strumento come "nslookup" per eseguire una query sul record SOA per la zona, che viene creato automaticamente anche al momento della creazione della zona.

Non è necessario specificare i server dei nomi DNS di Azure. Se la delega è stata configurata correttamente, il normale processo di risoluzione DNS trova i server dei nomi automaticamente.

```
nslookup -type=SOA contoso.com
```

Di seguito è riportata una risposta di esempio dal comando precedente:

```
Server: ns1-04.azure-dns.com
Address: 208.76.47.4

contoso.com
primary name server = ns1-04.azure-dns.com
responsible mail addr = msnhst.microsoft.com
serial = 1
refresh = 900 (15 mins)
retry = 300 (5 mins)
expire = 604800 (7 days)
default TTL = 300 (5 mins)
```

## <a name="delegate-sub-domains-in-azure-dns"></a>Delegare sottodomini in DNS di Azure

Se si vuole configurare una zona figlio separata, è possibile delegare un sottodominio in DNS di Azure. Dopo aver configurato e delegato "contoso.net" in DNS di Azure, si supponga di voler configurare una zona figlio separata, "partners.contoso.net".

1. Creare la zona figlio "partners.contoso.net" in DNS di Azure.
2. Cercare i record NS autorevoli nella zona figlio per ottenere i server dei nomi che ospitano la zona figlio nel sistema DNS di Azure.
3. Delegare la zona figlio configurando i record NS nella zona padre che punta alla zona figlio.

### <a name="create-a-dns-zone"></a>Creare una zona DNS

1. Accedere al portale di Azure
1. Nel menu Hub fare clic su **Nuovo > Rete >** e quindi fare clic su **Zona DNS** per aprire la pagina Crea zona DNS.

    ![Zona DNS](./media/dns-domain-delegation/dns.png)

1. Nella pagina **Crea zona DNS** immettere i valori seguenti, quindi fare clic su **Crea**:

   | **Impostazione** | **Valore** | **Dettagli** |
   |---|---|---|
   |**Nome**|partners.contoso.net|Nome della zona DNS|
   |**Sottoscrizione**|[Sottoscrizione]|Selezionare una sottoscrizione in cui creare il gateway applicazione.|
   |**Gruppo di risorse**|**Utilizza esistente:** contosoRG|Creare un gruppo di risorse. Il nome del gruppo di risorse deve essere univoco all'interno della sottoscrizione selezionata. Per altre informazioni sui gruppi di risorse, vedere l'articolo [Panoramica di Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Posizione**|Stati Uniti occidentali||

> [!NOTE]
> Il gruppo di risorse indica la località del gruppo di risorse e non ha alcun impatto sulla zona DNS. La posizione della zona DNS è sempre "globale" e non viene visualizzata.

### <a name="retrieve-name-servers"></a>Recuperare i server dei nomi

1. Dopo la creazione della zona DNS, nel riquadro **Preferiti** del portale di Azure fare clic su **Tutte le risorse**. Fare clic sulla zona DNS **partners.contoso.net** nella pagina **Tutte le risorse**. Se nella sottoscrizione selezionata sono già presenti delle risorse, è possibile immettere **partners.contoso.net** nella casella Filtra per nome per accedere facilmente alla zona DNS.

1. Recuperare i server dei nomi dalla pagina della zona DNS. In questo esempio, alla zona "contoso.net" sono stati assegnati i server dei nomi "ns1-01.azure-dns.com", "ns2-01.azure-dns.net", "ns3-01.azure-dns.org" e "ns4-01.azure-dns.info":

 ![Dns-nameserver](./media/dns-domain-delegation/viewzonens500.png)

DNS Azure crea automaticamente i record NS autorevoli nella zona con i server dei nomi assegnati.  Per visualizzare i nomi dei server dei nomi con Azure PowerShell o l'interfaccia della riga di comando di Azure è sufficiente recuperare questi record.

### <a name="create-name-server-record-in-parent-zone"></a>Creare un record del server dei nomi in una zona padre

1. Passare alla zona DNS **contoso.net** nel portale di Azure.
1. Fare clic su **+ Set di record**.
1. Nella pagina **Aggiungi set di record** immettere i valori seguenti, quindi fare clic su **OK**:

   | **Impostazione** | **Valore** | **Dettagli** |
   |---|---|---|
   |**Nome**|partners|Nome della zona DNS figlio|
   |**Tipo**|NS|Usare NS per i record del server dei nomi.|
   |**TTL**|1|Durata (TTL).|
   |**Unità TTL**|Ore|Imposta l'unità TTL in ore|
   |**SERVER DEI NOMI**|{server dei nomi dalla zona partners.contoso.net}|Immettere tutti e quattro i server dei nomi dalla zona partners.contoso.net. |

   ![Dns-nameserver](./media/dns-domain-delegation/partnerzone.png)


### <a name="delegating-sub-domains-in-azure-dns-with-other-tools"></a>Delega di sottodomini in DNS di Azure con altri strumenti

Gli esempi seguenti illustrano la procedura per la delega di sottodomini in DNS di Azure con PowerShell e con l'interfaccia della riga di comando:

#### <a name="powershell"></a>PowerShell

L'esempio di PowerShell seguente dimostra il funzionamento. Gli stessi passaggi possono essere eseguiti con il portale di Azure o l'interfaccia della riga di comando multipiattaforma di Azure.

```powershell
# Create the parent and child zones. These can be in same resource group or different resource groups as Azure DNS is a global service.
$parent = New-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
$child = New-AzureRmDnsZone -Name partners.contoso.net -ResourceGroupName contosoRG

# Retrieve the authoritative NS records from the child zone as shown in the next example. This contains the name servers assigned to the child zone.
$child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS

# Create the corresponding NS record set in the parent zone to complete the delegation. The record set name in the parent zone matches the child zone name, in this case "partners".
$parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
$parent_ns_recordset.Records = $child_ns_recordset.Records
Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset
```

Usare `nslookup` per verificare che tutto sia configurato correttamente eseguendo la ricerca del record SOA della zona figlio.

```
nslookup -type=SOA partners.contoso.com
```

```
Server: ns1-08.azure-dns.com
Address: 208.76.47.8

partners.contoso.com
    primary name server = ns1-08.azure-dns.com
    responsible mail addr = msnhst.microsoft.com
    serial = 1
    refresh = 900 (15 mins)
    retry = 300 (5 mins)
    expire = 604800 (7 days)
    default TTL = 300 (5 mins)
```

#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli
#!/bin/bash

# Create the parent and child zones. These can be in same resource group or different resource groups as Azure DNS is a global service.
az network dns zone create -g contosoRG -n contoso.net
az network dns zone create -g contosoRG -n partners.contoso.net
```

Recuperare i server dei nomi per la zona `partners.contoso.net` dall'output.

```
{
  "etag": "00000003-0000-0000-418f-250de2b2d201",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contosorg/providers/Microsoft.Network/dnszones/partners.contoso.net",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "partners.contoso.net",
  "nameServers": [
    "ns1-09.azure-dns.com.",
    "ns2-09.azure-dns.net.",
    "ns3-09.azure-dns.org.",
    "ns4-09.azure-dns.info."
  ],
  "numberOfRecordSets": 2,
  "resourceGroup": "contosorg",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Creare il set di record e i record NS per ogni server dei nomi.

```azurecli
#!/bin/bash

# Create the record set
az network dns record-set ns create --resource-group contosorg --zone-name contoso.net --name partners

# Create a ns record for each name server.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns1-09.azure-dns.com.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns2-09.azure-dns.net.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns3-09.azure-dns.org.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns4-09.azure-dns.info.
```

## <a name="delete-all-resources"></a>Eliminare tutte le risorse

Per eliminare tutte le risorse create nell'esecuzione dell'esercizio, seguire questa procedura:

1. Nel riquadro **Preferiti** del portale di Azure fare clic su **Tutte le risorse**. Fare clic sul gruppo di risorse **contosorg** nella pagina Tutte le risorse. Se nella sottoscrizione selezionata sono già presenti delle risorse, è possibile immettere **contosorg** nella casella **Filtra per nome** per accedere facilmente al gruppo di risorse.
1. Nella pagina **contosorg** fare clic sul pulsante **Elimina**.
1. Il portale richiede di digitare il nome del gruppo di risorse per confermare che si desidera effettivamente procedere all'eliminazione. Digitare *contosorg* come nome del gruppo di risorse e quindi fare clic su **Elimina**. L'eliminazione di un gruppo di risorse determina l'eliminazione di tutte le risorse in esso contenute. È quindi consigliabile verificare sempre il contenuto di un gruppo prima di eliminarlo. Il portale elimina tutte le risorse contenute nel gruppo di risorse e quindi elimina il gruppo. Questo processo richiede alcuni minuti.

## <a name="next-steps"></a>Passaggi successivi

[Gestire le zone DNS](dns-operations-dnszones.md)

[Gestire i record DNS](dns-operations-recordsets.md)
