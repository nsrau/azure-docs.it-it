---
title: Risoluzione dei problemi relativi alla distribuzione di OpenShift in Azure | Microsoft Docs
description: Risoluzione dei problemi relativi alla distribuzione di OpenShift in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: 31512bb264b5e998e5b6adc76d37c82c174933be
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091696"
---
# <a name="troubleshoot-openshift-deployment-in-azure"></a>Risoluzione dei problemi relativi alla distribuzione di OpenShift in Azure

Se il cluster OpenShift non viene distribuito correttamente, il portale di Azure fornirà un output degli errori. L'output potrebbe risultare difficile da leggere, rendendo complicata l'identificazione del problema. Eseguire rapidamente un'analisi di questo output per il codice di uscita 3, 4 o 5. Di seguito vengono fornite informazioni su questi tre codici di uscita:

- Codice di uscita 3: Il nome utente/password o l'ID organizzazione/chiave di attivazione della sottoscrizione Red Hat non è corretto
- Codice di uscita 4: L'ID del pool di Red Hat non è corretto o non ci sono diritti disponibili
- Codice di uscita 5: Non è possibile eseguire il provisioning del volume del pool thin

Per tutti gli altri codici di uscita, connettersi agli host tramite ssh per visualizzare i file di log.

**OpenShift Container Platform**

Stabilire una connessione SSH all'host del playbook di Ansible. Per il modello o l'offerta del Marketplace, usare il bastion host. Dal bastion, è possibile stabilire una connessione SSH a tutti gli altri nodi del cluster (master, infra, CNS, calcolo). È necessario essere radice per visualizzare i file di log. Siccome la radice è disabilitata per l'accesso SSH per impostazione predefinita, non usare la radice per stabilire una connessione SSH ad altri nodi.

**OKD**

Stabilire una connessione SSH all'host del playbook di Ansible. Per il modello OKD (versione 3.9 e precedenti), usare l'host master-0. Per il modello OKD (versione 3.10 e successive), usare il bastion host. Dall'host del playbook di Ansible, è possibile stabilire una connessione SSH a tutti gli altri nodi del cluster (master, infranodo, CNS, calcolo). È necessario essere radice (sudo su-) per visualizzare i file di log. Siccome la radice è disabilitata per l'accesso SSH per impostazione predefinita, non usare la radice per stabilire una connessione SSH ad altri nodi.

## <a name="log-files"></a>File di log

I file di log (stderr e stdout) per gli script di preparazione host si `/var/lib/waagent/custom-script/download/0` trovano in in tutti gli host. Se si è verificato un errore durante la preparazione dell'host, è possibile visualizzare questi file di log per determinare l'errore.

Se gli script di preparazione sono stati eseguiti correttamente, sarà necessario esaminare `/var/lib/waagent/custom-script/download/1` i file di log nella directory dell'host Ansible PlayBook. Se l'errore si è verificato durante l'installazione di OpenShift, il file stdout visualizzerà l'errore. Riportare queste informazioni quando si contatta il supporto tecnico per ulteriore assistenza.

Output di esempio

```json
TASK [openshift_storage_glusterfs : Load heketi topology] **********************
fatal: [mycluster-master-0]: FAILED! => {"changed": true, "cmd": ["oc", "--config=/tmp/openshift-glusterfs-ansible-IbhnUM/admin.kubeconfig", "rsh", "--namespace=glusterfs", "deploy-heketi-storage-1-d9xl5", "heketi-cli", "-s", "http://localhost:8080", "--user", "admin", "--secret", "VuoJURT0/96E42Vv8+XHfsFpSS8R20rH1OiMs3OqARQ=", "topology", "load", "--json=/tmp/openshift-glusterfs-ansible-IbhnUM/topology.json", "2>&1"], "delta": "0:00:21.477831", "end": "2018-05-20 02:49:11.912899", "failed": true, "failed_when_result": true, "rc": 0, "start": "2018-05-20 02:48:50.435068", "stderr": "", "stderr_lines": [], "stdout": "Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2\n\tAllowing file volumes on cluster.\n\tAllowing block volumes on cluster.\n\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4\n\t\tAdding device /dev/sdd ... OK\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131\n\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdd ... OK\n\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\t\tAdding device /dev/sdd ... OK", "stdout_lines": ["Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2", "\tAllowing file volumes on cluster.", "\tAllowing block volumes on cluster.", "\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4", "\t\tAdding device /dev/sdd ... OK", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131", "\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdd ... OK", "\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\t\tAdding device /dev/sdd ... OK"]}

PLAY RECAP *********************************************************************
mycluster-cns-0       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-1       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-2       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-infra-0     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-1     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-2     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-master-0    : ok=502  changed=198  unreachable=0    failed=1   
mycluster-master-1    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-master-2    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-node-0      : ok=143  changed=55   unreachable=0    failed=0   
mycluster-node-1      : ok=143  changed=55   unreachable=0    failed=0   
localhost                  : ok=13   changed=0    unreachable=0    failed=0   

INSTALLER STATUS ***************************************************************
Initialization             : Complete (0:00:39)
Health Check               : Complete (0:00:24)
etcd Install               : Complete (0:01:24)
Master Install             : Complete (0:14:59)
Master Additional Install  : Complete (0:01:10)
Node Install               : Complete (0:10:58)
GlusterFS Install          : In Progress (0:03:33)
    This phase can be restarted by running: playbooks/openshift-glusterfs/config.yml

Failure summary:

  1. Hosts:    mycluster-master-0
     Play:     Configure GlusterFS
     Task:     Load heketi topology
     Message:  Failed without returning a message.
```

Gli errori più comuni durante l'installazione sono:

1. La chiave privata ha una passphrase
2. Il segreto dell'insieme di credenziali delle chiavi con chiave privata non è stato creato correttamente
3. Le credenziali dell'entità servizio non sono state immesse in modo corretto
4. L'entità servizio non ha accesso come collaboratore al gruppo di risorse

### <a name="private-key-has-a-passphrase"></a>La chiave privata ha una passphrase

Verrà visualizzato un errore per cui è stata negata l'autorizzazione per SSH. eseguire ssh nell'host PlayBook Ansible per verificare la presenza di una passphrase sulla chiave privata.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>Il segreto dell'insieme di credenziali delle chiavi con chiave privata non è stato creato correttamente

La chiave privata viene copiata nell'host PlayBook di Ansible-~/.ssh/id_rsa. Verificare che il file sia corretto. Eseguire un test aprendo una sessione SSH in uno dei nodi del cluster dall'host del playbook di Ansible.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>Le credenziali dell'entità servizio non sono state immesse in modo corretto

Quando è stato fornito l'input per il modello o l'offerta del Marketplace, non sono state specificate le informazioni corrette. Assicurarsi di usare l'appId (clientId) e la password (clientSecret) corretti per l'entità servizio. Verificare eseguendo il seguente comando CLI di Azure.

```bash
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>L'entità servizio non ha accesso come collaboratore al gruppo di risorse

Se il provider di servizi cloud di Azure è abilitato, l'entità servizio usata deve avere accesso come collaboratore al gruppo di risorse. Verificare eseguendo il seguente comando CLI di Azure.

```bash
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>Strumenti aggiuntivi

Per alcuni errori, è anche possibile usare i comandi seguenti per ottenere ulteriori informazioni:

1. > del \<servizio di stato systemctl
2. journalctl -xe
