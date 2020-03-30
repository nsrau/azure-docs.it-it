---
title: Abilitare la replica per le macchine virtuali di Azure crittografate in Azure Site RecoveryEnable replication for encrypted Azure VMs in Azure Site Recovery
description: Questo articolo descrive come configurare la replica per le macchine virtuali abilitate per Crittografia disco di Azure da un'area di Azure a un'altra tramite Site Recovery.This article describes how to configure replication for Azure Disk Encryption-enabled VMs from an Azure region to another by using Site Recovery.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/08/2019
ms.author: sutalasi
ms.openlocfilehash: 3a59f137240eff2a3a68fa5547be8c6c25d3e5fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75772228"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Replicare le macchine virtuali abilitate per Crittografia dischi di Azure in un'altra area di AzureReplicate Azure Disk Encryption-enabled virtual machines to another Azure region

Questo articolo descrive come replicare le macchine virtuali di Azure con Azure Disk Encryption (ADE) abilitato, da un'area di Azure a un'altra.

>[!NOTE]
> Site Recovery supporta attualmente ADE, con e senza Azure Active Directory (AAD) per le macchine virtuali che eseguono sistemi operativi Windows e Linux.Site Recovery currently supports ADE, with and without Azure Active Directory (AAD) for VMs running Windows and Linux operating systems.  Per i computer che eseguono ADE 1.1 (senza AAD), le macchine virtuali devono utilizzare dischi gestiti. Le macchine virtuali con dischi non gestiti non sono supportate. Se si passa da ADE 0.1 (con AAD) a 1.1, è necessario disabilitare la replica e abilitare la replica per una macchina virtuale dopo aver abilitato 1.1.


## <a name="required-user-permissions"></a><a id="required-user-permissions"></a>Autorizzazioni utente necessarie
Site Recovery richiede che l'utente disponga delle autorizzazioni per creare l'insieme di credenziali delle chiavi nell'area di destinazione e copiare le chiavi dall'insieme di credenziali delle chiavi dell'area di origine all'insieme di credenziali delle chiavi dell'area di destinazione.

Per abilitare la replica delle macchine virtuali abilitate per Crittografia disco dal portale di Azure, l'utente deve disporre delle autorizzazioni seguenti per l'area di origine e gli insiemi di chiavi dell'area di **destinazione.**

- Autorizzazioni dell'insieme di credenziali delle chiavi
    - Lista, Crea e Ottieni
    
- Autorizzazioni di accesso al segreto dell'insieme di credenziali delle chiavi
    - Operazioni di gestione segreta
        - Get, Elenco e Set
    
- Key vault key permissions (required only if the VMs use key encryption key to encrypt disk encryption keys)
    - Operazioni di gestione delle chiaviKey Management Operations
        - Ottenere, elencare e creare
    - Operazioni di crittografia
        - Decrittografare e crittografare

Per gestire le autorizzazioni, passare alla risorsa dell'insieme di credenziali delle chiavi nel portale. Aggiungere le autorizzazioni necessarie per l'utente. Nell'esempio riportato di seguito viene illustrato come abilitare le autorizzazioni per l'insieme di credenziali della chiave *ContosoWeb2Keyvault*, che si trova nell'area di origine.

