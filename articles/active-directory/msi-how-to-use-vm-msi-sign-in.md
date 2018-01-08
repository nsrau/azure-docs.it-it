---
title: "Come usare un'identità del servizio gestito di una macchina virtuale di Azure per l'accesso"
description: "Istruzioni dettagliate ed esempi relativi all'uso di un'identità del servizio gestito di una macchina virtuale di Azure per l'accesso al client di script e alle risorse."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: bryanla
ms.openlocfilehash: 6c9e3ce4bbe33d06af64d97e1455ec20902d0ff4
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in"></a>Come usare un'identità del servizio gestito di una macchina virtuale di Azure per l'accesso 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]  
In questo articolo vengono forniti esempi di script di PowerShell e CLI per l'accesso utilizzando un entità servizio MSI e informazioni aggiuntive su argomenti importanti, ad esempio la gestione degli errori.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Se si prevede di usare gli esempi di Azure PowerShell e dell'interfaccia della riga di comando di Azure presenti in questo articolo, assicurarsi di installare la versione più recente di [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) o dell'[Interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Tutti gli esempi di script in questo articolo presuppongono che il client da riga di comando sia in esecuzione su una macchina virtuale abilitata per l'identità del servizio gestito. Usare la funzionalità di connessione alla macchina virtuale nel portale di Azure per connettersi in remoto alla macchina virtuale. Per informazioni dettagliate sull'abilitazione dell'identità del servizio gestito in una macchina virtuale, vedere [Configurare un'Identità del servizio gestito della macchina virtuale tramite il portale di Azure](msi-qs-configure-portal-windows-vm.md) o una delle varianti dell'articolo (per PowerShell, interfaccia della riga di comando, un modello o una versione di Azure SDK). 
> - Per evitare errori durante l'accesso alle risorse, è necessario che all'identità del servizio gestito della macchina virtuale venga assegnato almeno l'accesso in "Lettura" nell'ambito appropriato (almeno la macchina virtuale) per consentire ad Azure Resource Manager di eseguire operazioni sulla macchina virtuale. Per dettagli, vedere [Assegnare a un'Identità del servizio gestito, ovvero MSI, l'accesso a una risorsa tramite il portale di Azure](msi-howto-assign-access-portal.md).

## <a name="overview"></a>Panoramica

Un'identità del servizio gestito fornisce un'[entità servizio](develop/active-directory-dev-glossary.md#service-principal-object), [creata con l'abilitazione dell'identità del servizio gestito](msi-overview.md#how-does-it-work) nella macchina virtuale. L'entità servizio può acquisire l'accesso alle risorse di Azure e può essere usata come identità dai client da script o riga di comando per l'accesso al client e alle risorse. In genere, per poter accedere a risorse protette con la propria identità, un client di script deve:  

   - essere registrato e autorizzato da Azure AD come applicazione client Web/riservata
   - accedere con la propria entità servizio usando le credenziali dell'app (probabilmente integrate nello script)

Con l'identità del servizio gestito, il client non deve più soddisfare questi requisiti e può accedere con l'entità servizio dell'identità del servizio gestito. 

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Lo script seguente illustra come:

1. Accedere ad Azure AD con l'entità servizio dell'identità del servizio gestito della macchina virtuale  
2. Chiamare Azure Resource Manager e ottenere l'ID dell'entità servizio della macchina virtuale. L'interfaccia della riga di comando si occupa di gestire automaticamente l'acquisizione/uso dei token. Assicurarsi di sostituire il nome della macchina virtuale per `<VM-NAME>`.  

   ```azurecli
   az login --msi
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The MSI service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Lo script seguente illustra come:

1. Acquisire un token di accesso dell'identità del servizio gestito per la macchina virtuale.  
2. Usare il token di accesso per accedere ad Azure AD con l'entità servizio dell'identità del servizio gestito corrispondente.   
3. Chiamare un cmdlet di Azure Resource Manager per ottenere informazioni sulla macchina virtuale. PowerShell si occupa di gestire automaticamente l'uso dei token.  

   ```azurepowershell
   # Get an access token for the MSI
   $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                                 -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
   $content =$response.Content | ConvertFrom-Json
   $access_token = $content.access_token
   echo "The MSI access token is $access_token"

   # Use the access token to sign in under the MSI service principal. -AccountID can be any string to identify the session.
   Login-AzureRmAccount -AccessToken $access_token -AccountId "MSI@50342"

   # Call Azure Resource Manager to get the service principal ID for the VM's MSI. 
   $vmInfoPs = Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The MSI service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>ID di risorsa per i servizi di Azure

Per un elenco di risorse che supportano Azure AD e che sono state testate con l'identità del servizio gestito e i relativi ID di risorsa, vedere [Servizi di Azure che supportano l'autenticazione di Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication).

## <a name="error-handling-guidance"></a>Istruzioni per la gestione degli errori 

Risposte come quelle riportate di seguito possono indicare che l'identità del servizio gestito della macchina virtuale non è stata configurata correttamente:

- PowerShell: *Invoke-WebRequest: Impossibile connettersi al server remoto*
- Interfaccia della riga di comando: *MSI: Failed to retrieve a token from 'http://localhost:50342/oauth2/token' with an error of 'HTTPConnectionPool(host='localhost', port=50342)* (Identità del servizio gestito: Impossibile recuperare un token da 'http://localhost:50342/oauth2/token' con un errore 'HTTPConnectionPool(host='localhost', port=50342)) 

Se si riceve uno di questi errori, tornare alla macchina virtuale di Azure nel [portale di Azure](https://portal.azure.com) e:

- Andare alla pagina **Configurazione** e assicurarsi che l'opzione "Identità del servizio gestito" sia impostata su "Sì".
- Andare alla pagina **Estensioni** e verificare che l'estensione dell'identità del servizio gestito sia stata distribuita correttamente.

Se una delle due opzioni è errata, è necessario ridistribuire l'Identità del servizio gestito nella risorsa o risolvere il problema di distribuzione. Vedere [Configurare un'identità del servizio gestito della macchina virtuale tramite il portale di Azure](msi-qs-configure-portal-windows-vm.md) per informazioni sulla configurazione della macchina virtuale.

## <a name="related-content"></a>Contenuti correlati

- Per abilitare l'identità del servizio gestito in una macchina virtuale di Azure, vedere [Configurare un'Identità del servizio gestito della macchina virtuale di Azure mediante PowerShell](msi-qs-configure-powershell-windows-vm.md) oppure [Configurare un'Identità del servizio gestito della macchina virtuale tramite l'interfaccia della riga di comando di Azure](msi-qs-configure-cli-windows-vm.md)

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.








