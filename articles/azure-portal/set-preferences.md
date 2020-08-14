---
title: Gestire impostazioni e preferenze di portale di Azure | Microsoft Docs
description: È possibile modificare portale di Azure impostazioni predefinite per soddisfare le proprie preferenze. Le impostazioni includono il timeout della sessione inattiva, la visualizzazione predefinita, la modalità menu, il contrasto, il tema, le notifiche, le lingue e i formati internazionali
services: azure-portal
keywords: impostazioni, timeout, lingua, regione
author: mgblythe
ms.author: mblythe
ms.date: 08/05/2020
ms.topic: how-to
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 20ed84a87486f1095a90e012368b1f56d6426c8e
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88205727"
---
# <a name="manage-azure-portal-settings-and-preferences"></a>Gestisci impostazioni e preferenze di portale di Azure

È possibile modificare le impostazioni predefinite del portale di Azure per soddisfare le proprie preferenze. La maggior parte delle impostazioni è disponibile dal menu **Impostazioni** dell'intestazione di pagina globale.

![Screenshot che mostra le icone delle intestazioni di pagina globali con le impostazioni evidenziate](./media/set-preferences/header-settings.png)


## <a name="choose-your-default-subscription"></a>Scegliere la sottoscrizione predefinita

È possibile modificare la sottoscrizione che viene aperta per impostazione predefinita quando si accede al portale di Azure. Questa operazione è utile se si ha una sottoscrizione primaria con cui si lavora, ma si usano altre volte. 

:::image type="content" source="media/set-preferences/filter-subscription-default-view.png" alt-text="Filtrare l'elenco di risorse in base alla sottoscrizione.":::

1. Selezionare l'icona di filtro directory e sottoscrizione nell'intestazione di pagina globale.

1. Selezionare le sottoscrizioni desiderate come sottoscrizioni predefinite quando si avvia il portale. 

    :::image type="content" source="media/set-preferences/default-directory-subscription-filter.png" alt-text="Selezionare le sottoscrizioni desiderate come sottoscrizioni predefinite quando si avvia il portale."::: 


## <a name="choose-your-default-view"></a>Scegliere la visualizzazione predefinita 

È possibile modificare la pagina che viene visualizzata per impostazione predefinita quando si accede al portale di Azure.

![Screenshot che mostra le impostazioni di portale di Azure con la visualizzazione predefinita evidenziata](./media/set-preferences/default-view.png)

- Non è possibile personalizzare **Home** .  Visualizza i collegamenti ai servizi di Azure più diffusi ed elenca le risorse usate più di recente. Ti offriamo anche collegamenti utili a risorse come Microsoft Learn e la roadmap per Azure.

- I dashboard possono essere personalizzati per creare un'area di lavoro progettata solo per l'utente. Ad esempio, è possibile compilare un dashboard con lo stato attivo per progetto, attività o ruolo. Se si seleziona **Dashboard**, la visualizzazione predefinita passerà al dashboard usato più di recente. Per altre informazioni, vedere [Creare e condividere dashboard nel portale di Azure](azure-portal-dashboards.md).

## <a name="choose-a-portal-menu-mode"></a>Scegliere una modalità del menu del portale

La modalità predefinita per il menu del portale controlla la quantità di spazio occupata dal menu del portale nella pagina.

![Screenshot che mostra le impostazioni di portale di Azure con i temi evidenziati](./media/set-preferences/menu-mode.png)

- Quando il menu del portale è in modalità a **comparsa** , è nascosto fino a quando non è necessario. Selezionare l'icona del menu per aprire o chiudere il menu.

- Se si sceglie **modalità ancorata** per il menu del portale, è sempre visibile. È possibile comprimere il menu per fornire maggiore spazio di lavoro.

## <a name="choose-a-theme-or-enable-high-contrast"></a>Scegli un tema o Abilita contrasto elevato

Il tema scelto influiscono sui colori dello sfondo e del carattere visualizzati nell'portale di Azure. È possibile scegliere uno dei quattro temi colori predefiniti. Selezionare ogni anteprima per trovare il tema più adatto.

In alternativa, è possibile scegliere uno dei temi a contrasto elevato. I temi a contrasto elevato rendono i portale di Azure più facili da leggere per gli utenti con problemi visivi. eseguono l'override di tutte le altre selezioni di tema.

![Screenshot che mostra le impostazioni di portale di Azure con i temi evidenziati](./media/set-preferences/theme.png)

## <a name="enable-or-disable-pop-up-notifications"></a>Abilitare o disabilitare le notifiche popup

