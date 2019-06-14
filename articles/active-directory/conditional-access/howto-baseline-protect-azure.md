---
title: Criterio di base per richiedere il MFA per la gestione dei servizi (anteprima) - Azure Active Directory
description: Criteri di accesso condizionale per richiedere l'autenticazione MFA per Azure Resource Manager
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
ms.openlocfilehash: 24b54a3645fe97903219841dd148c0942dfcda76
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112375"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>Criterio di base: Richiedere l'autenticazione MFA per la gestione dei servizi (anteprima)

Si stia utilizzando un'ampia gamma di servizi di Azure all'interno dell'organizzazione. Questi servizi possono essere gestiti tramite l'API Azure Resource Manager:

* Portale di Azure
* Azure PowerShell
* Interfaccia della riga di comando di Azure

Tramite Azure Resource Manager per gestire i servizi è un'azione con privilegiata elevati. Azure Resource Manager può modificare le configurazioni a livello di tenant, ad esempio le impostazioni del servizio e la fatturazione di sottoscrizione. Autenticazione a fattore singolo è vulnerabile a numerosi attacchi, ad esempio spray phishing e la password. Pertanto, è importante verificare l'identità degli utenti che desiderano accedere a Azure Resource Manager e aggiornare le configurazioni, richiedendo l'autenticazione a più fattori prima di consentire l'accesso.

**Richiedere l'autenticazione MFA per la gestione dei servizi** è un [criterio di base](concept-baseline-protection.md) che richiede MFA per qualsiasi utente che accede al portale di Azure, Azure PowerShell o CLI di Azure. Questo criterio si applica a tutti gli utenti l'accesso a Azure Resource Manager, indipendentemente se si è amministratore.

Dopo aver abilitato questo criterio in un tenant, verranno visualizzata una richiesta tutti gli utenti che accedono alle risorse di gestione di Azure multi-factor Authentication. Se l'utente non è registrato per MFA, l'utente sarà necessario registrare con l'App Microsoft Authenticator per continuare.

![Richiedere l'autenticazione MFA per Azure Resource Manager](./media/howto-baseline-protect-azure/baseline-policy-require-mfa-for-service-management.png)

Per eseguire interactive sign-in usando [Azure Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps), utilizzare il [Connect AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet.

```PowerShell
Connect-AzAccount
```

Quando viene eseguito, questo cmdlet visualizzerà una stringa del token. Per accedere, copiare questa stringa e incollarla [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)  in un browser. La sessione di PowerShell verrà autenticata per connettersi ad Azure.

Per eseguire interactive sign-in usando [Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest), eseguire il [login az](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) comando.

```azurecli
az login
```

Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso. In caso contrario, è necessario aprire una pagina del browser e seguire le istruzioni nella riga di comando per immettere un codice di autorizzazione dopo l'accesso alla [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin) nel browser. Successivamente, accedere con le credenziali dell'account nel browser.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

Poiché il **Richiedi autenticazione a più fattori per la gestione dei servizi** criteri si applicano a tutti gli utenti di Azure Resource Manager, è necessario apportare per garantire una regolare distribuzione diverse considerazioni. Queste considerazioni includono l'identificazione degli utenti e oggetti entità servizio in Azure AD che non è possibile o non devono eseguire MFA, nonché applicazioni e client usati dall'organizzazione che non supportano l'autenticazione moderna.

### <a name="user-exclusions"></a>Esclusioni di utente

Questo criterio di base offre la possibilità di escludere gli utenti. Prima di abilitare i criteri per il tenant, è consigliabile escludere i seguenti account:

* **Accesso di emergenza** oppure **emergenza** gli account per evitare il blocco degli account a livello di tenant. Nello scenario improbabile che tutti gli amministratori sono bloccati fuori dal tenant, l'account amministrativo di accesso di emergenza è utilizzabile per accedere alla procedura per ripristinare l'accesso ed esami del tenant.
   * Altre informazioni sono reperibili nell'articolo [gestire gli account di accesso di emergenza in Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Gli account del servizio** e **principi del servizio**, ad esempio la sincronizzazione Account connettere Azure AD. Account del servizio sono account non interattivi che non sono associate ad alcun utente specifico. In genere vengono usati dai servizi back-end e consentire l'accesso a livello di codice alle applicazioni. Gli account del servizio devono essere escluse perché Impossibile completare l'autenticazione a più fattori a livello di codice.
   * Se l'organizzazione dispone di questi account in uso nel codice o gli script, è consigliabile sostituirli con [gestito identità](../managed-identities-azure-resources/overview.md). Come soluzione alternativa temporanea, è possibile escludere questi account specifici dai criteri di base.
* Utenti che non dispongono o non sarà in grado di usare uno Smartphone.
   * Questo criterio richiede agli utenti di registrarsi per MFA usando l'app Microsoft Authenticator.

## <a name="enable-the-baseline-policy"></a>Abilitare il criterio di base

I criteri **criterio di base: Richiedere l'autenticazione MFA per la gestione dei servizi (anteprima)** è preconfigurata e verrà visualizzata nella parte superiore quando si passa al pannello di accesso condizionale nel portale di Azure.

Per abilitare questo criterio e proteggere gli amministratori:

1. Accedi per il **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.
1. Passare a **Azure Active Directory** > **accesso condizionale**.
1. Nell'elenco dei criteri, selezionare **criterio di base: Richiedere l'autenticazione MFA per la gestione dei servizi (anteprima)** .
1. Impostare **abilitare i criteri** al **Usa i criteri immediatamente**.
1. Aggiungere eventuali esclusioni utente facendo clic su **gli utenti** > **selezionare gli utenti esclusi** e scegliendo gli utenti che devono essere escluse. Fare clic su **selezionate** quindi **eseguita**.
1. Fare clic su **salvare**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere:

* [Criteri di protezione della linea di base di accesso condizionale](concept-baseline-protection.md)
* [Cinque passaggi per proteggere l'infrastruttura di identità](../../security/azure-ad-secure-steps.md)
* [Che cos'è l'accesso condizionale in Azure Active Directory?](overview.md)