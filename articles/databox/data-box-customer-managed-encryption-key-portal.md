---
title: Utilizzare il portale di Azure per gestire le chiavi gestite dal cliente per Azure Data Box
description: Informazioni su come usare portale di Azure per creare e gestire chiavi gestite dal cliente con Azure Key Vault per un Azure Data Box. Le chiavi gestite dal cliente consentono di creare, ruotare, disabilitare e revocare i controlli di accesso.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 11/19/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: f75907dc1fa079cebb3b80874090c658fd7b8174
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302824"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Usare chiavi gestite dal cliente in Azure Key Vault per Azure Data Box

Azure Data Box protegge la chiave di sblocco del dispositivo (nota anche come password del dispositivo), che viene usata per bloccare un dispositivo tramite una chiave di crittografia. Per impostazione predefinita, questa chiave di crittografia è una chiave gestita da Microsoft. Per un controllo aggiuntivo, è possibile usare una chiave gestita dal cliente.

L'uso di una chiave gestita dal cliente non influisce sulla modalità di crittografia dei dati nel dispositivo. Influiscono solo sulla crittografia della chiave di sblocco del dispositivo.

Per garantire questo livello di controllo durante l'elaborazione dell'ordine, utilizzare una chiave gestita dal cliente durante la creazione dell'ordine. Per ulteriori informazioni, vedere [esercitazione: Order Azure Data Box](data-box-deploy-ordered.md).

Questo articolo illustra come abilitare una chiave gestita dal cliente per l'ordine di Data Box esistente nel [portale di Azure](https://portal.azure.com/). Scoprirai come modificare l'insieme di credenziali delle chiavi, la chiave, la versione o l'identità per la chiave corrente gestita dal cliente oppure tornare a usare una chiave gestita da Microsoft.

Questo articolo si applica ai dispositivi Azure Data Box e Azure Data Box Heavy.

## <a name="requirements"></a>Requisiti

La chiave gestita dal cliente per un ordine di Data Box deve soddisfare i requisiti seguenti:

- La chiave deve essere creata e archiviata in un Azure Key Vault con **eliminazione** temporanea e **non ripulitura** abilitata. Per altre informazioni, vedere [Informazioni sull’insieme di credenziali chiave di Azure](../key-vault/general/overview.md) È possibile creare un insieme di credenziali delle chiavi e una chiave durante la creazione o l'aggiornamento dell'ordine.

- La chiave deve essere una chiave RSA di 2048 dimensioni o superiore.

## <a name="enable-key"></a>Abilita chiave

Per abilitare una chiave gestita dal cliente per l'ordine di Data Box esistente nel portale di Azure, attenersi alla seguente procedura:

