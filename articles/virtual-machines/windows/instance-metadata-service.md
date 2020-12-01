---
title: Servizio metadati dell'istanza di Azure per Windows
description: Informazioni sul servizio metadati dell'istanza di Azure e su come vengono fornite informazioni sulle istanze di macchine virtuali attualmente in esecuzione in Windows.
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/30/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: cc0d47807101a3cfbb26e7ea69cc7d117d3f9b31
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435230"
---
# <a name="azure-instance-metadata-service"></a>Servizio metadati dell'istanza di Azure

Il servizio metadati dell'istanza di Azure (IMDS) fornisce informazioni sulle istanze di macchine virtuali attualmente in esecuzione. È possibile usarlo per gestire e configurare le macchine virtuali.
Queste informazioni includono lo SKU, l'archiviazione, le configurazioni di rete e gli eventi di manutenzione imminenti. Per un elenco completo dei dati disponibili, vedere [API dei metadati](#metadata-apis).


IMDS è disponibile per l'esecuzione di istanze di macchine virtuali (VM) e di istanze del set di scalabilità di macchine virtuali. Tutte le API supportano le VM create e gestite con [Azure Resource Manager](/rest/api/resources/). Solo gli endpoint di rete e attestati supportano le macchine virtuali create usando il modello di distribuzione classica. L'endpoint attestato esegue questa operazione solo in un extent limitato.

