---
title: Eseguire script personalizzati nelle macchine virtuali Linux | Microsoft Docs
description: Automatizzare le attività di configurazione delle macchine virtuali Linux usando l'estensione per script personalizzati v2
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: roiyz
ms.openlocfilehash: 19637a1fe49550d0ed7aea7e3a596f1f77f5984b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60869878"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Usare l'estensione per script personalizzati di Azure versione 2 con macchine virtuali Linux
L'estensione per script personalizzati versione 2 scarica ed esegue script nelle macchine virtuali di Azure. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o altre attività di configurazione o gestione. È possibile scaricare gli script da Archiviazione di Azure, o da un altro percorso Internet accessibile, oppure è possibile fornirli al runtime dell'estensione. 

L'estensione per script personalizzati è integrabile con i modelli di Azure Resource Manager. È anche possibile eseguirla tramite l'interfaccia della riga di comando di Azure, PowerShell o l'API REST di Macchine virtuali di Azure.

Questo articolo descrive come usare l'estensione per script personalizzati dall'interfaccia della riga di comando di Azure ed eseguire l'estensione usando un modello di Azure Resource Manager. Sono inoltre illustrate le procedure di risoluzione dei problemi per i sistemi Linux.


Esistono due estensioni per script personalizzati Linux:
* Versione 1: Microsoft.OSTCExtensions.CustomScriptForLinux
* Versione 2: Microsoft.Azure.Extensions.CustomScript

Modificare le distribuzioni nuove ed esistenti per usare la nuova versione 2. La nuova versione è stata ideata come soluzione sostitutiva ad alte prestazioni. La migrazione è pertanto semplice quanto la modifica del nome e della versione e non è necessario modificare la configurazione dell'estensione.


### <a name="operating-system"></a>Sistema operativo

