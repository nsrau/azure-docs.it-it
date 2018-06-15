---
title: Estensione script personalizzata di Azure per Windows | Microsoft Docs
description: Automatizzare le attività di configurazione delle macchine virtuali Windows usando l'estensione script personalizzata
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/24/2018
ms.author: danis
ms.openlocfilehash: 80f9ecd40c5b9504a6554b95bf374046d8253933
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809778"
---
# <a name="custom-script-extension-for-windows"></a>Estensione Script personalizzato per Windows

L'estensione script personalizzata scarica ed esegue script sulle macchine virtuali di Azure. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o qualsiasi altra attività di configurazione o gestione. Gli script possono essere scaricati dall'archiviazione di Azure o da GitHub, oppure possono essere forniti al portale di Azure durante il runtime dell'estensione. L'estensione script personalizzata è integrabile nei modelli di Azure Resource Manager e può essere eseguita anche tramite l'interfaccia della riga di comando di Azure, PowerShell, il portale di Azure o l'API REST di Macchine virtuali di Azure.

Questo documento descrive come usare l'estensione di script personalizzata con il modulo Azure PowerShell e i modelli di Azure Resource Manager e inoltre illustra i passaggi per la risoluzione dei problemi nei sistemi Windows.

## <a name="prerequisites"></a>prerequisiti

> [!NOTE]  
> Non usare l'estensione script personalizzata per eseguire Update-AzureRmVM con la stessa macchina virtuale del relativo parametro, poiché attenderà se stessa.  
>   
> 

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
* Se si ha uno script che causerà un riavvio, installare le applicazioni e poi eseguire gli script e così via. È consigliabile pianificare il riavvio del computer usando un'attività pianificata di Windows oppure strumenti come le estensioni DSC, Chef o Puppet.
* L'estensione eseguirà lo script una sola volta. Se si vuole eseguire uno script a ogni avvio, è necessario usare l'estensione per creare un'attività pianificata di Windows.
* Se vuole pianificare il momento di esecuzione di uno script, usare l'estensione per creare un'attività pianificata di Windows. 
* Quando lo script è in esecuzione, l'unica indicazione presente nell'interfaccia della riga di comando o nel portale di Azure sarà lo stato dell'estensione "Transizione in corso". Se si vogliono aggiornamenti più frequenti sullo stato di uno script in esecuzione, sarà necessario creare una soluzione personalizzata.
* L'estensione script personalizzata non supporta in modo nativo i server proxy, ma è possibile usare uno strumento di trasferimento file che supporti i server proxy all'interno dello script, ad esempio *Curl* 
* Tenere presenti gli eventuali percorsi di directory non predefiniti usati dagli script o dai comandi e includere la logica necessaria per gestirli.


## <a name="extension-schema"></a>Schema dell'estensione

La configurazione dell'estensione script personalizzata specifica informazioni come il percorso dello script e il comando da eseguire. È possibile archiviare queste informazioni in file di configurazione, specificarle sulla riga di comando o definirle in un modello di Azure Resource Manager. 

I dati sensibili possono essere archiviati in una configurazione protetta, che viene crittografata e decrittografata solo all'interno della macchina virtuale. La configurazione protetta è utile quando il comando di esecuzione include segreti, ad esempio una password.

