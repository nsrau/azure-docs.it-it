---
title: Estensioni e funzionalità delle macchine virtuali di Azure per Linux | Microsoft Docs
description: Informazioni sulle estensioni disponibili per le macchine virtuali di Azure, raggruppate in base a ciò che forniscono o migliorano.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.openlocfilehash: bf6eca33eb1448eb84065fb7fe184d01e77feb61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60387274"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Estensioni della macchina virtuale e funzionalità per Linux

Le estensioni macchina virtuale di Azure sono piccole applicazioni che eseguono attività di configurazione e automazione post-distribuzione nelle macchine virtuali di Azure. Ad esempio, se una macchina virtuale richiede l'installazione di software, la protezione antivirus o l'esecuzione di uno script al suo interno, è possibile usare un'estensione macchina virtuale. Le estensioni macchina virtuale di Azure possono essere eseguite tramite l'interfaccia della riga di comando di Azure, PowerShell, i modelli di Azure Resource Manager e il portale di Azure. Le estensioni possono essere aggregate con una nuova distribuzione di macchina virtuale o eseguite su un sistema esistente.

Questo articolo offre una panoramica delle estensioni macchina virtuale, i prerequisiti per l'uso di queste estensioni di Azure e le indicazioni su come rilevare, gestire e rimuovere le estensioni. Questo articolo offre informazioni generali perché sono disponibili molte estensioni macchina virtuale, ognuna con una configurazione potenzialmente univoca. I dettagli sono disponibili nel documento specifico della singola estensione.

## <a name="use-cases-and-samples"></a>Casi d'uso ed esempi

Sono disponibili numerose estensioni della macchina virtuale di Azure, ognuna con uno specifico caso d'uso. Di seguito sono riportati alcuni esempi:

