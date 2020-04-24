---
title: Crittografare l'origine dell'applicazione inattiva
description: Crittografare i dati dell'applicazione in archiviazione di Azure e distribuirli come file di pacchetto.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 7e5e809fe8b670ae6ec5bfd15e54f9a8019e76d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408744"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>Crittografia dei componenti inattivi con chiavi gestite dal cliente

Per crittografare i dati dell'applicazione dell'app Web inattivi sono necessari un account di archiviazione di Azure e un Azure Key Vault. Questi servizi vengono usati quando si esegue l'app da un pacchetto di distribuzione.

  - [Archiviazione di Azure fornisce la crittografia](../storage/common/storage-service-encryption.md)inattiva. È possibile usare chiavi fornite dal sistema o chiavi personalizzate gestite dal cliente. Questa è la posizione in cui vengono archiviati i dati dell'applicazione quando non è in esecuzione in un'app Web in Azure.
  - L' [esecuzione da un pacchetto di distribuzione](deploy-run-package.md) è una funzionalità di distribuzione del servizio app. Consente di distribuire il contenuto del sito da un account di archiviazione di Azure usando un URL di firma di accesso condiviso (SAS).
  - I [riferimenti Key Vault](app-service-key-vault-references.md) sono una funzionalità di sicurezza del servizio app. Consente di importare i segreti in fase di esecuzione come impostazioni dell'applicazione. Usare questa operazione per crittografare l'URL SAS dell'account di archiviazione di Azure.

## <a name="set-up-encryption-at-rest"></a>Configurare la crittografia inattiva

### <a name="create-an-azure-storage-account"></a>Creare un account di Archiviazione di Azure

Prima di tutto, [creare un account di archiviazione di Azure](../storage/common/storage-account-create.md) e [crittografarlo con chiavi gestite dal cliente](../storage/common/encryption-customer-managed-keys.md). Una volta creato l'account di archiviazione, usare il [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) per caricare i file del pacchetto.

Usare quindi il Storage Explorer per [generare una](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer)firma di accesso condiviso. 

> [!NOTE]
> Salvare questo URL di firma di accesso condiviso, che verrà usato in un secondo momento per abilitare l'accesso sicuro al pacchetto di distribuzione in fase di esecuzione.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Configurare l'esecuzione da un pacchetto dall'account di archiviazione
  
Dopo aver caricato il file nell'archiviazione BLOB e avere un URL di firma di accesso condiviso per il `WEBSITE_RUN_FROM_PACKAGE` file, impostare l'impostazione dell'applicazione sull'URL di firma di accesso condiviso. L'esempio seguente esegue questa operazione usando l'interfaccia della riga di comando di Azure:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

L'aggiunta di questa impostazione dell'applicazione comporta il riavvio dell'app Web. Dopo che l'app è stata riavviata, selezionarla e verificare che l'app sia stata avviata correttamente usando il pacchetto di distribuzione. Se l'applicazione non è stata avviata correttamente, vedere la [Guida alla risoluzione dei problemi Esegui da pacchetto](deploy-run-package.md#troubleshooting).

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Crittografare l'impostazione dell'applicazione usando Key Vault riferimenti

A questo punto è possibile sostituire il valore `WEBSITE_RUN_FROM_PACKAGE` dell'impostazione dell'applicazione con un riferimento Key Vault all'URL con codifica SAS. In questo modo viene mantenuto l'URL di firma di accesso condiviso crittografato in Key Vault, che fornisce un livello di sicurezza aggiuntivo.

1. Usare il comando [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) seguente per creare un'istanza di Key Vault.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Seguire [queste istruzioni per concedere all'app l'accesso](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) all'insieme di credenziali delle chiavi:

1. Usare il comando [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) seguente per aggiungere l'URL esterno come segreto nell'insieme di credenziali delle chiavi:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  Usare il comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) seguente per creare l' `WEBSITE_RUN_FROM_PACKAGE` impostazione dell'applicazione con il valore come riferimento Key Vault all'URL esterno:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    L' `<secret-version>` oggetto sarà nell'output del comando precedente `az keyvault secret set` .

