---
title: Usare il portale di Azure per configurare le chiavi gestite dal cliente per il servizio di importazione/esportazioneUse the Azure portal to configure customer-managed keys for Import/Export service
description: Informazioni su come usare il portale di Azure per configurare le chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di Azure per il servizio Importazione/Esportazione di Azure.Learn how to use the Azure portal to configure customer-managed keys with Azure Key Vault for Azure Import/Export service. Le chiavi gestite dal cliente consentono di creare, ruotare, disabilitare e revocare i controlli di accesso.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: ddcb47bfe8ba2b77efd8ff0aed52f1412107f0c5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456499"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Usare chiavi gestite dal cliente nell'insieme di credenziali delle chiavi di Azure per il servizio importazione/esportazioneUse customer-managed keys in Azure Key Vault for Import/Export service

Importazione/esportazione di Azure protegge le chiavi BitLocker usate per bloccare le unità tramite una chiave di crittografia. Per impostazione predefinita, le chiavi BitLocker vengono crittografate con chiavi gestite da Microsoft.By default, BitLocker keys are encrypted with Microsoft-managed keys. Per un ulteriore controllo sulle chiavi di crittografia, è anche possibile fornire chiavi gestite dal cliente.

Le chiavi gestite dal cliente devono essere create e archiviate in un insieme di credenziali delle chiavi di Azure.Customer-managed keys must be created and stored in an Azure Key Vault. Per altre informazioni su Archiviazione delle chiavi di Azure, vedere [Che cos'è l'insieme di](../../key-vault/general/overview.md) credenziali delle chiavi di Azure.For more information about Azure Key Vault, see What is Azure Key Vault?

