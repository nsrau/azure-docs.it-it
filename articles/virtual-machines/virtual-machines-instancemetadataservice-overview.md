---
title: Panoramica del Servizio metadati dell&quot;istanza di Azure | Microsoft Docs
description: Interfaccia RESTful per ottenere informazioni sugli eventi di calcolo, di rete e di manutenzione previsti della macchina virtuale.
services: virtual-machines-windows, virtual-machines-linux,virtual-machines-scale-sets, cloud-services
documentationcenter: 
author: harijay
manager: timlt
editor: 
tags: 
ms.service: azure-instancemetadataservice
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: harijay
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 7849537ac85f39f2398648bb6302b610d1abf609
ms.contentlocale: it-it
ms.lasthandoff: 06/17/2017

---

# <a name="azure-instance-metadata-service"></a>Servizio metadati dell'istanza di Azure 


Il Servizio metadati dell'istanza di Azure fornisce informazioni sull'esecuzione delle istanze di macchine virtuali che possono essere utilizzate per gestire e configurare le macchine virtuali.
Le informazioni includono ad esempio SKU, configurazione di rete ed eventi di manutenzione previsti. Per altre informazioni sul tipo di informazioni disponibili, vedere le [categorie di metadati](#instance-metadata-data-categories).

Il Servizio metadati dell'istanza di Azure è un endpoint REST accessibile a tutte le macchine virtuali IaaS create tramite [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). L'endpoint è disponibile a un indirizzo IP non instradabile noto (`169.254.169.254`) a cui è possibile accedere solo dalla macchina virtuale.

### <a name="important-information"></a>Informazioni importanti

Questo servizio è **disponibile a livello generale** nelle aree globali di Azure. È in anteprima pubblica per il cloud di Azure Germania, per la Cina e per enti pubblici. Il servizio riceve regolarmente aggiornamenti per esporre nuove informazioni sulle istanze di macchine virtuali. Questa pagina riporta le [categorie di dati](#instance-metadata-data-categories) attualmente disponibili.

## <a name="service-availability"></a>Disponibilità del servizio
Il servizio è disponibile a livello generale in tutte le aree globali di Azure. È in anteprima pubblica nelle aree Germania, Cina  ed enti pubblici.

Regioni                                        | Disponibilità
-----------------------------------------------|-----------------------------------------------
[Tutte le aree globali di Azure con disponibilità a livello generale](https://azure.microsoft.com/en-us/regions/)     | Disponibile a livello generale 
[Azure Government](https://azure.microsoft.com/en-us/overview/clouds/government/)              | In anteprima 
[Azure per la Cina](https://www.azure.cn/)                                                           | In anteprima
[Azure Germania](https://azure.microsoft.com/en-us/overview/clouds/germany/)                    | In anteprima

Questa tabella viene aggiornata quando il servizio viene reso disponibile in altri cloud di Azure.

Per provare il Servizio metadati dell'istanza, creare una macchina virtuale da [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) o dal [portale di Azure](http://portal.azure.com) nelle aree di cui sopra e seguire gli esempi riportati di seguito.

## <a name="usage"></a>Utilizzo

### <a name="versioning"></a>Controllo delle versioni
Il Servizio metadati dell'istanza è con versione. Le versioni sono obbligatorie e la versione corrente è `2017-04-02`.

> [!NOTE] 
> Le versioni precedenti di anteprima di eventi pianificati {ultima} sono supportate come versione dell'API. Questo formato non è più supportato e verrà rimosso in futuro.

Quando si aggiungono versioni più recenti, quelle precedenti sono comunque accessibili per la compatibilità, se gli script presentano dipendenze in formati di dati specifici. Si noti però che la versione di anteprima corrente (2017-03-01) potrebbe non essere disponibile quando il servizio è disponibile a livello generale.

### <a name="using-headers"></a>Uso delle intestazioni
Quando si eseguono query sul Servizio metadati dell'istanza, è necessario specificare l'intestazione `Metadata: true` per garantire che la richiesta non sia stata reindirizzata accidentalmente.

### <a name="retrieving-metadata"></a>Recupero dei metadati

I metadati dell'istanza sono disponibili per l'esecuzione di macchine virtuali create e gestite tramite [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Accedere a tutte le categorie di dati per un'istanza di macchina virtuale utilizzando la richiesta seguente:

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02"
```

> [!NOTE] 
> Tutte le query dei metadati dell'istanza fanno distinzione tra maiuscole e minuscole.

### <a name="data-output"></a>Output dei dati
Per impostazione predefinita, il Servizio metadati dell'istanza restituisce i dati in formato JSON (`Content-Type: application/json`). Tuttavia, API diverse possono restituire dati in formati diversi se necessario.
La tabella seguente costituisce un riferimento per gli altri formati di dati che le API possono supportare.

API | Formato dati predefinito | Altri formati
--------|---------------------|--------------
/instance | json | text
/scheduledevents | json | Nessuno

Per accedere a un formato di risposta non predefinito, specificare il formato richiesto come parametro di stringa di query nella richiesta. ad esempio:

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02&format=text"
```

### <a name="security"></a>Sicurezza
L'endpoint del Servizio metadati dell'istanza è accessibile solo dall'istanza della macchina virtuale in esecuzione su un indirizzo IP non instradabile. Inoltre, qualsiasi richiesta con intestazione `X-Forwarded-For` viene rifiutata dal servizio.
È necessario anche che le richieste includano l'intestazione `Metadata: true` per garantire che la richiesta sia stata destinata direttamente e non faccia parte di un reindirizzamento non intenzionale. 

### <a name="error"></a>Tipi di errore
In caso di elementi dati non trovati o di richiesta non valida, il Servizio metadati dell'istanza restituisce errori HTTP standard. ad esempio:

Codice di stato HTTP | Motivo
----------------|-------
200 - OK |
400 - Richiesta non valida | Intestazione `Metadata: true` mancante
404 - Non trovato | L'elemento richiesto non esiste 
405 - Metodo non consentito | Sono supportate solo le richieste `GET` e `POST`
429 - Numero eccessivo di richieste | L'API supporta attualmente un massimo di 5 query al secondo
500 - Errore del servizio     | Ripetere l'operazione in un secondo momento

### <a name="examples"></a>esempi

> [!NOTE] 
> Tutte le risposte delle API sono stringhe JSON. Tutte le risposte di esempio che seguono sono di tipo pretty-print per una migliore leggibilità.

#### <a name="retrieving-network-information"></a>Recupero delle informazioni di rete

**Richiesta**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-04-02"
```

**Risposta**

> [!NOTE] 
> La risposta è una stringa JSON. La risposta di esempio che segue è di tipo pretty-print per una migliore leggibilità.

```
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

#### <a name="retrieving-public-ip-address"></a>Recupero dell'indirizzo IP pubblico

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-04-02&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Recupero di tutti i metadati per un'istanza

**Richiesta**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02"
```

**Risposta**

> [!NOTE] 
> La risposta è una stringa JSON. La risposta di esempio che segue è di tipo pretty-print per una migliore leggibilità.

```
{
  "compute": {
    "location": "westcentralus",
    "name": "IMDSSample",
    "offer": "UbuntuServer",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "Canonical",
    "sku": "16.04.0-LTS",
    "version": "16.04.201610200",
    "vmId": "5d33a910-a7a0-4443-9f01-6a807801b29b",
    "vmSize": "Standard_A1"
  },
  "network": {
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
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Recupero dei metadati in una macchina virtuale Windows

**Richiesta**

I metadati dell'istanza possono essere recuperati in Windows tramite l'utilità PowerShell `curl`: 

```
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-04-02 | select -ExpandProperty Content
```

Oppure tramite il cmdlet `Invoke-RestMethod`:
    
```
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2017-04-02 -Method get 
```

**Risposta**

> [!NOTE] 
> La risposta è una stringa JSON. La risposta di esempio che segue è di tipo pretty-print per una migliore leggibilità.

```
{
  "compute": {
    "location": "westus",
    "name": "SQLTest",
    "offer": "SQL2016SP1-WS2016",
    "osType": "Windows",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "MicrosoftSQLServer",
    "sku": "Enterprise",
    "version": "13.0.400110",
    "vmId": "453945c8-3923-4366-b2d3-ea4c80e9b70e",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": [
            
          ]
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="instance-metadata-data-categories"></a>Categorie di dati dei metadati dell'istanza
Tramite il Servizio metadati dell'istanza sono disponibili le categorie di dati seguenti:

Dati | Descrizione
-----|------------
location | Area di Azure in cui la macchina virtuale è in esecuzione
name | Nome della VM 
offer | Offre informazioni per l'immagine della macchina virtuale. Questo valore è presente solo per le immagini distribuite dalla raccolta di immagini di Azure.
publisher | Autore dell'immagine della macchina virtuale
sku | SKU specifica per l'immagine della macchina virtuale  
version | Versione dell'immagine della macchina virtuale 
osType | Linux o Windows 
platformUpdateDomain |  [Dominio di aggiornamento](virtual-machines-windows-manage-availability.md) in cui è in esecuzione la macchina virtuale
platformFaultDomain | [Dominio di errore](virtual-machines-windows-manage-availability.md) in cui è in esecuzione la macchina virtuale
vmId | [Identificatore univoco](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) della macchina virtuale
vmSize | [Dimensioni macchina virtuale](virtual-machines-windows-sizes.md)
ipv4/privateIpAddress | Indirizzo IPv4 locale della macchina virtuale 
ipv4/publicIpAddress | Indirizzo IPv4 pubblico della macchina virtuale
subnet/address | Indirizzo della subnet della macchina virtuale
subnet/prefix | Prefisso della subnet, ad esempio 24
ipv6/ipAddress | Indirizzo IPv6 locale della macchina virtuale
macAddress | Indirizzo mac della macchina virtuale 
scheduledevents | Attualmente in anteprima pubblica. Vedere [scheduledevents](virtual-machines-scheduled-events.md)

## <a name="example-scenarios-for-usage"></a>Scenari di uso di esempio  

### <a name="tracking-vm-running-on-azure"></a>Rilevamento della macchina virtuale in esecuzione in Azure

Come provider di servizi, potrebbe essere necessario tenere traccia del numero di macchine virtuali che eseguono il proprio software o avere agenti che devono verificare l'univocità della macchina virtuale. Per poter ottenere un ID univoco per una macchina virtuale, usare il campo `vmId` dal Servizio metadati dell'istanza.

**Richiesta**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-04-02&format=text"
```

**Risposta**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Posizionamento di contenitori, dominio di aggiornamento/errore basato su partizioni dati 

Per alcuni scenari, il posizionamento di repliche dati diverse è di importanza primaria. Ad esempio per il [posizionamento delle repliche HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) o per il posizionamento di contenitori tramite un [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) è necessario conoscere il `platformFaultDomain` e il `platformUpdateDomain` in cui la macchina virtuale è in esecuzione.
È possibile eseguire query su questi dati direttamente tramite il Servizio metadati dell'istanza.

**Richiesta**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-04-02&format=text" 
```

**Risposta**

```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Ottenere altre informazioni sulla macchina virtuale durante la richiesta di assistenza 

Come provider di servizi è possibile ricevere una chiamata di supporto per la quale occorre sapere altre informazioni sulla macchina virtuale. Chiedere al cliente di condividere i metadati di calcolo può risultare utile per avere informazioni di base che consentano al personale del supporto tecnico di conoscere il tipo di macchina virtuale in Azure. 

**Richiesta**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-04-02"
```

**Risposta**

> [!NOTE] 
> La risposta è una stringa JSON. La risposta di esempio che segue è di tipo pretty-print per una migliore leggibilità.

```
{
  "compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }
}
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Esempi di chiamate del Servizio metadati con diversi linguaggi all'interno della macchina virtuale 

Linguaggio | Esempio 
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go Lan   | https://github.com/Microsoft/azureimds/blob/master/imdssample.go            
python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
    

## <a name="faq"></a>Domande frequenti
1. Viene visualizzato l'errore `400 Bad Request, Required metadata header not specified`. Che cosa significa?
   * Il Servizio metadati dell'istanza richiede che nella richiesta venga passata l'intestazione `Metadata: true`. Il passaggio di questa intestazione nella chiamata REST consente l'accesso al Servizio metadati dell'istanza. 
2. Perché non riesco a ottenere le informazioni di calcolo per la macchina virtuale?
   * Attualmente il Servizio metadati dell'istanza supporta solo le istanze create con Azure Resource Manager. È possibile che in futuro venga aggiunto il supporto per le macchine virtuali del servizio cloud.
3. Ho creato la mia macchina virtuale tramite Azure Resource Manager tempo fa. Perché non riesco a vedere le informazioni sui metadati di calcolo?
   * Per tutte le macchine virtuali create dopo settembre 2016, è necessario aggiungere un [Tag](../azure-resource-manager/resource-group-using-tags.md) per iniziare a essere visualizzare i metadati di calcolo. Per le macchine virtuale precedenti (create prima di settembre 2016), è necessario aggiungere o rimuovere estensioni o dischi di dati dalla macchina virtuale per aggiornare i metadati.
4. Perché viene visualizzato l'errore `500 Internal Server Error`?
   * Inviare di nuovo la richiesta basata sul sistema di backoff esponenziale. Se il problema persiste, contattare il supporto di Azure.
5. Dove posso condividere domande o commenti aggiuntivi?
   * Inviare i propri commenti accedendo alla pagina http://feedback.azure.com.
7. Il servizio funziona per l'istanza del set di scalabilità di macchine virtuali?
   * Sì, il Servizio metadati è disponibile per le istanze del set di scalabilità. 
6. Come si ottiene assistenza per il servizio?
   * Per ottenere assistenza per il servizio, è necessario creare una richiesta di supporto nel portale di Azure per la macchina virtuale per la quale non si riesce a ottenere la risposta dei metadati dopo lunghi tentativi 

   ![Supporto per i metadati dell'istanza](./media/virtual-machines-instancemetadataservice-overview/InstanceMetadata-support.png)
    
## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sull'API [scheduledevents](virtual-machines-scheduled-events.md) **in anteprima pubblica** fornita dal Servizio metadati dell'istanza.