1. Passare a **Home** > **Keyvaults** > **ContosoWeb2KeyVault > criteri**di accesso .

   ![Finestra delle autorizzazioni dell'insieme di credenziali delle chiaviKey vault permissions window](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Si può vedere che non ci sono autorizzazioni utente. Selezionare **Aggiungi nuova**. Immettere le informazioni sull'utente e sulle autorizzazioni.

   ![Autorizzazioni Keyvault](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Se l'utente che abilita il ripristino di emergenza non dispone delle autorizzazioni per copiare le chiavi, un amministratore della sicurezza che dispone delle autorizzazioni appropriate può utilizzare lo script seguente per copiare i segreti e le chiavi di crittografia nell'area di destinazione.

Per risolvere i problemi relativi alle autorizzazioni, fare riferimento ai problemi di [autorizzazione dell'insieme](#trusted-root-certificates-error-code-151066) di credenziali delle chiavi più avanti in questo articolo.

>[!NOTE]
>Per abilitare la replica delle macchine virtuali abilitate per Crittografia disco dal portale, sono necessarie almeno le autorizzazioni "Elenco" per gli insiemi di credenziali delle chiavi, i segreti e le chiavi.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Copiare le chiavi di Crittografia disco nell'area di ripristino di emergenza tramite lo script di PowerShellCopy Disk Encryption keys to the DR region by using the PowerShell script

1. [Aprire il codice di script non elaborato "CopyKeys".](https://aka.ms/ade-asr-copy-keys-code)
2. Copiare lo script in un file e denominarlo **Copy-keys.ps1**.
3. Aprire l'applicazione Windows PowerShell e passare alla cartella in cui è stato salvato il file.
4. Eseguire Copy-keys.ps1.
5. Fornire le credenziali di Azure per l'accesso.
6. Selezionare la **sottoscrizione di Azure** delle VM.
7. Attendere il caricamento dei gruppi di risorse e quindi selezionare il **gruppo** di risorse delle macchine virtuali.
8. Selezionare le macchine virtuali dall'elenco visualizzato. Nell'elenco sono presenti solo le macchine virtuali abilitate per la crittografia del disco.
9. Selezionare il **percorso di destinazione**.

    - **Insiemi di credenziali di chiavi di crittografia del discoDisk encryption key vaults**
    - **Insiemi di credenziali delle chiavi di crittografia chiaveKey encryption key vaults**

   Per impostazione predefinita, Site Recovery crea un nuovo insieme di credenziali delle chiavi nell'area di destinazione. Il nome dell'insieme di credenziali ha un suffisso "asr" basato sulle chiavi di crittografia del disco della macchina virtuale di origine. Se esiste già un insieme di credenziali delle chiavi creato da Site Recovery, viene riutilizzato. Se necessario, selezionare un insieme di credenziali delle chiavi diverso dall'elenco.

## <a name="enable-replication"></a>Abilitare la replica

Per questo esempio, l'area primaria di Azure è l'Asia orientale e l'area secondaria è Asia sudorientale.

1. Nell'insieme di credenziali, **selezionare .**
2. Tenere presente i campi seguenti.
    - **Origine:** punto di origine delle macchine virtuali, in questo caso **Azure**.
    - **Percorso di origine:** l'area di Azure in cui si vogliono proteggere le macchine virtuali. Per questo esempio, il percorso di origine è "Asia orientale".
    - **Modello di distribuzione:** modello di distribuzione di Azure delle macchine di origine.
    - **Sottoscrizione di origine**: sottoscrizione a cui appartengono le macchine virtuali di origine. Può trattarsi di qualsiasi sottoscrizione presente nello stesso tenant di Azure Active Directory dell'insieme di credenziali dei servizi di ripristino.
    - **Gruppo di risorse:** gruppo di risorse a cui appartengono le macchine virtuali di origine. Tutte le macchine virtuali nel gruppo di risorse selezionato vengono elencate per la protezione nel passaggio successivo.

3. In **Macchine** > virtuali**Selezionare le macchine virtuali selezionare**ogni macchina virtuale che si vuole replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Selezionare quindi **OK**.

4. In **Impostazioni**è possibile configurare le seguenti impostazioni del sito di destinazione.

    - **Percorso di destinazione:** il percorso in cui verranno replicati i dati della macchina virtuale di origine. Site Recovery fornisce un elenco di aree di destinazione adatte in base alla posizione della macchina selezionata. Si consiglia di utilizzare la stessa posizione della posizione dell'insieme di credenziali dei servizi di ripristino.
    - **Sottoscrizione di**destinazione: sottoscrizione di destinazione usata per il ripristino di emergenza. Per impostazione predefinita, la sottoscrizione di destinazione è uguale a quella di origine.
    - **Gruppo di risorse di destinazione:** gruppo di risorse a cui tutte le macchine virtuali replicate appartengono. Per impostazione predefinita, Site Recovery crea un nuovo gruppo di risorse nell'area di destinazione. Il nome ottiene il suffisso "asr". Se esiste già un gruppo di risorse creato da Azure Site Recovery, viene riutilizzato. È inoltre possibile scegliere di personalizzarlo, come illustrato nella sezione seguente. La posizione del gruppo di risorse di destinazione può essere qualsiasi area di Azure, ad eccezione dell'area in cui sono ospitate le macchine virtuali di origine.
    - **Rete virtuale di destinazione:** per impostazione predefinita, Site Recovery crea una nuova rete virtuale nell'area di destinazione. Il nome ottiene il suffisso "asr". Viene mappato alla rete di origine e utilizzato per qualsiasi protezione futura. [Altre informazioni](site-recovery-network-mapping-azure-to-azure.md) sul mapping di rete.
    - Account di archiviazione di destinazione (se la macchina virtuale di **origine non usa dischi gestiti):** per impostazione predefinita, Site Recovery crea un nuovo account di archiviazione di destinazione inviando un'imitazione della configurazione di archiviazione della macchina virtuale di origine. Se esiste già un account di archiviazione, viene riutilizzato.
    - **Dischi gestiti di replica (se la macchina virtuale di origine utilizza dischi gestiti):** Site Recovery crea nuovi dischi gestiti di replica nell'area di destinazione per eseguire il mirroring dei dischi gestiti della macchina virtuale di origine dello stesso tipo di archiviazione (standard o premium) dei dischi gestiti della macchina virtuale di origine.
    - **Account di archiviazione cache:** Site Recovery richiede un account di archiviazione aggiuntivo denominato *cache storage* nell'area di origine. Tutte le modifiche nelle macchine virtuali di origine vengono rilevate e inviate all'account di archiviazione della cache. Vengono quindi replicati nel percorso di destinazione.
    - **Set di disponibilità:** per impostazione predefinita, Site Recovery crea un nuovo set di disponibilità nell'area di destinazione. Il nome ha il suffisso "asr". Se esiste già un set di disponibilità creato da Site Recovery, viene riutilizzato.
    - **Vault chiave di crittografia del disco:** per impostazione predefinita, Site Recovery crea un nuovo insieme di credenziali delle chiavi nell'area di destinazione. Ha un suffisso "asr" basato sulle chiavi di crittografia del disco della macchina virtuale di origine. Se esiste già un insieme di credenziali delle chiavi creato da Azure Site Recovery, viene riutilizzato.
    - **Insiemi**di credenziali delle chiavi di crittografia: per impostazione predefinita, Site Recovery crea un nuovo insieme di credenziali delle chiavi nell'area di destinazione. Il nome ha un suffisso "asr" basato sulle chiavi di crittografia della chiave della macchina virtuale di origine. Se esiste già un insieme di credenziali delle chiavi creato da Azure Site Recovery, viene riutilizzato.
    - **Criteri di replica:** definisce le impostazioni per la cronologia di conservazione dei punti di ripristino e la frequenza degli snapshot coerente con le app. Per impostazione predefinita, Site Recovery crea un nuovo criterio di replica con impostazioni predefinite di *24 ore* per la conservazione dei punti di ripristino e *60 minuti* per la frequenza degli snapshot coerente con l'app.

## <a name="customize-target-resources"></a>Personalizzare le risorse di destinazione

Seguire questi passaggi per modificare le impostazioni di destinazione predefinite di Site Recovery.

1. Selezionare **Personalizza** accanto a "Sottoscrizione di destinazione" per modificare la sottoscrizione di destinazione predefinita. Selezionare la sottoscrizione dall'elenco delle sottoscrizioni disponibili nel tenant di Azure AD.

2. Selezionare Personalizza accanto a "Gruppo di risorse, Rete, Archiviazione e Set di disponibilità" per modificare le impostazioni predefinite seguenti:Select **Customize** next to "Resource group, Network, Storage, and Availability sets" to modify the following default settings:
    - In Gruppo di **risorse di destinazione**selezionare il gruppo di risorse dall'elenco dei gruppi di risorse nel percorso di destinazione della sottoscrizione.
    - In **Rete virtuale**di destinazione selezionare la rete da un elenco di reti virtuali nel percorso di destinazione.
    - Per **Set di disponibilità**, è possibile aggiungere le impostazioni del set di disponibilità alla macchina virtuale, se fanno parte di un set di disponibilità nell'area di origine.
    - In **Account di archiviazione di destinazione**selezionare l'account da utilizzare.

2. Selezionare **Personalizza** accanto a "Impostazioni di crittografia" per modificare le seguenti impostazioni predefinite:
   - Per **Vault chiave di crittografia del disco**di destinazione , selezionare l'insieme di credenziali della chiave di crittografia del disco di destinazione dall'elenco degli insiemi di credenziali delle chiavi nella posizione di destinazione della sottoscrizione.
   - Per **Vault chiave**di crittografia di destinazione , selezionare l'insieme di credenziali della chiave di crittografia della chiave di destinazione dall'elenco degli insiemi di credenziali delle chiavi nella posizione di destinazione della sottoscrizione.

3. Selezionare **Crea risorsa** > di destinazione**Abilita replica**.
4. Dopo aver abilitato le macchine virtuali per la replica, è possibile controllare lo stato di integrità delle macchine virtuali in **Elementi replicati**.

>[!NOTE]
>Durante la replica iniziale, l'aggiornamento dello stato potrebbe richiedere del tempo, senza alcun progresso apparente. Fare clic su **Aggiorna** per ottenere lo stato più recente.

## <a name="update-target-vm-encryption-settings"></a>Aggiornare le impostazioni di crittografia della VM di destinazione
Negli scenari seguenti, sarà necessario aggiornare le impostazioni di crittografia della macchina virtuale di destinazione:In the following scenarios, you'll be required to update the target VM encryption settings:
  - È stata abilitata la replica di Site Recovery nella macchina virtuale. Successivamente, è stata abilitata la crittografia del disco nella macchina virtuale di origine.
  - È stata abilitata la replica di Site Recovery nella macchina virtuale. Successivamente, è stata modificata la chiave di crittografia del disco o la chiave di crittografia della chiave nella macchina virtuale di origine.

È possibile utilizzare [uno script](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) per copiare le chiavi di crittografia nell'area di destinazione e quindi aggiornare le impostazioni di crittografia di destinazione in**Proprietà** > dell'elemento > *replicato*servizi di **ripristino** > **Calcolo e Rete**.

![Finestra di dialogo Aggiorna impostazioni ADE](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="troubleshoot-key-vault-permission-issues-during--azure-to-azure-vm-replication"></a><a id="trusted-root-certificates-error-code-151066"></a>Risolvere i problemi di autorizzazione dell'insieme di credenziali delle chiavi durante la replica delle macchine virtuali da Azure ad AzureTroubleshoot key vault permission issues during Azure-to-Azure VM replication

Azure Site Recovery richiede almeno l'autorizzazione di lettura nell'insieme di credenziali delle chiavi dell'area di origine e l'autorizzazione di scrittura nell'insieme di credenziali delle chiavi dell'area di destinazione per leggere il segreto e copiarlo nell'insieme di credenziali delle chiavi dell'area di destinazione. 

**Causa 1:** Non si dispone dell'autorizzazione "GET" nell'insieme di chiavi **dell'area** di origine per leggere le chiavi. </br>
**Come risolvere:** Indipendentemente dal fatto che tu sia un amministratore della sottoscrizione o meno, è importante che tu abbia l'autorizzazione per ottenere l'autorizzazione per l'insieme di credenziali delle chiavi.

1. Passare all'insieme di credenziali delle chiavi dell'area di origine che in questo esempio è "ContososourceKeyvault" > criteri di **accessoGo** to source region Key vault which in this example is "ContososourceKeyvault" > Access policies 
2. In **Seleziona entità** aggiungere il propriodradmin@contoso.comnome utente, ad esempio: " " "
3. In **Autorizzazioni chiave** selezionare GET 
4. In **Autorizzazione segreta** selezionare GET 
5. Salvare i criteri di accesso

**Causa 2:** Non si dispone dell'autorizzazione necessaria nell'insieme di chiavi dell'area di **destinazione** per scrivere le chiavi. </br>

*Ad esempio:* si tenta di replicare una macchina virtuale con l'insieme di credenziali delle chiavi *ContososourceKeyvault* in un'area di origine.
Si dispone di tutte le autorizzazioni per l'insieme di credenziali delle chiavi dell'area di origine. Tuttavia, durante la protezione, si seleziona l'insieme di credenziali della chiave già creato ContosotargetKeyvault, che non dispone delle autorizzazioni. Si verifica un errore.

Autorizzazione richiesta nell'insieme di credenziali delle chiavi di [destinazionePermission](#required-user-permissions) required on target Key vault

**Come risolvere:** Passare a **Home** > **Keyvaults** > **ContosotargetKeyvault** > Access**criteri** e aggiungere le autorizzazioni appropriate.

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](site-recovery-test-failover-to-azure.md) sull'esecuzione di un failover di test.
