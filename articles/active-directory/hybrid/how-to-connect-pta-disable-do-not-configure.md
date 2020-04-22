---
title: Disabilitare PTA quando si usa Azure AD Connect "Non configurare" Documenti Microsoft
description: In questo articolo viene descritto come disabilitare PTA con la funzionalità di Azure AD Connect "non configurare".
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa1046dc64fed3edb6c9d04f76a96f488769ff42
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726800"
---
# <a name="disable-pta-when-using-azure-ad-connect-do-not-configure"></a>Disabilitare PTA quando si usa Azure AD Connect "Non configurare"Disable PTA when using Azure AD Connect "Do not configure"

Se si usa l'autenticazione pass-through con Azure AD Connect e l'utente è impostato su "Non configurare", è possibile disabilitarla. La disabilitazione di PTA può essere eseguita utilizzando i cmdlet seguenti. 

## <a name="prerequisites"></a>Prerequisiti
Sono richiesti i prerequisiti seguenti:
- Qualsiasi computer Windows in cui è installato l'agente PTA. 
- L'agente deve essere alla versione 1.5.1742.0 o successiva. 
- Un account amministratore globale di Azure per eseguire i cmdlet di PowerShell per disabilitare PTA.

>[!NOTE]
> Se l'agente è meno recente, è possibile che non disponga dei cmdlet necessari per completare l'operazione. È possibile ottenere un nuovo agente dal portale di Azure e installarlo in qualsiasi computer Windows e fornire le credenziali di amministratore. (L'installazione dell'agente non influisce sullo stato PTA nel cloud)

> [!IMPORTANT]
> Se si usa il cloud di Azure per enti pubblici, sarà necessario passare il parametro ENVIRONMENTNAME con il valore seguente. 
>
>| Nome ambiente | Cloud |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>Per disabilitare PTA
Dall'interno di una sessione di PowerShell, usare quanto segue per disabilitare PTA:From within a PowerShell session, use the following to disable PTA:
1. PS C: , Programmi , Agente di autenticazione Microsoft Azure AD Connect>`Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` o `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` o `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>Se non si ha accesso a un agente

Se non si dispone di un computer agente, è possibile utilizzare il comando seguente per installare un agente.

1. Scaricare l'agente di autenticazione più recente da portal.azure.com.
2. Installare la `.\AADConnectAuthAgentSetup.exe` funzione: o`.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>Passaggi successivi

- [Accesso utente con l'autenticazione pass-through di Azure Active Directory](how-to-connect-pta.md)