---
title: Abilitare il debug remoto con il recapito continuo | Documentazione Microsoft
description: Informazioni su come abilitare il debug remoto quando si usa la distribuzione continua per la pubblicazione in Azure.
services: cloud-services
documentationcenter: .net
author: TomArcher
manager: douge
editor: 
ms.assetid: 7d423639-3b2f-4ca5-ac5a-9ac19a217c29
ms.service: cloud-services
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: bd0768b9d46c12c38ecd530ccef8397d1b595d8d
ms.lasthandoff: 03/07/2017


---
# <a name="enable-remote-debugging-when-using-continuous-delivery-to-publish-to-azure"></a>Abilitare il debug remoto con la distribuzione continua per la pubblicazione in Azure
Per abilitare il debug remoto in Azure, per i servizi cloud o le macchine virtuali, quando si usa la [distribuzione continua](cloud-services-dotnet-continuous-delivery.md) per la pubblicazione in Azure, eseguire la procedura seguente.

## <a name="enabling-remote-debugging-for-cloud-services"></a>Abilitazione del debug remoto per i servizi cloud
1. Nell'agente di compilazione configurare l'ambiente iniziale per Azure come descritto in [Compilazione da riga di comando per Azure](http://msdn.microsoft.com/library/hh535755.aspx).
2. Poiché il pacchetto richiede l'installazione del runtime di debug remoto (msvsmon.exe), installare **Remote Tools per Visual Studio**.

    * [Remote Tools per Visual Studio 2017](https://go.microsoft.com/fwlink/?LinkId=746570)
    * [Remote Tools per Visual Studio 2015](https://go.microsoft.com/fwlink/?LinkId=615470)
    * [Remote Tools per Visual Studio 2013 Update 5](https://www.microsoft.com/download/details.aspx?id=48156)
    
    In alternativa, copiare i binari di debug remoto da un sistema in cui è installato Visual Studio.

3. Creare un certificato come descritto in [Panoramica dei certificati per servizi cloud di Azure](cloud-services-certs-create.md). Mantenere il file con estensione pfx e l'identificazione personale del certificato RDP e caricare il certificato nel servizio cloud di destinazione.
4. Nella riga di comando MSBuild usare le opzioni seguenti per eseguire la compilazione e creare un pacchetto con il debug remoto abilitato. Sostituire i percorsi effettivi ai file di progetto e di sistema per gli elementi racchiusi tra parentesi angolari.
   
        msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"
   
    `VSX64RemoteDebuggerPath` è il percorso della cartella contenente msvsmon.exe in Remote Tools per Visual Studio.
    `RemoteDebuggerConnectorVersion` è la versione di Azure SDK nel servizio cloud. Deve corrispondere anche alla versione installata con Visual Studio.
5. Eseguire la pubblicazione nel servizio cloud di destinazione usando il pacchetto e il file di configurazione (CSCFG) generato nel passaggio precedente.
6. Importare il certificato (file PFX) nel computer in cui è installato Visual Studio con Azure SDK per .NET. Assicurarsi di eseguire l'importazione nell'archivio certificati `CurrentUser\My` , in caso contrario il collegamento al debugger di Visual Studio avrà esito negativo.

## <a name="enabling-remote-debugging-for-virtual-machines"></a>Abilitazione del debug remoto per le macchine virtuali
1. Creare una macchina virtuale di Azure. Per informazioni, vedere l'articolo su come [creare una macchina virtuale che esegue Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) oppure l'articolo su come [Creare e gestire macchine virtuali di Azure in Visual Studio](../virtual-machines/virtual-machines-windows-classic-manage-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
2. Nella [pagina del portale di Azure classico](http://go.microsoft.com/fwlink/p/?LinkID=269851)visualizzare il dashboard della macchina virtuale per individuare **L’IDENTIFICAZIONE PERSONALE CERTIFICATO RDP**della macchina virtuale. Questo valore viene utilizzato per il valore `ServerThumbprint` nella configurazione dell'estensione.
3. Creare un certificato client come descritto in [Panoramica sui certificati per i servizi cloud di Azure](cloud-services-certs-create.md) (conservare il file .pfx e l'identificazione personale del certificato RDP).
4. Installare Azure Powershell (versione 0.7.4 o versione successiva) come descritto in [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).
5. Eseguire lo script seguente per abilitare l'estensione RemoteDebug. Sostituire i percorsi e i dati personali con i dati personali dell'utente, ad esempio nome della sottoscrizione, nome del servizio e identificazione personale.
   
   > [!NOTE]
   > Questo script è configurato per Visual Studio 2015. Se si usa Visual Studio 2013 o Visual Studio 2017, modificare le assegnazioni `$referenceName` e `$extensionName` seguenti in `RemoteDebugVS2013` o `RemoteDebugVS2017`.

    ```powershell   
    Add-AzureAccount

    Select-AzureSubscription "My Microsoft Subscription"

    $vm = Get-AzureVM -ServiceName "mytestvm1" -Name "mytestvm1"

    $endpoints = @(
                    ,@{Name="RDConnVS2013"; PublicPort=30400; PrivatePort=30398}
                    ,@{Name="RDFwdrVS2013"; PublicPort=31400; PrivatePort=31398}
                )

    foreach($endpoint in $endpoints)
    {
        Add-AzureEndpoint -VM $vm -Name $endpoint.Name -Protocol tcp -PublicPort $endpoint.PublicPort -LocalPort $endpoint.PrivatePort
    }

    $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015"
    $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
    $extensionName = "RemoteDebugVS2015"
    $version = "1.*"
    $publicConfiguration = "<PublicConfig><Connector.Enabled>true</Connector.Enabled><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>"

    $vm | Set-AzureVMExtension -ReferenceName $referenceName -Publisher $publisher -ExtensionName $extensionName -Version $version -PublicConfiguration $publicConfiguration

    foreach($extension in $vm.VM.ResourceExtensionReferences)
    {
        if(($extension.ReferenceName -eq $referenceName) `
        -and ($extension.Publisher -eq $publisher) `
        -and ($extension.Name -eq $extensionName) `
        -and ($extension.Version -eq $version))
        {
            $extension.ResourceExtensionParameterValues[0].Key = 'config.txt'
            break
        }
    }

    $vm | Update-AzureVM
    ```

6. Importare il certificato (file PFX) nel computer in cui è installato Visual Studio con Azure SDK per .NET.


