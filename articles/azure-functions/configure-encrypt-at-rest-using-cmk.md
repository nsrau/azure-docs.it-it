---
title: Crittografare l'origine dell'applicazione inattiviEncrypt your application source at rest
description: Crittografare i dati dell'applicazione in Archiviazione di Azure e distribuirlo come file di pacchetto.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 62179e900ace0d6d7b8b1f07e8f0ab685508f991
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408725"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>Crittografia inattivi con chiavi gestite dal cliente

La crittografia dei dati dell'applicazione dell'app per le funzioni inattivi richiede un account di archiviazione di Azure e un insieme di credenziali delle chiavi di Azure.Encrypting your function app's application data at rest for requires an Azure Storage Account and an Azure Key Vault. Questi servizi vengono usati quando si esegue l'app da un pacchetto di distribuzione.

  - [Archiviazione di Azure offre la crittografia inattivi.](../storage/common/storage-service-encryption.md) È possibile utilizzare chiavi fornite dal sistema o chiavi personalizzate gestite dal cliente. Si tratta della posizione in cui vengono archiviati i dati dell'applicazione quando non sono in esecuzione in un'app per le funzioni in Azure.This is where your application data is stored when it's not running in a function app in Azure.
  - [Esecuzione da un pacchetto di distribuzione] ((run-functions-from-deployment-package.md) è una funzionalità di distribuzione del servizio app. Consente di distribuire il contenuto del sito da un account di archiviazione di Azure usando un URL di firma di accesso condiviso.It allows you to deploy your site content from an Azure Storage Account using a Shared Access Signature (SAS) URL.
  - [I riferimenti dell'insieme di credenziali](../app-service/app-service-key-vault-references.md) delle chiavi sono una funzionalità di sicurezza del servizio app. Consente di importare segreti in fase di esecuzione come impostazioni dell'applicazione. Usare questa opzione per crittografare l'URL della firma di accesso condiviso dell'account di archiviazione di Azure.Use this to encrypt the SAS URL of your Azure Storage Account.

## <a name="set-up-encryption-at-rest"></a>Configurare la crittografia inattivi

### <a name="create-an-azure-storage-account"></a>Creare un account di Archiviazione di Azure

Creare [innanzitutto un account di archiviazione](../storage/common/storage-account-create.md) di Azure e [crittografarlo con le chiavi gestite dal cliente.](../storage/common/encryption-customer-managed-keys.md) Dopo aver creato l'account di archiviazione, usare [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) per caricare i file del pacchetto.

Successivamente, utilizzare Esplora archivi per [generare una sAS](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer). 

> [!NOTE]
> Salva questo URL di accesso condiviso, usato in un secondo momento per consentire l'accesso sicuro del pacchetto di distribuzione in fase di esecuzione.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Configurare l'esecuzione da un pacchetto dall'account di archiviazioneConfigure running from a package from your storage account
  
Dopo aver caricato il file nell'archivio BLOB e avere `WEBSITE_RUN_FROM_PACKAGE` un URL di sola distribuzione per il file, impostare l'impostazione dell'applicazione sull'URL della chiamata in stato di condiviso. L'esempio seguente fa usando l'interfaccia della riga di comando di Azure:The following example does it by using Azure CLI:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

L'aggiunta di questa impostazione dell'applicazione causa il riavvio dell'app per le funzioni. Dopo il riavvio dell'app, individuarlo e verificare che l'app sia stata avviata correttamente usando il pacchetto di distribuzione. Se l'applicazione non è stata avviata correttamente, vedere la guida alla [risoluzione dei problemi esecuzione del pacchetto](run-functions-from-deployment-package.md#troubleshooting).

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Crittografare l'impostazione dell'applicazione utilizzando i riferimenti dell'insieme di credenziali delle chiaviEncrypt the application setting using Key Vault

A questo punto è `WEBSITE_RUN_FROM_PACKAGE` possibile sostituire il valore dell'impostazione dell'applicazione con un riferimento dell'insieme di credenziali delle chiavi all'URL con codifica SAS. In questo modo l'URL di firma di accesso condiviso viene crittografato in Key Vault, che offre un ulteriore livello di sicurezza.

1. Utilizzare il [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) comando seguente per creare un'istanza dell'insieme di credenziali delle chiavi.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Seguire [queste istruzioni per concedere all'app l'accesso all'insieme](../app-service/app-service-key-vault-references.md#granting-your-app-access-to-key-vault) di credenziali delle chiavi:Follow these instructions to grant your app access to your key vault:

1. Utilizzare il [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) comando seguente per aggiungere l'URL esterno come segreto nell'insieme di credenziali delle chiavi:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  Utilizzare il [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) comando seguente `WEBSITE_RUN_FROM_PACKAGE` per creare l'impostazione dell'applicazione con il valore come riferimento dell'insieme di credenziali delle chiavi all'URL esterno:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    Sarà `<secret-version>` nell'output del comando `az keyvault secret set` precedente.

L'aggiornamento di questa impostazione dell'applicazione causa il riavvio dell'app per le funzioni. Dopo il riavvio dell'app, individuala per assicurarsi che sia stata avviata correttamente usando il riferimento dell'insieme di credenziali delle chiavi.

## <a name="how-to-rotate-the-access-token"></a>Come ruotare il token di accesso

È consigliabile ruotare periodicamente la chiave di accesso utente dell'account di archiviazione. Per garantire che l'app per le funzioni non perda inavvertitamente l'accesso, è necessario aggiornare anche l'URL di accesso condiviso nell'insieme di credenziali delle chiavi.

1. Ruotare la chiave di accesso base passando all'account di archiviazione nel portale di Azure.Rotate the SAS key by navigating to your storage account in the Azure portal. In **Impostazioni** > **Chiavi di accesso**fare clic sull'icona per ruotare la chiave di accesso livello di accesso base.

1. Copiare il nuovo URL di accesso condiviso e usare il comando seguente per impostare l'URL di chiamata in base alla sicurezza aggiornata nell'insieme di credenziali delle chiavi:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Aggiornare il riferimento dell'insieme di credenziali delle chiavi nell'impostazione dell'applicazione alla nuova versione segreta:Update the key vault reference in your application setting to the new secret version:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    Sarà `<secret-version>` nell'output del comando `az keyvault secret set` precedente.

## <a name="how-to-revoke-the-function-apps-data-access"></a>Come revocare l'accesso ai dati dell'app per le funzioni

Esistono due metodi per revocare l'accesso dell'app per le funzioni all'account di archiviazione. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Ruotare la chiave di accesso utente per l'account di archiviazione di AzureRotate the SAS key for the Azure Storage account

Se la chiave di accesso client per l'account di archiviazione viene ruotata, l'app per le funzioni non avrà più accesso all'account di archiviazione, ma continuerà a essere eseguita con l'ultima versione scaricata del file del pacchetto. Riavviare l'app per le funzioni per cancellare l'ultima versione scaricata.

### <a name="remove-the-function-apps-access-to-key-vault"></a>Rimuovere l'accesso dell'app per le funzioni all'insieme di credenziali delle chiavi

Puoi revocare l'accesso dell'app per le funzioni ai dati del sito disabilitando l'accesso dell'app per le funzioni all'insieme di credenziali delle chiavi. A tale scopo, rimuovere i criteri di accesso per l'identità dell'app per le funzioni. Si tratta della stessa identità creata in precedenza durante la configurazione dei riferimenti dell'insieme di credenziali delle chiavi.

## <a name="summary"></a>Riepilogo

I file dell'applicazione sono ora crittografati inattivi nell'account di archiviazione. All'avvio dell'app per le funzioni, recupera l'URL di chiamata in accesso condiviso dall'insieme di credenziali delle chiavi. Infine, l'app per le funzioni carica i file dell'applicazione dall'account di archiviazione. 

Se è necessario revocare l'accesso dell'app per le funzioni all'account di archiviazione, è possibile revocare l'accesso all'insieme di credenziali delle chiavi o ruotare le chiavi dell'account di archiviazione, che invalida l'URL della chiamata di accesso condiviso.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="is-there-any-additional-charge-for-running-my-function-app-from-the-deployment-package"></a>Sono previsti costi aggiuntivi per l'esecuzione dell'app per le funzioni dal pacchetto di distribuzione?

Solo il costo associato all'account di archiviazione di Azure e gli eventuali addebiti in uscita applicabili.

### <a name="how-does-running-from-the-deployment-package-affect-my-function-app"></a>In che modo l'esecuzione dal pacchetto di distribuzione influisce sull'app per le funzioni?

- L'esecuzione dell'app `wwwroot/` dal pacchetto di distribuzione rende di sola lettura. L'app riceve un errore quando tenta di scrivere in questa directory.
- I formati TAR e G-IP non sono supportati.
- Questa funzionalità non è compatibile con la cache locale.

## <a name="next-steps"></a>Passaggi successivi

- [Riferimenti dell'insieme di credenziali delle chiavi per il servizio appKey Vault references for App Service](../app-service/app-service-key-vault-references.md)
- [Crittografia di Archiviazione di Azure per i dati inattiviAzure Storage encryption for data at rest](../storage/common/storage-service-encryption.md)
