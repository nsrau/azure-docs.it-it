---
title: Gestire l'account di Automazione di Azure
description: Questo articolo descrive come gestire la configurazione dell'account di Automazione, ad esempio il rinnovo del certificato, l'eliminazione e gli errori di configurazione.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: b9ccc46192950af212975304329497785b0babbd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="manage-azure-automation-account"></a>Gestire l'account di Automazione di Azure
A un certo punto prima della scadenza dell'account di Automazione, è necessario rinnovare il certificato. Se si ritiene che l'account RunAs sia stato compromesso, è possibile eliminarlo e crearlo nuovamente. Questa sezione illustra come eseguire tali operazioni.

## <a name="self-signed-certificate-renewal"></a>Rinnovo del certificato autofirmato
Il certificato autofirmato creato per l'account RunAs scade un anno dopo la data di creazione. È possibile rinnovarlo in qualsiasi momento prima della scadenza. Quando si procede al rinnovo, il certificato valido corrente viene mantenuto per evitare di influire negativamente su eventuali runbook messi in coda o in esecuzione, che eseguono l'autenticazione con l'account RunAs. Il certificato rimane valido fino alla relativa data di scadenza.

> [!NOTE]
> Se l'account RunAs di Automazione è stato configurato per l'uso di un certificato rilasciato dall'autorità di certificazione aziendale e si usa questa opzione, il certificato viene sostituito da un certificato autofirmato.

Per rinnovare il certificato, seguire questa procedura:

1. Nel Portale di Azure aprire l'account di Automazione.

2. Nell'**account di Automazione** 
3. nel riquadro delle **proprietà dell'account** in **Impostazioni account** selezionare **Account RunAs**.

    ![Riquadro delle proprietà dell'account di Automazione](media/automation-manage-account/automation-account-properties-pane.png)
3. Nella pagina delle proprietà **Account RunAs** selezionare l'account RunAs o l'account RunAs classico per cui si vuole rinnovare il certificato.

4. Nel riquadro **Proprietà** per l'account selezionato fare clic su **Rinnova certificato**.

    ![Rinnovare il certificato per l'account RunAs](media/automation-manage-account/automation-account-renew-runas-certificate.png)

5. Durante il rinnovamento del certificato, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Eliminare un account RunAs o un account RunAs classico
Questa sezione illustra come eliminare e creare nuovamente un account RunAs o un account RunAs classico. Quando si esegue questa azione, l'account di Automazione viene mantenuto. Dopo aver eliminato l'account RunAs o l'account RunAs classico, è possibile ricrearlo nel portale di Azure.

1. Nel Portale di Azure aprire l'account di Automazione.

2. Nella pagina **Account di Automazione** selezionare **Account RunAs**.

3. Nella pagina delle proprietà **Account RunAs** selezionare l'account RunAs o l'account RunAs classico che si vuole eliminare. Nel riquadro **Proprietà** per l'account selezionato fare quindi clic su **Elimina**.

 ![Eliminare un account RunAs](media/automation-manage-account/automation-account-delete-runas.png)

4. Durante l'eliminazione dell'account, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

5. Dopo aver eliminato l'account, è possibile crearlo di nuovo nella pagina delle proprietà **Account RunAs** selezionando l'opzione di creazione **Account RunAs di Azure**.

 ![Ricreare l'account RunAs di Automazione](media/automation-manage-account/automation-account-create-runas.png)

## <a name="misconfiguration"></a>Errore di configurazione
Alcuni elementi di configurazione necessari per il corretto funzionamento dell'account RunAs o dell'account RunAs classico potrebbero essere stati eliminati o non essere stati creati correttamente durante la configurazione iniziale. Tali elementi includono:

* Asset del certificato
* Asset di connessione
* Account RunAs rimosso dal ruolo di collaboratore
* Entità servizio o applicazione in Azure AD

In caso di errore di configurazione, l'account di Automazione rileva le modifiche e visualizza lo stato *Incompleto* nel riquadro delle proprietà **Account RunAs** per l'account.

![Stato di configurazione Incompleto dell'account RunAs](media/automation-manage-account/automation-account-runas-incomplete-config.png)

Quando si seleziona l'account RunAs, nel riquadro **Proprietà** dell'account viene visualizzato il messaggio di errore seguente:

![Messaggio di avviso relativo alla configurazione incompleta dell'account RunAs](media/automation-manage-account/automation-account-runas-incomplete-config-msg.png).

Per risolvere rapidamente questi problemi relativi all'account RunAs, è possibile eliminare e ricreare l'account.

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulle entità servizio, vedere [Oggetti applicazione e oggetti entità servizio](../active-directory/active-directory-application-objects.md).
* Per altre informazioni sul controllo degli accessi in base al ruolo in Automazione di Azure, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).
* Per altre informazioni sui certificati e i servizi di Azure, vedere [Panoramica sui certificati per i servizi cloud di Azure](../cloud-services/cloud-services-certs-create.md).