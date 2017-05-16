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
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: b5c0268e1a0ebfcb33781678a367090cad865907
ms.lasthandoff: 04/11/2017

---

# <a name="azures-instance-metadata-service---preview"></a>Servizio metadati dell'istanza di Azure --Anteprima

> [!NOTE] 
> Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le condizioni d'uso. Per altre informazioni vedere le [Condizioni Supplementari per l'Utilizzo delle Anteprime di Microsoft Azure]. (https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
>

Il Servizio metadati dell'istanza offre informazioni riguardanti le istanze di macchine virtuali in esecuzione. Queste informazioni consentono di gestire e configurare le istanze in Azure. Il Servizio metadati dell'istanza di Azure è un endpoint RESTful disponibile per tutte le macchine virtuali IaaS create tramite [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN). L'endpoint è disponibile a un indirizzo IP non instradabile noto (*169.254.169.254*) a cui è possibile accedere solo dalla macchina virtuale. Questo servizio offre dati importanti sull'istanza di macchina virtuale, sulla configurazione di rete e sugli eventi di manutenzione previsti. Per altre informazioni, vedere [categorie di metadati](#instance-metadata-data-categories).

### <a name="important-information"></a>Informazioni importanti
Attualmente questo servizio è in **anteprima** e ospita informazioni relative all'istanza di macchina virtuale e agli eventi di manutenzione previsti. Il servizio continua a ricevere gli aggiornamenti e questa pagina riflette le [categorie di dati](#instance-metadata-data-categories) specifiche disponibili.


## <a name="service-availability"></a>Disponibilità del servizio
L'anteprima corrente è disponibile nell'area **Stati Uniti centro occidentali** di Azure. La tabella seguente viene aggiornata per le aree in cui l'anteprima del servizio diventa disponibile.
Per provare il Servizio metadati dell'istanza, creare una macchina virtuale da [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN) o dal [Portale di Azure](http://portal.azure.com) e seguire gli esempi nella sezione appropriata per accedere al servizio metadati. 

Aree in cui è disponibile l'anteprima del Servizio metadati dell'istanza|
------------------------------------------------------------|
Stati Uniti centro-occidentali |



## <a name="retrieving-instance-metadata"></a>Recupero di metadati dell'istanza 

I metadati dell'istanza sono disponibili per l'esecuzione di macchine virtuali create e gestite tramite [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN). Accedere a tutte le categorie di dati per un'istanza di macchina virtuale con l'URI seguente:

```
 curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```

L'output predefinito per tutti i metadati di istanza è in formato json (tipo di contenuto Application/json)

## <a name="usage-examples"></a>Esempi di uso
Di seguito sono riportati set di esempi e la semantica di uso per il Servizio metadati dell'istanza

### <a name="versioning"></a>Controllo delle versioni 
Il Servizio metadati dell'istanza è sottoposto a controllo delle versioni. Le versioni sono obbligatorie e la versione di anteprima corrente è 2017-03-01.


```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```

Quando si aggiungono versioni più recenti, quelle precedenti sono comunque accessibili per la compatibilità, se gli script presentano dipendenze in formati di dati specifici. Si noti che la versione di anteprima corrente potrebbe non essere disponibile quando il servizio è disponibile a livello generale.


### <a name="data-output"></a>Output dei dati
Per impostazione predefinita, i metadati dell'istanza restituiscono i dati in json (tipo di contenuto = application/json). Elementi nodo diversi possono restituire dati in un formato predefinito diverso applicabile. La tabella che segue costituisce un riferimento rapido per i formati di dati 

Elemento | Formato dati predefinito | Altri formati
--------|---------------------|--------------
/instance | json | text
/scheduledevents | json | nessuno

Per il formato di testo usare format=text nell'URL della richiesta, ad esempio 

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01&format=text"
```
### <a name="security"></a>Sicurezza
L'endpoint di metadati dell'istanza è accessibile solo dall'istanza della macchina virtuale in esecuzione su un indirizzo IP 169.254.169.254 non instradabile. Inoltre qualsiasi richiesta con **intestazione X-Forwarded-For** viene rifiutata dal servizio metadati. È necessario anche che le richieste da inviare includano l'intestazione **Metadata:true** per garantire che la richiesta sia stata destinata direttamente e non faccia parte di un reindirizzamento non intenzionale. 
### <a name="error"></a>Errore
In caso di elementi dati non trovati o richieste non valide, il Servizio metadati dell'istanza restituisce l'errore HTTP standard. Di seguito sono riportati alcuni esempi di codici restituiti 

Codice restituito HTTP | Motivo
----------------|-------
200 | OK
400 | Richiesta non valida, intestazione mancante, passare -H Metadata:true
404 | Non trovato, l'elemento richiesto non esiste 
405 | Metodo non supportato 
429 | Numero eccessivo di richieste, attualmente sono supportate solo un massimo di 5 query al secondo

### <a name="examples"></a>esempi
#### <a name="retrieving-the-network-information"></a>Recupero delle informazioni di rete 

**Richiesta**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-03-01"

```
**Risposta**
> [!NOTE] 
> La risposta è una stringa json. L'output che segue è formattato json con utilità come [jq](https://stedolan.github.io/jq/).
>

```
{
  "interface": [
    {
      "ipv4": {
        "ipaddress": [
          {
            "ipaddress": "10.0.0.4",
            "publicip": "<>.<>.<>.<>"
          }
        ],
        "subnet": [
          {
            "address": "10.0.0.0",
            "dnsservers": [
              {
                "ipaddress": "10.0.0.2"
              },
              {
                "ipaddress": "10.0.0.3"
              }
            ],
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipaddress": []
      },
      "mac": "000D3A00FA89"
    }
  ]
}
```

#### <a name="retrieving-public-ip-address"></a>Recupero dell'indirizzo IP pubblico

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipaddress/0/publicip?api-version=2017-03-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Recupero di tutti i metadati per un'istanza

**Richiesta**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```
**Risposta**
> [!NOTE]
> La risposta è una stringa json. L'output che segue è formattato json con utilità come [jq](https://stedolan.github.io/jq/).
>

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
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipaddress": [
            {
              "ipaddress": "10.0.0.4",
              "publicip": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.0.0",
              "dnsservers": [
                {
                  "ipaddress": "10.0.0.2"
                },
                {
                  "ipaddress": "10.0.0.3"
                }
              ],
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipaddress": []
        },
        "mac": "000D3A00FA89"
      }
    ]
  }
}

```
#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Recupero di metadati in una macchina virtuale Windows

I metadati dell'istanza possono essere recuperati in Windows tramite il comando curl dell'utilità Powershell. Al prompt di PowerShell eseguire i comandi seguenti: 

**Richiesta**
```
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-03-01 | select -ExpandProperty Content
```
**Risposta**
>[!NOTE]
> La risposta è una stringa json. L'output che segue è formattato json con l'utilità Powershell ConvertTo-Json.
> 

```
{
    "compute":  {
                    "location":  "CentralUSEUAP",
                    "name":  "SQLTest",
                    "offer":  "SQL2016SP1-WS2016",
                    "osType":  "Windows",
                    "platformFaultDomain":  "0",
                    "platformUpdateDomain":  "0",
                    "publisher":  "MicrosoftSQLServer",
                    "sku":  "Enterprise",
                    "version":  "13.0.400110",
                    "vmId":  "453945c8-3923-4366-b2d3-ea4c80e9b70e",
                    "vmSize":  "Standard_DS2"
                },
    "network":  {
                    "interface":  [
                                      "@{ipv4=; ipv6=; mac=002248020E1E}"
                                  ]
                }
}
```


## <a name="instance-metadata-data-categories"></a>Categorie di dati dei metadati dell'istanza
La tabella seguente include un elenco di tutte le categorie di dati disponibili tramite i metadati dell'istanza

Dati | Descrizione
-----|------------
location | Area di Azure in cui la macchina virtuale è in esecuzione 
name | Nome della VM 
offer | Offre informazioni per l'immagine della macchina virtuale. Questi valori sono presenti solo per immagini distribuite dalla raccolta immagini di Azure 
publisher | Autore dell'immagine della macchina virtuale
sku | SKU specifica per l'immagine della macchina virtuale  
version | Versione dell'immagine della macchina virtuale 
osType | Linux o Windows 
platformUpdateDomain |  [Dominio di aggiornamento](virtual-machines-windows-manage-availability.md) in cui è in esecuzione la macchina virtuale. 
platformFaultDomain | [Dominio di errore](virtual-machines-windows-manage-availability.md) in cui è in esecuzione la macchina virtuale.
vmId | Identificatore univoco per la macchina virtuale. Altre informazioni [qui](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)
vmSize | [Dimensioni macchina virtuale](virtual-machines-windows-sizes.md)
ipv4/ipaddress | Indirizzo IP locale della macchina virtuale 
IPv4/publicip | Indirizzo IP pubblico per l'istanza 
subnet/address | Indirizzo per la subnet 
subnet/dnsservers/ipaddress1 | Server DNS primario
subnet/dnsservers/ipaddress2 | Server DNS secondario
subnet/prefix | Prefisso della subnet, ad esempio 24
ipv6/ipaddress | Indirizzo IPv6 per la macchina virtuale
mac | Indirizzo mac della macchina virtuale 
scheduledevents | vedere [scheduledevents](virtual-machines-scheduled-events.md)



## <a name="example-scenarios-for-usage"></a>Scenari di uso di esempio  

### <a name="tracking-vm-running-on-azure"></a>Rilevamento della macchina virtuale in esecuzione in Azure 

Come provider di servizi, potrebbe essere necessario tenere traccia del numero di macchine virtuali che eseguono il proprio software o avere agenti che devono verificare l'univocità della macchina virtuale. Per essere in grado di ottenere un ID univoco per una macchina virtuale, usare il campo vmId dal Servizio metadati dell'istanza 

**Richiesta**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-03-01&format=text"
```
**Risposta**
```
5c08b38e-4d57-4c23-ac45-aca61037f084

```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Posizionamento di contenitori, dominio di aggiornamento/errore basato su partizioni dati 

Per alcuni scenari in cui il posizionamento di repliche diverse è di importanza primaria. Ad esempio il [posizionamento delle repliche HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) o per il posizionamento di contenitori tramite un [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) è necessario conoscere il platformFaultDomain e il platformUpdateDomain su cui la macchina virtuale è in esecuzione. È possibile eseguire query su questo punto dati direttamente tramite i metadati dell'istanza

**Richiesta**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-03-01&format=text" 
```
**Risposta**
```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Ottenere altre informazioni sulla macchina virtuale durante la richiesta di assistenza 

Come provider di servizi è possibile ricevere una chiamata del supporto in cui si desidera avere altre informazioni sulla macchina virtuale. Chiedere al cliente di condividere i metadati di calcolo può risultare utile per avere informazioni di base che consentano al personale del supporto tecnico di conoscere il tipo di macchina virtuale in Azure. 

**Richiesta**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-03-01"
```
**Risposta**
> [!NOTE] 
> La risposta è una stringa json. L'output che segue è formattato json con utilità come [jq](https://stedolan.github.io/jq/).
>

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

## <a name="faq"></a>domande frequenti
1. Viene visualizzato l'errore 400 - Richiesta non valida. L'intestazione dei metadati richiesta non è specificata. Che cosa significa?
   * Il Servizio metadati dell'istanza richiede che nella richiesta venga passata l'intestazione Metadata:true. Il passaggio dell'intestazione nella chiamata REST consente l'accesso al Servizio metadati dell'istanza. 
2. Perché non riesco a ottenere le informazioni di calcolo per la macchina virtuale?
   * Attualmente il Servizio metadati dell'istanza supporta solo le istanze create da Azure Resource Manager. È possibile che in futuro verrà aggiunto il supporto per le macchine virtuali dei Servizi cloud. 
3. Ho creato la mia macchina virtuale tramite Azure Resource Manager tempo fa. Perché non riesco a vedere le informazioni sui metadati di calcolo?
   * Per tutte le macchine virtuali create dopo settembre 2016, è necessario aggiungere un [Tag](../azure-resource-manager/resource-group-using-tags.md) per iniziare a essere visualizzare i metadati di calcolo. Per le macchine virtuale precedenti (create prima di settembre 2016), è necessario aggiungere o rimuovere estensioni o dischi di dati alla macchina virtuale per aggiornare i metadati.
4. Perché viene visualizzato l'errore 500 - Errore interno del server?
   * Attualmente l'anteprima dei metadati dell'istanza è disponibile solo nell'area Stati Uniti centro occidentali. Distribuire le proprie macchine virtuali nelle aree supportate.  
4. Dove posso condividere domande o commenti aggiuntivi?
   * Inviare i propri commenti all'indirizzo feedback.azure.com.
    
## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [scheduledevents] (virtual-machines-scheduled-events.md)

