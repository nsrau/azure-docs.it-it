---
title: Risoluzione dei problemi relativi all'estensione Pannello di accesso di Azure per Internet Explorer | Documentazione Microsoft
description: Come usare Criteri di gruppo per distribuire il componente aggiuntivo di Internet Explorer per il portale App personali.
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2018
ms.author: barbkess
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7ccaf272a79268ecd781821593a6041d5653a39b
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54471889"
---
# <a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>Risoluzione dei problemi di Access Panel Extension per Internet Explorer
Questo articolo semplifica la risoluzione dei problemi seguenti:

* Non è possibile accedere alle app tramite il portale App personali durante l'uso di Internet Explorer.
* Viene visualizzato il messaggio "Installa software" anche se è già stato installato il software.

Se l'utente è un amministratore, vedere anche: [Come distribuire l'estensione Pannello di accesso per Internet Explorer con Criteri di gruppo](deploy-access-panel-browser-extension.md)

## <a name="run-the-diagnostic-tool"></a>Eseguire lo strumento di diagnostica
È possibile eseguire la diagnostica dei problemi di installazione di Access Panel Extension scaricando ed eseguendo il relativo strumento di diagnostica:

1. [Fare clic qui per scaricare lo strumento di diagnostica.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
2. Aprire il file, quindi premere il pulsante **Estrai tutti** .
   
    ![Pulsante Estrai tutti](./media/manage-access-panel-browser-extension/extract1.png)
3. Premere il pulsante **Estrai** per continuare.
   
    ![Premere Estrai](./media/manage-access-panel-browser-extension/extract2.png)
4. Per eseguire lo strumento, fare doppio clic con il pulsante destro del mouse sul file denominato **AccessPanelExtensionDiagnosticTool** e quindi selezionare **Apri con > Microsoft Windows Based Script Host**.
   
    ![Apri con > Microsoft Windows Based Script Host](./media/manage-access-panel-browser-extension/open_tool.png)
5. Viene quindi visualizzata la finestra di diagnostica seguente, contenente la descrizione dei possibili errori dell'installazione.
   
    ![Esempio della finestra di diagnostica](./media/manage-access-panel-browser-extension/tool_preview.png)
6. Fare clic su "**Sì**" per consentire al programma di risolvere i problemi rilevati.
7. Per salvare queste modifiche, chiudere tutte le finestre di Internet Explorer e quindi riaprire Internet Explorer.<br />Se risulta ancora impossibile accedere alle applicazioni, attenersi alla procedura riportata di seguito.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Verificare che Access Panel Extension sia abilitato.
Per verificare se Access Panel Extension è abilitato in Internet Explorer:

1. In Internet Explorer fare clic sull'**icona a forma di ingranaggio** nell'angolo superiore destro della finestra. Selezionare quindi **Opzioni Internet**.<br />Nelle versioni precedenti di Internet Explorer scegliere **Strumenti > Opzioni Internet**.
   
    ![Accedere a Strumenti > Opzioni Internet](./media/manage-access-panel-browser-extension/internetoptions.png)
2. Fare clic sulla scheda **Programmi** e quindi sul pulsante **Gestione componenti aggiuntivi**.
   
    ![Fare clic su Gestione componenti aggiuntivi](./media/manage-access-panel-browser-extension/internetoptions_programs.png)
3. In questa finestra di dialogo selezionare **Access Panel Extension** e quindi fare clic sul pulsante **Abilita**.
   
    ![Fare clic su Abilita](./media/manage-access-panel-browser-extension/enableaddon.png)
4. Per salvare queste modifiche, chiudere tutte le finestre di Internet Explorer e quindi riaprire Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Abilitare le estensioni per InPrivate Browsing
Se si usa la modalità InPrivate Browsing:

1. In Internet Explorer fare clic sull'**icona a forma di ingranaggio** nell'angolo superiore destro della finestra. Selezionare quindi **Opzioni Internet**.<br />Nelle versioni precedenti di Internet Explorer scegliere **Strumenti > Opzioni Internet**.
   
    ![Esempio della finestra di diagnostica](./media/manage-access-panel-browser-extension/inprivateoptions.png)
2. Passare alla scheda **Privacy** e **deselezionare** la casella di controllo **Disabilita estensioni e barre degli strumenti all'avvio di InPrivate Browsing**</p>
   
    ![Deselezionare la casella di controllo Disabilita estensioni e barre degli strumenti all'avvio di InPrivate Browsing](./media/manage-access-panel-browser-extension/enabletoolbars.png)
3. Per salvare queste modifiche, chiudere tutte le finestre di Internet Explorer e quindi riaprire Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Disinstallare Access Panel Extension
Per disinstallare Access Panel Extension dal computer:

1. Sulla tastiera premere il **tasto Windows** per aprire il menu Start. Quando il menu è aperto, è possibile digitare un testo qualsiasi per eseguire una ricerca. Digitare "Pannello di controllo" e quindi aprire il **Pannello di controllo** quando viene visualizzato nei risultati della ricerca.
   
    ![Cercare Pannello di controllo](./media/manage-access-panel-browser-extension/search_sm.png)
2. Nell'angolo in alto a destra del Pannello di controllo modificare l'opzione **Visualizza per** impostandola su **Icone grandi**. Individuare e fare clic sul pulsante **Programmi e funzionalità**.
   
    ![Modificare la visualizzazione in modo da visualizzare le icone grandi](./media/manage-access-panel-browser-extension/control_panel.png)
3. Nell'elenco selezionare **Access Panel Extension** e quindi scegliere il pulsante **Disinstalla**.
   
    ![Fare clic su Disinstalla](./media/manage-access-panel-browser-extension/uninstall.png)
4. È quindi possibile provare a installare di nuovo l'estensione per verificare se il problema è stato risolto.

Se si verificano problemi durante la disinstallazione dell'estensione, è possibile rimuoverla anche usando lo strumento [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) .

## <a name="related-articles"></a>Articoli correlati
* [Accesso alle applicazioni e Single Sign-On con Azure Active Directory](what-is-single-sign-on.md)
* [Come distribuire l'estensione Pannello di accesso per Internet Explorer con Criteri di gruppo](deploy-access-panel-browser-extension.md)

