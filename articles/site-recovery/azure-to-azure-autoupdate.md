---
title: Aggiornamento automatico del servizio Mobility in Azure Site Recovery
description: Panoramica dell'aggiornamento automatico del servizio Mobility durante la replica di macchine virtuali di Azure con Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: rajanaki
ms.openlocfilehash: b57ce89979225015dc87bbfb17f9603897ef6d6b
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135837"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Aggiornamento automatico del servizio Mobility nella replica da Azure ad Azure

Azure Site Recovery usa una cadenza mensile per risolvere eventuali problemi e migliorare le funzionalità esistenti o aggiungerne di nuove. Per rimanere aggiornati con il servizio, è necessario pianificare la distribuzione delle patch ogni mese. Per evitare il sovraccarico associato a ogni aggiornamento, è possibile consentire Site Recovery di gestire gli aggiornamenti dei componenti.

Come indicato nell' [architettura di ripristino di emergenza da Azure ad Azure](azure-to-azure-architecture.md), il servizio Mobility viene installato in tutte le macchine virtuali (VM) di Azure in cui è abilitata la replica da un'area di Azure a un'altra. Quando si usano gli aggiornamenti automatici, ogni nuova versione aggiorna l'estensione del servizio Mobility.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Funzionamento degli aggiornamenti automatici

Quando si usa Site Recovery per gestire gli aggiornamenti, distribuisce un Runbook globale (usato dai servizi di Azure) tramite un account di automazione, creato nella stessa sottoscrizione dell'insieme di credenziali. Ogni insieme di credenziali usa un account di automazione. Per ogni VM in un insieme di credenziali, Runbook controlla gli aggiornamenti automatici attivi. Se è disponibile una versione più recente dell'estensione del servizio Mobility, viene installato l'aggiornamento.

La pianificazione predefinita del Runbook viene eseguita ogni giorno alle 12:00 del fuso orario della geografia della macchina virtuale replicata. È anche possibile modificare la pianificazione del Runbook tramite l'account di automazione.

> [!NOTE]
> A partire dall' [aggiornamento cumulativo 35](site-recovery-whats-new.md#updates-march-2019), è possibile scegliere un account di automazione esistente da usare per gli aggiornamenti. Prima dell'aggiornamento cumulativo 35, per impostazione predefinita Site Recovery creato l'account di automazione. È possibile selezionare questa opzione solo quando si Abilita la replica per una macchina virtuale. Non è disponibile per una macchina virtuale in cui è già abilitata la replica. L'impostazione selezionata si applica a tutte le macchine virtuali di Azure protette nello stesso insieme di credenziali.

Per attivare gli aggiornamenti automatici non è necessario riavviare le macchine virtuali di Azure o influire sulla replica in corso.

La fatturazione dei processi nell'account di automazione è basata sul numero di minuti di runtime del processo usati in un mese. L'esecuzione del processo richiede alcuni secondi a circa un minuto al giorno ed è coperta da unità gratuite. Per impostazione predefinita, 500 minuti sono inclusi come unità gratuite per un account di automazione, come illustrato nella tabella seguente:

| Unità gratuite incluse (ogni mese) | Prezzo |
|---|---|
| Runtime processo 500 minuti | ₹ 0,14 al minuto

## <a name="enable-automatic-updates"></a>Abilitare gli aggiornamenti automatici

Esistono diversi modi in cui Site Recovery possibile gestire gli aggiornamenti dell'estensione:

