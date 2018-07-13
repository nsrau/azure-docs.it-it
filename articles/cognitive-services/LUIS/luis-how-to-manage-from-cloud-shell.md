---
title: Visualizzare l'utilizzo di LUIS da Azure Cloud Shell | Microsoft Docs
description: Informazioni su come ottenere dati di utilizzo in Azure Cloud Shell per LUIS.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/08/2017
ms.author: v-geberr
ms.openlocfilehash: 8dfe5b2363fbf9b89947956f212d8d4e1ef5d82a
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867036"
---
# <a name="manage-luis-service-from-azure-cloud-shell"></a>Gestire il servizio LUIS da Azure Cloud Shell
Il portale di Azure consente di usare i cmdlet di PowerShell per lavorare con le risorse LUIS. 

Questi cmdlet consentono di [creare](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/new-azurermcognitiveservicesaccount?view=azurermps-6.0.0) una sottoscrizione LUIS, ottenere informazioni sulla sottoscrizione, incluso l'[utilizzo](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0), e [rimuovere](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/remove-azurermcognitiveservicesaccount?view=azurermps-6.0.0) la sottoscrizione. 

## <a name="cloud-shell-storage-account-and-authentication"></a>Account di archiviazione di Azure Cloud Shell e autenticazione
Per usare PowerShell nel portale di Azure [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell), è necessario avere un account di archiviazione di Azure. Se non si ha un [account di archiviazione](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#set-up-a-clouddrive-file-share), verrà chiesto di crearne uno. L'account di archiviazione consente di salvare gli script di PowerShell in Cloud Shell.  

È necessario eseguire l'autenticazione in Azure in Cloud Shell per accedere alle risorse. 

Quando si ha un account di archiviazione e si è autenticati, è possibile eseguire i cmdlet di PowerShell.

## <a name="open-cloud-shell"></a>Aprire Cloud Shell
Quando si usa Cloud Shell del portale di Azure, si usa sempre la versione più recente di PowerShell. 

Usare il pulsante **Avvia Cloud Shell** per aprire Cloud Shell o aprire un browser con [https://shell.azure.com](https://shell.azure.com). 

<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>

## <a name="luis-endpoint-usage-information"></a>Informazioni sull'utilizzo dell'endpoint LUIS

Il cmdlet di PowerShell 6.x `Get-AzureRmCognitiveServicesAccountUsage` fornisce informazioni sull'utilizzo di Servizi cognitivi Microsoft incluso LUIS. [Get-AzureRmCognitiveServicesAccountUsage](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0) richiede il gruppo di risorse e il nome di risorsa del servizio. 

La sintassi del comando è:

```
Get-AzureRmCognitiveServicesAccountUsage -ResourceGroupName my-resource-group -Name my-luis-service-name
```

Nell'esempio seguente il nome del gruppo di risorse è `luis-westus-rg` e il nome di sottoscrizione del servizio LUIS è `luis-westus-1`. Entrambi questi nomi sono scelti quando si crea il servizio LUIS. 

Il cmdlet restituisce le informazioni sull'utilizzo di 16 dei 10.000 accessi all'endpoint relativi a un periodo di 30 giorni con il 7 giugno come data di fine periodo:

```
CurrentValue  : 16
Name          : LUIS.Calls
Limit         : 10000
Status        : Included
Unit          : Count
QuotaPeriod   : 30.00:00:00
NextResetTime : 2018-06-07T18:28:52Z
```

Salvare il comando come file di PowerShell, *.ps1, nell'account di archiviazione di Azure associato con Cloud Shell ed eseguirlo in qualsiasi momento. 

![Eseguire uno script dalla risorsa di archiviazione](./media/luis-how-to-manage-from-powershell/run-script-from-storage.png)

Quando lo script viene salvato nell'unità del cloud, è possibile eseguire lo script di PowerShell da Cloud Shell dell'app telefonica di Azure. 

![Eseguire uno script dalla risorsa di archiviazione nell'app telefonica](./media/luis-how-to-manage-from-powershell/phone-app.png)