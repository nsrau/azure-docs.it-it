---
title: Usare chiavi gestite dal cliente per crittografare i dati di configurazione
description: Crittografare i dati di configurazione usando chiavi gestite dal cliente
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/18/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 8942c93b7346613b8cfdc97d9afe09f1c473fb10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87384872"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>Usare chiavi gestite dal cliente per crittografare i dati di configurazione dell'app
App Azure configurazione [Crittografa le informazioni riservate](../security/fundamentals/encryption-atrest.md)inattive. L'uso delle chiavi gestite dal cliente offre protezione avanzata dei dati consentendo di gestire le chiavi di crittografia.  Quando si usa la crittografia con chiave gestita, tutte le informazioni riservate nella configurazione dell'app vengono crittografate con una chiave di Azure Key Vault fornita dall'utente.  Questa funzionalità consente di ruotare la chiave di crittografia su richiesta.  Consente inoltre di revocare l'accesso app Azure configurazione alle informazioni riservate revocando l'accesso dell'istanza di configurazione dell'app alla chiave.

## <a name="overview"></a>Panoramica 
App Azure configurazione crittografa le informazioni riservate inattive usando una chiave di crittografia AES a 256 bit fornita da Microsoft. Ogni istanza di configurazione dell'app ha una propria chiave di crittografia gestita dal servizio e usata per crittografare le informazioni riservate. Le informazioni riservate includono i valori disponibili nelle coppie chiave-valore.  Quando è abilitata la funzionalità della chiave gestita dal cliente, la configurazione dell'app usa un'identità gestita assegnata all'istanza di configurazione dell'app per l'autenticazione con Azure Active Directory. L'identità gestita chiama quindi Azure Key Vault e esegue il wrapping della chiave di crittografia dell'istanza di configurazione dell'app. La chiave di crittografia di cui è stato eseguito il wrapped viene quindi archiviata e la chiave di crittografia di cui è stato eseguito il wrapped viene memorizzata nella cache La configurazione dell'app Aggiorna la chiave di crittografia dell'istanza di configurazione dell'app per ogni ora. In questo modo si garantisce la disponibilità in condizioni operative normali. 

>[!IMPORTANT]
> Se l'identità assegnata all'istanza di configurazione dell'app non è più autorizzata a annullare il wrapping della chiave di crittografia dell'istanza o se la chiave gestita viene eliminata definitivamente, non sarà più possibile decrittografare le informazioni riservate archiviate nell'istanza di configurazione dell'app. L'uso della funzione di [eliminazione](../key-vault/general/soft-delete-overview.md) temporanea di Azure Key Vault attenua la possibilità di eliminare accidentalmente la chiave di crittografia.

Quando gli utenti abilitano la funzionalità della chiave gestita dal cliente nell'istanza di configurazione app Azure, controllano la capacità del servizio di accedere alle informazioni riservate. La chiave gestita funge da chiave di crittografia radice. Un utente può revocare l'accesso dell'istanza di configurazione dell'app alla chiave gestita modificando i criteri di accesso dell'insieme di credenziali delle chiavi. Quando questo accesso viene revocato, la configurazione dell'app perderà la possibilità di decrittografare i dati utente entro un'ora. A questo punto, l'istanza di configurazione dell'app vieterà tutti i tentativi di accesso. Questa situazione è reversibile concedendo di nuovo l'accesso al servizio alla chiave gestita.  Entro un'ora, la configurazione dell'app sarà in grado di decrittografare i dati utente e operare in condizioni normali.

>[!NOTE]
>Tutti i dati di configurazione app Azure vengono archiviati per un massimo di 24 ore in un backup isolato. Include la chiave di crittografia Unwrapped. Questi dati non sono immediatamente disponibili per il servizio o il team del servizio. In caso di ripristino di emergenza, app Azure configurazione si rirevocherà dai dati della chiave gestita.

## <a name="requirements"></a>Requisiti
Per abilitare correttamente la funzionalità della chiave gestita dal cliente per la configurazione di app Azure, sono necessari i componenti seguenti:
- Istanza di configurazione app Azure livello standard
- Azure Key Vault con funzionalità di eliminazione e ripulitura flessibili abilitate
- Una chiave RSA o RSA-HSM all'interno dell'Key Vault
    - La chiave non deve essere scaduta, deve essere abilitata ed è necessario che siano abilitate entrambe le funzionalità wrap e unwrap

