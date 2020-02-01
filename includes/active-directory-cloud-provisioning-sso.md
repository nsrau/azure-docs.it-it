---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: ba1bdd60a3363cfab694bae9b8ee3cf63e24b054
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907578"
---
## <a name="steps-to-enable-single-sign-on"></a>Passaggi per abilitare l'accesso Single Sign-on
Il provisioning Cloud funziona con Single Sign-on.  Attualmente non è disponibile un'opzione per abilitare SSO durante l'installazione dell'agente, tuttavia è possibile utilizzare la procedura seguente per abilitare l'accesso SSO e utilizzarlo. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>Passaggio 1: scaricare ed estrarre i file di Azure AD Connect
1.  Scaricare prima di tutto la versione più recente di [Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2.  Aprire un prompt dei comandi usando i privilegi amministrativi e passare al file MSI appena scaricato.
3.  Eseguire il comando seguente: `msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. Modificare FilePath e extractfolder in modo che corrispondano al percorso del file e al nome della cartella di estrazione.  Il contenuto dovrebbe ora trovarsi nella cartella estrazione.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>Passaggio 2: importare il modulo di PowerShell seamless SSO

1. Scaricare e installare [Azure ad PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Passare alla cartella `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importare il modulo di PowerShell Seamless SSO usando il comando seguente: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Passaggio 3: ottenere l'elenco di foreste Active Directory in cui è stato abilitato seamless SSO

1. Eseguire PowerShell come amministratore. In PowerShell eseguire la chiamata a `New-AzureADSSOAuthenticationContext`. Quando richiesto, immettere le credenziali di amministratore globale del tenant.
2. Chiamare `Get-AzureADSSOStatus`. Questo comando consente di visualizzare l'elenco di foreste di Active Directory, ovvero l'elenco "Domini", in cui è stata abilitata questa funzionalità.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Passaggio 4: abilitare l'accesso SSO facile per ogni foresta di Active Directory

1. Chiamare `Enable-AzureADSSOForest`. Quando richiesto, immettere le credenziali dell'amministratore di dominio per la foresta di Active Directory da usare.

   > [!NOTE]
   >Il nome utente delle credenziali di amministratore di dominio deve essere specificato nel formato del nome dell'account SAM (CONTOSO\johndoe o contoso. com\johndoe). Viene utilizzata la parte di dominio del nome utente per individuare il controller di dominio dell'amministratore di dominio tramite DNS.

   >[!NOTE]
   >L'account amministratore di dominio utilizzato non deve essere un membro del gruppo utenti protetti. In tal caso, l'operazione avrà esito negativo.

2. Ripetere la procedura precedente per ogni foresta di Active Directory in cui si desidera configurare la funzionalità.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Passaggio 5. Abilitare la funzionalità nel tenant

Per abilitare la funzionalità nel tenant, chiamare `Enable-AzureADSSO -Enable $true`.
