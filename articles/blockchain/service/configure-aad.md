---
title: Configurare l'accesso ad Azure Active Directory - Servizio Blockchain di AzureConfigure Azure Active Directory access - Azure Blockchain Service
description: Come configurare il servizio Blockchain di Azure con l'accesso ad Azure Active DirectoryHow to configure Azure Blockchain Service with Azure Active Directory access
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 682ab282036fcd592e66942d08a84cdce46d8915
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455853"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Come configurare l'accesso ad Azure Active Directory per il servizio Blockchain di AzureHow to configure Azure Active Directory access for Azure Blockchain Service

In questo articolo viene illustrato come concedere l'accesso e connettersi ai nodi del servizio Blockchain di Azure usando gli ID utente, gruppo o applicazione di Azure Active Directory (Azure AD).

Azure AD provides cloud-based identity management and allows you to use a single identity across an entire enterprise and access applications in Azure. Il servizio Blockchain di Azure è integrato con Azure AD e offre vantaggi quali la federazione degli ID, l'accesso Single Sign-On e l'autenticazione a più fattori.

## <a name="prerequisites"></a>Prerequisiti

* [Creare un membro blockchain tramite il portale di AzureCreate a blockchain member using the Azure portal](create-member.md)

## <a name="grant-access"></a>Concedere l'accesso

È possibile concedere l'accesso sia a livello di membro che a livello di nodo. La concessione dei diritti di accesso a livello di membro concederà a sua volta l'accesso a tutti i nodi sotto il membro.

### <a name="grant-member-level-access"></a>Concedere l'accesso a livello di membroGrant member level access

Per concedere l'autorizzazione di accesso a livello di membro.

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Passare a **Controllo di accesso (IAM) > Aggiungi >'assegnazione**di ruolo Aggiungi.
1. Selezionare il ruolo **Blockchain Member Node Access (Preview)** e aggiungere l'oggetto ID di Azure AD a cui si desidera concedere l'accesso. L'oggetto ID di Azure AD può essere:Azure AD ID object can be:

    | Oggetto Azure ADAzure AD object | Esempio |
    |-----------------|---------|
    | Utente di Azure AD   | `kim@contoso.onmicrosoft.com` |
    | Gruppo di Azure ADAzure AD group  | `sales@contoso.onmicrosoft.com` |
    | ID applicazione  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Aggiungi un'assegnazione di ruolo](./media/configure-aad/add-role-assignment.png)

1. Selezionare **Salva**.

### <a name="grant-node-level-access"></a>Concedere l'accesso a livello di nodoGrant node level access

È possibile concedere l'accesso a livello di nodo passando alla sicurezza del nodo e facendo clic sul nome del nodo a cui si desidera concedere l'accesso.

Selezionare il ruolo Blockchain Member Node Access (Preview) e aggiungere l'oggetto ID di Azure AD a cui si desidera concedere l'accesso.

Per altre informazioni, vedere [Configurare i nodi di transazione del servizio Blockchain](configure-transaction-nodes.md#azure-active-directory-access-control)di Azure.For more information, see Configure Azure Blockchain Service transaction nodes .

## <a name="connect-using-azure-blockchain-connector"></a>Connettersi usando il connettore Blockchain di AzureConnect using Azure Blockchain Connector

Scaricare o clonare il connettore Blockchain di [Azure da GitHub](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

La sezione di avvio rapido nel **file Leggimi** per compilare il connettore dal codice sorgente.

### <a name="connect-using-an-azure-ad-user-account"></a>Connettersi usando un account utente di Azure ADConnect using an Azure AD user account

1. Eseguire il comando seguente per eseguire l'autenticazione usando un account utente di Azure AD. Sostituire \<myAADDirectory\> con un dominio di Azure AD. Ad esempio: `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD richiede le credenziali.
1. Accedere con il nome utente e la password.
1. Dopo l'autenticazione, il proxy locale si connette al nodo blockchain. È ora possibile collegare il client Geth all'endpoint locale.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Connettersi utilizzando un ID applicazioneConnect using an application ID

Molte applicazioni eseguono l'autenticazione con Azure AD usando un ID applicazione anziché un account utente di Azure AD.

Per connettersi al nodo utilizzando un ID applicazione, sostituire **aadauthcode** con **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parametro | Descrizione |
|-----------|-------------|
| tenant-id | Dominio di Azure AD, ad esempio,Azure AD domain, For example,`yourdomain.onmicrosoft.com`
| id client | ID client dell'applicazione registrata in Azure AD
| segreto client | Client secret of the registered application in Azure AD

Per altre informazioni su come registrare un'applicazione in Azure AD, vedere [Procedura: Usare il portale per creare un'applicazione Azure AD e un'entità](../../active-directory/develop/howto-create-service-principal-portal.md) servizio che può accedere alle risorseFor more information on how to register an application in Azure AD, see How to: Use the portal to create an Azure AD application and service principal that can access resources

### <a name="connect-a-mobile-device-or-text-browser"></a>Connettere un dispositivo mobile o un browser di testo

Per un dispositivo mobile o un browser basato su testo in cui la visualizzazione popup di autenticazione di Azure AD non è possibile, Azure AD genera un passcode monouso. È possibile copiare il passcode e procedere con l'autenticazione di Azure AD in un altro ambiente.

Per generare il passcode, sostituire **aadauthcode** con **aaddevice**. Sostituire \<myAADDirectory\> con un dominio di Azure AD. Ad esempio: `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla sicurezza dei dati nel servizio Blockchain di Azure, vedere Sicurezza del servizio Blockchain di Azure.For more information about data security in Azure Blockchain Service, see [Azure Blockchain Service security](data-security.md).