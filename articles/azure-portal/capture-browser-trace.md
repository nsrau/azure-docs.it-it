---
title: Acquisire una traccia del browser per la risoluzione dei problemi | Microsoft Docs
description: Acquisire informazioni sulla rete da una traccia del browser per facilitare la risoluzione dei problemi con il portale di Azure.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 05/11/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: dba321d055e64d62ca91f95461c3299bee5f90d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83714220"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>Acquisire una traccia del browser per la risoluzione dei problemi

Se si sta risolvendo un problema con il portale di Azure ed è necessario contattare il supporto tecnico Microsoft, è consigliabile acquisire prima una traccia del browser e alcune informazioni aggiuntive. Le informazioni raccolte possono offrire dettagli importanti sul portale nel momento in cui si verifica il problema. Seguire la procedura descritta in questo articolo per gli strumenti di sviluppo nel browser in uso: Google Chrome o Microsoft Edge (Chromium), Microsoft Edge (EdgeHTML), Apple Safari o Firefox.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome e Microsoft Edge (Chromium)

Google Chrome e Microsoft Edge (Chromium) sono entrambi basati sul [progetto open source Chromium](https://www.chromium.org/Home). I passaggi seguenti illustrano come usare gli strumenti di sviluppo, che sono molto simili nei due browser. Per altre informazioni, vedere [Strumenti di sviluppo di Chrome](https://developers.google.com/web/tools/chrome-devtools) e [Strumenti di sviluppo di Microsoft Edge (Chromium)](/microsoft-edge/devtools-guide-chromium).

1. Accedere al [portale di Azure](https://portal.azure.com). È importante accedere _prima_ di avviare la traccia in modo che la traccia non contenga informazioni riservate correlate all'accesso. 

1. Iniziare a registrare le azioni eseguite nel portale usando [Registrazione azioni utente](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. Nel portale passare al passaggio immediatamente precedente alla posizione in cui si verifica il problema.

1. Premere F12 o selezionare l'![icona delle impostazioni del browser](media/capture-browser-trace/chromium-icon-settings.png) > **More tools** (Altri strumenti)  > **Developer tools** (Strumenti di sviluppo).

1. Per impostazione predefinita, il browser mantiene le informazioni di traccia solo per la pagina attualmente caricata. Impostare le opzioni seguenti in modo che il browser mantenga tutte le informazioni di traccia, anche se la riproduzione richiede più di una pagina:

    1. Selezionare la scheda **Network** (Rete), quindi **Preserve log** (Mantieni log).

          ![Screenshot di "Preserve log" (Mantieni log)](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. Selezionare la scheda **Console**, selezionare **Console settings** (Impostazioni console), quindi **Preserve Log** (Mantieni log). Selezionare di nuovo **Console settings** (Impostazioni console) per chiudere il riquadro delle impostazioni.

          ![Screenshot di "Preserve Log" (Mantieni log)](media/capture-browser-trace/chromium-console-preserve-log.png)

1. Selezionare la scheda **Network** (Rete), quindi selezionare **Stop recording network log** (Arresta registrazione log di rete) e **Clear** (Cancella).

    ![Screenshot di "Stop recording network log" (Arresta registrazione log di rete) e "Clear" (Cancella)](media/capture-browser-trace/chromium-stop-clear-session.png)

1. Selezionare **Record network log** (Registra log di rete), quindi riprodurre il problema nel portale.

    ![Screenshot di "Start profiling session" (Avvia sessione di profilatura)](media/capture-browser-trace/chromium-start-session.png)

    Verrà visualizzato l'output della sessione simile all'immagine seguente.

    ![Screenshot dei risultati della traccia del browser](media/capture-browser-trace/chromium-browser-trace-results.png)

1. Dopo aver riprodotto il comportamento imprevisto del portale, selezionare **Stop recording network log** (Arresta registrazione log di rete), quindi selezionare **Export HAR** (Esporta HAR) e salvare il file.

    ![Screenshot di "Export HAR" (Esporta HAR)](media/capture-browser-trace/chromium-network-export-har.png)

1. Arrestare Registrazione azioni utente e salvare la registrazione.

1. Tornare al riquadro degli strumenti di sviluppo del browser e selezionare la scheda **Console**. Fare clic con il pulsante destro del mouse su uno dei messaggi, quindi selezionare **Save as...** (Salva con nome) e salvare l'output della console in un file di testo.

    ![Screenshot dell'output della console](media/capture-browser-trace/chromium-console-select.png)

1. Inserire in un pacchetto il file HAR, l'output della console e la registrazione dello schermo in un formato compresso come zip e condividerlo con il supporto tecnico Microsoft.

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

La procedura seguente illustra come usare gli strumenti di sviluppo in Microsoft Edge (EdgeHTML). Per altre informazioni, vedere [Strumenti di sviluppo di Microsoft Edge (EdgeHTML)](/microsoft-edge/devtools-guide).

1. Accedere al [portale di Azure](https://portal.azure.com). È importante accedere _prima_ di avviare la traccia in modo che la traccia non contenga informazioni riservate correlate all'accesso. 

1. Iniziare a registrare le azioni eseguite nel portale usando [Registrazione azioni utente](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. Nel portale passare al passaggio immediatamente precedente alla posizione in cui si verifica il problema.

1. Premere F12 o selezionare l'![icona delle impostazioni del browser](media/capture-browser-trace/edge-icon-settings.png) > **More tools** (Altri strumenti)  > **Developer tools** (Strumenti di sviluppo).

1. Per impostazione predefinita, il browser mantiene le informazioni di traccia solo per la pagina attualmente caricata. Impostare le opzioni seguenti in modo che il browser mantenga tutte le informazioni di traccia, anche se la riproduzione richiede più di una pagina:

    1. Selezionare la scheda **Network** (Rete), quindi deselezionare l'opzione **Clear entries on navigate** (Cancella voci durante la navigazione).

          ![Screenshot di "Clear entries on navigate" (Cancella voci durante la navigazione)](media/capture-browser-trace/edge-network-clear-entries.png)

    1. Selezionare la scheda **Console**, quindi selezionare **Preserve Log** (Mantieni log).

          ![Screenshot di "Preserve Log" (Mantieni log)](media/capture-browser-trace/edge-console-preserve-log.png)

1. Selezionare la scheda **Network** (Rete), quindi selezionare **Stop profiling session** (Arresta sessione di profilatura) e **Clear session** (Cancella sessione).

    ![Screenshot di "Stop profiling session" (Arresta sessione di profilatura) e "Clear session" (Cancella sessione)](media/capture-browser-trace/edge-stop-clear-session.png)

1. Selezionare **Start profiling session** (Avvia sessione di profilatura), quindi riprodurre il problema nel portale.

    ![Screenshot di "Start profiling session" (Avvia sessione di profilatura)](media/capture-browser-trace/edge-start-session.png)

    Verrà visualizzato l'output della sessione simile all'immagine seguente.

    ![Screenshot dei risultati della traccia del browser](media/capture-browser-trace/edge-browser-trace-results.png)

1. Dopo aver riprodotto il comportamento imprevisto del portale, selezionare **Stop profiling session** (Arresta sessione di profilatura), quindi selezionare **Export HAR** (Esporta HAR) e salvare il file.

    ![Screenshot di "Export HAR" (Esporta HAR)](media/capture-browser-trace/edge-network-export-har.png)

1. Arrestare Registrazione azioni utente e salvare la registrazione.

1. Tornare al riquadro degli strumenti di sviluppo del browser, selezionare la scheda **Console** ed espandere la finestra. Posizionare il cursore all'inizio dell'output della console, quindi trascinare e selezionare l'intero contenuto dell'output. Fare clic con il pulsante destro del mouse e selezionare **Copy** (Copia) e salvare l'output della console in un file di testo.

    ![Screenshot dell'output della console](media/capture-browser-trace/edge-console-select.png)

1. Inserire in un pacchetto il file HAR, l'output della console e la registrazione dello schermo in un formato compresso come zip e condividerlo con il supporto tecnico Microsoft.

## <a name="apple-safari"></a>Apple Safari

La procedura seguente illustra come usare gli strumenti di sviluppo in Apple Safari. Per altre informazioni, vedere [Panoramica degli strumenti di sviluppo di Safari](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac).

1. Abilitare gli strumenti di sviluppo in Apple Safari:

    1. Selezionare **Safari**, quindi selezionare **Preferences** (Preferenze).

        ![Screenshot delle preferenze di Safari](media/capture-browser-trace/safari-preferences.png)

    1. Selezionare la scheda **Advanced** (Avanzate), quindi selezionare **Show Develop menu in menu bar** (Mostra menu sviluppo nella barra dei menu).

        ![Screenshot delle preferenze avanzate di Safari](media/capture-browser-trace/safari-show-develop-menu.png)

1. Accedere al [portale di Azure](https://portal.azure.com). È importante accedere _prima_ di avviare la traccia in modo che la traccia non contenga informazioni riservate correlate all'accesso. 

1. Iniziare a registrare i passaggi eseguiti nel portale. Per altre informazioni, vedere [Come registrare la schermata in Mac](https://support.apple.com/HT208721).

1. Nel portale passare al passaggio immediatamente precedente alla posizione in cui si verifica il problema.

1. Selezionare **Develop** (Sviluppo), quindi selezionare **Show Web Inspector** (Mostra Web Inspector).

    ![Screenshot di "Show Web Inspector" (Mostra Web Inspector)](media/capture-browser-trace/safari-show-web-inspector.png)

1. Per impostazione predefinita, il browser mantiene le informazioni di traccia solo per la pagina attualmente caricata. Impostare le opzioni seguenti in modo che il browser mantenga tutte le informazioni di traccia, anche se la riproduzione richiede più di una pagina:

    1. Selezionare la scheda **Network** (Rete), quindi **Preserve Log** (Mantieni log).

          ![Screenshot di "Preserve Log" (Mantieni log)](media/capture-browser-trace/safari-network-preserve-log.png)

    1. Selezionare la scheda **Console**, quindi selezionare **Preserve Log** (Mantieni log).

          ![Screenshot di "Preserve Log" (Mantieni log)](media/capture-browser-trace/safari-console-preserve-log.png)

1. Selezionare la scheda **Network** (Rete), quindi **Clear Network Items** (Cancella elementi rete).

    ![Screenshot di "Clear Network Items" (Cancella elementi rete)](media/capture-browser-trace/safari-clear-session.png)

1. Riprodurre il problema nel portale. Verrà visualizzato l'output della sessione simile all'immagine seguente.

    ![Screenshot dei risultati della traccia del browser](media/capture-browser-trace/safari-browser-trace-results.png)

1. Dopo aver riprodotto il comportamento imprevisto del portale, selezionare **Export** (Esporta) e salvare il file.

    ![Screenshot di "Export" (Esporta)](media/capture-browser-trace/safari-network-export-har.png)

1. Arrestare la registrazione della schermata e salvare la registrazione.

1. Tornare al riquadro degli strumenti di sviluppo del browser, selezionare la scheda **Console** ed espandere la finestra. Posizionare il cursore all'inizio dell'output della console, quindi trascinare e selezionare l'intero contenuto dell'output. Usare Comando+C per copiare l'output e salvarlo in un file di testo.

    ![Screenshot dell'output della console](media/capture-browser-trace/safari-console-select.png)

1. Inserire in un pacchetto il file HAR, l'output della console e la registrazione della schermata in un formato compresso come zip e condividerlo con il supporto tecnico Microsoft.

## <a name="firefox"></a>Firefox

La procedura seguente illustra come usare gli strumenti di sviluppo in Firefox. Per altre informazioni, vedere [Strumenti di sviluppo di Firefox](https://developer.mozilla.org/docs/Tools).

1. Accedere al [portale di Azure](https://portal.azure.com). È importante accedere _prima_ di avviare la traccia in modo che la traccia non contenga informazioni riservate correlate all'accesso. 

1. Iniziare a registrare i passaggi eseguiti nel portale. Usare [Registrazione azioni utente](https://support.microsoft.com/help/22878/windows-10-record-steps) in Windows oppure vedere [Come registrare la schermata in Mac](https://support.apple.com/HT208721).

1. Nel portale passare al passaggio immediatamente precedente alla posizione in cui si verifica il problema.

1. Premere F12 o selezionare l'![icona delle impostazioni del browser](media/capture-browser-trace/firefox-icon-settings.png) > **Web Developer** > **Toggle Tools** (Attiva/disattiva strumenti).

1. Per impostazione predefinita, il browser mantiene le informazioni di traccia solo per la pagina attualmente caricata. Impostare le opzioni seguenti in modo che il browser mantenga tutte le informazioni di traccia, anche se la riproduzione richiede più di una pagina:

    1. Selezionare la scheda **Network** (Rete), quindi **Persist Logs** (Mantieni log).

          ![Screenshot di "Persist Logs" (Mantieni log)](media/capture-browser-trace/firefox-network-persist-logs.png)

    1. Selezionare la scheda **Console**, selezionare **Console settings** (Impostazioni console), quindi **Persist Logs** (Mantieni log).

          ![Screenshot di "Persist Logs" (Mantieni log)](media/capture-browser-trace/firefox-console-persist-logs.png)

1. Selezionare la scheda **Network** (Rete), quindi selezionare **Clear** (Cancella).

    ![Screenshot di "Clear" (Cancella)](media/capture-browser-trace/firefox-clear-session.png)

1. Riprodurre il problema nel portale. Verrà visualizzato l'output della sessione simile all'immagine seguente.

    ![Screenshot dei risultati della traccia del browser](media/capture-browser-trace/firefox-browser-trace-results.png)

1. Dopo aver riprodotto il comportamento imprevisto del portale, selezionare **HAR Export/Import** (Esporta/Importa HAR), quindi **Save All As HAR** (Salva tutto come HAR).

    ![Screenshot di "Export HAR" (Esporta HAR)](media/capture-browser-trace/firefox-network-export-har.png)

1. Arrestare Registrazione azioni utente in Windows o la registrazione della schermata in Mac e salvare la registrazione.

1. Tornare al riquadro degli strumenti di sviluppo del browser e selezionare la scheda **Console**. Fare clic con il pulsante destro del mouse su uno dei messaggi, quindi selezionare **Export Visible Message To** (Esporta messaggio visibile in) e salvare l'output della console in un file di testo.

    ![Screenshot dell'output della console](media/capture-browser-trace/firefox-console-select.png)

1. Inserire in un pacchetto il file HAR, l'output della console e la registrazione della schermata in un formato compresso come zip e condividerlo con il supporto tecnico Microsoft.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica del portale di Azure](azure-portal-overview.md)