---
title: Esercitazione sulla rotazione per le risorse con due set di credenziali
description: Questa esercitazione illustra come automatizzare la rotazione di un segreto per le risorse che usano due set di credenziali di autenticazione.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: e2729a5dca54bb7e65e9504f89bd203f9928b120
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91251644"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-two-sets-of-authentication-credentials"></a>Automatizzare la rotazione di un segreto per le risorse con due set di credenziali di autenticazione

Sebbene il modo migliore per eseguire l'autenticazione ai servizi di Azure preveda l'utilizzo di un'[identità gestita](../general/authentication.md), esistono alcuni scenari in cui questa alternativa non è disponibile. In questi casi, si usano chiavi di accesso o password. Le chiavi di accesso e le password devono essere ruotate di frequente.

Questa esercitazione illustra come automatizzare la rotazione periodica dei segreti per i database e i servizi che usano due set di credenziali di autenticazione. In particolare, in questa esercitazione vengono ruotate le chiavi dell'account di archiviazione di Azure archiviate in Azure Key Vault come segreti usando una funzione attivata da una notifica di Griglia di eventi di Azure. :

> [!NOTE]
> Le chiavi dell'account di archiviazione possono essere gestite automaticamente in Key Vault fornendo token di firma di accesso condiviso per l'accesso delegato all'account di archiviazione. Alcuni servizi richiedono la stringa di connessione dell'account di archiviazione con la chiave di accesso e questa soluzione è consigliata per tale scenario

![Diagramma della soluzione di rotazione](../media/secrets/rotation-dual/rotation-diagram.png)

Nella soluzione precedente Azure Key Vault archivia le chiavi di accesso individuali dell'account di archiviazione come versioni dello stesso segreto, alternando tra la chiave primaria e quella secondaria nelle versioni successive. Quando una chiave di accesso viene archiviata nella versione più recente del segreto, la chiave alternativa viene rigenerata e aggiunta a Key Vault come nuova e ultima versione del segreto. Tale soluzione fornisce alle applicazioni un intero ciclo di rotazione da aggiornare alla chiave rigenerata più recente. 

1. 30 giorni prima della data di scadenza di un segreto, Key Vault pubblica l'evento "In scadenza" in Griglia di eventi.
1. Griglia di eventi controlla le sottoscrizioni di eventi e, tramite POST HTTP, chiama l'endpoint app per le funzioni che ha sottoscritto questo evento.
1. L'app per le funzioni identifica una chiave alternativa (diversa da quella più recente) e chiama l'account di archiviazione per rigenerarla
1. L'app per le funzioni aggiunge nuova chiave rigenerata ad Azure Key Vault come nuova versione del segreto.

## <a name="prerequisites"></a>Prerequisiti
* Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Insieme di credenziali chiave di Azure
* Due account di archiviazione di Azure

Se non si hanno a disposizione un insieme di credenziali delle chiavi e gli account di archiviazione esistenti, è possibile usare il collegamento alla distribuzione seguente:

