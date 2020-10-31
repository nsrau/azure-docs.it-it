---
title: Installare il gateway dati locale per Azure Analysis Services | Microsoft Docs
description: Informazioni su come installare e configurare un gateway dati locale per connettersi a origini dati locali da un server Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 64bd9e4a4cf78d2628e946af30c2d290ff002cf7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081145"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Installare e configurare un gateway dati locale

Quando uno o più server Azure Analysis Services nella stessa area si connettono a origini dati locali, è necessario un gateway dati locale.  Mentre il gateway installato è lo stesso usato da altri servizi, ad esempio Power BI, Power Apps e app per la logica, quando si installa per Azure Analysis Services, è necessario completare alcuni passaggi aggiuntivi. Questo articolo di installazione è specifico per **Azure Analysis Services** . 

Per altre informazioni sul funzionamento di Azure Analysis Services con il gateway, vedere [connessione alle origini dati locali](analysis-services-gateway.md). Per altre informazioni sugli scenari di installazione avanzati e sul gateway in generale, vedere la documentazione relativa ai [gateway dati locali](/data-integration/gateway/service-gateway-onprem).

## <a name="prerequisites"></a>Prerequisiti

**Requisiti minimi:**

* .NET Framework 4.5
* versione a 64 bit di Windows 8/Windows Server 2012 R2 (o versioni successive)

**Consigliato:**

* 8 CPU core
* 8 GB di memoria
* versione a 64 bit di Windows 8/Windows Server 2012 R2 (o versioni successive)

**Considerazioni importanti:**

* Durante l'installazione, quando si registra il gateway con Azure, viene selezionata l'area predefinita per la sottoscrizione. È possibile scegliere una sottoscrizione e un'area diversi. Se si dispone di server in più aree, è necessario installare un gateway per ogni area. 
* Il gateway non può essere installato in un controller di dominio.
* In un singolo computer può essere installato un solo gateway.
* Installare il gateway in un computer che rimane attivo e non passa alla modalità di sospensione.
* Non installare il gateway in un computer con una connessione wireless solo alla rete. È infatti possibile che si verifichi un calo delle prestazioni.
* Quando si installa il gateway, l'account utente con il quale è stato effettuato l'accesso al computer deve accedere come privilegi del servizio. Al termine dell'installazione, il servizio di gateway dati locale usa l'account NT SERVICE\PBIEgwService per accedere come un servizio. È possibile specificare un altro account durante l'installazione o in Services al termine dell'installazione. Assicurarsi che le impostazioni dei Criteri di gruppo consentano di accedere come privilegi di servizio all'account con il quale ci si è connessi al momento dell'installazione e all'account di servizio scelto per l'accesso.
* Accedere ad Azure con un account di Azure AD per lo stesso [tenant](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) della sottoscrizione in cui si sta registrando il gateway. Gli account Azure B2B (guest) non sono supportati per l'installazione e registrazione di un gateway.
* Se le origini dati si trovano in un'istanza di Rete virtuale di Azure (VNet), è necessario configurare la proprietà del server [AlwaysUseGateway](analysis-services-vnet-gateway.md).

