---
title: Aggiornamento automatico del servizio Mobility di Azure al ripristino di emergenza di Azure | Microsoft Docs
description: Viene fornita una panoramica dell'aggiornamento automatico del servizio Mobility durante la replica delle macchine virtuali di Azure tramite Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: f31fccd2bf6d0daae03b025b53a41a0fad4ce2ef
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210132"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Aggiornamento automatico del servizio Mobility in una replica di Azure

Azure Site Recovery ha una frequenza di rilascio mensile in cui vengono aggiunti miglioramenti delle funzionalità esistenti o nuove funzionalità e vengono risolti gli eventuali problemi noti. Ciò significa che è necessario pianificare la distribuzione delle patch ogni mese affinché il servizio rimanga aggiornato. Per evitare il sovraccarico associato all'aggiornamento, gli utenti possono scegliere di consentire a Site Recovery di gestire gli aggiornamenti dei componenti. Come descritto in dettaglio nel [riferimento dell'architettura](azure-to-azure-architecture.md) per il ripristino di emergenza da Azure ad Azure, il servizio Mobility viene installato in tutte le macchine virtuali di Azure per cui è abilitata la replica durante la replica delle macchine virtuali da un'area di Azure a un'altra. Una volta abilitato l'aggiornamento automatico, l'estensione del servizio Mobility viene aggiornata con il rilascio di ogni nuova versione. In questo documento viene illustrato quanto segue:

- Come funziona l'aggiornamento automatico
- Abilitare gli aggiornamenti automatici
- Problemi comuni e risoluzione dei problemi
 
## <a name="how-does-automatic-update-work"></a>Come funziona l'aggiornamento automatico

Quando si consente a Site Recovery di gestire gli aggiornamenti, viene distribuito un runbook globale (usato dai servizi di Azure) tramite un account di automazione che viene creato nella stessa sottoscrizione dell'insieme di credenziali. Un account di automazione viene usato per un insieme di credenziali specifico. Il runbook verifica per quali macchine virtuali in un insieme di credenziali sono attivi gli aggiornamenti automatici e avvia un aggiornamento dell'estensione del servizio Mobility se è disponibile una versione più recente. La pianificazione predefinita del runbook avviene ogni giorno alle 00.00 in base al fuso orario dell'area geografica della macchina virtuale replicata. Se necessario, è anche possibile modificare la pianificazione del runbook tramite l'account di automazione. 

> [!NOTE]
> Per abilitare gli aggiornamenti automatici non è necessario riavviare le macchine virtuali di Azure e la replica in corso non subisce alcun impatto.

> [!NOTE]
> La fatturazione per i processi usati dall'account di automazione è basata sul numero di minuti di esecuzione del processo usati in quel mese; per impostazione predefinita 500 minuti sono inclusi come unità gratuite per un account di automazione. L'esecuzione dei processi giornalieri va da **alcuni secondi a circa un minuto**; sono previsti **crediti gratuiti**.

UNITÀ GRATUITE INCLUSE (AL MESE)**   PREZZO tempo di esecuzione del processo    500 minuti a ₹ 0,14/minuto

## <a name="enable-automatic-updates"></a>Abilitare gli aggiornamenti automatici

È possibile scegliere di consentire a Site Recovery di gestire gli aggiornamenti nei modi seguenti:

