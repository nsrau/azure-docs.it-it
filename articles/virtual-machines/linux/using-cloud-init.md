---
title: Panoramica del supporto cloud-init per le macchine virtuali Linux in AzureOverview of cloud-init support for Linux VMs in Azure
description: Panoramica delle funzionalità di cloud-init per configurare una macchina virtuale in fase di provisioning in Azure.Overview of cloud-init capabilities to configure a VM at provisioning time in Azure.
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
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>supporto cloud-init per le macchine virtuali in Azure
Questo articolo illustra il supporto esistente per cloud-init per configurare una macchina virtuale (VM) o set di scalabilità di macchine virtuali al momento del provisioning in Azure.This article explains the support that exists for [cloud-init](https://cloudinit.readthedocs.io) to configure a virtual machine (VM) or virtual machine scale sets at provisioning time in Azure. Queste configurazioni cloud-init vengono eseguite al primo avvio dopo il provisioning delle risorse da Parte di Azure.These cloud-init configurations are run on first boot once the resources have been provisioned by Azure.  

Provisioning della macchina virtuale è il processo in cui Azure passerà i valori dei parametri di creazione della macchina virtuale, ad esempio nome host, nome utente, password e così via, e li renderà disponibili per la macchina virtuale all'avvio. Un 'agente di provisioning' utilizzerà tali valori, configurerà la macchina virtuale e riporterà il report al termine del processo. 

Azure supporta due agenti di provisioning [cloud-init](https://cloudinit.readthedocs.io)e l'agente Linux di [Azure (WALA).](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)

## <a name="cloud-init-overview"></a>panoramica di cloud-init
[cloud-init](https://cloudinit.readthedocs.io) è un approccio ampiamente usato per personalizzare una macchina virtuale Linux quando si avvia per la prima volta. Cloud-init consente di installare pacchetti e scrivere file o configurare utenti e impostazioni di sicurezza. Poiché cloud-init viene chiamato durante il processo di avvio iniziale, non è necessario applicare passaggi aggiuntivi o agenti alla configurazione.  Per ulteriori informazioni su come `#cloud-config` formattare correttamente i file o altri input, vedere il sito della [documentazione cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  I file `#cloud-config` sono file di testo codificati in formato base64.

cloud-init funziona anche tra le distribuzioni. Ad esempio, non si usa **apt-get install** o **yum install** per installare un pacchetto. In alternativa, è possibile definire un elenco di pacchetti da installare. cloud-init utilizza automaticamente lo strumento di gestione dei pacchetti nativo per la distribuzione selezionata.

Microsoft sta collaborando attivamente con i partner di distribuzione Linux approvati per offrire immagini abilitate per cloud-init in Azure Marketplace. Queste immagini permettono il funzionamento uniforme di distribuzioni e configurazioni di cloud-init con macchine virtuali e set di scalabilità di macchine virtuali. Inizialmente collaboriamo con i partner distro Linux approvati e a monte per garantire funzioni cloud-init con il sistema operativo in Azure, quindi i pacchetti vengono aggiornati e resi disponibili pubblicamente nei repository dei pacchetti distro. 

Esistono due fasi per rendere cloud-init disponibile per il sistema operativo Linux distro approvato in Azure, il supporto del pacchetto e quindi il supporto delle immagini:There are two stages to making cloud-init available to the endorsed Linux distro OS's on Azure, package support, and then image support:
* 'cloud-init package support in Azure' documenti che cloud-init pacchetti inavanti in poi sono supportati o in anteprima, in modo da poter utilizzare questi pacchetti con il sistema operativo in un'immagine personalizzata.
* Documenti 'image cloud-init ready' se l'immagine è già configurata per l'utilizzo di cloud-init.


### <a name="canonical"></a>Canonical
| Editore / Versione| Offerta | SKU | Versione | image cloud-init ready | Supporto del pacchetto cloud-init in Azurecloud-init package support on Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonico 18.04 |UbuntuServer |18.04-LTS |più recenti |sì | sì |
|Canonico 16.04|UbuntuServer |16.04-LTS |più recenti |sì | sì |
|Canonico 14.04|UbuntuServer |14.04.5-LTS |più recenti |sì | sì |

### <a name="rhel"></a>RHEL
| Editore / Versione | Offerta | SKU | Versione | image cloud-init ready | Supporto del pacchetto cloud-init in Azurecloud-init package support on Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |sì | sì - supporto dalla versione del pacchetto: *18.2-1.el7_6.2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | sì (notare che questa è un'immagine di anteprima, e una volta che tutte le immagini RHEL 7.7 supportano cloud-init, questo verrà rimosso a metà 2020, verrà dato un avviso) | sì - supporto dalla versione del pacchetto: *18.5-3.el7*|
|RedHat 7.7 |RHEL |7-RAW | n/d| no - aggiornamenti delle immagini per completare la fine di aprile 2020| sì - supporto dalla versione del pacchetto: *18.5-3.el7*|
|RedHat 7.7 |RHEL |7-LVM | n/d| no - aggiornamenti delle immagini per completare la fine di aprile| sì - supporto dalla versione del pacchetto: *18.5-3.el7*|
|RedHat 7.7 |RHEL |7.7 | n/d| no - aggiornamenti delle immagini per completare la fine di aprile | sì - supporto dalla versione del pacchetto: *18.5-3.el7*|
|RedHat 7.7 |rel-byos | rhel-lvm77 | n/d|no - aggiornamenti delle immagini per completare la fine di aprile  | sì - supporto dalla versione del pacchetto: *18.5-3.el7*|

### <a name="centos"></a>CentOS

| Editore / Versione | Offerta | SKU | Versione | image cloud-init ready | Supporto del pacchetto cloud-init in Azurecloud-init package support on Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7.7 |CentOS |7-CI |7.7.20190920 |sì (notare che questa è un'immagine di anteprima, e una volta che tutte le immagini CentOS 7.7 supportano cloud-init, questo verrà rimosso a metà 2020, verrà dato un avviso) | sì - supporto dalla versione del pacchetto: *18.5-3.el7.centos*|

* Le immagini di CentOS 7.7 che saranno abilitate per cloud-init saranno aggiornate qui a marzo 2020 

### <a name="oracle"></a>Oracle

| Editore / Versione | Offerta | SKU | Versione | image cloud-init ready | Supporto del pacchetto cloud-init in Azurecloud-init package support on Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracolo 7.7 |Oracle-Linux |77-ci |7.7.01| immagine di anteprima (notare che questa è un'immagine di anteprima, ed è una volta che tutte le immagini Oracle 7.7 supportano cloud-init, questo verrà rimosso metà 2020, avviso sarà dato) | no, in anteprima, il pacchetto è: *18.5-3.0.1.el7*

### <a name="debian--suse-sles"></a>Debian & SuSE SLES
Attualmente stiamo lavorando per visualizzare in anteprima il supporto, prevediamo aggiornamenti a febbraio e marzo 2020.

Attualmente Azure Stack supporterà il provisioning delle immagini abilitate per l'inte nel cloud.


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Qual è la differenza tra cloud-init e l'agente Linux (WALA)?
WALA è un agente specifico della piattaforma Azure utilizzato per eseguire il provisioning e configurare le macchine virtuali e gestire [le estensioni](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)di Azure.WALA is an Azure platform-specific agent used to provision and configure VMs, and handle Azure extensions . 

Stiamo migliorando il compito di configurare le macchine virtuali per l'uso di cloud-init invece dell'agente Linux al fine di consentire ai clienti cloud-init esistenti di utilizzare i loro script cloud-init correnti o nuovi clienti per sfruttare le funzionalità di configurazione cloud-init avanzate. Se si dispone di investimenti esistenti in script cloud-init per la configurazione di sistemi Linux, non sono **necessarie impostazioni aggiuntive** per abilitarli nell'inserimento nel cloud. 

cloud-init non è in grado di elaborare le estensioni di Azure, pertanto WALA è ancora necessario nell'immagine per elaborare le estensioni, ma dovrà avere il codice di provisioning disabilitato, per le immagini distros Linux approvate che vengono convertite in provisioning tramite cloud-init, avranno WALA installato e l'installazione corretta.

Quando si crea una macchina virtuale, `--custom-data` se non si include l'opzione dell'interfaccia della riga di comando di Azure in fase di provisioning, cloud-init o WALA accetta i parametri di provisioning minimi necessari per eseguire il provisioning della macchina virtuale e completare la distribuzione con le impostazioni predefinite.  Se si fa riferimento alla configurazione `--custom-data` cloud-init con lo switch, tutto ciò che è contenuto nei dati personalizzati sarà disponibile per cloud-init all'avvio della macchina virtuale.

Le configurazioni cloud-init applicate alle macchine virtuali non hanno vincoli di tempo e non causeranno l'esito negativo di una distribuzione in base al timeout. Questo non è vero per WALA, se si modifica il waLA predefinito per elaborare i dati personalizzati, non può superare il limite totale di tempo di provisioning della macchina virtuale di 40 minuti, in caso affermativo, la creazione della macchina virtuale avrà esito negativo.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Distribuzione di una macchina virtuale abilitata per cloud-init
Distribuire una macchina virtuale abilitata per cloud-init è facile come fare riferimento a una distribuzione abilitata per cloud-init.  I gestori di distribuzioni Linux devono scegliere se abilitare e integrare cloud-init nelle proprie immagini pubblicate in Azure. Dopo aver verificato che l'immagine che si vuole distribuire sia abilitata per cloud-init, è possibile usare l'interfaccia della riga di comando di Azure per distribuirla. 

Il primo passaggio della distribuzione di questa immagine è creare un gruppo di risorse con il comando [az group create](/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nel percorso *eastus.*

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
 
