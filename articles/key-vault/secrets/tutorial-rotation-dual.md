---
title: Esercitazione sulla rotazione per le risorse con due set di credenziali
description: Questa esercitazione illustra come automatizzare la rotazione di un segreto per le risorse che usano due set di credenziali di autenticazione.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: f208752f13848f0f54648d934d1dfb518e2ea1fd
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95500341"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>Automatizzare la rotazione di un segreto per le risorse che hanno due set di credenziali di autenticazione

Sebbene il modo migliore per eseguire l'autenticazione ai servizi di Azure preveda l'utilizzo di un'[identità gestita](../general/authentication.md), esistono alcuni scenari in cui questa alternativa non è disponibile. In questi casi, si usano chiavi di accesso o password. Le chiavi di accesso e le password devono essere ruotate di frequente.

Questa esercitazione illustra come automatizzare la rotazione periodica dei segreti per i database e i servizi che usano due set di credenziali di autenticazione. In particolare, illustra come ruotare le chiavi dell'account di Archiviazione di Azure archiviate in Azure Key Vault come segreti. Si userà una funzione attivata da una notifica di Griglia di eventi di Azure. 

> [!NOTE]
> Le chiavi dell'account di archiviazione possono essere gestite automaticamente in Key Vault fornendo token di firma di accesso condiviso per l'accesso delegato all'account di archiviazione. Esistono servizi che richiedono stringhe di connessione dell'account di archiviazione con chiavi di accesso. Questa soluzione è consigliabile per uno scenario di questo tipo.

Ecco la soluzione di rotazione descritta in questa esercitazione: 

![Diagramma che illustra la soluzione di rotazione.](../media/secrets/rotation-dual/rotation-diagram.png)

In questa soluzione Azure Key Vault archivia le chiavi di accesso individuali dell'account di archiviazione come versioni dello stesso segreto, alternando tra la chiave primaria e quella secondaria nelle versioni successive. Quando una chiave di accesso viene archiviata nella versione più recente del segreto, la chiave alternativa viene rigenerata e aggiunta a Key Vault come nuova versione più recente del segreto. La soluzione fornisce l'intero ciclo di rotazione dell'applicazione da aggiornare alla chiave rigenerata più recente. 

1. Trenta giorni prima della data di scadenza di un segreto, Key Vault pubblica l'evento in scadenza in Griglia di eventi.
1. Griglia di eventi controlla le sottoscrizioni di eventi e, tramite POST HTTP, chiama l'endpoint dell'app per le funzioni che ha sottoscritto l'evento.
1. L'app per le funzioni identifica la chiave alternativa (diversa da quella più recente) e chiama l'account di archiviazione per rigenerarla.
1. L'app per le funzioni aggiunge la nuova chiave rigenerata ad Azure Key Vault come nuova versione del segreto.

