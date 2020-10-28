---
title: Crittografia dei dati di backup tramite chiavi gestite dal cliente
description: Informazioni su come backup di Azure consente di crittografare i dati di backup usando chiavi gestite dal cliente (CMK).
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 6e3eea4b5f44203b68c1263c0fb3ae843cabbe72
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895988"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Crittografia dei dati di backup tramite chiavi gestite dal cliente

Backup di Azure consente di crittografare i dati di backup usando chiavi gestite dal cliente (CMK) invece di usare chiavi gestite dalla piattaforma, abilitate per impostazione predefinita. Le chiavi usate per crittografare i dati di backup devono essere archiviate in [Azure Key Vault](../key-vault/index.yml).

La chiave di crittografia usata per crittografare i backup può essere diversa da quella usata per l'origine. I dati vengono protetti usando una chiave di crittografia dei dati basata su AES 256, che, a sua volta, viene protetta usando le chiavi (KEK). In questo modo si ottiene il controllo completo sui dati e sulle chiavi. Per consentire la crittografia, è necessario che all'insieme di credenziali dei servizi di ripristino sia concesso l'accesso alla chiave di crittografia nel Azure Key Vault. È possibile modificare la chiave come e quando richiesto.

Questo articolo illustra quanto segue:

- Creazione di un insieme di credenziali di servizi di ripristino
- Configurazione dell'insieme di credenziali di servizi di ripristino per crittografare i dati di backup usando chiavi gestite dal cliente
- Esecuzione del backup negli insiemi di credenziali crittografati con chiavi gestite dal cliente
- Ripristino dei dati dai backup

## <a name="before-you-start"></a>Prima di iniziare

- Questa funzionalità consente di crittografare solo i nuovi insiemi di credenziali **dei servizi di ripristino** . Gli insiemi di credenziali contenenti gli elementi esistenti registrati o tentati di essere registrati non sono supportati.

- Una volta abilitata per un insieme di credenziali di servizi di ripristino, la crittografia con chiavi gestite dal cliente non può essere ripristinata usando chiavi gestite dalla piattaforma (impostazione predefinita). È possibile modificare le chiavi di crittografia in base ai propri requisiti.

- Questa funzionalità **non supporta attualmente il backup con l'agente Mars** e potrebbe non essere possibile usare un insieme di credenziali crittografato con CMK per lo stesso. L'agente MARS usa una crittografia basata su passphrase utente. Questa funzionalità non supporta inoltre il backup di VM classiche.

- Questa funzionalità non è correlata a [crittografia dischi di Azure](../security/fundamentals/azure-disk-encryption-vms-vmss.md), che usa la crittografia basata su Guest dei dischi di una macchina virtuale con BitLocker (per Windows) e DM-Crypt (per Linux)

- L'insieme di credenziali di servizi di ripristino può essere crittografato solo con chiavi archiviate in un Azure Key Vault, che si trova nella **stessa area** . Inoltre, le chiavi devono essere solo **chiavi RSA 2048** e devono essere in stato **abilitato** .

- Lo stato di CMK dell'insieme di credenziali di servizi di ripristino crittografati tra gruppi di risorse e sottoscrizioni

- Questa funzionalità è attualmente configurabile solo dal portale di Azure.

Se non è stato creato e configurato l'insieme di credenziali di servizi di ripristino, è possibile [leggere qui](backup-create-rs-vault.md).

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Configurazione di un insieme di credenziali per la crittografia tramite chiavi gestite dal cliente

Questa sezione prevede i passaggi seguenti:

1. Abilitare l'identità gestita per l'insieme di credenziali di servizi di ripristino

1. Assegnare le autorizzazioni all'insieme di credenziali per accedere alla chiave di crittografia nel Azure Key Vault

1. Abilitare l'eliminazione temporanea e ripulire la protezione sul Azure Key Vault

1. Assegnare la chiave di crittografia all'insieme di credenziali dei servizi di ripristino

È necessario che tutti questi passaggi siano seguiti nell'ordine indicato in precedenza per ottenere i risultati desiderati. Ogni passaggio viene descritto in dettaglio di seguito.

### <a name="enable-managed-identity-for-your-recovery-services-vault"></a>Abilitare l'identità gestita per l'insieme di credenziali di servizi di ripristino

Backup di Azure usa l'identità gestita assegnata dal sistema per autenticare l'insieme di credenziali di servizi di ripristino per accedere alle chiavi di crittografia archiviate nel Azure Key Vault. Per abilitare l'identità gestita per l'insieme di credenziali di servizi di ripristino, attenersi alla procedura descritta di seguito.