Se si aggiorna questa impostazione dell'applicazione, l'app Web verrà riavviata. Dopo che l'app è stata riavviata, selezionarla per verificare che sia stata avviata correttamente usando il riferimento Key Vault.

## <a name="how-to-rotate-the-access-token"></a>Come ruotare il token di accesso

È consigliabile ruotare periodicamente la chiave SAS dell'account di archiviazione. Per assicurarsi che l'app Web non si liberi inavvertitamente, è necessario aggiornare anche l'URL di firma di accesso condiviso in Key Vault.

1. Ruotare la chiave SAS passando all'account di archiviazione nell'portale di Azure. In **Impostazioni** > **chiavi di accesso**, fare clic sull'icona per ruotare la chiave SAS.

1. Copiare il nuovo URL di firma di accesso condiviso e usare il comando seguente per impostare l'URL SAS aggiornato nell'insieme di credenziali delle chiavi:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Aggiornare il riferimento all'insieme di credenziali delle chiavi nell'impostazione dell'applicazione alla nuova versione del segreto:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    L' `<secret-version>` oggetto sarà nell'output del comando precedente `az keyvault secret set` .

## <a name="how-to-revoke-the-web-apps-data-access"></a>Come revocare l'accesso ai dati dell'app Web

Esistono due metodi per revocare l'accesso dell'app Web all'account di archiviazione. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Ruotare la chiave SAS per l'account di archiviazione di Azure

Se la chiave SAS per l'account di archiviazione viene ruotata, l'app Web non avrà più accesso all'account di archiviazione, ma continuerà a essere eseguita con l'ultima versione scaricata del file del pacchetto. Riavviare l'app Web per cancellare l'ultima versione scaricata.

### <a name="remove-the-web-apps-access-to-key-vault"></a>Rimuovere l'accesso dell'app Web a Key Vault

È possibile revocare l'accesso dell'app Web ai dati del sito disabilitando l'accesso dell'app Web a Key Vault. A tale scopo, rimuovere i criteri di accesso per l'identità dell'app Web. Si tratta della stessa identità creata in precedenza durante la configurazione dei riferimenti a Key Vault.

## <a name="summary"></a>Riepilogo

I file dell'applicazione sono ora crittografati a riposo nell'account di archiviazione. Quando l'app Web viene avviata, recupera l'URL di firma di accesso condiviso dall'insieme di credenziali delle chiavi. Infine, l'app Web carica i file dell'applicazione dall'account di archiviazione. 

Se è necessario revocare l'accesso dell'app Web all'account di archiviazione, è possibile revocare l'accesso all'insieme di credenziali delle chiavi o ruotare le chiavi dell'account di archiviazione, che invalida l'URL della firma di accesso condiviso.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="is-there-any-additional-charge-for-running-my-web-app-from-the-deployment-package"></a>Sono previsti costi aggiuntivi per l'esecuzione di un'app Web dal pacchetto di distribuzione?

Solo il costo associato all'account di archiviazione di Azure e gli eventuali addebiti in uscita applicabili.

### <a name="how-does-running-from-the-deployment-package-affect-my-web-app"></a>In che modo l'esecuzione dal pacchetto di distribuzione influisce sull'app Web?

- L'esecuzione dell'app dal pacchetto di distribuzione `wwwroot/` rende di sola lettura. L'app riceve un errore durante il tentativo di scrittura in questa directory.
- I formati TAR e GZIP non sono supportati.
- Questa funzionalità non è compatibile con la cache locale.

## <a name="next-steps"></a>Passaggi successivi

- [Riferimenti Key Vault per il servizio app](app-service-key-vault-references.md)
- [Crittografia di archiviazione di Azure per dati inattivi](../storage/common/storage-service-encryption.md)
