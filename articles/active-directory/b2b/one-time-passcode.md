---
title: Autenticazione del codice di accesso monouso per gli utenti Guest B2B-Azure AD
description: Come usare il passcode monouso tramite indirizzo di posta elettronica per autenticare gli utenti guest B2B senza la necessità di un account Microsoft.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6d897bb983eb06baa4f1573f1f875eea8bb8afc
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272315"
---
# <a name="email-one-time-passcode-authentication-preview"></a>Autenticazione con passcode monouso tramite indirizzo di posta elettronica (anteprima)

|     |
| --- |
| Passcode monouso tramite indirizzo di posta elettronica è una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Questo articolo descrive come abilitare l'autenticazione del codice di posta elettronica monouso per gli utenti Guest B2B. La funzionalità passcode monouso tramite indirizzo di posta elettronica permette di autenticare gli utenti guest B2B quando non possono essere autenticati tramite altri mezzi, quali Azure AD, un account Microsoft (MSA) o federazione Google. Grazie all'autenticazione con passcode monouso, non è necessario creare un account Microsoft. Quando l'utente guest riscatta un invito o accede a una risorsa condivisa, può richiedere un codice temporaneo, che viene inviato all'indirizzo di posta elettronica. Quindi immette tale codice per continuare ad accedere.

