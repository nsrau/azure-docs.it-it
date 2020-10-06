---
title: Avvio rapido - Creare un'istanza di Gestione API di Azure tramite PowerShell | Microsoft Docs
description: Creare una nuova istanza di Gestione API di Azure usando Azure PowerShell.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/14/2020
ms.author: apimpm
ms.openlocfilehash: eb2c42d26a85a07518a018ba5b8817f13d3cd17f
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707075"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-powershell"></a>Avvio rapido: Creare una nuova istanza di Gestione API di Azure usando PowerShell

Gestione API di Azure consente alle organizzazioni di pubblicare API per permettere a sviluppatori esterni, partner e interni di sfruttare tutte le potenzialità dei dati e dei servizi. Gestione API fornisce le competenze fondamentali per assicurare un programma API di successo attraverso il coinvolgimento degli sviluppatori, informazioni aziendali approfondite, strumenti di analisi, sicurezza e protezione. Gestione API di Azure consente di creare e gestire gateway API moderni per servizi back-end esistenti ospitati ovunque. Per altre informazioni, vedere la [panoramica](api-management-key-concepts.md).

Questo argomento di avvio rapido descrive i passaggi per la creazione di una nuova istanza di Gestione API tramite i cmdlet di Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 1.0 o successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

Il comando seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione Stati Uniti occidentali (WestUS):

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location WestUS
```

## <a name="create-an-api-management-service"></a>Creare un servizio Gestione API

Ora che si dispone di un gruppo di risorse, è possibile creare un'istanza del servizio Gestione API. Crearne una usando [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) e specificare un nome di servizio e i dettagli del server di pubblicazione. Il nome del servizio deve essere univoco in Azure.

Nell'esempio seguente viene usato come nome di servizio *myapim*. Aggiornarlo con un valore univoco. Aggiornare anche il nome dell'organizzazione del server di pubblicazione dell'API e l'indirizzo di posta elettronica dell'amministratore per la ricezione delle notifiche.

Per impostazione predefinita, il comando crea l'istanza nel livello Developer, un'opzione economica per valutare Gestione API di Azure. Questo livello non è disponibile per la produzione. Per altre informazioni sulla scalabilità dei livelli di Gestione API, vedere [upgrade and scale](upgrade-and-scale.md) (Aggiornare e ridimensionare).

> [!NOTE]
> L'operazione può richiedere molto tempo. Possono essere necessari da 30 a 40 minuti per creare e attivare un servizio Gestione API in questo livello.

```azurepowershell-interactive
New-AzApiManagement -Name "myapim" -ResourceGroupName "myResourceGroup" `
  -Location "West US" -Organization "Contoso" -AdminEmail "admin@contoso.com" 
```

Dopo che il comando ha completato l'azione, eseguire [Get-AzApiManagement](/powershell/module/az.apimanagement/get-azapimanagement) per visualizzare le proprietà del servizio Gestione API di Azure. Dopo l'attivazione, lo stato del provisioning indica che l'operazione è stata completata e l'istanza del servizio presenta diversi URL associati. Ad esempio:\

```azurepowershell-interactive
Get-AzApiManagement -Name "myapim" -ResourceGroupName "myResourceGroup" 
```

Output di esempio:

```console
PublicIPAddresses                     : {203.0.113.1}
PrivateIPAddresses                    :
Id                                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.ApiManagement/service/myapim
Name                                  : myapim
Location                              : West US
Sku                                   : Developer
Capacity                              : 1
CreatedTimeUtc                        : 9/9/2020 9:07:43 PM
ProvisioningState                     : Succeeded
RuntimeUrl                            : https://myapim.azure-api.net
RuntimeRegionalUrl                    : https://myapi-westus-01.regional.azure-api.net
PortalUrl                             : https://myapim.portal.azure-api.net
DeveloperPortalUrl                    : https://myapim.developer.azure-api.net
ManagementApiUrl                      : https://myapim.management.azure-api.net
ScmUrl                                : https://myapim.scm.azure-api.net
PublisherEmail                        : admin@contoso.com
OrganizationName                      : Contoso
NotificationSenderEmail               : apimgmt-noreply@mail.windowsazure.com
VirtualNetwork                        :
VpnType                               : None
PortalCustomHostnameConfiguration     :
ProxyCustomHostnameConfiguration      : {myapim.azure-api.net}
ManagementCustomHostnameConfiguration :
ScmCustomHostnameConfiguration        :
DeveloperPortalHostnameConfiguration  :
SystemCertificates                    :
Tags                                  : {}
AdditionalRegions                     : {}
SslSetting                            : Microsoft.Azure.Commands.ApiManagement.Models.PsApiManagementSslSetting
Identity                              :
EnableClientCertificate               :
ResourceGroupName                     : myResourceGroup

```

Una volta completata la distribuzione, è possibile iniziare a usare l'istanza del servizio Gestione API. Iniziare con l'esercitazione per l'[importazione e la pubblicazione della prima API](import-and-publish.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Importare e pubblicare la prima API](import-and-publish.md)
