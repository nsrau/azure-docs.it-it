---
title: Panoramica del supporto di cloud-init per macchine virtuali Linux in Azure
description: Panoramica delle funzionalità cloud-init per configurare una macchina virtuale in fase di provisioning in Azure.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/19/2019
ms.author: danis
ms.openlocfilehash: 9e42229b08d7817b64c66c4ab23877c837339475
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83827319"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Supporto di cloud-init per le macchine virtuali in Azure
Questo articolo descrive il supporto disponibile per [cloud-init](https://cloudinit.readthedocs.io) per configurare una macchina virtuale (VM) o i set di scalabilità di macchine virtuali in fase di provisioning in Azure. Queste configurazioni cloud-init vengono eseguite al primo avvio dopo il provisioning delle risorse da parte di Azure.  

Il provisioning delle VM è il processo in cui Azure passerà i valori del parametro VM Create, ad esempio nome host, nome utente, password e così via e li renderà disponibili alla VM all'avvio. Un 'agente di provisioning' utilizzerà tali valori, configurerà la macchina virtuale e restituirà un report al termine dell'operazione. 

Azure supporta due agenti di provisioning, [cloud-init](https://cloudinit.readthedocs.io) e [Azure Linux Agent (WALA)](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux).

## <a name="cloud-init-overview"></a>Panoramica di cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) è un approccio ampiamente diffuso per personalizzare una macchina virtuale Linux al primo avvio. Cloud-init consente di installare pacchetti e scrivere file o configurare utenti e impostazioni di sicurezza. Poiché cloud-init viene chiamato durante il processo di avvio iniziale, non è necessario applicare passaggi aggiuntivi o agenti alla configurazione.  Per altre informazioni su come formattare correttamente i file `#cloud-config` o altri input, vedere il [sito della documentazione di cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  I file `#cloud-config` sono file di testo codificati in formato base64.

Cloud-init funziona anche tra distribuzioni. Ad esempio, non si usa **apt-get install** o **yum install** per installare un pacchetto. In alternativa, è possibile definire un elenco di pacchetti da installare. Cloud-init userà automaticamente lo strumento nativo di gestione pacchetti per la distribuzione selezionata.

Microsoft sta collaborando attivamente con i partner di distribuzione Linux approvati per offrire immagini abilitate per cloud-init in Azure Marketplace. Queste immagini permettono il funzionamento uniforme di distribuzioni e configurazioni di cloud-init con macchine virtuali e set di scalabilità di macchine virtuali. Inizialmente collaboriamo con i partner di distribuzioni Linux approvati e a monte per garantire che cloud-init funzioni con il sistema operativo in Azure, quindi i pacchetti vengono aggiornati e resi disponibili pubblicamente nei repository dei pacchetti di distribuzioni. 

Sono previste due fasi per rendere disponibile cloud-init per il sistema operativo delle distribuzioni Linux approvate in Azure, il supporto dei pacchetti e il supporto delle immagini:
* Il 'supporto dei pacchetti cloud-init in' fa riferimento ai pacchetti cloud-init supportati in futuro o in anteprima, che possono quindi essere usati nel sistema operativo in un'immagine personalizzata.
* Per 'immagine pronta per cloud-init' si intende se l'immagine è già configurata per l'uso di cloud-init.


### <a name="canonical"></a>Canonical
| Editore/versione| Offerta | SKU | Versione | Immagine pronta per cloud-init | Supporto dei pacchetti cloud-init in Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical 20.04 |UbuntuServer |18.04-LTS |più recenti |sì | sì |
|Canonical 18.04 |UbuntuServer |18.04-LTS |più recenti |sì | sì |
|Canonical 16.04|UbuntuServer |16.04-LTS |più recenti |sì | sì |
|Canonical 14.04|UbuntuServer |14.04.5-LTS |più recenti |sì | sì |

### <a name="rhel"></a>RHEL
| Editore/versione | Offerta | SKU | Versione | Immagine pronta per cloud-init | Supporto dei pacchetti cloud-init in Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |sì | Sì: supporto a partire dalla versione del pacchetto *18.2-1.el7_6.2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | Sì (si noti che si tratta di un'immagine in anteprima e, quando tutte le immagini RHEL 7.7 supporteranno cloud-init, verrà rimossa il 1° settembre 2020) | Sì: supporto a partire dalla versione del pacchetto *18.5-6.el7*|
|RedHat 7.7 (Gen1)|RHEL |7.7 | 7.7.2020051912 | No: aggiornamenti dell'immagine distribuiti in anteprima, da completare a fine maggio | Sì: supporto a partire dalla versione del pacchetto *18.5-6.el7*|
|RedHat 7.7 (Gen2)|RHEL | 77-gen2 | 7.7.2020051913 | No: aggiornamenti dell'immagine distribuiti in anteprima, da completare a fine maggio | Sì: supporto a partire dalla versione del pacchetto *18.5-6.el7*|
|RedHat 7.7 (Gen1)|RHEL |7-LVM | 7.7.2020051921 | No: aggiornamenti dell'immagine distribuiti in anteprima, da completare a fine maggio | Sì: supporto a partire dalla versione del pacchetto *18.5-6.el7*|
|RedHat 7.7 (Gen2)|RHEL | 7lvm-gen2 | 7.7.2020051922  | No: aggiornamenti dell'immagine distribuiti in anteprima, da completare a fine maggio | Sì: supporto a partire dalla versione del pacchetto *18.5-6.el7*|
|RedHat 7.7 (Gen1) |rhel-byos | rhel-lvm77 | 7.7.20200416 | No: aggiornamenti dell'immagine distribuiti in anteprima, da completare a fine maggio  | Sì: supporto a partire dalla versione del pacchetto *18.5-6.el7*|
|RedHat 8.1 (Gen1) |RHEL |8.1-ci |8.1.2020042511 | Sì (si noti che si tratta di un'immagine in anteprima e, quando tutte le immagini RHEL 8.1 supporteranno cloud-init, verrà rimossa il 1° agosto 2020) | No, tempo di completamento stimato per il supporto completo giugno 2020|
|RedHat 8.1 (Gen2) |RHEL |81-ci-gen2 |8.1.2020042524 | Sì (si noti che si tratta di un'immagine in anteprima e, quando tutte le immagini RHEL 8.1 supporteranno cloud-init, verrà rimossa il 1° agosto 2020) | No, tempo di completamento stimato per il supporto completo giugno 2020 |

RedHat: le immagini RHEL 7.8 e 8.2 (Gen1 e Gen2) vengono sottoposte a provisioning tramite cloud-init.

### <a name="centos"></a>CentOS

| Editore/versione | Offerta | SKU | Versione | Immagine pronta per cloud-init | Supporto dei pacchetti cloud-init in Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7.7 |CentOS |7-CI |7.7.20190920 |Sì (si noti che si tratta di un'immagine in anteprima e, quando tutte le immagini CentOS 7.7 supporteranno cloud-init, verrà rimossa il 1° settembre 2020) | Sì: supporto a partire dalla versione del pacchetto *18.5-3.el7.centos*|

* Le immagini CentOS 7.7 che saranno abilitate per cloud-init verranno aggiornate a giugno 2020 
* Le immagini CentOS 7.8 vengono sottoposte a provisioning tramite cloud-init.


### <a name="oracle"></a>Oracle

| Editore/versione | Offerta | SKU | Versione | Immagine pronta per cloud-init | Supporto dei pacchetti cloud-init in Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7.7 |Oracle-Linux |77-ci |7.7.01| Immagine in anteprima (si noti che si tratta di un'immagine in anteprima e, quando tutte le immagini Oracle 7.7 supporteranno cloud-init, verrà rimossa a metà 2020 con apposito avviso) | No, in anteprima, il pacchetto è *18.5-3.0.1.el7*

### <a name="suse-sles"></a>SuSE SLES
| Editore/versione | Offerta | SKU | Versione | Immagine pronta per cloud-init | Supporto dei pacchetti cloud-init in Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|SUSE SLES 15 SP1 |suse |sles-15-sp1-basic |cloud-init-preview| Per informazioni dettagliate, vedere il [blog di cloud-init per SUSE](https://suse.com/c/clout-init-coming-to-suse-images-in-azure/) | No, in anteprima. |
|SUSE SLES 15 SP1 |suse |sles-15-sp1-basic |gen2-cloud-init-preview| Per informazioni dettagliate, vedere il [blog di cloud-init per SUSE](https://suse.com/c/clout-init-coming-to-suse-images-in-azure/) | No, in anteprima. |


### <a name="debian"></a>Debian
Attualmente stiamo lavorando al supporto in anteprima, con aggiornamenti previsti a giugno 2020.

Azure Stack supporterà il provisioning delle immagini abilitate per cloud-init.


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Qual è la differenza tra cloud-init e l'agente Linux (WALA)?
WALA è un agente specifico per la piattaforma Azure usato per il provisioning e la configurazione di macchine virtuali e per la gestione delle [estensioni di Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux). 

Stiamo migliorando l'attività di configurazione delle macchine virtuali per l'uso di cloud-init al posto dell'agente Linux per consentire agli attuali clienti di cloud-init di usare gli script cloud-init correnti o ai nuovi clienti di sfruttare le funzionalità avanzate di configurazione di cloud-init. Se sono stati effettuati investimenti in script cloud-init per la configurazione di sistemi Linux, **non sono necessarie altre impostazioni** per elaborarli tramite cloud-init. 

Cloud-init non è in grado di elaborare le estensioni di Azure, quindi WALA è ancora necessario nell'immagine per elaborare le estensioni, ma il codice di provisioning dovrà essere disabilitato, perché le immagini di distribuzioni Linux approvate che vengono convertite per effettuarne il provisioning tramite cloud-init avranno WALA installato e configurato correttamente.

Quando si crea una VM, se non si include l'opzione `--custom-data` dell'interfaccia della riga di comando di Azure in fase di provisioning, cloud-init o WALA accetta i parametri di provisioning minimi necessari per il provisioning e il completamento della distribuzione delle VM con le impostazioni predefinite.  Se si fa riferimento alla configurazione di cloud-init con l'opzione `--custom-data`, tutto il contenuto dei dati personalizzati sarà disponibile per cloud-init all'avvio della VM.

Le configurazioni di cloud-init applicate alle VM non hanno vincoli di tempo e non causano la mancata riuscita della distribuzione dovuta al timeout. Ciò non vale per WALA. Se si cambiano le impostazioni predefinite di WALA per elaborare dati personalizzati, non è possibile superare il limite consentito di 40 minuti del tempo di provisioning totale della VM, altrimenti il comando VM Create genererà un errore.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Distribuzione di una macchina virtuale abilitata per cloud-init
Distribuire una macchina virtuale abilitata per cloud-init è facile come fare riferimento a una distribuzione abilitata per cloud-init.  I gestori di distribuzioni Linux devono scegliere se abilitare e integrare cloud-init nelle proprie immagini pubblicate in Azure. Dopo aver verificato che l'immagine che si vuole distribuire sia abilitata per cloud-init, è possibile usare l'interfaccia della riga di comando di Azure per distribuirla. 

Il primo passaggio della distribuzione di questa immagine è creare un gruppo di risorse con il comando [az group create](/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
Il passaggio successivo consiste nel creare un file denominato *cloud-init.txt* nella shell corrente e incollare la configurazione seguente. Per questo esempio, creare il file in Cloud Shell anziché nel computer locale. È possibile usare qualsiasi editor. Immettere `sensible-editor cloud-init.txt` per creare il file e visualizzare un elenco degli editor disponibili. Scegliere #1 per usare l'editor **nano**. Assicurarsi che l'intero file cloud-init venga copiato correttamente, in particolare la prima riga:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Premere `ctrl-X` per uscire dal file, digitare `y` per salvare il file e premere `enter` per confermare il nome del file alla chiusura.

Il passaggio finale consiste nel creare una macchina virtuale con il comando [az vm create](/cli/azure/vm). 

L'esempio seguente crea una macchina virtuale denominata *centos74* e le chiavi SSH, se non esistono già in un percorso predefinito. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`.  Usare il parametro `--custom-data` per specificare il file di configurazione di cloud-init. Se il file è stato salvato all'esterno della directory di lavoro corrente, specificare il percorso completo della configurazione *cloud-init.txt* . L'esempio seguente crea una macchina virtuale denominata *centos74*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Dopo la creazione della macchina virtuale, l'interfaccia della riga di comando di Azure mostra informazioni specifiche della distribuzione. Prendere nota di `publicIpAddress`. Questo indirizzo viene usato per accedere alla VM.  Sono necessari alcuni minuti per creare la macchina virtuale, installare il pacchetto e avviare l'app. Sono presenti attività in background la cui esecuzione continua dopo che l'interfaccia della riga di comando di Azure è tornata al prompt. È possibile eseguire SSH nella macchina virtuale e seguire i passaggi descritti nella sezione della risoluzione dei problemi per visualizzare i log di cloud-init. 

## <a name="troubleshooting-cloud-init"></a>Risoluzione dei problemi relativi a cloud-init
Al termine del provisioning della macchina virtuale, cloud-init verrà eseguito in tutti i moduli e gli script definiti in `--custom-data` per configurare la macchina virtuale.  Se è necessario correggere eventuali errori o omissioni della configurazione, cercare il nome del modulo (ad esempio, `disk_setup` o `runcmd`) nel log di cloud-init che si trova in **/var/log/cloud-init.log**.

> [!NOTE]
> Non tutti gli errori dei moduli generano un errore di configurazione di cloud-init completo e irreversibile. Ad esempio, se usando il modulo `runcmd` lo script non riesce, cloud-init segnalerà comunque che il provisioning è riuscito perché il modulo runcmd è stato eseguito.

Per altre informazioni sulla registrazione di cloud-init, fare riferimento alla [documentazione di cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>Passaggi successivi
Per esempi cloud-init di modifiche di configurazione, vedere i documenti seguenti:
 
- [Aggiungere un altro utente Linux a una VM](cloudinit-add-user.md)
- [Eseguire uno strumento di gestione di pacchetti per aggiornare pacchetti esistenti al primo avvio](cloudinit-update-vm.md)
- [Modificare il nome host locale della macchina virtuale](cloudinit-update-vm-hostname.md) 
- [Installare un pacchetto dell'applicazione, aggiornare i file di configurazione e inserire le chiavi](tutorial-automate-vm-deployment.md)
 
