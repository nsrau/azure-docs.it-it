---
title: Abilitare l'identità gestita assegnata dal sistema per l'applicazione Azure Spring Cloud
description: Come abilitare l'identità gestita assegnata dal sistema per l'applicazione.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/13/2020
ms.custom: devx-track-java
ms.openlocfilehash: 1b9d7326ec13176fbe65ba430a8a33bb93a48f74
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091454"
---
# <a name="how-to-enable-system-assigned-managed-identity-for-azure-spring-cloud-application"></a>Come abilitare l'identità gestita assegnata dal sistema per l'applicazione Azure Spring Cloud
Le identità gestite per le risorse di Azure forniscono un'identità gestita automaticamente in Azure Active Directory a una risorsa di Azure, ad esempio l'applicazione Azure Spring cloud. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice.

Questo articolo illustra come abilitare e disabilitare le identità gestite assegnate dal sistema per un'app cloud di Azure Spring usando il portale di Azure e l'interfaccia della riga di comando (disponibile dalla versione 0.2.4).

## <a name="prerequisites"></a>Prerequisiti
Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione Panoramica](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
È necessaria un'istanza distribuita di Azure Spring cloud. Seguire la [Guida introduttiva per la distribuzione tramite l'interfaccia della riga di](spring-cloud-quickstart-launch-app-cli.md)comando di Azure.

## <a name="add-a-system-assigned-identity"></a>Aggiungere un'identità assegnata dal sistema
La creazione di un'app con un'identità assegnata dal sistema richiede l'impostazione di una proprietà aggiuntiva nell'applicazione.

### <a name="using-azure-portal"></a>Uso del portale di Azure
Per configurare un'identità gestita nel [portale di Azure](https://portal.azure.com/), creare prima di tutto un'app e quindi abilitare la funzionalità.

1. Creare un'app nel portale come di consueto. Accedervi nel portale.
2. Scorrere verso il basso fino al gruppo di **Impostazioni** nel riquadro di spostamento a sinistra.
3. Selezionare **Identità**.
4. All'interno della scheda **Assegnata dal sistema** impostare **Stato** su *Attivato*. Fare clic su **Salva**.

 ![Identità gestita nel portale](./media/spring-cloud-managed-identity/identity-1.png)

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
È possibile abilitare l'identità gestita assegnata dal sistema durante la creazione dell'app o in un'app esistente.

**Abilitare l'identità gestita assegnata dal sistema durante la creazione di un'app**

Nell'esempio seguente viene creata un'app denominata *APP_NAME* con un'identità gestita assegnata dal sistema, come richiesto dal `--assign-identity` parametro.

```azurecli
az spring-cloud app create -n app_name -s service_name -g resource_group_name --assign-identity
```

**Abilitare l'identità gestita assegnata dal sistema in un'app esistente** Usare `az spring-cloud app identity assign` il comando per abilitare l'identità assegnata dal sistema in un'app esistente.

```azurecli
az spring-cloud app identity assign -n app_name -s service_name -g resource_group_name
```

## <a name="obtain-tokens-for-azure-resources"></a>Ottenere i token per le risorse di Azure
Un'app può usare la propria identità gestita per ottenere i token per accedere ad altre risorse protette da Azure Active Directory, ad esempio Azure Key Vault. Questi token rappresentano l'applicazione che accede alla risorsa, non qualsiasi utente specifico dell'applicazione.

Potrebbe essere necessario [configurare la risorsa di destinazione per consentire l'accesso dall'applicazione](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/howto-assign-access-portal). Ad esempio, se si richiede un token per accedere a Key Vault, assicurarsi di avere aggiunto un criterio di accesso che includa l'identità dell'applicazione. In caso contrario, le chiamate a Key Vault verranno rifiutate, anche se includono il token. Per altre informazioni sulle risorse che supportano i token di Azure Active Directory, vedere [Servizi di Azure che supportano l'autenticazione di Azure AD](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-azure-ad-authentication).

Il cloud Spring di Azure condivide lo stesso endpoint per l'acquisizione di token con la macchina virtuale di Azure. Per acquisire un token, è consigliabile usare Java SDK o i principianti di Spring boot.  Vedere [come usare il token VM](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token) per diversi esempi di codice e script e istruzioni su argomenti importanti, ad esempio la gestione di errori HTTP e di scadenza dei token.

Consigliato: usare Java SDK o i principianti di Spring boot per ottenere i token.  Vedere gli esempi nei [passaggi successivi](#next-steps).

## <a name="disable-system-assigned-identity-from-an-app"></a>Disabilitare l'identità assegnata dal sistema da un'app
La rimozione di un'identità assegnata dal sistema lo eliminerà anche dal Azure AD. L'eliminazione della risorsa dell'app rimuove automaticamente le identità assegnate dal sistema dal Azure AD.

### <a name="using-azure-portal"></a>Uso del portale di Azure
Per rimuovere l'identità gestita assegnata dal sistema da un'app che non è più necessaria:

1. Accedere al [portale di Azure](https://portal.azure.com/) usando un account associato alla sottoscrizione di Azure che contiene l'istanza di Azure Spring cloud.
1. Passare alla macchina virtuale desiderata e selezionare **Identità**.
1. In **stato assegnato al sistema** / **Status**selezionare **disattivato** , quindi fare clic su **Salva**:

 ![Identità gestita nel portale](./media/spring-cloud-managed-identity/remove-identity.png)

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
Per rimuovere l'identità gestita assegnata dal sistema da un'app che non è più necessaria, usare il comando seguente:
```azurecli
az spring-cloud app identity remove -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>Passaggi successivi
* [Come usare le identità gestite con Java SDK](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)
* [Accedere a Azure Key Vault con identità gestite in Spring boot Starter](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets/README.md#use-msi--managed-identities)
* [Altre informazioni sulle identità gestite per le risorse di Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)

