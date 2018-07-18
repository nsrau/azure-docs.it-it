---
title: Eseguire script personalizzati nelle macchine virtuali Linux | Microsoft Docs
description: Automatizzare le attività di configurazione delle macchine virtuali Linux usando l'estensione per script personalizzati v1
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: danis
ms.openlocfilehash: 526021ca238be7bc934e639c34d3e49879279a6a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127653"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Usare l'estensione per script personalizzati di Azure versione 1 con macchine virtuali Linux
L'estensione per script personalizzati versione 1 scarica ed esegue script nelle macchine virtuali di Azure. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o altre attività di configurazione o gestione. È possibile scaricare gli script da Archiviazione di Azure, o da un altro percorso Internet accessibile, oppure è possibile fornirli al runtime dell'estensione. 

L'estensione per script personalizzati è integrabile con i modelli di Azure Resource Manager. È anche possibile eseguirla tramite l'interfaccia della riga di comando di Azure, PowerShell, il portale di Azure o l'API REST di Macchine virtuali di Azure.

Questo articolo descrive come usare l'estensione per script personalizzati dall'interfaccia della riga di comando di Azure ed eseguire l'estensione usando un modello di Azure Resource Manager. Sono inoltre illustrate le procedure di risoluzione dei problemi per i sistemi Linux.


Esistono due estensioni per script personalizzati Linux:
* Versione 1: Microsoft.OSTCExtensions.CustomScriptForLinux
* Versione 2: Microsoft.Azure.Extensions.CustomScript

