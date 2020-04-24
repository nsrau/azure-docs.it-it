---
title: Panoramica del supporto di cloud-init per macchine virtuali Linux in Azure
description: Panoramica delle funzionalità di cloud-init per configurare una macchina virtuale in fase di provisioning in Azure.
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
ms.date: 01/23/2019
ms.author: danis
ms.openlocfilehash: 1f0395956fa6977be5d1d6f4f4faf06b84c094d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79465040"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>supporto di cloud-init per le macchine virtuali in Azure
Questo articolo illustra il supporto esistente per [cloud-init](https://cloudinit.readthedocs.io) per configurare una macchina virtuale (VM) o set di scalabilità di macchine virtuali in fase di provisioning in Azure. Queste configurazioni cloud-init vengono eseguite al primo avvio dopo il provisioning delle risorse da parte di Azure.  

Il provisioning delle VM è il processo in cui Azure passerà alla macchina virtuale la creazione di valori di parametri, ad esempio nome host, nome utente, password e così via, e li renderà disponibili alla macchina virtuale durante l'avvio. Un "agente di provisioning" utilizzerà tali valori, configurerà la macchina virtuale e restituirà un report al termine dell'operazione. 

Azure supporta due agenti di provisioning [cloud-init](https://cloudinit.readthedocs.io)e l' [agente Linux di Azure (wala)](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux).

## <a name="cloud-init-overview"></a>Panoramica di cloud-init
[cloud-init](https://cloudinit.readthedocs.io) è un approccio ampiamente usato per personalizzare una macchina virtuale Linux mentre viene avviata per la prima volta. Cloud-init consente di installare pacchetti e scrivere file o configurare utenti e impostazioni di sicurezza. Poiché cloud-init viene chiamato durante il processo di avvio iniziale, non è necessario applicare passaggi aggiuntivi o agenti alla configurazione.  Per altre informazioni su come formattare correttamente i `#cloud-config` file o altri input, vedere il sito della documentazione di [cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  I file `#cloud-config` sono file di testo codificati in formato base64.

cloud-init funziona anche tra le distribuzioni. Ad esempio, non si usa **apt-get install** o **yum install** per installare un pacchetto. In alternativa, è possibile definire un elenco di pacchetti da installare. cloud-init usa automaticamente lo strumento di gestione dei pacchetti nativi per la distribuzione selezionata.

Microsoft sta collaborando attivamente con i partner di distribuzione Linux approvati per offrire immagini abilitate per cloud-init in Azure Marketplace. Queste immagini permettono il funzionamento uniforme di distribuzioni e configurazioni di cloud-init con macchine virtuali e set di scalabilità di macchine virtuali. Inizialmente collaboriamo con i partner di distribuzioni di Linux approvati e upstream per garantire le funzioni cloud-init con il sistema operativo in Azure, quindi i pacchetti vengono aggiornati e resi disponibili pubblicamente nei repository dei pacchetti di distribuzione. 

Ci sono due fasi per rendere disponibile cloud-init per il sistema operativo della distro Linux approvata in Azure, il supporto per i pacchetti e quindi il supporto delle immagini:
* "supporto dei pacchetti cloud-init in Azure" i documenti di cloud-init in poi sono supportati o in anteprima, quindi è possibile usare questi pacchetti con il sistema operativo in un'immagine personalizzata.
* documenti ' image cloud-init Ready ' se l'immagine è già configurata per l'uso di cloud-init.


### <a name="canonical"></a>Canonical
| Server di pubblicazione/versione| Offerta | SKU | Versione | Image cloud-init Ready | supporto del pacchetto cloud-init in Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonico 18,04 |UbuntuServer |18.04-LTS |più recenti |sì | sì |
|Canonico 16,04|UbuntuServer |16.04-LTS |più recenti |sì | sì |
|Canonico 14,04|UbuntuServer |14.04.5-LTS |più recenti |sì | sì |

### <a name="rhel"></a>RHEL
| Server di pubblicazione/versione | Offerta | SKU | Versione | Image cloud-init Ready | supporto del pacchetto cloud-init in Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |sì | Sì-supporto dalla versione del pacchetto: *18.2-1. el7_6.2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | Sì (si noti che si tratta di un'immagine di anteprima e, una volta che tutte le immagini RHEL 7,7 supportano cloud-init, questa verrà rimossa a metà 2020, verrà visualizzato un avviso) | Sì-supporto dalla versione del pacchetto: *18.5 3. EL7*|
|RedHat 7.7 |RHEL |7-RAW | n/d| aggiornamenti senza immagini per completare la fine del 2020 aprile| Sì-supporto dalla versione del pacchetto: *18.5 3. EL7*|
|RedHat 7.7 |RHEL |7-LVM | n/d| aggiornamenti senza immagini per completare la fine di aprile| Sì-supporto dalla versione del pacchetto: *18.5 3. EL7*|
|RedHat 7.7 |RHEL |7.7 | n/d| aggiornamenti senza immagini per completare la fine di aprile | Sì-supporto dalla versione del pacchetto: *18.5 3. EL7*|
|RedHat 7.7 |RHEL-BYOS | RHEL-lvm77 | n/d|aggiornamenti senza immagini per completare la fine di aprile  | Sì-supporto dalla versione del pacchetto: *18.5 3. EL7*|

### <a name="centos"></a>CentOS

| Server di pubblicazione/versione | Offerta | SKU | Versione | Image cloud-init Ready | supporto del pacchetto cloud-init in Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7,7 |CentOS |7-CI |7.7.20190920 |Sì (si noti che si tratta di un'immagine di anteprima e, una volta che tutte le immagini CentOS 7,7 supportano cloud-init, questa operazione verrà rimossa a metà 2020, verrà visualizzato un avviso) | Sì-supporto della versione del pacchetto: *18.5 3. EL7. CentOS*|

* Le immagini CentOS 7,7 che saranno abilitate per cloud-init verranno aggiornate qui nel 2020 marzo 

### <a name="oracle"></a>Oracle

| Server di pubblicazione/versione | Offerta | SKU | Versione | Image cloud-init Ready | supporto del pacchetto cloud-init in Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7,7 |Oracle-Linux |77-ci |7.7.01| Anteprima immagine (si noti che si tratta di un'immagine di anteprima e una volta che tutte le immagini Oracle 7,7 supportano cloud-init, questa operazione verrà rimossa a metà 2020, verrà visualizzato un avviso) | No, in anteprima, il pacchetto è: *18.5-3.0.1. EL7*

### <a name="debian--suse-sles"></a>Debian & SuSE SLES
Attualmente si sta lavorando al supporto in anteprima, si prevede che gli aggiornamenti vengano eseguiti a febbraio e al 2020 marzo.

Attualmente Azure Stack supporterà il provisioning delle immagini abilitate per cloud-init.


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Qual è la differenza tra cloud-init e l'agente Linux (WALA)?
WALA è un agente specifico della piattaforma Azure usato per il provisioning e la configurazione di macchine virtuali e per gestire le [estensioni di Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux). 

Si sta migliorando l'attività di configurazione delle macchine virtuali per l'uso di cloud-init anziché dell'agente Linux per consentire ai clienti esistenti di cloud-init di usare gli script cloud-init attuali o nuovi clienti per sfruttare le funzionalità di configurazione complete di cloud-init. Se si hanno investimenti esistenti negli script cloud-init per la configurazione dei sistemi Linux, non sono **necessarie impostazioni aggiuntive** per abilitare il processo cloud-init. 

cloud-init non è in grado di elaborare le estensioni di Azure, quindi WALA è ancora necessario nell'immagine per elaborare le estensioni, ma è necessario che il codice di provisioning sia disabilitato, per le immagini di distribuzioni di Linux approvate che vengono convertite nel provisioning da cloud-init, i file WALA sono installati e vengono installati correttamente.

Quando si crea una macchina virtuale, se non si include l'opzione `--custom-data` dell'interfaccia della riga di comando di Azure in fase di provisioning, cloud-init o wala accetta i parametri di provisioning delle macchine virtuali minimi necessari per eseguire il provisioning della macchina virtuale e completare la distribuzione con le impostazioni predefinite.  Se si fa riferimento alla configurazione cloud-init con `--custom-data` l'opzione, qualunque sia contenuto nei dati personalizzati sarà disponibile per cloud-init quando viene avviata la macchina virtuale.

le configurazioni cloud-init applicate alle macchine virtuali non hanno vincoli di tempo e non determinano un errore di distribuzione. Questa operazione non è valida per WALA. Se si modificano le impostazioni predefinite di WALA per elaborare i dati personalizzati, non è possibile superare il limite di tempo totale di provisioning della macchina virtuale di 40 minuti. in tal caso, la creazione della macchina virtuale avrà esito negativo.

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
 