## <a name="download"></a>Scarica

 [Scaricare il gateway](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a>Installazione

1. Eseguire l'installazione.

2. Selezionare **gateway dati locale** .

   ![Select](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Selezionare un percorso, accettare le condizioni e quindi fare clic su **Installa** .

   ![Percorso di installazione e condizioni di licenza](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Accedere ad Azure. L'account deve essere nell'istanza di Microsoft Azure Active Directory del tenant. Questo account viene usato per l'amministratore del gateway. Gli account Azure B2B (guest) non sono supportati per l'installazione e registrazione del gateway.

   ![Accedere ad Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Se si accede con un account di dominio, questo viene mappato all'account aziendale in Azure AD. L'account aziendale viene usato come amministratore del gateway.

## <a name="register"></a>Registrazione

Per creare una risorsa per il gateway in Azure, è necessario registrare l'istanza locale installata con il servizio cloud gateway. 

1.  Selezionare l'opzione **Consente di registrare un nuovo gateway in questo computer** .

    ![Screenshot che evidenzia l'opzione Registra un nuovo gateway in questo computer.](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Digitare un nome e la chiave di ripristino per il gateway. Per impostazione predefinita, il gateway usa l'area predefinita della sottoscrizione. Se è necessario selezionare un'area diversa, selezionare **Cambia area** .

    > [!IMPORTANT]
    > Salvare la chiave di ripristino in un luogo sicuro. La chiave di ripristino è necessaria per poter acquisire la proprietà, eseguire la migrazione o ripristinare un gateway. 

   ![Registrazione](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a>Creare una risorsa per il gateway di Azure

Dopo aver installato e registrato il gateway, è necessario creare una risorsa del gateway in Azure. Accedere ad Azure con lo stesso account usato durante la registrazione del gateway.

1. In portale di Azure fare clic su **Crea una risorsa** , quindi cercare **il gateway dati locale** e quindi fare clic su **Crea** .

   ![Creazione di una risorsa per il gateway](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. In **Crea gateway di connessione** immettere queste impostazioni:

   * **Nome** : inserire un nome per la risorsa del gateway. 

   * **Sottoscrizione** : selezionare la sottoscrizione di Azure da associare alla risorsa del gateway. 
   
     La sottoscrizione predefinita si basa sull'account di Azure usato per accedere.

   * **Gruppo di risorse** : creare un gruppo di risorse o selezionarne uno esistente.

   * **Percorso** : selezionare l'area in cui è stato registrato il gateway.

   * **Nome installazione** : se l'installazione del gateway non è già selezionata, selezionare il gateway installato nel computer e registrato. 

     Al termine dell'operazione, scegliere **Crea** .

## <a name="connect-gateway-resource-to-server"></a>Connetti risorsa gateway al server

> [!NOTE]
> La connessione a una risorsa del gateway in una sottoscrizione diversa dal server non è supportata nel portale, ma è supportata tramite PowerShell.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nella panoramica del server Azure Analysis Services fare clic su **Gateway dati locale** .

   ![Connessione del server al gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. In **Selezionare un gateway dati locale da connettere** selezionare la risorsa per il gateway e quindi fare clic su **Connetti gateway selezionato** .

   ![Connessione del server alla risorsa per il gateway](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Se il gateway non viene visualizzato nell'elenco, probabilmente il server non si trova nell'area specificata durante la registrazione del gateway.

    Quando la connessione tra il server e la risorsa del gateway ha esito positivo, lo stato visualizzerà **Connected** .


    ![Connessione del server alla risorsa del gateway riuscita](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Usare [Get-AzResource](/powershell/module/az.resources/get-azresource) per ottenere il ResourceID del gateway. Connettere quindi la risorsa gateway a un server nuovo o esistente specificando **-GatewayResourceID** in [set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) o [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver).

Per ottenere l'ID risorsa del gateway:

```azurepowershell-interactive
Connect-AzAccount -Tenant $TenantId -Subscription $subscriptionIdforGateway -Environment "AzureCloud"
$GatewayResourceId = $(Get-AzResource -ResourceType "Microsoft.Web/connectionGateways" -Name $gatewayName).ResourceId  

```

Per configurare un server esistente:

```azurepowershell-interactive
Connect-AzAccount -Tenant $TenantId -Subscription $subscriptionIdforAzureAS -Environment "AzureCloud"
Set-AzAnalysisServicesServer -ResourceGroupName $RGName -Name $servername -GatewayResourceId $GatewayResourceId

```
---

È tutto. Se è necessario aprire le porte o risolvere eventuali problemi, vedere l'articolo su [Gateway dati locale](analysis-services-gateway.md).

## <a name="next-steps"></a>Passaggi successivi

* [Gestire Analysis Services](analysis-services-manage.md)   
* [Recuperare i dati da Azure Analysis Services](analysis-services-connect.md)   
* [Usare il gateway per le origini dati in un'istanza di Rete virtuale di Azure](analysis-services-vnet-gateway.md)