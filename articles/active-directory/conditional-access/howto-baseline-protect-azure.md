---
title: I criteri di base richiedono l'autenticazione a più fattori per la gestione dei servizi (anteprima)-Azure Active Directory
description: Criteri di accesso condizionale per richiedere l'autenticazione a più fattori per Azure Resource Manager
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: aab2aa4415345747a0e87b90ef0a7ee770ef3465
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608130"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>Criteri di base: Richiedi autenticazione a più fattori per la gestione dei servizi (anteprima)

È possibile che si stia usando un'ampia gamma di servizi di Azure nell'organizzazione. Questi servizi possono essere gestiti tramite Azure Resource Manager API:

* Portale di Azure
* Azure PowerShell
* Interfaccia della riga di comando di Azure

L'uso di Azure Resource Manager per gestire i servizi è un'azione con privilegi elevati. Azure Resource Manager possibile modificare le configurazioni a livello di tenant, ad esempio le impostazioni del servizio e la fatturazione della sottoscrizione. L'autenticazione a fattore singolo è vulnerabile a una serie di attacchi come phishing e spray per la password. È quindi importante verificare l'identità degli utenti che vogliono accedere alle configurazioni di Azure Resource Manager e aggiornamento, richiedendo l'autenticazione a più fattori prima di consentire l'accesso.

**Richiedi** autenticazione a più fattori per la gestione dei servizi è un [criterio di base](concept-baseline-protection.md) che richiede l'autenticazione a più fattori per tutti gli utenti che accedono a portale di Azure, Azure PowerShell o CLI di Azure. Questo criterio si applica a tutti gli utenti che accedono a Azure Resource Manager, indipendentemente dal fatto che si tratti di un amministratore.

Quando questo criterio è abilitato in un tenant, tutti gli utenti che accedono alle risorse di gestione di Azure verranno verificati con multi-factor authentication. Se l'utente non è registrato per l'autenticazione a più fattori, l'utente dovrà eseguire la registrazione usando l'app Microsoft Authenticator per continuare.

Per eseguire l'accesso interattivo con [Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps), usare il cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

```PowerShell
Connect-AzAccount
```

Quando viene eseguito, questo cmdlet visualizzerà una stringa del token. Per accedere, copiare questa stringa e incollarla [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)  in un browser. La sessione di PowerShell verrà autenticata per connettersi ad Azure.

Per eseguire l'accesso interattivo usando l' [interfaccia](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)della riga di comando di Azure, eseguire il comando [AZ login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) .

```azurecli
az login
```

Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso. In caso contrario, è necessario aprire una pagina del browser e seguire le istruzioni nella riga di comando per immettere un codice di autorizzazione dopo [https://aka.ms/devicelogin](https://aka.ms/devicelogin) lo spostamento nel browser. Successivamente, accedere con le credenziali dell'account nel browser.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

Poiché il criterio Richiedi autenticazione a più fattori **per la gestione dei servizi** si applica a tutti gli utenti di Azure Resource Manager, è necessario tenere presenti alcune considerazioni per garantire una distribuzione uniforme. Queste considerazioni includono l'identificazione degli utenti e dei principi di servizio in Azure AD che non possono o non devono eseguire l'autenticazione a più fattori, nonché le applicazioni e i client usati dall'organizzazione che non supportano l'autenticazione moderna.

## <a name="enable-the-baseline-policy"></a>Abilitare i criteri di base

Criteri di **base dei criteri: Richiedi autenticazione a più fattori per gestione servizi** (anteprima) è preconfigurata e verrà visualizzata nella parte superiore quando si passa al pannello accesso condizionale in portale di Azure.

Per abilitare questo criterio e proteggere gli amministratori:

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare a **Azure Active Directory** > **accesso condizionale**.
1. Nell'elenco dei criteri selezionare **criteri di base: Richiedi autenticazione a più fattori per la gestione**dei servizi (anteprima).
1. Impostare **Abilita criterio** per **usare immediatamente i criteri**.
1. Fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere:

* [Criteri di protezione di base per l'accesso condizionale](concept-baseline-protection.md)
* [Cinque passaggi per proteggere l'infrastruttura di identità](../../security/fundamentals/steps-secure-identity.md)
* [Che cos'è l'accesso condizionale in Azure Active Directory?](overview.md)