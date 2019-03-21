---
title: Configurare la replica di VM abilitate per Crittografia dischi di Azure (ADE) in Azure Site Recovery | Microsoft Docs
description: Questo articolo illustra come configurare la replica di VM ADE da un'area di Azure a un'altra mediante Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 27c1481314ba1dd77cdcf229842aeec7de3e4444
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58117456"
---
# <a name="replicate-azure-disk-encryption-ade-enabled-virtual-machines-to-another-azure-region"></a>Eseguire la replica di macchine virtuali abilitate per Crittografia dischi di Azure (ADE) in un'altra area di Azure

Questo articolo illustra come abilitare la replica di VM abilitate per Crittografia dischi di Azure (ADE) da un'area di Azure a un'altra.

>[!NOTE]
>Azure Site Recovery supporta attualmente solo macchine virtuali di Azure che eseguono il sistema operativo Windows e che sono [enabled abilitate per la crittografia con l'app Azure AD](https://aka.ms/ade-aad-app).
>

## <a name="required-user-permissions"></a>Autorizzazioni utente necessarie
Azure Site Recovery richiede che l'utente sia autorizzato a creare l'insieme di credenziali delle chiavi nell'area di destinazione e a copiare le chiavi nell'area.

Per abilitare la replica di VM ADE dal portale, è necessario avere le autorizzazioni seguenti.
- Autorizzazioni dell'insieme di credenziali delle chiavi
    - list
    - Create
    - Get

-   Autorizzazioni di accesso al segreto dell'insieme di credenziali delle chiavi
    - Elenco
    - Create
    - Get

- Autorizzazioni di accesso alla chiave dell'insieme di credenziali delle chiavi (obbligatorie solo se le VM usano la chiave di crittografia delle chiavi per crittografare le chiavi di crittografia dei dischi)
    - Elenco
    - Get
    - Create
    - Crittografare il contenuto
    - Decrypt

Per gestire le autorizzazioni, passare alla risorsa relativa all'insieme di credenziali delle chiavi nel portale e aggiungere all'utente le autorizzazioni necessarie. Di seguito è illustrato come abilitarla per l'insieme di credenziali delle chiavi "ContosoWeb2Keyvault", che è l'area di origine.


-  Passare a "Home > Insiemi di credenziali delle chiavi > ContosoWeb2KeyVault > Criteri di accesso"

![Autorizzazioni per l'insieme di credenziali delle chiavi](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)



- Come si può vedere, non è impostata alcuna autorizzazione, pertanto occorre aggiungere l'autorizzazione menzionata sopra facendo clic su "Aggiungi nuovo" e specificando l'utente e l'autorizzazione.

![Autorizzazioni per l'insieme di credenziali delle chiavi](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Se l'utente che abilita il ripristino di emergenza non ha le autorizzazioni necessarie per copiare le chiavi, è possibile fornire all'amministratore della sicurezza lo script indicato di seguito con le autorizzazioni appropriate per copiare le chiavi e i segreti di crittografia nell'area di destinazione.

Vedere [questo articolo](#trusted-root-certificates-error-code-151066) per informazioni sulla risoluzione dei problemi relativi alle autorizzazioni.
>[!NOTE]
>Per abilitare la replica di VM ADE dal portale, è necessario avere almeno le autorizzazioni "List" per gli insiemi di credenziali delle chiavi, i segreti e le chiavi.
>

## <a name="copy-ade-keys-to-dr-region-using-powershell-script"></a>Copiare le chiavi ADE nell'area del ripristino di emergenza tramite script PowerShell

1. Aprire il codice script non elaborato CopyKeys in una finestra del browser facendo clic su [questo collegamento](https://aka.ms/ade-asr-copy-keys-code).
2. Copiare lo script in un file e assegnargli il nome Copy-keys.ps1.
2. Aprire l'applicazione Windows PowerShell e passare alla cartella in cui si trova il file.
3. Avviare Copy-keys.ps1.
4. Specificare le credenziali di accesso di Azure.
5. Selezionare la **sottoscrizione di Azure** delle VM.
6. Attendere che i gruppi di risorse vengano caricati e quindi selezionare il **gruppo di risorse** delle VM.
7. Selezionare le VM nell'elenco visualizzato. Nell'elenco vengono visualizzate solo le VM abilitate con Crittografia dischi di Azure.
8. Selezionare la **posizione di destinazione**.
9. **Insieme di credenziali delle chiavi di crittografia del Data Box Disk**: per impostazione predefinita, Azure Site Recovery crea un nuovo insieme di credenziali delle chiavi nell'area di destinazione il cui nome contiene il suffisso "asr" basato sulle chiavi di crittografia del disco della macchina virtuale di origine. Nel caso in cui l'insieme di credenziali delle chiavi creato da Azure Site Recovery esista già, verrà riutilizzato. Se necessario, è possibile selezionare nell'elenco un insieme di credenziali delle chiavi diverso.
10. **Insieme di credenziali delle chiavi di crittografia della chiave**: per impostazione predefinita, Azure Site Recovery crea un nuovo insieme di credenziali delle chiavi nell'area di destinazione il cui nome contiene il suffisso "asr" basato sulle chiavi di crittografia della chiave della macchina virtuale di origine. Nel caso in cui l'insieme di credenziali delle chiavi creato da Azure Site Recovery esista già, verrà riutilizzato. Se necessario, è possibile selezionare nell'elenco un insieme di credenziali delle chiavi diverso.

## <a name="enable-replication"></a>Abilitare la replica

Questa procedura presuppone che l'area di Azure primaria sia Asia orientale e l'area secondaria sia Asia sud-orientale.

1. Nell'insieme di credenziali fare clic su **+Replica**.
2. Annotare i campi seguenti:
    - **Origine**: punto di origine delle macchine virtuali, in questo caso **Azure**.
    - **Posizione di origine**: area di Azure da cui si vogliono proteggere le macchine virtuali. Per questa illustrazione, il percorso di origine sarà "Asia orientale"
    - **Modello di distribuzione**: modello di distribuzione di Azure per le macchine virtuali di origine.
    - **Sottoscrizione di origine**: la sottoscrizione a cui appartengono le macchine virtuali di origine. Può essere qualsiasi sottoscrizione che si trova nello stesso tenant di Azure Active Directory in cui è presente l'insieme di credenziali di Servizi di ripristino.
    - **Gruppo di risorse**: il gruppo di risorse a cui appartengono le macchine virtuali di origine. Tutte le macchine virtuali nel gruppo di risorse selezionato verranno elencate per la protezione nel passaggio successivo.

3. In **Macchine virtuali > Seleziona macchine virtuali** fare clic per selezionare tutte le VM da replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Fare quindi clic su **OK**.

4. In **Impostazioni** è possibile configurare le impostazioni del sito di destinazione:

    - **Posizione di destinazione**: posizione in cui verrà eseguita la replica dei dati delle macchine virtuali di origine. In base al percorso selezionato per le macchine virtuali, Site Recovery fornirà l'elenco di aree di destinazione idonee. È consigliabile mantenere il percorso di destinazione identico al percorso dell'insieme di credenziali di Servizi di ripristino.
    - **Sottoscrizione di destinazione**: sottoscrizione di destinazione usata per il ripristino di emergenza. Per impostazione predefinita, la sottoscrizione di destinazione sarà uguale alla sottoscrizione di origine.
    - **Gruppo di risorse di destinazione**: gruppo di risorse a cui tutte le macchine virtuali replicate appartengono. Per impostazione predefinita, Azure Site Recovery crea un nuovo gruppo di risorse nell'area di destinazione il cui nome presenta il suffisso "asr". Nel caso in cui il gruppo di risorse creato da Azure Site Recovery esista già, verrà riusato. È anche possibile scegliere di personalizzarlo, come illustrato nella sezione seguente. Il percorso del gruppo di risorse di destinazione può essere in qualsiasi area di Azure, ad eccezione di quella in cui sono ospitate le macchine virtuali di origine.
    - **Rete virtuale di destinazione**: per impostazione predefinita, Site Recovery crea una nuova rete virtuale con un nome con il suffisso "asr" nell'area di destinazione. Questa rete è mappata alla rete di origine ed è usata per protezione futura. [Altre informazioni](site-recovery-network-mapping-azure-to-azure.md) sul mapping di rete.
    - **Account di archiviazione di destinazione (se la VM di origine non usa dischi gestiti)**: per impostazione predefinita, Site Recovery crea un nuovo account di archiviazione di destinazione, che rispecchia la configurazione delle risorse di archiviazione della macchina virtuale di origine. Nel caso in cui l'account di archiviazione esista già, verrà riusato.
    - **Dischi gestiti di replica (se la VM di origine sfrutta dischi gestiti)**: Site Recovery crea dischi gestiti di replica nell'area di destinazione per eseguire il mirroring dei dischi gestiti della VM di origine con lo stesso tipo di archiviazione (Standard o Premium) del disco gestito della VM di origine.
    - **Account di archiviazione della cache**: Site Recovery necessita di un account di archiviazione aggiuntivo, definito account di archiviazione della cache, nell'area di origine. Tutte le modifiche apportate nelle VM di origine vengono registrate e inviate all'account di archiviazione della cache prima della replica nella posizione di destinazione.
    - **Set di disponibilità**: per impostazione predefinita, Azure Site Recovery crea un nuovo set di disponibilità nell'area di destinazione, con un nome con suffisso "asr". Nel caso in cui il set di disponibilità creato da Azure Site Recovery esista già, verrà riusato.
    - **Insieme di credenziali delle chiavi di crittografia del Data Box Disk**: per impostazione predefinita, Azure Site Recovery crea un nuovo insieme di credenziali delle chiavi nell'area di destinazione il cui nome contiene il suffisso "asr" basato sulle chiavi di crittografia del disco della macchina virtuale di origine. Nel caso in cui l'insieme di credenziali delle chiavi creato da Azure Site Recovery esista già, verrà riutilizzato.
    - **Insieme di credenziali delle chiavi di crittografia della chiave**: per impostazione predefinita, Azure Site Recovery crea un nuovo insieme di credenziali delle chiavi nell'area di destinazione il cui nome contiene il suffisso "asr" basato sulle chiavi di crittografia della chiave della macchina virtuale di origine. Nel caso in cui l'insieme di credenziali delle chiavi creato da Azure Site Recovery esista già, verrà riutilizzato.
    - **Criteri di replica**: Site Recovery definisce le impostazioni per la cronologia della conservazione del punto di recupero e per una frequenza snapshot coerente con l'app. Per impostazione predefinita, Azure Site Recovery crea nuovi criteri di replica con impostazioni predefinite di 24 ore per la conservazione del punto di recupero e di 60 minuti per la frequenza snapshot coerente con l'app.



## <a name="customize-target-resources"></a>Personalizzare le risorse di destinazione

È possibile modificare le impostazioni di destinazione predefinite usate da Site Recovery.


1. Fare clic su **Personalizza:** accanto a "Sottoscrizione di destinazione" per modificare la sottoscrizione di destinazione predefinita. Selezionare la sottoscrizione dall'elenco di tutte le sottoscrizioni disponibili nello stesso tenant di Azure Active Directory (AAD).

2. Fare clic su **Personalizza** accanto a Gruppo di risorse, Rete, Archiviazione e Set di disponibilità per modificare le impostazioni predefinite seguenti:
    - In **Gruppo di risorse di destinazione** è possibile selezionare il gruppo di risorse dall'elenco di tutti i gruppi di risorse nel percorso di destinazione all'interno della sottoscrizione.
    - In **Rete virtuale di destinazione:**, selezionare la rete dall'elenco di tutte le reti virtuali nel percorso di destinazione.
    - In **Set di disponibilità** è possibile aggiungere le impostazioni del set di disponibilità per la macchina virtuale, se sono parte di un set di disponibilità nell'area di origine.
    - In **Account di archiviazione di destinazione**, selezionare l'account da usare.


2. Fare clic su **Personalizza** accanto a Impostazioni crittografia per modificare le impostazioni predefinite seguenti:
   - In **Insieme di credenziali delle chiavi di crittografia del disco di destinazione** selezionare l'insieme di credenziali delle chiavi di crittografia del disco di destinazione dall'elenco degli insiemi di credenziali delle chiavi presenti nella posizione di destinazione della sottoscrizione.
     - In **Insieme di credenziali delle chiavi di crittografia della chiave di destinazione** selezionare l'insieme di credenziali delle chiavi di crittografia della chiave di destinazione dall'elenco degli insiemi di credenziali delle chiavi presenti nella posizione di destinazione della sottoscrizione.

3. Fare clic su **Crea risorsa di destinazione** > **Abilitazione della replica**.
4. Una volta abilitate le macchine virtuali per la replica è possibile controllare lo stato di integrità della macchina virtuale in **Elementi replicati**

>[!NOTE]
>Durante la replica iniziale, l'aggiornamento dello stato può richiedere tempo, senza mostrare alcun progresso. Per ottenere l'ultimo stato del processo, fare clic sul pulsante **Aggiorna**.
>

## <a name="update-target-vm-encryption-settings"></a>Aggiornare le impostazioni di crittografia della VM di destinazione
Nello scenari seguenti sarà necessario aggiornare le impostazioni di crittografia della VM di destinazione.
  - È stata abilitata la replica di Site Recovery nella macchina virtuale e in un secondo momento è stato abilitato il servizio Crittografia dischi di Azure nella VM di origine.
  - È stata abilitata la replica di Site Recovery nella macchina virtuale e in un secondo momento sono state modificate la chiave di crittografia dei dischi e/o la chiave di crittografia delle chiavi nella VM di origine.

È possibile usare [lo script](#copy-ade-keys-to-dr-region-using-powershell-script) per copiare le chiavi di crittografia nell'area di destinazione e quindi aggiornare le impostazioni di crittografia di destinazione in **Insieme di credenziali di Servizi di ripristino -> Elemento replicato -> Proprietà -> Calcolo e rete**.

![update-ade-settings](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="trusted-root-certificates-error-code-151066"></a>Risolvere i problemi relativi alle autorizzazioni degli insiemi di credenziali delle chiavi durante la replica di macchine virtuali da Azure ad Azure

**Causa 1:** è possibile che sia stato selezionato dall'area di destinazione un insieme di credenziali delle chiavi già creato che non ha le autorizzazioni necessarie.
Se si seleziona un insieme di credenziali delle chiavi già creato nell'area di destinazione invece di crearlo automaticamente con Azure Site Recovery, assicurarsi che abbia le autorizzazioni necessarie come menzionato in precedenza.</br>
*Ad esempio*: un utente cerca di replicare una macchina virtuale che ha un insieme di credenziali delle chiavi nell'area di origine denominato "ContososourceKeyvault".
L'utente ha tutte le autorizzazioni sull'insieme di credenziali delle chiavi dell'area di origine, ma durante la protezione seleziona un insieme di credenziali delle chiavi già creato denominato "ContosotargetKeyvault", che non ha l'autorizzazione necessaria, quindi la protezione genera un errore.</br>
**Come correggere:** Passare a "Home > Insiemi di credenziali delle chiavi > ContososourceKeyvault > Criteri di accesso" e aggiungere le autorizzazioni come illustrato sopra. 

**Causa 2:** è possibile che sia stato selezionato dall'area di destinazione un insieme di credenziali delle chiavi già creato che non ha le autorizzazioni di decrittografia-crittografia.
Se si seleziona un insieme di credenziali delle chiavi già creato nell'area di destinazione invece di crearlo automaticamente con Azure Site Recovery, assicurarsi che l'utente abbia le autorizzazioni di decrittografia-crittografia in caso si debbano crittografare le chiavi anche nell'area di origine.</br>
*Ad esempio*: un utente cerca di replicare una macchina virtuale che ha un insieme di credenziali delle chiavi nell'area di origine denominato "ContososourceKeyvault".
L'utente ha tutte le autorizzazioni sull'insieme di credenziali delle chiavi dell'area di origine, ma durante la protezione seleziona un insieme di credenziali delle chiavi già creato denominato "ContosotargetKeyvault", che non ha l'autorizzazione necessaria per decrittografare e crittografare.</br>
**Come correggere:** Passare a "Home > Insiemi di credenziali delle chiavi > ContososourceKeyvault > Criteri di accesso" e aggiungere le autorizzazioni in Autorizzazioni chiave > Operazioni crittografiche.

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](site-recovery-test-failover-to-azure.md) sull'esecuzione di un failover di test.