Modificare le distribuzioni nuove ed esistenti per usare la nuova versione ([Microsoft.Azure.Extensions.CustomScript](https://docs.microsoft.com/en-us/azure/virtual-machines/extensions/custom-script-linux)). La nuova versione è stata ideata come soluzione sostitutiva ad alte prestazioni. La migrazione è pertanto semplice quanto la modifica del nome e della versione e non è necessario modificare la configurazione dell'estensione.

 

### <a name="operating-system"></a>Sistema operativo
Distribuzioni Linux supportate:

- CentOS 6.5 e successive
- Debian 8 e successive
    - Debian 8.7 non viene fornito con Python2 nelle immagini più recenti, perciò CustomScriptForLinux non funziona in modo corretto.
- FreeBSD
- OpenSUSE 13.1 e successive
- Oracle Linux 6.4 e successive
- SUSE Linux Enterprise Server 11 SP3 e successive
- Ubuntu 12.04 e successive

### <a name="script-location"></a>Percorso dello script

È possibile servirsi dell'estensione per usare le credenziali di Archiviazione BLOB di Azure, in modo da accedere alle risorse di archiviazione BLOB di Azure. In alternativa, lo script può trovarsi in qualsiasi posizione, purché la macchina virtuale possa eseguire il routing a tale endpoint, ad esempio GitHub, il file server interno e così via.

### <a name="internet-connectivity"></a>Connettività Internet
Se è necessario scaricare uno script esternamente, ad esempio da GitHub o Archiviazione di Azure, è necessario aprire porte aggiuntive per il firewall o il gruppo di sicurezza di rete. Se lo script si trova ad esempio in Archiviazione di Azure, è possibile consentire l'accesso usando i tag di servizio del gruppo di sicurezza di rete di Azure per [Archiviazione](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags).

Se lo script è in un server locale, può essere necessario aprire porte aggiuntive per il firewall o il gruppo di sicurezza di rete.

### <a name="tips-and-tricks"></a>Suggerimenti e consigli
* La percentuale di errori più elevata per questa estensione è dovuta a errori di sintassi nello script. Verificare che lo script venga eseguito senza errori e inserire nello script altre opzioni di registrazione, per trovare più facilmente i punti che causano errori.
* Scrivere script idempotenti in modo che, se per errore vengono eseguiti più di una volta, non siano apportate modifiche al sistema.
* Verificare che gli script non richiedano l'input dell'utente durante l'esecuzione.
* Il tempo massimo consentito per l'esecuzione dello script è pari a 90 minuti. Tempi superiori comportano un errore di provisioning dell'estensione.
* Non inserire riavvii all'interno dello script, altrimenti si verificheranno problemi con le altre estensioni in fase di installazione e, dopo il riavvio, l'estensione non riprenderà a funzionare. 
* Se si ha uno script che causa un riavvio, installare le applicazioni ed eseguire gli script. È consigliabile pianificare il riavvio del computer usando un processo Cron oppure tramite strumenti come le estensioni DSC, Chef o Puppet.
* L'estensione eseguirà lo script una sola volta. Se si vuole eseguire uno script a ogni avvio, è necessario usare un'[immagine abilitata per cloud-init](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/using-cloud-init) e un modulo [Scripts Per Boot](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot). In alternativa, è possibile usare lo script per creare un'unità di servizio Systemd.
* Se si vuole pianificare il momento di esecuzione di uno script, usare l'estensione per creare un processo Cron. 
* Durante l'esecuzione dello script, l'unica indicazione presente nell'interfaccia della riga di comando o nel portale di Azure sarà lo stato dell'estensione "Transizione in corso". Se si vogliono aggiornamenti più frequenti sullo stato di uno script in esecuzione, è necessario creare una soluzione personalizzata.
* L'estensione per script personalizzati non supporta in modo nativo i server proxy, ma è possibile usare uno strumento di trasferimento file che supporti i server proxy all'interno dello script, ad esempio *Curl*. 
* Tenere presenti gli eventuali percorsi di directory non predefiniti usati dagli script o dai comandi e includere la logica necessaria per gestirli.



## <a name="extension-schema"></a>Schema dell'estensione

La configurazione dell'estensione script personalizzata specifica informazioni come il percorso dello script e il comando da eseguire. È possibile archiviare queste informazioni in file di configurazione, specificarle sulla riga di comando o definirle in un modello di Azure Resource Manager. 

I dati sensibili possono essere archiviati in una configurazione protetta, che viene crittografata e decrittografata solo all'interno della macchina virtuale. La configurazione protetta è utile quando il comando di esecuzione include segreti, ad esempio una password.

Questi elementi devono essere trattati come dati sensibili ed essere specificati nella configurazione protetta dell'estensione. I dati della configurazione protetta dell'estensione macchina virtuale di Azure vengono crittografati, per essere poi decrittografati solo nella macchina virtuale di destinazione.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "<url>"
      ],
      "enableInternalDNSCheck": true
    },
    "protectedSettings": {
      "storageAccountName": "<storage-account-name>",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "<command>"
    }
  }
}
```

### <a name="property-values"></a>Valori delle proprietà

| NOME | Valore/Esempio | Tipo di dati | 
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.OSTCExtensions | stringa |
| type | CustomScriptForLinux | stringa |
| typeHandlerVersion | 1,5 | int |
| fileUris (es.) | https://github.com/MyProject/Archive/MyPythonScript.py | array |
| commandToExecute (es.) | python MyPythonScript.py <my-param1> | stringa |
| enableInternalDNSCheck | true | boolean |
| storageAccountName (es.) | examplestorageacct | stringa |
| storageAccountKey (es.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | stringa |

### <a name="property-value-details"></a>Dettagli sui valori delle proprietà
* `fileUris`: (facoltativo, matrice di stringhe) elenco di URI degli script
* `enableInternalDNSCheck`: (facoltativo, bool) il valore predefinito è True. Impostare su False per disabilitare il controllo DNS.
* `commandToExecute`: (facoltativo, stringa) script del punto di ingresso da eseguire
* `storageAccountName`: (facoltativo, stringa) nome dell'account di archiviazione
* `storageAccountKey`: (facoltativo, stringa) chiave di accesso dell'account di archiviazione

I valori seguenti possono essere configurati in impostazioni pubbliche o protette, ma non devono essere specificati contemporaneamente nelle impostazioni pubbliche e in quelle protette.
* `commandToExecute`

Le impostazioni pubbliche possono essere utili per il debug, ma è consigliabile usare impostazioni protette.

Le impostazioni pubbliche vengono inviate in testo non crittografato alla macchina virtuale in cui verrà eseguito lo script.  Le impostazioni protette vengono crittografate usando una chiave nota solo alla macchina virtuale e ad Azure. Le impostazioni vengono salvate nella macchina virtuale così come sono state inviate. Se ad esempio erano crittografate, vengono salvate in formato crittografato nella macchina virtuale. Il certificato usato per decrittografare i valori crittografati è archiviato nella macchina virtuale e viene usato per decrittografare le impostazioni (se necessario) in fase di esecuzione.


## <a name="template-deployment"></a>Distribuzione del modello
Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. Lo schema JSON indicato nella sezione precedente può essere usato in un modello di Azure Resource Manager per eseguire l'estensione di script personalizzata durante la distribuzione di un modello di Azure Resource Manager. 


```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": ["http://MyAccount.blob.core.windows.net/vhds/MyShellScript.sh"]
    },
    "protectedSettings": {
      "storageAccountName": "MyAccount",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "sh MyShellScript.sh"
    }
  }
}
```

>[!NOTE]
>Questi nomi di proprietà fanno distinzione tra maiuscole e minuscole. Per evitare problemi nella distribuzione, usare i nomi come indicato di seguito.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Quando si usa l'interfaccia della riga di comando di Azure per eseguire l'estensione per script personalizzati, creare uno o più file di configurazione, È necessario avere almeno "commandToExecute".

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Facoltativamente, è possibile specificare le impostazioni nel comando come stringa in formato JSON. Ciò consente di specificare la configurazione durante l'esecuzione e senza un file di configurazione separato.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Esempi di interfaccia della riga di comando di Azure

#### <a name="public-configuration-with-no-script-file"></a>Configurazione pubblica senza file di script

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Comando dell'interfaccia della riga di comando di Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>File di configurazione pubblica e protetta

Si usa un file di configurazione pubblica per specificare l'URI del file di script e un file di configurazione protetta per specificare il comando da eseguire.

File di configurazione pubblica:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

File di configurazione protetta:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Comando dell'interfaccia della riga di comando di Azure:

```azurecli
az vm extension set 
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>risoluzione dei problemi
Quando viene eseguita l'estensione per script personalizzati, lo script viene creato o scaricato in una directory simile all'esempio seguente. Anche l'output del comando viene salvato in questa directory, nei file `stdout` e `stderr`. 

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Per risolvere questo problema, controllare innanzitutto il log agente Linux, assicurarsi che l'estensione sia stata eseguita e verificare:

