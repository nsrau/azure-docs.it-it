---
title: Distribuire Elasticsearch in una macchina virtuale di sviluppo in Azure
description: Esercitazione - Installare Elastic Stack in una macchina virtuale Linux di sviluppo in Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rloutlaw
manager: justhe
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 10/11/2017
ms.author: routlaw
ms.openlocfilehash: eeb1b8b9105e055339cb31fa4b9d4b411cb06c54
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="install-the-elastic-stack-on-an-azure-vm"></a>Installare Elastic Stack in una macchina virtuale di Azure

Questo articolo descrive la procedura per distribuire [Elasticsearch](https://www.elastic.co/products/elasticsearch), [Logstash](https://www.elastic.co/products/logstash) e [Kibana](https://www.elastic.co/products/kibana) in una macchina virtuale Ubuntu in Azure. Per visualizzare Elastic Stack in azione, è possibile connettersi a Kibana e usare alcuni dati di registrazione di esempio. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una macchina virtuale Ubuntu in un gruppo di risorse di Azure
> * Installare Elasticsearch, Logstash e Kibana nella macchina virtuale
> * Inviare dati di esempio a Elasticsearch con Logstash 
> * Aprire le porte e usare dati della console Kibana


 Questa distribuzione è adatta per lo sviluppo di base con lo Elastic Stack. Per altre informazioni su Elastic Stack, incluse le raccomandazioni per un ambiente di produzione, vedere la [documentazione di Elastic](https://www.elastic.co/guide/index.html) e il [Centro architetture Azure](/azure/architecture/elasticsearch/).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, in questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una VM con il comando [az vm create](/cli/azure/vm#az_vm_create). 

L'esempio seguente crea una macchina virtuale denominata *myVM* e le chiavi SSH, se non esistono già in un percorso predefinito. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Dopo che la VM è stata creata, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente. Prendere nota di `publicIpAddress`. Questo indirizzo viene usato per accedere alla VM.

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="ssh-into-your-vm"></a>Usare SSH per connettersi alla macchina virtuale

Se non si conosce già l'indirizzo IP pubblico della VM, eseguire il comando [az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_list):

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Usare il comando seguente per creare una sessione SSH con la macchina virtuale. Sostituire l'indirizzo IP pubblico corretto della macchina virtuale. In questo esempio l'indirizzo IP è *40.68.254.142*.

```bash
ssh azureuser@40.68.254.142
```

## <a name="install-the-elastic-stack"></a>Installare Elastic Stack

Importare la chiave di firma di Elasticsearch e aggiornare l'elenco di origini APT in modo da includere il repository dei pacchetti Elastic:

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list
```

Installare Java Virtual Machine nella macchina virtuale e configurare la variabile JAVA_HOME. Questa operazione è necessaria per l'esecuzione dei componenti di Elastic Stack.

```bash
sudo apt update && sudo apt install openjdk-8-jre-headless
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

Eseguire i comandi seguenti per aggiornare le origini dei pacchetti Ubuntu e installare Elasticsearch, Kibana e Logstash.

```bash
sudo apt update && sudo apt install elasticsearch kibana logstash   
```

> [!NOTE]
> Per istruzioni di installazione dettagliate, inclusi i layout di directory e la configurazione iniziale, vedere la [documentazione di Elastic](https://www.elastic.co/guide/en/elastic-stack/current/installing-elastic-stack.html)

## <a name="start-elasticsearch"></a>Avviare Elasticsearch 

Avviare Elasticsearch nella macchina virtuale con il comando seguente:

```bash
sudo systemctl start elasticsearch.service
```

Questo comando non genera alcun output. Verificare quindi che Elasticsearch sia in esecuzione sulla macchina virtuale con questo comando `curl`:

```bash
sudo curl -XGET 'localhost:9200/'
```

Se Elasticsearch è in esecuzione, viene visualizzato un output simile al seguente:

```json
{
  "name" : "w6Z4NwR",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "SDzCajBoSK2EkXmHvJVaDQ",
  "version" : {
    "number" : "5.6.3",
    "build_hash" : "1a2f265",
    "build_date" : "2017-10-06T20:33:39.012Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.1"
  },
  "tagline" : "You Know, for Search"
}
```

## <a name="start-logstash-and-add-data-to-elasticsearch"></a>Avviare Logstash e aggiungere dati a Elasticsearch

Avviare Logstash con il comando seguente:

```bash 
sudo systemctl start logstash.service
```

Testare Logstash in modalità interattiva per verificare che funzioni correttamente:

```bash
sudo /usr/share/logstash/bin/logstash -e 'input { stdin { } } output { stdout {} }'
```

Si tratta di una [pipeline](https://www.elastic.co/guide/en/logstash/5.6/pipeline.html) di LogStash di base che restituisce l'input standard all'output standard. 

```output
The stdin plugin is now waiting for input:
hello azure
2017-10-11T20:01:08.904Z myVM hello azure
```

Impostare LogStash per inoltrare i messaggi del kernel dalla macchina virtuale a Elasticsearch. Creare un nuovo file in una directory vuota denominata `vm-syslog-logstash.conf` e incollare nel file la configurazione di Logstash seguente:

```Logstash
input {
    stdin {
        type => "stdin-type"
    }

    file {
        type => "syslog"
        path => [ "/var/log/*.log", "/var/log/*/*.log", "/var/log/messages", "/var/log/syslog" ]
        start_position => "beginning"
    }
}

output {

    stdout {
        codec => rubydebug
    }
    elasticsearch {
        hosts  => "localhost:9200"
    }
}
```

Testare la configurazione e inviare i dati Syslog a Elasticsearch:

```bash
sudo /usr/share/logstash/bin/logstash -f vm-syslog-logstash.conf
```

Si osserverà come le voci di Syslog nel terminal vengano restituite dopo essere state inviate a Elasticsearch. Usare `CTRL+C` per uscire da Logstash dopo aver inviato alcuni dati.

## <a name="start-kibana-and-visualize-the-data-in-elasticsearch"></a>Avviare Kibana e visualizzare i dati in Elasticsearch

Modificare `/etc/kibana/kibana.yml` e cambiare l'indirizzo IP su cui è in ascolto Kibana in modo da poter accedervi dal Web browser.

```bash
server.host:"0.0.0.0"
```

Avviare Kibana con il comando seguente:

```bash
sudo systemctl start kibana.service
```

Aprire la porta 5601 dall'interfaccia della riga di comando di Azure per consentire l'accesso remoto alla console Kibana:

```azurecli-interactive
az vm open-port --port 5601 --resource-group myResourceGroup --name myVM
```

Aprire la console Kibana e selezionare **Crea** per generare un indice predefinito in base ai dati Syslog precedentemente inviati a Elasticsearch. 

![Sfogliare gli eventi Syslog in Kibana](media/elasticsearch-install/kibana-index.png)

Selezionare **Individua** nella console Kibana per cercare, esplorare e filtrare eventi Syslog.

![Sfogliare gli eventi Syslog in Kibana](media/elasticsearch-install/kibana-search-filter.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato distribuito Elastic Stack in un macchina virtuale di sviluppo in Azure. Si è appreso come:

> [!div class="checklist"]
> * Creare una macchina virtuale Ubuntu in un gruppo di risorse di Azure
> * Installare Elasticsearch, Logstash e Kibana nella macchina virtuale
> * Inviare dati di esempio a Elasticsearch da Logstash 
> * Aprire le porte e usare dati della console Kibana
