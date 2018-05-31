---
title: Aggiornamento automatico del servizio Mobility di Azure al ripristino di emergenza di Azure | Microsoft Docs
description: Viene fornita una panoramica dell'aggiornamento automatico del servizio Mobility che viene usato per la replica delle macchine virtuali di Azure tramite Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: rajanaki
ms.openlocfilehash: 45f2e2927f699769bb385038c04d4dd23e075a9a
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
ms.locfileid: "32779686"
---
# <a name="automatic-update-of-mobility-service-extension-in-azure-to-azure-replication"></a>Aggiornamento automatico dell'estensione del servizio Mobility in una replica di Azure

Azure Site Recovery ha una frequenza di rilascio mensile in cui vengono aggiunti miglioramenti delle funzionalità esistenti o nuove funzionalità e vengono risolti gli eventuali problemi noti. Ciò significa che è necessario pianificare la distribuzione delle patch con cadenza mensile affinché il servizio rimanga aggiornato. Per evitare il sovraccarico associato all'aggiornamento, gli utenti possono scegliere di consentire a Site Recovery di gestire gli aggiornamenti dei componenti. Come descritto in dettaglio nel [riferimento dell'architettura](azure-to-azure-architecture.md) per il ripristino di emergenza da Azure ad Azure, il servizio Mobility viene installato in tutte le macchine virtuali di Azure per cui è abilitata la replica durante la replica delle macchine virtuali da un'area di Azure a un'altra. In questo documento viene illustrato quanto segue:

- Come funziona l'aggiornamento automatico
- Abilitare gli aggiornamenti automatici
- Problemi comuni e risoluzione dei problemi
 
## <a name="how-does-automatic-update-work"></a>Come funziona l'aggiornamento automatico

Quando si consente a Site Recovery di gestire gli aggiornamenti, viene distribuito un runbook globale (usato dai servizi di Azure) tramite un account di automazione che viene creato nella stessa sottoscrizione dell'insieme di credenziali. Un account di automazione viene usato per un insieme di credenziali specifico. Il runbook verifica per quali macchine virtuali in un insieme di credenziali sono attivi gli aggiornamenti automatici e avvia un aggiornamento dell'estensione del servizio Mobility se è disponibile una versione più recente. La pianificazione predefinita del runbook avviene ogni giorno alle 12:00 in base al fuso orario dell'area geografica della macchina virtuale replicata. Se necessario, è anche possibile modificare la pianificazione del runbook tramite l'account di automazione. 

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

![vault-toggle-autuo-update](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important] 
> Se si sceglie *Allow ASR to manage* (Consenti ad Azure Site Recovery di gestire), l'impostazione viene applicata a tutte le macchine virtuali nell'insieme di credenziali corrispondente.


> [!Note] 
> Entrambe le opzioni segnalano con quale account di automazione vengono gestiti gli aggiornamenti. Se è la prima volta che si abilita questa funzionalità in un insieme di credenziali, verrà creato un nuovo account di automazione. Tutte le operazioni di replica successive nello stesso insieme di credenziali useranno l'account precedentemente creato.

## <a name="common-issues--troubleshooting"></a>Problemi comuni e risoluzione dei problemi

In caso di problemi con gli aggiornamenti automatici, verrà visualizzata una notifica in "Problemi comuni" nel dashboard dell'insieme di credenziali. 

Se si tenta di abilitare gli aggiornamenti automatici e l'operazione non dà esito positivo, vedere di seguito per la risoluzione dei problemi.

**Errore**: non si hanno le autorizzazioni per creare un account RunAs di Azure (entità servizio) e concedere il ruolo di collaboratore all'entità servizio. 
- Azione consigliata: verificare che all'account connesso sia assegnato il ruolo di collaboratore e ripetere l'operazione.
 
Quando gli aggiornamenti automatici sono attivati, la maggior parte dei problemi può essere risolta dal servizio Site Recovery e richiede di fare clic sul pulsante **Ripara**.

![repair-button](./media/azure-to-azure-autoupdate/repair.png)

Se il pulsante Ripara non è disponibile, fare riferimento al messaggio di errore visualizzato nel riquadro delle impostazioni dell'estensione.

 - **Errore**: l'account RunAs non ha l'autorizzazione per accedere alla risorsa dei servizi di ripristino.

    **Azione consigliata**: eliminare e [ricreare l'account RunAs](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account) o verificare che l'applicazione Azure Active Directory dell'account RunAs di Automazione abbia accesso alla risorsa dei servizi di ripristino.

- **Errore**: impossibile trovare l'account RunAs. È possibile che uno degli elementi seguenti (applicazione di Azure Active Directory, entità servizio, ruolo, asset di certificato di Automazione, asset di connessione di Automazione) o l'identificazione personale non siano identici tra certificato e connessione. 

    **Azione consigliata**: eliminare e [ricreare l'account RunAs](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account).
