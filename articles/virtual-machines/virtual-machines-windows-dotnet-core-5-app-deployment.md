---
title: Automazione della distribuzione di applicazioni con le estensioni delle macchine virtuali | Microsoft Docs
description: Macchine virtuali di Azure - Esercitazione DotNet Core
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 79c91304-6c1b-4354-a185-fecc129b139d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: nepeters
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 2d60af167b8d7805e6f01264de84fb1351d85f98
ms.lasthandoff: 03/16/2017


---
# <a name="application-deployment-with-azure-resource-manager-templates-for-windows-vms"></a>Distribuzione di applicazioni con i modelli di Azure Resource Manager per macchine virtuali Windows

Dopo che tutti i requisiti dell'infrastruttura di Azure sono stati identificati e convertiti in un modello di distribuzione, è necessario occuparsi della distribuzione effettiva delle applicazioni. Per distribuzione delle applicazioni si intende l'installazione effettiva dei file binari delle applicazioni nelle risorse di Azure. Per l'esempio Music Store, è necessario installare e configurare .Net Core e IIS in ogni macchina virtuale, nonché installare i file binari di Music Store nella macchina virtuale e creare il database di Music Store.

Questo documento descrive in che modo le estensioni delle macchine virtuali possono automatizzare la distribuzione e la configurazione di applicazioni nelle macchine virtuali di Azure. Tutte le dipendenze e le configurazioni univoche sono evidenziate. Per ottenere risultati ottimali, pre-distribuire un'istanza della soluzione alla propria sottoscrizione di Azure ed esercitarsi con il modello di Azure Resource Manager. Il modello completo è disponibile in [Music Store Deployment on Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows) (Distribuzione di Music Store in Windows).

## <a name="configuration-script"></a>Script di configurazione
Le estensioni delle macchine virtuali sono programmi specializzati che vengono eseguiti sulle macchine virtuali per automatizzare le attività di configurazione. Le estensioni sono disponibili per molti scopi specifici, ad esempio un programma antivirus, la configurazione della registrazione e la configurazione di Docker. L'estensione script personalizzata può essere usata per eseguire uno script su una macchina virtuale. Nel caso dell'esempio Music Store, l'estensione script personalizzata viene usata per configurare le macchine virtuali Windows e installare l'applicazione Music Store.

Prima di vedere in che modo le estensioni delle macchine virtuali sono dichiarate in un modello di Azure Resource Manager, esaminare lo script che viene eseguito. Questo script configura la macchina virtuale Windows in modo da ospitare l'applicazione Music Store. Durante l'esecuzione, lo script installa tutto il software necessario, installa l'applicazione Music Store dal controllo del codice sorgente e prepara il database. 

> Questo esempio è fornito a scopo dimostrativo.

```PowerShell
<#
    .SYNOPSIS
        Downloads and configures .Net Core Music Store application sample across IIS and Azure SQL DB.
#>

Param (
    [string]$user,
    [string]$password,
    [string]$sqlserver
)

# Firewall
netsh advfirewall firewall add rule name="http" dir=in action=allow protocol=TCP localport=80

# Folders
New-Item -ItemType Directory c:\temp
New-Item -ItemType Directory c:\music

# Install iis
Install-WindowsFeature web-server -IncludeManagementTools

# Install dot.net core sdk
Invoke-WebRequest http://go.microsoft.com/fwlink/?LinkID=615460 -outfile c:\temp\vc_redistx64.exe
Start-Process c:\temp\vc_redistx64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkID=809122 -outfile c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe
Start-Process c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkId=817246 -outfile c:\temp\DotNetCore.WindowsHosting.exe
Start-Process c:\temp\DotNetCore.WindowsHosting.exe -ArgumentList '/quiet' -Wait

# Download music app
Invoke-WebRequest  https://github.com/Microsoft/dotnet-core-sample-templates/raw/master/dotnet-core-music-windows/music-app/music-store-azure-demo-pub.zip -OutFile c:\temp\musicstore.zip
Expand-Archive C:\temp\musicstore.zip c:\music

# Azure SQL connection sting in environment variable
[Environment]::SetEnvironmentVariable("Data:DefaultConnection:ConnectionString", "Server=$sqlserver;Database=MusicStore;Integrated Security=False;User Id=$user;Password=$password;MultipleActiveResultSets=True;Connect Timeout=30",[EnvironmentVariableTarget]::Machine)

# Pre-create database
$env:Data:DefaultConnection:ConnectionString = "Server=$sqlserver;Database=MusicStore;Integrated Security=False;User Id=$user;Password=$password;MultipleActiveResultSets=True;Connect Timeout=30"
Start-Process 'C:\Program Files\dotnet\dotnet.exe' -ArgumentList 'c:\music\MusicStore.dll'

# Configure iis
Remove-WebSite -Name "Default Web Site"
Set-ItemProperty IIS:\AppPools\DefaultAppPool\ managedRuntimeVersion ""
New-Website -Name "MusicStore" -Port 80 -PhysicalPath C:\music\ -ApplicationPool DefaultAppPool
& iisreset
```

