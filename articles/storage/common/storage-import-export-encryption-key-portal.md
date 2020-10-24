---
title: Usare il portale di Azure per configurare le chiavi gestite dal cliente per il servizio di importazione/esportazione
description: Informazioni su come usare la portale di Azure per configurare chiavi gestite dal cliente con Azure Key Vault per il servizio importazione/esportazione di Azure. Le chiavi gestite dal cliente consentono di creare, ruotare, disabilitare e revocare i controlli di accesso.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 345fd486788cfbb69454be488d771d9b4ea394ab
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488640"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Usare chiavi gestite dal cliente in Azure Key Vault per il servizio di importazione/esportazione

Importazione/esportazione di Azure protegge le chiavi BitLocker usate per bloccare le unità tramite una chiave di crittografia. Per impostazione predefinita, le chiavi BitLocker vengono crittografate con le chiavi gestite da Microsoft. Per un maggiore controllo sulle chiavi di crittografia, è anche possibile fornire chiavi gestite dal cliente.

Le chiavi gestite dal cliente devono essere create e archiviate in un Azure Key Vault. Per altre informazioni sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali delle chiavi di Azure?](../../key-vault/general/overview.md)

Questo articolo illustra come usare le chiavi gestite dal cliente con il servizio di importazione/esportazione nell' [portale di Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. È stato creato un processo di importazione o esportazione in base alle istruzioni riportate in:

    - [Creare un processo di importazione per i BLOB](storage-import-export-data-to-blobs.md).
    - [Creare un processo di importazione per i file](storage-import-export-data-to-files.md).
    - [Creare un processo di esportazione per i BLOB](storage-import-export-data-from-blobs.md)

