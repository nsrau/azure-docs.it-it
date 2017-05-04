---
title: Usare i cmdlet PowerShell con Azure RemoteApp | Microsoft Docs
description: Informazioni su come usare i cmdlet Windows PowerShell in Azure RemoteApp.
services: remoteapp
documentationcenter: 
author: guscatalano
manager: mbaldwin
editor: 
ms.assetid: 7d3d5ded-6f73-4de6-a8ac-c1d622e842a2
ms.service: remoteapp
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 8bea98274f94853cb52fe8b51fdaa4f224c649e4
ms.lasthandoff: 04/27/2017


---
# <a name="use-windows-powershell-cmdlets-with-azure-remoteapp"></a>Usare i cmdlet Windows PowerShell con Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp verrà sospeso a partire dal 31 agosto 2017. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

 È possibile usare i cmdlet Azure RemoteApp PowerShell per amministrare e gestire le raccolte. Per iniziare, usare le informazioni seguenti.

## <a name="get-the-cmdlets"></a>Ottenere i cmdlet
- - -
Prima di tutto scaricare i cmdlet di Azure PowerShell [qui](http://go.microsoft.com/?linkid=9811175). I cmdlet di RemoteApp sono inclusi. 

Vedere la [guida dei cmdlet di Azure RemoteApp](/powershell/module/azure?view=azuresmps-3.7.0).

## <a name="configure-azure-cmdlets-to-use-your-subscription"></a>Configurare i cmdlet di Azure per usare la sottoscrizione
- - -
Seguire [questa guida](/powershell/azure/overview) per poter usare i cmdlet con la sottoscrizione di Azure.

Per iniziare rapidamente è possibile eseguire questi passaggi:

1. Scaricare e installare i [cmdlet di Azure PowerShell](http://go.microsoft.com/?linkid=9811175).
2. Avviare Microsoft Azure PowerShell.
3. Eseguire **Add-AzureAccount** per l'autenticazione alla sottoscrizione di Azure. Quando richiesto, immettere il nome utente e la password usati per accedere al portale di Azure.  
4. Eseguire **Get-AzureSubscription** per elencare le sottoscrizioni associate all'account utente. 
5. Eseguire **Select-AzureSubscription** e specificare il nome o l'ID della sottoscrizione da usare nella console di PowerShell.

La console di Azure PowerShell è configurata e pronta per l'uso. Tenere presente che sarà necessario ripetere i passaggi da 2 a 5 ogni volta che si avvia la console di Azure PowerShell.  

## <a name="create-a-cloud-collection"></a>Creare una raccolta nel cloud
- - -
È semplice, basta eseguire il comando seguente:

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

Il comando precedente pubblica automaticamente le applicazioni di Microsoft Office 365 (Excel, OneNote, Outlook, PowerPoint, Visio e Word).

Per completare la creazione della raccolta possono essere necessari anche più di 30 minuti. Questo comando restituisce quindi un ID di traccia che è possibile usare come indicato di seguito:

    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

Dopo aver completato la creazione della raccolta, è possibile aggiungervi utenti con il comando seguente:

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

L'operazione è così completata. L'utente dovrebbe essere in grado di connettersi all'applicazione tramite il client Azure RemoteApp disponibile [qui](https://www.remoteapp.windowsazure.com/).

## <a name="available-cmdlets"></a>Cmdlet disponibili
Sono disponibili molti altri comandi, la cui documentazione sarà disponibile a breve:

Cmdlet della raccolta RemoteApp di base: 

* New-AzureRemoteAppCollection
* Get-AzureRemoteAppCollection
* Set-AzureRemoteAppCollection
* Update-AzureRemoteAppCollection
* Remove-AzureRemoteAppCollection
* Add-AzureRemoteAppUser
* Get-AzureRemoteAppUser
* Remove-AzureRemoteAppUser
* Get-AzureRemoteAppSession
* Disconnect-AzureRemoteAppSession
* Invoke-AzureRemoteAppSessionLogoff
* Send-AzureRemoteAppSessionMessage
* Get-AzureRemoteAppProgram
* Get-AzureRemoteAppStartMenuProgram
* Publish-AzureRemoteAppProgram
* Unpublish-AzureRemoteAppProgram
* Get-AzureRemoteAppCollectionUsageDetails
* Get-AzureRemoteAppCollectionUsageSummary
* Get-AzureRemoteAppPlan

Cmdlet della rete virtuale RemoteApp:

* New-AzureRemoteAppVNet
* Get-AzureRemoteAppVNet
* Set-AzureRemoteAppVNet
* Remove-AzureRemoteAppVNet
* Get-AzureRemoteAppVpnDevice
* Get-- AzureRemoteAppVpnDeviceConfigScript
* Reset-AzureRemoteAppVpnSharedKey

Cmdlet dell'immagine modello RemoteApp:

* New-AzureRemoteAppTemplateImage
* Get-AzureRemoteAppTemplateImage
* Rename-AzureRemoteAppTemplateImage
* Remove-AzureRemoteAppTemplateImage

Altri cmdlet di RemoteApp:

* Get-AzureRemoteAppLocation
* Get-AzureRemoteAppWorkspace
* Set-AzureRemoteAppWorkspace
* Get-AzureRemoteAppOperationResult