IMDS è un endpoint REST disponibile a un indirizzo IP noto e non instradabile ( `169.254.169.254` ). È possibile accedervi solo dall'interno della macchina virtuale. La comunicazione tra la macchina virtuale e IMDS non lascia mai l'host.
Fare in modo che i client HTTP ignorino i proxy Web all'interno della VM durante l'esecuzione di query su IMDS e trattino `169.254.169.254` gli stessi di [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="security"></a>Sicurezza

L'endpoint IMDS è accessibile solo dall'interno dell'istanza di macchina virtuale in esecuzione in un indirizzo IP non instradabile. Inoltre, qualsiasi richiesta con un' `X-Forwarded-For` intestazione viene rifiutata dal servizio.
Le richieste devono anche contenere un' `Metadata: true` intestazione, per garantire che la richiesta effettiva sia stata destinata direttamente e non faccia parte di un reindirizzamento non intenzionale.

> [!IMPORTANT]
> IMDS non è un canale per i dati sensibili. L'endpoint è aperto a tutti i processi nella macchina virtuale. Prendere in considerazione le informazioni esposte tramite questo servizio come informazioni condivise per tutte le applicazioni in esecuzione all'interno della macchina virtuale.

## <a name="usage"></a>Utilizzo

### <a name="access-azure-instance-metadata-service"></a>Accedere al servizio metadati dell'istanza di Azure

Per accedere a IMDS, creare una macchina virtuale da [Azure Resource Manager](/rest/api/resources/) o [portale di Azure](https://portal.azure.com)e usare gli esempi seguenti.
Per altri esempi, vedere [esempi di metadati dell'istanza di Azure](https://github.com/microsoft/azureimds).

Ecco il codice di esempio per recuperare tutti i metadati per un'istanza. Per accedere a un'origine dati specifica, vedere la sezione [API dei metadati](#metadata-apis) . 

**Richiesta**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance?api-version=2020-09-01 | ConvertTo-Json
```
> [!NOTE]
> Il `-NoProxy` flag è disponibile solo in PowerShell 6 o versioni successive. È possibile omettere il flag se non si ha una configurazione del proxy.

**Risposta**

> [!NOTE]
> La risposta è una stringa JSON. Inviare tramite pipe la query REST tramite il `ConvertTo-Json` cmdlet per una stampa piuttosto semplice.

```json
{
    "compute": {
        "azEnvironment": "AZUREPUBLICCLOUD",
        "isHostCompatibilityLayerVm": "true",
        "licenseType":  "Windows_Client",
        "location": "westus",
        "name": "examplevmname",
        "offer": "Windows",
        "osProfile": {
            "adminUsername": "admin",
            "computerName": "examplevmname",
            "disablePasswordAuthentication": "true"
        },
        "osType": "linux",
        "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
        "plan": {
            "name": "planName",
            "product": "planProduct",
            "publisher": "planPublisher"
        },
        "platformFaultDomain": "36",
        "platformUpdateDomain": "42",
        "publicKeys": [{
                "keyData": "ssh-rsa 0",
                "path": "/home/user/.ssh/authorized_keys0"
            },
            {
                "keyData": "ssh-rsa 1",
                "path": "/home/user/.ssh/authorized_keys1"
            }
        ],
        "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
        "resourceGroupName": "macikgo-test-may-23",
        "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
        "securityProfile": {
            "secureBootEnabled": "true",
            "virtualTpmEnabled": "false"
        },
        "sku": "Windows-Server-2012-R2-Datacenter",
        "storageProfile": {
            "dataDisks": [{
                "caching": "None",
                "createOption": "Empty",
                "diskSizeGB": "1024",
                "image": {
                    "uri": ""
                },
                "lun": "0",
                "managedDisk": {
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampledatadiskname",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }],
            "imageReference": {
                "id": "",
                "offer": "UbuntuServer",
                "publisher": "Canonical",
                "sku": "16.04.0-LTS",
                "version": "latest"
            },
            "osDisk": {
                "caching": "ReadWrite",
                "createOption": "FromImage",
                "diskSizeGB": "30",
                "diffDiskSettings": {
                    "option": "Local"
                },
                "encryptionSettings": {
                    "enabled": "false"
                },
                "image": {
                    "uri": ""
                },
                "managedDisk": {
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampleosdiskname",
                "osType": "Linux",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }
        },
        "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
        "tags": "baz:bash;foo:bar",
        "version": "15.05.22",
        "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
        "vmScaleSetName": "crpteste9vflji9",
        "vmSize": "Standard_A3",
        "zone": ""
    },
    "network": {
        "interface": [{
            "ipv4": {
               "ipAddress": [{
                    "privateIpAddress": "10.144.133.132",
                    "publicIpAddress": ""
                }],
                "subnet": [{
                    "address": "10.144.133.128",
                    "prefix": "26"
                }]
            },
            "ipv6": {
                "ipAddress": [
                 ]
            },
            "macAddress": "0011AAFFBB22"
        }]
    }
}
```

### <a name="data-output"></a>Output dei dati

Per impostazione predefinita, IMDS restituisce i dati in formato JSON ( `Content-Type: application/json` ). Tuttavia, alcune API possono restituire dati in formati diversi, se richiesto.
Nella tabella seguente sono elencati altri formati di dati che potrebbero essere supportati dalle API.

API | Formato dati predefinito | Altri formati
--------|---------------------|--------------
/attested | json | none
/identity | json | none
/instance | json | text
/scheduledevents | json | none

Per accedere a un formato di risposta non predefinito, specificare il formato richiesto come parametro della stringa di query nella richiesta. Ad esempio:

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Per i nodi foglia in `/metadata/instance` , `format=json` non funziona. Per queste query, `format=text` deve essere specificato in modo esplicito perché il formato predefinito è JSON.

### <a name="version"></a>Version

IMDS è con versione e la specifica della versione API nella richiesta HTTP è obbligatoria.

Le versioni API supportate sono: 
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 01/06/2019
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01

> [!NOTE]
> La versione 2020-10-01 potrebbe non essere ancora disponibile in ogni area.

Quando vengono aggiunte nuove versioni, è comunque possibile accedere alle versioni precedenti per la compatibilità se gli script hanno dipendenze da formati di dati specifici.

Quando non si specifica una versione, viene visualizzato un errore con un elenco delle versioni supportate più recenti.

> [!NOTE]
> La risposta è una stringa JSON. Nell'esempio seguente viene indicata la condizione di errore quando la versione non è specificata. La risposta è abbastanza stampata per migliorare la leggibilità.

**Richiesta**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance
```

**Risposta**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

## <a name="metadata-apis"></a>API dei metadati

IMDS contiene più API che rappresentano origini dati diverse.

API | Descrizione | Versione introdotta
----|-------------|-----------------------
/attested | Vedere [i dati attestati](#attested-data) | 2018-10-01
/identity | Vedere [Acquisire un token di accesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
/instance | Vedere [API dell'istanza](#instance-api) | 2017-04-02
/scheduledevents | Vedere [gli eventi pianificati](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>API dell'istanza

API dell'istanza espone i metadati importanti per le istanze di macchina virtuale, tra cui la macchina virtuale, la rete e l'archiviazione. È possibile accedere alle categorie seguenti tramite `instance/compute` :

Dati | Descrizione | Versione introdotta
-----|-------------|-----------------------
azEnvironment | Ambiente Azure in cui è in esecuzione la macchina virtuale. | 2018-10-01
customData | Questa funzionalità è attualmente disabilitata. | 2019-02-01
isHostCompatibilityLayerVm | Indica se la macchina virtuale è in esecuzione nel livello di compatibilità dell'host. | 2020-06-01
licenseType | Tipo di licenza per [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit). Si noti che questo è presente solo per le macchine virtuali abilitate per vantaggio Azure Hybrid. | 2020-09-01
posizione | Area di Azure in cui è in esecuzione la macchina virtuale. | 2017-04-02
name | Nome della macchina virtuale. | 2017-04-02
offer | Offre informazioni per l'immagine della macchina virtuale. Questa operazione è disponibile solo per le immagini distribuite dalla raccolta immagini di Azure. | 2017-04-02
osProfile.adminUsername | Specifica il nome dell'account amministratore. | 2020-07-15
osProfile. nomecomputer | Specifica il nome del computer. | 2020-07-15
osProfile. disablePasswordAuthentication | Specifica se l'autenticazione della password è disabilitata. Si noti che questa operazione è disponibile solo per le macchine virtuali Linux. | 2020-10-01
osType | Linux o Windows. | 2017-04-02
placementGroupId | [Gruppo di posizionamento](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) del set di scalabilità di macchine virtuali. | 2017-08-01
piano | [Piano](/rest/api/compute/virtualmachines/createorupdate#plan) contenente il nome, il prodotto e il server di pubblicazione per una macchina virtuale se si tratta di un'immagine di Azure Marketplace. | 2018-04-02
platformUpdateDomain |  [Dominio di aggiornamento](../manage-availability.md) in cui è in esecuzione la macchina virtuale. | 2017-04-02
platformFaultDomain | [Dominio di errore](../manage-availability.md) in cui è in esecuzione la macchina virtuale. | 2017-04-02
provider | Provider della macchina virtuale. | 2018-10-01
publicKeys | [Raccolta di chiavi pubbliche](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) assegnate alla macchina virtuale e ai percorsi. | 2018-04-02
publisher | Server di pubblicazione dell'immagine della macchina virtuale. | 2017-04-02
resourceGroupName | [Gruppo di risorse](../../azure-resource-manager/management/overview.md) per la macchina virtuale. | 2017-08-01
resourceId | ID [completo della risorsa](/rest/api/resources/resources/getbyid) . | 2019-03-11
sku | SKU specifico per l'immagine di macchina virtuale. | 2017-04-02
securityProfile. della securebootenabled | Indica se l'avvio protetto UEFI è abilitato nella macchina virtuale. | 2020-06-01
securityProfile.virtualTpmEnabled | Indica se il Trusted Platform Module virtuale (TPM) è abilitato nella macchina virtuale. | 2020-06-01
storageProfile | Vedere [profilo di archiviazione](#storage-metadata). | 01/06/2019
subscriptionId | Sottoscrizione di Azure per la macchina virtuale. | 2017-08-01
tags | [Tag](../../azure-resource-manager/management/tag-resources.md) per la macchina virtuale.  | 2017-08-01
tagsList | Tag formattati come matrice JSON per un'analisi più semplice a livello di codice.  | 2019-06-04
version | Versione dell'immagine di macchina virtuale. | 2017-04-02
vmId | [Identificatore univoco](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) per la macchina virtuale. | 2017-04-02
vmScaleSetName | Nome del set di scalabilità di [macchine virtuali](../../virtual-machine-scale-sets/overview.md) del set di scalabilità di macchine virtuali. | 2017-12-01
vmSize | Vedere [dimensioni della macchina virtuale](../sizes.md). | 2017-04-02
zona | [Zona di disponibilità](../../availability-zones/az-overview.md) della macchina virtuale. | 2017-12-01

### <a name="sample-1-track-a-vm-running-on-azure"></a>Esempio 1: tenere traccia di una macchina virtuale in esecuzione in Azure

Come provider di servizi, potrebbe essere necessario tenere traccia del numero di macchine virtuali che eseguono il software o avere agenti che devono tenere traccia dell'univocità della VM. Per poter ottenere un ID univoco per una macchina virtuale, usare il `vmId` campo da IMDS.

**Richiesta**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Risposta**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-different-data-replicas"></a>Esempio 2: posizionamento di repliche di dati diverse

Per alcuni scenari, il posizionamento di repliche dati diverse è di importanza primaria. Ad esempio, il [posizionamento della replica](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) o il posizionamento del contenitore di HDFS tramite un agente di [orchestrazione](https://kubernetes.io/docs/user-guide/node-selection/) potrebbe richiedere la conoscenza di `platformFaultDomain` e `platformUpdateDomain` la macchina virtuale è in esecuzione in.
Per prendere decisioni di questo tipo è anche possibile basarsi sulle [zone di disponibilità](../../availability-zones/az-overview.md) per le istanze.
È possibile eseguire query su questi dati direttamente tramite IMDS.

**Richiesta**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Risposta**

```text
0
```

### <a name="sample-3-get-more-information-about-the-vm-during-support-case"></a>Esempio 3: ottenere altre informazioni sulla macchina virtuale durante il caso di supporto

Come provider di servizi, è possibile ricevere una chiamata di supporto in cui si desidera ottenere altre informazioni sulla macchina virtuale. Chiedere al cliente di condividere i metadati di calcolo può essere utile in questo caso.

**Richiesta**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01
```

**Risposta**

> [!NOTE]
> La risposta è una stringa JSON. La risposta di esempio che segue è di tipo pretty-print per una migliore leggibilità.

```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "Windows",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "Windows-Server-2012-R2-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

### <a name="sample-4-get-the-azure-environment-where-the-vm-is-running"></a>Esempio 4: ottenere l'ambiente Azure in cui è in esecuzione la macchina virtuale

Azure dispone di diversi cloud sovrani, come [Azure per enti pubblici](https://azure.microsoft.com/overview/clouds/government/). In alcuni casi è necessario l'ambiente Azure per prendere decisioni in fase di esecuzione. L'esempio seguente illustra come è possibile ottenere questo comportamento.

**Richiesta**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Risposta**

```text
AzurePublicCloud
```

Il cloud e i valori dell'ambiente Azure sono elencati qui.

 Cloud   | Ambiente Azure
---------|-----------------
[Tutte le aree globali di Azure disponibili a livello generale](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[21Vianet per Azure Cina](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Germania](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>Metadati di rete 

I metadati di rete fanno parte dell'API dell'istanza. Le seguenti categorie di rete sono disponibili tramite l' `instance/network` endpoint.

Dati | Descrizione | Versione introdotta
-----|-------------|-----------------------
ipv4/privateIpAddress | Indirizzo IPv4 locale della macchina virtuale. | 2017-04-02
ipv4/publicIpAddress | Indirizzo IPv4 pubblico della macchina virtuale. | 2017-04-02
subnet/address | Indirizzo della subnet della macchina virtuale. | 2017-04-02
subnet/prefix | Prefisso della subnet. Esempio: 24 | 2017-04-02
ipv6/ipAddress | Indirizzo IPv6 locale della macchina virtuale. | 2017-04-02
macAddress | Indirizzo MAC della macchina virtuale. | 2017-04-02

> [!NOTE]
> Tutte le risposte delle API sono stringhe JSON. Tutte le risposte di esempio che seguono sono di tipo pretty-print per una migliore leggibilità.

#### <a name="sample-1-retrieve-network-information"></a>Esempio 1: recuperare le informazioni di rete

**Richiesta**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/network?api-version=2017-08-01
```

**Risposta**

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="sample-2-retrieve-public-ip-address"></a>Esempio 2: recuperare l'indirizzo IP pubblico

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>Metadati di archiviazione

I metadati di archiviazione fanno parte dell'API dell'istanza, sotto l' `instance/compute/storageProfile` endpoint.
Forniscono informazioni dettagliate sui dischi di archiviazione associati alla macchina virtuale. 

Il profilo di archiviazione di una macchina virtuale è suddiviso in tre categorie: riferimento immagine, disco del sistema operativo e dischi dati.

L'oggetto riferimento all'immagine contiene le seguenti informazioni sull'immagine del sistema operativo:

Dati    | Descrizione
--------|-----------------
id      | ID risorsa
offer   | Offerta della piattaforma o dell'immagine
publisher | Editore immagine
sku     | SKU dell'immagine
version | Versione della piattaforma o dell'immagine

L'oggetto disco del sistema operativo contiene le informazioni seguenti sul disco del sistema operativo usato dalla macchina virtuale:

Dati    | Descrizione
--------|-----------------
caching | Requisiti per la memorizzazione nella cache
createOption | Informazioni sul modo in cui è stata creata la macchina virtuale
diffDiskSettings | Impostazioni disco temporaneo
diskSizeGB | Dimensioni del disco in GB
encryptionSettings | Impostazioni di crittografia per il disco
image   | Disco rigido virtuale dell'immagine utente di origine
managedDisk | Parametri del disco gestito
name    | Nome del disco
osType  | Tipo di sistema operativo incluso nel disco
vhd     | Disco rigido virtuale
writeAcceleratorEnabled | Indica se `writeAccelerator` il disco è abilitato o meno.

L'array di dischi dati contiene un elenco di dischi dati collegati alla macchina virtuale. Ogni oggetto disco dati contiene le informazioni seguenti:

Dati    | Descrizione
--------|-----------------
caching | Requisiti per la memorizzazione nella cache
createOption | Informazioni sul modo in cui è stata creata la macchina virtuale
diffDiskSettings | Impostazioni disco temporaneo
diskSizeGB | Dimensioni del disco in GB
image   | Disco rigido virtuale dell'immagine utente di origine
lun     | Numero di unità logica del disco
managedDisk | Parametri del disco gestito
name    | Nome del disco
vhd     | Disco rigido virtuale
writeAcceleratorEnabled | Indica se `writeAccelerator` il disco è abilitato o meno.

Nell'esempio seguente viene illustrato come eseguire una query sulle informazioni di archiviazione della macchina virtuale.

**Richiesta**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01
```

**Risposta**

> [!NOTE]
> La risposta è una stringa JSON. La risposta di esempio che segue è di tipo pretty-print per una migliore leggibilità.

```json
{
    "dataDisks": [
      {
        "caching": "None",
        "createOption": "Empty",
        "diskSizeGB": "1024",
        "image": {
          "uri": ""
        },
        "lun": "0",
        "managedDisk": {
          "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
          "storageAccountType": "Standard_LRS"
        },
        "name": "exampledatadiskname",
        "vhd": {
          "uri": ""
        },
        "writeAcceleratorEnabled": "false"
      }
    ],
    "imageReference": {
      "id": "",
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04.0-LTS",
      "version": "latest"
    },
    "osDisk": {
      "caching": "ReadWrite",
      "createOption": "FromImage",
      "diskSizeGB": "30",
      "diffDiskSettings": {
        "option": "Local"
      },
      "encryptionSettings": {
        "enabled": "false"
      },
      "image": {
        "uri": ""
      },
      "managedDisk": {
        "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
        "storageAccountType": "Standard_LRS"
      },
      "name": "exampleosdiskname",
      "osType": "Linux",
      "vhd": {
        "uri": ""
      },
      "writeAcceleratorEnabled": "false"
    }
}
```

## <a name="vm-tags"></a>Tag della macchina virtuale

I tag delle macchine virtuali sono inclusi nell'endpoint dell'API dell'istanza `instance/compute/tags` .
È possibile che i tag siano stati applicati alla macchina virtuale di Azure per organizzarli in modo logico in una tassonomia. È possibile recuperare i tag assegnati a una macchina virtuale usando la richiesta seguente.

**Richiesta**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Risposta**

```text
Department:IT;Environment:Test;Role:WebRole
```

Il campo `tags` è una stringa con i tag delimitati da punto e virgola. Questo output può essere un problema se i punti e virgola vengono usati nei tag stessi. Se viene scritto un parser per estrarre i tag a livello di codice, è consigliabile utilizzare il `tagsList` campo. Il `tagsList` campo è una matrice JSON senza delimitatori e, di conseguenza, è più facile da analizzare.

**Richiesta**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04
```

**Risposta**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

## <a name="attested-data"></a>Dati attestati

IMDS consente di garantire che i dati forniti provengano da Azure. Microsoft firma parte di queste informazioni, quindi è possibile verificare che un'immagine in Azure Marketplace sia quella in esecuzione in Azure.

### <a name="sample-1-get-attested-data"></a>Esempio 1: ottenere i dati attestati

> [!NOTE]
> Tutte le risposte delle API sono stringhe JSON. Le risposte di esempio seguenti sono riformattate per offrire una migliore leggibilità.

**Richiesta**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

> [!NOTE]
> A causa del meccanismo di memorizzazione nella cache di IMDS, `nonce` potrebbe essere restituito un valore precedentemente memorizzato nella cache.

`Api-version` è un campo obbligatorio. Vedere la [sezione sull'uso](#usage) per le versioni API supportate.
`Nonce` è una stringa facoltativa di 10 cifre. Se non viene specificato, IMDS restituisce il timestamp UTC (Coordinated Universal Time) corrente al suo posto.

**Response**.

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Il BLOB della firma è una versione con firma [PKCS7](https://aka.ms/pkcs7)del documento. Contiene il certificato usato per la firma, insieme ad alcuni dettagli specifici della VM. 

Per le macchine virtuali create con Azure Resource Manager, sono incluse `vmId` ,, `sku` `nonce` , `subscriptionId` , per la `timeStamp` creazione e la scadenza del documento e le informazioni sul piano sull'immagine. Le informazioni sul piano vengono popolate solo per le immagini di Azure Marketplace. 

Per le macchine virtuali create con il modello di distribuzione classica, `vmId` è garantita solo la popolazione. È possibile estrarre il certificato dalla risposta e usarlo per verificare che la risposta sia valida e che provenga da Azure.

Il documento contiene i campi seguenti:

Dati | Descrizione | Versione introdotta
-----|-------------|-----------------------
licenseType | Tipo di licenza per [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit). Si noti che questo è presente solo per le macchine virtuali abilitate per vantaggio Azure Hybrid. | 2020-09-01
nonce | Stringa che può essere fornita facoltativamente con la richiesta. Se non `nonce` è stato specificato alcun parametro, viene utilizzato il timestamp UTC (Coordinated Universal Time) corrente. | 2018-10-01
piano | Il [piano immagine di Azure Marketplace](/rest/api/compute/virtualmachines/createorupdate#plan). Contiene l'ID del piano (nome), l'immagine del prodotto o l'offerta (prodotto) e l'ID editore (editore). | 2018-10-01
timestamp/createdOn | Timestamp dell'ora UTC (Coordinated Universal Time) per la creazione del documento firmato. | 2018-20-01
timestamp/expiresOn | Timestamp dell'ora UTC (Coordinated Universal Time) per la scadenza del documento firmato. | 2018-10-01
vmId |  [Identificatore univoco](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) per la macchina virtuale. | 2018-10-01
subscriptionId | Sottoscrizione di Azure per la macchina virtuale. | 2019-04-30
sku | SKU specifico per l'immagine di macchina virtuale. | 2019-11-01

### <a name="sample-2-validate-that-the-vm-is-running-in-azure"></a>Esempio 2: verificare che la macchina virtuale sia in esecuzione in Azure

I fornitori di Azure Marketplace vogliono garantire che il software sia concesso in licenza per l'esecuzione solo in Azure. Se un utente copia il disco rigido virtuale in un ambiente locale, il fornitore deve essere in grado di rilevarlo. Tramite IMDS, questi fornitori possono ottenere dati firmati che garantiscono la risposta solo da Azure.

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

Verificare che la firma provenga da Microsoft Azure e verificare la presenza di errori nella catena di certificati.

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

> [!NOTE]
> A causa del meccanismo di memorizzazione nella cache di IMDS, `nonce` potrebbe essere restituito un valore precedentemente memorizzato nella cache.

Nel `nonce` documento firmato può essere confrontato se è stato specificato un `nonce` parametro nella richiesta iniziale.

> [!NOTE]
> Il certificato per il cloud pubblico e ogni cloud sovrano sarà diverso.

Cloud | Certificato
------|------------
[Tutte le aree globali di Azure disponibili a livello generale](https://azure.microsoft.com/regions/) | *.metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)          | *.metadata.azure.us
[21Vianet per Azure Cina](https://azure.microsoft.com/global-infrastructure/china/)     | *.metadata.azure.cn
[Azure Germania](https://azure.microsoft.com/overview/clouds/germany/)                | *.metadata.microsoftazure.de

> [!NOTE]
> I certificati potrebbero non corrispondere esattamente `metadata.azure.com` a per il cloud pubblico. Per questo motivo, la convalida della certificazione dovrebbe consentire un nome comune da qualsiasi `.metadata.azure.com` sottodominio.

Nei casi in cui non è possibile scaricare il certificato intermedio a causa di vincoli di rete durante la convalida, è possibile aggiungere il certificato intermedio. Si noti che Azure esegue il rollup dei certificati, ovvero la pratica PKI standard. È necessario aggiornare i certificati aggiunti quando si verifica il rollover. Ogni volta che viene pianificata una modifica per l'aggiornamento del certificato intermedio, il Blog di Azure viene aggiornato e i clienti di Azure ricevono una notifica. 

È possibile trovare i certificati intermedi nel [repository PKI](https://www.microsoft.com/pki/mscorp/cps/default.htm). I certificati intermedi per ognuna delle aree possono essere diversi.

> [!NOTE]
> Il certificato intermedio per Azure Cina 21Vianet è dalla CA radice globale DigiCert anziché da Baltimora.
Se sono stati aggiunti i certificati intermedi per Azure Cina come parte di una modifica dell'autorità di catena principale, è necessario aggiornare i certificati intermedi.

## <a name="failover-clustering-in-windows-server"></a>Clustering di failover in Windows Server

Quando si esegue una query su IMDS con il clustering di failover, è talvolta necessario aggiungere una route alla tabella di routing. Ecco come:

1. Aprire un prompt dei comandi con privilegi di amministratore.

1. Eseguire il comando seguente e prendere nota dell'indirizzo dell'interfaccia per la destinazione di rete ( `0.0.0.0` ) nella tabella di route IPv4.

```bat
route print
```

> [!NOTE]
> L'output di esempio seguente è da una macchina virtuale Windows Server con cluster di failover abilitato. Per semplicità, l'output contiene solo la tabella di route IPv4.

```text
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

Eseguire il comando seguente e usare l'indirizzo dell'interfaccia per la destinazione di rete ( `0.0.0.0` ), ovvero ( `10.0.1.10` ) in questo esempio.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

## <a name="managed-identity"></a>Identità gestita

Un'identità gestita, assegnata dal sistema, può essere abilitata nella macchina virtuale. È anche possibile assegnare una o più identità gestite assegnate dall'utente alla macchina virtuale.
È quindi possibile richiedere token per le identità gestite da IMDS. Usare questi token per eseguire l'autenticazione con altri servizi di Azure, ad esempio Azure Key Vault.

Per i passaggi dettagliati per abilitare questa funzionalità, vedere [Acquisire un token di accesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="scheduled-events"></a>Eventi pianificati
È possibile ottenere lo stato degli eventi pianificati usando IMDS. L'utente può quindi specificare un set di azioni da eseguire su questi eventi. Per ulteriori informazioni, vedere [eventi pianificati](scheduled-events.md). 

## <a name="regional-availability"></a>Disponibilità a livello di area

Il servizio è disponibile a livello generale in tutti i cloud di Azure.

## <a name="sample-code-in-different-languages"></a>Codice di esempio in linguaggi diversi

La tabella seguente elenca esempi di chiamata a IMDS usando linguaggi diversi all'interno della macchina virtuale:

Linguaggio      | Esempio
--------------|----------------
C++ (Windows) | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Go            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
NodeJS        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Visual Basic  | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb

## <a name="errors-and-debugging"></a>Errori e debug

Se è presente un elemento dati non trovato o una richiesta in formato non valido, IMDS restituisce errori HTTP standard. Ad esempio:

Codice di stato HTTP | Motivo
-----------------|-------
200 - OK |
400 - Richiesta non valida | `Metadata: true`Intestazione mancante o parametro mancante `format=json` durante l'esecuzione di una query su un nodo foglia.
404 - Non trovato  | L'elemento richiesto non esiste.
405 - Metodo non consentito | `GET`Sono supportate solo le richieste.
410 - Non disponibile | Riprovare tra qualche istante per un massimo di 70 secondi.
429 - Numero eccessivo di richieste | L'API supporta attualmente un massimo di 5 query al secondo.
500 - Errore del servizio     | Riprovare tra qualche minuto.

### <a name="frequently-asked-questions"></a>Domande frequenti

**Ricevo l'errore `400 Bad Request, Required metadata header not specified` . Cosa significa?**

IMDS richiede che l'intestazione `Metadata: true` venga passata nella richiesta. Il passaggio di questa intestazione nella chiamata REST consente l'accesso a IMDS.

**Perché non riesco a ottenere le informazioni di calcolo per la macchina virtuale?**

Attualmente, IMDS supporta solo le istanze create con Azure Resource Manager.

**La macchina virtuale è stata creata tramite Azure Resource Manager tempo fa. Perché non si vedono le informazioni sui metadati di calcolo?**

Se la macchina virtuale è stata creata dopo il 2016 settembre, aggiungere un [tag](../../azure-resource-manager/management/tag-resources.md) per iniziare a visualizzare i metadati di calcolo. Se la macchina virtuale è stata creata prima del 2016 settembre, aggiungere o rimuovere estensioni o dischi dati nell'istanza di macchina virtuale per aggiornare i metadati.

**Perché non vengono visualizzati tutti i dati popolati per una nuova versione?**

Se la macchina virtuale è stata creata dopo il 2016 settembre, aggiungere un [tag](../../azure-resource-manager/management/tag-resources.md) per iniziare a visualizzare i metadati di calcolo. Se la macchina virtuale è stata creata prima del 2016 settembre, aggiungere o rimuovere estensioni o dischi dati nell'istanza di macchina virtuale per aggiornare i metadati.

**Perché viene ricevuto l'errore `500 Internal Server Error` o `410 Resource Gone` ?**

Ripetere la richiesta. Per ulteriori informazioni, vedere [gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults). Se il problema persiste, creare un problema di supporto nella portale di Azure per la macchina virtuale.

**Questo funziona per le istanze del set di scalabilità di macchine virtuali?**

Sì, IMDS è disponibile per le istanze del set di scalabilità di macchine virtuali.

**I tag sono stati aggiornati nei set di scalabilità di macchine virtuali, ma non vengono visualizzati nelle istanze (a differenza delle macchine virtuali a istanza singola). Si è verificato un errore?**

Attualmente i tag per i set di scalabilità di macchine virtuali vengono visualizzati solo alla macchina virtuale al riavvio, alla ricreazione dell'immagine o alla modifica del disco nell'istanza.

**Perché si è verificato un timeout della richiesta per la chiamata al servizio?**

Le chiamate ai metadati devono essere effettuate dall'indirizzo IP primario assegnato alla scheda di rete primaria della macchina virtuale. Inoltre, se sono state modificate le route, è necessario che sia presente una route per l'indirizzo 169.254.169.254/32 nella tabella di routing locale della macchina virtuale.
   * <details>
        <summary>Verifica della tabella di routing</summary>

        1. Esegui il dump della tabella di routing locale e cerca la voce IMDS. Ad esempio:
            ```console
            > route print
            IPv4 Route Table
            ===========================================================================
            Active Routes:
            Network Destination        Netmask          Gateway       Interface  Metric
                      0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                    127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                    127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
              127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
                168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
              169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
            ... (continues) ...
            ```
        1. Verificare che esista una route per `169.254.169.254` e annotare l'interfaccia di rete corrispondente (ad esempio, `172.16.69.7` ).
        1. Eseguire il dump della configurazione dell'interfaccia e trovare l'interfaccia che corrisponde a quella a cui viene fatto riferimento nella tabella di routing, annotando l'indirizzo MAC (fisico).
            ```console
            > ipconfig /all
            ... (continues) ...
            Ethernet adapter Ethernet:

               Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
               Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
               Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
               DHCP Enabled. . . . . . . . . . . : Yes
               Autoconfiguration Enabled . . . . : Yes
               Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
               IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
               Subnet Mask . . . . . . . . . . . : 255.255.255.0
            ... (continues) ...
            ```
        1. Verificare che l'interfaccia corrisponda alla scheda di interfaccia di rete primaria e all'indirizzo IP primario della macchina virtuale. È possibile trovare la scheda di interfaccia di rete e l'indirizzo IP primari osservando la configurazione di rete nel portale di Azure oppure cercandolo con l'interfaccia della riga di comando di Azure. Prendere nota degli indirizzi IP pubblici e privati (e dell'indirizzo MAC se si usa l'interfaccia della riga di comando). Ecco un esempio di interfaccia della riga di comando di PowerShell:
            ```powershell
            $ResourceGroup = '<Resource_Group>'
            $VmName = '<VM_Name>'
            $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
            foreach($NicName in $NicNames)
            {
                $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
                Write-Host $NicName, $Nic.primary, $Nic.macAddress
            }
            # Output: wintest767 True 00-0D-3A-E5-1C-C0
            ```
        1. Se non corrispondono, aggiornare la tabella di routing in modo che la scheda di interfaccia di rete primaria e l'IP siano assegnati.
    </details>

## <a name="support"></a>Supporto

Se non si riesce a ottenere una risposta ai metadati dopo più tentativi, è possibile creare un problema di supporto nella portale di Azure.
Per **tipo di problema** selezionare **gestione**. Per **Category** selezionare **servizio metadati dell'istanza**.

![Screenshot del supporto del servizio metadati dell'istanza](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Passaggi successivi

[Acquisire un token di accesso per la macchina virtuale](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[Eventi pianificati](scheduled-events.md)
