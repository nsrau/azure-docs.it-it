---
title: Impostare le preferenze del portale di Azure Documenti Microsoft
description: È possibile modificare le impostazioni predefinite del portale di Azure per soddisfare le proprie preferenze. Le impostazioni includono timeout della sessione inattiva, visualizzazione predefinita, modalità menu, contrasto, tema, notifiche e lingua e formati regionali
services: azure-portal
keywords: impostazioni, timeout, lingua, regionale
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 7bcfdeec832b14eb53c0dab6cb2f53970d85c804
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310680"
---
# <a name="set-your-azure-portal-preferences"></a>Impostare le preferenze del portale di Azure

È possibile modificare le impostazioni predefinite del portale di Azure per soddisfare le proprie preferenze. Ognuna delle impostazioni elencate di seguito può essere modificata:

* [Timeout sessione inattiva](#change-the-idle-duration-for-inactive-sign-out)
* [Visualizzazione predefinita](#choose-your-default-view)
* [Modalità menu portale](#choose-a-portal-menu-mode)
* [Tema Colore e contrasto elevato](#choose-a-theme)
* [Notifiche popup](#enable-or-disable-pop-up-notifications)
* [Lingua e formato locale](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>Modificare le impostazioni generali del portale

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Selezionare **Impostazioni** nell'intestazione di pagina globale.

    ![Schermata che mostra le icone dell'intestazione di pagina globale con le impostazioni evidenziate](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>Modificare la durata di inattività per la disconnessione inattiva

L'impostazione del timeout di inattività consente di proteggere le risorse da accessi non autorizzati se si dimentica di proteggere la workstation. Dopo essere rimasti inattivi per un po', si viene automaticamente disconnessi dalla sessione del portale di Azure.After you're idle for a while, you're automatically signed out of your Azure portal session.

Selezionare il menu a discesa in **Disconnetti quando inattivo**. Scegliere la durata dopo la quale la sessione del portale di Azure viene disconnessa se si è inattivi.

   ![Schermata che mostra le impostazioni del portale con le impostazioni di timeout inattive evidenziate](./media/set-preferences/inactive-signout-user.png)

La modifica viene salvata automaticamente. Se si è inattivi, la sessione del portale di Azure si disconnetterà dopo la durata impostata.

Questa impostazione può anche essere effettuata da un amministratore a livello di directory per applicare un tempo massimo di inattività. Se un amministratore ha effettuato un'impostazione di timeout a livello di directory, puoi comunque impostare la tua durata di disconnessione inattiva. Scegliere un'impostazione dell'ora inferiore a quella impostata a livello di directory.

Se l'amministratore ha abilitato un criterio di timeout di inattività, selezionare la casella di controllo Ignora criteri di **timeout di inattività** della directory. Impostare un intervallo di tempo inferiore all'impostazione dei criteri.

   ![Screenshot che mostra le impostazioni del portale con l'impostazione del criterio di timeout di inattività della directory evidenziata](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> Se si è un amministratore e si vuole applicare un'impostazione di timeout inattivo per tutti gli utenti del portale di Azure, vedere Impostare il timeout di [inattività](admin-timeout.md) a livello di directory per gli utenti del portale di Azure
>

### <a name="choose-your-default-view"></a>Scegliere la visualizzazione predefinita 

È possibile modificare la pagina che viene aperta per impostazione predefinita quando si accede al portale di Azure.You can change the page that opens by default when your sign-in to the Azure portal.

   ![Screenshot che mostra le impostazioni del portale di Azure con la visualizzazione predefinita evidenziata](./media/set-preferences/default-view.png)

L'impostazione di visualizzazione predefinita controlla quale visualizzazione del portale di Azure viene visualizzata quando si accede. È possibile scegliere di aprire Azure Home per impostazione predefinita o la visualizzazione Dashboard.You can choose to open Azure Home by default, or the Dashboard view.

* **La casa** non può essere personalizzata.  Visualizza i collegamenti ai servizi di Azure più diffusi ed elenca le risorse usate più di recente. Vengono inoltre forniti collegamenti utili a risorse come Microsoft Learn e la roadmap di Azure.We also give you useful links to resources like Microsoft Learn and the Azure roadmap.
* I dashboard possono essere personalizzati per creare un'area di lavoro progettata appositamente per l'utente. Ad esempio, è possibile compilare una dashboard con progetto, attività o ruolo attivo. Se si seleziona **Dashboard**, la visualizzazione predefinita passerà al dashboard utilizzato più di recente.

### <a name="choose-a-portal-menu-mode"></a>Scegliere una modalità menu portale

La modalità predefinita per il menu del portale controlla la quantità di spazio occupato dal menu del portale nella pagina.

* Quando il menu del portale è in modalità **riquadro a comparsa,** viene nascosto fino a quando non è necessario. Selezionare l'icona del menu per aprire o chiudere il menu.
* Se si sceglie la modalità **ancorata** per il menu del portale, questa è sempre visibile. È possibile comprimere il menu per fornire più spazio di lavoro. 

### <a name="choose-a-theme"></a>Scegli un tema

Il tema scelto influisce sui colori dello sfondo e del tipo di carattere visualizzati nel portale di Azure.The the me that choose affects the background and font colors that appear in the Azure portal. È possibile selezionare uno dei quattro temi di colore preimpostati. Seleziona ogni miniatura per trovare il tema che meglio ti si addice.

   ![Screenshot che mostra le impostazioni del portale di Azure con i temi evidenziati](./media/set-preferences/theme.png)

È invece possibile scegliere uno dei temi a contrasto elevato. Le impostazioni di contrasto elevato semplificano la lettura del portale di Azure per gli utenti con problemi di vista e sostituiscono tutte le altre selezioni del tema. Per ulteriori informazioni, consultate [Attivare il contrasto elevato o modificare il tema.](azure-portal-change-theme-high-contrast.md)

### <a name="enable-or-disable-pop-up-notifications"></a>Abilitare o disabilitare le notifiche popup

Le notifiche sono messaggi di sistema relativi alla sessione corrente. Forniscono informazioni come il saldo del credito corrente, quando le risorse appena create diventano disponibili, o confermano l'ultima azione, ad esempio. Quando le notifiche a comparsa sono attivate, i messaggi vengono visualizzati brevemente nell'angolo superiore dello schermo. 

Per abilitare o disabilitare le notifiche popup, seleziona o deseleziona la casella di controllo **Abilita notifiche popup.**

   ![Screenshot che mostra le impostazioni del portale di Azure con le notifiche popup evidenziate](./media/set-preferences/popup-notifications.png)

Per leggere tutte le notifiche ricevute durante la sessione corrente, selezionare **Notifiche** nell'intestazione globale.

   ![Screenshot dell'intestazione globale del portale di Azure con le notifiche evidenziate](./media/set-preferences/read-notifications.png)

Se si desidera leggere le notifiche delle sessioni precedenti, cercare gli eventi nel log attività. Per altre informazioni, vedere [Visualizzare e recuperare gli eventi](/azure/azure-monitor/platform/activity-log-view)del log attività di Azure .

### <a name="settings-under-useful-links"></a>Impostazioni sotto link utili

Se sono state apportate modifiche alle impostazioni del portale di Azure e si vuole annullarle, selezionare **Ripristina impostazioni predefinite**. Tutte le modifiche apportate alle impostazioni del portale andranno perse. Questa opzione non influisce sulle personalizzazioni del dashboard.

Per ulteriori informazioni su **Esporta tutte le impostazioni** o Elimina tutte le impostazioni e i dashboard **privati,** vedere [Esportare o eliminare le impostazioni utente](azure-portal-export-delete-settings.md).

## <a name="change-language-and-regional-settings"></a>Modificare le impostazioni internazionali e della lingua

Sono disponibili due impostazioni che controllano la modalità di visualizzazione del testo nel portale di Azure.There are two settings that control how the text in the Azure portal appears. L'impostazione Lingua controlla la lingua visualizzata per il testo nel portale di Azure.The **Language** setting controls the language you see for text in the Azure portal. **Il formato regionale** controlla la modalità di visualizzazione di date, ore, numeri e valuta.

Per modificare la lingua usata nel portale di Azure, usare l'elenco a discesa per selezionare dall'elenco delle lingue disponibili.

La selezione del formato regionale cambia per visualizzare le opzioni internazionali solo per la lingua selezionata. Per modificare la selezione automatica, utilizzare l'elenco a discesa per scegliere il formato regionale desiderato.

Ad esempio, se si seleziona l'inglese come lingua e quindi si seleziona Stati Uniti come formato regionale, la valuta viene visualizzata in dollari statunitensi. Se si seleziona l'inglese come lingua e quindi si seleziona Europa come formato regionale, la valuta viene visualizzata in euro.

Selezionare **Applica** per aggiornare le impostazioni della lingua e del formato regionale.

   ![Schermata che mostra le impostazioni di lingua e formato regionale](./media/set-preferences/language.png)

>[!NOTE]
>Queste impostazioni internazionali e della lingua influiscono solo sul portale di Azure.These language and regional settings affect only the Azure portal. I collegamenti alla documentazione che si aprono in una nuova scheda o finestra utilizzeranno le impostazioni della lingua del browser per determinare la lingua da visualizzare.
>

## <a name="next-steps"></a>Passaggi successivi

* [Creare e condividere dashboard personalizzati](azure-portal-dashboards.md)
* [Serie di video di procedure per il portale di Azure](azure-portal-video-series.md)