L'estensione per script personalizzati di Linux verrà eseguita sui sistemi operativi supportati dall'estensione. Per altre informazioni, vedere questo [articolo](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Percorso dello script

È possibile servirsi dell'estensione per usare le credenziali di Archiviazione BLOB di Azure, in modo da accedere alle risorse di archiviazione BLOB di Azure. In alternativa, lo script può trovarsi in qualsiasi posizione, purché la macchina virtuale possa eseguire il routing a tale endpoint, ad esempio GitHub, il file server interno e così via.

### <a name="internet-connectivity"></a>Connettività Internet
Se è necessario scaricare uno script esternamente, ad esempio da GitHub o Archiviazione di Azure, è necessario aprire porte aggiuntive per il firewall o il gruppo di sicurezza di rete. Se lo script si trova ad esempio in Archiviazione di Azure, è possibile consentire l'accesso usando i tag di servizio del gruppo di sicurezza di rete di Azure per [Archiviazione](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Se lo script è in un server locale, può essere necessario aprire porte aggiuntive per il firewall o il gruppo di sicurezza di rete.

### <a name="tips-and-tricks"></a>Suggerimenti e consigli
* La percentuale di errori più elevata per questa estensione è dovuta a errori di sintassi nello script. Verificare che lo script venga eseguito senza errori e inserire nello script altre opzioni di registrazione, per trovare più facilmente i punti che causano errori.
* Scrivere script idempotenti in modo che, se per errore vengono eseguiti più di una volta, non siano apportate modifiche al sistema.
* Verificare che gli script non richiedano l'input dell'utente durante l'esecuzione.
* Il tempo massimo consentito per l'esecuzione dello script è pari a 90 minuti. Tempi superiori comportano un errore di provisioning dell'estensione.
* Non inserire riavvii all'interno dello script, altrimenti si verificheranno problemi con le altre estensioni in fase di installazione e, dopo il riavvio, l'estensione non riprenderà a funzionare. 
* Se si ha uno script che causa un riavvio, installare le applicazioni ed eseguire gli script. È consigliabile pianificare il riavvio del computer usando un processo Cron oppure tramite strumenti come le estensioni DSC, Chef o Puppet.
* L'estensione eseguirà lo script una sola volta. Se si vuole eseguire uno script a ogni avvio, è necessario usare un'[immagine abilitata per cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) e un modulo [Scripts Per Boot](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot). In alternativa, è possibile usare lo script per creare un'unità di servizio Systemd.
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
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
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
      "skipDos2Unix":false,
      "timestamp":123456789          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."]  
    }
  }
}
```

### <a name="property-values"></a>Valori delle proprietà

| NOME | Valore/Esempio | Tipo di dati | 
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute.Extensions | string |
| type | CustomScript | string |
| typeHandlerVersion | 2.0 | int |
| fileUris (es.) | https://github.com/MyProject/Archive/MyPythonScript.py | array |
| commandToExecute (es.) | python MyPythonScript.py \<my-param1> | string |
| script | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | string |
| skipDos2Unix  (esempio) | false | boolean |
| timestamp  (esempio) | 123456789 | valore integer a 32 bit |
| storageAccountName (es.) | examplestorageacct | string |
| storageAccountKey (es.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

### <a name="property-value-details"></a>Dettagli sui valori delle proprietà
* `skipDos2Unix`: (facoltativo, booleano) ignorare la conversione dos2unix dello script o degli URL di file basati su script.
* `timestamp` (facoltativo, valore integer a 32 bit) usare questo campo solo per attivare una nuova esecuzione dello script modificando il valore del campo.  Qualsiasi valore intero è accettabile, purché sia diverso dal valore precedente.
  * `commandToExecute`: (**obbligatorio** se lo script non è impostato, stringa) script del punto di ingresso da eseguire. Usare in alternativa questo campo se il comando contiene segreti, ad esempio password.
* `script`: (**obbligatorio** se commandToExecute non è impostato, stringa), script con codifica Base64 (e facoltativamente compresso con GZip) eseguito da /bin/sh.
* `fileUris`: (facoltativo, matrice di stringhe) URL relativi ai file da scaricare.
* `storageAccountName`: (facoltativo, stringa) nome dell'account di archiviazione. Se si specificano credenziali di archiviazione, tutti i valori di `fileUris` devono essere URL relativi a BLOB di Azure.
* `storageAccountKey`: (facoltativo, stringa) chiave di accesso dell'account di archiviazione


I valori seguenti possono essere configurati in impostazioni pubbliche o protette. L'estensione rifiuterà qualsiasi configurazione in cui i valori riportati di seguito sono specificati sia nelle impostazioni pubbliche che in quelle protette.
* `commandToExecute`
* `script`
* `fileUris`

Le impostazioni pubbliche possono essere utili per il debug, ma è consigliabile usare impostazioni protette.

Le impostazioni pubbliche vengono inviate in testo non crittografato alla macchina virtuale in cui verrà eseguito lo script.  Le impostazioni protette vengono crittografate usando una chiave nota solo alla macchina virtuale e ad Azure. Le impostazioni vengono salvate nella macchina virtuale così come sono state inviate. Se ad esempio erano crittografate, vengono salvate in formato crittografato nella macchina virtuale. Il certificato usato per decrittografare i valori crittografati è archiviato nella macchina virtuale e viene usato per decrittografare le impostazioni (se necessario) in fase di esecuzione.

#### <a name="property-skipdos2unix"></a>Proprietà: skipDos2Unix

Il valore predefinito è False, pertanto la conversione dos2unix **viene** eseguita.

La versione precedente di CustomScript, Microsoft.OSTCExtensions.CustomScriptForLinux, converte automaticamente file DOS in file UNIX trasformando `\r\n` in `\n`. Questa conversione esiste ancora ed è abilitata per impostazione predefinita. La conversione viene applicata a tutti i file scaricati da fileUris o all'impostazione script basata su uno dei criteri seguenti.

* Se l'estensione è `.sh`, `.txt`, `.py` o `.pl`, verrà convertita. L'impostazione script corrisponderà sempre a questi criteri poiché si presuppone che uno script venga eseguito con /bin/sh e venga salvato come script.sh nella macchina virtuale.
* Se il file inizia con `#!`.

La conversione dos2unix può essere ignorata impostando skipDos2Unix su True.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Proprietà: script