Questo articolo illustra come usare le chiavi gestite dal cliente con il servizio importazione/esportazione nel portale di Azure.This article shows how to use customer-managed keys with Import/Export service in the [Azure portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. È stato creato un processo di importazione o esportazione in base alle istruzioni in:

    - [Creare un processo di importazione per i BLOB.](storage-import-export-data-to-blobs.md)
    - [Creare un processo](storage-import-export-data-to-files.md)di importazione per i file .
    - [Creare un processo di esportazione per i BLOBCreate an export job for blobs](storage-import-export-data-from-blobs.md)

2. Si dispone di un insieme di credenziali delle chiavi di Azure esistente con una chiave che è possibile usare per proteggere la chiave BitLocker.You have an existing Azure Key Vault with a key in it that you can use to protect your BitLocker key. Per informazioni su come creare un insieme di credenziali delle chiavi usando il portale di Azure, vedere [Guida introduttiva: Impostare e recuperare un segreto da Un](../../key-vault/secrets/quick-create-portal.md)insieme di credenziali delle chiavi di Azure usando il portale di Azure.To learn how to create a key vault using the Azure portal, see Quickstart: Set and retrieve a secret from Azure Key Vault using the Azure portal.

    - **L'eliminazione temporanea** e **l'opzione Non eliminare** sono impostate sull'insieme di credenziali delle chiavi esistente. Queste proprietà non sono abilitate per impostazione predefinita. Per abilitare queste proprietà, vedere le sezioni **intitolate Abilitazione dell'eliminazione temporanea** e **abilitazione** della protezione dell'eliminazione in uno degli articoli seguenti:

        - [Come usare l'eliminazione temporanea con PowerShell](../../key-vault/general/soft-delete-powershell.md).
        - [Come utilizzare l'eliminazione temporanea con CLI](../../key-vault/general/soft-delete-cli.md).
    - L'insieme di credenziali delle chiavi esistente deve avere una chiave RSA di dimensioni 2048 o superiore. Per altre informazioni sulle chiavi, vedere **Chiavi dell'insieme di credenziali delle chiavi** in Informazioni su chiavi, segreti e certificati dell'insieme di credenziali delle chiavi di [Azure.](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)
    - L'insieme di credenziali delle chiavi deve trovarsi nella stessa area dell'account di archiviazione per i dati.  
    - Se non si dispone di un insieme di credenziali delle chiavi di Azure esistente, è anche possibile crearlo inline come descritto nella sezione seguente.

## <a name="enable-keys"></a>Abilitare i tasti

La configurazione della chiave gestita dal cliente per il servizio di importazione/esportazione è facoltativa. Per impostazione predefinita, il servizio di importazione/esportazione utilizza una chiave gestita Microsoft per proteggere la chiave BitLocker. Per abilitare le chiavi gestite dal cliente nel portale di Azure, eseguire la procedura seguente:To enable customer-managed keys in the Azure portal, follow these steps:

1. Passare al pannello **Panoramica** per il processo di importazione.
2. Nel riquadro destro selezionare Scegliere la modalità di **crittografia delle chiavi BitLocker.**

    ![Scegliere l'opzione di crittografia](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. Nel pannello **Crittografia** è possibile visualizzare e copiare la chiave BitLocker del dispositivo. In **Tipo di crittografia**è possibile scegliere la modalità di protezione della chiave BitLocker. Per impostazione predefinita, viene utilizzata una chiave gestita Microsoft.By default, a Microsoft managed key is used.

    ![Visualizza chiave BitLocker](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. È possibile specificare una chiave gestita dal cliente. Dopo aver selezionato la chiave gestita dal cliente, **selezionare l'insieme**di credenziali delle chiavi e una chiave .

    ![Selezionare la chiave gestita dal cliente](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. Nel pannello **Seleziona chiave da archivio chiavi** di Azure la sottoscrizione viene popolata automaticamente. Per **L'insieme di credenziali**delle chiavi, è possibile selezionare un insieme di credenziali delle chiavi esistente dall'elenco a discesa.

    ![Selezionare o creare l'insieme di credenziali delle chiavi di AzureSelect or create Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. È inoltre possibile selezionare **Crea nuovo** per creare un nuovo insieme di credenziali delle chiavi. Nel **pannello Crea insieme di credenziali**delle chiavi immettere il gruppo di risorse e il nome dell'insieme di credenziali delle chiavi. Accettare tutte le altre impostazioni predefinite. Selezionare **Rivedi e crea**.

    ![Creare un nuovo insieme di credenziali delle chiavi di AzureCreate new Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. Esaminare le informazioni associate all'insieme di credenziali delle chiavi e selezionare **Crea**. Attendere un paio di minuti per il completamento della creazione dell'insieme di credenziali delle chiavi.

    ![Creare un Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. In **Seleziona chiave da Azure Key Vault**è possibile selezionare una chiave nell'insieme di credenziali delle chiavi esistente.

9. Se è stato creato un nuovo insieme di credenziali delle chiavi, selezionare **Crea nuovo** per creare una chiave. La dimensione della chiave RSA può essere maggiore o superiore a 2048.

    ![Creare una nuova chiave in Archiviazione chiavi di AzureCreate new key in Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    Se la protezione per l'eliminazione temporanea e l'eliminazione non è abilitata quando si crea l'insieme di credenziali delle chiavi, l'insieme di credenziali delle chiavi verrà aggiornato per abilitare la protezione soft delete ed purge.

10. Specificare il nome della chiave, accettare le altre impostazioni predefinite e selezionare **Crea**.

    ![Crea nuova chiave](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. Selezionare la **versione** e quindi scegliere **Seleziona**. Viene notificato che viene creata una chiave nell'insieme di credenziali delle chiavi.

    ![Nuova chiave creata nell'insieme di credenziali delle chiavi](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

Nel pannello **Crittografia** è possibile visualizzare l'insieme di credenziali delle chiavi e la chiave selezionata per la chiave gestita dal cliente.

## <a name="disable-keys"></a>Disattivare i tasti

È possibile disabilitare le chiavi gestite da Microsoft e passare alle chiavi gestite dal cliente solo in qualsiasi fase del processo di importazione/esportazione. Tuttavia, non è possibile disabilitare la chiave gestita dal cliente dopo averla creata.

## <a name="troubleshoot-customer-managed-key-errors"></a>Risolvere gli errori relativi alle chiavi gestite dai clientiTroubleshoot customer managed key errors

Se si ricevono errori relativi alla chiave gestita dal cliente, utilizzare la tabella seguente per risolvere i problemi:

| Codice di errore     |Dettagli     | Recuperabile?    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked | È stata applicata una chiave gestita dal cliente ma l'accesso alla chiave è attualmente revocato. Per ulteriori informazioni, vedere [Abilitazione dell'accesso alla chiave](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).                                                      | Sì, controlla se: <ol><li>L'insieme di credenziali delle chiavi contiene ancora il file MSI nei criteri di accesso.</li><li>I criteri di accesso forniscono le autorizzazioni per Get, Wrap, Unwrap.</li><li>Se l'insieme di credenziali delle chiavi si trova in una rete virtuale dietro il firewall, **verificare** se l'opzione Consenti servizi attendibili Microsoft è abilitata.</li></ol>                                                                                            |
| CmkErrorDisabled (disabilitato)      | È stata applicata una chiave gestita dal cliente ma la chiave è disabilitata. Per ulteriori informazioni, vedere Come [abilitare la chiave](https://docs.microsoft.com/rest/api/keyvault/vaults/createorupdate).                                                                             | Sì, attivando la versione chiave     |
| CmkErrorNotFound      | È stata applicata una chiave gestita dal cliente ma non è possibile trovare la chiave. <br>Se la chiave viene eliminata ed eliminata dopo il periodo di conservazione, non è possibile recuperarla. Se è stato eseguito il backup della chiave, è possibile ripristinare la chiave per risolvere il problema. | No, la chiave è stata eliminata ed è stata eliminata anche dopo il periodo di conservazione. <br>Sì, solo se il cliente ha la chiave di backup e la ripristina.  |
| CmkErrorVaultNotFound | È stata applicata una chiave gestita dal cliente ma non è possibile trovare l'insieme di credenziali delle chiavi associato alla chiave.<br>Se è stato eliminato l'insieme di credenziali delle chiavi, non è possibile recuperare la chiave gestita dal cliente.  Se è stata eseguita la migrazione dell'insieme di credenziali delle chiavi in un tenant diverso, vedere [Modificare un ID tenant dell'insieme di credenziali](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix)delle chiavi dopo uno spostamento della sottoscrizione. |   No, se il cliente ha eliminato l'insieme di credenziali delle chiavi.<br> Sì, se l'insieme di credenziali delle chiavi è stato sottoposto a una migrazione tenant, eseguire una delle operazione: <ol><li>spostare nuovamente l'insieme di credenziali delle chiavi al vecchio tenant.</li><li>impostare Identità - Nessuno e quindi di nuovo su Identità - SystemAssigned, questo elimina e ricrea l'identità</li></ol>|

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