>[!NOTE]
>Una volta abilitata, l'identità gestita **non** deve essere disabilitata (anche temporaneamente). La disabilitazione dell'identità gestita può causare un comportamento incoerente.

1. Passare all'insieme di credenziali dei servizi di ripristino-> **identità**

    ![Impostazioni di identità](./media/encryption-at-rest-with-cmk/managed-identity.png)

1. Modificare lo **stato** **su on** e selezionare **Salva** .

1. Viene generato un ID oggetto, ovvero l'identità gestita assegnata dal sistema dell'insieme di credenziali.

### <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>Assegnare le autorizzazioni all'insieme di credenziali dei servizi di ripristino per accedere alla chiave di crittografia nel Azure Key Vault

A questo punto è necessario consentire all'insieme di credenziali dei servizi di ripristino di accedere ai Azure Key Vault che contengono la chiave di crittografia. Questa operazione viene eseguita consentendo all'identità gestita dell'insieme di credenziali di servizi di ripristino di accedere al Key Vault.

1. Passare ai **criteri di accesso** Azure Key Vault->. Continuare con **+ Aggiungi criteri di accesso** .

    ![Aggiungi criteri di accesso](./media/encryption-at-rest-with-cmk/access-policies.png)

1. In **autorizzazioni chiave** selezionare le operazioni **Get** , **List** , **Unwrap Key** e **Wrap Key** . Specifica le azioni sulla chiave che saranno consentite.

    ![Assegnazione di autorizzazioni per chiavi](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. Passare a **Seleziona entità** e cercare l'insieme di credenziali nella casella di ricerca usando il nome o l'identità gestita. Quando viene visualizzato, selezionare l'insieme di credenziali e scegliere **Seleziona** nella parte inferiore del riquadro.

    ![Selezionare un'entità](./media/encryption-at-rest-with-cmk/select-principal.png)

1. Al termine, selezionare **Aggiungi** per aggiungere i nuovi criteri di accesso.

1. Selezionare **Save (Salva** ) per salvare le modifiche apportate ai criteri di accesso del Azure Key Vault.

### <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>Abilitare l'eliminazione temporanea e ripulire la protezione sul Azure Key Vault

È necessario **abilitare l'eliminazione temporanea e ripulire la protezione** sul Azure Key Vault in cui è archiviata la chiave di crittografia. Questa operazione può essere eseguita dall'interfaccia utente di Azure Key Vault, come illustrato di seguito. In alternativa, è possibile impostare queste proprietà durante la creazione del Key Vault. Per altre informazioni su queste proprietà Key Vault, vedere [qui](../key-vault/general/soft-delete-overview.md).

![Abilitare l'eliminazione temporanea e la protezione dalla rimozione definitiva](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

È anche possibile abilitare l'eliminazione temporanea e ripulire la protezione tramite PowerShell seguendo questa procedura:

1. Accedere all'account Azure personale.

    ```azurepowershell
    Login-AzAccount
    ```

1. Selezionare la sottoscrizione che contiene l'insieme di credenziali.

    ```azurepowershell
    Set-AzContext -SubscriptionId SubscriptionId
    ```

1. Abilitare l'eliminazione temporanea

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

1. Abilita Ripulisci protezione

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

### <a name="assign-encryption-key-to-the-rs-vault"></a>Assegnare la chiave di crittografia all'insieme di credenziali RS

>[!NOTE]
> Prima di procedere, verificare quanto segue:
>
> - Tutti i passaggi indicati in precedenza sono stati completati correttamente:
>   - L'identità gestita dell'insieme di credenziali di servizi di ripristino è stata abilitata ed è stata assegnata le autorizzazioni necessarie
>   - Per la Azure Key Vault è abilitata l'eliminazione temporanea e la protezione da ripulitura
> - L'insieme di credenziali di servizi di ripristino per cui si vuole abilitare la crittografia CMK **non contiene** elementi protetti o registrati

Una volta verificate le precedenti, continuare con la selezione della chiave di crittografia per l'insieme di credenziali.

Per assegnare la chiave:

1. Passare all'insieme di credenziali dei servizi di ripristino- **proprietà** >

    ![Impostazioni di crittografia](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. Selezionare **Aggiorna** in **impostazioni di crittografia** .

1. Nel riquadro impostazioni di crittografia selezionare **Usa una chiave personalizzata** e continuare a specificare la chiave usando uno dei modi seguenti. **Verificare che la chiave che si vuole usare sia una chiave RSA 2048, che si trova in uno stato abilitato.**

    1. Immettere l' **URI della chiave** con cui si vogliono crittografare i dati in questo insieme di credenziali di servizi di ripristino. È anche necessario specificare la sottoscrizione in cui è presente la Azure Key Vault (che contiene la chiave). Questo URI chiave può essere ottenuto dalla chiave corrispondente nell'Azure Key Vault. Verificare che l'URI della chiave sia copiato correttamente. È consigliabile usare il pulsante **copia negli Appunti** fornito con l'identificatore di chiave.

        ![Immettere l'URI della chiave](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. Individuare e selezionare la chiave dal Key Vault nel riquadro Selezione chiave.

        ![Selezionare la chiave da Key Vault](./media/encryption-at-rest-with-cmk/key-vault.png)

1. Selezionare **Salva** .

1. **Rilevamento dello stato di avanzamento dell'aggiornamento della chiave di crittografia:** È possibile tenere traccia dello stato di avanzamento dell'assegnazione della chiave usando il **log attività** nell'insieme di credenziali di servizi di ripristino. Lo stato dovrebbe presto essere modificato in **riuscito** . L'insieme di credenziali ora eseguirà la crittografia di tutti i dati con la chiave specificata come KEK.

    ![Tenere traccia dello stato di avanzamento con il log attività](./media/encryption-at-rest-with-cmk/activity-log.png)

    ![Stato completato](./media/encryption-at-rest-with-cmk/status-succeeded.png)

>[!NOTE]
> Questo processo rimane invariato quando si desidera aggiornare o modificare la chiave di crittografia. Se si desidera aggiornare e utilizzare una chiave da un'altra Key Vault (diversa da quella attualmente in uso), verificare che:
>
> - Il Key Vault si trova nella stessa area dell'insieme di credenziali di servizi di ripristino
>
> - Per l'insieme di credenziali delle chiavi è abilitata la protezione eliminazione temporanea e ripulitura
>
> - L'insieme di credenziali di servizi di ripristino dispone delle autorizzazioni necessarie per accedere al Key Vault.

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>Esecuzione del backup in un insieme di credenziali crittografato con chiavi gestite dal cliente

Prima di procedere alla configurazione della protezione, è consigliabile assicurarsi che venga rispettato l'elenco di controllo seguente. Questo è importante perché, una volta che un elemento è stato configurato per eseguire il backup (o si è tentato di eseguire la configurazione) in un insieme di credenziali crittografato non CMK, non è possibile abilitare la crittografia con chiavi gestite dal cliente e continuerà a usare le chiavi gestite dalla piattaforma.

>[!IMPORTANT]
> Prima di procedere alla configurazione della protezione **, è necessario avere completato** i passaggi seguenti:
>
>1. L'insieme di credenziali di backup è stato creato
>1. Abilita l'identità gestita assegnata dal sistema dell'insieme di credenziali di backup
>1. Autorizzazioni assegnate all'insieme di credenziali per il backup per accedere alle chiavi di crittografia dalla Key Vault
>1. È stata abilitata l'eliminazione temporanea e la protezione per l'Key Vault
>1. È stata assegnata una chiave di crittografia valida per l'insieme di credenziali di backup
>
>Se tutti i passaggi precedenti sono stati confermati, procedere solo con la configurazione del backup.

Il processo per configurare ed eseguire i backup in un insieme di credenziali di servizi di ripristino crittografato con chiavi gestite dal cliente è identico a quello di un insieme di credenziali che usa chiavi gestite dalla piattaforma, **senza modifiche all'esperienza** . Questo vale per il [backup di macchine virtuali di Azure](./quick-backup-vm-portal.md) e per il backup dei carichi di lavoro in esecuzione all'interno di una macchina virtuale, ad esempio [SAP Hana](./tutorial-backup-sap-hana-db.md) [SQL Server](./tutorial-sql-backup.md) database.

## <a name="restoring-data-from-backup"></a>Ripristino dei dati dal backup

### <a name="vm-backup"></a>Backup VM

I dati archiviati nell'insieme di credenziali di servizi di ripristino possono essere ripristinati in base ai passaggi descritti [qui](./backup-azure-arm-restore-vms.md). Quando si esegue il ripristino da un insieme di credenziali di servizi di ripristino crittografato con chiavi gestite dal cliente, è possibile scegliere di crittografare i dati ripristinati con un set di crittografia del disco (DES).

#### <a name="restoring-vm--disk"></a>Ripristino di VM/dischi

1. Quando si ripristina un disco o una macchina virtuale da un punto di ripristino "snapshot", i dati ripristinati verranno crittografati con le DES usate per la crittografia dei dischi della VM di origine.

1. Quando si ripristina un disco/una macchina virtuale da un punto di ripristino con tipo di ripristino "insieme di credenziali", è possibile scegliere di crittografare i dati ripristinati usando un DES, specificato al momento del ripristino. In alternativa, è possibile scegliere di continuare con il ripristino dei dati senza specificare un DES, nel qual caso verrà crittografato con le chiavi gestite da Microsoft.

È possibile crittografare il disco o la macchina virtuale ripristinata al termine del ripristino, indipendentemente dalla selezione eseguita durante l'avvio del ripristino.

![Punti di ripristino](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>Selezionare un set di crittografia del disco durante il ripristino dal punto di ripristino dell'insieme di credenziali

Il set di crittografia del disco viene specificato in impostazioni di crittografia nel riquadro ripristino, come illustrato di seguito:

1. In **Crittografa dischi con la chiave** selezionare **Sì** .

1. Nell'elenco a discesa selezionare la DES da usare per i dischi ripristinati. **Assicurarsi di avere accesso al DES.**

>[!NOTE]
>La possibilità di scegliere un DES durante il ripristino non è disponibile se si sta ripristinando una VM che usa crittografia dischi di Azure.

![Crittografare il disco usando la chiave](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

#### <a name="restoring-files"></a>Ripristino di file

Quando si esegue un ripristino di file, i dati ripristinati verranno crittografati con la chiave utilizzata per crittografare il percorso di destinazione.

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>Ripristino di database SAP HANA/SQL in macchine virtuali di Azure

Quando si esegue il ripristino da un database SAP HANA/SQL di cui è stato eseguito il backup in esecuzione in una macchina virtuale di Azure, i dati ripristinati verranno crittografati usando la chiave di crittografia usata nel percorso di archiviazione di destinazione. Potrebbe trattarsi di una chiave gestita dal cliente o una chiave gestita dalla piattaforma usata per crittografare i dischi della macchina virtuale.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>È possibile crittografare un insieme di credenziali di backup esistente con chiavi gestite dal cliente?

No, la crittografia CMK può essere abilitata solo per i nuovi insiemi di credenziali. Quindi, l'insieme di credenziali non deve mai avere elementi protetti. Infatti, non è necessario eseguire alcun tentativo di proteggere gli elementi nell'insieme di credenziali prima di abilitare la crittografia tramite chiavi gestite dal cliente.

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>Ho provato a proteggere un elemento nell'insieme di credenziali, ma non è riuscito e l'insieme di credenziali non contiene ancora elementi protetti. È possibile abilitare la crittografia CMK per questo insieme di credenziali?

No, l'insieme di credenziali non deve avere alcun tentativo di proteggere gli elementi nel passato.

### <a name="i-have-a-vault-thats-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>Ho un insieme di credenziali che usa la crittografia CMK. È possibile ripristinare la crittografia in un secondo momento usando chiavi gestite dalla piattaforma anche se sono presenti elementi di backup protetti nell'insieme di credenziali?

No, dopo aver abilitato la crittografia CMK, non è possibile ripristinare l'uso delle chiavi gestite dalla piattaforma. È possibile modificare le chiavi usate in base ai propri requisiti.

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>La crittografia CMK per backup di Azure si applica anche a Azure Site Recovery?

No, in questo articolo viene illustrata solo la crittografia dei dati di backup. Per Azure Site Recovery, è necessario impostare la proprietà separatamente come disponibile dal servizio.

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>Ho perso una delle procedure descritte in questo articolo e ho continuato a proteggere l'origine dati. È ancora possibile usare la crittografia CMK?

Non seguendo la procedura descritta nell'articolo e continuando a proteggere gli elementi, l'insieme di credenziali potrebbe non essere in grado di usare la crittografia con chiavi gestite dal cliente. È quindi consigliabile fare riferimento a [questo elenco di controllo](#backing-up-to-a-vault-encrypted-with-customer-managed-keys) prima di procedere con la protezione degli elementi.

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>L'uso di CMK-Encryption viene aggiunto al costo dei backup?

L'uso della crittografia CMK per il backup non comporta costi aggiuntivi. È tuttavia possibile continuare a sostenere i costi per l'uso del Azure Key Vault in cui è archiviata la chiave.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica delle funzionalità di sicurezza in Backup di Azure](security-overview.md)