- [Gestire come parte del passaggio abilitazione della replica](#manage-as-part-of-the-enable-replication-step)
- [Attivare o disattivare le impostazioni di aggiornamento dell'estensione nell'insieme di credenziali](#toggle-the-extension-update-settings-inside-the-vault)
- [Gestire gli aggiornamenti manualmente](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>Gestire come parte del passaggio abilitazione della replica

Quando si Abilita la replica per una macchina virtuale a partire [dalla visualizzazione della macchina virtuale](azure-to-azure-quickstart.md) o dall'insieme di credenziali di [servizi di ripristino](azure-to-azure-how-to-enable-replication.md), è possibile consentire Site Recovery di gestire gli aggiornamenti per l'estensione Site Recovery o gestirli manualmente.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="Impostazioni estensione":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Attivare o disattivare le impostazioni di aggiornamento dell'estensione nell'insieme di credenziali

1. Dall'insieme di credenziali dei servizi di ripristino passare a **Gestisci**  >  **Site Recovery infrastruttura**.
1. In per le impostazioni di aggiornamento **dell'estensione per macchine virtuali di Azure**  >  **Extension Update Settings**  >  **consentire a Site Recovery di gestire**, selezionare **on**.

   Per gestire l'estensione manualmente, selezionare **disattivato**.

1. Selezionare **Salva**.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="Impostazioni di aggiornamento dell'estensione":::

> [!IMPORTANT]
> Quando si sceglie **consenti Site Recovery di gestire**, l'impostazione viene applicata a tutte le macchine virtuali nell'insieme di credenziali.

> [!NOTE]
> Entrambe le opzioni inviano una notifica all'utente dell'account di automazione usato per la gestione degli aggiornamenti. Se si usa questa funzionalità in un insieme di credenziali per la prima volta, per impostazione predefinita viene creato un nuovo account di automazione. In alternativa, è possibile personalizzare l'impostazione e scegliere un account di automazione esistente. Per abilitare la replica nello stesso insieme di credenziali viene usato l'account di automazione creato in precedenza. Attualmente, nel menu a discesa verranno elencati solo gli account di automazione che si trovano nello stesso gruppo di risorse dell'insieme di credenziali.

### <a name="manage-updates-manually"></a>Gestire gli aggiornamenti manualmente

1. Se sono presenti nuovi aggiornamenti per il servizio Mobility installato nelle VM, verrà visualizzata la notifica seguente: **è disponibile la nuova Site Recovery aggiornamento dell'agente di replica. Fare clic per installare.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="Finestra Elementi replicati":::

1. Selezionare la notifica per aprire la pagina di selezione della macchina virtuale.
1. Scegliere le macchine virtuali da aggiornare e quindi fare clic su **OK**. Il servizio di aggiornamento Mobility si avvierà per ogni macchina virtuale selezionata.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="Elenco delle macchine virtuali in Elementi replicati":::

## <a name="common-issues-and-troubleshooting"></a>Problemi comuni e risoluzione dei problemi

Se si verifica un problema con gli aggiornamenti automatici, verrà visualizzata una notifica di errore in **problemi di configurazione** nel dashboard dell'insieme di credenziali.

Se non è possibile abilitare gli aggiornamenti automatici, vedere gli errori comuni e le azioni consigliate seguenti:

- **Errore**: non si hanno le autorizzazioni per creare un account RunAs di Azure (entità servizio) e concedere il ruolo di collaboratore all'entità servizio.

  **Azione consigliata**: assicurarsi che l'account connesso sia assegnato come collaboratore e riprovare. Per altre informazioni sull'assegnazione di autorizzazioni, vedere la sezione autorizzazioni necessarie di [procedura: usare il portale per creare un'applicazione Azure ad e un'entità servizio che possano accedere alle risorse](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

  Per risolvere la maggior parte dei problemi dopo aver abilitato gli aggiornamenti automatici, selezionare **Ripristina**. Se il pulsante Ripristina non è disponibile, vedere il messaggio di errore visualizzato nel riquadro Impostazioni aggiornamento estensioni.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="Pulsante di ripristino del servizio Site Recovery nelle impostazioni di aggiornamento dell'estensione":::

- **Errore**: l'account RunAs non ha l'autorizzazione per accedere alla risorsa dei servizi di ripristino.

  **Azione consigliata**: eliminare e quindi [ricreare l'account RunAs](../automation/manage-runas-account.md). In alternativa, assicurarsi che l'applicazione Azure Active Directory dell'account RunAs di automazione possa accedere alla risorsa dei servizi di ripristino.

- **Errore**: impossibile trovare l'account RunAs. È possibile che uno degli elementi seguenti (applicazione di Azure Active Directory, entità servizio, ruolo, asset di certificato di Automazione, asset di connessione di Automazione) o l'identificazione personale non siano identici tra certificato e connessione.

  **Azione consigliata**: eliminare e quindi [ricreare l'account RunAs](../automation/manage-runas-account.md).

- **Errore**: il certificato RunAs di Azure usato dall'account di automazione sta per scadere.

  Il certificato autofirmato creato per l'account RunAs scade un anno dalla data di creazione. È possibile rinnovarlo in qualsiasi momento prima della scadenza. Se è stata effettuata l'iscrizione per le notifiche tramite posta elettronica, si riceveranno anche messaggi di posta elettronica quando è richiesta un'azione da parte dell'utente. Questo errore verrà visualizzato due mesi prima della data di scadenza e cambierà in un errore critico se il certificato è scaduto. Una volta scaduto il certificato, l'aggiornamento automatico non funzionerà finché non si rinnova lo stesso.

  **Azione consigliata**: per risolvere il problema, selezionare **Ripristina** , quindi **Rinnova certificato**.

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="rinnovo-certificato":::

  > [!NOTE]
  > Dopo aver rinnovato il certificato, aggiornare la pagina per visualizzare lo stato corrente.
