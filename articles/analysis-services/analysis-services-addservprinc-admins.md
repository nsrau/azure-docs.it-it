---
title: Aggiungere un'entità servizio al ruolo di amministratore di Azure Analysis Services . Documenti Microsoft
description: Informazioni su come aggiungere un'entità servizio di automazione al ruolo di amministratore del server di Azure Analysis ServicesLearn how to add an automation service principal to the Azure Analysis Services server admin role
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: 1370f65405963ebf825e986e6801607a0d96156e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298089"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Aggiungere un'entità servizio al ruolo di amministratore del server 

 Per automatizzare le attività di PowerShell, un'entità servizio deve avere privilegi di **amministratore del server** nel server Analysis Services gestito. Questo articolo descrive come aggiungere un'entità servizio al ruolo di amministratore del server in un server Azure Analysis Services. È possibile eseguire questa operazione usando SQL Server Management StudioSQL Server Management Studio o un modello di Resource Manager.You can do this using SQL Server Management StudioSQL Server Management Studio or a Resource Manager template.

## <a name="before-you-begin"></a>Prima di iniziare
Prima di completare questa attività, è necessario aver creato un'entità servizio registrata in Azure Active Directory.

[Creare un'entità servizio - Portale di AzureCreate service principal - Azure portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Creare un'entità servizio - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Utilizzare SQL Server Management Studio

È possibile configurare gli amministratori del server utilizzando SQL Server Management Studio (SSMS). Per completare questa attività, è necessario avere autorizzazioni di [amministratore del server](analysis-services-server-admins.md) nel server Azure Analysis Services. 

1. In SSMS connettersi al server Azure Analysis Services.
2. In**Protezione** **proprietà** > server fare clic su **Aggiungi**.
3. In **Selezionare un utente o un gruppo** cercare l'app registrata per nome, selezionarla e quindi fare clic su **Aggiungi**.

    ![Cercare l'account dell'entità servizio](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Verificare l'ID account dell'entità servizio e quindi fare clic su **OK**.
    
    ![Cercare l'account dell'entità servizio](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Utilizzo di un modello di Resource ManagerUsing a Resource Manager template

È anche possibile configurare gli amministratori del server distribuendo il server Analysis Services usando un modello di Azure Resource Manager.You can also configure server administrators by deploying the Analysis Services server using an Azure Resource Manager template. L'identità che esegue la distribuzione deve appartenere al ruolo **Collaboratore** per la risorsa nel [controllo degli accessi](../role-based-access-control/overview.md)in base al ruolo di Azure.

> [!IMPORTANT]
> L'entità servizio deve essere `app:{service-principal-client-id}@{azure-ad-tenant-id}`aggiunta utilizzando il formato .

Il modello di Resource Manager seguente distribuisce un server Analysis ServicesAnalysis Services con un'entità servizio specificata aggiunta al ruolo di amministrazione di Analysis ServicesAnalysis Services :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "analysisServicesServerName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "analysisServicesSkuName": {
            "type": "string"
        },
        "analysisServicesCapacity": {
            "type": "int"
        },
        "servicePrincipalClientId": {
            "type": "string"
        },
        "servicePrincipalTenantId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('analysisServicesServerName')]",
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('analysisServicesSkuName')]",
                "capacity": "[parameters('analysisServicesCapacity')]"
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "[concat('app:', parameters('servicePrincipalClientId'), '@', parameters('servicePrincipalTenantId'))]"
                    ]
                }
            }
        }
    ]
}
```

## <a name="using-managed-identities"></a>Utilizzo di identità gestiteUsing managed identities

È inoltre possibile aggiungere un'identità gestita all'elenco Amministratori di Analysis ServicesAnalysis Services . Ad esempio, si potrebbe avere [un'app per la logica con un'identità gestita assegnata](../logic-apps/create-managed-service-identity.md)dal sistema e si desidera concedergli la possibilità di amministrare il server Analysis Services.

Nella maggior parte delle parti del portale di Azure e delle API, le identità gestite vengono identificate tramite l'ID oggetto entità servizio. Tuttavia, Analysis ServicesAnalysis Services richiede che vengano identificati utilizzando il relativo ID client. Per ottenere l'ID client per un'entità servizio, è possibile usare l'interfaccia della riga di comando di Azure:To obtain the client ID for a service principal, you can use the Azure CLI:

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

In alternativa, è possibile usare PowerShell:Alternatively you can use PowerShell:

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

È quindi possibile usare questo ID client insieme all'ID tenant per aggiungere l'identità gestita all'elenco Amministratori di Analysis ServicesAnalysis Services , come descritto in precedenza.

## <a name="related-information"></a>Informazioni correlate

* [Scaricare il modulo PowerShell di SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Scaricare SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


