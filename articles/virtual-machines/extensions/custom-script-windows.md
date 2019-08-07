---
title: Estensione script personalizzata di Azure per Windows | Microsoft Docs
description: Automatizzare le attività di configurazione delle macchine virtuali Windows usando l'estensione script personalizzata
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: robreed
ms.openlocfilehash: 58b6531a394db8f9d29dcc0fe9b4b40d1725e70a
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774576"
---
# <a name="custom-script-extension-for-windows"></a>Estensione Script personalizzato per Windows

L'estensione script personalizzata scarica ed esegue script sulle macchine virtuali di Azure. Questa estensione è utile per la configurazione post-distribuzione, l'installazione software o qualsiasi altra attività di configurazione o gestione. Gli script possono essere scaricati dall'archiviazione di Azure o da GitHub, oppure possono essere forniti al portale di Azure durante il runtime dell'estensione. L'estensione script personalizzata si integra con i modelli Azure Resource Manager e può essere eseguita usando l'interfaccia della riga di comando di Azure, PowerShell, portale di Azure o l'API REST di macchine virtuali di Azure.

Questo documento descrive come usare l'estensione di script personalizzata con il modulo Azure PowerShell e i modelli di Azure Resource Manager e inoltre illustra i passaggi per la risoluzione dei problemi nei sistemi Windows.

## <a name="prerequisites"></a>Prerequisiti

> [!NOTE]  
> Non usare l'estensione script personalizzata per eseguire Update-AzVM con la stessa macchina virtuale del relativo parametro, poiché attenderà se stessa.  

### <a name="operating-system"></a>Sistema operativo