2. Si dispone di un Azure Key Vault esistente con una chiave che è possibile utilizzare per proteggere la chiave BitLocker. Per informazioni su come creare un insieme di credenziali delle chiavi usando il portale di Azure, vedere [Guida introduttiva: impostare e recuperare un segreto da Azure Key Vault tramite il portale di Azure](../../key-vault/secrets/quick-create-portal.md).

    - Le operazioni di **eliminazione** temporanea e **non ripulitura** sono impostate sul Key Vault esistente. Queste proprietà non sono abilitate per impostazione predefinita. Per abilitare queste proprietà, vedere le sezioni intitolate abilitazione dell' **eliminazione** temporanea e **Abilitazione della protezione ripulitura** in uno degli articoli seguenti:

        - [Come usare l'eliminazione temporanea con PowerShell](../../key-vault/general/soft-delete-powershell.md).
        - [Come usare l'eliminazione temporanea con l'interfaccia](../../key-vault/general/soft-delete-cli.md)della riga di comando.
    - L'insieme di credenziali delle chiavi esistente deve avere una chiave RSA di 2048 o più dimensioni. Per ulteriori informazioni sulle chiavi, vedere **Key Vault chiavi** in [informazioni su Azure Key Vault chiavi, segreti e certificati](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).
    - Key Vault deve trovarsi nella stessa area dell'account di archiviazione per i dati.  
    - Se non si dispone di un Azure Key Vault esistente, è anche possibile crearlo inline, come descritto nella sezione seguente.

## <a name="enable-keys"></a>Abilita chiavi

La configurazione della chiave gestita dal cliente per il servizio di importazione/esportazione è facoltativa. Per impostazione predefinita, il servizio di importazione/esportazione usa una chiave gestita da Microsoft per proteggere la chiave di BitLocker. Per abilitare le chiavi gestite dal cliente nel portale di Azure, attenersi alla procedura seguente:

1. Passare al pannello **Panoramica** per il processo di importazione.
2. Nel riquadro di destra selezionare scegliere la **modalità di crittografia delle chiavi BitLocker**.

    ![Scegliere l'opzione di crittografia](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. Nel pannello **crittografia** è possibile visualizzare e copiare la chiave BitLocker del dispositivo. In **tipo di crittografia**è possibile scegliere la modalità di protezione della chiave BitLocker. Per impostazione predefinita, viene usata una chiave gestita da Microsoft.

    ![Visualizza chiave BitLocker](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. È possibile specificare una chiave gestita dal cliente. Dopo aver selezionato la chiave gestita dal cliente, **selezionare Key Vault e una chiave**.

    ![Selezionare la chiave gestita dal cliente](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. Nel pannello **Seleziona chiave da Azure Key Vault** la sottoscrizione viene popolata automaticamente. Per **Key Vault**è possibile selezionare un insieme di credenziali delle chiavi esistente dall'elenco a discesa.

    ![Selezionare o creare Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. È anche possibile selezionare **Crea nuovo** per creare un nuovo insieme di credenziali delle chiavi. Nel pannello **Create Key Vault**immettere il gruppo di risorse e il nome dell'insieme di credenziali delle chiavi. Accettare tutte le altre impostazioni predefinite. Selezionare **Rivedi e crea**.

    ![Crea nuovo Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. Esaminare le informazioni associate all'insieme di credenziali delle chiavi e selezionare **Crea**. Attendere un paio di minuti per il completamento della creazione dell'insieme di credenziali delle chiavi.

    ![Creare un Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. Nella **chiave SELECT da Azure Key Vault**è possibile selezionare una chiave nell'insieme di credenziali delle chiavi esistente.

9. Se è stato creato un nuovo insieme di credenziali delle chiavi, selezionare **Crea nuovo** per creare una chiave. La dimensione della chiave RSA può essere 2048 o successiva.

    ![Crea nuova chiave in Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    Se la protezione eliminazione temporanea e ripulitura non è abilitata quando si crea l'insieme di credenziali delle chiavi, l'insieme di credenziali delle chiavi verrà aggiornato in modo che sia abilitata la protezione eliminazione temporanea e ripulitura.

10. Specificare il nome della chiave, accettare le altre impostazioni predefinite e selezionare **Crea**.

    ![Crea nuova chiave](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. Selezionare la **versione** e quindi scegliere **Seleziona**. Si riceve una notifica che indica che è stata creata una chiave nell'insieme di credenziali delle chiavi.

    ![Nuova chiave creata nell'insieme di credenziali delle chiavi](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

Nel pannello **crittografia** è possibile visualizzare l'insieme di credenziali delle chiavi e la chiave selezionata per la chiave gestita dal cliente.

> [!IMPORTANT]
> È possibile disabilitare solo le chiavi gestite da Microsoft e passare alle chiavi gestite dal cliente in qualsiasi fase del processo di importazione/esportazione. Non è tuttavia possibile disabilitare la chiave gestita dal cliente dopo averla creata.

## <a name="troubleshoot-customer-managed-key-errors"></a>Risolvere gli errori relativi alle chiavi gestite dal cliente

Se si ricevono errori correlati alla chiave gestita dal cliente, usare la tabella seguente per risolvere i problemi:

| Codice di errore     |Dettagli     | Ripristinabili?    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked | L'accesso alla chiave gestita dal cliente è stato revocato.                                                       | Sì, controllare se: <ol><li>L'insieme di credenziali delle chiavi ha ancora il file MSI nei criteri di accesso.</li><li>Per i criteri di accesso sono abilitate le autorizzazioni Get, wrap e unwrap.</li><li>Se l'insieme di credenziali delle chiavi si trova in una VNet dietro il firewall, verificare che l'opzione **Consenti servizi attendibili Microsoft** sia abilitata.</li><li>Controllare se l'identità del servizio gestito della risorsa del processo è stata reimpostata `None` usando le API.<br>In caso affermativo, impostare il valore di nuovo su `Identity = SystemAssigned` . Viene ricreata l'identità per la risorsa processo.<br>Una volta creata la nuova identità, abilitare le `Get` `Wrap` autorizzazioni, e `Unwrap` per la nuova identità nei criteri di accesso dell'insieme di credenziali delle chiavi</li></ol>                                                                                            |
| CmkErrorKeyDisabled      | La chiave gestita dal cliente è disabilitata.                                         | Sì, abilitando la versione della chiave     |
| CmkErrorKeyNotFound      | Impossibile trovare la chiave gestita dal cliente. | Sì, se la chiave è stata eliminata ma è ancora entro la durata dell'eliminazione, usando [Annulla la rimozione della chiave](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval)dell'insieme di credenziali delle chiavi.<br>Altro <ol><li>Sì, se il cliente ha il backup della chiave e lo ripristina.</li><li>No, in caso contrario.</li></ol>
| CmkErrorVaultNotFound |Non è possibile trovare l'insieme di credenziali delle chiavi della chiave gestita dal cliente. |   Se l'insieme di credenziali delle chiavi è stato eliminato:<ol><li>Sì, se è in fase di ripulitura-protezione, usando la procedura descritta in [ripristinare un](/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault)insieme di credenziali delle chiavi.</li><li>No, se il superamento della durata della protezione da ripulitura.</li></ol><br>Altrimenti, se l'insieme di credenziali delle chiavi è stato migrato in un tenant diverso, sì, può essere recuperato usando uno dei passaggi seguenti:<ol><li>Ripristinare l'insieme di credenziali delle chiavi al tenant precedente.</li><li>Impostare `Identity = None` , quindi impostare di nuovo il valore su `Identity = SystemAssigned` . Questa operazione consente di eliminare e ricreare l'identità una volta creata la nuova identità. Abilitare `Get` `Wrap` `Unwrap` le autorizzazioni, e per la nuova identità nei criteri di accesso dell'insieme di credenziali delle chiavi.</li></ol>|

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure Key Vault](/azure/key-vault/key-vault-overview)?
