---
title: Configurare l'accesso Azure Active Directory-servizio Azure blockchain
description: Come configurare il servizio Azure blockchain con accesso Azure Active Directory
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 682ab282036fcd592e66942d08a84cdce46d8915
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455853"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Come configurare l'accesso Azure Active Directory per il servizio Azure blockchain

Questo articolo illustra come concedere l'accesso e connettersi ai nodi del servizio blockchain di Azure usando gli ID utente, gruppo o applicazione Azure Active Directory (Azure AD).

Azure AD offre la gestione delle identità basata sul cloud e consente di usare una singola identità in un'intera azienda e accedere alle applicazioni in Azure. Il servizio Azure blockchain è integrato con Azure AD e offre vantaggi come federazione ID, Single Sign-On e autenticazione a più fattori.

## <a name="prerequisites"></a>prerequisiti

* [Creare un membro blockchain usando il portale di Azure](create-member.md)

## <a name="grant-access"></a>Concedere l'accesso

È possibile concedere l'accesso a livello di membro e a livello di nodo. La concessione dei diritti di accesso a livello di membro consentirà a sua volta l'accesso a tutti i nodi del membro.

### <a name="grant-member-level-access"></a>Concedi accesso a livello di membro

Per concedere l'autorizzazione di accesso a livello di membro.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a **controllo di accesso (IAM) > aggiungi > Aggiungi assegnazione ruolo**.
1. Selezionare il ruolo di **accesso al nodo membro blockchain (anteprima)** e aggiungere l'oggetto ID Azure ad a cui si vuole concedere l'accesso. Azure AD oggetto ID può essere:

    | Oggetto Azure AD | Esempio |
    |-----------------|---------|
    | Utente Azure AD   | `kim@contoso.onmicrosoft.com` |
    | Gruppo di Azure AD  | `sales@contoso.onmicrosoft.com` |
    | ID applicazione  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Aggiungi un'assegnazione di ruolo](./media/configure-aad/add-role-assignment.png)

1. Selezionare **Salva**.

### <a name="grant-node-level-access"></a>Concessione dell'accesso a livello di nodo

È possibile concedere l'accesso a livello di nodo passando alla sicurezza del nodo e facendo clic sul nome del nodo a cui si vuole concedere l'accesso.

Selezionare il ruolo di accesso al nodo membro blockchain (anteprima) e aggiungere l'oggetto ID Azure AD a cui si vuole concedere l'accesso.

Per altre informazioni, vedere [configurare i nodi delle transazioni del servizio blockchain di Azure](configure-transaction-nodes.md#azure-active-directory-access-control).

## <a name="connect-using-azure-blockchain-connector"></a>Connettersi con Azure blockchain Connector

Scaricare o clonare il [connettore Azure blockchain da GitHub](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

La sezione seguire la Guida introduttiva nel **file Leggimi** per compilare il connettore dal codice sorgente.

### <a name="connect-using-an-azure-ad-user-account"></a>Connettersi usando un account utente Azure AD

1. Eseguire il comando seguente per eseguire l'autenticazione con un account utente Azure AD. Sostituire \<\> myAADDirectory con un dominio Azure AD. Ad esempio: `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD richiede le credenziali.
1. Accedere con il nome utente e la password.
1. Al completamento dell'autenticazione, il proxy locale si connette al nodo blockchain. È ora possibile associare il client di Geth con l'endpoint locale.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Connetti usando un ID applicazione

Molte applicazioni eseguono l'autenticazione con Azure AD usando un ID applicazione anziché un account utente Azure AD.

Per connettersi al nodo usando un ID applicazione, sostituire **aadauthcode** con **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| . | DESCRIZIONE |
|-----------|-------------|
| ID tenant | Azure AD dominio, ad esempio `yourdomain.onmicrosoft.com`
| ID client | ID client dell'applicazione registrata in Azure AD
| segreto client | Segreto client dell'applicazione registrata in Azure AD

Per altre informazioni su come registrare un'applicazione in Azure AD, vedere [procedura: usare il portale per creare un'applicazione Azure ad e un'entità servizio che possano accedere alle risorse](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Connettere un dispositivo mobile o un browser di testo

Per un dispositivo mobile o un browser basato su testo in cui non è possibile visualizzare la visualizzazione popup dell'autenticazione Azure AD, Azure AD genera un solo codice di accesso. È possibile copiare il codice e procedere con l'autenticazione Azure AD in un altro ambiente.

Per generare il codice, sostituire **aadauthcode** con **aaddevice**. Sostituire \<\> myAADDirectory con un dominio Azure AD. Ad esempio: `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla sicurezza dei dati nel servizio Azure blockchain, vedere la pagina relativa alla [sicurezza del servizio blockchain di Azure](data-security.md).