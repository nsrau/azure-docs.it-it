---
title: Usare le chiavi gestite dal cliente per crittografare i dati di configurazioneUse customer-managed keys to encrypt your configuration data
description: Crittografare i dati di configurazione utilizzando chiavi gestite dal clienteEncrypt your configuration data using customer-managed keys
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/18/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 5749b2fc58c4e1c5c75142f85a5132946714e25b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472635"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>Usare le chiavi gestite dal cliente per crittografare i dati di configurazione dell'appUse customer-managed keys to encrypt your App Configuration data
Configurazione app di Azure [crittografa le informazioni riservate inattivi.](../security/fundamentals/encryption-atrest.md) L'uso di chiavi gestite dal cliente garantisce una maggiore protezione dei dati consentendo di gestire le chiavi di crittografia.  Quando viene usata la crittografia della chiave gestita, tutte le informazioni riservate in Configurazione app vengono crittografate con una chiave dell'insieme di credenziali delle chiavi di Azure fornita dall'utente.  Ciò offre la possibilità di ruotare la chiave di crittografia su richiesta.  Fornisce inoltre la possibilità di revocare l'accesso di Configurazione app di Azure alle informazioni riservate revocando l'accesso dell'istanza di configurazione app alla chiave.

## <a name="overview"></a>Panoramica 
Azure App Configuration encrypts sensitive information at rest using a 256-bit AES encryption key provided by Microsoft. Ogni istanza di Configurazione app ha la propria chiave di crittografia gestita dal servizio e usata per crittografare le informazioni riservate. Le informazioni riservate includono i valori trovati nelle coppie chiave-valore.  Quando la funzionalità a chiave gestita dal cliente è abilitata, Configurazione app usa un'identità gestita assegnata all'istanza di Configurazione app per l'autenticazione con Azure Active Directory.When customer-managed key capability is enabled, App Configuration uses a managed identity assigned to the App Configuration instance to authenticate with Azure Active Directory. L'identità gestita chiama quindi l'insieme di credenziali delle chiavi di Azure ed esegue il wrapping della chiave di crittografia dell'istanza di configurazione dell'app. La chiave di crittografia di cui è stato eseguito il wrapping viene quindi archiviata e la chiave di crittografia senza ritorno a capo viene memorizzata nella cache all'interno di Configurazione app per un'ora. Configurazione app aggiorna la versione senza ritorno a capo della chiave di crittografia dell'istanza di configurazione app ogni ora. Ciò garantisce la disponibilità in condizioni operative normali. 

>[!IMPORTANT]
> Se l'identità assegnata all'istanza di Configurazione app non è più autorizzata ad annullare il wrapping della chiave di crittografia dell'istanza o se la chiave gestita viene eliminata definitivamente, non sarà più possibile decrittografare le informazioni riservate archiviate nell'app Istanza di configurazione. L'uso della funzione di [eliminazione temporanea](../key-vault/key-vault-ovw-soft-delete.md) di Azure Key Vault riduce la possibilità di eliminare accidentalmente la chiave di crittografia.

Quando gli utenti abilitano la funzionalità della chiave gestita dal cliente nell'istanza di Configurazione app di Azure, controllano la capacità del servizio di accedere alle informazioni riservate. La chiave gestita funge da chiave di crittografia radice. Un utente può revocare l'accesso dell'istanza di configurazione dell'app alla chiave gestita modificando i criteri di accesso dell'insieme di credenziali delle chiavi. Quando questo accesso viene revocato, Configurazione app perderà la possibilità di decrittografare i dati utente entro un'ora. A questo punto, l'istanza di configurazione app proibisce tutti i tentativi di accesso. Questa situazione è recuperabile concedendo al servizio l'accesso alla chiave gestita ancora una volta.  Entro un'ora, App Configuration sarà in grado di decrittografare i dati utente e operare in condizioni normali.

>[!NOTE]
>Tutti i dati di Configurazione app di Azure vengono archiviati per un massimo di 24 ore in un backup isolato. Ciò include la chiave di crittografia unwrapped. Questi dati non sono immediatamente disponibili per il team di assistenza o assistenza. In caso di ripristino di emergenza, Configurazione app di Azure si revoca nuovamente dai dati della chiave gestita.

## <a name="requirements"></a>Requisiti
The following components are required to successfully enable the customer-managed key capability for Azure App Configuration:
- Istanza di configurazione dell'app di Azure a livello standardStandard tier Azure App Configuration instance
- L'insieme di credenziali delle chiavi di Azure con le funzionalità di eliminazione temporanea ed eliminazione di protezione abilitateAzure Key Vault with soft-delete and purge-protection features enabled
- Una chiave RSA o RSA-HSM all'interno dell'insieme di credenziali delle chiavi
    - La chiave non deve essere scaduta, deve essere abilitata e deve avere entrambe le funzionalità di wrapping e unwrap abilitate

