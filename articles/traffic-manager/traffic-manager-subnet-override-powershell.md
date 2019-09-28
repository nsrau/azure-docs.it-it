---
title: Override della subnet di gestione traffico di Azure con Azure PowerShell | Microsoft Docs
description: Questo articolo consente di comprendere come viene usato l'override della subnet di gestione traffico per eseguire l'override del metodo di routing di un profilo di gestione traffico per indirizzare il traffico a un endpoint in base all'indirizzo IP dell'utente finale tramite l'intervallo IP predefinito ai mapping degli endpoint tramite Azure PowerShell.
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: 93362c0e1b359583e30886172f8ccb155c5ffaf4
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348884"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Override della subnet di gestione traffico con Azure PowerShell

L'override della subnet di gestione traffico consente di modificare il metodo di routing di un profilo.  L'aggiunta di una sostituzione consente di indirizzare il traffico in base all'indirizzo IP dell'utente finale con un intervallo di indirizzi IP predefinito al mapping degli endpoint. 

## <a name="how-subnet-override-works"></a>Funzionamento dell'override della subnet

Quando si aggiungono sostituzioni di subnet a un profilo di gestione traffico, gestione traffico verificherà prima di tutto se è presente una subnet sostitutiva per l'indirizzo IP dell'utente finale. Se ne viene trovato uno, la query DNS dell'utente viene indirizzata all'endpoint corrispondente.  Se non viene trovato alcun mapping, gestione traffico esegue il fallback al metodo di routing originale del profilo. 

Gli intervalli di indirizzi IP possono essere specificati come intervalli CIDR (ad esempio, 1.2.3.0/24) o come intervalli di indirizzi (ad esempio, 1.2.3.4-5.6.7.8). Gli intervalli IP associati a ogni endpoint devono essere univoci per tale endpoint. Qualsiasi sovrapposizione degli intervalli IP tra endpoint diversi causerà il rifiuto del profilo da parte di gestione traffico.

Sono disponibili due tipi di profili di routing che supportano le sostituzioni delle subnet:

* **Geografico** : se Gestione traffico trova una sostituzione della subnet per l'indirizzo IP della query DNS, la query verrà indirizzata all'endpoint indipendentemente dall'integrità dell'endpoint.
* **Prestazioni** : se Gestione traffico trova una sostituzione della subnet per l'indirizzo IP della query DNS, eseguirà il routing del traffico all'endpoint solo se è integro.  Gestione traffico eseguirà il fallback all'euristica di routing delle prestazioni se l'endpoint di override della subnet non è integro.

## <a name="create-a-traffic-manager-subnet-override"></a>Creare un'override della subnet di gestione traffico

Per creare una sostituzione della subnet di gestione traffico, è possibile usare Azure PowerShell per aggiungere le subnet per la sostituzione all'endpoint di gestione traffico.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

È possibile eseguire i comandi seguenti in [Azure Cloud Shell](https://shell.azure.com/powershell) oppure in PowerShell dal computer. Azure Cloud Shell è una shell interattiva gratuita. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Se si esegue PowerShell dal computer, è necessario il modulo Azure PowerShell versione 1.0.0 o successiva. Per trovare la versione installata, è possibile eseguire `Get-Module -ListAvailable Az`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzAccount` per accedere ad Azure.


1. **Recuperare l'endpoint di gestione traffico:**

    Per abilitare l'override della subnet, recuperare l'endpoint a cui si vuole aggiungere la sostituzione e archiviarlo in una variabile usando [Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Sostituire il nome, ProfileName e ResourceGroupName con i valori dell'endpoint che si sta modificando.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Aggiungere l'intervallo di indirizzi IP all'endpoint:**
    
    Per aggiungere l'intervallo di indirizzi IP all'endpoint, usare [Add-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0) per aggiungere l'intervallo.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    Una volta aggiunti gli intervalli, usare [set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) per aggiornare l'endpoint.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
Per completare la rimozione dell'intervallo di indirizzi IP, è possibile usare [Remove-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0).

1.  **Recuperare l'endpoint di gestione traffico:**

    Per abilitare l'override della subnet, recuperare l'endpoint a cui si vuole aggiungere la sostituzione e archiviarlo in una variabile usando [Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Sostituire il nome, ProfileName e ResourceGroupName con i valori dell'endpoint che si sta modificando.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Rimuovere l'intervallo di indirizzi IP dall'endpoint:**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     Una volta rimossi gli intervalli, usare [set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) per aggiornare l'endpoint.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sui [metodi di routing](traffic-manager-routing-methods.md)di Gestione traffico.

Informazioni sul [metodo di routing del traffico della subnet](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)
