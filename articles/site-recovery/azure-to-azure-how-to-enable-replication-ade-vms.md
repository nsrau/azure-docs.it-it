---
title: Configurare la replica per le macchine virtuali basate su crittografia dischi di Azure in Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come configurare la replica di macchine virtuali basate su crittografia dischi di Azure da un'area di Azure a un'altra con Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 4943b730bb46ee00200d84faf95a7ccb069d3aa8
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678956"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Eseguire la replica di macchine virtuali abilitate per crittografia dischi di Azure in un'altra area di Azure

Questo articolo descrive come replicare le macchine virtuali basate su crittografia dischi di Azure da un'area di Azure a un altro.

>[!NOTE]
>Azure Site Recovery supporta attualmente solo macchine virtuali di Azure che eseguono un sistema operativo Windows e che si trovano [abilitata per la crittografia con Azure Active Directory (Azure AD)](https://aka.ms/ade-aad-app).

## <a name="required-user-permissions"></a>Autorizzazioni utente necessarie
Site Recovery richiede che l'utente disponga delle autorizzazioni per creare l'insieme di credenziali delle chiavi nelle chiavi di area geografica e copia di destinazione all'area.

Per abilitare la replica di macchine virtuali con crittografia del disco abilitata dal portale di Azure, l'utente richiede le autorizzazioni seguenti:

- Autorizzazioni dell'insieme di credenziali delle chiavi
    - Elenco
    - Create
    - Get

-   Autorizzazioni di accesso al segreto dell'insieme di credenziali delle chiavi
    - Elenco
    - Create
    - Get

- Autorizzazioni per insieme di credenziali delle chiavi (obbligatorio solo se le macchine virtuali usano una chiave di crittografia per crittografare le chiavi di crittografia del disco)
    - Elenco
    - Get
    - Create
    - Crittografare il contenuto
    - Decrypt

Per gestire le autorizzazioni, passare alla risorsa insieme di credenziali delle chiavi nel portale. Aggiungere le autorizzazioni necessarie per l'utente. Nell'esempio seguente viene illustrato come abilitare le autorizzazioni per l'insieme di credenziali delle chiavi *ContosoWeb2Keyvault*, ovvero nell'area di origine.

1. Passare a **casa** > **Keyvaults** > **ContosoWeb2KeyVault > Criteri di accesso**.

   ![Finestra autorizzazioni insieme di credenziali delle chiavi](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Si noterà che esistono autorizzazioni utente. Selezionare **Aggiungi nuova**. Immettere le informazioni utente e le autorizzazioni.

   ![autorizzazioni di Key Vault](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Se l'utente che abilita il ripristino di emergenza (DR) non dispone delle autorizzazioni per copiare le chiavi, un amministratore della sicurezza che dispone delle autorizzazioni appropriate possa usare lo script seguente per copiare le chiavi e segreti di crittografia all'area di destinazione.

Per risolvere i problemi di autorizzazioni, fare riferimento a [problemi di autorizzazione dell'insieme di credenziali della chiave](#trusted-root-certificates-error-code-151066) più avanti in questo articolo.

>[!NOTE]
>Per abilitare la replica delle macchine virtuali con crittografia del disco abilitata dal portale, è necessario almeno "List" le autorizzazioni per l'insiemi di credenziali delle chiavi, segreti e chiavi.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Copiare le chiavi di crittografia dei dischi nell'area di ripristino di emergenza usando lo script di PowerShell

1. [Aprire il codice di script non elaborati "CopyKeys"](https://aka.ms/ade-asr-copy-keys-code).
2. Copiare lo script in un file e denominarlo **copia keys.ps1**.
3. Aprire l'applicazione di Windows PowerShell e passare alla cartella in cui è stato salvato il file.
4. Eseguire keys.ps1 di copia.
5. Specificare le credenziali di Azure per l'accesso.
6. Selezionare la **sottoscrizione di Azure** delle VM.
7. Attendere che i gruppi di risorse caricare e quindi selezionare il **gruppo di risorse** delle macchine virtuali.
8. Selezionare le macchine virtuali nell'elenco visualizzato. Solo le macchine virtuali abilitate per la crittografia del disco sono presenti nell'elenco.
9. Selezionare il **percorso di destinazione**.

    - **Insiemi di credenziali chiave di crittografia del disco**
    - **Insiemi di credenziali chiave di crittografia della chiave**

   Per impostazione predefinita, Site Recovery crea un nuovo insieme di credenziali delle chiavi nell'area di destinazione. Nome dell'insieme di credenziali ha un suffisso "asr" basato sulle chiavi di crittografia del disco della macchina virtuale di origine. Se un insieme di credenziali delle chiavi esiste già che è stato creato da Site Recovery, verrà riusato. Selezionare un insieme di credenziali chiave diverso nell'elenco se necessario.

## <a name="enable-replication"></a>Abilitare la replica

Per questo esempio, l'area di Azure primaria sia Asia orientale e l'area secondaria sia Asia sud-orientale.

1. Nell'insieme di credenziali, selezionare **+ replica**.
2. Si notino i campi seguenti.
    - **Origine**: punto di origine delle macchine virtuali, in questo caso **Azure**.
    - **Posizione di origine**: L'area di Azure in cui si desidera proteggere le macchine virtuali. Per questo esempio, il percorso di origine è "Asia orientale".
    - **Modello di distribuzione**: Il modello di distribuzione di Azure di macchine virtuali di origine.
    - **Sottoscrizione di origine**: la sottoscrizione a cui appartengono le macchine virtuali di origine. Può trattarsi di qualsiasi sottoscrizione che è nello stesso tenant di Azure Active Directory come l'insieme di credenziali di servizi di ripristino.
    - **Gruppo di risorse**: il gruppo di risorse a cui appartengono le macchine virtuali di origine. Vengono elencate tutte le macchine virtuali nel gruppo di risorse selezionato per la protezione nel passaggio successivo.

3. Nelle **macchine virtuali** > **selezionare le macchine virtuali**, selezionare ogni macchina virtuale che si vuole replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Quindi selezionare **OK**.

4. Nelle **impostazioni**, è possibile configurare le seguenti impostazioni sito di destinazione.

    - **Percorso di destinazione**: posizione in cui verrà eseguita la replica dei dati delle macchine virtuali di origine. Site Recovery fornisce un elenco delle aree di destinazione appropriate in base alla posizione del computer selezionato. È consigliabile usare la stessa posizione come percorso dell'insieme di credenziali di servizi di ripristino.
    - **Sottoscrizione di destinazione**: La sottoscrizione di destinazione che viene usata per il ripristino di emergenza. Per impostazione predefinita, la sottoscrizione di destinazione è uguale a quella di origine.
    - **Gruppo di risorse di destinazione**: gruppo di risorse a cui tutte le macchine virtuali replicate appartengono. Per impostazione predefinita, Site Recovery crea un nuovo gruppo di risorse nell'area di destinazione. Il nome Ottiene il suffisso "asr". Se un gruppo di risorse esiste già che è stato creato da Azure Site Recovery, verrà riusato. È anche possibile scegliere di personalizzarlo, come illustrato nella sezione seguente. Il percorso del gruppo di risorse di destinazione può essere qualsiasi area di Azure tranne l'area in cui sono ospitate le macchine virtuali di origine.
    - **Rete virtuale di destinazione**: Per impostazione predefinita, Site Recovery crea una nuova rete virtuale nell'area di destinazione. Il nome Ottiene il suffisso "asr". È mappata alla rete di origine e utilizzato per la protezione futura. [Altre informazioni](site-recovery-network-mapping-azure-to-azure.md) sul mapping di rete.
    - **Gli account di archiviazione di destinazione (se la VM di origine non usa dischi gestiti)**: Per impostazione predefinita, Site Recovery crea un nuovo account di archiviazione di destinazione mediante la riproduzione di configurazione dell'archiviazione della macchina virtuale di origine. Se esiste già un account di archiviazione, verrà riusato.
    - **Dischi gestiti di replica (se la VM di origine Usa dischi gestiti)**: Site Recovery Crea dischi gestiti di replica nell'area di destinazione per eseguire il mirroring dischi gestiti della VM di origine dello stesso tipo di archiviazione (standard o premium) come dischi gestiti della VM di origine.
    - **Account di archiviazione della cache**: Site Recovery necessita di un account di archiviazione aggiuntivo denominato *memorizza nella cache di archiviazione* nell'area di origine. Tutte le modifiche apportate nelle VM di origine sono registrate e inviate all'account di archiviazione della cache. Si sta quindi replicati al percorso di destinazione.
    - **Set di disponibilità**: Per impostazione predefinita, Site Recovery crea un nuovo set di disponibilità nell'area di destinazione. Il nome con suffisso "asr". Se un set di disponibilità che è stato creato da Site Recovery già esiste, verrà riusato.
    - **Insieme di credenziali delle chiavi di crittografia del Data Box Disk**: Per impostazione predefinita, Site Recovery crea un nuovo insieme di credenziali delle chiavi nell'area di destinazione. Include un suffisso "asr" basato sulle chiavi di crittografia del disco della macchina virtuale di origine. Se un insieme di credenziali delle chiavi creato da Azure Site Recovery già esiste, verrà riusato.
    - **Insieme di credenziali delle chiavi di crittografia della chiave**: Per impostazione predefinita, Site Recovery crea un nuovo insieme di credenziali delle chiavi nell'area di destinazione. Il nome con suffisso "asr" basato su chiavi di crittografia della chiave della macchina virtuale di origine. Se non esiste un insieme di credenziali già creato da Azure Site Recovery, verrà riusato.
    - **Criteri di replica**: Definisce le impostazioni per la cronologia della conservazione del punto di ripristino e la frequenza snapshot coerenti con l'app. Per impostazione predefinita, Site Recovery crea nuovi criteri di replica con impostazioni predefinite di *24 ore* conservazione del punto di ripristino e *60 minuti* per la frequenza snapshot coerenti con l'app.

## <a name="customize-target-resources"></a>Personalizzare le risorse di destinazione

Seguire questi passaggi per modificare le impostazioni di destinazione predefinite di Site Recovery.

1. Selezionare **Personalizza** accanto a "Sottoscrizione di destinazione" per modificare la sottoscrizione di destinazione predefinito. Selezionare la sottoscrizione dall'elenco delle sottoscrizioni disponibili nel tenant di Azure AD.

2. Selezionare **Personalizza** accanto a "gruppo di risorse, rete, archiviazione e set di disponibilità" per modificare le impostazioni predefinite seguenti:
    - Per la **gruppo di risorse di destinazione**, selezionare il gruppo di risorse dall'elenco dei gruppi di risorse nel percorso di destinazione della sottoscrizione.
    - Per la **rete virtuale di destinazione**, selezionare la rete da un elenco di reti virtuali nel percorso di destinazione.
    - Per la **set di disponibilità**, è possibile aggiungere le impostazioni di set di disponibilità per la macchina virtuale, se sono parte di un set di disponibilità nell'area di origine.
    - Per la **gli account di archiviazione di destinazione**, selezionare l'account da usare.

2. Selezionare **Personalizza** accanto a "Impostazioni crittografia" per modificare le impostazioni predefinite seguenti:
   - Per la **credenziali delle chiavi di crittografia di destinazione disco**, selezionare la destinazione del disco crittografia chiave dell'insieme di credenziali dall'elenco di insiemi di credenziali delle chiavi nel percorso di destinazione della sottoscrizione.
   - Per la **insieme di credenziali delle chiave di crittografia destinazione**, selezionare l'insieme di credenziali chiave di destinazione chiave di crittografia dall'elenco di insiemi di credenziali delle chiavi nel percorso di destinazione della sottoscrizione.

3. Selezionare **Crea risorsa di destinazione** > **abilitazione replica**.
4. Dopo che le macchine virtuali abilitate per la replica, è possibile controllare lo stato di integrità delle VM sotto **elementi replicati**.

>[!NOTE]
>Durante la replica iniziale, lo stato potrebbe richiedere del tempo per aggiornare, senza mostrare alcun progresso apparente. Fare clic su **Aggiorna** per ottenere lo stato più recente.

## <a name="update-target-vm-encryption-settings"></a>Aggiornare le impostazioni di crittografia della VM di destinazione
Negli scenari seguenti, verrà richiesto di aggiornare le impostazioni di crittografia della macchina virtuale di destinazione:
  - È abilitata la replica di Site Recovery nella macchina virtuale. In un secondo momento, è abilitata la crittografia del disco nella macchina virtuale di origine.
  - È abilitata la replica di Site Recovery nella macchina virtuale. In un secondo momento, è stato modificato la chiave di crittografia del disco o una chiave di crittografia della chiave nella macchina virtuale di origine.

È possibile usare [uno script](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) per copiare le chiavi di crittografia all'area di destinazione e quindi aggiornare le impostazioni di crittografia di destinazione nel **insieme di credenziali di Recovery services** > *elementoreplicato*  >  **Delle proprietà** > **calcolo e rete**.

![Finestra di dialogo Impostazioni di aggiornamento ADE](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a id="trusted-root-certificates-error-code-151066"></a>Risolvere i problemi di autorizzazione dell'insieme di credenziali chiave durante la replica delle macchine Virtuali di Azure ad Azure

**Causa 1:** Potrebbe essere selezionati dall'area di destinazione un già creato chiave dell'insieme di credenziali che non dispone delle autorizzazioni necessarie anziché consentire a Site Recovery di crearne uno. Assicurarsi che sia l'insieme di credenziali delle chiavi di richiedere le autorizzazioni, come descritto in precedenza.

*Ad esempio*: Si tenta di replicare una macchina virtuale con istanza di key vault *ContososourceKeyvault* su un'area di origine.
Tutte le autorizzazioni si attiva l'insieme di credenziali chiave di origine area. Ma durante la protezione, si seleziona l'insieme di chiavi già creato ContosotargetKeyvault, che non dispone delle autorizzazioni. Si verifica un errore.

**Come correggere:** Passare a **casa** > **Keyvaults** > **ContososourceKeyvault** > **i criteri di accesso** e aggiungere le autorizzazioni appropriate.

**Causa 2:** Si potrebbe essere stata selezionata dall'area di destinazione un già creato chiave dell'insieme di credenziali che non dispone di decrittografia-crittografare autorizzazioni anziché consentire a Site Recovery di crearne uno. Assicurarsi di aver crittografare decrypt autorizzazioni se si sta anche crittografare la chiave nell'area di origine.</br>

*Ad esempio*: Si tenta di replicare una macchina virtuale con un insieme di credenziali delle chiavi *ContososourceKeyvault* sull'area di origine. L'insieme di credenziali chiave di origine area sono presenti tutte le necessarie autorizzazioni. Ma durante la protezione, si seleziona l'insieme di chiavi già creato ContosotargetKeyvault, che non dispone delle autorizzazioni per decrittografare e crittografare. Si verifica un errore.</br>

**Come correggere:** Passare a **casa** > **Keyvaults** > **ContososourceKeyvault** > **i criteri di accesso**. Aggiungere le autorizzazioni in **autorizzazioni chiave** > **operazioni crittografiche**.

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](site-recovery-test-failover-to-azure.md) sull'esecuzione di un failover di test.
