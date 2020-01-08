---
title: Impostare le preferenze di portale di Azure | Microsoft Docs
description: È possibile modificare portale di Azure impostazioni predefinite per soddisfare le proprie preferenze. Le impostazioni includono il timeout della sessione inattiva, la visualizzazione predefinita, la modalità menu, il contrasto, il tema, le notifiche, le lingue e i formati internazionali
services: azure-portal
keywords: impostazioni, timeout, lingua, regione
author: mblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 0c18ccbf5d9e4884af46e088f1a4ead67f50c3f5
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75641367"
---
# <a name="set-your-azure-portal-preferences"></a>Imposta le preferenze di portale di Azure

È possibile modificare le impostazioni predefinite del portale di Azure per soddisfare le proprie preferenze. Ognuna delle impostazioni elencate di seguito può essere modificata:

* [Timeout sessione inattiva](#change-the-idle-duration-for-inactive-sign-out)
* [Visualizzazione predefinita](#choose-your-default-view)
* [Modalità menu del portale](#choose-a-portal-menu-mode)
* [Tema di colore e contrasto elevato](#choose-a-theme)
* [Notifiche popup](#enable-or-disable-pop-up-notifications)
* [Lingua e formato locale](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>Modificare le impostazioni del portale generale

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Impostazioni** dall'intestazione di pagina globale.

    ![Screenshot che mostra le icone delle intestazioni di pagina globali con le impostazioni evidenziate](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>Modificare la durata di inattività per la disconnessione inattiva

L'impostazione del timeout di inattività consente di proteggere le risorse da accessi non autorizzati se si dimentica di proteggere la workstation. Dopo un periodo di inattività, l'utente viene disconnesso automaticamente dalla sessione di portale di Azure.

Selezionare l'elenco a discesa in **Sign me out when inactive**. Scegliere il periodo di tempo dopo il quale la sessione di portale di Azure viene disattivata se si è inattiva.

   ![Screenshot che mostra le impostazioni del portale con impostazioni di timeout inattive evidenziate](./media/set-preferences/inactive-signout-user.png)

La modifica viene salvata automaticamente. Se il computer è inattivo, la sessione di portale di Azure verrà disconnessi dopo la durata impostata.

Questa impostazione può essere eseguita anche da un amministratore a livello di directory per applicare un tempo di inattività massimo. Se un amministratore ha eseguito un'impostazione di timeout a livello di directory, è comunque possibile impostare la durata di disconnessione inattiva. Scegliere un'impostazione ora minore di quella impostata a livello di directory.

Se l'amministratore ha abilitato un criterio di timeout di inattività, selezionare la casella di controllo **Sostituisci i criteri di timeout di inattività directory** . Impostare un intervallo di tempo minore dell'impostazione dei criteri.

   ![Screenshot che mostra le impostazioni del portale con Sostituisci l'impostazione dei criteri di timeout inattività directory evidenziata](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> Se si è un amministratore e si vuole applicare un'impostazione di timeout inattiva per tutti gli utenti del portale di Azure, vedere [impostare il timeout di inattività a livello di directory per gli utenti del portale di Azure](admin-timeout.md)
>

### <a name="choose-your-default-view"></a>Scegliere la visualizzazione predefinita 

È possibile modificare la pagina che viene visualizzata per impostazione predefinita quando si accede al portale di Azure.

   ![Screenshot che mostra le impostazioni di portale di Azure con la visualizzazione predefinita evidenziata](./media/set-preferences/default-view.png)

L'impostazione di visualizzazione predefinita controlla quale visualizzazione portale di Azure viene visualizzata quando si esegue l'accesso. Per impostazione predefinita, è possibile scegliere di aprire Azure Home o la visualizzazione dashboard.

* Non è possibile personalizzare **Home** .  Visualizza i collegamenti ai servizi di Azure più diffusi ed elenca le risorse usate più di recente. Ti offriamo anche collegamenti utili a risorse come Microsoft Learn e la roadmap per Azure.
* I dashboard possono essere personalizzati per creare un'area di lavoro progettata solo per l'utente. Ad esempio, è possibile compilare un dashboard con lo stato attivo per progetto, attività o ruolo. Se si seleziona **Dashboard**, la visualizzazione predefinita passerà al dashboard usato più di recente.

### <a name="choose-a-portal-menu-mode"></a>Scegliere una modalità del menu del portale

La modalità predefinita per il menu del portale controlla la quantità di spazio occupata dal menu del portale nella pagina.

* Quando il menu del portale è in modalità a **comparsa** , è nascosto fino a quando non è necessario. Selezionare l'icona di menu per aprire o chiudere il menu.
* Se si sceglie modalità **ancorata** per il menu del portale, è sempre visibile. È possibile comprimere il menu per fornire maggiore spazio di lavoro. 

### <a name="choose-a-theme"></a>Scegliere un tema

Il tema scelto influiscono sui colori dello sfondo e del carattere visualizzati nell'portale di Azure. È possibile scegliere uno dei quattro temi colori predefiniti. Selezionare ogni anteprima per trovare il tema più adatto.

   ![Screenshot che mostra le impostazioni di portale di Azure con i temi evidenziati](./media/set-preferences/theme.png)

È invece possibile scegliere uno dei temi a contrasto elevato. Le impostazioni a contrasto elevato rendono più semplice la lettura del portale di Azure per gli utenti con problemi di visione ed eseguono l'override di tutte le altre selezioni del tema. Per altre informazioni, vedere [attivare un contrasto elevato o cambiare tema](azure-portal-change-theme-high-contrast.md).

### <a name="enable-or-disable-pop-up-notifications"></a>Abilitare o disabilitare le notifiche popup

Le notifiche sono messaggi di sistema correlati alla sessione corrente. Forniscono informazioni come il saldo del credito corrente, quando le risorse appena create diventano disponibili o confermano l'ultima azione, ad esempio. Quando le notifiche popup sono attivate, i messaggi vengono visualizzati brevemente nell'angolo superiore dello schermo. 

Per abilitare o disabilitare le notifiche popup, selezionare o deselezionare la casella di controllo **Abilita notifiche popup** .

   ![Screenshot che mostra le impostazioni di portale di Azure con le notifiche popup evidenziate](./media/set-preferences/popup-notifications.png)

Per leggere tutte le notifiche ricevute durante la sessione corrente, selezionare **notifiche** dall'intestazione globale.

   ![Screenshot che Mostra portale di Azure intestazione globale con le notifiche evidenziate](./media/set-preferences/read-notifications.png)

Se si desidera leggere le notifiche dalle sessioni precedenti, cercare gli eventi nel log attività. Per altre informazioni, [vedere visualizzare e recuperare gli eventi del log attività di Azure](/azure/azure-monitor/platform/activity-log-view).

### <a name="settings-under-useful-links"></a>Impostazioni in collegamenti utili

Se sono state apportate modifiche alle impostazioni portale di Azure e si desidera eliminarle, selezionare **Ripristina impostazioni predefinite**. Tutte le modifiche apportate alle impostazioni del portale andranno perse. Questa opzione non influisce sulle personalizzazioni del dashboard.

Per altre informazioni su come **esportare tutte le impostazioni** o **eliminare tutte le impostazioni e i dashboard privati**, vedere [esportare o eliminare le impostazioni utente](azure-portal-export-delete-settings.md).

## <a name="change-language-and-regional-settings"></a>Modificare le impostazioni internazionali e della lingua

Sono disponibili due impostazioni che controllano il modo in cui viene visualizzato il testo nel portale di Azure. L'impostazione della **lingua** controlla la lingua visualizzata per il testo nel portale di Azure. Il **formato regionale** controlla il modo in cui vengono visualizzate le date, l'ora, i numeri e la valuta.

Per modificare la lingua utilizzata nella portale di Azure, utilizzare l'elenco a discesa per effettuare una selezione nell'elenco delle lingue disponibili.

La selezione del formato regionale cambia per visualizzare le opzioni internazionali solo per la lingua selezionata. Per modificare la selezione automatica, usare l'elenco a discesa per scegliere il formato locale desiderato.

Se, ad esempio, si seleziona inglese come lingua e quindi si seleziona Stati Uniti come formato locale, la valuta viene visualizzata in dollari statunitensi. Se si seleziona inglese come lingua e quindi si seleziona Europa come formato locale, la valuta viene visualizzata in euro.

Selezionare **applica** per aggiornare le impostazioni relative alla lingua e al formato locale.

   ![Screenshot che illustra le impostazioni del linguaggio e del formato locale](./media/set-preferences/language.png)

>[!NOTE]
>Queste impostazioni internazionali e di lingua hanno effetto solo sul portale di Azure. I collegamenti alla documentazione aperti in una nuova scheda o finestra utilizzeranno le impostazioni della lingua del browser per determinare la lingua da visualizzare.
>

## <a name="next-steps"></a>Passaggi successivi

* [Creare e condividere dashboard personalizzati](azure-portal-dashboards.md)
* [Serie di video illustrativi su portale di Azure](azure-portal-video-series.md)
