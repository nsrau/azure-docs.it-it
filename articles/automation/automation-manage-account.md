---
title: Gestire l'account di Automazione di Azure | Microsoft Docs
description: Questo articolo descrive come gestire la configurazione dell'account di Automazione, ad esempio il rinnovo del certificato, l'eliminazione e gli errori di configurazione.
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/13/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 41efdbcacede74bac038342688362ff480cadc7e
ms.contentlocale: it-it
ms.lasthandoff: 04/15/2017

---

# <a name="manage-azure-automation-account"></a>Gestire l'account di Automazione di Azure
Prima della scadenza dell'account di Automazione, è necessario rinnovare il certificato. Se si ritiene che l'account RunAs sia stato compromesso, è possibile eliminarlo e crearlo nuovamente. Questa sezione illustra come eseguire tali operazioni.

## <a name="self-signed-certificate-renewal"></a>Rinnovo del certificato autofirmato
Il certificato autofirmato creato per l'account RunAs scade un anno dopo la data di creazione. È possibile rinnovarlo in qualsiasi momento prima della scadenza. Quando si procede al rinnovo, il certificato valido corrente viene mantenuto per evitare di influire negativamente su eventuali runbook messi in coda o in esecuzione, che eseguono l'autenticazione con l'account RunAs. Il certificato rimane valido fino alla relativa data di scadenza.

> [!NOTE]
> Se l'account RunAs di Automazione è stato configurato per l'uso di un certificato rilasciato dall'Autorità di certificazione globale (enterprise) e si usa tale opzione, il certificato viene sostituito da un certificato autofirmato.

Per rinnovare il certificato, seguire questa procedura:

1. Nel Portale di Azure aprire l'account di Automazione.

2. Nel pannello delle **proprietà dell'account** in **Account di Automazione** selezionare **Account RunAs** nella sezione **Impostazioni account**.

    ![Riquadro delle proprietà dell'account di Automazione](media/automation-manage-account/automation-account-properties-pane.png)
3. Nel pannello delle proprietà **Account RunAs** selezionare l'account RunAs o l'account RunAs classico per cui si vuole rinnovare il certificato.

4. Nel pannello **Proprietà** dell'account selezionato fare clic su **Rinnova certificato**.

    ![Rinnovare il certificato per l'account RunAs](media/automation-manage-account/automation-account-renew-runas-certificate.png)

5. Durante il rinnovamento del certificato, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Eliminare un account RunAs o un account RunAs classico
Questa sezione illustra come eliminare e creare nuovamente un account RunAs o un account RunAs classico. Quando si esegue questa azione, l'account di Automazione viene mantenuto. Dopo aver eliminato l'account RunAs o l'account RunAs classico, è possibile ricrearlo nel portale di Azure.

1. Nel Portale di Azure aprire l'account di Automazione.

2. Nel pannello delle proprietà dell'account in **Account di Automazione** selezionare **Account RunAs**.

3. Nel pannello delle proprietà **Account RunAs** selezionare l'account RunAs o l'account RunAs classico da eliminare. Nel pannello **Proprietà** dell'account selezionato fare quindi clic su **Elimina**.

 ![Eliminare un account RunAs](media/automation-manage-account/automation-account-delete-runas.png)

4. Durante l'eliminazione dell'account, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

5. Dopo aver eliminato l'account, è possibile crearlo nuovamente nel pannello delle proprietà **Account RunAs** selezionando l'opzione di creazione **Account RunAs di Azure**.

 ![Ricreare l'account RunAs di Automazione](media/automation-manage-account/automation-account-create-runas.png)

## <a name="misconfiguration"></a>Errore di configurazione
Alcuni elementi di configurazione necessari per il corretto funzionamento dell'account RunAs o dell'account RunAs classico potrebbero essere stati eliminati o non essere stati creati correttamente durante la configurazione iniziale. Tali elementi includono:

* Asset del certificato
* Asset di connessione
* Account RunAs rimosso dal ruolo di collaboratore
* Entità servizio o applicazione in Azure AD

In caso di errore di configurazione, l'account di Automazione rileva le modifiche e visualizza lo stato *Incompleto* nel pannello delle proprietà **Account RunAs** per l'account.

![Stato di configurazione Incompleto dell'account RunAs](media/automation-manage-account/automation-account-runas-incomplete-config.png)

Quando si seleziona l'account RunAs, nel riquadro **Proprietà** dell'account viene visualizzato il messaggio di errore seguente:

![Messaggio di avviso relativo alla configurazione incompleta dell'account RunAs](media/automation-manage-account/automation-account-runas-incomplete-config-msg.png).

Per risolvere rapidamente questi problemi relativi all'account RunAs, è possibile eliminare e ricreare l'account.

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulle entità servizio, vedere [Oggetti applicazione e oggetti entità servizio](../active-directory/active-directory-application-objects.md).
* Per altre informazioni sul controllo degli accessi in base al ruolo in Automazione di Azure, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).
* Per altre informazioni sui certificati e i servizi di Azure, vedere [Panoramica sui certificati per i servizi cloud di Azure](../cloud-services/cloud-services-certs-create.md).
