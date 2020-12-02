---
title: Configurare Attestazione di Azure con l'interfaccia della riga di comando di Azure
description: Informazioni su come impostare e configurare un provider di attestazioni con l'interfaccia della riga di comando di Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: dee9e7596c0a30301d9e0453ef22a6dfe9541522
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "96020943"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>Avvio rapido: Configurare Attestazione di Azure con l'interfaccia della riga di comando di Azure

Usare l'interfaccia della riga di comando di Azure per configurare Attestazione di Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="get-started"></a>Introduzione

1. Usare il comando seguente per accedere ad Azure:

   ```azurecli
   az login
   ```

1. Se necessario, passare alla sottoscrizione per Attestazione di Azure:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Registrare il provider di risorse Microsoft.Attestation nella sottoscrizione con il comando [az provider register](/cli/azure/provider#az_provider_register):

   ```azurecli
   az provider register --name Microsoft.Attestation
   ```

   Per altre informazioni sui provider di risorse di Azure e su come configurarli e gestirli, vedere [Provider e tipi di risorse di Azure](../azure-resource-manager/management/resource-providers-and-types.md).

   > [!NOTE]
   > È necessario registrare un provider di risorse una sola volta per una sottoscrizione.

1. Creare un gruppo di risorse per il provider di attestazioni. È possibile inserire altre risorse di Azure, inclusa una macchina virtuale con un'istanza dell'applicazione client, nello stesso gruppo di risorse. Eseguire il comando [az group create](/cli/azure/group#az_group_create) per creare un gruppo di risorse o usarne uno esistente:

   ```azurecli
   az group create --name attestationrg --location uksouth
   ```

## <a name="create-and-manage-an-attestation-provider"></a>Creare e gestire un provider di attestazioni

Ecco i comandi che è possibile usare per creare e gestire il provider di attestazioni:

1. Eseguire il comando [az attestation create](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) per creare un provider di attestazioni:

   ```azurecli
   az attestation create --resource-group attestationrg --name attestationProvider --location uksouth \
      --attestation-policy SgxDisableDebugMode --certs-input-path C:\test\policySignersCertificates.pem
   ```

   Il parametro **--certs-input-path** specifica un set di chiavi di firma attendibili. Se si specifica un nome file per questo parametro, è necessario configurare il provider di attestazioni solo con criteri in formato JWT firmato. In caso contrario, il criterio può essere configurato in formato testo o JWT non firmato. Per informazioni sul formato JWT, vedere [Concetti di base](basic-concepts.md). Per alcuni esempi del certificato, vedere [Esempi di un certificato del firmatario di criteri di attestazione](policy-signer-examples.md).

1. Eseguire il comando [az attestation show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) per recuperare le proprietà del provider di attestazioni, ad esempio lo stato e AttestURI:

   ```azurecli
   az attestation show --resource-group attestationrg --name attestationProvider
   ```

   Questo comando visualizza valori simili all'output seguente:

   ```output
   Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
   Location: MyLocation
   ResourceGroupName: MyResourceGroup
   Name: MyAttestationProvider
   Status: Ready
   TrustModel: AAD
   AttestUri: https://MyAttestationProvider.us.attest.azure.net
   Tags:
   TagsTable:
   ```

È possibile eliminare un provider di attestazioni usando il comando [az attestation delete](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_delete):

```azurecli
az attestation delete --resource-group attestationrg --name attestationProvider
```

## <a name="policy-management"></a>Gestione dei criteri

Per gestire i criteri, gli utenti di Azure AD devono avere le autorizzazioni seguenti per `Actions`:

- `Microsoft.Attestation/attestationProviders/attestation/read`
- `Microsoft.Attestation/attestationProviders/attestation/write`
- `Microsoft.Attestation/attestationProviders/attestation/delete`

Queste autorizzazioni possono essere assegnate a un utente di AD tramite un ruolo come `Owner` (autorizzazioni con caratteri jolly), `Contributor` (autorizzazioni con caratteri jolly) o `Attestation Contributor` (autorizzazioni specifiche solo per Attestazione di Azure).  

Per leggere i criteri, gli utenti di Azure AD devono avere l'autorizzazione seguente per `Actions`:

- `Microsoft.Attestation/attestationProviders/attestation/read`

Questa autorizzazione può essere assegnata a un utente di AD tramite un ruolo come `Reader` (autorizzazioni con caratteri jolly) o `Attestation Reader` (autorizzazioni specifiche solo per Attestazione di Azure).

Usare i comandi descritti qui per fornire la gestione dei criteri per un provider di attestazioni, un ambiente TEE alla volta.

Il comando [az attestation policy show](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) restituisce il criterio corrente per l'ambiente TEE specificato:

```azurecli
az attestation policy show --resource-group attestationrg --name attestationProvider --tee SgxEnclave
```

> [!NOTE]
> Il comando visualizza il criterio sia in formato testo che JWT.

Di seguito sono elencati i tipi di TEE supportati:

- `CyResComponent`
- `OpenEnclave`
- `SgxEnclave`
- `VSMEnclave`

Usare il comando [az attestation policy set](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) per impostare un nuovo criterio per l'ambiente TEE specificato.

```azurecli
az attestation policy set --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --new-attestation-policy newAttestationPolicyname
```

I criteri di attestazione in formato JWT devono contenere un'attestazione denominata `AttestationPolicy`. I criteri firmati devono essere firmati con una chiave che corrisponde a uno dei certificati del firmatario di criteri esistenti.

Per alcuni esempi del criterio, vedere [Esempi di un criterio di attestazione](policy-examples.md).

Il comando [az attestation policy reset](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_reset) imposta un nuovo criterio per l'ambiente TEE specificato.

```azurecli
az attestation policy reset --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --policy-jws "eyJhbGciOiJub25lIn0.."
```

## <a name="policy-signer-certificates-management"></a>Gestione dei certificati del firmatario di criteri

Usare i comandi seguenti per gestire i certificati del firmatario di criteri per un provider di attestazioni:

```azurecli
az attestation signer list --resource-group attestationrg --name attestationProvider

az attestation signer add --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."

az attestation signer remove --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."
```

Il certificato del firmatario di criteri è un token JWT firmato con un'attestazione denominata `maa-policyCertificate`. Il valore dell'attestazione è un token JWK che contiene la chiave di firma attendibile da aggiungere. Il token JWT deve essere firmato con una chiave privata che corrisponde a uno dei certificati del firmatario di criteri esistenti. Per informazioni su JWK e JWK, vedere [Concetti di base](basic-concepts.md).

Tutte le modifiche semantiche del certificato del firmatario di criteri devono essere eseguite all'esterno dell'interfaccia della riga di comando di Azure. Per quanto riguarda l'interfaccia della riga di comando di Azure, si tratta di una semplice stringa.

Per alcuni esempi del certificato, vedere [Esempi di un certificato del firmatario di criteri di attestazione](policy-signer-examples.md).

## <a name="next-steps"></a>Passaggi successivi

- [Come creare e firmare un criterio di attestazione](author-sign-policy.md)
- [Implementare l'attestazione con un'enclave SGX usando esempi di codice](/samples/browse/?expanded=azure&terms=attestation)