- Applicare le configurazioni dello stato desiderato tramite PowerShell a una macchina virtuale usando l'estensione DSC per Linux. Per altre informazioni, vedere l'argomento relativo all'[Estensione DSC (Desired State Configuration) di Azure](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Configurare il monitoraggio di una macchina virtuale con l'estensione macchina virtuale Microsoft Monitoring Agent. Per altre informazioni, vedere [Come monitorare una macchina virtuale Linux in Azure](../linux/tutorial-monitoring.md).
- Configurare il monitoraggio dell'infrastruttura di Azure con l'estensione Chef o Datadog. Per altre informazioni, vedere i [documenti di Chef](https://docs.chef.io/azure_portal.html) o il [blog Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Oltre alle estensioni specifiche del processo, è disponibile un'estensione Script personalizzato per le macchine virtuali Linux e Windows. L'estensione dello script personalizzata per Linux consente l'esecuzione di qualsiasi script Bash su una VM. Gli script personalizzati sono utili per la progettazione di distribuzioni di Azure che richiedono una configurazione in aggiunta a quella offerta dagli strumenti nativi di Azure. Per altre informazioni, vedere [Estensione di script personalizzata per le VM Linux](custom-script-linux.md).

## <a name="prerequisites"></a>Prerequisiti

Per gestire l'estensione nella macchina virtuale, è necessario aver installato l'agente Linux di Azure. Alcune estensioni individuali hanno prerequisiti, ad esempio l'accesso alle risorse o alle dipendenze.

### <a name="azure-vm-agent"></a>Agente VM di Azure

L'agente di macchine virtuali di Azure gestisce le interazioni tra una macchina virtuale di Azure e il controller di infrastruttura di Azure. L'agente di macchine virtuali è responsabile di molti aspetti funzionali della distribuzione e della gestione delle macchine virtuali di Azure, tra cui l'esecuzione delle estensioni macchina virtuale. L'agente di macchine virtuali di Azure è preinstallato nelle immagini di Azure Marketplace e può essere installato manualmente nei sistemi operativi supportati. L'agente di VM di Azure per Linux è noto come agente Linux.

Per informazioni sui sistemi operativi supportati e per le istruzioni di installazione, vedere [Agente delle macchine virtuali di Azure](agent-linux.md).

#### <a name="supported-agent-versions"></a>Versioni supportate dell'agente

Per garantire la migliore esperienza possibile, sono previsti requisiti minimi per le versioni supportate dell'agente. Per altre informazioni, vedere [questo articolo](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Sistemi operativi supportati

L'agente Linux viene eseguito su più sistemi operativi, tuttavia il framework delle estensioni prevede un limite per i sistemi operativi. Per altre informazioni, vedere [questo articolo](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Alcune estensioni non sono supportate in tutti i sistemi operativi e possono generare il *codice di errore 51, "Sistema operativo non supportato"*. Vedere la documentazione della singola estensione per informazioni sul supporto.

#### <a name="network-access"></a>Accesso alla rete

I pacchetti di estensioni vengono scaricati dal repository delle estensioni di Archiviazione di Azure, mentre i caricamenti dello stato delle estensioni vengono pubblicati in Archiviazione di Azure. Se si usa una versione [supportata](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) degli agenti, non è necessario consentire l'accesso ad Archiviazione di Azure nell'area della macchina virtuale, perché è possibile usare l'agente per reindirizzare la comunicazione al controller di infrastruttura di Azure. Se è in uso una versione non supportata dell'agente, è necessario consentire l'accesso in uscita ad Archiviazione di Azure in tale area dalla macchina virtuale.

> [!IMPORTANT]
> Se l'accesso a *168.63.129.16* è stato bloccato con il firewall guest, le estensioni non rispettano quanto specificato sopra.

Gli agenti possono essere usati solo per caricare i pacchetti di estensioni e lo stato della creazione di report. Ad esempio, se l'installazione di un'estensione richiede il download di uno script da GitHub (script personalizzato) o ha bisogno di accedere ad Archiviazione di Azure (Backup di Azure), allora è necessario aprire altre porte del firewall/gruppo di sicurezza di rete. Estensioni diverse hanno requisiti diversi, perché sono applicazioni indipendenti. È possibile consentire l'accesso ad Archiviazione di Azure per le estensioni usando i tag di servizio del gruppo di sicurezza di rete per [Archiviazione](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Per reindirizzare le richieste di traffico dell'agente, l'agente Linux include il supporto del server proxy. Tuttavia, il supporto del server proxy non applica le estensioni. È necessario configurare ogni singola estensione per usare un proxy.

## <a name="discover-vm-extensions"></a>Individuare le estensioni della macchina virtuale

Sono disponibili molte estensioni diverse delle macchine virtuali da usare con macchine virtuali di Azure. Per visualizzare un elenco completo, usare [az vm extension image list](/cli/azure/vm/extension/image#az-vm-extension-image-list). L'esempio seguente elenca tutte le estensioni disponibili nella posizione *westus*:

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Eseguire le estensioni della macchina virtuale

Le estensioni macchina virtuale di Azure vengono eseguite nelle macchine virtuali esistenti e questo è utile quando è necessario apportare modifiche alla configurazione o ripristinare la connettività in una macchina virtuale già distribuita. Le estensioni della macchina virtuale possono essere anche unite in bundle con le distribuzioni del modello di Azure Resource Manager. L'uso delle estensioni con i modelli di Resource Manager consente di distribuire e configurare le macchine virtuali di Azure senza l'intervento post-distribuzione.

Per eseguire un'estensione in una macchina virtuale esistente, è possibile usare i metodi seguenti.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Le estensioni macchina virtuale di Azure possono essere eseguite in una VM esistente con il comando [az vm extension set](/cli/azure/vm/extension#az-vm-extension-set). L'esempio seguente esegue l'estensione di script personalizzato su una VM denominata *myVM* in un gruppo di risorse denominato *myResourceGroup*:

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Quando l'estensione viene eseguita correttamente, l'output è simile all'esempio seguente:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Portale di Azure

Le estensioni macchina virtuale possono essere applicate a una macchina virtuale esistente tramite il portale di Azure. Selezionare la macchina virtuale nel portale, scegliere **Estensioni**, quindi selezionare **Aggiungi**. Scegliere l'estensione desiderata dall'elenco di quelle disponibili e quindi seguire le istruzioni della procedura guidata.

L'immagine seguente illustra l'installazione dell'estensione di script personalizzato per Linux dal portale di Azure:

![Installare l'estensione di script personalizzata](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure

Le estensioni della macchina virtuale possono essere aggiunte a un modello di Azure Resource Manager ed eseguite con la distribuzione del modello. Quando si distribuisce un'estensione con un modello, è possibile creare distribuzioni di Azure completamente configurate. Ad esempio, il codice JSON seguente proviene da un modello di Resource Manager che consente di distribuire un set di macchine virtuali con bilanciamento del carico e un database SQL di Azure, quindi installa un'applicazione .NET Core in ogni macchina virtuale. L'estensione della macchina virtuale gestisce l'installazione del software.

Per altre informazioni, vedere il [modello di Resource Manager](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux) completo.

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

Per altre informazioni sulla creazione di modelli di Resource Manager, vedere [Creazione di modelli di Azure Resource Manager](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Proteggere i dati dell'estensione della macchina virtuale

Quando si esegue un'estensione macchina virtuale, potrebbe essere necessario includere informazioni riservate, ad esempio credenziali, nomi degli account di archiviazione e chiavi di accesso degli account di archiviazione. Molte estensioni macchina virtuale includono una configurazione protetta, che consente di crittografare dati e di decrittografarli solo all'interno della macchina virtuale di destinazione. Ogni estensione dispone di uno schema di configurazione protetta specifico, descritto in dettaglio nella documentazione specifica dell'estensione.

L'esempio seguente illustra un'istanza dell'estensione script personalizzata per Linux. Il comando da eseguire include un set di credenziali. In questo esempio il comando da eseguire non è crittografato:

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Lo spostamento della proprietà del **comando da eseguire** nella configurazione **protetta** consente di proteggere la stringa di esecuzione, come mostrato nell'esempio seguente:

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

### <a name="how-do-agents-and-extensions-get-updated"></a>Aggiornamento di agenti ed estensioni

Agenti ed estensioni condividono il meccanismo di aggiornamento. Alcuni aggiornamenti non richiedono regole del firewall aggiuntive.

Quando è disponibile un aggiornamento, viene installato nella macchina virtuale solo se sono presenti una modifica alle estensioni e altre modifiche al modello di macchina virtuale, ad esempio:

- Dischi dati
- Estensioni
- Contenitore della diagnostica di avvio
- Segreti del sistema operativo guest
- Dimensioni macchina virtuale
- Profilo di rete

I server di pubblicazione rendono disponibili gli aggiornamenti nelle varie aree geografiche in momenti diversi, quindi è possibile che le macchine virtuali in aree diverse eseguano versioni diverse.

#### <a name="agent-updates"></a>Aggiornamenti dell'agente

L'agente di macchine virtuali Linux contiene il *codice dell'agente di provisioning* e il *codice per la gestione delle estensioni* in un unico pacchetto e non possono essere separati. È possibile disabilitare l'*agente di provisioning* quando si vuole effettuare il provisioning in Azure usando cloud-init. A questo scopo, vedere [uso di cloud-init](../linux/using-cloud-init.md).

Le versioni supportate degli agenti possono usare gli aggiornamenti automatici. Il solo codice a poter essere aggiornato è il *codice di gestione delle estensioni*, non il codice di provisioning. Il *codice dell'agente di provisioning* è un codice da eseguire una volta.

Il *codice di gestione delle estensioni* è responsabile della comunicazione con l'infrastruttura di Azure e della gestione delle operazioni di estensione macchina virtuale, ad esempio installazioni, stato della creazione di report, aggiornamento e rimozione delle singole estensioni. Gli aggiornamenti contengono correzioni per la sicurezza, correzioni di bug e miglioramenti al *codice di gestione delle estensioni*.

Quando l'agente è installato, viene creato un daemon padre. Questo elemento padre genera quindi un processo figlio che viene usato per gestire le estensioni. Se un aggiornamento è disponibile per l'agente, viene scaricato, l'elemento padre arresta il processo figlio, lo aggiorna, quindi lo riavvia. In caso di problema con l'aggiornamento, il processo padre esegue il rollback alla versione figlio precedente.

Il processo padre non può essere aggiornato automaticamente. L'elemento padre può essere aggiornato solo da un aggiornamento del pacchetto di distribuzione.

Per verificare quale versione è in esecuzione, controllare `waagent` come segue:

```bash
waagent --version
```

L'output è simile all'esempio seguente:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

Nell'output di esempio precedente l'elemento padre o la "versione distribuita del pacchetto" è *WALinuxAgent-2.2.17*

"Goal state agent" è la versione dell'aggiornamento automatico.

È consigliabile impostare sempre l'aggiornamento automatico per l'agente, [AutoUpdate.Enabled=y](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent). Se non viene abilitato, è necessario continuare ad aggiornare manualmente l'agente, senza ottenere correzioni di bug e della sicurezza.

#### <a name="extension-updates"></a>Aggiornamenti delle estensioni

Quando è disponibile l'aggiornamento di un'estensione, l'agente Linux lo scarica e lo installa. Gli aggiornamenti automatici delle estensioni sono *secondari* oppure *aggiornamenti rapidi*. È possibile accettare o rifiutare esplicitamente gli aggiornamenti *secondari* delle estensioni quando si effettua il provisioning dell'estensione. L'esempio seguente mostra come aggiornare automaticamente le versioni secondarie in un modello di Resource Manager con *autoUpgradeMinorVersion": true,'*:

```json
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
```

Per ottenere le più recenti versioni secondarie delle correzioni di bug, si consiglia vivamente di selezionare sempre l'aggiornamento automatico nelle distribuzioni delle estensioni. Non è possibile rifiutare esplicitamente gli aggiornamenti rapidi che contengono correzioni di bug chiave o correzioni per la sicurezza.

### <a name="how-to-identify-extension-updates"></a>Come identificare gli aggiornamenti delle estensioni

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identificare se è impostato autoUpgradeMinorVersion per l'estensione in una macchina virtuale

È possibile determinare dal modello di macchina virtuale se il provisioning dell'estensione è stato eseguito con 'autoUpgradeMinorVersion'. Per controllare, usare [az vm show](/cli/azure/vm#az-vm-show) e specificare il nome del gruppo di risorse e della macchina virtuale come indicato di seguito:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

L'output di esempio seguente mostra che *autoUpgradeMinorVersion* è impostato su *true*:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identificare quando è stato eseguito autoUpgradeMinorVersion

Per vedere quando è stato eseguito un aggiornamento dell'estensione, esaminare i log dell'agente nella macchina virtuale in */var/log/waagent.log*.

Nell'esempio seguente nella VM era installato *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025*. Era disponibile un aggiornamento rapido per *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027*:

```bash
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Expected handler state: enabled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Decide which version to use
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Use version: 2.3.9027
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Current handler state is: NotInstalled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Download extension package
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Unpack extension package
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Download, message=Download succeeded
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Initialize extension directory
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update settings file: 0.settings
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Disable extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Launch command:diagnostic.py -disable
...
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Disable, message=Launch command succeeded: diagnostic.py -disable
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Launch command:diagnostic.py -update
2017/08/14 20:21:57 LinuxAzureDiagnostic started to handle.
```

## <a name="agent-permissions"></a>Autorizzazioni dell'agente

Per svolgere le attività, è necessario che l'agente venga eseguito come *radice*.

## <a name="troubleshoot-vm-extensions"></a>Risoluzione dei problemi relativi alle estensioni della macchina virtuale

Ogni estensione macchina virtuale può richiedere passaggi per la risoluzione dei problemi specifici per l'estensione. Ad esempio, quando si usa l'estensione di script personalizzato, i dettagli sull'esecuzione dello script sono disponibili in locale nella macchina virtuale in cui è stata eseguita l'estensione. Tutti i passaggi per la risoluzione dei problemi specifici dell'estensione sono descritti in dettaglio nella documentazione specifica dell'estensione.

I passaggi seguenti per la risoluzione dei problemi sono validi per tutte le estensioni macchina virtuale.

1. Per controllare il log dell'agente Linux, esaminare l'attività di quando il provisioning dell'estensione è stato eseguito in */var/log/waagent.log*

2. Per informazioni più dettagliate, controllare i log effettivi dell'estensione in */var/log/azure/<extensionName>*

3. Controllare le sezioni della documentazione sulla risoluzione dei problemi specifica per l'estensione relative a codici di errore, problemi noti e così via.

3. Esaminare i log di sistema. Controllare la presenza di altre operazioni che potrebbero aver interferito con l'estensione, ad esempio un'installazione a esecuzione prolungata di un'altra applicazione che ha richiesto l'accesso di gestione pacchetti esclusiva.

### <a name="common-reasons-for-extension-failures"></a>Motivi comuni che causano errori delle estensioni

1. Il limite di tempo per l'esecuzione delle estensioni è di 20 minuti. Fanno eccezione le estensioni Script personalizzato, Chef e DSC che hanno 90 minuti. Se la distribuzione supera questo tempo, viene contrassegnata come timeout. Il problema potrebbe essere causato da macchine virtuali con risorse insufficienti, altre configurazioni di macchine virtuali o attività di avvio che utilizzano grandi quantità di risorse mentre l'estensione sta tentando di effettuare il provisioning.

2. Prerequisiti minimi non soddisfatti. Alcune estensioni hanno dipendenze negli SKU di macchine virtuali, ad esempio le immagini HPC. Le estensioni possono prevedere determinati requisiti di accesso alla rete, ad esempio la comunicazione con servizi pubblici o Archiviazione di Azure. Altri esempi potrebbero essere l'accesso al repository di pacchetti, l'esaurimento dello spazio su disco o restrizioni di sicurezza.

3. Accesso esclusivo alla gestione pacchetti. In alcuni casi è possibile riscontrare un conflitto tra la configurazione della macchina virtuale a esecuzione prolungata e l'installazione dell'estensione, in cui entrambi gli elementi hanno bisogno di accesso esclusivo alla gestione pacchetti.

### <a name="view-extension-status"></a>Visualizzare lo stato dell'estensione

Dopo l'esecuzione di un'estensione macchina virtuale in una macchina virtuale, usare [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view) per restituire lo stato dell'estensione, come segue:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

L'output è simile a quello dell'esempio seguente:

```bash
  {
    "name": "customScript",
    "statuses": [
      {
        "code": "ProvisioningState/failed/0",
        "displayStatus": "Provisioning failed",
        "level": "Error",
        "message": "Enable failed: failed to execute command: command terminated with exit status=127\n[stdout]\n\n[stderr]\n/bin/sh: 1: ech: not found\n",
        "time": null
      }
    ],
    "substatuses": null,
    "type": "Microsoft.Azure.Extensions.customScript",
    "typeHandlerVersion": "2.0.6"
  }
```

Lo stato di esecuzione dell'estensione è disponibile anche nel portale di Azure. Per visualizzare lo stato di un'estensione, selezionare la macchina virtuale, scegliere **Estensioni** e selezionare l'estensione desiderata.

### <a name="rerun-a-vm-extension"></a>Rieseguire un'estensione macchina virtuale

In alcuni casi potrebbe essere necessario ripetere l'esecuzione della macchina virtuale. È possibile eseguire tale operazione rimuovendo l'estensione e quindi eseguendola di nuovo con il metodo scelto. Per rimuovere un'estensione, usare [az vm extension delete](/cli/azure/vm/extension#az-vm-extension-delete), come segue:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

È anche possibile rimuovere un'estensione nel portale di Azure come indicato di seguito:

1. Selezionare una macchina virtuale.
2. Scegliere **Estensioni**.
3. Selezionare l'estensione desiderata.
4. Scegliere **Disinstalla**.

## <a name="common-vm-extension-reference"></a>Riferimento alle estensioni della macchina virtuale comuni

| Nome estensione | DESCRIZIONE | Altre informazioni |
| --- | --- | --- |
| Estensione script personalizzata per Linux |Eseguire gli script in una macchina virtuale di Azure |[Estensione script personalizzata per Linux](custom-script-linux.md) |
| Estensione dell'accesso alle macchine virtuali |Ripristinare l'accesso a una macchina virtuale di Azure |[Estensione dell'accesso alle macchine virtuali](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Estensione di Diagnostica di Azure |Gestisce Diagnostica di Azure. |[Estensione di Diagnostica di Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Estensione dell'accesso alla VM di Azure |Gestire gli utenti e le credenziali |[Estensione dell'accesso alla VM per Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle estensioni macchina virtuale, vedere [Azure virtual machine extensions and features overview](overview.md) (Panoramica delle funzionalità e delle estensioni macchina virtuale di Azure).