Le notifiche sono messaggi di sistema correlati alla sessione corrente. Forniscono informazioni come il saldo del credito corrente, quando le risorse appena create diventano disponibili o confermano l'ultima azione, ad esempio. Quando le notifiche popup sono attivate, i messaggi vengono visualizzati brevemente nell'angolo superiore dello schermo. 

Per abilitare o disabilitare le notifiche popup, selezionare o deselezionare **Abilita notifiche popup**.

![Screenshot che mostra le impostazioni di portale di Azure con le notifiche popup evidenziate](./media/set-preferences/popup-notifications.png)

Per leggere tutte le notifiche ricevute durante la sessione corrente, selezionare **notifiche** dall'intestazione globale.

![Screenshot che Mostra portale di Azure intestazione globale con le notifiche evidenziate](./media/set-preferences/read-notifications.png)

Se si desidera leggere le notifiche dalle sessioni precedenti, cercare gli eventi nel log attività. Per altre informazioni, vedere [visualizzare il log attività](../azure-monitor/platform/activity-log.md#view-the-activity-log). 

## <a name="change-the-inactivity-timeout-setting"></a>Modificare l'impostazione di timeout di inattività

L'impostazione del timeout di inattività consente di proteggere le risorse da accessi non autorizzati se si dimentica di proteggere la workstation. Dopo un periodo di inattività, l'utente viene disconnesso automaticamente dalla sessione di portale di Azure. Come utente singolo, è possibile modificare l'impostazione di timeout per se stessi. Se si è un amministratore, è possibile impostarlo a livello di directory per tutti gli utenti nella directory.

### <a name="change-your-individual-timeout-setting-user"></a>Modificare le impostazioni di timeout individuali (utente)

Selezionare l'elenco a discesa in **Sign me out when inactive**. Scegliere il periodo di tempo dopo il quale la sessione di portale di Azure viene disattivata se si è inattiva.

![Screenshot che mostra le impostazioni del portale con impostazioni di timeout inattive evidenziate](./media/set-preferences/inactive-signout-user.png)

La modifica viene salvata automaticamente. Se il computer è inattivo, la sessione di portale di Azure verrà disconnessi dopo la durata impostata.

Se l'amministratore ha abilitato un criterio di timeout di inattività, è comunque possibile impostarne uno personalizzato, purché sia minore dell'impostazione a livello di directory. Selezionare **Sostituisci i criteri di timeout di inattività directory**, quindi impostare un intervallo di tempo.

![Screenshot che mostra le impostazioni del portale con Sostituisci l'impostazione dei criteri di timeout inattività directory evidenziata](./media/set-preferences/inactive-signout-override.png)

### <a name="change-the-directory-timeout-setting-admin"></a>Modificare l'impostazione di timeout directory (amministratore)

Gli amministratori del [ruolo amministratore globale](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) possono applicare il tempo massimo di inattività prima che una sessione venga disconnesso. L'impostazione del timeout di inattività si applica a livello di directory. L'impostazione ha effetto per le nuove sessioni. Non verrà applicato immediatamente a tutti gli utenti che hanno già eseguito l'accesso. Per ulteriori informazioni sulle directory, vedere [Active Directory Domain Services Overview](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

Se si è un amministratore globale e si desidera applicare un'impostazione di timeout di inattività per tutti gli utenti del portale di Azure, attenersi alla procedura seguente:

1. Selezionare il testo del collegamento **Configura timeout a livello di directory**.

    ![Screenshot che mostra le impostazioni del portale con il testo del collegamento evidenziato](./media/set-preferences/settings-admin.png)

1. Nella pagina **Configura timeout inattività a livello di directory** selezionare Abilita timeout di inattività a **livello di directory per il portale di Azure** per attivare l'impostazione.

1. Immettere quindi le **ore** e i **minuti** per l'intervallo di tempo massimo durante il quale un utente può rimanere inattivo prima che la sessione venga disconnesso automaticamente.

1. Selezionare **Applica**.

    ![Screenshot che mostra la pagina per impostare il timeout di inattività a livello di directory](./media/set-preferences/configure.png)

Per verificare che i criteri di timeout di inattività siano impostati correttamente, selezionare **notifiche** dall'intestazione di pagina globale. Verificare che sia elencata una notifica di esito positivo.

![Screenshot che mostra il messaggio di notifica riuscito per il timeout di inattività a livello di directory](./media/set-preferences/confirmation.png)

## <a name="restore-default-settings"></a>Ripristina impostazioni predefinite

Se sono state apportate modifiche alle impostazioni portale di Azure e si desidera eliminarle, selezionare **Ripristina impostazioni predefinite**. Tutte le modifiche apportate alle impostazioni del portale andranno perse. Questa opzione non influisce sulle personalizzazioni del dashboard.

![Screenshot che mostra il ripristino delle impostazioni predefinite](./media/set-preferences/useful-links-restore-defaults.png)

## <a name="export-user-settings"></a>Esporta impostazioni utente

Le informazioni sulle impostazioni personalizzate vengono archiviate in Azure. È possibile esportare i dati utente seguenti:

* Dashboard privati nell'portale di Azure
* Impostazioni utente come le sottoscrizioni o le directory preferite e l'ultima directory di accesso
* Temi e altre impostazioni personalizzate del portale

È consigliabile esportare ed esaminare le impostazioni se si prevede di eliminarle. La ricompilazione di dashboard o la reimpostazione delle impostazioni può richiedere molto tempo.

Per esportare le impostazioni del portale, selezionare **Esporta tutte le impostazioni**.

![Screenshot che mostra l'esportazione delle impostazioni](./media/set-preferences/useful-links-export-settings.png)

L'esportazione delle impostazioni crea un file con *estensione JSON* che contiene le impostazioni utente, ad esempio il tema colori, i Preferiti e i dashboard privati. A causa della natura dinamica delle impostazioni utente e del rischio di danneggiamento dei dati, non è possibile importare le impostazioni dal file con estensione *JSON* .

## <a name="delete-user-settings-and-dashboards"></a>Elimina le impostazioni utente e i dashboard

Le informazioni sulle impostazioni personalizzate vengono archiviate in Azure. È possibile eliminare i seguenti dati utente:

* Dashboard privati nell'portale di Azure
* Impostazioni utente come le sottoscrizioni o le directory preferite e l'ultima directory di accesso
* Temi e altre impostazioni personalizzate del portale

È consigliabile esportare ed esaminare le impostazioni prima di eliminarle. La ricompilazione di dashboard o la reimpostazione delle impostazioni personalizzate può richiedere molto tempo.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

Per eliminare le impostazioni del portale, selezionare **Elimina tutte le impostazioni e dashboard privati**.

![Screenshot che mostra l'eliminazione di impostazioni](./media/set-preferences/useful-links-delete-settings.png)

## <a name="change-language-and-regional-settings"></a>Modificare le impostazioni internazionali e della lingua

Sono disponibili due impostazioni che controllano il modo in cui viene visualizzato il testo nel portale di Azure: 
- L'impostazione della **lingua** controlla la lingua visualizzata per il testo nel portale di Azure. 

- Il **formato regionale** controlla il modo in cui vengono visualizzate le date, l'ora, i numeri e la valuta.

Per modificare la lingua utilizzata nella portale di Azure, utilizzare l'elenco a discesa per effettuare una selezione nell'elenco delle lingue disponibili.

La selezione del formato regionale cambia per visualizzare le opzioni internazionali solo per la lingua selezionata. Per modificare la selezione automatica, usare l'elenco a discesa per scegliere il formato locale desiderato.

Se, ad esempio, si seleziona inglese come lingua e quindi si seleziona Stati Uniti come formato locale, la valuta viene visualizzata in dollari statunitensi. Se si seleziona inglese come lingua e quindi si seleziona Europa come formato locale, la valuta viene visualizzata in euro.

Selezionare **applica** per aggiornare le impostazioni relative alla lingua e al formato locale.

   ![Screenshot che illustra le impostazioni del linguaggio e del formato locale](./media/set-preferences/language.png)

>[!NOTE]
>Queste impostazioni internazionali e di lingua hanno effetto solo sul portale di Azure. I collegamenti alla documentazione aperti in una nuova scheda o finestra utilizzano le impostazioni della lingua del browser per determinare la lingua da visualizzare.
>

## <a name="next-steps"></a>Passaggi successivi

- [Tasti di scelta rapida nel portale di Azure](azure-portal-keyboard-shortcuts.md)
- [Browser e dispositivi supportati](azure-portal-supported-browsers-devices.md)
- [Aggiunta, rimozione e ridisposizione di Preferiti](azure-portal-add-remove-sort-favorites.md)
- [Creare e condividere dashboard personalizzati](azure-portal-dashboards.md)
- [Serie di video di procedure per il portale di Azure](azure-portal-video-series.md)