CustomScript supporta l'esecuzione di uno script definito dall'utente. Le impostazioni script combinano commandToExecute e fileUris in un'unica impostazione. Anziché dover configurare un file per il download da Archiviazione di Azure o dal gist di GitHub, è possibile codificare semplicemente lo script come impostazione. Lo script può essere usato per sostituire commandToExecute e fileUris.

Lo script **deve** avere codifica Base64.  Lo script può essere **facoltativamente** compresso con GZip. L'impostazione script può essere usata nelle impostazioni pubbliche o protette. La dimensione massima dei dati del parametro script è 256 KB. Se lo script supera questa dimensione non verrà eseguito.

Si consideri ad esempio lo script seguente salvato nel file /script.sh/.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

L'impostazione script CustomScript corretta dovrebbe essere creata usando l'output del comando seguente.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

Lo script può essere facoltativamente compresso con GZip per ridurre ulteriormente le dimensioni (nella maggior parte dei casi). CustomScript rileva automaticamente l'uso della compressione GZip.

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript usa l'agoritmo seguente per eseguire uno script.

 1. Assicurarsi che la lunghezza del valore dello script non superi 256 KB.
 1. Decodificare in Base64 il valore dello script
 1. _Provare_ a comprimere con GZip il valore decodificato in Base64
 1. Scrivere il valore decodificato (e facoltativamente decompresso) su disco (/var/lib/waagent/custom-script/#/script.sh)
 1. Eseguire lo script usando _/bin/sh -c /var/lib/waagent/custom-script/#/script.sh.


## <a name="template-deployment"></a>Distribuzione del modello
Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. Lo schema JSON indicato nella sezione precedente può essere usato in un modello di Azure Resource Manager per eseguire l'estensione di script personalizzata durante la distribuzione di un modello di Azure Resource Manager. Un modello di esempio che include l'estensione script personalizzata è disponibile su [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


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
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <param2>",
      "fileUris": ["https://github.com/MyProject/Archive/hello.sh"
      ]  
    }
  }
}
```

>[!NOTE]
>Questi nomi di proprietà fanno distinzione tra maiuscole e minuscole. Per evitare problemi nella distribuzione, usare i nomi come indicato di seguito.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Quando si usa l'interfaccia della riga di comando di Azure per eseguire l'estensione per script personalizzati, creare uno o più file di configurazione, È necessario avere almeno "commandToExecute".

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Facoltativamente, è possibile specificare le impostazioni nel comando come stringa in formato JSON. Ciò consente di specificare la configurazione durante l'esecuzione e senza un file di configurazione separato.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Esempi di interfaccia della riga di comando di Azure

#### <a name="public-configuration-with-script-file"></a>Configurazione pubblica con file di script

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Comando dell'interfaccia della riga di comando di Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

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
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
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
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>risoluzione dei problemi
Quando viene eseguita l'estensione per script personalizzati, lo script viene creato o scaricato in una directory simile all'esempio seguente. Anche l'output del comando viene salvato in questa directory, nei file `stdout` e `stderr`.

```bash
/var/lib/waagent/custom-script/download/0/
```

Per risolvere questo problema, controllare innanzitutto il log agente Linux, assicurarsi che l'estensione sia stata eseguita e verificare:

```bash
/var/log/waagent.log 
```

Cercare l'esecuzione dell'estensione, che dovrebbe avere l'aspetto seguente:
```text
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```
Alcuni punti da notare:
1. Enable indica il momento in cui il comando inizia l'esecuzione.
2. Download è relativo al download del pacchetto di estensioni CustomScript da Azure e non ai file di script specificati in fileUris.


L'estensione per script di Azure genera un log che è possibile trovare in questo percorso:

```bash
/var/log/azure/custom-script/handler.log
```

È possibile cercare singole esecuzioni, che sarà simile a:
```text
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=start
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=pre-check
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="comparing seqnum" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="seqnum saved" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="reading configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="read configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating json schema"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="json schema valid"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsing configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsed configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating configuration logically"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validated configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="creating output directory" path=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="created output directory"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 files=1
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download start"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download complete" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing protected commandToExecute" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executed command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=enabled
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=end
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
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Passaggi successivi
Per visualizzare il codice, i problemi e le versioni correnti, vedere il [repository custom-script-extension-linux](https://github.com/Azure/custom-script-extension-linux).

