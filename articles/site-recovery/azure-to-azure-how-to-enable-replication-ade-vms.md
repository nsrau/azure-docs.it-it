---
title: Configurare la replica per le macchine virtuali abilitate per crittografia dischi di Azure in Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come configurare la replica per le macchine virtuali abilitate per crittografia dischi di Azure da un'area di Azure a un'altra usando Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 25f8c19ad1c574f6a3d84d50d911b4da6b52cfc3
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516512"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Replicare le macchine virtuali abilitate per crittografia dischi di Azure in un'altra area di Azure

Questo articolo descrive come replicare le macchine virtuali abilitate per crittografia dischi di Azure da un'area di Azure a un'altra.

>[!NOTE]
>Azure Site Recovery attualmente supporta solo le macchine virtuali di Azure che eseguono un sistema operativo Windows e [abilitate per la crittografia con Azure Active Directory (Azure ad)](https://aka.ms/ade-aad-app).

## <a id="required-user-permissions"></a>Autorizzazioni utente necessarie
Site Recovery richiede che l'utente disponga delle autorizzazioni per la creazione dell'insieme di credenziali delle chiavi nell'area di destinazione e la copia delle chiavi dall'area di origine all'insieme di credenziali delle chiavi dell'area di destinazione.

Per abilitare la replica delle macchine virtuali abilitate per la crittografia del disco dalla portale di Azure, l'utente deve disporre delle autorizzazioni seguenti per l' **area di origine e** per gli insiemi di credenziali delle chiavi dell'area di destinazione.

- Autorizzazioni dell'insieme di credenziali delle chiavi
    - Elencare, creare e ottenere
    
- Autorizzazioni di accesso al segreto dell'insieme di credenziali delle chiavi
    - Operazioni di gestione dei segreti
        - Get, List e set
    
- Autorizzazioni della chiave di Key Vault (obbligatoria solo se le VM usano la chiave di crittografia della chiave per crittografare le chiavi di crittografia del disco)
    - Operazioni di gestione delle chiavi
        - Ottenere, elencare e creare
    - Operazioni crittografiche
        - Decrittografia e crittografia

Per gestire le autorizzazioni, passare alla risorsa di Key Vault nel portale. Aggiungere le autorizzazioni necessarie per l'utente. L'esempio seguente illustra come abilitare le autorizzazioni per l'insieme di credenziali delle chiavi *ContosoWeb2Keyvault*, che si trova nell'area di origine.

1. Passare a **Home** > **Vaults** > **ContosoWeb2KeyVault > criteri di accesso**.

   ![Finestra delle autorizzazioni di Key Vault](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. È possibile notare che non sono disponibili autorizzazioni utente. Selezionare **Aggiungi nuova**. Immettere le informazioni sull'utente e sulle autorizzazioni.

   ![Autorizzazioni dell'insieme di credenziali delle credenziali](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Se l'utente che Abilita il ripristino di emergenza non dispone delle autorizzazioni per la copia delle chiavi, un amministratore della sicurezza che dispone delle autorizzazioni appropriate può usare lo script seguente per copiare i segreti e le chiavi di crittografia nell'area di destinazione.

Per risolvere i problemi relativi alle autorizzazioni, vedere l'argomento relativo alle autorizzazioni di [Key Vault](#trusted-root-certificates-error-code-151066) più avanti in questo articolo.

>[!NOTE]
>Per abilitare la replica delle macchine virtuali abilitate per la crittografia del disco dal portale, sono necessarie almeno le autorizzazioni "list" per gli insiemi di credenziali delle chiavi, i segreti e le chiavi.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Copiare le chiavi di crittografia del disco nell'area di ripristino di emergenza usando lo script di PowerShell

1. [Aprire il codice dello script non elaborato "CopyKeys"](https://aka.ms/ade-asr-copy-keys-code).
2. Copiare lo script in un file e denominarlo **Copy-Keys. ps1**.
3. Aprire l'applicazione Windows PowerShell e passare alla cartella in cui è stato salvato il file.
4. Eseguire copy-Keys. ps1.
5. Fornire le credenziali di Azure per l'accesso.
6. Selezionare la **sottoscrizione di Azure** delle VM.
7. Attendere il caricamento dei gruppi di risorse e quindi selezionare il **gruppo di risorse** delle macchine virtuali.
8. Selezionare le VM dall'elenco visualizzato. Nell'elenco sono presenti solo le macchine virtuali abilitate per la crittografia del disco.
9. Selezionare il **percorso di destinazione**.

    - **Insiemi di credenziali delle chiavi di crittografia del disco**
    - **Key Vault chiave di crittografia**

   Per impostazione predefinita, Site Recovery crea un nuovo insieme di credenziali delle chiavi nell'area di destinazione. Il nome dell'insieme di credenziali presenta un suffisso "ASR" basato sulle chiavi di crittografia del disco della VM di origine. Se un insieme di credenziali delle chiavi esiste già creato da Site Recovery, viene riutilizzato. Se necessario, selezionare un insieme di credenziali delle chiavi diverso dall'elenco.

## <a name="enable-replication"></a>Abilita replica

Per questo esempio, l'area di Azure primaria è Asia orientale e l'area secondaria è Asia orientale sud.

1. Nell'insieme di credenziali selezionare **+ replica**.
2. Si notino i campi seguenti.
    - **Origine**: punto di origine delle macchine virtuali, in questo caso **Azure**.
    - **Posizione di origine**: Area di Azure in cui si desidera proteggere le macchine virtuali. Per questo esempio, il percorso di origine è "Asia orientale".
    - **Modello di distribuzione**: Il modello di distribuzione di Azure dei computer di origine.
    - **Sottoscrizione di origine**: la sottoscrizione a cui appartengono le macchine virtuali di origine. Può trattarsi di qualsiasi sottoscrizione che si trovi nello stesso tenant di Azure Active Directory dell'insieme di credenziali di servizi di ripristino.
    - **Gruppo di risorse**: il gruppo di risorse a cui appartengono le macchine virtuali di origine. Tutte le macchine virtuali nel gruppo di risorse selezionato sono elencate per la protezione nel passaggio successivo.

3. In **macchine** > virtuali**selezionare macchine virtuali**, selezionare tutte le VM che si desidera replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Quindi selezionare **OK**.

4. In **Impostazioni**è possibile configurare le seguenti impostazioni del sito di destinazione.

    - **Percorso di destinazione**: posizione in cui verrà eseguita la replica dei dati delle macchine virtuali di origine. Site Recovery fornisce un elenco di aree di destinazione appropriate in base alla posizione del computer selezionato. Si consiglia di usare la stessa località del percorso dell'insieme di credenziali di servizi di ripristino.
    - **Sottoscrizione di destinazione**: Sottoscrizione di destinazione usata per il ripristino di emergenza. Per impostazione predefinita, la sottoscrizione di destinazione è uguale a quella di origine.
    - **Gruppo di risorse di destinazione**: gruppo di risorse a cui tutte le macchine virtuali replicate appartengono. Per impostazione predefinita, Site Recovery crea un nuovo gruppo di risorse nell'area di destinazione. Il nome ottiene il suffisso "ASR". Se esiste già un gruppo di risorse creato da Azure Site Recovery, viene riutilizzato. È anche possibile scegliere di personalizzarlo, come illustrato nella sezione seguente. Il percorso del gruppo di risorse di destinazione può essere qualsiasi area di Azure, ad eccezione dell'area in cui sono ospitate le macchine virtuali di origine.
    - **Rete virtuale di destinazione**: Per impostazione predefinita, Site Recovery crea una nuova rete virtuale nell'area di destinazione. Il nome ottiene il suffisso "ASR". Viene mappato alla rete di origine e usato per la protezione futura. [Altre informazioni](site-recovery-network-mapping-azure-to-azure.md) sul mapping di rete.
    - **Account di archiviazione di destinazione (se la VM di origine non usa dischi gestiti)** : Per impostazione predefinita, Site Recovery crea un nuovo account di archiviazione di destinazione simulando la configurazione dell'archiviazione della VM di origine. Se un account di archiviazione esiste già, viene riusato.
    - **Dischi gestiti di replica (se la macchina virtuale di origine usa dischi gestiti)** : Site Recovery crea nuovi dischi gestiti di replica nell'area di destinazione per eseguire il mirroring dei dischi gestiti della VM di origine dello stesso tipo di archiviazione (standard o Premium) dei dischi gestiti della VM di origine.
    - **Account di archiviazione della cache**: Site Recovery necessita di un account di archiviazione aggiuntivo denominato *archiviazione della cache* nell'area di origine. Tutte le modifiche apportate alle VM di origine vengono registrate e inviate all'account di archiviazione della cache. Vengono quindi replicati nel percorso di destinazione.
    - **Set di disponibilità**: Per impostazione predefinita, Site Recovery crea un nuovo set di disponibilità nell'area di destinazione. Il nome presenta il suffisso "ASR". Se un set di disponibilità creato da Site Recovery esiste già, viene riutilizzato.
    - **Insieme di credenziali delle chiavi di crittografia del Data Box Disk**: Per impostazione predefinita, Site Recovery crea un nuovo insieme di credenziali delle chiavi nell'area di destinazione. Ha un suffisso "ASR" basato sulle chiavi di crittografia del disco della VM di origine. Se un insieme di credenziali delle chiavi creato da Azure Site Recovery esiste già, viene riusato.
    - **Insieme di credenziali delle chiavi di crittografia della chiave**: Per impostazione predefinita, Site Recovery crea un nuovo insieme di credenziali delle chiavi nell'area di destinazione. Il nome presenta un suffisso "ASR" basato sulle chiavi di crittografia della chiave della VM di origine. Se un insieme di credenziali delle chiavi creato da Azure Site Recovery esiste già, viene riusato.
    - **Criteri di replica**: Definisce le impostazioni per la cronologia di conservazione dei punti di ripristino e la frequenza di snapshot coerenti con l'app. Per impostazione predefinita, Site Recovery crea un nuovo criterio di replica con impostazioni predefinite di *24 ore* per la conservazione del punto di ripristino e *60 minuti* per la frequenza degli snapshot coerenti con l'app.

## <a name="customize-target-resources"></a>Personalizzare le risorse di destinazione

Per modificare le impostazioni di destinazione predefinite Site Recovery, attenersi alla seguente procedura.

1. Selezionare **Personalizza** accanto a "sottoscrizione di destinazione" per modificare la sottoscrizione di destinazione predefinita. Selezionare la sottoscrizione dall'elenco di sottoscrizioni disponibili nel tenant del Azure AD.

2. Selezionare **Personalizza** accanto a "gruppo di risorse, rete, archiviazione e set di disponibilità" per modificare le impostazioni predefinite seguenti:
    - Per **gruppo di risorse di destinazione**selezionare il gruppo di risorse dall'elenco dei gruppi di risorse nel percorso di destinazione della sottoscrizione.
    - In **rete virtuale di destinazione**selezionare la rete da un elenco di reti virtuali nel percorso di destinazione.
    - Per il **set di disponibilità**, è possibile aggiungere le impostazioni del set di disponibilità alla VM, se sono parte di un set di disponibilità nell'area di origine.
    - Per **account di archiviazione di destinazione**selezionare l'account da usare.

2. Selezionare **Personalizza** accanto a "impostazioni di crittografia" per modificare le impostazioni predefinite seguenti:
   - Per l'insieme di credenziali delle **chiavi di crittografia del disco**di destinazione, selezionare l'insieme di credenziali delle chiavi di crittografia del disco nell'elenco degli insiemi di credenziali delle chiavi nel percorso di destinazione della sottoscrizione.
   - Per l'insieme di credenziali delle chiavi di **crittografia**chiave di destinazione, selezionare l'insieme di credenziali chiave di crittografia della chiave dall'elenco degli insiemi di credenziali delle chiavi nel percorso di destinazione della sottoscrizione.

3. Selezionare **Crea risorsa** > di destinazione**Abilita replica**.
4. Quando le macchine virtuali sono abilitate per la replica, è possibile controllare lo stato di integrità delle macchine virtuali in **elementi replicati**.

>[!NOTE]
>Durante la replica iniziale, l'aggiornamento dello stato potrebbe richiedere del tempo, senza lo stato di avanzamento. Fare clic su **Aggiorna** per ottenere lo stato più recente.

## <a name="update-target-vm-encryption-settings"></a>Aggiornare le impostazioni di crittografia della VM di destinazione
Negli scenari seguenti verrà richiesto di aggiornare le impostazioni di crittografia della macchina virtuale di destinazione:
  - È stata abilitata la replica Site Recovery nella macchina virtuale. Successivamente, è stata abilitata la crittografia del disco nella macchina virtuale di origine.
  - È stata abilitata la replica Site Recovery nella macchina virtuale. Successivamente, è stata modificata la chiave di crittografia del disco o la chiave di crittografia della chiave nella macchina virtuale di origine.

È possibile usare [uno script](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) per copiare le chiavi di crittografia nell'area di destinazione e quindi aggiornare le impostazioni di crittografia > di destinazione nell'insieme di credenziali di servizi di ripristino**Proprietà**  >  *elemento* > replicato **Calcolo e rete**.

![Finestra di dialogo Aggiorna impostazioni ADE](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a id="trusted-root-certificates-error-code-151066"></a>Risolvere i problemi di autorizzazione di Key Vault durante la replica di VM da Azure ad Azure

Azure Site Recovery richiede almeno l'autorizzazione di lettura per l'insieme di credenziali delle chiavi dell'area di origine e l'autorizzazione di scrittura nell'insieme di credenziali delle chiavi dell'area di destinazione per leggere il segreto e copiarlo nell'insieme di credenziali delle chiavi dell'area 

**Causa 1:** Non si dispone dell'autorizzazione "GET" nell'insieme di credenziali delle chiavi dell' **area di origine** per leggere le chiavi. </br>
**Come correggere:** Indipendentemente dal fatto che l'utente sia o meno un amministratore della sottoscrizione, è importante avere l'autorizzazione Get per l'insieme di credenziali delle chiavi.

1. Passare a Key Vault dell'area di origine, che in questo esempio è "ContososourceKeyvault" > **criteri di accesso** 
2. In **Seleziona entità** Aggiungi il nome utente, ad esempio:dradmin@contoso.com""
3. In **autorizzazioni chiave** selezionare Get 
4. In **autorizzazione segreta** selezionare Get 
5. Salvare i criteri di accesso

**Causa 2:** Non si dispone dell'autorizzazione necessaria per la scrittura delle chiavi nell'insieme di credenziali delle chiavi dell' **area di destinazione** . </br>

*Ad esempio*: Si tenta di replicare una macchina virtuale con *ContososourceKeyvault* di Key Vault in un'area di origine.
Sono disponibili tutte le autorizzazioni per l'insieme di credenziali delle chiavi dell'area di origine. Tuttavia, durante la protezione, si seleziona l'insieme di credenziali delle chiavi già creato ContosotargetKeyvault, che non ha le autorizzazioni. Si è verificato un errore.

Autorizzazione richiesta nell'insieme di credenziali delle [chiavi di destinazione](#required-user-permissions)

**Come correggere:** Passare a **Home** > **Vault** > ContosotargetKeyvaultcriteri > di**accesso** e aggiungere le autorizzazioni appropriate.

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](site-recovery-test-failover-to-azure.md) sull'esecuzione di un failover di test.