L'estensione di script personalizzata per Windows verrà eseguita nell'estensione OSs supportata. per ulteriori informazioni, vedere questa [estensione di Azure sistemi operativi supportati](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Percorso dello script

È possibile configurare l'estensione per usare le credenziali di archiviazione BLOB di Azure per accedere all'archivio BLOB di Azure. Il percorso dello script può essere ovunque, purché la macchina virtuale possa essere indirizzata a tale endpoint, ad esempio GitHub o un file server interno.

### <a name="internet-connectivity"></a>Connettività Internet

Se è necessario scaricare uno script esternamente, ad esempio da GitHub o da archiviazione di Azure, è necessario aprire altre porte del gruppo di sicurezza di rete e del firewall. Ad esempio, se lo script si trova in archiviazione di Azure, è possibile consentire l'accesso usando i tag del servizio NSG di Azure per l' [archiviazione](../../virtual-network/security-overview.md#service-tags).

Se lo script si trova in un server locale, potrebbe essere necessario aprire altre porte del gruppo di sicurezza di rete e del firewall.

### <a name="tips-and-tricks"></a>Suggerimenti e consigli

* Il tasso di errore più elevato per questa estensione è dovuto a errori di sintassi nello script, testare l'esecuzione dello script senza errori e anche inserire altre attività di accesso nello script per semplificare la ricerca della posizione in cui si è verificato l'errore.
* Scrivere script idempotente. In questo modo si garantisce che, in caso di esecuzione accidentale, non provocherà modifiche al sistema.
* Verificare che gli script non richiedano l'input dell'utente durante l'esecuzione.
* Il tempo massimo consentito per l'esecuzione dello script è di 90 minuti. Tempi superiori comportano un errore di provisioning dell'estensione.
* Non inserire riavvii all'interno dello script, altrimenti si verificheranno problemi con le altre estensioni in fase di installazione. Dopo il riavvio, inoltre, l'estensione non riprenderà a funzionare.
* Se si dispone di uno script che determinerà un riavvio, installare le applicazioni ed eseguire gli script, è possibile pianificare il riavvio usando un'attività pianificata di Windows o usare strumenti come DSC, chef o estensioni Puppet.
* L'estensione eseguirà lo script una sola volta. Se si vuole eseguire uno script a ogni avvio, è necessario usare l'estensione per creare un'attività pianificata di Windows.
* Se vuole pianificare il momento di esecuzione di uno script, usare l'estensione per creare un'attività pianificata di Windows.
* Durante l'esecuzione dello script, l'unica indicazione presente nell'interfaccia della riga di comando o nel portale di Azure sarà lo stato dell'estensione "Transizione in corso". Se si vogliono aggiornamenti più frequenti sullo stato di uno script in esecuzione, è necessario creare una soluzione personalizzata.
* L'estensione script personalizzata non supporta in modo nativo i server proxy, ma è possibile usare uno strumento di trasferimento file che supporti i server proxy all'interno dello script, ad esempio *Curl*
* Tenere presenti gli eventuali percorsi di directory non predefiniti usati dagli script o dai comandi e includere la logica necessaria per gestire questa situazione.
* L'estensione dello script personalizzata viene eseguita con l'account LocalSystem

## <a name="extension-schema"></a>Schema dell'estensione

La configurazione dell'estensione script personalizzata specifica informazioni come il percorso dello script e il comando da eseguire. È possibile archiviare queste informazioni in file di configurazione, specificarle sulla riga di comando o definirle in un modello di Azure Resource Manager.

I dati sensibili possono essere archiviati in una configurazione protetta, che viene crittografata e decrittografata solo all'interno della macchina virtuale. La configurazione protetta è utile quando il comando di esecuzione include segreti, ad esempio una password.

Questi elementi devono essere trattati come dati sensibili ed essere specificati nella configurazione protetta dell'estensione. I dati della configurazione protetta dell'estensione macchina virtuale di Azure vengono crittografati, per essere poi decrittografati solo nella macchina virtuale di destinazione.

```json
{
    "apiVersion": "2018-06-01",
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
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```

> [!NOTE]
> È possibile installare una sola versione di un'estensione in una macchina virtuale in un momento specifico. la specifica di uno script personalizzato due volte nello stesso modello di Gestione risorse per la stessa macchina virtuale avrà esito negativo.

### <a name="property-values"></a>Valori delle proprietà

| NOME | Valore/Esempio | Tipo di dati |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute | string |
| type | CustomScriptExtension | string |
| typeHandlerVersion | 1.9 | int |
| fileUris (es.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| timestamp  (esempio) | 123456789 | valore integer a 32 bit |
| commandToExecute (es.) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | string |
| storageAccountName (es.) | examplestorageacct | string |
| storageAccountKey (es.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

>[!NOTE]
>Questi nomi di proprietà fanno distinzione tra maiuscole e minuscole. Per evitare problemi nella distribuzione, usare i nomi come indicato di seguito.

#### <a name="property-value-details"></a>Dettagli sui valori delle proprietà

* `commandToExecute`: (**obbligatorio**, stringa) script del punto di ingresso da eseguire. Usare in alternativa questo campo se il comando contiene segreti, ad esempio password, oppure se gli URI di file sono riservati.
* `fileUris`: (facoltativo, matrice di stringhe) URL relativi ai file da scaricare.
* `timestamp` (facoltativo, valore integer a 32 bit) usare questo campo solo per attivare una nuova esecuzione dello script modificando il valore del campo.  Qualsiasi valore intero è accettabile, purché sia diverso dal valore precedente.
* `storageAccountName`: (facoltativo, stringa) nome dell'account di archiviazione. Se si specificano credenziali di archiviazione, tutti i valori di `fileUris` devono essere URL relativi a BLOB di Azure.
* `storageAccountKey`: (facoltativo, stringa) chiave di accesso dell'account di archiviazione

I valori seguenti possono essere configurati in impostazioni pubbliche o protette. L'estensione rifiuterà qualsiasi configurazione in cui i valori riportati di seguito sono specificati sia nelle impostazioni pubbliche che in quelle protette.

* `commandToExecute`

L'uso delle impostazioni pubbliche può risultare utile per il debug, ma è consigliabile usare le impostazioni protette.

Le impostazioni pubbliche vengono inviate in testo non crittografato alla macchina virtuale in cui verrà eseguito lo script.  Le impostazioni protette vengono crittografate usando una chiave nota solo alla macchina virtuale e ad Azure. Le impostazioni vengono salvate nella macchina virtuale così come sono state inviate, ovvero se le impostazioni sono state crittografate, vengono salvate crittografate nella macchina virtuale. Il certificato usato per decrittografare i valori crittografati è archiviato nella macchina virtuale e viene usato per decrittografare le impostazioni (se necessario) in fase di esecuzione.

## <a name="template-deployment"></a>Distribuzione del modello

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. Lo schema JSON, illustrato in dettaglio nella sezione precedente, può essere usato in un modello di Azure Resource Manager per eseguire l'estensione dello script personalizzato durante la distribuzione. Gli esempi seguenti illustrano come usare l'estensione Script personalizzato:

* [Esercitazione: Distribuire estensioni di macchina virtuale con modelli di Azure Resource Manager](../../azure-resource-manager/resource-manager-tutorial-deploy-vm-extensions.md)
* [Deploy Two Tier Application on Windows and Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows) (Distribuire un'applicazione a due livelli in Windows e nel database SQL di Azure)

## <a name="powershell-deployment"></a>Distribuzione PowerShell

Il comando `Set-AzVMCustomScriptExtension` consente di aggiungere l'estensione di script personalizzata a una macchina virtuale esistente. Per altre informazioni, vedere [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>Esempi aggiuntivi

### <a name="using-multiple-scripts"></a>Uso di più script

In questo esempio sono disponibili tre script usati per compilare il server. Il **commandToExecute** chiama il primo script, quindi sono disponibili le opzioni per la chiamata degli altri. È ad esempio possibile disporre di uno script Master che controlla l'esecuzione, con la gestione corretta degli errori, la registrazione e la gestione dello stato. Gli script vengono scaricati nel computer locale per l'esecuzione di. Ad esempio, `1_Add_Tools.ps1` in è possibile `2_Add_Features.ps1` chiamare aggiungendo `.\2_Add_Features.ps1` allo script e ripetere questo processo per gli altri script definiti in `$settings`.

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.9" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Esecuzione di script da una condivisione locale

In questo esempio, potrebbe essere necessario usare un server SMB locale per il percorso dello script. In questo modo non è necessario fornire altre impostazioni, ad eccezione di **commandToExecute**.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Come eseguire uno script personalizzato più volte tramite l'interfaccia della riga di comando

Si può eseguire più volte l'estensione script personalizzata solo nelle condizioni seguenti:

* Il parametro del **nome** dell'estensione è uguale a quello della distribuzione precedente dell'estensione.
* Aggiornare la configurazione; in caso contrario, il comando non verrà eseguito nuovamente. È possibile, ad esempio, aggiungere al comando una proprietà dinamica, come un timestamp.

In alternativa, è possibile impostare la proprietà [Proprietà forceupdatetag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) su **true**.

### <a name="using-invoke-webrequest"></a>Uso di Invoke-WebRequest

Se si usa [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) nello script, è necessario specificare il parametro `-UseBasicParsing` . in caso contrario, verrà visualizzato l'errore seguente quando si verifica lo stato dettagliato:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```

## <a name="classic-vms"></a>Macchine virtuali classiche

Per distribuire l'estensione di script personalizzati nelle VM classiche, è possibile usare i cmdlet di portale di Azure o i Azure PowerShell classici.

### <a name="azure-portal"></a>Portale di Azure

Passare alla risorsa della macchina virtuale classica. Selezionare **estensioni** in **Impostazioni**.

Fare clic su **+ Aggiungi** e nell'elenco di risorse scegliere **estensione script personalizzata**.

Nella pagina **Installa estensione** selezionare il file di PowerShell locale e compilare eventuali argomenti e fare clic su **OK**.

### <a name="powershell"></a>PowerShell

Usare il cmdlet [set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) per aggiungere l'estensione di script personalizzata a una macchina virtuale esistente.

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati nel portale di Azure e tramite il modulo Azure PowerShell. Per visualizzare lo stato di distribuzione delle estensioni per una determinata macchina virtuale, eseguire il comando seguente:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

L'output dell'estensione viene registrato nei file presenti nella cartella seguente nella macchina virtuale di destinazione.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

I file specificati vengono scaricati nella cartella seguente nella macchina virtuale di destinazione.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

dove `<n>` è un numero intero decimale che può variare nelle diverse esecuzioni dell'estensione.  Il valore `1.*` corrisponde al valore effettivo attuale `typeHandlerVersion` dell'estensione.  Ad esempio, la directory effettiva potrebbe essere `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Quando si esegue il comando `commandToExecute`, nell'estensione viene impostata questa directory (ad esempio `...\Downloads\2`) come directory di lavoro attuale. In questo modo viene abilitato l'uso di percorsi relativi per individuare i file scaricati tramite la proprietà `fileURIs`. Nella tabella seguente sono riportati alcuni esempi.

Poiché il percorso di download assoluto può variare nel tempo, quando è possibile è preferibile optare per percorsi relativi di script/file nella stringa `commandToExecute`. Ad esempio:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Le informazioni sul percorso dopo il primo segmento URI vengono mantenute per i `fileUris` file scaricati tramite l'elenco delle proprietà.  Come illustrato nella tabella riportata di seguito, per i file scaricati viene eseguito il mapping nelle sottodirectory di download per riflettere la struttura dei valori `fileUris`.  

#### <a name="examples-of-downloaded-files"></a>Esempi di file scaricati

| URI in fileUris | Percorso relativo scaricato | Percorso assoluto scaricato <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> i percorsi assoluti della directory cambiano per la durata della macchina virtuale, ma non all'interno di una singola esecuzione dell'estensione CustomScript.

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). È anche possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).
