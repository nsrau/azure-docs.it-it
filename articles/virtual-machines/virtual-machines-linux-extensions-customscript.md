<properties
   pageTitle="Script personalizzati nelle macchine virtuali Linux | Microsoft Azure"
   description="Automatizzare le attività di configurazione delle macchine virtuali Linux usando l'estensione script personalizzata"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="nepeters"/>

# Uso dell'estensione script personalizzata di Azure con macchine virtuali Linux

L'estensione script personalizzata scarica ed esegue script sulle macchine virtuali di Azure. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o qualsiasi altra attività di configurazione o gestione. Gli script possono essere scaricati dall'archiviazione di Azure, o da un altro percorso Internet accessibile, oppure possono essere forniti al runtime dell'estensione. L'estensione script personalizzata è integrabile nei modelli di Azure Resource Manager e può essere eseguita anche tramite l'interfaccia della riga di comando di Azure, PowerShell, il portale di Azure o l'API REST di Macchine virtuali di Azure.

Questo documento descrive come usare l'estensione script personalizzata dall'interfaccia della riga di comando di Azure, e da un modello di Azure Resource Manager, e inoltre illustra i passaggi per la risoluzione dei problemi nei sistemi Linux.

## Configurazione dell'estensione

La configurazione dell'estensione script personalizzata specifica informazioni come il percorso dello script e il comando da eseguire. Questa configurazione può essere archiviata nei file di configurazione, specificata sulla riga di comando o definita in un modello di Azure Resource Manager. I dati sensibili possono essere archiviati in una configurazione protetta, che verrà crittografata e decrittografata solo all'interno della macchina virtuale. La configurazione protetta è utile quando il comando di esecuzione include segreti, ad esempio una password.

### Configurazione pubblica

Schema:

- **commandToExecute**: (obbligatorio, stringa) script del punto di ingresso da eseguire.
- **fileUris**: (facoltativo, matrice di stringhe) URL relativi ai file da scaricare.
- **timestamp**: (facoltativo, intero) usare questo campo solo per attivare una nuova esecuzione dello script modificando il valore del campo.

```none
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### Configurazione protetta

Schema:

- **commandToExecute**: (facoltativo, stringa) script del punto di ingresso da eseguire. Usare in alternativa questo campo se il comando contiene segreti, ad esempio password.
- **storageAccountName**: (facoltativo, stringa) nome dell'account di archiviazione. Se si specificano credenziali di archiviazione, tutti i valori di fileUris devono essere URL relativi a BLOB di Azure.
- **storageAccountKey**: (facoltativo, stringa) chiave di accesso dell'account di archiviazione.


```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## Interfaccia della riga di comando di Azure

Quando si usa l'interfaccia della riga di comando di Azure per eseguire l'estensione script personalizzata, creare uno o più file di configurazione contenenti almeno il relativo URI e il comando di esecuzione dello script.

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /scirpt-config.json
```

Facoltativamente, il comando può essere eseguito mediante le opzioni `--public-config` e `--private-config`, in modo che la configurazione possa essere specificata durante l'esecuzione e senza un file di configurazione separato.

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

### Esempi di interfaccia della riga di comando di Azure

**Esempio 1**: configurazione pubblica con file di script.

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
  "commandToExecute": "./hello.sh"
}
```

Comando dell'interfaccia della riga di comando di Azure:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**Esempio 2**: configurazione pubblica senza file di script.

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Comando dell'interfaccia della riga di comando di Azure:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**Esempio 3**: vengono usati un file di configurazione pubblica per specificare l'URI del file di script e un file di configurazione protetta per specificare il comando da eseguire.

File di configurazione pubblica:

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
}
```

File di configurazione protetta:

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Comando dell'interfaccia della riga di comando di Azure:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path ./public.json --private-config-path ./protected.json
```

## Modello di Resource Manager

L'estensione script personalizzata di Azure può essere eseguita durante la fase di distribuzione della macchina virtuale usando un modello di Resource Manager. A tale scopo, aggiungere una risorsa JSON formattata correttamente al modello di distribuzione.

### Esempi di Resource Manager

**Esempio 1**: configurazione pubblica.

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

**Esempio 2**: comando di esecuzione nella configurazione protetta.

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
      "fileUris": [
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

Per un esempio completo, vedere la demo di .NET Core Music Store: [Demo di Music Store](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db).

## Risoluzione dei problemi

Quando viene eseguita l'estensione script personalizzata, lo script viene creato o scaricato in una directory simile all'esempio seguente. Anche l'output del comando viene salvato in questa directory, nei file `stdout` e `stderr`.

```none
/var/lib/azure/custom-script/download/0/
```

L'estensione script di Azure genera un log che è possibile trovare nella directory seguente.

```none
/var/log/azure/customscript/handler.log
```

Lo stato dell'esecuzione dell'estensione script personalizzata può essere recuperato anche con l'interfaccia della riga di comando di Azure.

```none
azure vm extension get <resource-group> <vm-name>
```

L'output ha un aspetto simile al testo seguente:

```none
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## Passaggi successivi

Per informazioni su altre estensioni script delle macchine virtuali, vedere [Azure Script Extension overview for Linux](./virtual-machines-linux-extensions-features.md) (Informazioni generali sulle estensioni script di Azure per Linux).

<!---HONumber=AcomDC_0928_2016-->