---
title: Sostituzione della subnet di Gestione traffico di Azure con Azure PowerShell Documenti Microsoft
description: Questo articolo illustra come usare l'override della subnet di Gestione traffico per eseguire l'override del metodo di routing di un profilo di Gestione traffico per indirizzare il traffico a un endpoint basato sull'indirizzo IP dell'utente finale tramite un intervallo IP predefinito per i mapping degli endpoint tramite Azure Powershell.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 323093ec78a9486d19496b0ee90e37cb42eea341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938412"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Sostituzione della subnet di Gestione traffico con Azure PowershellTraffic Manager subnet override using Azure Powershell

L'override della subnet di Gestione traffico consente di modificare il metodo di routing di un profilo.  L'aggiunta di una sostituzione indirizzerà il traffico in base all'indirizzo IP dell'utente finale con un intervallo IP predefinito per il mapping degli endpoint. 

## <a name="how-subnet-override-works"></a>Funzionamento dell'override della subnet

Quando le sostituzioni di subnet vengono aggiunte a un profilo di gestione del traffico, Gestione traffico controllerà innanzitutto se è presente una sostituzione di subnet per l'indirizzo IP dell'utente finale. Se ne viene trovato uno, la query DNS dell'utente verrà indirizzata all'endpoint corrispondente.  Se non viene trovato un mapping, Gestione traffico eseguirà il rollback al metodo di routing originale del profilo. 

Gli intervalli di indirizzi IP possono essere specificati come intervalli CIDR (ad esempio, 1.2.3.0/24) o come intervalli di indirizzi (ad esempio, 1.2.3.4-5.6.7.8). Gli intervalli IP associati a ogni endpoint devono essere univoci per tale endpoint. Qualsiasi sovrapposizione di intervalli IP tra endpoint diversi causerà il rifiuto del profilo da parte di Gestione traffico.

Esistono due tipi di profili di routing che supportano le sostituzioni di subnet:

* **Geografico:** se Gestione traffico trova una sostituzione di subnet per l'indirizzo IP della query DNS, instrada la query all'endpoint indipendentemente dall'integrità dell'endpoint.
* **Prestazioni:** se Gestione traffico trova una sostituzione di subnet per l'indirizzo IP della query DNS, instrada il traffico all'endpoint solo se è integro.  Gestione traffico eseguirà il rollback all'euristica del routing delle prestazioni se l'endpoint di sostituzione della subnet non è integro.

## <a name="create-a-traffic-manager-subnet-override"></a>Creare una sostituzione della subnet di Gestione trafficoCreate a Traffic Manager subnet override

Per creare un override della subnet di Gestione traffico, è possibile usare Azure PowerShell per aggiungere le subnet per la sostituzione all'endpoint di Gestione traffico.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

È possibile eseguire i comandi seguenti in [Azure Cloud Shell](https://shell.azure.com/powershell) oppure in PowerShell dal computer. Azure Cloud Shell è una shell interattiva gratuita. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Se si esegue PowerShell dal computer, è necessario il modulo Azure PowerShell versione 1.0.0 o successiva. Per trovare la versione installata, è possibile eseguire `Get-Module -ListAvailable Az`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzAccount` per accedere ad Azure.


1. **Recuperare l'endpoint di Gestione traffico:Retrieve the Traffic Manager endpoint:**

    Per abilitare l'override della subnet, recuperare l'endpoint a cui si vuole aggiungere l'override e archiviarla in una variabile utilizzando [Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Sostituire Name, ProfileName e ResourceGroupName con i valori dell'endpoint da modificare.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Aggiungere l'intervallo di indirizzi IP all'endpoint:**
    
    Per aggiungere l'intervallo di indirizzi IP all'endpoint, si userà [Add-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0) per aggiungere l'intervallo.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    Una volta aggiunti gli intervalli, usare [Set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) per aggiornare l'endpoint.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
La rimozione dell'intervallo di indirizzi IP può essere completata utilizzando [Remove-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0).

1.  **Recuperare l'endpoint di Gestione traffico:Retrieve the Traffic Manager endpoint:**

    Per abilitare l'override della subnet, recuperare l'endpoint a cui si vuole aggiungere l'override e archiviarla in una variabile utilizzando [Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Sostituire Name, ProfileName e ResourceGroupName con i valori dell'endpoint da modificare.

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
     Una volta rimossi gli intervalli, usare [Set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) per aggiornare l'endpoint.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sui [metodi di routing](traffic-manager-routing-methods.md)di Gestione traffico.

Informazioni sul metodo di routing del [traffico della subnet](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)