Una volta configurate queste risorse, restano due passaggi per consentire alla configurazione di app Azure di usare la chiave di Key Vault:
1. Assegnare un'identità gestita all'istanza di configurazione app Azure
2. Concedere l'identità `GET` , `WRAP` , e le `UNWRAP` autorizzazioni nei criteri di accesso del Key Vault di destinazione.

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Abilitare la crittografia della chiave gestita dal cliente per l'istanza di configurazione app Azure
Per iniziare, sarà necessario disporre di un'istanza di configurazione app Azure configurata correttamente. Se non è ancora disponibile un'istanza di configurazione dell'app, seguire una di queste guide introduttive per configurarne una:
- [Creare un'app ASP.NET Core con la configurazione app Azure](quickstart-aspnet-core-app.md)
- [Creare un'app .NET Core con la configurazione di app Azure](quickstart-dotnet-core-app.md)
- [Creare un'app .NET Framework con Configurazione app di Azure](quickstart-dotnet-app.md)
- [Creare un'app Java Spring con Configurazione app di Azure](quickstart-java-spring-app.md)

>[!TIP]
> Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire le istruzioni della riga di comando riportate in questo articolo.  Include strumenti comuni di Azure preinstallati, tra cui .NET Core SDK. Se è stato eseguito l'accesso alla sottoscrizione di Azure, avviare [Azure Cloud Shell](https://shell.azure.com) da shell.azure.com.  Per altre informazioni su Azure Cloud Shell, [leggere la documentazione](../cloud-shell/overview.md)

### <a name="create-and-configure-an-azure-key-vault"></a>Creare e configurare un Azure Key Vault
1. Creare una Azure Key Vault usando l'interfaccia della riga di comando di Azure.  Si noti che `vault-name` e `resource-group-name` sono forniti dall'utente e devono essere univoci.  Usiamo `contoso-vault` e `contoso-resource-group` in questi esempi.

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. Abilitare l'eliminazione temporanea e la ripulitura-protezione per la Key Vault. Sostituire i nomi del Key Vault ( `contoso-vault` ) e del gruppo di risorse `contoso-resource-group` () creati nel passaggio 1.

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. Creare una chiave di Key Vault. Fornire un oggetto univoco `key-name` per questa chiave e sostituire i nomi del Key Vault ( `contoso-vault` ) creato nel passaggio 1. Specificare se si preferisce `RSA` o la `RSA-HSM` crittografia.

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    L'output di questo comando Mostra l'ID chiave ("Kid") per la chiave generata.  Prendere nota dell'ID chiave da usare più avanti in questo esercizio.  Il formato dell'ID chiave è: `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}` .  L'ID chiave ha tre componenti importanti:
    1. URI Key Vault:' https:///{My Key Vault}. Vault. Azure. NET
    1. Nome chiave Key Vault: {nome chiave}
    1. Versione chiave Key Vault: {versione chiave}

1. Creare un'identità gestita assegnata dal sistema usando l'interfaccia della riga di comando di Azure, sostituendo il nome dell'istanza di configurazione dell'app e del gruppo di risorse usato nei passaggi precedenti. L'identità gestita verrà usata per accedere alla chiave gestita. Viene usato `contoso-app-config` per illustrare il nome di un'istanza di configurazione dell'app:
    
    ```azurecli
    az appconfig identity assign --name contoso-app-config --resource-group contoso-resource-group --identities [system]
    ```
    
    L'output di questo comando include l'ID principale ("principalId") e l'ID tenant ("tenandId") dell'identità assegnata dal sistema.  Questi ID verranno usati per concedere all'identità l'accesso alla chiave gestita.

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. Per eseguire la convalida, la crittografia e la decrittografia delle chiavi, l'identità gestita dell'istanza di configurazione app Azure deve accedere alla chiave. Il set specifico di azioni a cui è necessario accedere include: `GET` , `WRAP` e `UNWRAP` per le chiavi.  Per concedere l'accesso è necessario l'ID entità dell'identità gestita dell'istanza di configurazione dell'app. Questo valore è stato ottenuto nel passaggio precedente. Viene visualizzato sotto come `contoso-principalId` . Concedere l'autorizzazione alla chiave gestita usando la riga di comando:

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Una volta che l'istanza di configurazione di app Azure può accedere alla chiave gestita, è possibile abilitare la funzionalità chiave gestita dal cliente nel servizio usando l'interfaccia della riga di comando di Azure. Ricordare le proprietà seguenti registrate durante la procedura di creazione della chiave: `key name` `key vault URI` .

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

L'istanza di configurazione app Azure è ora configurata per l'utilizzo di una chiave gestita dal cliente archiviata nel Azure Key Vault.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stata configurata l'istanza di configurazione di app Azure per usare una chiave gestita dal cliente per la crittografia.  Informazioni su come [integrare il servizio con le identità gestite di Azure](howto-integrate-azure-managed-service-identity.md).