## <a name="prerequisites"></a>Prerequisiti
* Una sottoscrizione di Azure. [Crearne una gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure Key Vault.
* Due account di archiviazione di Azure.

Se non si hanno a disposizione un insieme di credenziali delle chiavi e account di archiviazione, è possibile usare questo collegamento di distribuzione:

[![Collegamento Distribuisci in Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. In **Gruppo di risorse** selezionare **Crea nuovo**. Assegnare al gruppo il nome **akvrotation** e quindi scegliere **OK**.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.

    ![Screenshot che mostra come creare un gruppo di risorse.](../media/secrets/rotation-dual/dual-rotation-1.png)

A questo punto si hanno un insieme di credenziali delle chiavi e due account di archiviazione. È possibile verificare questa configurazione nell'interfaccia della riga di comando di Azure eseguendo questo comando:

```azurecli
az resource list -o table -g akvrotation
```

Il risultato dovrebbe essere simile all'output seguente:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>Creare e distribuire la funzione di rotazione delle chiavi

Verrà ora creata un'app per le funzioni con un'identità gestita dal sistema, oltre ad altri componenti necessari. Si distribuirà inoltre la funzione di rotazione per le chiavi degli account di archiviazione.

La funzione di rotazione dell'app per le funzioni richiede i componenti e la configurazione seguenti:
- Un piano di servizio app di Azure
- Un account di archiviazione per gestire i trigger dell'app per le funzioni
- Un criterio di accesso ai segreti in Key Vault
- Il ruolo del servizio dell'operatore della chiave dell'account di archiviazione assegnato all'app per le funzioni in modo che possa accedere alle chiavi di accesso degli account di archiviazione
- Una funzione di rotazione delle chiavi con un trigger di eventi e un trigger HTTP (rotazione su richiesta)
- Una sottoscrizione di eventi di Griglia di eventi per l'evento **SecretNearExpiry**

1. Selezionare il collegamento alla distribuzione modelli di Azure: 

   [![Collegamento alla distribuzione modelli di Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json)

1. Nell'elenco **Gruppo di risorse** selezionare **akvrotation**.
1. Nella casella **Storage Account RG** (Gruppo di risorse dell'account di archiviazione) immettere il nome del gruppo di risorse in cui si trova l'account di archiviazione. Mantenere il valore predefinito **[resourceGroup().name]** se l'account di archiviazione si trova già nello stesso gruppo di risorse in cui si distribuirà la funzione di rotazione delle chiavi.
1. Nella casella **Nome account di archiviazione** immettere il nome dell'account di archiviazione che contiene le chiavi di accesso da ruotare.
1. Nella casella **Key Vault RG** (Gruppo di risorse dell'insieme di credenziali delle chiavi) immettere il nome del gruppo di risorse in cui si trova l'insieme di credenziali delle chiavi. Mantenere il valore predefinito **[resourceGroup().name]** se l'insieme di credenziali delle chiavi si trova già nello stesso gruppo di risorse in cui si distribuirà la funzione di rotazione delle chiavi.
1. Nella casella **Nome insieme di credenziali delle chiavi** immettere il nome dell'insieme di credenziali delle chiavi.
1. Nella casella **Nome dell'app per le funzioni** immettere il nome dell'app per le funzioni.
1. Nella casella **Nome del segreto** immettere il nome del segreto in cui verranno archiviate le chiavi di accesso.
1. Nella casella **URL del repository** immettere il percorso GitHub del codice della funzione: **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** .
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.

   ![Screenshot che mostra come creare il primo account di archiviazione.](../media/secrets/rotation-dual/dual-rotation-2.png)

Dopo aver completato la procedura precedente, si avranno un account di archiviazione, una server farm, un'app per le funzioni e informazioni dettagliate sull'applicazione. Una volta completata la distribuzione, verrà visualizzata questa pagina: ![Screenshot che mostra la pagina La distribuzione è stata completata.](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> In caso di errore, è possibile selezionare **Ridistribuisci** per completare la distribuzione dei componenti.


I modelli di distribuzione e il codice per la funzione di rotazione sono disponibili su [GitHub](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>Aggiungere le chiavi di accesso dell'account di archiviazione a Key Vault

Impostare prima di tutto il criterio di accesso per concedere agli utenti le autorizzazioni per **gestire i segreti**:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

A questo punto è possibile creare un nuovo segreto con una chiave di accesso dell'account di archiviazione come valore. Saranno anche necessari l'ID della risorsa dell'account di archiviazione, il periodo di validità del segreto e l'ID chiave da aggiungere al segreto, in modo che la funzione di rotazione possa rigenerare la chiave nell'account di archiviazione.

Determinare l'ID risorsa dell'account di archiviazione. Il valore è disponibile nella proprietà `id`.
```azurecli
az storage account show -n akvrotationstorage
```

Elencare le chiavi di accesso dell'account di archiviazione in modo da recuperarne i valori:

```azurecli
az storage account keys list -n akvrotationstorage 
```

Eseguire questo comando usando i valori recuperati per `key1Value` e `storageAccountResourceId`:

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Se si crea un segreto con una data di scadenza breve, entro alcuni minuti viene pubblicato un evento `SecretNearExpiry`. Questo evento a sua volta attiverà la funzione per ruotare il segreto.

È possibile verificare che le chiavi di accesso siano state rigenerate recuperando la chiave dell'account di archiviazione e il segreto di Key Vault e confrontandoli.

Usare questo comando per ottenere le informazioni sul segreto:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
Si noti che `CredentialId` è stato aggiornato al valore di `keyName` alternativo e che `value` è stato rigenerato: ![Screenshot che mostra l'output del comando a z keyvault secret show per il primo account di archiviazione.](../media/secrets/rotation-dual/dual-rotation-4.png)

Recuperare le chiavi di accesso per confrontare i valori:
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Screenshot che mostra l'output del comando a z storage account keys list per il primo account di archiviazione.](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>Aggiungere gli account di archiviazione per la rotazione

È possibile riutilizzare la stessa app per le funzioni per ruotare le chiavi di più account di archiviazione. 

Per aggiungere chiavi di account di archiviazione a una funzione esistente per la rotazione, è necessario:
- Il ruolo del servizio dell'operatore della chiave dell'account di archiviazione assegnato all'app per le funzioni in modo che possa accedere alle chiavi di accesso dell'account di archiviazione.
- Una sottoscrizione di eventi di Griglia di eventi per l'evento **SecretNearExpiry**.

1. Selezionare il collegamento alla distribuzione modelli di Azure: 

   [![Collegamento alla distribuzione modelli di Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscription%2Fazuredeploy.json)

1. Nell'elenco **Gruppo di risorse** selezionare **akvrotation**.
1. Nella casella **Nome account di archiviazione** immettere il nome dell'account di archiviazione che contiene le chiavi di accesso da ruotare.
1. Nella casella **Nome insieme di credenziali delle chiavi** immettere il nome dell'insieme di credenziali delle chiavi.
1. Nella casella **Nome dell'app per le funzioni** immettere il nome dell'app per le funzioni.
1. Nella casella **Nome del segreto** immettere il nome del segreto in cui verranno archiviate le chiavi di accesso.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.

   ![Screenshot che mostra come creare un account di archiviazione aggiuntivo.](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Aggiungere un'altra chiave di accesso dell'account di archiviazione a Key Vault

Determinare l'ID risorsa dell'account di archiviazione. Il valore è disponibile nella proprietà `id`.
```azurecli
az storage account show -n akvrotationstorage2
```

Elencare le chiavi di accesso dell'account di archiviazione in modo da recuperare i valori della chiave 2:

```azurecli
az storage account keys list -n akvrotationstorage2 
```

Eseguire questo comando usando i valori recuperati per `key2Value` e `storageAccountResourceId`:

```azurecli
tomorrowDate=`date -d tomorrow -Iseconds -u | awk -F'+' '{print $1"Z"}'`
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Usare questo comando per ottenere le informazioni sul segreto:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
Si noti che `CredentialId` è stato aggiornato al valore di `keyName` alternativo e che `value` è stato rigenerato: ![Screenshot che mostra l'output del comando a z keyvault secret show per il secondo account di archiviazione.](../media/secrets/rotation-dual/dual-rotation-8.png)

Recuperare le chiavi di accesso per confrontare i valori:
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Screenshot che mostra l'output del comando a z storage account keys list per il secondo account di archiviazione.](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-dual-credential-rotation-functions"></a>Funzioni di rotazione di due credenziali di Key Vault

- [Account di archiviazione](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Cache Redis](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="next-steps"></a>Passaggi successivi
- Panoramica: [Monitoraggio di Key Vault con Griglia di eventi di Azure](../general/event-grid-overview.md)
- Procedura: [Creare la prima funzione nel portale di Azure](../../azure-functions/functions-create-first-azure-function.md)
- Procedura: [Ricevere un messaggio di posta elettronica quando viene modificato un segreto dell'insieme di credenziali delle chiavi](../general/event-grid-logicapps.md)
- Informazioni di riferimento: [Schema di eventi di Griglia di eventi di Azure per Azure Key Vault](../../event-grid/event-schema-key-vault.md)