- [Come parte del passaggio di abilitazione della replica](#as-part-of-the-enable-replication-step)
- [Attivare o disattivare le impostazioni di aggiornamento dell'estensione nell'insieme di credenziali](#toggle-the-extension-update-settings-inside-the-vault)

### <a name="as-part-of-the-enable-replication-step"></a>Come parte del passaggio di abilitazione della replica:

Quando si abilita la replica di una macchina virtuale a partire [dalla visualizzazione della macchina virtuale](azure-to-azure-quickstart.md) o [dall'insieme di credenziali di Servizi di ripristino](azure-to-azure-how-to-enable-replication.md), verrà visualizzata l'opzione per scegliere di consentire a Site Recovery di gestire gli aggiornamenti per l'estensione di Site Recovery o di eseguire l'operazione manualmente.

![enable-replication-auto-update](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Attivare o disattivare le impostazioni di aggiornamento dell'estensione nell'insieme di credenziali

1. Nell'insieme di credenziali passare a **Manage** (Gestisci) -> **Site Recovery Infrastructure** (Infrastruttura di Site Recovery)
2. In **For Azure virtual Machines** (Per le macchine virtuali di Azure) -> **Extension Update Settings** (Impostazioni di aggiornamento dell'estensione), fare clic sul pulsante Attiva/Disattiva per scegliere se consentire ad *Azure Site Recovery di gestire gli aggiornamenti* o se *gestirli manualmente*. Fare clic su **Save**.

![vault-toggle-auto-update](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important] 
> Se si sceglie *Allow ASR to manage* (Consenti ad Azure Site Recovery di gestire), l'impostazione viene applicata a tutte le macchine virtuali nell'insieme di credenziali corrispondente.


> [!Note] 
> Entrambe le opzioni segnalano con quale account di automazione vengono gestiti gli aggiornamenti. Se è la prima volta che si abilita questa funzionalità in un insieme di credenziali, verrà creato un nuovo account di automazione. Tutte le operazioni di replica successive nello stesso insieme di credenziali useranno l'account precedentemente creato.

### <a name="manage-manually"></a>Gestire manualmente

1. Se sono disponibili nuovi aggiornamenti per il servizio Mobility installato nella macchina virtuale di Azure, viene visualizzata la notifica "È disponibile un nuovo aggiornamento dell'agente di replica di Site Recovery. Fare clic per installare."

     ![Finestra Elementi replicati](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)
3. Selezionare la notifica per aprire la pagina di selezione della macchina virtuale.
4. Selezionare le macchine virtuali su cui aggiornare il servizio Mobility e quindi selezionare **OK**.

     ![Elenco delle macchine virtuali in Elementi replicati](./media/vmware-azure-install-mobility-service/update-okpng.png)

Verrà avviato il processo di aggiornamento del servizio Mobility per ognuna delle macchine virtuali selezionate.


## <a name="common-issues--troubleshooting"></a>Problemi comuni e risoluzione dei problemi

In caso di problemi con gli aggiornamenti automatici, verrà visualizzata una notifica in "Problemi comuni" nel dashboard dell'insieme di credenziali. 

Se si tenta di abilitare gli aggiornamenti automatici e l'operazione non dà esito positivo, vedere di seguito per la risoluzione dei problemi.

**Errore**: non si è autorizzati a creare un account RunAs di Azure (entità servizio) e a concedere il ruolo Collaboratore all'entità servizio. 
- Azione consigliata: verificare che all'account connesso sia assegnato il ruolo di "Collaboratore" e ripetere l'operazione. Fare riferimento a [questo](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) documento per altre informazioni sull'assegnazione delle autorizzazioni appropriate.
 
Quando gli aggiornamenti automatici sono attivati, la maggior parte dei problemi può essere risolta dal servizio Site Recovery e richiede di fare clic sul pulsante **Ripara**.

![repair-button](./media/azure-to-azure-autoupdate/repair.png)

Se il pulsante Ripara non è disponibile, fare riferimento al messaggio di errore visualizzato nel riquadro delle impostazioni dell'estensione.

 - **Errore**: l'account RunAs non ha l'autorizzazione per accedere alla risorsa dei servizi di ripristino.

    **Azione consigliata**: eliminare e [ricreare l'account RunAs](https://docs.microsoft.com/azure/automation/automation-create-runas-account) o verificare che l'applicazione Azure Active Directory dell'account RunAs di Automazione abbia accesso alla risorsa dei servizi di ripristino.

- **Errore**: impossibile trovare l'account RunAs. È possibile che uno degli elementi seguenti (applicazione di Azure Active Directory, entità servizio, ruolo, asset di certificato di Automazione, asset di connessione di Automazione) o l'identificazione personale non siano identici tra certificato e connessione. 

    **Azione consigliata**: eliminare e [ricreare l'account RunAs](https://docs.microsoft.com/azure/automation/automation-create-runas-account).