```bash
/var/log/waagent.log 
```

Cercare l'esecuzione dell'estensione, che dovrebbe avere l'aspetto seguente:
```text
2018/04/26 15:29:44.835067 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Target handler state: enabled
2018/04/26 15:29:44.867625 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] [Enable] current handler state is: notinstalled
2018/04/26 15:29:44.959605 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Download, message=Download succeeded, duration=59
2018/04/26 15:29:44.993269 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Initialize extension directory
2018/04/26 15:29:45.022972 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Update settings file: 0.settings
2018/04/26 15:29:45.051763 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Install extension [customscript.py -install]
2018/04/26 15:29:45 CustomScriptForLinux started to handle.
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] cwd is /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Change log file to /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log
2018/04/26 15:29:46.088212 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Install, message=Launch command succeeded: customscript.py -install, duration=1005
2018/04/26 15:29:46.133367 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable extension [customscript.py -enable]
2018/04/26 15:29:46 CustomScriptForLinux started to handle.
..
2018/04/26 15:29:47.178163 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Enable, message=Launch command succeeded: customscript.py -enable, duration=1012
```
Alcuni punti da notare:
1. Enable indica il momento in cui il comando inizia l'esecuzione.
2. Download è relativo al download del pacchetto di estensioni CustomScript da Azure e non ai file di script specificati in fileUris.
3. È inoltre possibile visualizzare il file di log in cui avviene la scrittura "/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log".

Il passaggio successivo consiste nel controllare il file di log, nel formato:
```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Cercare l'esecuzione individuale, che dovrebbe avere l'aspetto seguente:
```text
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable,transitioning,0,Launching the script...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] sequence number is 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] setting file path is/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/config/0.settings
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] JSON config: {"runtimeSettings": [{"handlerSettings": {"protectedSettings": "MIIB0AYJKoZIhvcNAQcDoIIBwTCCAb0CAQAxggF+hnEXRtFKTTuKiFC8gTfHKupUSs7qI0zFYRya", "publicSettings": {"fileUris": ["https://dannytesting.blob.core.windows.net/demo/myBash.sh"]}, "protectedSettingsCertThumbprint": "4385AB21617C2452FF6998C0A37F71A0A01C8368"}}]}
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Config decoded correctly.
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Will try to download files, number of retries = 10, wait SECONDS between retrievals = 20s
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Downloading,transitioning,0,Downloading files...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] No azure storage account and key specified in protected settings. Downloading scripts from external links...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Converting /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh from DOS to Unix formats: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Removing BOM of /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Succeeded to download files, retry count = 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Internal DNS is ready, retry count = 0
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47 
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47 
2018/04/26 15:29:47 
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Daemon,success,0,Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47 
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47 
2018/04/26 15:29:47 
```
È possibile visualizzare:
* Il comando Enable che inizia nel log
* Le impostazioni passate all'estensione
* L'estensione che esegue il download del file e il relativo risultato.
* Il comando in esecuzione e il relativo risultato.

Lo stato dell'esecuzione dell'estensione per script personalizzati può essere recuperato anche tramite l'interfaccia della riga di comando di Azure:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

L'output ha un aspetto simile al testo seguente:

```azurecli
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>Passaggi successivi
Per visualizzare il codice, i problemi e le versioni correnti, vedere il [repository delle estensioni CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).

