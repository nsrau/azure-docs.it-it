---
title: Risolvere i problemi relativi all'estensione del pannello di accesso di Azure per IE Documenti Microsoft
description: Come usare Criteri di gruppo per distribuire il componente aggiuntivo di Internet Explorer per il portale App personali.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0269c87572e2a9242a54491103ae0fcc3637518
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67723914"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Risolvere i problemi relativi all'estensione del pannello di accesso per Internet Explorer

Questo articolo semplifica la risoluzione dei problemi seguenti:

* Non è possibile accedere alle app tramite il portale App personali durante l'uso di Internet Explorer.
* Viene visualizzato il messaggio "Installa software" anche se è già stato installato il software.

Per gli amministratori, vedere [Distribuzione dell'estensione](deploy-access-panel-browser-extension.md)del Pannello di accesso per Internet Explorer tramite Criteri di gruppo .

## <a name="run-the-diagnostic-tool"></a>Eseguire lo strumento di diagnosticaRun the diagnostic tool

È possibile diagnosticare i problemi di installazione con l'estensione del pannello di accesso scaricando ed eseguendo lo strumento di diagnostica del Pannello di accesso. 

Per scaricare e installare lo strumento di diagnostica:

1. [Selezionare questo collegamento per scaricare lo strumento di diagnostica.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Aprire il file ed estrarre il contenuto sul computer.
1. Per eseguire lo strumento, fare clic con il pulsante destro del mouse sul file *denominato AccessPanelExtensionDiagnosticTool.js* e scegliere **Apri con** > **Microsoft Windows Based Script Host**.

    ![Apri con > Microsoft Windows Based Script Host](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Esaminare i risultati della diagnostica visualizzati e selezionare **Sì** per risolvere i problemi. Se l'estensione non funziona, viene visualizzata la finestra di dialogo **Controlla risultati.**  
1. Leggere il messaggio e selezionare **OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Verificare che Access Panel Extension sia abilitato.

Per verificare di aver abilitato l'estensione del Pannello di accesso in Internet Explorer:

1. In Internet Explorer, selezionare l'icona a forma di **ingranaggio** nell'angolo superiore destro della finestra e selezionare **Opzioni Internet**.
1. Passare alla scheda **Programmi** e selezionare **Gestione componenti aggiuntivi**.
1. Selezionare **Estensione pannello di accesso** nella sezione Microsoft **Corporation** e selezionare **Abilita**.
1. Per salvare le modifiche, chiudere tutte le finestre del browser Internet Explorer aperte. La modifica avrà effetto alla successiva apertura di Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Abilitare le estensioni per InPrivate BrowsingEnable extensions for InPrivate Browsing

Per abilitare le estensioni per InPrivate Browsing:

1. In Internet Explorer, selezionare l'icona a forma di **ingranaggio** nell'angolo superiore destro della finestra e selezionare **Opzioni Internet**.
1. Passare alla scheda **Privacy** e verificare che la casella di controllo **Disabilita barre degli strumenti ed estensioni all'avvio** di InPrivate Browsing sia deselezionata.
1. Per salvare le modifiche, chiudere tutte le finestre del browser Internet Explorer aperte. La modifica avrà effetto alla successiva apertura di Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Disinstallare Access Panel Extension

Per disinstallare l'estensione del pannello di accesso dal computer:

1. Nel Pannello di controllo cercare *Disinstalla*.
1. Nei risultati della ricerca selezionare **Disinstalla un programma**.

    ![Selezionare l'opzione Disinstalla un programma dal Pannello di controllo](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. Dall'elenco, selezionare **Access Panel Extension (Estensione pannello** di accesso) e **selezionare Uninstall**.

    ![Disinstallare Access Panel Extension](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. È quindi possibile provare a installare di nuovo l'estensione per verificare se il problema è stato risolto.

Se si verificano problemi di disinstallazione dell'estensione, è anche possibile rimuoverlo utilizzando lo strumento [Microsoft Fix It.](https://go.microsoft.com/?linkid=9779673)

## <a name="related-articles"></a>Articoli correlati

* [Accesso alle applicazioni e Single Sign-On con Azure Active Directory](what-is-single-sign-on.md)
* [Come distribuire l'estensione del Pannello di accesso per Internet Explorer utilizzando criteri di gruppo](deploy-access-panel-browser-extension.md)