Questa funzionalità è attualmente disponibile in anteprima (consultare la sezione [Acconsentire esplicitamente all'anteprima](#opting-in-to-the-preview) di seguito). Dopo la versione di anteprima, questa funzionalità diverrà un'impostazione predefinita per tutti i tenant.

> [!NOTE]
> Gli utenti con passcode monouso devono accedere tramite un collegamento che include il contesto tenant (ad esempio `https://myapps.microsoft.com/?tenantid=<tenant id>` o `https://portal.azure.com/<tenant id>` oppure, nel caso di un dominio verificato, `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). È possibile usare anche collegamenti diretti alle applicazioni e alle risorse, purché includano tale contesto. Gli utenti guest attualmente non possono accedere tramite endpoint privi di un contesto tenant. Se si usa ad esempio `https://myapps.microsoft.com`, `https://portal.azure.com` o l'endpoint comune di Teams, verrà generato un errore. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Esperienza utente per gli utenti guest con passcode monouso
Grazie all'autenticazione con passcode monouso, l'utente guest può riscattare l'invito facendo clic su un collegamento diretto o tramite l'indirizzo di posta elettronica di invito. In entrambi i casi, un messaggio nel browser indica che verrà inviato un codice all'indirizzo di posta elettronica dell'utente guest. L'utente guest seleziona **Send code** (Invia codice):
 
   ![Screenshot che mostra il pulsante Invia codice](media/one-time-passcode/otp-send-code.png)
 
Un passcode viene inviato all'indirizzo di posta elettronica dell'utente. L'utente recupera il passcode dal messaggio di posta elettronica e lo immette nella finestra del browser:
 
   ![Screenshot che mostra la pagina di immissione del codice](media/one-time-passcode/otp-enter-code.png)
 
L'utente guest viene autenticato e può quindi visualizzare la risorsa condivisa o continuare ad accedere. 

> [!NOTE]
> I passcode monouso sono validi per 30 minuti. Dopo 30 minuti, tale passcode monouso specifico non è più valido e l'utente deve richiederne uno nuovo. Le sessioni utente scadono dopo 24 ore. Dopo tale periodo, l'utente guest riceve un nuovo passcode quando accede alla risorsa. La scadenza della sessione offre una sicurezza maggiore, in particolare quando un utente guest lascia la società o non ha più bisogno dell'accesso.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>In quali casi un utente guest ottiene un passcode monouso?

Quando un utente guest riscatta un invito o usa un collegamento a una risorsa che è stato condivisa con tale utente, questo riceve un passcode monouso se:
- Non ha un account Azure AD 
- Non ha un account Microsoft 
- Il tenant che emette l'invito non ha impostato federazione Google per gli utenti @gmail.com e @googlemail.com 

Al momento dell'invito non è presente alcuna indicazione del fatto che l'utente che si sta invitando userà l'autenticazione con passcode monouso. Tuttavia quando l'utente guest accede, l'autenticazione con passcode monouso sarà il metodo di fallback se non è possibile utilizzare altri metodi di autenticazione. 

È possibile visualizzare gli utenti guest che eseguono l'autenticazione con i passcode monouso nel portale di Azure passando ad **Azure Active Directory** > **Relazioni aziendali**  >  **Utenti da altre organizzazioni**.

![Screenshot che mostra un utente del codice di accesso monouso con il valore di origine OTP](media/one-time-passcode/otp-users.png)

> [!NOTE]
> Quando un utente riscatta un passcode monouso e in un secondo momento ottiene un account Microsoft, un account di Azure AD o un altro account federato, continuerà a essere autenticato con un passcode monouso. Se si vuole aggiornare il metodo di autenticazione di tale utente, è possibile eliminare l'account utente guest e invitarlo nuovamente.

### <a name="example"></a>Esempio
Utente guest alexdoe@gmail.com viene invitato a Fabrikam, che non dispone di federazione Google configurata. Alex non ha un account Microsoft. Riceveranno un unico codice per l'autenticazione.

## <a name="opting-in-to-the-preview"></a>Acconsentire esplicitamente all'anteprima 
Potrebbero volerci alcuni minuti affinché l'azione di consenso esplicito abbia effetto. In seguito, solo gli utenti appena invitati che soddisfano le condizioni riportate sopra useranno l'autenticazione con passcode monouso. Gli utenti guest che hanno precedentemente riscattato un invito continueranno a usare lo stesso metodo di autenticazione.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>Per acconsentire esplicitamente tramite il portale di Azure AD
1.  Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale di Azure AD.
2.  Nel riquadro di spostamento selezionare **Azure Active Directory**.
3.  In **Gestisci**  selezionare **Relazioni aziendali**.
4.  Selezionare **Impostazioni**.
5.  In **Abilita passcode monouso tramite posta elettronica per gli utenti guest (anteprima)** selezionare **Sì**.
 
### <a name="to-opt-in-using-powershell"></a>Per acconsentire esplicitamente tramite PowerShell

Innanzitutto è necessario installare la versione più recente di Azure AD PowerShell per il modulo Graph (AzureADPreview). Quindi è possibile stabilire se i criteri di B2B esistono già ed eseguono i comandi appropriati.

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>Prerequisito: installare il modulo AzureADPreview più recente
Controllare innanzitutto quali moduli sono installati. Aprire Windows PowerShell come utente con privilegi elevati (Esegui come amministratore) ed eseguire il comando seguente:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

Se il modulo AzureADPreview viene visualizzato senza alcun messaggio che indica la disponibilità di una versione più recente, procedere. In caso contrario, in base al contenuto del messaggio, eseguire una delle operazioni seguenti:

- Se non viene restituito alcun risultato, eseguire il comando seguente per installare il modulo AzureADPreview:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Se nei risultati viene visualizzato solo il modulo AzureAD, eseguire i comandi seguenti per installare il modulo AzureADPreview: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Se nei risultati viene visualizzato solo il modulo AzureADPreview, ma si riceve un messaggio che indica che è disponibile una versione più recente, eseguire i comandi seguenti per aggiornare il modulo: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

Potrebbe essere visualizzato un prompt che indica che si sta installando il modulo da un repository non attendibile. Ciò si verifica se il repository PSGallery non è stato precedentemente impostato come repository attendibile. Premere **Y** per installare il modulo.

#### <a name="check-for-existing-policies-and-opt-in"></a>Verificare la presenza di criteri esistenti e acconsentire esplicitamente

Successivamente, verificare se un B2BManagementPolicy esiste eseguendo il comando seguente:

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- Se l'output è False, i criteri attualmente non esistono. Creare un nuovo B2BManagementPolicy e acconsentire esplicitamente all'anteprima eseguendo il comando seguente:

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- Se l'output è True, il criterio B2BManagementPolicy è attualmente esistente. Per aggiornare i criteri e acconsentire esplicitamente all'anteprima, eseguire il comando seguente:
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>Rifiutare esplicitamente l'anteprima dopo avervi acconsentito esplicitamente
Potrebbero volerci alcuni minuti affinché l'azione di rifiuto esplicito abbia effetto. Se si disattiva l'anteprima, nessun utente guest che abbia riscattato un passcode monouso sarà in grado di accedere. È possibile eliminare l'utente guest e invitare nuovamente l'utente per consentirgli nuovamente l'accesso usando un altro metodo di autenticazione.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>Per disattivare l'anteprima tramite il portale di Azure AD
1.  Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale di Azure AD.
2.  Nel riquadro di spostamento selezionare **Azure Active Directory**.
3.  In **Gestisci**  selezionare **Relazioni aziendali**.
4.  Selezionare **Impostazioni**.
5.  In **Abilita passcode monouso tramite posta elettronica per gli utenti guest (anteprima)** selezionare **No**.

### <a name="to-turn-off-the-preview-using-powershell"></a>Per disattivare l'anteprima tramite PowerShell
Installare il modulo AzureADPreview più recente, se non è già presente (vedere [prerequisito: installare il modulo AzureADPreview più recente](#prerequisite-install-the-latest-azureadpreview-module) ). Verificare quindi che i criteri di anteprima di passcode monouso siano attualmente esistenti eseguendo il comando seguente:

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

Se l'output è True, rifiutare esplicitamente l'anteprima eseguendo il comando seguente:

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