Dopo aver configurato queste risorse, rimangono due passaggi per consentire a Configurazione app di Azure di usare la chiave dell'insieme di credenziali delle chiavi:Once these resources are configured, two steps remain to allow Azure App Configuration to use the Key Vault key:
1. Assegnare un'identità gestita all'istanza di Configurazione app di AzureAssign a managed identity to the Azure App Configuration instance
2. Concedere l'identità `GET`, `WRAP`e `UNWRAP` le autorizzazioni nei criteri di accesso dell'insieme di credenziali delle chiavi di destinazione.

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Abilitare la crittografia delle chiavi gestita dal cliente per l'istanza di Configurazione app di AzureEnable customer-managed key encryption for your Azure App Configuration instance
Per iniziare, è necessaria un'istanza di Configurazione app di Azure configurata correttamente. Se non è ancora disponibile un'istanza di Configurazione app, seguire una delle guide introduttive seguenti per configurarne una:
- [Creare un'app ASP.NET Core con la configurazione dell'app di AzureCreate an ASP.NET Core app with Azure App Configuration](quickstart-aspnet-core-app.md)
- [Creare un'app .NET Core con Azure App Configuration](quickstart-dotnet-core-app.md)
- [Creare un'app .NET Framework con Configurazione app di Azure](quickstart-dotnet-app.md)
- [Creare un'app Java Spring con Configurazione app di Azure](quickstart-java-spring-app.md)

>[!TIP]
> Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire le istruzioni della riga di comando riportate in questo articolo.  Include strumenti comuni di Azure preinstallati, tra cui .NET Core SDK. Se è stato eseguito l'accesso alla sottoscrizione di Azure, avviare [Azure Cloud Shell](https://shell.azure.com) da shell.azure.com.  Per altre informazioni su Azure Cloud Shell, [leggere la documentazione](../cloud-shell/overview.md)

### <a name="create-and-configure-an-azure-key-vault"></a>Creare e configurare un insieme di credenziali delle chiavi di AzureCreate and configure an Azure Key Vault
1. Creare un insieme di credenziali delle chiavi di Azure usando l'interfaccia della riga di comando di Azure.Create an Azure Key Vault using the Azure CLI.  Si noti che entrambi `vault-name` e `resource-group-name` sono forniti dall'utente e devono essere univoco.  Usiamo `contoso-vault` e `contoso-resource-group` in questi esempi.

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. Abilitare l'eliminazione temporanea e la protezione dall'eliminazione per l'insieme di credenziali delle chiavi. Sostituire i nomi dell'insieme di credenziali delle chiavi (`contoso-vault`) e del gruppo di risorse (`contoso-resource-group`) creati nel passaggio 1.

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. Creare una chiave dell'insieme di credenziali. Specificare `key-name` un un univoco per questa chiave e`contoso-vault`sostituire i nomi dell'insieme di credenziali delle chiavi ( ) creato nel passaggio 1. Specificare se `RSA` `RSA-HSM` si preferisce o se si sta encryptionanda.

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    L'output di questo comando mostra l'ID chiave ("kid") per la chiave generata.  Prendere nota dell'ID chiave da utilizzare più avanti in questo esercizio.  L'ID chiave ha `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}`il formato: .  L'ID chiave ha tre componenti importanti:
    1. URI dell'insieme di credenziali delle chiavi: 'https://'l'insieme di credenziali delle chiavi.vault.azure.netKey Vault URI: 'https://'my key vault'.vault.azure.net
    1. Nome della chiave dell'insieme di credenziali delle chiavi: "Nome chiave"
    1. Versione della chiave dell'insieme di credenziali delle chiavi: versione della chiave

1. Creare un'identità gestita assegnata dal sistema usando l'interfaccia della riga di comando di Azure, sostituendo il nome dell'istanza di configurazione dell'app e del gruppo di risorse usato nei passaggi precedenti. L'identità gestita verrà utilizzata per accedere alla chiave gestita. Usiamo `contoso-app-config` per illustrare il nome di un'istanza di configurazione app:We use to illustrate the name of an App Configuration instance:
    
    ```azurecli
    az appconfig identity assign --na1. me contoso-app-config --group contoso-resource-group --identities [system]
    ```
    
    L'output di questo comando include l'ID entità ("principalId") e l'ID tenant ("tenandId") dell'identità assegnata dal sistema.  Verrà utilizzato per concedere all'identità l'accesso alla chiave gestita.

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. L'identità gestita dell'istanza di Configurazione app di Azure deve accedere alla chiave per eseguire la convalida, la crittografia e la decrittografia della chiave. Il set specifico di azioni a `GET`cui `WRAP`è `UNWRAP` necessario accedere include: , , e per le chiavi.  La concessione dell'accesso richiede l'ID entità dell'identità gestita dell'istanza di configurazione app. Questo valore è stato ottenuto nel passaggio precedente. È mostrato di `contoso-principalId`seguito come . Concedere l'autorizzazione alla chiave gestita tramite la riga di comando:

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Dopo aver ora che l'istanza di Configurazione app di Azure può accedere alla chiave gestita, è possibile abilitare la funzionalità della chiave gestita dal cliente nel servizio usando l'interfaccia della riga di comando di Azure.Once the Azure App Configuration instance can access the managed key, we can enable the customer-managed key capability in the service using the Azure CLI. Richiamare le seguenti proprietà registrate `key name` `key vault URI`durante i passaggi di creazione della chiave: .

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

L'istanza di Configurazione app di Azure è ora configurata per usare una chiave gestita dal cliente archiviata nell'insieme di credenziali delle chiavi di Azure.Your Azure App Configuration instance is now configured to use a customer-managed key stored in Azure Key Vault.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stata configurata l'istanza di Configurazione app di Azure per usare una chiave gestita dal cliente per la crittografia.  Informazioni su come [integrare il servizio con le identità gestite di Azure.](howto-integrate-azure-managed-service-identity.md)