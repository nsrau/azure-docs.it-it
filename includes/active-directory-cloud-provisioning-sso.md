---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: 3aa1571b46938b03f556fa124d3f0a2a70f2c5c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "79504386"
---
## <a name="steps-to-enable-single-sign-on"></a>Passaggi per abilitare Single Sign-On
Il provisioning cloud funziona con Single Sign-On.  Attualmente non è disponibile un'opzione per abilitare SSO durante l'installazione dell'agente, tuttavia è possibile utilizzare la procedura seguente per abilitare SSO e utilizzarlo. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>Passaggio 1: Scaricare ed estrarre i file di Azure AD ConnectStep 1: Download and extract Azure AD Connect files
1.  Scaricare innanzitutto la versione più recente di [Azure AD ConnectFirst,](https://www.microsoft.com/download/details.aspx?id=47594) download the latest version of Azure AD Connect
2.  Aprire un prompt dei comandi utilizzando privilegi amministrativi e passare al file msi appena scaricato.
3.  Eseguire quanto segue:`msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. Modificare il percorso del file e la cartella di estrazione in modo che corrispondano al percorso del file e al nome della cartella di estrazione.  Il contenuto dovrebbe ora trovarsi nella cartella di estrazione.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>Passaggio 2: Importare il modulo PowerShell SSO sempliceStep 2: Import the Seamless SSO PowerShell module

1. Scaricare e installare [Azure AD PowerShell.](https://docs.microsoft.com/powershell/azure/active-directory/overview)
2. Passare alla cartella `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importare il modulo di PowerShell Seamless SSO usando il comando seguente: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Passaggio 3: Ottenere l'elenco delle foreste di Active Directory in cui è stato abilitato SSO semplice

1. Eseguire PowerShell come amministratore. In PowerShell eseguire la chiamata a `New-AzureADSSOAuthenticationContext`. Quando richiesto, immettere le credenziali di amministratore globale del tenant.
2. Chiamare `Get-AzureADSSOStatus`. Questo comando consente di visualizzare l'elenco di foreste di Active Directory, ovvero l'elenco "Domini", in cui è stata abilitata questa funzionalità.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Passaggio 4: abilitare l'accesso SSO facile per ogni foresta di Active Directory

1. Chiamare `Enable-AzureADSSOForest`. Quando richiesto, immettere le credenziali dell'amministratore di dominio per la foresta di Active Directory da usare.

   > [!NOTE]
   >Il nome utente delle credenziali di amministratore di dominio deve essere immesso nel formato del nome dell'account SAM (contoso.johndoe o contoso.com/johndoe). Utilizziamo la parte del dominio del nome utente per individuare il controller di dominio dell'amministratore di dominio tramite DNS.

   >[!NOTE]
   >L'account amministratore di dominio utilizzato non deve essere membro del gruppo utenti protetti. In tal caso, l'operazione avrà esito negativo.

2. Ripetere la procedura precedente per ogni foresta di Active Directory in cui si desidera configurare la funzionalità.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Passaggio 5. Abilitare la funzionalità nel tenant

Per abilitare la funzionalità nel tenant, chiamare `Enable-AzureADSSO -Enable $true`.
