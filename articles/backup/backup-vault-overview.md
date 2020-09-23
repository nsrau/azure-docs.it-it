---
title: Panoramica degli insiemi di credenziali di backup
description: Panoramica degli insiemi di credenziali di backup.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 773152212ed831c0083cfdf912f45ece578d079f
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90997262"
---
# <a name="backup-vaults-overview"></a>Panoramica degli insiemi di credenziali di backup

Questo articolo descrive le funzionalità di un insieme di credenziali per il backup. Un insieme di credenziali di backup è un'entità di archiviazione di Azure che ospita i dati di backup per determinati carichi di lavoro più recenti supportati da backup di Azure. È possibile usare insiemi di credenziali di backup per conservare i dati di backup per vari servizi di Azure, ad esempio i server di database di Azure per PostgreSQL e i carichi di lavoro più recenti supportati da backup di Azure. Gli insiemi di credenziali di backup consentono di organizzare facilmente i dati di backup, riducendo al minimo il sovraccarico di gestione. Gli insiemi di credenziali di backup si basano sul modello di Azure Resource Manager di Azure, che offre funzionalità come:

- **Funzionalità ottimizzate per la protezione dei dati di backup**: con gli insiemi di credenziali di backup, backup di Azure offre funzionalità di sicurezza per proteggere i backup nel cloud. Queste funzionalità di sicurezza garantiscono la protezione dei backup e ripristinano i dati in modo sicuro anche se i server di produzione e di backup vengono compromessi. [Scopri di più](backup-azure-security-feature.md)

- **Controllo degli accessi in base al ruolo o RBAC **: il controllo degli accessi in base al ruolo consente un controllo della gestione degli accessi con granularità fine in Azure. [Azure offre diversi ruoli predefiniti](../role-based-access-control/built-in-roles.md) mentre Backup di Azure dispone di tre [ruoli predefiniti per la gestione dei punti di ripristino](backup-rbac-rs-vault.md). Gli insiemi di credenziali di backup sono compatibili con il controllo degli accessi in base al ruolo, che limita l'accesso di backup e ripristino al set definito di ruoli utente. [Scopri di più](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>Impostazioni di archiviazione nell'insieme di credenziali per il backup

Un insieme di credenziali per il backup è un'entità che archivia i backup e i punti di ripristino creati nel corso del tempo. L'insieme di credenziali per il backup contiene anche i criteri di backup associati alle macchine virtuali protette.

- Backup di Azure gestisce automaticamente lo spazio di archiviazione per l'insieme di credenziali. Quando si crea l'insieme di credenziali per il backup, scegliere la ridondanza di archiviazione che soddisfa le esigenze aziendali.

