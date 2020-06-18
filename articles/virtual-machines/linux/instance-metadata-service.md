---
title: Servizio metadati dell'istanza di Azure
description: Interfaccia RESTful per ottenere informazioni sugli eventi di calcolo, di rete e di manutenzione previsti di una macchina virtuale Linux.
services: virtual-machines-linux
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines-linux
ms.subservice: monitoring
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/29/2020
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: ce3463d39b17e7099f85945caa92d1f009b696c0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649842"
---
# <a name="azure-instance-metadata-service"></a>Servizio metadati dell'istanza di Azure

Il servizio metadati dell'istanza di Azure (IMDS) fornisce informazioni sulle istanze di macchine virtuali attualmente in esecuzione che possono essere usate per gestire e configurare le macchine virtuali.
Sono inclusi SKU, archiviazione, configurazioni di rete e gli eventi di manutenzione previsti. Per un elenco completo dei dati disponibili, vedere [API dei metadati](#metadata-apis). Il servizio metadati dell'istanza è disponibile per le istanze della macchina virtuale e del set di scalabilità di macchine virtuali. È disponibile solo per le macchine virtuali in esecuzione create/gestite tramite [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). 

Il servizio metadati dell'istanza di Azure è un endpoint REST disponibile in un indirizzo IP non instradabile noto (`169.254.169.254`), a cui è possibile accedere solo dall'interno della macchina virtuale.

## <a name="security"></a>Sicurezza

L'endpoint del Servizio metadati dell'istanza è accessibile solo dall'istanza della macchina virtuale in esecuzione su un indirizzo IP non instradabile. Inoltre, qualsiasi richiesta con intestazione `X-Forwarded-For` viene rifiutata dal servizio.
È necessario anche che le richieste includano l'intestazione `Metadata: true` per garantire che la richiesta sia stata destinata direttamente e non faccia parte di un reindirizzamento non intenzionale.

> [!IMPORTANT]
> Il servizio metadati dell'istanza non è un canale per dati sensibili. L'endpoint è aperto a tutti i processi nella macchina virtuale. Le informazioni esposte tramite questo servizio devono essere considerate come informazioni condivise a tutte le applicazioni in esecuzione nella macchina virtuale.

## <a name="usage"></a>Uso

### <a name="accessing-azure-instance-metadata-service"></a>Accesso al servizio metadati dell'istanza di Azure

Per accedere al servizio metadati dell'istanza, creare una macchina virtuale in [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) o nel [portale di Azure](https://portal.azure.com) e seguire questi esempi.
Altri esempi sul modo di eseguire una query su IMDS sono disponibili in [Esempi di metadati dell'istanza di Azure](https://github.com/microsoft/azureimds).

Di seguito viene indicato il codice di esempio per recuperare tutti i metadati per un'istanza. Per accedere a un'origine dati specifica, vedere la sezione [API dei metadati](#metadata-apis). 

**Richiesta**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-06-01"
```

**Risposta**

> [!NOTE]
> La risposta è una stringa JSON. La risposta di esempio che segue è di tipo pretty-print per una migliore leggibilità.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
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
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Prod;Role:WorkerRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

### <a name="data-output"></a>Output dei dati

Per impostazione predefinita, il Servizio metadati dell'istanza restituisce i dati in formato JSON (`Content-Type: application/json`). Tuttavia, API diverse restituiscono dati in formati diversi se necessario.
La tabella seguente costituisce un riferimento per gli altri formati di dati che le API possono supportare.

API | Formato dati predefinito | Altri formati
--------|---------------------|--------------
/instance | json | text
/scheduledevents | json | none
/attested | json | none

Per accedere a un formato di risposta non predefinito, specificare il formato richiesto come parametro della stringa di query nella richiesta. Ad esempio:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Per i nodi foglia, `format=json` non funziona. Per le query `format=text` è necessario specificare in modo esplicito se il formato predefinito è json.

### <a name="versioning"></a>Controllo delle versioni

Il servizio metadati dell'istanza è associato a una versione e la specifica della versione API nella richiesta HTTP è obbligatoria.

Le versioni del servizio supportate sono le seguenti: 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15.

Si noti che quando viene rilasciata una nuova versione, la distribuzione in tutte le aree richiederà del tempo. Attualmente la versione 2019-11-01 è ancora in fase di distribuzione e potrebbe non essere disponibile in tutte le aree.

Quando vengono aggiunte versioni più recenti, quelle precedenti rimangono comunque accessibili per la compatibilità, se gli script presentano dipendenze in formati di dati specifici.

Quando non viene specificata alcuna versione, viene restituito un errore con un elenco delle versioni supportate più recenti.

> [!NOTE]
> La risposta è una stringa JSON. Nell'esempio seguente viene indicata la condizione di errore quando la versione non è specificata e la risposta è pretty-print per migliorare la leggibilità.

**Richiesta**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
```

**Risposta**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2018-10-01",
        "2018-04-02",
        "2018-02-01"
    ]
}
```

## <a name="metadata-apis"></a>API dei metadati
Il servizio IMDS contiene più interfacce API che rappresentano origini dati diverse. 

Dati | Descrizione | Versione introdotta
-----|-------------|-----------------------
instance | Vedere [API dell'istanza](#instance-api) | 2017-04-02
attested | Vedere [Dati con attestazione](#attested-data) | 2018-10-01
identity | Vedere [Acquisire un token di accesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
scheduledevents | Vedere [Eventi pianificati](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>API dell'istanza

L'API dell'istanza espone i metadati importanti per le istanze di macchina virtuale, tra cui la macchina virtuale, la rete e l'archiviazione. È possibile accedere categorie seguenti tramite instance/compute:

Dati | Descrizione | Versione introdotta
-----|-------------|-----------------------
azEnvironment | Ambiente di Azure in cui è in esecuzione la macchina virtuale | 2018-10-01
customData | Questa funzionalità è attualmente disabilitata e la documentazione verrà aggiornata quando diventerà disponibile | 2019-02-01
posizione | Area di Azure in cui la macchina virtuale è in esecuzione | 2017-04-02
name | Nome della VM | 2017-04-02
offer | Offre informazioni per l'immagine di macchina virtuale ed è presente solo per le immagini distribuite dalla raccolta immagini di Azure | 2017-04-02
osType | Linux o Windows | 2017-04-02
placementGroupId | [Gruppo di posizionamento](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) del set di scalabilità di macchine virtuali | 2017-08-01
piano | [Piano](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) che contiene il nome, il prodotto e il server di pubblicazione per una macchina virtuale se si tratta di un'immagine di Azure Marketplace | 2018-04-02
platformUpdateDomain |  [Dominio di aggiornamento](manage-availability.md) in cui è in esecuzione la macchina virtuale | 2017-04-02
platformFaultDomain | [Dominio di errore](manage-availability.md) in cui è in esecuzione la macchina virtuale | 2017-04-02
provider | Provider della macchina virtuale | 2018-10-01
publicKeys | [Raccolta di chiavi pubbliche](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) assegnate alla macchina virtuale e ai percorsi | 2018-04-02
publisher | Autore dell'immagine della macchina virtuale | 2017-04-02
resourceGroupName | [Gruppo di risorse](../../azure-resource-manager/management/overview.md) per la macchina virtuale | 2017-08-01
resourceId | ID [completo](https://docs.microsoft.com/rest/api/resources/resources/getbyid) della risorsa | 2019-03-11
sku | SKU specifica per l'immagine della macchina virtuale | 2017-04-02
storageProfile | Vedere [Profilo di archiviazione](#storage-metadata) | 01/06/2019
subscriptionId | Sottoscrizione di Azure per la macchina virtuale | 2017-08-01
tags | [Tag](../../azure-resource-manager/management/tag-resources.md) per la macchina virtuale  | 2017-08-01
tagsList | Tag formattati come matrice JSON per un'analisi più semplice a livello programmatico  | 2019-06-04
version | Versione dell'immagine della macchina virtuale | 2017-04-02
vmId | [Identificatore univoco](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) della macchina virtuale | 2017-04-02
vmScaleSetName | [Nome del set di scalabilità di macchine virtuali](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) del proprio set di scalabilità di macchine virtuali | 2017-12-01
vmSize | [Dimensioni macchina virtuale](sizes.md) | 2017-04-02
zona | [Zona di disponibilità](../../availability-zones/az-overview.md) della macchina virtuale | 2017-12-01

### <a name="sample-1-tracking-vm-running-on-azure"></a>Esempio 1. Rilevamento della macchina virtuale in esecuzione in Azure

Come provider di servizi, potrebbe essere necessario tenere traccia del numero di macchine virtuali che eseguono il proprio software o avere agenti che devono verificare l'univocità della macchina virtuale. Per poter ottenere un ID univoco per una macchina virtuale, usare il campo `vmId` dal Servizio metadati dell'istanza.

**Richiesta**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Risposta**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-containers-data-partitions-based-faultupdate-domain"></a>Esempio 2: Posizionamento di contenitori, dominio di aggiornamento/errore basato su partizioni dati

Per alcuni scenari, il posizionamento di repliche dati diverse è di importanza primaria. Ad esempio per il [posizionamento delle repliche HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) o per il posizionamento di contenitori tramite un [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) è necessario conoscere il `platformFaultDomain` e il `platformUpdateDomain` in cui la macchina virtuale è in esecuzione.
Per prendere decisioni di questo tipo è anche possibile basarsi sulle [zone di disponibilità](../../availability-zones/az-overview.md) per le istanze.
È possibile eseguire query su questi dati direttamente tramite il Servizio metadati dell'istanza.

**Richiesta**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Risposta**

```text
0
```

### <a name="sample-3-getting-more-information-about-the-vm-during-support-case"></a>Esempio 3: Ottenere altre informazioni sulla macchina virtuale durante la richiesta di assistenza

Come provider di servizi è possibile ricevere una chiamata di supporto per la quale occorre sapere altre informazioni sulla macchina virtuale. Chiedere al cliente di condividere i metadati di calcolo può risultare utile per avere informazioni di base che consentano al personale del supporto tecnico di conoscere il tipo di macchina virtuale in Azure.

**Richiesta**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01"
```

**Risposta**

> [!NOTE]
> La risposta è una stringa JSON. La risposta di esempio che segue è di tipo pretty-print per una migliore leggibilità.

```json
{
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
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
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
}
```

### <a name="sample-4-getting-azure-environment-where-the-vm-is-running"></a>Esempio 4: Ottenere l'ambiente di Azure in cui è in esecuzione la macchina virtuale

Azure offre vari cloud sovrani, ad esempio [Azure per enti pubblici](https://azure.microsoft.com/overview/clouds/government/). In taluni casi, per alcune decisioni di runtime è necessario l'ambiente di Azure. L'esempio seguente illustra come è possibile ottenere questo comportamento.

**Richiesta**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Risposta**

```bash
AzurePublicCloud
```

Il cloud e i valori dell'ambiente di Azure sono elencati di seguito.

 Cloud   | Ambiente Azure
---------|-----------------
[Tutte le aree globali di Azure con disponibilità a livello generale](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[21Vianet per Azure Cina](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Germania](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>Metadati di rete 

I metadati di rete fanno parte dell'API dell'istanza. Le categorie di rete seguenti sono disponibili tramite l'endpoint instance/network.

Dati | Descrizione | Versione introdotta
-----|-------------|-----------------------
ipv4/privateIpAddress | Indirizzo IPv4 locale della macchina virtuale | 2017-04-02
ipv4/publicIpAddress | Indirizzo IPv4 pubblico della macchina virtuale | 2017-04-02
subnet/address | Indirizzo della subnet della macchina virtuale | 2017-04-02
subnet/prefix | Prefisso della subnet, ad esempio 24 | 2017-04-02
ipv6/ipAddress | Indirizzo IPv6 locale della macchina virtuale | 2017-04-02
macAddress | Indirizzo mac della macchina virtuale | 2017-04-02

> [!NOTE]
> Tutte le risposte delle API sono stringhe JSON. Tutte le risposte di esempio che seguono sono di tipo pretty-print per una migliore leggibilità.

#### <a name="sample-1-retrieving-network-information"></a>Esempio 1. Recupero delle informazioni di rete

***Richiesta***

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

***Risposta***

> [!NOTE]
> La risposta è una stringa JSON. La risposta di esempio che segue è di tipo pretty-print per una migliore leggibilità.

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

#### <a name="sample-2-retrieving-public-ip-address"></a>Esempio 2: Recupero dell'indirizzo IP pubblico

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"

```

## <a name="storage-metadata"></a>Metadati di archiviazione

I metadati di archiviazione fanno parte dell'API dell'istanza in un endpoint instance/compute/storageProfile.
Forniscono informazioni dettagliate sui dischi di archiviazione associati alla macchina virtuale. 

Il profilo di archiviazione di una macchina virtuale è suddiviso in tre categorie, ovvero riferimento immagine, disco del sistema operativo e dischi dati.

L'oggetto di riferimento immagine contiene le informazioni seguenti sull'immagine del sistema operativo:

Dati    | Descrizione
--------|-----------------
id      | ID risorsa
offer   | Offerta dell'immagine della piattaforma o del marketplace
publisher | Editore immagine
sku     | SKU dell'immagine
version | Versione dell'immagine della piattaforma o del marketplace

L'oggetto disco del sistema operativo contiene le informazioni seguenti sul disco del sistema operativo usato dalla macchina virtuale:

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
writeAcceleratorEnabled | Indica se writeAccelerator è abilitato sul disco

L'array di dischi dati contiene un elenco di dischi dati collegati alla macchina virtuale. Ogni oggetto disco dati contiene le informazioni seguenti:

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
writeAcceleratorEnabled | Indica se writeAccelerator è abilitato sul disco

Di seguito è riportato un esempio di come eseguire una query sulle informazioni di archiviazione della macchina virtuale.

**Richiesta**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
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

I tag delle macchine virtuali sono inclusi nell'endpoint instance/compute/tags dell'API.
È possibile che i tag siano stati applicati alla macchina virtuale di Azure per essere organizzati in modo logico in una tassonomia. I tag assegnati a una macchina virtuale possono essere recuperati tramite la richiesta seguente.

**Richiesta**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Risposta**

```text
Department:IT;Environment:Test;Role:WebRole
```

Il campo `tags` è una stringa con i tag delimitati da punto e virgola. Ciò può costituire un problema se il punto e virgola viene usati nei tag stessi. Se viene scritto un parser per estrarre i tag a livello programmatico, è necessario basarsi sul campo `tagsList`, che è una matrice JSON senza delimitatori e, di conseguenza, più semplice da analizzare.

**Richiesta**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04&format=json"
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

## <a name="attested-data"></a>Dati con attestazione

Parte dello scenario gestito dal servizio metadati dell'istanza consiste nel garantire che i dati forniti provengano da Azure. Una parte di queste informazioni viene firmata, in modo da fornire una garanzia per le immagini del marketplace che l'immagine è in esecuzione in Azure.

### <a name="sample-1-getting-attested-data"></a>Esempio 1. Recupero di dati con attestazione

> [!NOTE]
> Tutte le risposte delle API sono stringhe JSON. Le risposte di esempio seguenti sono riformattate per offrire una migliore leggibilità.

***Richiesta***

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

La versione dell'API è un campo obbligatorio. Vedere la [sezione sull'uso](#usage) per le versioni API supportate.
Nonce è una stringa facoltativa di 10 cifre. Se non specificata, IMDS restituisce il timestamp UTC corrente al suo posto. A causa del meccanismo di memorizzazione nella cache di IMDS, è possibile che venga restituito un valore nonce precedentemente memorizzato nella cache.

 ***Risposta***

> [!NOTE]
> La risposta è una stringa JSON. La risposta di esempio che segue è di tipo pretty-print per una migliore leggibilità.

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Il BLOB di firma è una versione [pkcs7](https://aka.ms/pkcs7) firmata del documento. Contiene il certificato usato per la firma insieme ai dettagli della macchina virtuale, ad esempio gli elementi vmId, sku, nonce, subscriptionId e timeStamp, per la creazione e la scadenza del documento e le informazioni sul piano relative all'immagine. Le informazioni sul piano vengono popolate solo per le immagini di Azure Marketplace. Il certificato può essere estratto dalla risposta e usato per verificare che la risposta sia valida e provenga da Azure.

### <a name="sample-2-validating-that-the-vm-is-running-in-azure"></a>Esempio 2: Verifica dell'esecuzione della macchina virtuale in Azure

I fornitori di Marketplace vogliono garantire che il software venga concesso in licenza solo per l'esecuzione in Azure. È necessario un modo per rilevare se qualcuno copia il disco rigido virtuale in locale. Con una chiamata al servizio metadati dell'istanza, i fornitori di Marketplace possono ottenere i dati firmati che garantiscono la provenienza della risposta solo da Azure.

> [!NOTE]
> Richiede l'installazione di jq.

***Richiesta***

```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2019-04-30 | jq -r '.["signature"]' > signature
  # Decode the signature
  base64 -d signature > decodedsignature
  #Get PKCS7 format
  openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
  # Get Public key out of pkc7
  openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
  #Get the intermediate certificate
  wget -q -O intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
  openssl x509 -inform der -in intermediate.cer -out intermediate.pem
  #Verify the contents
  openssl smime -verify -in sign.pk7 -inform pem -noverify
 ```

 **Risposta**

```json
Verification successful
{"nonce":"20181128-001617",
  "plan":
    {
     "name":"",
     "product":"",
     "publisher":""
    },
"timeStamp":
  {
    "createdOn":"11/28/18 00:16:17 -0000",
    "expiresOn":"11/28/18 06:16:17 -0000"
  },
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34",
"subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
"sku": "RS3-Pro"
}
```

Dati | Descrizione
-----|------------
nonce | Stringa facoltativa fornita dall'utente con la richiesta. Se nella richiesta non è presente l'elemento nonce, viene restituito il timestamp UTC corrente
piano | [Piano](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) per una macchina virtuale in un'immagine di Azure Marketplace, che contiene nome, prodotto ed editore
timestamp/createdOn | Timestamp UTC relativo alla creazione del primo documento firmato
timestamp/expiresOn | Timestamp UTC relativo alla scadenza del documento firmato
vmId |  [Identificatore univoco](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) della macchina virtuale
subscriptionId | Sottoscrizione di Azure per la macchina virtuale, introdotta in `2019-04-30`
sku | SKU specifica per l'immagine di macchina virtuale, introdotta in `2019-11-01`

#### <a name="sample-3-verifying-the-signature"></a>Esempio 3: Verifica della firma

Dopo aver ottenuto la firma sopra indicata, è possibile verificare che provenga da Microsoft. È anche possibile verificare il certificato intermedio e la catena di certificati. È infine possibile verificare che l'ID sottoscrizione sia corretto.

> [!NOTE]
> Il certificato per il cloud pubblico e il cloud sovrano sarà diverso.

 Cloud | Certificato
---------|-----------------
[Tutte le aree globali di Azure con disponibilità a livello generale](https://azure.microsoft.com/regions/)     | *.metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | *.metadata.azure.us
[21Vianet per Azure Cina](https://azure.microsoft.com/global-infrastructure/china/)         | *.metadata.azure.cn
[Azure Germania](https://azure.microsoft.com/overview/clouds/germany/)                    | *.metadata.microsoftazure.de

Esiste un problema noto relativo al certificato usato per la firma. I certificati possono non corrispondere esattamente a `metadata.azure.com` per il cloud pubblico. Di conseguenza, la convalida della certificazione deve consentire un nome comune da qualsiasi sottodominio `.metadata.azure.com`.

```bash

# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

Nei casi in cui non sia possibile scaricare il certificato intermedio a causa di vincoli di rete durante la convalida, il certificato intermedio può essere aggiunto. Azure tuttavia eseguirà il rollover dei certificati in base alle procedure standard di infrastruttura a chiave pubblica. I certificati aggiunti devono essere aggiornati quando viene eseguito il rollover. Ogni volta che viene pianificata una modifica per l'aggiornamento del certificato intermedio, il blog di Azure verrà aggiornato e i clienti di Azure riceveranno una notifica. I certificati intermedi sono disponibili [qui](https://www.microsoft.com/pki/mscorp/cps/default.htm). I certificati intermedi per ognuna delle aree possono essere diversi.

> [!NOTE]
>Il certificato intermedio per 21Vianet per Azure Cina proverrà da DigiCert Global Root CA anziché da Baltimora.
Se sono stati aggiunti i certificati intermedi per Azure Cina come parte della modifica dell'autorità della catena radice, sarà necessario aggiornare i certificati intermedi.

## <a name="managed-identity-via-metadata-service"></a>Identità gestita tramite il servizio metadati
L'utente può abilitare l'identità gestita in una macchina virtuale e quindi sfruttare il servizio metadati dell'istanza per passare il token per accedere ai servizi di Azure. Le applicazioni in esecuzione in una macchina virtuale ora possono richiedere un token dall'endpoint del servizio metadati dell'istanza di Azure e quindi usarlo per eseguire l'autenticazione nei servizi cloud, incluso l'insieme di credenziali delle chiavi.
Per i passaggi dettagliati per abilitare questa funzionalità, vedere [Acquisire un token di accesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="scheduled-events-via-metadata-service"></a>Eventi pianificati tramite il servizio metadati
È possibile ottenere lo stato degli eventi pianificati tramite il servizio metadati, quindi l'utente può specificare un set di azioni da eseguire su questi eventi.  Per informazioni dettagliate, vedere [Eventi pianificati](scheduled-events.md). 

## <a name="regional-availability"></a>Disponibilità a livello di area

Il servizio è **disponibile a livello generale** in tutte le aree di Azure. Esempio: 
1. [Tutte le aree globali di Azure con disponibilità a livello generale](https://azure.microsoft.com/regions/)
2. [Azure Government](https://azure.microsoft.com/overview/clouds/government/)  
3. [21Vianet per Azure Cina](https://www.azure.cn/) 
4. [Azure Germania](https://azure.microsoft.com/overview/clouds/germany/) 

## <a name="sample-code-in-different-languages"></a>Codice di esempio in linguaggi diversi

Esempi di chiamate al servizio metadati con diversi linguaggi all'interno della macchina virtuale

Linguaggio | Esempio
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Puppet | https://github.com/keirans/azuremetadata

## <a name="error-and-debugging"></a>Errori e debug

In caso di elementi dati non trovati o di richiesta non valida, il Servizio metadati dell'istanza restituisce errori HTTP standard. Ad esempio:

Codice di stato HTTP | Motivo
----------------|-------
200 - OK |
400 - Richiesta non valida | Intestazione `Metadata: true` mancante o formato mancante durante l'esecuzione di query su un nodo foglia
404 - Non trovato | L'elemento richiesto non esiste
405 - Metodo non consentito | Sono supportate solo le richieste `GET`
410 - Non disponibile | Riprovare tra qualche istante per un massimo di 70 secondi
429 - Numero eccessivo di richieste | L'API supporta attualmente un massimo di 5 query al secondo
500 - Errore del servizio     | Ripetere l'operazione in un secondo momento

### <a name="known-issues-and-faq"></a>Problemi noti e domande frequenti

1. Viene visualizzato l'errore `400 Bad Request, Required metadata header not specified`. Che cosa significa?
   * Il Servizio metadati dell'istanza richiede che nella richiesta venga passata l'intestazione `Metadata: true`. Il passaggio di questa intestazione nella chiamata REST consente l'accesso al Servizio metadati dell'istanza.
2. Perché non riesco a ottenere le informazioni di calcolo per la macchina virtuale?
   * Attualmente il Servizio metadati dell'istanza supporta solo le istanze create con Azure Resource Manager. È possibile che in futuro venga aggiunto il supporto per le macchine virtuali del servizio cloud.
3. Ho creato la mia macchina virtuale tramite Azure Resource Manager tempo fa. Perché non riesco a vedere le informazioni sui metadati di calcolo?
   * Per tutte le macchine virtuali create dopo settembre 2016, è necessario aggiungere un [Tag](../../azure-resource-manager/management/tag-resources.md) per iniziare a essere visualizzare i metadati di calcolo. Per le macchine virtuale precedenti (create prima di settembre 2016), è necessario aggiungere o rimuovere estensioni o dischi di dati dalla macchina virtuale per aggiornare i metadati.
4. Non vengono visualizzati tutti i dati popolati per la nuova versione
   * Per tutte le macchine virtuali create dopo settembre 2016, è necessario aggiungere un [Tag](../../azure-resource-manager/management/tag-resources.md) per iniziare a essere visualizzare i metadati di calcolo. Per le macchine virtuale precedenti (create prima di settembre 2016), è necessario aggiungere o rimuovere estensioni o dischi di dati dalla macchina virtuale per aggiornare i metadati.
5. Perché viene visualizzato l'errore `500 Internal Server Error`?
   * Inviare di nuovo la richiesta basata sul sistema di backoff esponenziale. Se il problema persiste, contattare il supporto di Azure.
6. Dove posso condividere domande o commenti aggiuntivi?
   * Inviare i commenti in https://feedback.azure.com.
7. Il servizio funziona per l'istanza del set di scalabilità di macchine virtuali?
   * Sì, il Servizio metadati è disponibile per le istanze del set di scalabilità.
8. Come si ottiene assistenza per il servizio?
   * Per ottenere assistenza per il servizio, creare una richiesta di supporto nel portale di Azure per la macchina virtuale per la quale non si riesce a ottenere la risposta dei metadati dopo lunghi tentativi.
9. Ricevo il timeout della richiesta per la chiamata del servizio?
   * Le chiamate di metadati devono essere effettuate dall'indirizzo IP primario assegnato alla scheda di rete principale della macchina virtuale. Inoltre, nel caso in cui siano state modificate le route, deve essere presente una route per l'indirizzo 169.254.0.0/16 esterna alla scheda di rete.
10. Perché dopo essere stati aggiornati, i tag nel set di scalabilità di macchine virtuali non vengono visualizzati nelle istanze, a differenza delle macchine virtuali?
    * Attualmente per i set di scalabilità i tag vengono visualizzati nella macchina virtuale solo al riavvio/ricreazione dell'immagine/o cambio del disco per l'istanza.

## <a name="support-and-feedback"></a>Supporto tecnico e commenti

Inviare i commenti all'indirizzo https://feedback.azure.com.
Per ottenere assistenza per il servizio, creare una richiesta di supporto nel portale di Azure per la macchina virtuale per la quale non si riesce a ottenere la risposta dei metadati dopo lunghi tentativi.

![Supporto per i metadati dell'istanza](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:
1.  [Acquisire un token di accesso per la macchina virtuale](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).
2.  [Eventi pianificati](scheduled-events.md) 