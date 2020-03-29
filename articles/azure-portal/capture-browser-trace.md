---
title: Acquisire una traccia del browser per la risoluzione dei problemi Documenti Microsoft
description: Acquisire informazioni di rete da una traccia del browser per risolvere i problemi con il portale di Azure.Capture network information from a browser trace to help troubleshoot issues with the Azure portal.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/09/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 2b506c9d15dafcd23b24207fe15ed0532939209f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310697"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>Acquisire una traccia del browser per la risoluzione dei problemi

Se si sta risolvendo un problema con il portale di Azure ed è necessario contattare il supporto tecnico Microsoft, è consigliabile acquisire prima una traccia del browser e alcune informazioni aggiuntive. Le informazioni raccolte possono fornire dettagli importanti sul portale nel momento in cui si verifica il problema. Segui i passaggi descritti in questo articolo per gli strumenti di sviluppo nel browser che usi: Google Chrome o Microsoft Edge (Chromium), Microsoft Edge (EdgeHTML) o Apple Safari.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome e Microsoft Edge (Chromium)

Google Chrome e Microsoft Edge (Chromium) sono entrambi basati sul [progetto open source Chromium](https://www.chromium.org/Home). I passaggi seguenti illustrano come usare gli strumenti di sviluppo, che sono molto simili nei due browser. Per ulteriori informazioni, consultate [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) e [Microsoft Edge (Chromium) Developer Tools](/microsoft-edge/devtools-guide-chromium).

1. Accedere al [portale](https://portal.azure.com)di Azure . È importante accedere _prima_ di avviare la traccia in modo che la traccia non contenga informazioni riservate relative all'accesso. 

1. Iniziare a registrare i passaggi da eseguire nel portale, utilizzando [Registrazione azioni utente](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. Nel portale passare al passaggio immediatamente prima del punto in cui si verifica il problema.

1. Premere F12 ![o selezionare Screenshot](media/capture-browser-trace/chromium-icon-settings.png) > delle impostazioni del browser icona**Altri strumenti** > **Strumenti di sviluppo**.

1. Per impostazione predefinita, il browser mantiene le informazioni di traccia solo per la pagina attualmente caricata. Impostare le seguenti opzioni in modo che il browser mantenga tutte le informazioni di traccia, anche se il repro richiede di passare a più di una pagina:

    1. Selezionare la scheda **Rete** , quindi selezionare **Mantieni registro**.

          ![Screenshot di "Mantieni registro"](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. Seleziona la scheda **Console,** seleziona **Impostazioni console**, quindi **Mantieni registro**. Selezionare nuovamente **Impostazioni console** per chiudere il riquadro delle impostazioni.

          ![Screenshot di "Mantieni registro"](media/capture-browser-trace/chromium-console-preserve-log.png)

1. Selezionare la scheda **Rete** , quindi selezionare **Interrompi registrazione registro di rete** e **Cancella**.

    ![Screenshot di "Interrompi registrazione registro di rete" e "Cancella"](media/capture-browser-trace/chromium-stop-clear-session.png)

1. Selezionare **Registra registro di rete**, quindi riprodurre il problema nel portale.

    ![Screenshot di "Avvia sessione di profilatura"](media/capture-browser-trace/chromium-start-session.png)

    Verrà visualizzato un output della sessione simile all'immagine seguente.

    ![Schermata dei risultati della traccia del browser](media/capture-browser-trace/chromium-browser-trace-results.png)

1. Dopo aver riprodotto il comportamento imprevisto del portale, selezionare **Interrompi registrazione registro di rete**, quindi **selezionare Esporta HAR** e salvare il file.

    ![Screenshot di "Esporta HAR"](media/capture-browser-trace/chromium-network-export-har.png)

1. Arrestare Registrazione azioni utente e salvare il file.

1. Nel riquadro degli strumenti di sviluppo del browser selezionare **Save as...** la scheda **Console.**

    ![Schermata dell'output della console](media/capture-browser-trace/chromium-console-select.png)

1. Crea un pacchetto del file HAR, dell'output della console e della registrazione dello schermo in un formato compresso come .zip e condividilo con il supporto Microsoft.

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

La procedura seguente illustra come usare gli strumenti di sviluppo in Microsoft Edge (EdgeHTML). Per ulteriori informazioni, vedere [Microsoft Edge (EdgeHTML) Developer Tools](/microsoft-edge/devtools-guide).

1. Accedere al [portale](https://portal.azure.com)di Azure . È importante accedere _prima_ di avviare la traccia in modo che la traccia non contenga informazioni riservate relative all'accesso. 

1. Iniziare a registrare i passaggi da eseguire nel portale, utilizzando [Registrazione azioni utente](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. Nel portale passare al passaggio immediatamente prima del punto in cui si verifica il problema.

1. Premere F12 ![o selezionare Screenshot](media/capture-browser-trace/edge-icon-settings.png) > delle impostazioni del browser icona**Altri strumenti** > **Strumenti di sviluppo**.

1. Per impostazione predefinita, il browser mantiene le informazioni di traccia solo per la pagina attualmente caricata. Impostare le seguenti opzioni in modo che il browser mantenga tutte le informazioni di traccia, anche se il repro richiede di passare a più di una pagina:

    1. Selezionare la scheda **Rete** , quindi deselezionare l'opzione **Cancella voci durante la navigazione**.

          ![Screenshot di "Cancella voci durante la navigazione"](media/capture-browser-trace/edge-network-clear-entries.png)

    1. Selezionare la scheda **Console** , quindi selezionare **Mantieni registro**.

          ![Screenshot di "Mantieni registro"](media/capture-browser-trace/edge-console-preserve-log.png)

1. Selezionare la scheda **Rete** , quindi selezionare **Interrompi sessione di profilatura** e **Cancella sessione**.

    ![Screenshot di "Interrompi sessione di profilatura" e "Cancella sessione"](media/capture-browser-trace/edge-stop-clear-session.png)

1. Selezionare **Avvia sessione di profilatura**, quindi riprodurre il problema nel portale.

    ![Screenshot di "Avvia sessione di profilatura"](media/capture-browser-trace/edge-start-session.png)

    Verrà visualizzato un output della sessione simile all'immagine seguente.

    ![Schermata dei risultati della traccia del browser](media/capture-browser-trace/edge-browser-trace-results.png)

1. Dopo aver riprodotto il comportamento imprevisto del portale, selezionare **Interrompi sessione di profilatura**, quindi **selezionare Esporta come HAR** e salvare il file.

    ![Screenshot di "Esporta come HAR"](media/capture-browser-trace/edge-network-export-har.png)

1. Arrestare Registrazione azioni utente e salvare il file.

1. Nel riquadro degli strumenti di sviluppo del browser selezionare la scheda **Console** ed espandere la finestra. Posizionare il cursore all'inizio dell'output della console, quindi trascinare e selezionare l'intero contenuto dell'output. Fare clic con il pulsante destro del mouse, quindi **scegliere Copia**e salvare l'output della console in un file di testo.

    ![Schermata dell'output della console](media/capture-browser-trace/edge-console-select.png)

1. Crea un pacchetto del file HAR, dell'output della console e della registrazione dello schermo in un formato compresso come .zip e condividilo con il supporto Microsoft.

## <a name="apple-safari"></a>Apple Safari

I passaggi seguenti illustrano come usare gli strumenti di sviluppo in Apple Safari. Per ulteriori informazioni, consulta [Panoramica degli strumenti](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac)di sviluppo di Safari.

1. Abilita gli strumenti di sviluppo in Apple Safari:

    1. Seleziona **Safari**, quindi **Preferenze**.

        ![Screenshot delle preferenze di Safari](media/capture-browser-trace/safari-preferences.png)

    1. Selezionare la scheda **Avanzate** , quindi selezionare **Mostra menu Sviluppo nella barra dei menu**.

        ![Screenshot delle preferenze avanzate di Safari](media/capture-browser-trace/safari-show-develop-menu.png)

1. Accedere al [portale](https://portal.azure.com)di Azure . È importante accedere _prima_ di avviare la traccia in modo che la traccia non contenga informazioni riservate relative all'accesso. 

1. Iniziare a registrare i passaggi eseguiti nel portale. Per ulteriori informazioni, consulta [Come registrare lo schermo sul Mac.](https://support.apple.com/HT208721)

1. Nel portale passare al passaggio immediatamente prima del punto in cui si verifica il problema.

1. Selezionare **Sviluppo**, quindi **Mostra Controllo Web**.

    ![Screenshot di "Mostra controllo Web"](media/capture-browser-trace/safari-show-web-inspector.png)

1. Per impostazione predefinita, il browser mantiene le informazioni di traccia solo per la pagina attualmente caricata. Impostare le seguenti opzioni in modo che il browser mantenga tutte le informazioni di traccia, anche se il repro richiede di passare a più di una pagina:

    1. Selezionare la scheda **Rete** , quindi selezionare **Mantieni registro**.

          ![Screenshot di "Mantieni registro"](media/capture-browser-trace/safari-network-preserve-log.png)

    1. Selezionare la scheda **Console** , quindi selezionare **Mantieni registro**.

          ![Screenshot di "Mantieni registro"](media/capture-browser-trace/safari-console-preserve-log.png)

1. Selezionare la scheda **Rete** , quindi selezionare **Cancella elementi di rete**.

    ![Screenshot di "Cancella elementi di rete"](media/capture-browser-trace/safari-clear-session.png)

1. Riprodurre il problema nel portale. Verrà visualizzato un output della sessione simile all'immagine seguente.

    ![Schermata dei risultati della traccia del browser](media/capture-browser-trace/safari-browser-trace-results.png)

1. Dopo aver riprodotto il comportamento imprevisto del portale, selezionare **Esporta** e salvare il file.

    ![Screenshot di "Esporta"](media/capture-browser-trace/safari-network-export-har.png)

1. Arrestare il registratore dello schermo e salvare il file.

1. Nel riquadro degli strumenti di sviluppo del browser selezionare la scheda **Console** ed espandere la finestra. Posizionare il cursore all'inizio dell'output della console, quindi trascinare e selezionare l'intero contenuto dell'output. Utilizzare Comando-C per copiare l'output e salvarlo in un file di testo.

    ![Schermata dell'output della console](media/capture-browser-trace/safari-console-select.png)

1. Crea un pacchetto del file HAR, dell'output della console e della registrazione dello schermo in un formato compresso come .zip e condividilo con il supporto Microsoft.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica del portale di Azure](azure-portal-overview.md)