---
title: Come configurare l'accesso di Azure Active Directory
description: Come configurare il servizio di Azure Blockchain con accesso di Azure Active Directory
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: seal
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 616e342f1d52179c40c225c5dafc9de13ce85e06
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028216"
---
# <a name="how-to-configure-azure-active-directory-access"></a>Come configurare l'accesso di Azure Active Directory

In questo articolo descrive come concedere l'accesso e connettersi ai nodi di Azure Blockchain Service usando Azure Active Directory (Azure AD) utente, gruppo o gli ID applicazione.

Azure AD fornisce la gestione delle identità basato sul cloud e consente di usare una singola identità tra un intero le applicazioni dell'organizzazione e accesso in Azure. Azure Blockchain Service è integrato con Azure AD e garantisce vantaggi quali federation ID, l'autenticazione single sign-on e a più fattori.

## <a name="prerequisites"></a>Prerequisiti

* [Creare un membro di blockchain usando il portale di Azure](create-member.md)

## <a name="grant-access"></a>Concedere l'accesso

È possibile concedere l'accesso a livello di membro e il livello del nodo. Concessione dei diritti di accesso a livello di membro verrà a sua volta concedere l'accesso a tutti i nodi sotto il membro.

### <a name="grant-member-level-access"></a>Concedere l'accesso a livello di membro

Per concedere l'autorizzazione di accesso a livello di membro.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a **controllo di accesso (IAM) > Aggiungi > Aggiungi l'assegnazione di ruolo**.
1. Selezionare il **Blockchain membro nodo accesso (anteprima)** ruolo e aggiungere l'oggetto ID di Azure AD che si desidera concedere l'accesso a. Oggetto ID AD Azure può essere:

    | Oggetto Azure AD | Esempio |
    |-----------------|---------|
    | Utente di Azure AD   | `frank@contoso.onmicrosoft.com` |
    | Gruppo di Azure Active Directory  | `sales@contoso.onmicrosoft.com` |
    | ID applicazione  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Aggiungi un'assegnazione di ruolo](./media/configure-aad/add-role-assignment.png)

1. Selezionare **Salva**.

### <a name="grant-node-level-access"></a>Concedere l'accesso a livello di nodo

1. È possibile concedere l'accesso a livello di nodo passando alla sicurezza da nodo e fare clic sul nome del nodo che si vuole concedere l'accesso.
1. Selezionare il ruolo di accesso al nodo membro Blockchain (anteprima) e aggiungere l'oggetto ID di Azure AD che si desidera concedere l'accesso a. 

## <a name="connect-using-azure-blockchain-connector"></a>Connettersi tramite connettore di Azure Blockchain

Scaricare o clonare la [connettore di Blockchain di Azure da GitHub](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

La seguente sezione Guida introduttiva il **Leggimi** per creare il connettore dal codice sorgente.

### <a name="connect-using-an-azure-ad-user-account"></a>Connettersi usando un account utente di Azure AD

1. Eseguire il comando seguente per eseguire l'autenticazione usando un account utente di Azure AD. Sostituire \<myAADDirectory\> con un dominio di Azure AD. Ad esempio: `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD richiede le credenziali.
1. Accedere con il nome utente e password.
1. Al termine dell'autenticazione, il proxy locale si connette al nodo blockchain. È ora possibile collegare il client Geth con l'endpoint locale.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Connettersi usando un ID applicazione

Molte applicazioni l'autenticazione con Azure AD usando un ID applicazione invece di un account utente di Azure AD.

Per connettersi al nodo usando un ID applicazione, sostituire **aadauthcode** con **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parametro | Descrizione |
|-----------|-------------|
| tenant-id | Dominio di Azure AD, ad esempio, `yourdomain.onmicrosoft.com`
| client-id | ID client dell'applicazione registrato in Azure AD
| client-secret | Segreto client dell'applicazione registrata in Azure AD

Per altre informazioni su come registrare un'applicazione in Azure AD, vedere [come: Usare il portale per creare un'entità servizio e applicazione di Azure AD che possano accedere alle risorse](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Connettere un dispositivo o il testo di browser per dispositivi mobili

Per un dispositivo per dispositivi mobili o browser basato su testo in cui non è possibile la visualizzazione di popup di autenticazione di Azure AD, Azure AD genera un passcode monouso. È possibile copiare il passcode e procedere con l'autenticazione di Azure AD in un altro ambiente.

Per generare il passcode, sostituire **aadauthcode** con **aaddevice**. Sostituire \<myAADDirectory\> con un dominio di Azure AD. Ad esempio: `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla sicurezza dei dati nel servizio di Blockchain di Azure, vedere:

> [!div class="nextstepaction"]
> [Sicurezza di Azure Blockchain Service](data-security.md)