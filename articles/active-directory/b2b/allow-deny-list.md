---
title: Consente o Blocca invites da organizzazioni specifiche - Azure Active Directory | Microsoft Docs
description: Viene illustrato come un amministratore può usare il portale di Azure o PowerShell per impostare un elenco di accesso consentito o negato per consentire o impedire agli utenti di B2B di accedere da determinati domini.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa975446c19db3176fdb89ccfb1a987b1fda049d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113229"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Consentire o bloccare gli inviti agli utenti B2B da organizzazioni specifiche

È possibile usare un elenco di accesso consentito o un elenco di accesso negato per bloccare gli inviti agli utenti B2B da organizzazioni specifiche. Ad esempio, se si desidera bloccare i domini di indirizzi di posta elettronica personale, è possibile configurare un elenco di accesso negato che contenga domini come Gmail.com e Outlook.com. Oppure, se l'azienda collabora con altre, ad esempio Contoso.com e Fabrikam.com e Litware.com e si vuole limitare gli inviti solo a queste organizzazioni, è possibile aggiungere Contoso.com, Fabrikam.com e Litware.com all'elenco di accesso consentito.
  
## <a name="important-considerations"></a>Considerazioni importanti

- È possibile creare un elenco di accesso consentito o un elenco di accesso negato. Non è consentita la configurazione di entrambi i tipi di elenchi. Per impostazione predefinita, i domini non inclusi nell'elenco di accesso consentito vengono inclusi nell'elenco di accesso negato e viceversa. 
- È possibile creare un solo criterio per organizzazione. È possibile aggiornare il criterio per includere ulteriori domini oppure eliminarlo per crearne uno nuovo. 
- Questo elenco funziona in modo indipendente dagli elenchi di accesso consentito/bloccato di OneDrive for Business e SharePoint Online. Se si vuole limitare la condivisione di singoli file in SharePoint Online, è necessario impostare un elenco di accesso consentito o un elenco di accesso negato per OneDrive for Business e SharePoint Online. Per altre informazioni, vedere [Condivisione di domini con restrizioni in SharePoint Online e OneDrive for Business](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- Questo elenco non è applicabile a utenti esterni che hanno già accettato l'invito. L'elenco verrà applicato dopo la configurazione. Se un invito di un utente è in sospeso e si imposta un criterio che blocca il suo dominio, il tentativo dell'utente di accettare l'invito avrà esito negativo.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Impostare il criterio per l'elenco di accesso consentito o l'elenco di accesso negato nel portale

Per impostazione predefinita, l'impostazione **Allow invitations to be sent to any domain (most inclusive)** (Consenti l'invio di inviti a qualsiasi dominio - meno restrittiva) è abilitata. In questo caso, è possibile invitare gli utenti B2B da qualsiasi organizzazione.

### <a name="add-a-deny-list"></a>Aggiungere un elenco di accesso negato

Questo è lo scenario più comune, in cui l'organizzazione vuole collaborare quasi con qualsiasi organizzazione, ma vuole impedire agli utenti di domini specifici di essere invitati come utenti B2B.

