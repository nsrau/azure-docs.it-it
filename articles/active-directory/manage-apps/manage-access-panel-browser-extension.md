---
title: Risolvere i problemi relativi all'estensione del pannello di accesso di Azure per IE | Microsoft Docs
description: Come usare Criteri di gruppo per distribuire il componente aggiuntivo di Internet Explorer per il portale App personali.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16abfbeacd972ee8b0ab55f09945e687c95f0093
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763262"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Risolvere i problemi relativi all'estensione del pannello di accesso per Internet Explorer

Questo articolo semplifica la risoluzione dei problemi seguenti:

* Non è possibile accedere alle app tramite il portale App personali durante l'uso di Internet Explorer.
* Viene visualizzato il messaggio "Installa software" anche se è già stato installato il software.

Se si è un amministratore, vedere [come distribuire l'estensione del pannello di accesso per Internet Explorer usando criteri di gruppo](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>Eseguire lo strumento di diagnostica

È possibile diagnosticare i problemi di installazione con l'estensione del pannello di accesso scaricando ed eseguendo lo strumento di diagnostica del pannello di accesso. 

Per scaricare e installare lo strumento di diagnostica:

1. [Selezionare questo collegamento per scaricare lo strumento di diagnostica.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Aprire il file ed estrarre il contenuto nel computer.
1. Per eseguire lo strumento, fare clic con il pulsante destro del mouse sul file denominato *AccessPanelExtensionDiagnosticTool.js* e scegliere **Apri con**  >  **Windows script host basato su Microsoft Windows**.

    ![Apri con > Microsoft Windows Based Script Host](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Esaminare i risultati diagnostici visualizzati e selezionare **Sì** per risolvere i problemi. Verrà visualizzata la finestra di dialogo **Controlla risultati** con informazioni sulle operazioni da eseguire se l'estensione non funziona.  
1. Leggere il messaggio e selezionare **OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Verificare che Access Panel Extension sia abilitato.

Per verificare che l'estensione del pannello di accesso sia stata abilitata in Internet Explorer:

1. In Internet Explorer selezionare l' **icona dell'ingranaggio** nell'angolo in alto a destra della finestra e selezionare **Opzioni Internet**.
1. Passare alla scheda **programmi** e selezionare **Gestisci componenti**aggiuntivi.
1. Selezionare l' **estensione del pannello di accesso** nella sezione **Microsoft Corporation** e selezionare **Abilita**.
1. Per salvare le modifiche, chiudere tutte le finestre del browser di Internet Explorer aperte. La modifica viene applicata la volta successiva che si apre Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Abilitare le estensioni per InPrivate Browsing

Per abilitare le estensioni per InPrivate Browsing:

1. In Internet Explorer selezionare l' **icona dell'ingranaggio** nell'angolo in alto a destra della finestra e selezionare **Opzioni Internet**.
1. Passare alla scheda **privacy** e verificare che la casella di controllo **Disabilita barre degli strumenti ed estensioni al momento dell'avvio di InPrivate Browsing** sia deselezionata.
1. Per salvare le modifiche, chiudere tutte le finestre del browser di Internet Explorer aperte. La modifica viene applicata la volta successiva che si apre Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Disinstallare Access Panel Extension

Per disinstallare l'estensione del pannello di accesso dal computer:

1. Nel pannello di controllo cercare *Disinstalla*.
1. Nei risultati della ricerca selezionare **Disinstalla un programma**.

    ![Selezionare l'opzione Disinstalla un programma dal pannello di controllo](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. Nell'elenco selezionare **l'estensione del pannello di accesso** e selezionare **Disinstalla**.

    ![Disinstallare Access Panel Extension](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. È quindi possibile provare a installare di nuovo l'estensione per verificare se il problema è stato risolto.

Se si verificano problemi durante la disinstallazione dell'estensione, è possibile rimuoverla anche usando lo strumento [Microsoft Fix it](https://go.microsoft.com/?linkid=9779673) .

## <a name="related-articles"></a>Articoli correlati

* [Accesso alle applicazioni e Single Sign-On con Azure Active Directory](what-is-single-sign-on.md)
* [Come distribuire l'estensione pannello di accesso per Internet Explorer utilizzando Criteri di gruppo](deploy-access-panel-browser-extension.md)
