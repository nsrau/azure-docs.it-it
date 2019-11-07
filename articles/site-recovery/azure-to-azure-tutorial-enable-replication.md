---
title: Configurare il ripristino di emergenza per le macchine virtuali di Azure in un'area di Azure secondaria con Azure Site Recovery
description: Informazioni su come configurare il ripristino di emergenza per macchine virtuali di Azure in un'area di Azure diversa usando il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: e46bc9e4fbb2b573338b8be43c38e658ebde05a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468042"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Configurare il ripristino di emergenza per le macchine virtuali di Azure

Il servizio [Azure Site Recovery](site-recovery-overview.md) favorisce l'attuazione della strategia di ripristino di emergenza gestendo e coordinando le operazioni di replica, failover e failback di computer locali e macchine virtuali di Azure.

Questa esercitazione illustra come configurare il ripristino di emergenza per le macchine virtuali di Azure replicandole da un'area di Azure a un'altra. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un insieme di credenziali di Servizi di ripristino
> * Verificare le impostazioni delle risorse di destinazione
> * Configurare la connettività di rete in uscita per le macchine virtuali
> * Abilitare la replica per una macchina virtuale

> [!NOTE]
> Questo articolo contiene le istruzioni per distribuire il ripristino di emergenza con le impostazioni più semplici. Per ottenere informazioni sulle impostazioni personalizzate, leggere gli articoli nella [sezione Procedure](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

- Esaminare [l'architettura e i componenti dello scenario](concepts-azure-to-azure-architecture.md).
- Prima di iniziare, verificare i [requisiti di supporto](site-recovery-support-matrix-azure-to-azure.md).

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Creare l'insieme di credenziali in tutte le aree, ad eccezione dell'area di origine.

1. Accedere a [portale di Azure](https://portal.azure.com) > **Servizi di ripristino**.
2. Nel menu del portale di Azure o nella pagina **Home** selezionare **Crea una risorsa**. Selezionare quindi **Strumenti di gestione** > **Backup e Site Recovery**.
3. In **Nome**specificare un nome descrittivo per identificare l'insieme di credenziali. Se è disponibile più di una sottoscrizione, selezionare quella appropriata.
4. Creare un gruppo di risorse o selezionarne uno esistente. Specificare un'area di Azure. Per verificare le aree supportate, vedere la sezione relativa alla disponibilità a livello geografico in [Prezzi di Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Per accedere rapidamente all'insieme di credenziali dal dashboard, fare clic su **Aggiungi al dashboard** e quindi su **Crea**.

   ![Nuovo insieme di credenziali](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   Il nuovo insieme di credenziali viene aggiunto al **Dashboard**, nella sezione **Tutte le risorse**, e nella pagina **Insiemi di credenziali dei servizi di ripristino** principale.

## <a name="verify-target-resource-settings"></a>Verificare le impostazioni delle risorse di destinazione

1. Verificare che la sottoscrizione di Azure in uso consenta la creazione di macchine virtuali nell'area di destinazione. Contattare il supporto tecnico per abilitare la quota necessaria.
2. Assicurarsi che la sottoscrizione offra risorse sufficienti per supportare macchine virtuali con dimensioni corrispondenti alle macchine virtuali di origine. Per la macchina virtuale di destinazione, Site Recovery sceglie la stessa dimensione o la dimensione più vicina possibile.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>Configurare la connettività di rete in uscita per le macchine virtuali

Per un corretto funzionamento di Site Recovery, è necessario modificare la connettività di rete in uscita dalle macchine virtuali che si vuole replicare.

> [!NOTE]
> Site Recovery non supporta l'uso di un proxy di autenticazione per controllare la connettività di rete.


### <a name="outbound-connectivity-for-urls"></a>Connettività in uscita per gli URL

Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, consentire l'accesso a questi URL.

| **URL** | **Dettagli** |
| ------- | ----------- |
| *.blob.core.windows.net | Consente la scrittura di dati dalla macchina virtuale nell'account di archiviazione della cache all'area di origine. |
| login.microsoftonline.com | Fornisce l'autenticazione e l'autorizzazione per gli URL del servizio Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Consente alla macchina virtuale di comunicare con il servizio Site Recovery. |
| *.servicebus.windows.net | Consente alla macchina virtuale di scrivere i dati di diagnostica e monitoraggio di Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Connettività in uscita per gli intervalli di indirizzi IP

Se si vuole controllare la connettività in uscita usando indirizzi IP invece di URL, autorizzare questi indirizzi per firewall basati su IP, proxy o regole dei gruppi di sicurezza di rete.

  - [Intervalli IP del data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653)
  - [Intervalli IP del data center di Microsoft Azure in Germania](https://www.microsoft.com/download/details.aspx?id=54770)
  - [Intervalli IP del data center di Microsoft Azure in Cina](https://www.microsoft.com/download/details.aspx?id=42064)
  - [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Indirizzi IP dell'endpoint di servizio di Site Recovery](https://aka.ms/site-recovery-public-ips)

Se si usa NSG è possibile creare una tag del servizio di archiviazione basato sulle regole NSG per l'area di origine. [Altre informazioni](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)

## <a name="verify-azure-vm-certificates"></a>Verificare i certificati della macchina virtuale di Azure

Controllare che nelle macchine virtuali da replicare siano presenti tutti i certificati radice più recenti. Se non sono disponibili, non è possibile registrare la macchina virtuale in Site Recovery per motivi di sicurezza.

- Per le macchine virtuali di Windows, installare tutti gli aggiornamenti di Windows più recenti nella macchina virtuale in modo che tutti i certificati radice trusted siano presenti nel computer. In un ambiente non connesso, seguire i processi di aggiornamento di Windows e di aggiornamento dei certificati standard per l'organizzazione.
- Per le macchine virtuali Linux, seguire le indicazioni fornite dal distributore di Linux per ottenere i certificati radice trusted più recenti e l'elenco di revoche di certificati nella macchina virtuale.

## <a name="set-permissions-on-the-account"></a>Impostare le autorizzazioni per l'account

Azure Site Recovery offre tre ruoli predefiniti per controllare le operazioni di gestione di Site Recovery.

- **Collaboratore di Site Recovery** - Questo ruolo ha tutte le autorizzazioni necessarie per gestire le operazioni di Azure Site Recovery in un insieme di credenziali di Servizi di ripristino. Un utente con questo ruolo, tuttavia, non può creare o eliminare un insieme di credenziali di Servizi di ripristino oppure assegnare diritti di accesso ad altri utenti. Questo ruolo è ideale per gli amministratori del ripristino di emergenza che possono abilitare e gestire il ripristino di emergenza per le applicazioni o per intere organizzazioni.

- **Operatore di Site Recovery** - Questo ruolo ha le autorizzazioni per eseguire e gestire le operazioni di failover e failback. Un utente con questo ruolo non può abilitare o disabilitare la replica, creare o eliminare insiemi di credenziali, registrare una nuova infrastruttura oppure assegnare diritti di accesso ad altri utenti. Questo ruolo è ideale per un operatore di ripristino di emergenza che può eseguire il failover di macchine virtuali o applicazioni quando richiesto dai proprietari delle applicazioni e dagli amministratori IT. In seguito alla risoluzione della situazione di emergenza, l'operatore di ripristino di emergenza può proteggere nuovamente le macchine virtuali ed eseguirne il failback.

- **Lettore di Site Recovery** - Questo ruolo ha le autorizzazioni per visualizzare tutte le operazioni di gestione di Site Recovery. Questo ruolo è ideale per un dirigente del monitoraggio IT che può controllare lo stato corrente di protezione e generare ticket di supporto.

Altre informazioni sui [ruoli predefiniti del Controllo degli accessi in base al ruolo di Azure](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication-for-a-vm"></a>Abilitare la replica per una macchina virtuale

### <a name="select-the-source"></a>Selezionare l'origine

1. In Insiemi di credenziali dei servizi di ripristino fare clic sul nome dell'insieme di credenziali e quindi selezionare **+Replica**.
2. In **Source** (Origine) selezionare **Azure**.
3. In **Percorso di origine** selezionare l'area di Azure di origine in cui le VM sono attualmente in esecuzione.
4. Selezionare la **sottoscrizione di origine** in cui sono in esecuzione le macchine virtuali. Può essere qualsiasi sottoscrizione che si trova nello stesso tenant di Azure Active Directory in cui è presente l'insieme di credenziali di Servizi di ripristino.
5. Selezionare il **gruppo di risorse di origine** e fare clic su **OK** per salvare le impostazioni.

    ![Impostare l'origine](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>Selezionare le macchine virtuali

Site Recovery recupera un elenco delle macchine virtuali associate alla sottoscrizione e al gruppo di risorse/servizio cloud.

1. In **Macchine virtuali** selezionare le macchine virtuali da replicare.
2. Fare clic su **OK**.

### <a name="configure-replication-settings"></a>Configurare le impostazioni di replica

Site Recovery crea le impostazioni predefinite e i criteri di replica per l'area di destinazione. È possibile modificare le impostazioni in base alle esigenze.

1. Fare clic su **Impostazioni** per visualizzare le impostazioni di destinazione e replica.
2. Per eseguire l'override delle impostazioni di destinazione predefinite, fare clic su **Personalizza** accanto a **Gruppo di risorse, rete, archiviazione e set di disponibilità**.

   ![Configurare le impostazioni](./media/azure-to-azure-tutorial-enable-replication/settings.png)


3. Personalizzare le impostazioni di destinazione, come riepilogato nella tabella.

    **Impostazione** | **Dettagli**
    --- | ---
    **Sottoscrizione di destinazione** | Per impostazione predefinita, la sottoscrizione di destinazione è uguale a quella di origine. Fare clic su "Personalizza" per selezionare una sottoscrizione di destinazione diversa nello stesso tenant di Azure Active Directory.
    **Posizione di destinazione** | area di destinazione usata per il ripristino di emergenza.<br/><br/> È consigliabile che il percorso di destinazione corrisponda al percorso dell'insieme di credenziali di Site Recovery.
    **Gruppo di risorse di destinazione** | gruppo di risorse nell'area di destinazione in cui si trovano le macchine virtuali di Azure dopo il failover.<br/><br/> Per impostazione predefinita, Site Recovery crea un nuovo gruppo di risorse nell'area di destinazione con suffisso "asr". Il percorso del gruppo di risorse di destinazione può essere in qualsiasi area, ad eccezione di quella in cui sono ospitate le macchine virtuali di origine.
    **Rete virtuale di destinazione** | rete nell'area di destinazione in cui si trovano le macchine virtuali dopo il failover.<br/><br/> Per impostazione predefinita, Site Recovery crea una nuova rete virtuale e subnet nell'area di destinazione con suffisso "asr".
    **Account di archiviazione della cache** | Site Recovery usa un account di archiviazione nell'area di origine. Le modifiche apportate alle macchine virtuali di origine vengono inviate a questo account prima della replica nel percorso di destinazione.<br/><br/> Se si usa un account di archiviazione della cache abilitato per il firewall, assicurarsi di selezionare **Consenti servizi Microsoft attendibili**. [Altre informazioni.](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
    **Account di archiviazione di destinazione (se la macchina virtuale di origine usa dischi non gestiti)** | per impostazione predefinita, Site Recovery crea un nuovo account di archiviazione nell'area di destinazione per eseguire il mirroring dell'account di archiviazione della macchina virtuale di origine.<br/><br/> Abilitare **Consenti servizi Microsoft attendibili** se si usa un account di archiviazione della cache abilitato per il firewall.
    **Dischi gestiti di replica (se la macchina virtuale di origine usa dischi gestiti)** : | per impostazione predefinita, Site Recovery crea dischi gestiti di replica nell'area di destinazione per eseguire il mirroring dei dischi gestiti della macchina virtuale di origine con lo stesso tipo di archiviazione (Standard o Premium) del disco gestito della macchina virtuale di origine. È possibile personalizzare solo Tipo di disco 
    **Set di disponibilità di destinazione** | per impostazione predefinita, Azure Site Recovery crea nell'area di destinazione un nuovo set di disponibilità il cui nome include il suffisso "asr" per la parte attinente alle macchine virtuali di un set di disponibilità nell'area di origine. Nel caso in cui il set di disponibilità creato da Azure Site Recovery esista già, verrà riusato.
    **Zone di disponibilità di destinazione** | per impostazione predefinita, Site Recovery assegna nell'area di origine lo stesso numero di zona dell'area di origine se l'area di destinazione supporta le zone di disponibilità.<br/><br/> Se l'area di destinazione non supporta le zone di disponibilità, per impostazione predefinita le macchine virtuali di destinazione vengono configurate come istanze singole.<br/><br/> Fare clic su **Personalizza** per configurare le macchine virtuali come parte di un set di disponibilità nell'area di destinazione.<br/><br/> Dopo avere abilitato la replica non è possibile modificare il tipo di disponibilità, ovvero l'istanza singola, il set di disponibilità o la zona di disponibilità. Per modificare il tipo di disponibilità, è necessario disabilitare e abilitare la replica.

4. Per personalizzare le impostazioni per i criteri di replica, fare clic su **Personalizza** accanto a **Criteri di replica** e modificare le impostazioni in base alle esigenze.

    **Impostazione** | **Dettagli**
    --- | ---
    **Criteri di replica** | nome dei criteri.
    **Conservazione del punto di ripristino** | per impostazione predefinita, Site Recovery conserva i punti di ripristino per 24 ore. È possibile configurare un valore compreso tra 1 e 72 ore.
    **Frequenza snapshot coerenti con l'applicazione** | per impostazione predefinita, Site Recovery accetta uno snapshot coerente con l'app ogni 4 ore. È possibile configurare un valore compreso tra 1 e 12 ore.<br/><br/> Uno snapshot coerente con l'app è uno snapshot temporizzato dei dati dell'applicazione all'interno della macchina virtuale. Il servizio Copia Shadow del volume assicura che lo stato dell'app nella macchina virtuale sia coerente quando viene creato lo snapshot.
    **Gruppo di replica** | se l'applicazione richiede la coerenza tra più macchine virtuali, è possibile creare un gruppo di replica per tali macchine virtuali. Per impostazione predefinita, le VM selezionate non fanno parte di gruppi di replica.

5. Per aggiungere VM a un gruppo di replica nuovo o esistente, in **Personalizza** selezionare **Sì** per la coerenza tra più macchine virtuali Fare quindi clic su **OK**. 

    >[!NOTE]
    >- Tutte le macchine virtuali in un gruppo di replica hanno punti di ripristino coerenti con l'arresto anomalo del sistema e coerenti con l'app quando si esegue il failover.
    >- L'abilitazione della coerenza su più macchine virtuali può avere un impatto sulle prestazioni del carico di lavoro poiché implica un uso intensivo della CPU. Deve essere usata solo se i computer eseguono lo stesso carico di lavoro ed è necessaria la coerenza tra più macchine virtuali.
    >- È possibile avere un massimo di 16 macchine virtuali in un gruppo di replica.
    >- Se si abilita la coerenza tra più macchine virtuali, i computer inclusi nel gruppo di replica comunicano tra loro sulla porta 20004. Verificare che nessun firewall blocchi le comunicazioni interne tra le macchine virtuali su questa porta.
    >- Per le macchine virtuali Linux in un gruppo di replica, verificare che il traffico in uscita sulla porta 20004 venga aperto manualmente in base alle indicazioni per la versione di Linux.



### <a name="configure-encryption-settings"></a>Configurare le impostazioni di crittografia

Se nella macchina virtuale di origine è abilitato il servizio Crittografia dischi di Azure, verificare le impostazioni.

1. Verificare le impostazioni:
    - **Insieme di credenziali delle chiavi di crittografia del Data Box Disk**: Per impostazione predefinita, Site Recovery crea un nuovo insieme di credenziali delle chiavi in base alle chiavi di crittografia del disco della macchina virtuale di origine, con suffisso "asr". Se l'insieme di credenziali delle chiavi esiste già, verrà riutilizzato.
    - **Insieme di credenziali delle chiavi di crittografia della chiave**: Per impostazione predefinita, Site Recovery crea un nuovo insieme di credenziali delle chiavi nell'area di destinazione. Il nome ha come suffisso "asr" e si basa sulle chiavi di crittografia del disco della macchina virtuale di origine. Se l'insieme di credenziali delle chiavi creato da Site Recovery esiste già, verrà riutilizzato.

2. Fare clic su **Personalizza** per selezionare gli insiemi di credenziali delle chiavi personalizzati.

>[!NOTE]
>Azure Site Recovery supporta attualmente solo macchine virtuali di Azure che eseguono i sistemi operativi Windows e che sono [abilitate per la crittografia con l'app Azure AD](https://aka.ms/ade-aad-app).
>

### <a name="track-replication-status"></a>Tenere traccia dello stato della replica

1. In **Impostazioni** fare clic su **Aggiorna** per ottenere lo stato più recente.
2. Monitorare l'avanzamento e tenere traccia dello stato come segue:
    - Tenere traccia dello stato del processo **Abilita protezione** in **Impostazioni** > **Processi** > **Processi di Site Recovery**.
    - In **Impostazioni** > **Elementi replicati** è possibile visualizzare lo stato delle VM e lo stato della replica iniziale. Fare clic sulla VM per visualizzare nel dettaglio le rispettive impostazioni.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato il ripristino di emergenza per una macchina virtuale di Azure. Ora è possibile avviare un'analisi di ripristino di emergenza per verificare che il failover funzioni come previsto.

> [!div class="nextstepaction"]
> [Eseguire un'esercitazione sul ripristino di emergenza](azure-to-azure-tutorial-dr-drill.md)