1. Passare alla schermata **Panoramica** per l'ordine del data box.

    ![Schermata Panoramica di un ordine di Data Box-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Passare a **impostazioni > crittografia** e selezionare **chiave gestita dal cliente**. Selezionare quindi **selezionare una chiave e un** insieme di credenziali delle chiavi.

    ![Selezionare l'opzione crittografia della chiave gestita dal cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

   Nella schermata **Seleziona chiave da Azure Key Vault** la sottoscrizione viene popolata automaticamente.

 3. Per **Key Vault** è possibile selezionare un insieme di credenziali delle chiavi esistente dall'elenco a discesa oppure selezionare **Crea nuovo** e creare un nuovo insieme di credenziali delle chiavi.

     ![Opzioni di Key Vault quando si seleziona una chiave gestita dal cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3-a.png)

     Per creare un nuovo insieme di credenziali delle chiavi, immettere la sottoscrizione, il gruppo di risorse, il nome dell'insieme di credenziali delle chiavi e altre informazioni nella schermata **Crea nuovo** insieme di credenziali delle chiavi. In **Opzioni di ripristino** assicurarsi che la protezione **eliminazione** temporanea e **ripulitura** sia abilitata. Selezionare quindi **Rivedi e crea**.

      ![Esaminare e creare Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

      Esaminare le informazioni per l'insieme di credenziali delle chiavi e selezionare **Crea**. Attendere un paio di minuti per il completamento della creazione dell'insieme di credenziali delle chiavi.

       ![Creare Azure Key Vault con le impostazioni](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

4. Nella schermata **Seleziona chiave da Azure Key Vault** è possibile selezionare una chiave esistente dall'insieme di credenziali delle chiavi o crearne una nuova.

    ![Selezionare la chiave da Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

   Se si vuole creare una nuova chiave, selezionare **Crea nuovo**. È necessario utilizzare una chiave RSA. La dimensione può essere 2048 o successiva.

    ![Crea nuova chiave in Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6-a.png)

    Immettere un nome per la nuova chiave, accettare le altre impostazioni predefinite e selezionare **Crea**. Si riceverà una notifica che indica che è stata creata una chiave nell'insieme di credenziali delle chiavi.

    ![Nome nuova chiave](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)

5. Per la **versione**, è possibile selezionare una versione chiave esistente dall'elenco a discesa.

    ![Selezionare la versione per la nuova chiave](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

    Se si vuole generare una nuova versione della chiave, selezionare **Crea nuovo**.

    ![Aprire una finestra di dialogo per la creazione di una nuova versione della chiave](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-a.png)

    Scegliere impostazioni per la nuova versione della chiave e selezionare **Crea**.

    ![Creare una nuova versione della chiave](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-b.png)

6. Dopo aver selezionato un insieme di credenziali delle chiavi, una chiave e una versione della chiave, scegliere **Seleziona**.

    ![Chiave in una Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-c.png)

    Le impostazioni del **tipo di crittografia** mostrano l'insieme di credenziali delle chiavi e la chiave scelti.

    ![Chiave e Key Vault per una chiave gestita dal cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

7. Consente di selezionare il tipo di identità da utilizzare per gestire la chiave gestita dal cliente per la risorsa. È possibile usare l'identità **assegnata dal sistema** generata durante la creazione dell'ordine o scegliere un'identità assegnata dall'utente.

    Un'identità assegnata dall'utente è una risorsa indipendente che è possibile usare per gestire l'accesso alle risorse. Per altre informazioni, vedere [tipi di identità gestiti](/azure/active-directory/managed-identities-azure-resources/overview).

    ![Selezionare il tipo di identità](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-13.png)

    Per assegnare un'identità utente, selezionare **utente assegnato**. Selezionare quindi selezionare **un'identità utente** e selezionare l'identità gestita che si vuole usare.

    ![Selezionare un'identità da usare](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-14.png)

    Non è possibile creare una nuova identità utente qui. Per informazioni su come crearne uno, vedere [creare, elencare, eliminare o assegnare un ruolo a un'identità gestita assegnata dall'utente usando il portale di Azure](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal).

    L'identità utente selezionata viene visualizzata nelle impostazioni del **tipo di crittografia** .

    ![Identità utente selezionata visualizzata nelle impostazioni del tipo di crittografia](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-15.png)

 9. Selezionare **Save (Salva** ) per salvare le impostazioni del **tipo di crittografia** aggiornate.

     ![Salva la chiave gestita dal cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    L'URL della chiave viene visualizzato in **tipo di crittografia**.

    ![URL della chiave gestita dal cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)<!--Probably need new screen from recent order. Can you provide one? I can't create an order using CMK with the subscription I'm using.-->

## <a name="change-key"></a>Cambia chiave

Per modificare l'insieme di credenziali delle chiavi, la chiave e/o la versione della chiave per la chiave gestita dal cliente attualmente in uso, attenersi alla procedura seguente:

1. Nella schermata **Panoramica** per l'ordine di data box, passare a **Impostazioni**  >  **crittografia** e fare clic su **cambia chiave**.

    ![Schermata di Panoramica di un ordine di Data Box con chiave gestita dal cliente-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16.png)

2. Scegliere **selezionare un insieme di credenziali delle chiavi e una chiave diversi**.

    ![Schermata Panoramica di un ordine di Data Box, selezionare un'altra chiave e un'opzione dell'insieme di credenziali delle chiavi](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16-a.png)

3. La schermata **Seleziona chiave da insieme di** credenziali delle chiavi Mostra la sottoscrizione, ma nessun insieme di credenziali delle chiavi, chiave o versione della chiave. È possibile apportare le modifiche seguenti:

   - Selezionare una chiave diversa dallo stesso insieme di credenziali delle chiavi. È necessario selezionare l'insieme di credenziali delle chiavi prima di selezionare la chiave e la versione.

   - Selezionare un insieme di credenziali delle chiavi diverso e assegnare una nuova chiave.

   - Modificare la versione per la chiave corrente.
   
    Al termine delle modifiche, scegliere **Seleziona**.

    ![Scegliere l'opzione di crittografia-2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17.png)

4. Selezionare **Salva**.

    ![Salva impostazioni di crittografia aggiornate-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="change-identity"></a>Cambia identità

Per modificare l'identità utilizzata per gestire l'accesso alla chiave gestita dal cliente per questo ordine, attenersi alla seguente procedura:

1. Nella schermata **Panoramica** per l'ordine di data box completato passare a **Impostazioni**  >  **crittografia**.

2. Apportare una delle modifiche seguenti:

     - Per passare a un'identità utente diversa, fare clic su **Seleziona un'identità utente diversa**. Selezionare quindi un'identità diversa nel pannello sul lato destro dello schermo e scegliere **Seleziona**.

       ![Opzione per la modifica dell'identità assegnata dall'utente per una chiave gestita dal cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-18.png)

   - Per passare all'identità assegnata dal sistema generata durante la creazione dell'ordine, selezionare **sistema assegnato** da **Seleziona tipo di identità**.

     ![Opzione per la modifica a un sistema assegnato a una chiave gestita dal cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-19.png)

3. Selezionare **Salva**.

    ![Salva impostazioni di crittografia aggiornate-2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="use-microsoft-managed-key"></a>Usa chiave gestita Microsoft

Per passare dall'utilizzo di una chiave gestita dal cliente alla chiave gestita da Microsoft per l'ordine, attenersi alla procedura seguente:

1. Nella schermata **Panoramica** per l'ordine di data box completato passare a **Impostazioni**  >  **crittografia**.

2. In **Seleziona tipo** selezionare **chiave gestita da Microsoft**.

    ![Schermata Panoramica di un ordine di Data Box-5](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-20.png)

3. Selezionare **Salva**.

    ![Salva le impostazioni di crittografia aggiornate per una chiave gestita da Microsoft](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-21.png)

## <a name="troubleshoot-errors"></a>Risolvere gli errori

Se si ricevono errori correlati alla chiave gestita dal cliente, usare la tabella seguente per risolvere i problemi.

| Codice di errore| Dettagli errore| Ripristinabili?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Non è stato possibile recuperare la passkey perché la chiave gestita dal cliente è disabilitata.| Sì, abilitando la versione della chiave.|
| SsemUserErrorEncryptionKeyExpired| Non è stato possibile recuperare la passkey perché la chiave gestita dal cliente è scaduta.| Sì, abilitando la versione della chiave.|
| SsemUserErrorKeyDetailsNotFound| Non è stato possibile recuperare la passkey perché la chiave gestita dal cliente non è stata trovata.| Se l'insieme di credenziali delle chiavi è stato eliminato, non è possibile recuperare la chiave gestita dal cliente.  Se è stata eseguita la migrazione dell'insieme di credenziali delle chiavi in un tenant diverso, vedere [modificare un ID tenant di Key Vault dopo uno spostamento della sottoscrizione](../key-vault/general/move-subscription.md). Se l'insieme di credenziali delle chiavi è stato eliminato:<ol><li>Sì, se è in fase di ripulitura-protezione, usando la procedura descritta in [ripristinare un](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault)insieme di credenziali delle chiavi.</li><li>No, se il superamento della durata della protezione da ripulitura.</li></ol><br>Altrimenti, se l'insieme di credenziali delle chiavi ha subito una migrazione del tenant, sì, può essere recuperato usando uno dei passaggi seguenti: <ol><li>Ripristinare l'insieme di credenziali delle chiavi al tenant precedente.</li><li>Impostare `Identity = None` , quindi impostare di nuovo il valore su `Identity = SystemAssigned` . Questa operazione consente di eliminare e ricreare l'identità una volta creata la nuova identità. Abilitare `Get` `Wrap` `Unwrap` le autorizzazioni, e per la nuova identità nei criteri di accesso dell'insieme di credenziali delle chiavi.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException | È stata applicata una chiave gestita dal cliente, ma l'accesso alla chiave non è stato concesso o è stato revocato oppure non è possibile accedere all'insieme di credenziali delle chiavi a causa dell'abilitazione del firewall. | Aggiungere l'identità selezionata all'insieme di credenziali delle chiavi per abilitare l'accesso alla chiave gestita dal cliente. Se key Vault dispone di un firewall abilitato, passare a un'identità assegnata dal sistema e quindi aggiungere una chiave gestita dal cliente. Per ulteriori informazioni, vedere la pagina relativa alla modalità di [Abilitazione della chiave](#enable-key). |
| SsemUserErrorKeyVaultDetailsNotFound| Non è stato possibile recuperare la passkey perché l'insieme di credenziali delle chiavi associato per la chiave gestita dal cliente non è stato trovato. | Se l'insieme di credenziali delle chiavi è stato eliminato, non è possibile recuperare la chiave gestita dal cliente.  Se è stata eseguita la migrazione dell'insieme di credenziali delle chiavi in un tenant diverso, vedere [modificare un ID tenant di Key Vault dopo uno spostamento della sottoscrizione](../key-vault/general/move-subscription.md). Se l'insieme di credenziali delle chiavi è stato eliminato:<ol><li>Sì, se è in fase di ripulitura-protezione, usando la procedura descritta in [ripristinare un](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault)insieme di credenziali delle chiavi.</li><li>No, se il superamento della durata della protezione da ripulitura.</li></ol><br>Altrimenti, se l'insieme di credenziali delle chiavi ha subito una migrazione del tenant, sì, può essere recuperato usando uno dei passaggi seguenti: <ol><li>Ripristinare l'insieme di credenziali delle chiavi al tenant precedente.</li><li>Impostare `Identity = None` , quindi impostare di nuovo il valore su `Identity = SystemAssigned` . Questa operazione consente di eliminare e ricreare l'identità una volta creata la nuova identità. Abilitare `Get` `Wrap` `Unwrap` le autorizzazioni, e per la nuova identità nei criteri di accesso dell'insieme di credenziali delle chiavi.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Non è stato possibile recuperare la passkey perché la chiave gestita dal cliente non è stata trovata.| Sì, controllare se: <ol><li>L'insieme di credenziali delle chiavi ha ancora il file MSI nei criteri di accesso.</li><li>L'identità è di tipo System assegnato.</li><li>Abilitare le autorizzazioni Get, wrap e unwrap per l'identità nei criteri di accesso dell'insieme di credenziali delle chiavi.</li></ol>|
| SsemUserErrorUserAssignedLimitReached | L'aggiunta di una nuova identità assegnata dall'utente non è riuscita perché è stato raggiunto il numero massimo di identità assegnate dall'utente che è possibile aggiungere. | Ripetere l'operazione con un minor numero di identità utente oppure rimuovere alcune identità assegnate dall'utente dalla risorsa prima di riprovare. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | Operazione di accesso identità gestita non riuscita. <br> Nota: per lo scenario in cui la sottoscrizione viene spostata in un tenant diverso. Il cliente deve spostare manualmente l'identità in un nuovo tenant. Per informazioni dettagliate, fare. | Spostare l'identità selezionata nel nuovo tenant in cui è presente la sottoscrizione. Per ulteriori informazioni, vedere la pagina relativa alla modalità di [Abilitazione della chiave](#enable-key). |
| SsemUserErrorKekUserIdentityNotFound | È stata applicata una chiave gestita dal cliente, ma l'identità assegnata dall'utente che ha accesso alla chiave non è stata trovata in Active Directory. <br> Nota: questa situazione si verifica quando l'identità utente viene eliminata da Azure.| Per abilitare l'accesso alla chiave gestita dal cliente, provare ad aggiungere all'insieme di credenziali delle chiavi una diversa identità assegnata all'utente. Per ulteriori informazioni, vedere la pagina relativa alla modalità di [Abilitazione della chiave](#enable-key). |
| SsemUserErrorUserAssignedIdentityAbsent | Non è stato possibile recuperare la passkey perché la chiave gestita dal cliente non è stata trovata. | Non è stato possibile accedere alla chiave gestita dal cliente. L'identità assegnata dall'utente (UAI) associata alla chiave è stata eliminata o il tipo UAI è stato modificato. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | Operazione di accesso identità gestita non riuscita. <br> Nota: per lo scenario in cui la sottoscrizione viene spostata in un tenant diverso. Il cliente deve spostare manualmente l'identità in un nuovo tenant. Per informazioni dettagliate, fare. | Per abilitare l'accesso alla chiave gestita dal cliente, provare ad aggiungere all'insieme di credenziali delle chiavi una diversa identità assegnata all'utente. Per ulteriori informazioni, vedere la pagina relativa alla modalità di [Abilitazione della chiave](#enable-key).|
| SsemUserErrorKeyVaultBadRequestException | È stata applicata una chiave gestita dal cliente, ma l'accesso alla chiave non è stato concesso o è stato revocato oppure non è possibile accedere all'insieme di credenziali delle chiavi a causa dell'abilitazione del firewall. | Aggiungere l'identità selezionata all'insieme di credenziali delle chiavi per abilitare l'accesso alla chiave gestita dal cliente. Se key Vault dispone di un firewall abilitato, passare a un'identità assegnata dal sistema e quindi aggiungere una chiave gestita dal cliente. Per ulteriori informazioni, vedere la pagina relativa alla modalità di [Abilitazione della chiave](#enable-key). |
| Errore generico  | Non è stato possibile recuperare la passkey.| Si tratta di un errore generico. Contattare supporto tecnico Microsoft per risolvere l'errore e determinare i passaggi successivi.|

## <a name="next-steps"></a>Passaggi successivi

- [Cos'è l'insieme di credenziali chiave di Azure?](../key-vault/general/overview.md)
- [Guida introduttiva: Impostare e recuperare un segreto da Azure Key Vault usando il portale di Azure](../key-vault/secrets/quick-create-portal.md)