[![Immagine che mostra il pulsante "Distribuisci in Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. In **Gruppo di risorse** selezionare **Crea nuovo**. Assegnare al gruppo il nome **akvrotation** e fare clic su **OK**.
1. Selezionare **Rivedi+Crea**.
1. Selezionare **Crea**

    ![Creare un gruppo di risorse](../media/secrets/rotation-dual/dual-rotation-1.png)

A questo punto si avranno un insieme di credenziali delle chiavi e due account di archiviazione. È possibile verificare questa configurazione nell'interfaccia della riga di comando di Azure con il comando seguente:

```azurecli
az resource list -o table -g akvrotation
```

Il risultato sarà simile all'output seguente:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-storage-account-key-rotation-function"></a>Creare e distribuire la funzione di rotazione delle chiavi degli account di archiviazione
> [!IMPORTANT]
> Il modello seguente richiede che Key Vault, l'account di archiviazione di Azure e la funzione di Azure si trovino nello stesso gruppo di risorse

Successivamente, creare un'app per le funzioni con un'identità gestita dal sistema, oltre ad altri componenti necessari, e distribuire le funzioni di rotazione delle chiavi degli account di archiviazione

Le funzioni di rotazione dell'app per le funzioni richiedono i componenti e la configurazione seguenti:
- Un piano di servizio app di Azure
- Un account di archiviazione necessario per la gestione dei trigger dell'app per le funzioni
- Un criterio di accesso ai segreti in Key Vault
- Assegnare il ruolo del servizio dell'operatore della chiave dell'account di archiviazione all'app per le funzioni per l'accesso alle chiavi di accesso dell'account di archiviazione
- Funzioni di rotazione delle chiavi dell'account di archiviazione con trigger di eventi e trigger http (rotazione su richiesta)
- Sottoscrizione di EventGrid per l'evento **SecretNearExpiry**

1. Selezionare il collegamento alla distribuzione modelli di Azure: 

   [![Immagine che mostra il pulsante "Distribuisci in Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json)

1. Nell'elenco **Gruppo di risorse** selezionare **akvrotation**.
1. In **Nome account di archiviazione** digitare il nome dell'account di archiviazione con le chiavi di accesso da ruotare
1. In **Nome dell'insieme di credenziali delle chiavi** digitare il nome dell'insieme di credenziali delle chiavi
1. In **Nome dell'app per le funzioni** digitare il nome dell'app per le funzioni
1. In **Nome del segreto** digitare il nome del segreto in cui verranno archiviate le chiavi di accesso
1. In **URL del repository** digitare la posizione in GitHub del codice della funzione ( **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** )
1. Selezionare **Rivedi+Crea**.
1. Selezionare **Crea**

   ![Esaminare e creare il primo account di archiviazione](../media/secrets/rotation-dual/dual-rotation-2.png)

Dopo aver completato la procedura precedente, saranno disponibili un account di archiviazione, una server farm, un'app per le funzioni e le informazioni dettagliate sull'applicazione. Una volta completata la distribuzione, verrà visualizzata la schermata seguente: ![Distribuzione completata](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> In caso di errori, è possibile fare clic **Ridistribuisci** per completare la distribuzione dei componenti rimanenti.


I modelli di distribuzione e il codice delle funzioni di rotazione sono disponibili in [GitHub](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-storage-account-access-key-to-key-vault"></a>Aggiungere la chiave di accesso dell'account di archiviazione a Key Vault

Impostare prima di tutto il criterio di accesso per concedere agli utenti le autorizzazioni per *gestire i segreti*:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

È ora possibile creare un nuovo segreto con una chiave di accesso dell'account di archiviazione come valore. Sarà anche necessario avere l'ID della risorsa dell'account di archiviazione, il periodo di validità del segreto e l'ID chiave da aggiungere al segreto, in modo che la funzione di rotazione possa rigenerare la chiave nell'account di archiviazione.

Recuperare l'ID risorsa account di archiviazione. Il valore si trova nella proprietà `id`
```azurecli
az storage account show -n akvrotationstorage
```

Elencare le chiavi di accesso dell'account di archiviazione per recuperarne i valori

```azurecli
az storage account keys list -n akvrotationstorage 
```

Popolare i valori recuperati per **key1Value** e **storageAccountResourceId**

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

La creazione di un segreto con una data di scadenza breve pubblicherà entro pochi minuti un evento `SecretNearExpiry`, che attiverà a sua volta la funzione di rotazione del segreto.

È possibile verificare che le chiavi di accesso vengano rigenerate recuperando e confrontando le chiavi degli account di archiviazione e il segreto di Key Vault.

È possibile visualizzare le informazioni sul segreto usando il comando seguente:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
Si noti che `CredentialId` è stato aggiornato al valore `keyName` alternativo e `value` viene rigenerato ![Output di az keyvault secret show per il primo account di archiviazione](../media/secrets/rotation-dual/dual-rotation-4.png)

Recuperare le chiavi di accesso per convalidare il valore
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Output di az storage account keys list per il primo account di archiviazione](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-additional-storage-accounts-for-rotation"></a>Aggiungere altri account di archiviazione per la rotazione

È possibile riutilizzare la stessa app per le funzioni per ruotare più account di archiviazione. 

Per aggiungere altre chiavi dell'account di archiviazione per la rotazione alla funzione esistente è necessario:
- Assegnare il ruolo del servizio dell'operatore della chiave dell'account di archiviazione all'app per le funzioni per l'accesso alle chiavi di accesso dell'account di archiviazione
- Sottoscrizione di EventGrid per l'evento **SecretNearExpiry**

1. Selezionare il collegamento alla distribuzione modelli di Azure: 

   [![Immagine che mostra il pulsante "Distribuisci in Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscription%2Fazuredeploy.json)

1. Nell'elenco **Gruppo di risorse** selezionare **akvrotation**.
1. In **Nome account di archiviazione** digitare il nome dell'account di archiviazione con le chiavi di accesso da ruotare
1. In **Nome dell'insieme di credenziali delle chiavi** digitare il nome dell'insieme di credenziali delle chiavi
1. In **Nome dell'app per le funzioni** digitare il nome dell'app per le funzioni
1. In **Nome del segreto** digitare il nome del segreto in cui verranno archiviate le chiavi di accesso
1. Selezionare **Rivedi+Crea**.
1. Selezionare **Crea**

   ![Verificare e creare il secondo account di archiviazione](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Aggiungere un'altra chiave di accesso dell'account di archiviazione a Key Vault

Recuperare l'ID risorsa account di archiviazione. Il valore si trova nella proprietà `id`
```azurecli
az storage account show -n akvrotationstorage2
```

Elencare le chiavi di accesso dell'account di archiviazione per recuperare il valore di key2

```azurecli
az storage account keys list -n akvrotationstorage2 
```

Popolare i valori recuperati per **key2Value** e **storageAccountResourceId**

```azurecli
tomorrowDate=`date -d tomorrow -Iseconds -u | awk -F'+' '{print $1"Z"}'`
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Visualizzare le informazioni sul segreto usando il comando seguente:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
Si noti che `CredentialId` è stato aggiornato al valore `keyName` alternativo e `value` viene rigenerato ![Output di az keyvault secret show per il secondo account di archiviazione](../media/secrets/rotation-dual/dual-rotation-8.png)

Recuperare le chiavi di accesso per convalidare il valore
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Output di az storage account keys list per il secondo account di archiviazione](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="available-key-vault-dual-credential-rotation-functions"></a>Funzioni disponibili per la rotazione di due credenziali di Key Vault

- [Storage Account](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Cache Redis](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="learn-more"></a>Altre informazioni
- Panoramica: [Monitoraggio di Key Vault con Griglia di eventi di Azure](../general/event-grid-overview.md)
- Procedura: [Creare la prima funzione nel portale di Azure](../../azure-functions/functions-create-first-azure-function.md)
- Procedura: [Ricevere un messaggio di posta elettronica quando viene modificato un segreto dell'insieme di credenziali delle chiavi](../general/event-grid-logicapps.md)
- [Schema di eventi di Griglia di eventi di Azure per Azure Key Vault](../../event-grid/event-schema-key-vault.md)
