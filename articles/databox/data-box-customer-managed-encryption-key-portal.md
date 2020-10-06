---
title: Usare il portale di Azure per configurare le chiavi gestite dal cliente per Azure Data Box
description: Informazioni su come usare la portale di Azure per configurare le chiavi gestite dal cliente con Azure Key Vault per Azure Data Box. Le chiavi gestite dal cliente consentono di creare, ruotare, disabilitare e revocare i controlli di accesso.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 05/07/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 31147d534109e0d74d33d102075c69eeb703496e
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91739936"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Usare chiavi gestite dal cliente in Azure Key Vault per Azure Data Box

Azure Data Box protegge la chiave di sblocco del dispositivo (nota anche come password del dispositivo) usata per bloccare il dispositivo tramite una chiave di crittografia. Per impostazione predefinita, la chiave di sblocco del dispositivo per un ordine di Data Box viene crittografata con una chiave gestita da Microsoft. Per un controllo aggiuntivo sulla chiave di sblocco del dispositivo, è anche possibile fornire una chiave gestita dal cliente. 

Le chiavi gestite dal cliente devono essere create e archiviate in un Azure Key Vault. Per ulteriori informazioni su Azure Key Vault, vedere [che cos'è Azure Key Vault?](../key-vault/general/overview.md).

Questo articolo illustra come usare le chiavi gestite dal cliente con Azure Data Box nel [portale di Azure](https://portal.azure.com/). Questo articolo si applica sia ai dispositivi Azure Data Box che ai dispositivi Azure Data Box Heavy.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. È stato creato un ordine Azure Data Box in base alle istruzioni riportate nell' [esercitazione: order Azure Data Box](data-box-deploy-ordered.md).

2. Si dispone di un Azure Key Vault esistente con una chiave che è possibile usare per proteggere la chiave di sblocco del dispositivo. Per informazioni su come creare un insieme di credenziali delle chiavi usando il portale di Azure, vedere [Guida introduttiva: impostare e recuperare un segreto da Azure Key Vault tramite il portale di Azure](../key-vault/secrets/quick-create-portal.md).

    - Le operazioni di **eliminazione** temporanea e **non ripulitura** sono impostate nell'insieme di credenziali delle chiavi esistente. Queste proprietà non sono abilitate per impostazione predefinita. Per abilitare queste proprietà, vedere le sezioni intitolate abilitazione dell' **eliminazione** temporanea e **Abilitazione della protezione ripulitura** in uno degli articoli seguenti:

        - [Come usare l'eliminazione temporanea con PowerShell](../key-vault/general/soft-delete-powershell.md).
        - [Come usare l'eliminazione temporanea con l'interfaccia](../key-vault/general/soft-delete-cli.md)della riga di comando.
    - L'insieme di credenziali delle chiavi esistente deve avere una chiave RSA di 2048 o più dimensioni. Per ulteriori informazioni sulle chiavi, vedere [informazioni sulle chiavi di Azure Key Vault](../key-vault/keys/about-keys.md).
    - Key Vault deve trovarsi nella stessa area degli account di archiviazione usati per i dati. È possibile collegare più account di archiviazione con la risorsa Azure Data Box.
    - Se non si dispone di un insieme di credenziali delle chiavi esistente, è anche possibile crearlo inline, come descritto nella sezione seguente.

## <a name="enable-keys"></a>Abilita chiavi

La configurazione della chiave gestita dal cliente per la Azure Data Box è facoltativa. Per impostazione predefinita, Data Box usa una chiave gestita da Microsoft per proteggere la chiave BitLocker. Per abilitare una chiave gestita dal cliente nel portale di Azure, attenersi alla seguente procedura:

1. Passare al pannello **Panoramica** per l'ordine di data box.

    ![Pannello di panoramica dell'ordine di Data Box](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Passare a **impostazioni > crittografia**. In **tipo di crittografia**è possibile scegliere come si vuole proteggere la chiave di sblocco del dispositivo. Per impostazione predefinita, viene usata una chiave gestita da Microsoft per proteggere la password di sblocco del dispositivo. 

    ![Scegliere l'opzione di crittografia](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-2.png)

3. Selezionare tipo di crittografia come **chiave gestita dal cliente**. Dopo aver selezionato la chiave gestita dal cliente, **selezionare un insieme di credenziali delle chiavi e una chiave**.

    ![Selezionare la chiave gestita dal cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

5. Nel pannello **Seleziona chiave da Azure Key Vault** la sottoscrizione viene popolata automaticamente. Per **Key Vault**è possibile selezionare un insieme di credenziali delle chiavi esistente dall'elenco a discesa.

    ![Crea nuovo Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-31.png)

    È anche possibile selezionare **Crea nuovo** per creare un nuovo insieme di credenziali delle chiavi. Nel pannello **Create Key Vault**immettere il gruppo di risorse e il nome dell'insieme di credenziali delle chiavi. Assicurarsi che la protezione **eliminazione** temporanea e **ripulitura** sia abilitata. Accettare tutte le altre impostazioni predefinite. Selezionare **Rivedi e crea**.

    ![Crea nuovo Azure Key Vault 2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

7. Esaminare le informazioni associate all'insieme di credenziali delle chiavi e selezionare **Crea**. Attendere un paio di minuti per il completamento della creazione dell'insieme di credenziali delle chiavi.

    ![Creare un Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

8. Nella **chiave SELECT da Azure Key Vault**è possibile selezionare una chiave nell'insieme di credenziali delle chiavi esistente.

    ![Crea nuova chiave in Azure Key Vault 3](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

9. Se si vuole creare una nuova chiave, selezionare **Crea nuovo** per creare una chiave. La dimensione della chiave RSA può essere 2048 o successiva.

    ![Crea nuova chiave in Azure Key Vault 4](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-61.png)

10. Specificare il nome della chiave, accettare le altre impostazioni predefinite e selezionare **Crea**. 

    ![Crea nuova chiave](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)


11. Si riceve una notifica che indica che è stata creata una chiave nell'insieme di credenziali delle chiavi. Selezionare la **versione** e quindi scegliere **Seleziona**.

    ![Nuova chiave creata nell'insieme di credenziali delle chiavi](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

12. Nel riquadro **tipo di crittografia** è possibile visualizzare l'insieme di credenziali delle chiavi e la chiave selezionata per la chiave gestita dal cliente.

    ![Chiave e Key Vault per la chiave gestita dal cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

13. Salvare la chiave. 

    ![Salva chiave gestita dal cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    L'URL della chiave viene visualizzato in **tipo di crittografia**.

    ![URL della chiave gestita dal cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)

> [!IMPORTANT]
> È possibile disabilitare la chiave gestita da Microsoft e passare alla chiave gestita dal cliente in qualsiasi fase dell'ordine Data Box. Tuttavia, dopo aver creato la chiave gestita dal cliente, non è possibile tornare alla chiave gestita da Microsoft.

## <a name="troubleshoot-errors"></a>Risolvere gli errori

Se si ricevono errori correlati alla chiave gestita dal cliente, usare la tabella seguente per risolvere i problemi.

| Codice di errore| Dettagli errore| Ripristinabili?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Non è stato possibile recuperare la passkey perché la chiave gestita dal cliente è disabilitata.| Sì, abilitando la versione della chiave.|
| SsemUserErrorEncryptionKeyExpired| Non è stato possibile recuperare la passkey perché la chiave gestita dal cliente è scaduta.| Sì, abilitando la versione della chiave.|
| SsemUserErrorKeyDetailsNotFound| Non è stato possibile recuperare la passkey perché la chiave gestita dal cliente non è stata trovata.| Se l'insieme di credenziali delle chiavi è stato eliminato, non è possibile recuperare la chiave gestita dal cliente.  Se è stata eseguita la migrazione dell'insieme di credenziali delle chiavi in un tenant diverso, vedere [modificare un ID tenant di Key Vault dopo uno spostamento della sottoscrizione](../key-vault/general/move-subscription.md). Se l'insieme di credenziali delle chiavi è stato eliminato:<ol><li>Sì, se è in fase di ripulitura-protezione, usando la procedura descritta in [ripristinare un](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault)insieme di credenziali delle chiavi.</li><li>No, se il superamento della durata della protezione da ripulitura.</li></ol><br>Altrimenti, se l'insieme di credenziali delle chiavi ha subito una migrazione del tenant, sì, può essere recuperato usando uno dei passaggi seguenti: <ol><li>Ripristinare l'insieme di credenziali delle chiavi al tenant precedente.</li><li>Impostare `Identity = None` , quindi impostare di nuovo il valore su `Identity = SystemAssigned` . Questa operazione consente di eliminare e ricreare l'identità una volta creata la nuova identità. Abilitare `Get` `Wrap` `Unwrap` le autorizzazioni, e per la nuova identità nei criteri di accesso dell'insieme di credenziali delle chiavi.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException| Non è stato possibile recuperare la passkey perché l'accesso alla chiave gestita dal cliente è stato revocato.| Sì, controllare se: <ol><li>L'insieme di credenziali delle chiavi ha ancora il file MSI nei criteri di accesso.</li><li>Il criterio di accesso fornisce le autorizzazioni per ottenere, eseguire il wrapping, annullare il wrapping.</li><li>Se l'insieme di credenziali delle chiavi si trova in una vNet dietro il firewall, verificare che l'opzione **Consenti servizi attendibili Microsoft** sia abilitata.</li></ol>|
| SsemUserErrorKeyVaultDetailsNotFound| Non è stato possibile recuperare la passkey perché l'insieme di credenziali delle chiavi associato per la chiave gestita dal cliente non è stato trovato. | Se l'insieme di credenziali delle chiavi è stato eliminato, non è possibile recuperare la chiave gestita dal cliente.  Se è stata eseguita la migrazione dell'insieme di credenziali delle chiavi in un tenant diverso, vedere [modificare un ID tenant di Key Vault dopo uno spostamento della sottoscrizione](../key-vault/general/move-subscription.md). Se l'insieme di credenziali delle chiavi è stato eliminato:<ol><li>Sì, se è in fase di ripulitura-protezione, usando la procedura descritta in [ripristinare un](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault)insieme di credenziali delle chiavi.</li><li>No, se il superamento della durata della protezione da ripulitura.</li></ol><br>Altrimenti, se l'insieme di credenziali delle chiavi ha subito una migrazione del tenant, sì, può essere recuperato usando uno dei passaggi seguenti: <ol><li>Ripristinare l'insieme di credenziali delle chiavi al tenant precedente.</li><li>Impostare `Identity = None` , quindi impostare di nuovo il valore su `Identity = SystemAssigned` . Questa operazione consente di eliminare e ricreare l'identità una volta creata la nuova identità. Abilitare `Get` `Wrap` `Unwrap` le autorizzazioni, e per la nuova identità nei criteri di accesso dell'insieme di credenziali delle chiavi.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Non è stato possibile recuperare la passkey perché la chiave gestita dal cliente non è stata trovata.| Sì, controllare se: <ol><li>L'insieme di credenziali delle chiavi ha ancora il file MSI nei criteri di accesso.</li><li>L'identità è di tipo System assegnato.</li><li>Abilitare le autorizzazioni Get, wrap e unwrap per l'identità nei criteri di accesso dell'insieme di credenziali delle chiavi.</li></ol>|
| Errore generico  | Non è stato possibile recuperare la passkey.| Si tratta di un errore generico. Contattare supporto tecnico Microsoft per risolvere l'errore e determinare i passaggi successivi.|


## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure Key Vault](../key-vault/general/overview.md)?
