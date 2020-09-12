---
title: Creare immagini Linux senza un agente di provisioning
description: Crea immagini Linux generalizzate senza un agente di provisioning in Azure.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/01/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 63bc3caf97e1325c365171ba3f8e6353885d9b68
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322552"
---
# <a name="creating-generalized-images-without-a-provisioning-agent"></a>Creazione di immagini generalizzate senza un agente di provisioning

Microsoft Azure fornisce gli agenti di provisioning per le macchine virtuali Linux sotto forma di [walinuxagent](https://github.com/Azure/WALinuxAgent) o [cloud-init](https://github.com/canonical/cloud-init) (scelta consigliata). Potrebbe tuttavia verificarsi uno scenario in cui non si vuole usare una di queste applicazioni per l'agente di provisioning, ad esempio:

- La distribuzione/versione Linux non supporta l'agente cloud-init/Linux.
- È necessario impostare specifiche proprietà della macchina virtuale, ad esempio il nome host.

> [!NOTE] 
>
> Se non è necessario impostare alcuna proprietà o una qualsiasi forma di provisioning, è necessario valutare la creazione di un'immagine specializzata.

Questo articolo illustra come è possibile configurare l'immagine di macchina virtuale per soddisfare i requisiti della piattaforma Azure e impostare il nome host, senza installare un agente di provisioning.

## <a name="networking-and-reporting-ready"></a>Rete e creazione di report pronti

Per far sì che la macchina virtuale Linux comunichi con i componenti di Azure, è necessario un client DHCP per recuperare un IP host dalla rete virtuale, nonché la risoluzione DNS e la gestione delle route. La maggior parte delle distribuzioni è disponibile con queste utilità predefinite. Gli strumenti testati in Azure da fornitori di distribuzioni Linux `dhclient` includono `network-manager` , `systemd-networkd` e altri.

> [!NOTE]
>
> Attualmente la creazione di immagini generalizzate senza un agente di provisioning supporta solo le macchine virtuali abilitate per DHCP.

Dopo che la rete è stata impostata e configurata, è necessario "report Ready". Questo indica ad Azure che la macchina virtuale è stata correttamente provisioning.

> [!IMPORTANT]
>
> Se non si riesce a segnalare pronto ad Azure, la macchina virtuale verrà riavviata.

## <a name="demosample"></a>Demo/esempio

Questa demo illustra come è possibile prendere un'immagine del Marketplace esistente (in questo caso, una VM di Debian Buster) e rimuovere l'agente Linux (walinuxagent), ma anche creare il processo di base per segnalare ad Azure che la macchina virtuale è "pronta".

### <a name="create-the-resource-group-and-base-vm"></a>Creare il gruppo di risorse e la VM di base:

```bash
$ az group create --location eastus --name demo1
```

Creare la VM di base:

```bash
$ az vm create \
    --resource-group demo1 \
    --name demo1 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo1 \
    --image "debian:debian-10:10:latest"
```

### <a name="remove-the-image-provisioning-agent"></a>Rimuovere l'agente di provisioning dell'immagine

Una volta eseguito il provisioning della macchina virtuale, è possibile connettersi tramite SSH e rimuovere l'agente Linux:

```bash
$ sudo apt purge -y waagent
$ sudo rm -rf /var/lib/waagent /etc/waagent.conf /var/log/waagent.log
```

### <a name="add-required-code-to-the-vm"></a>Aggiungere il codice necessario alla macchina virtuale

Anche all'interno della macchina virtuale, poiché l'agente Linux di Azure è stato rimosso, è necessario fornire un meccanismo per la segnalazione di pronto. 

#### <a name="python-script"></a>Script Python

```python
import http.client
import sys
from xml.etree import ElementTree

wireserver_ip = '168.63.129.16'
wireserver_conn = http.client.HTTPConnection(wireserver_ip)

print('Retrieving goal state from the Wireserver')
wireserver_conn.request(
    'GET',
    '/machine?comp=goalstate',
    headers={'x-ms-version': '2012-11-30'}
)

resp = wireserver_conn.getresponse()

if resp.status != 200:
    print('Unable to connect with wireserver')
    sys.exit(1)

wireserver_goalstate = resp.read().decode('utf-8')

xml_el = ElementTree.fromstring(wireserver_goalstate)

container_id = xml_el.findtext('Container/ContainerId')
instance_id = xml_el.findtext('Container/RoleInstanceList/RoleInstance/InstanceId')
print(f'ContainerId: {container_id}')
print(f'InstanceId: {instance_id}')

# Construct the XML response we need to send to Wireserver to report ready.
health = ElementTree.Element('Health')
goalstate_incarnation = ElementTree.SubElement(health, 'GoalStateIncarnation')
goalstate_incarnation.text = '1'
container = ElementTree.SubElement(health, 'Container')
container_id_el = ElementTree.SubElement(container, 'ContainerId')
container_id_el.text = container_id
role_instance_list = ElementTree.SubElement(container, 'RoleInstanceList')
role = ElementTree.SubElement(role_instance_list, 'Role')
instance_id_el = ElementTree.SubElement(role, 'InstanceId')
instance_id_el.text = instance_id
health_second = ElementTree.SubElement(role, 'Health')
state = ElementTree.SubElement(health_second, 'State')
state.text = 'Ready'

out_xml = ElementTree.tostring(
    health,
    encoding='unicode',
    method='xml'
)
print('Sending the following data to Wireserver:')
print(out_xml)

wireserver_conn.request(
    'POST',
    '/machine?comp=health',
    headers={
        'x-ms-version': '2012-11-30',
        'Content-Type': 'text/xml;charset=utf-8',
        'x-ms-agent-name': 'custom-provisioning'
    },
    body=out_xml
)

resp = wireserver_conn.getresponse()
print(f'Response: {resp.status} {resp.reason}')

wireserver_conn.close()
```

#### <a name="generic-steps-without-using-python"></a>Passaggi generici (senza usare Python)

Se la macchina virtuale non dispone di Python installato o disponibile, è possibile riprodurre a livello di codice questa logica di script con i passaggi seguenti:

1. Recuperare `ContainerId` e `InstanceId` analizzando la risposta da WireServer: `curl -X GET -H 'x-ms-version: 2012-11-30' http://$168.63.129.16/machine?comp=goalstate` .

2. Costruire i dati XML seguenti, inserendo l'oggetto analizzato `ContainerId` e `InstanceId` dal passaggio precedente:
   ```xml
   <Health>
     <GoalStateIncarnation>1</GoalStateIncarnation>
     <Container>
       <ContainerId>CONTAINER_ID</ContainerId>
       <RoleInstanceList>
         <Role>
           <InstanceId>INSTANCE_ID</InstanceId>
           <Health>
             <State>Ready</State>
           </Health>
         </Role>
       </RoleInstanceList>
     </Container>
   </Health>
   ```

3. Inserire i dati in WireServer: `curl -X POST -H 'x-ms-version: 2012-11-30' -H "x-ms-agent-name: WALinuxAgent" -H "Content-Type: text/xml;charset=utf-8" -d "$REPORT_READY_XML" http://168.63.129.16/machine?comp=health`

### <a name="automating-running-the-code-at-first-boot"></a>Automazione dell'esecuzione del codice al primo avvio

Questa demo USA systemd, che è il sistema init più comune nelle distribuzioni moderne di Linux. Quindi, il modo più semplice e più nativo per assicurarsi che questo meccanismo di preparazione dei report venga eseguito al momento giusto consiste nel creare un'unità di servizio systemd. È possibile aggiungere il file di unità seguente a `/etc/systemd/system` (questo esempio assegna un nome al file di unità `azure-provisioning.service` ):

```
[Unit]
Description=Azure Provisioning

[Service]
Type=oneshot
ExecStart=/usr/bin/python3 /usr/local/azure-provisioning.py
ExecStart=/bin/bash -c "hostnamectl set-hostname $(curl \
    -H 'metadata: true' \
    'http://169.254.169.254/metadata/instance/compute/name?api-version=2019-06-01&format=text')"
ExecStart=/usr/bin/systemctl disable azure-provisioning.service

[Install]
WantedBy=multi-user.target
```

Questo servizio systemd esegue tre operazioni per il provisioning di base:

1. Report pronti ad Azure (per indicare che l'operazione è stata completata correttamente).
1. Rinomina la macchina virtuale in base al nome della macchina virtuale fornita dall'utente effettuando il pull dei dati dal [servizio metadati dell'istanza di Azure (IMDS)](https://docs.microsoft.com/azure/virtual-machines/linux/instance-metadata-service). **Nota** IMDS fornisce anche altri [metadati dell'istanza](https://docs.microsoft.com/azure/virtual-machines/linux/instance-metadata-service#accessing-azure-instance-metadata-service), ad esempio le chiavi pubbliche SSH, in modo che sia possibile impostare più di un nome host.
1. Viene disabilitato in modo che venga eseguito solo al primo avvio e non ai successivi riavvii.

Con l'unità nel file System, eseguire il comando seguente per abilitarlo:

```
$ sudo systemctl enable azure-provisioning.service
```

A questo punto la macchina virtuale è pronta per essere generalizzata e deve essere creata da un'immagine. 

#### <a name="completing-the-preparation-of-the-image"></a>Completamento della preparazione dell'immagine

Nel computer di sviluppo eseguire le operazioni seguenti per preparare la creazione di immagini dalla VM di base:

```
$ az vm deallocate --resource-group demo1 --name demo1
$ az vm generalize --resource-group demo1 --name demo1
```

E creare l'immagine da questa macchina virtuale:

```
$ az image create \
    --resource-group demo1 \
    --source demo1 \
    --location eastus \
    --name demo1img
```

A questo punto è possibile creare una nuova macchina virtuale (o più macchine virtuali) dall'immagine:

```
$ IMAGE_ID=$(az image show -g demo1 -n demo1img --query id -o tsv)
$ az vm create \
    --resource-group demo12 \
    --name demo12 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo12 \
    --image "$IMAGE_ID" 
    --enable-agent false
```

> [!NOTE]
>
> È importante impostare su `--enable-agent` `false` perché walinuxagent non esiste in questa macchina virtuale che verrà creata dall'immagine.

Il provisioning della macchina virtuale deve essere completato correttamente. Per accedere alla macchina virtuale di nuovo provisioning, dovrebbe essere possibile visualizzare l'output del servizio di sistema pronto per il report:

```
$ sudo journalctl -u azure-provisioning.service
-- Logs begin at Thu 2020-06-11 20:28:45 UTC, end at Thu 2020-06-11 20:31:24 UTC. --
Jun 11 20:28:49 thstringnopa systemd[1]: Starting Azure Provisioning...
Jun 11 20:28:54 thstringnopa python3[320]: Retrieving goal state from the Wireserver
Jun 11 20:28:54 thstringnopa python3[320]: ContainerId: 7b324f53-983a-43bc-b919-1775d6077608
Jun 11 20:28:54 thstringnopa python3[320]: InstanceId: fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2
Jun 11 20:28:54 thstringnopa python3[320]: Sending the following data to Wireserver:
Jun 11 20:28:54 thstringnopa python3[320]: <Health><GoalStateIncarnation>1</GoalStateIncarnation><Container><ContainerId>7b324f53-983a-43bc-b919-1775d6077608</ContainerId><RoleInstanceList><Role><InstanceId>fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2</InstanceId><Health><State>Ready</State></Health></Role></RoleInstanceList></Container></Health>
Jun 11 20:28:54 thstringnopa python3[320]: Response: 200 OK
Jun 11 20:28:56 thstringnopa bash[472]:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Jun 11 20:28:56 thstringnopa bash[472]:                                  Dload  Upload   Total   Spent    Left  Speed
Jun 11 20:28:56 thstringnopa bash[472]: [158B blob data]
Jun 11 20:28:56 thstringnopa2 systemctl[475]: Removed /etc/systemd/system/multi-user.target.wants/azure-provisioning.service.
Jun 11 20:28:56 thstringnopa2 systemd[1]: azure-provisioning.service: Succeeded.
Jun 11 20:28:56 thstringnopa2 systemd[1]: Started Azure Provisioning.
```

## <a name="support"></a>Supporto

Se si implementa il proprio codice di provisioning/agente, si è proprietari del supporto di questo codice, il supporto tecnico Microsoft analizzerà solo i problemi relativi alle interfacce di provisioning non disponibili. In quest'area vengono continuamente apportati miglioramenti e modifiche, quindi è necessario monitorare le modifiche apportate a cloud-init e all'agente Linux di Azure per il provisioning delle modifiche API.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [provisioning di Linux](provisioning.md).
