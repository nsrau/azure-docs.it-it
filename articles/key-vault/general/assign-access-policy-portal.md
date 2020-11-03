---
title: Assegnare un criterio di accesso Azure Key Vault (portale)
description: Come usare il portale di Azure per assegnare un criterio di accesso Key Vault a un'entità servizio o a un'identità di applicazione.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 9277d100eb6744adfc20fbba9f96b822654e64a3
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287702"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Assegnare un criterio di accesso Key Vault usando il portale di Azure

Un criterio di accesso Key Vault determina se una determinata entità servizio, ovvero un'applicazione o un gruppo di utenti, può eseguire diverse operazioni su Key Vault [segreti](../secrets/index.yml), [chiavi](../keys/index.yml)e [certificati](../certificates/index.yml). È possibile assegnare i criteri di accesso usando il portale di Azure (questo articolo), l'interfaccia della riga di comando di [Azure](assign-access-policy-cli.md)o [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Per ulteriori informazioni sulla creazione di gruppi in Azure Active Directory tramite portale di Azure, vedere [creare un gruppo di base e aggiungere membri](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

## <a name="assign-an-access-policy"></a>Assegnare un criterio di accesso

1.  Nella [portale di Azure](https://portal.azure.com)passare alla risorsa key Vault. 

1.  In **Impostazioni** selezionare **criteri di accesso** , quindi selezionare **Aggiungi criteri di accesso** :

    ![Selezionare criteri di accesso e selezionare Aggiungi assegnazione ruolo](../media/authentication/assign-policy-portal-01.png)

1.  Selezionare le autorizzazioni desiderate in autorizzazioni **certificato** , **autorizzazioni chiave** e **autorizzazioni segrete**. È anche possibile selezionare un modello che contiene combinazioni di autorizzazioni comuni:

    ![Specifica delle autorizzazioni dei criteri di accesso](../media/authentication/assign-policy-portal-02.png)

1. In **Seleziona entità** scegliere il collegamento **Nessuno selezionato** per aprire il riquadro di selezione **principale** . Immettere il nome dell'app o dell'entità servizio nel campo di ricerca, selezionare il risultato appropriato, quindi scegliere **Seleziona**.

    ![Selezione dell'entità servizio per i criteri di accesso](../media/authentication/assign-policy-portal-03.png)

    Se si usa un'identità gestita per l'app, cercare e selezionare il nome dell'app stessa. Per altre informazioni sulle identità gestite e sulle entità servizio, vedere [Key Vault autenticazione-identità dell'app e entità servizio](authentication.md#app-identity-and-security-principals).
 
1.  Tornare al riquadro **Aggiungi criteri di accesso** e selezionare **Aggiungi** per salvare i criteri di accesso.

    ![Aggiunta dei criteri di accesso con l'entità servizio assegnata](../media/authentication/assign-policy-portal-04.png)

1. Tornare alla pagina **criteri di accesso** , verificare che il criterio di accesso sia elencato in **criteri di accesso correnti** , quindi selezionare **Salva**. I criteri di accesso non vengono applicati fino a quando non vengono salvati.

    ![Salvataggio delle modifiche ai criteri di accesso](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>Passaggi successivi

- [Sicurezza di Azure Key Vault: Gestione delle identità e dell'accesso](overview-security.md#identity-and-access-management)
- [Proteggere l'](secure-your-key-vault.md)insieme di credenziali delle chiavi.
- [Guida per gli sviluppatori per Azure Key Vault](developers-guide.md)
- [Procedure consigliate per Azure Key Vault](best-practices.md)