## <a name="vm-script-extension"></a>Estensione script della macchina virtuale
Le estensioni delle macchine virtuali possono essere eseguite su una macchina virtuale in fase di compilazione includendo la risorsa dell'estensione nel modello di Azure Resource Manager. È possibile aggiungere l'estensione usando la procedura guidata Aggiungi nuova risorsa di Visual Studio o inserendo una risorsa JSON valida nel modello. La risorsa dell'estensione script è annidata all'interno della risorsa della macchina virtuale, come illustrato nell'esempio seguente.

Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Estensione script della macchina virtuale](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L339). 

Nel codice JSON seguente è possibile notare che lo script è archiviato in GitHub. Questo script può essere incluso anche nell'archiviazione BLOB di Azure. Inoltre, i modelli di Azure Resource Manager consentono di creare la stringa di esecuzione dello script in modo che i valori dei parametri del modello siano utilizzabili come parametri per l'esecuzione dello script. In questo caso, i dati vengono forniti quando si distribuiscono i modelli e questi valori possono quindi essere usati durante l'esecuzione dello script.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
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
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
  }
}
```

Come indicato in precedenza, è anche possibile archiviare gli script personalizzati nell'archivio BLOB di Azure. Esistono due possibilità di archiviazione per le risorse di script nell'archivio BLOB: rendere pubblico il contenitore o lo script e seguire l'approccio descritto in precedenza, oppure mantenere l'archivio BLOB privato il che implica indicare storageAccountName e storageAccountKey nella definizione della risorsa CustomScriptExtension.

Nell'esempio seguente è stato effettuato un passaggio in più. Sebbene sia possibile indicare il nome e la chiave dell'account di archiviazione come parametro o variabile durante la distribuzione, i modelli di Resource Manager implementano la funzione `listKeys` che può recuperare la chiave dell'account di archiviazione a livello di programmazione e inserirla nel modello in fase di distribuzione.

Nell'esempio di definizione di risorsa CustomScriptExtension riportato di seguito, lo script personalizzato è già stato caricato in un account di archiviazione di Azure denominato `mystorageaccount9999` che esiste in un altro gruppo di risorse denominato `mysa999rgname`. Quando si distribuisce un modello contenente questa risorsa, la funzione `listKeys` recupera a livello di programmazione la chiave dell'account di archiviazione per l'account di archiviazione `mystorageaccount9999` nel gruppo di risorse `mysa999rgname` e la inserisce nel modello.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
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
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://mystorageaccount9999.blob.core.windows.net/container/configure-music-app.ps1"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]",
      "storageAccountName": "mystorageaccount9999",
      "storageAccountKey": "[listKeys(resourceId('mysa999rgname','Microsoft.Storage/storageAccounts', mystorageaccount9999), '2015-06-15').key1]"
    }
  }
}
```

Il principale vantaggio di questo approccio è che non è necessario modificare i parametri del modello o della distribuzione nel caso in cui la chiave dell'account di archiviazione venga modificata.

Per altre informazioni sull'estensione script personalizzata, vedere [Custom script extensions with Resource Manager templates](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)(Estensioni script personalizzate con i modelli di Resource Manager).

## <a name="next-step"></a>Passaggio successivo
<hr>

[Explore More Azure Resource Manager Templates](https://github.com/Azure/azure-quickstart-templates)


