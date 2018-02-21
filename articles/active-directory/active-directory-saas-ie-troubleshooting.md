---
title: Risoluzione dei problemi relativi all'estensione Pannello di accesso di Azure per Internet Explorer | Documentazione Microsoft
description: Come usare Criteri di gruppo per distribuire il componente aggiuntivo di Internet Explorer per il portale App personali.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: f56b3230-26fd-42ec-9e3d-2c12daf15479
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ff6d0b299c45d89ee8d3a79fa98ce4a542174a5b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>Risoluzione dei problemi di Access Panel Extension per Internet Explorer
Questo articolo semplifica la risoluzione dei problemi seguenti:

* Non è possibile accedere alle app tramite il portale App personali durante l'uso di Internet Explorer.
* Viene visualizzato il messaggio "Installa software" anche se è già stato installato il software.

Se l'utente è un amministratore, vedere anche [Come distribuire Access Panel Extension per Internet Explorer con Criteri di gruppo](active-directory-saas-ie-group-policy.md)

## <a name="run-the-diagnostic-tool"></a>Eseguire lo strumento di diagnostica
È possibile eseguire la diagnostica dei problemi di installazione di Access Panel Extension scaricando ed eseguendo il relativo strumento di diagnostica:

1. [Fare clic qui per scaricare lo strumento di diagnostica.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
2. Aprire il file, quindi premere il pulsante **Estrai tutti** .
   
    ![Pulsante Estrai tutti](./media/active-directory-saas-ie-troubleshooting/extract1.png)
3. Premere il pulsante **Estrai** per continuare.
   
    ![Premere Estrai](./media/active-directory-saas-ie-troubleshooting/extract2.png)
4. Per eseguire lo strumento, fare doppio clic con il pulsante destro del mouse sul file denominato **AccessPanelExtensionDiagnosticTool** e quindi selezionare **Apri con > Microsoft Windows Based Script Host**.
   
    ![Apri con > Microsoft Windows Based Script Host](./media/active-directory-saas-ie-troubleshooting/open_tool.png)
5. Viene quindi visualizzata la finestra di diagnostica seguente, contenente la descrizione dei possibili errori dell'installazione.
   
    ![Esempio della finestra di diagnostica](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)
6. Fare clic su "**Sì**" per consentire al programma di risolvere i problemi rilevati.
7. Per salvare queste modifiche, chiudere tutte le finestre di Internet Explorer e quindi riaprire Internet Explorer.<br />Se risulta ancora impossibile accedere alle applicazioni, attenersi alla procedura riportata di seguito.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Verificare che Access Panel Extension sia abilitato.
Per verificare se Access Panel Extension è abilitato in Internet Explorer:

1. In Internet Explorer fare clic sull'**icona a forma di ingranaggio** nell'angolo superiore destro della finestra. Selezionare quindi **Opzioni Internet**.<br />Nelle versioni precedenti di Internet Explorer scegliere **Strumenti > Opzioni Internet**.
   
    ![Accedere a Strumenti > Opzioni Internet](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)
2. Fare clic sulla scheda **Programmi** e quindi sul pulsante **Gestione componenti aggiuntivi**.
   
    ![Fare clic su Gestione componenti aggiuntivi](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)
3. In questa finestra di dialogo selezionare **Access Panel Extension** e quindi fare clic sul pulsante **Abilita**.
   
    ![Fare clic su Abilita](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)
4. Per salvare queste modifiche, chiudere tutte le finestre di Internet Explorer e quindi riaprire Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Abilitare le estensioni per InPrivate Browsing
Se si usa la modalità InPrivate Browsing:

1. In Internet Explorer fare clic sull'**icona a forma di ingranaggio** nell'angolo superiore destro della finestra. Selezionare quindi **Opzioni Internet**.<br />Nelle versioni precedenti di Internet Explorer scegliere **Strumenti > Opzioni Internet**.
   
    ![Esempio della finestra di diagnostica](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)
2. Passare alla scheda **Privacy** e **deselezionare** la casella di controllo **Disabilita estensioni e barre degli strumenti all'avvio di InPrivate Browsing**</p>
   
    ![Deselezionare la casella di controllo Disabilita estensioni e barre degli strumenti all'avvio di InPrivate Browsing](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)
3. Per salvare queste modifiche, chiudere tutte le finestre di Internet Explorer e quindi riaprire Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Disinstallare Access Panel Extension
Per disinstallare Access Panel Extension dal computer:

1. Sulla tastiera premere il **tasto Windows** per aprire il menu Start. Quando il menu è aperto, è possibile digitare un testo qualsiasi per eseguire una ricerca. Digitare "Pannello di controllo" e quindi aprire il **Pannello di controllo** quando viene visualizzato nei risultati della ricerca.
   
    ![Cercare Pannello di controllo](./media/active-directory-saas-ie-troubleshooting/search_sm.png)
2. Nell'angolo in alto a destra del Pannello di controllo modificare l'opzione **Visualizza per** impostandola su **Icone grandi**. Individuare e fare clic sul pulsante **Programmi e funzionalità**.
   
    ![Modificare la visualizzazione in modo da visualizzare le icone grandi](./media/active-directory-saas-ie-troubleshooting/control_panel.png)
3. Nell'elenco selezionare **Access Panel Extension** e quindi scegliere il pulsante **Disinstalla**.
   
    ![Fare clic su Disinstalla](./media/active-directory-saas-ie-troubleshooting/uninstall.png)
4. È quindi possibile provare a installare di nuovo l'estensione per verificare se il problema è stato risolto.

Se si verificano problemi durante la disinstallazione dell'estensione, è possibile rimuoverla anche usando lo strumento [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) .

## <a name="related-articles"></a>Articoli correlati
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
* [Accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Come distribuire l'estensione Pannello di accesso per Internet Explorer con Criteri di gruppo](active-directory-saas-ie-group-policy.md)