Questi elementi devono essere trattati come dati sensibili ed essere specificati nella configurazione protetta dell'estensione. I dati della configurazione protetta dell'estensione macchina virtuale di Azure vengono crittografati, per essere poi decrittografati solo nella macchina virtuale di destinazione.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.9",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ]
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```
**Nota:** è possibile installare in una macchina virtuale una sola versione di un'estensione per volta. Se si specifica uno script personalizzato due volte nello stesso modello di Resource Manager per la stessa macchina virtuale, l'operazione avrà esito negativo. 

### <a name="property-values"></a>Valori delle proprietà

| NOME | Valore/Esempio | Tipo di dati |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute | stringa |
| type | CustomScriptExtension | stringa |
| typeHandlerVersion | 1.9 | int |
| fileUris (es.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| commandToExecute (es.) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | stringa |
| storageAccountName (es.) | examplestorageacct | stringa |
| storageAccountKey (es.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | stringa |

>[!NOTE]
>Questi nomi di proprietà fanno distinzione tra maiuscole e minuscole. Per evitare problemi nella distribuzione, usare i nomi come indicato di seguito.

#### <a name="property-value-details"></a>Dettagli sui valori delle proprietà
 * `commandToExecute`: (**obbligatorio**, stringa) script del punto di ingresso da eseguire. Usare in alternativa questo campo se il comando contiene segreti, ad esempio password, oppure se gli URI di file sono riservati.
* `fileUris`: (facoltativo, matrice di stringhe) URL relativi ai file da scaricare.
* `storageAccountName`: (facoltativo, stringa) nome dell'account di archiviazione. Se si specificano credenziali di archiviazione, tutti i valori di `fileUris` devono essere URL relativi a BLOB di Azure.
* `storageAccountKey`: (facoltativo, stringa) chiave di accesso dell'account di archiviazione

I valori seguenti possono essere configurati in impostazioni pubbliche o protette. L'estensione rifiuterà qualsiasi configurazione in cui i valori riportati di seguito sono specificati sia nelle impostazioni pubbliche che in quelle protette.
* `commandToExecute`

Le impostazioni pubbliche possono essere utili per il debug, ma è consigliabile usare impostazioni protette.

Le impostazioni pubbliche vengono inviate in testo non crittografato alla macchina virtuale in cui verrà eseguito lo script.  Le impostazioni protette vengono crittografate usando una chiave nota solo alla macchina virtuale e ad Azure. Le impostazioni vengono salvate nella macchina virtuale così come sono state inviate. Se ad esempio erano crittografate, vengono salvate in formato crittografato nella macchina virtuale. Il certificato usato per decrittografare i valori crittografati è archiviato nella macchina virtuale e viene usato per decrittografare le impostazioni (se necessario) in fase di esecuzione.

## <a name="template-deployment"></a>Distribuzione del modello

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. Lo schema JSON indicato nella sezione precedente può essere usato in un modello di Azure Resource Manager per eseguire l'estensione di script personalizzata durante la distribuzione di un modello di Azure Resource Manager. Un modello di esempio che include l'estensione script personalizzata è disponibile su [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>Distribuzione PowerShell

Il comando `Set-AzureRmVMCustomScriptExtension` consente di aggiungere l'estensione di script personalizzata a una macchina virtuale esistente. Per ulteriori informazioni, vedere [Set-AzureRmVMCustomScriptExtension](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension).
```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```
## <a name="further-examples"></a>Altri esempi

### <a name="using-multiple-script"></a>Uso di più script
In questo esempio sono presenti tre script usati per la creazione di un server. 'commandToExecute' chiama il primo script, poi sono disponibili diverse opzioni rispetto al modo in cui chiamare gli altri, ad esempio si può avere uno script master che controlla l'esecuzione, con la gestione degli errori, la registrazione e la gestione dello stato appropriate.

```powershell

$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$Settings = @{"fileUris" = $fileUri};

$storageaccname = "xxxxxxx"
$storagekey = "1234ABCD"
$ProtectedSettings = @{"storageAccountName" = $storageaccname; "storageAccountKey" = $storagekey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzureRmVMExtension -ResourceGroupName myRG `
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "buildserver1" ` 
    -Publisher "Microsoft.Compute" ` 
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" ` 
    -Settings $Settings ` 
    -ProtectedSettings $ProtectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Esecuzione di script da una condivisione locale
In questo esempio è possibile usare un server SMB locale come percorso dello script. Si noti che non è necessario passare altre impostazioni, eccetto *commandToExecute*.

```powershell
$ProtectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};
 
Set-AzureRmVMExtension -ResourceGroupName myRG 
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "serverUpdate" 
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $ProtectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Come eseguire uno script personalizzato più volte tramite l'interfaccia della riga di comando
Si può eseguire più volte l'estensione script personalizzata solo nelle condizioni seguenti:
1. Il parametro 'Name' dell'estensione deve essere lo stesso usato nella distribuzione precedente dell'estensione.
2. È necessario aggiornare la configurazione o il comando non verrà eseguito nuovamente. Ad esempio, si può aggiungere al comando una proprietà dinamica, come un timestamp. 

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati nel portale di Azure e tramite il modulo Azure PowerShell. Per visualizzare lo stato di distribuzione delle estensioni per una determinata macchina virtuale, eseguire il comando seguente:

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

L'output dell'esecuzione dell'estensione viene registrato nei file presenti nella directory seguente nella macchina virtuale di destinazione.
```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

I file specificati vengono scaricati nella directory seguente nella macchina virtuale di destinazione.
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
dove `<n>` è un numero intero decimale che può variare nelle diverse esecuzioni dell'estensione.  Il valore `1.*` corrisponde al valore effettivo attuale `typeHandlerVersion` dell'estensione.  Ad esempio, la directory effettiva potrebbe essere `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Quando si esegue il comando `commandToExecute`, nell'estensione viene impostata questa directory (ad esempio `...\Downloads\2`) come directory di lavoro attuale. In questo modo viene abilitato l'uso di percorsi relativi per individuare i file scaricati tramite la proprietà `fileURIs`. Nella tabella seguente sono riportati alcuni esempi.

Poiché il percorso di download assoluto può variare nel tempo, quando è possibile è preferibile optare per percorsi relativi di script/file nella stringa `commandToExecute`. Ad esempio: 
```json
    "commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Le informazioni sul percorso dopo il primo segmento URI vengono mantenute per i file scaricati tramite l'elenco delle proprietà `fileUris`.  Come illustrato nella tabella riportata di seguito, per i file scaricati viene eseguito il mapping nelle sottodirectory di download per riflettere la struttura dei valori `fileUris`.  

#### <a name="examples-of-downloaded-files"></a>Esempi di file scaricati

| URI in fileUris | Percorso relativo scaricato | Percorso assoluto scaricato * |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* Come in precedenza, i percorsi assoluti delle directory cambiano nella durata della macchina virtuale ma non all'interno di una singola esecuzione dell'estensione CustomScript.

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).