Per aggiungere un elenco di accesso negato:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory** > **Utenti** > **Impostazioni utente**.
3. In **Utenti esterni** selezionare **Manage external collaboration settings** (Gestisci impostazioni di collaborazione esterna).
4. In **Collaboration restrictions** (Restrizioni per la collaborazione) selezionare **Deny invitations to the specified domains** (Nega inviti ai domini specificati).
5. In **Domini di destinazione** immettere il nome di uno dei domini che si vuole bloccare. Per specificare più domini, immettere ognuno in una nuova riga. Ad esempio:

   ![Mostra l'opzione di negazione con i domini aggiunti](./media/allow-deny-list/DenyListSettings.png)
 
6. Al termine, fare clic su **Salva**.

Dopo aver impostato il criterio, se si prova a invitare un utente da un dominio bloccato, viene visualizzato un messaggio che informa che il dominio dell'utente è attualmente bloccato dai criteri relativi agli inviti.
 
### <a name="add-an-allow-list"></a>Aggiungere un elenco di accesso consentito

Si tratta di una configurazione più restrittiva, in cui è possibile impostare domini specifici nell'elenco di accesso consentito e limitare gli inviti a qualsiasi altro dominio o organizzazione non menzionati. 

Se si vuole usare un elenco di accesso consentito, assicurarsi di dedicare tempo a una valutazione accurata delle esigenze aziendali. Se si creano criteri troppo restrittivi, gli utenti possono scegliere di inviare documenti tramite posta elettronica o trovare altri modi per collaborare non approvati dal reparto IT.


Per aggiungere un elenco di accesso consentito:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory** > **Utenti** > **Impostazioni utente**.
3. In **Utenti esterni** selezionare **Manage external collaboration settings** (Gestisci impostazioni di collaborazione esterna).
4. In **Restrizioni di collaborazione** selezionare **Consenti l'invio di inviti solo ai domini specificati (più restrittivo)** .
5. In **DOMINI DI DESTINAZIONE** immettere il nome di uno dei domini che si vuole consentire. Per specificare più domini, immettere ognuno in una nuova riga. Ad esempio:

   ![Mostra l'opzione Consenti con i domini aggiunti](./media/allow-deny-list/AllowListSettings.png)
 
6. Al termine, fare clic su **Salva**.

Dopo aver impostato il criterio, se si prova a invitare un utente da un dominio che non si trova nell'elenco di accesso consentito, viene visualizzato un messaggio che informa che il dominio dell'utente è attualmente bloccato dai criteri relativi agli inviti.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Passare dall'elenco di accesso consentito a quello di accesso negato e viceversa 

Se si passa da un criterio all'altro, la configurazione del criterio esistente viene rimossa. Assicurarsi di creare un backup dei dettagli della configurazione prima di eseguire il passaggio. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Impostare il criterio per l'elenco di accesso consentito o l'elenco di accesso negato con PowerShell

### <a name="prerequisite"></a>Prerequisito

Per impostare l'elenco di accesso consentito o di accesso negato tramite PowerShell, è necessario installare la versione di anteprima del modulo di Azure Active Directory per Windows PowerShell. In particolare, installare la versione 2.0.0.98 del modulo AzureADPreview o una versione successiva.

Per controllare la versione del modulo e verificare se è installato:
 
1. Aprire Windows PowerShell come utente con privilegi elevati (Esegui come amministratore). 
2. Eseguire il comando seguente per vedere se sono disponibili versioni del modulo di Azure Active Directory per Windows PowerShell installate nel computer:

   ```powershell  
   Get-Module -ListAvailable AzureAD*
   ```

Se il modulo non è installato o non è disponibile una versione richiesta, eseguire una delle operazioni seguenti:

- Se non viene restituito alcun risultato, eseguire il comando seguente per installare la versione più recente del modulo AzureADPreview:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Se nei risultati viene visualizzato solo il modulo AzureAD, eseguire i comandi seguenti per installare il modulo AzureADPreview: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Se nei risultati viene visualizzato solo il modulo AzureADPreview, ma la versione è precedente a 2.0.0.98, eseguire i comandi seguenti per l'aggiornamento: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ```

- Se nei risultati vengono visualizzati entrambi i moduli AzureAD e AzureADPreview, ma la versione del modulo AzureADPreview è precedente a 2.0.0.98, eseguire i comandi seguenti per l'aggiornamento: 

   ```powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ```

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>Usare i cmdlet AzureADPolicy per configurare i criteri

Per creare un elenco di accesso consentito o negato, usare il cmdlet [New-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview). L'esempio seguente illustra come impostare un elenco di accesso negato che blocca il dominio "live.com".

```powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Di seguito è riportato lo stesso esempio, ma con la definizione del criterio inline.

```powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Per impostare il criterio per l'elenco di accesso consentito o negato, usare il cmdlet [Set-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview). Ad esempio:

```powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
```

Per ottenere il criterio, usare il cmdlet [Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview). Ad esempio:

```powershell
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
```

Per rimuovere il criterio, usare il cmdlet [Remove-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview). Ad esempio:

```powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
```

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica di Azure AD B2B, vedere [Informazioni su Collaborazione B2B di Azure AD](what-is-b2b.md)
- Per informazioni su collaborazione B2B e accesso condizionale, vedere [accesso condizionale per gli utenti di collaborazione B2B](conditional-access.md).