- Per altre informazioni sulla ridondanza di archiviazione, vedere questi articoli sulla ridondanza [geografica](../storage/common/storage-redundancy.md#geo-redundant-storage) e [locale](../storage/common/storage-redundancy.md#locally-redundant-storage) .

## <a name="encryption-settings-in-the-backup-vault"></a>Impostazioni di crittografia nell'insieme di credenziali per il backup

Questa sezione illustra le opzioni disponibili per la crittografia dei dati di backup archiviati nell'insieme di credenziali per il backup.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Crittografia dei dati di backup tramite chiavi gestite dalla piattaforma

Per impostazione predefinita, tutti i dati vengono crittografati usando chiavi gestite dalla piattaforma. Non è necessario eseguire alcuna azione esplicita da parte dell'utente per abilitare questa crittografia. Si applica a tutti i carichi di lavoro di cui viene eseguito il backup nell'insieme di credenziali di backup.

## <a name="create-a-backup-vault"></a>Creare un insieme di credenziali per il backup

Un insieme di credenziali per il backup è un'entità di gestione che archivia i punti di ripristino creati nel tempo e fornisce un'interfaccia per eseguire operazioni correlate al backup. come l'esecuzione di backup su richiesta, l'esecuzione di ripristini e la creazione di criteri di backup.

Per creare un insieme di credenziali per il backup, seguire questa procedura.

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo <https://portal.azure.com>.

### <a name="create-backup-vault"></a>Crea insieme di credenziali di backup

1. Digitare insiemi di credenziali di **backup** nella casella di ricerca.
1. In **Servizi**selezionare insiemi di credenziali per il **backup**.
1. Nella pagina insiemi di credenziali per il **backup** selezionare **Aggiungi**.
1. Nella **scheda nozioni di base**, in **Dettagli progetto**, verificare che sia selezionata la sottoscrizione corretta, quindi scegliere **Crea nuovo** gruppo di risorse. Immettere *myResourceGroup* come nome.

  ![Creare un nuovo gruppo di risorse](./media/backup-vault-overview/new-resource-group.png)

1. In **Dettagli istanza** *digitare l'insieme di credenziali* per il nome dell'insieme di credenziali per il **backup** e scegliere la propria area preferita, in questo caso *Stati Uniti orientali* per l' **area geografica**.
1. Ora scegliere la **ridondanza di archiviazione**. Non è possibile modificare la ridondanza di archiviazione dopo avere protetto gli elementi nell'insieme di credenziali.
1. Se si usa Azure come endpoint di archiviazione di backup primario, è consigliabile continuare a usare l'impostazione con **ridondanza geografica** predefinita.
1. Se non si usa Azure come endpoint di archiviazione di backup primario, scegliere l'opzione **Con ridondanza locale**, che riduce i costi di archiviazione di Azure.
1. Altre informazioni sulla ridondanza [geografica](../storage/common/storage-redundancy.md#geo-redundant-storage) e [locale](../storage/common/storage-redundancy.md#locally-redundant-storage) .

  ![Scegliere la ridondanza di archiviazione](./media/backup-vault-overview/storage-redundancy.png)

1. Selezionare il pulsante Verifica e crea nella parte inferiore della pagina.

    ![Selezionare Verifica + crea](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>Eliminare un insieme di credenziali per il backup

Questa sezione descrive come eliminare un insieme di credenziali per il backup. Contiene le istruzioni per rimuovere le dipendenze e quindi eliminare un insieme di credenziali.

### <a name="before-you-start"></a>Prima di iniziare

Non è possibile eliminare un insieme di credenziali per il backup con una delle seguenti dipendenze:

- Non è possibile eliminare un insieme di credenziali che contiene origini dati protette, ad esempio i server di database di Azure per PostgreSQL.
- Non è possibile eliminare un insieme di credenziali che contiene i dati di backup.

Se si tenta di eliminare l'insieme di credenziali senza rimuovere le dipendenze, si verificheranno i messaggi di errore seguenti:

>Non è possibile eliminare l'insieme di credenziali per il backup perché sono presenti istanze di backup o criteri di backup esistenti nell'insieme di credenziali. Eliminare tutte le istanze di backup e i criteri di backup presenti nell'insieme di credenziali e quindi provare a eliminare l'insieme di credenziali.

### <a name="proper-way-to-delete-a-vault"></a>Modo corretto per eliminare un insieme di credenziali

>[!WARNING]
L'operazione seguente è distruttiva e non può essere annullata. Tutti i dati di backup e gli elementi di backup associati al server protetto verranno eliminati definitivamente. Procedere con cautela.

Per eliminare correttamente un insieme di credenziali, è necessario seguire i passaggi nell'ordine seguente:

- È necessario controllare per verificare se sono presenti elementi protetti:
  - Passare a **istanze di backup** nella barra di spostamento a sinistra. Tutti gli elementi elencati di seguito devono essere eliminati per primi.

Una volta completati questi passaggi, è possibile continuare a eliminare l'insieme di credenziali.

### <a name="delete-the-backup-vault"></a>Eliminare l'insieme di credenziali per il backup

Quando nell'insieme di credenziali non sono presenti altri elementi, selezionare **Elimina** nel dashboard dell'insieme di credenziali. Verrà visualizzato un messaggio di conferma che chiede se si vuole eliminare l'insieme di credenziali.

![Elimina insieme di credenziali](./media/backup-vault-overview/delete-vault.png)

1. Selezionare **Sì** per verificare che si vuole eliminare l'insieme di credenziali. L'insieme di credenziali viene eliminato. Il portale torna al menu **nuovo** del servizio.

## <a name="monitor-and-manage-the-backup-vault"></a>Monitorare e gestire l'insieme di credenziali per il backup

Questa sezione illustra come usare il Dashboard panoramica dell' **insieme** di credenziali di backup per monitorare e gestire gli insiemi di credenziali di backup. Il riquadro Panoramica contiene due riquadri: **processi** e **istanze**.

![Dashboard panoramica](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>Gestire le istanze di backup

Nel riquadro **Jobs (processi** ) si ottiene una vista riepilogativa di tutti i processi correlati al backup e al ripristino nell'insieme di credenziali per il backup. Selezionando uno dei numeri in questo riquadro è possibile visualizzare altre informazioni sui processi per un tipo di origine dati, un tipo di operazione e uno stato specifici.

![Istanze di backup](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>Gestire i processi di backup

Nel riquadro **istanze di backup** si ottiene una vista riepilogativa di tutte le istanze di backup nell'insieme di credenziali di backup. Selezionando uno dei numeri in questo riquadro è possibile visualizzare altre informazioni sulle istanze di backup per un determinato tipo di origine dati e lo stato di protezione.

![Processi di backup](./media/backup-vault-overview/backup-jobs.png)

## <a name="next-steps"></a>Passaggi successivi

- [Configurare il backup nei database PostgreSQL di Azure](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
