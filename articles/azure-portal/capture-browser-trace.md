---
title: Acquisire una traccia del browser per la risoluzione dei problemi | Microsoft Docs
description: Acquisire informazioni sulla rete da una traccia del browser per facilitare la risoluzione dei problemi relativi all'portale di Azure.
services: azure-portal
keywords: ''
author: mblythe
ms.author: mblythe
ms.date: 01/09/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 86a1f522810a29fa2f3188a2ef3a5e0a470187ba
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030367"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>Acquisire una traccia del browser per la risoluzione dei problemi

Se si sta risolvendo un problema con la portale di Azure ed è necessario contattare il supporto tecnico Microsoft, è consigliabile acquisire prima di tutto una traccia del browser e alcune informazioni aggiuntive. Le informazioni raccolte possono fornire dettagli importanti sul portale nel momento in cui si verifica il problema. Seguire la procedura descritta in questo articolo per gli strumenti di sviluppo nel browser in uso: Google Chrome o Microsoft Edge (cromo), Microsoft Edge (EdgeHTML) o Apple Safari.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome e Microsoft Edge (cromo)

Google Chrome e Microsoft Edge (cromo) sono entrambi basati sul [progetto Chromium Open Source](https://www.chromium.org/Home). Nei passaggi seguenti viene illustrato come utilizzare gli strumenti di sviluppo, che sono molto simili nei due browser. Per altre informazioni, vedere [Chrome devtools](https://developers.google.com/web/tools/chrome-devtools) e [Microsoft Edge (cromo) strumenti di sviluppo](/microsoft-edge/devtools-guide-chromium).

1. Accedere al [portale di Azure](https://portal.azure.com). È importante accedere _prima_ di avviare la traccia, in modo che la traccia non contenga informazioni riservate correlate all'accesso. 

1. Iniziare a registrare i passaggi da eseguire nel portale, usando la registrazione dei [passaggi](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. Nel portale passare al passaggio immediatamente prima del momento in cui si verifica il problema.

1. Premere F12 o selezionare ![screenshot dell'icona delle impostazioni del browser](media/capture-browser-trace/chromium-icon-settings.png) > **più strumenti** > **strumenti di sviluppo**.

1. Per impostazione predefinita, il browser mantiene le informazioni di traccia solo per la pagina attualmente caricata. Impostare le opzioni seguenti in modo che il browser mantenga tutte le informazioni di traccia, anche se la riproduzione richiede più di una pagina:

    1. Selezionare la scheda **rete** , quindi selezionare **Mantieni registro**.

          ![Screenshot di "Mantieni log"](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. Selezionare la scheda **console** , selezionare **Impostazioni console**, quindi selezionare **Mantieni registro**. Selezionare di nuovo **Impostazioni console** per chiudere il riquadro Impostazioni.

          ![Screenshot di "Mantieni log"](media/capture-browser-trace/chromium-console-preserve-log.png)

1. Selezionare la scheda **rete** , quindi selezionare **Interrompi registrazione log di rete** e **Cancella**.

    ![Screenshot di "Interrompi registrazione del log di rete" e "Cancella"](media/capture-browser-trace/chromium-stop-clear-session.png)

1. Selezionare **registra log di rete**e quindi riprodurre il problema nel portale.

    ![Screenshot di "Avvia sessione di profilatura"](media/capture-browser-trace/chromium-start-session.png)

    Viene visualizzato un output di sessione simile all'immagine seguente.

    ![Screenshot dei risultati della traccia del browser](media/capture-browser-trace/chromium-browser-trace-results.png)

1. Dopo aver riprodotto il comportamento imprevisto del portale, selezionare **Interrompi registrazione log di rete**, quindi selezionare **Esporta har** e salvare il file.

    ![Screenshot di "Export HAR"](media/capture-browser-trace/chromium-network-export-har.png)

1. Arrestare la registrazione dei passaggi e salvare il file.

1. Tornare al riquadro strumenti di sviluppo del browser, selezionare la scheda **console** . fare clic con il pulsante destro del mouse su, scegliere **Salva con nome**e salvare l'output della console in un file di testo.

    ![Screenshot dell'output della console](media/capture-browser-trace/chromium-console-select.png)

1. Assemblare il file HAR, l'output della console e la registrazione dello schermo in un formato compresso come zip e condividerlo con il supporto tecnico Microsoft.

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

I passaggi seguenti illustrano come usare gli strumenti di sviluppo in Microsoft Edge (EdgeHTML). Per ulteriori informazioni, vedere [Microsoft Edge (EdgeHTML) strumenti di sviluppo](/microsoft-edge/devtools-guide).

1. Accedere al [portale di Azure](https://portal.azure.com). È importante accedere _prima_ di avviare la traccia, in modo che la traccia non contenga informazioni riservate correlate all'accesso. 

1. Iniziare a registrare i passaggi da eseguire nel portale, usando la registrazione dei [passaggi](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. Nel portale passare al passaggio immediatamente prima del momento in cui si verifica il problema.

1. Premere F12 o selezionare ![screenshot dell'icona delle impostazioni del browser](media/capture-browser-trace/edge-icon-settings.png) > **più strumenti** > **strumenti di sviluppo**.

1. Per impostazione predefinita, il browser mantiene le informazioni di traccia solo per la pagina attualmente caricata. Impostare le opzioni seguenti in modo che il browser mantenga tutte le informazioni di traccia, anche se la riproduzione richiede più di una pagina:

    1. Selezionare la scheda **rete** , quindi deselezionare l'opzione **Cancella voci durante la navigazione**.

          ![Screenshot di "Cancella voci durante la navigazione"](media/capture-browser-trace/edge-network-clear-entries.png)

    1. Selezionare la scheda **console** , quindi selezionare **Mantieni registro**.

          ![Screenshot di "Mantieni log"](media/capture-browser-trace/edge-console-preserve-log.png)

1. Selezionare la scheda **rete** , quindi selezionare **Interrompi sessione di profilatura** e **Cancella sessione**.

    ![Screenshot di "Interrompi sessione di profilatura" e "Cancella sessione"](media/capture-browser-trace/edge-stop-clear-session.png)

1. Selezionare **Avvia sessione di profilatura**, quindi riprodurre il problema nel portale.

    ![Screenshot di "Avvia sessione di profilatura"](media/capture-browser-trace/edge-start-session.png)

    Viene visualizzato un output di sessione simile all'immagine seguente.

    ![Screenshot dei risultati della traccia del browser](media/capture-browser-trace/edge-browser-trace-results.png)

1. Dopo aver riprodotto il comportamento imprevisto del portale, selezionare **Interrompi sessione di profilatura**, quindi selezionare **Esporta come har** e salvare il file.

    ![Screenshot di "Esporta come HAR"](media/capture-browser-trace/edge-network-export-har.png)

1. Arrestare la registrazione dei passaggi e salvare il file.

1. Tornare al riquadro strumenti di sviluppo del browser, selezionare la scheda **console** ed espandere la finestra. Posizionare il cursore all'inizio dell'output della console, quindi trascinare e selezionare l'intero contenuto dell'output. Fare clic con il pulsante destro del mouse, scegliere **copia**e salvare l'output della console in un file di testo.

    ![Screenshot dell'output della console](media/capture-browser-trace/edge-console-select.png)

1. Assemblare il file HAR, l'output della console e la registrazione dello schermo in un formato compresso come zip e condividerlo con il supporto tecnico Microsoft.

## <a name="apple-safari"></a>Apple Safari

I passaggi seguenti illustrano come usare gli strumenti di sviluppo in Apple Safari. Per altre informazioni, vedere [Safari strumenti di sviluppo Overview](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac).

1. Abilitare gli strumenti di sviluppo in Apple Safari:

    1. Selezionare **Safari**, quindi **Preferenze**.

        ![Screenshot delle preferenze Safari](media/capture-browser-trace/safari-preferences.png)

    1. Selezionare la scheda **Avanzate** , quindi scegliere **Mostra menu sviluppa nella barra dei menu**.

        ![Screenshot delle preferenze avanzate di Safari](media/capture-browser-trace/safari-show-develop-menu.png)

1. Accedere al [portale di Azure](https://portal.azure.com). È importante accedere _prima_ di avviare la traccia, in modo che la traccia non contenga informazioni riservate correlate all'accesso. 

1. Iniziare a registrare i passaggi da eseguire nel portale. Per altre informazioni, vedere [come registrare la schermata nel Mac](https://support.apple.com/HT208721).

1. Nel portale passare al passaggio immediatamente prima del momento in cui si verifica il problema.

1. Selezionare **sviluppo**, quindi selezionare **Mostra controllo Web**.

    ![Screenshot di "Show Web Inspector"](media/capture-browser-trace/safari-show-web-inspector.png)

1. Per impostazione predefinita, il browser mantiene le informazioni di traccia solo per la pagina attualmente caricata. Impostare le opzioni seguenti in modo che il browser mantenga tutte le informazioni di traccia, anche se la riproduzione richiede più di una pagina:

    1. Selezionare la scheda **rete** , quindi selezionare **Mantieni registro**.

          ![Screenshot di "Mantieni log"](media/capture-browser-trace/safari-network-preserve-log.png)

    1. Selezionare la scheda **console** , quindi selezionare **Mantieni registro**.

          ![Screenshot di "Mantieni log"](media/capture-browser-trace/safari-console-preserve-log.png)

1. Selezionare la scheda **rete** , quindi selezionare **Cancella elementi di rete**.

    ![Screenshot di "Cancella elementi di rete"](media/capture-browser-trace/safari-clear-session.png)

1. Riprodurre il problema nel portale. Viene visualizzato un output di sessione simile all'immagine seguente.

    ![Screenshot dei risultati della traccia del browser](media/capture-browser-trace/safari-browser-trace-results.png)

1. Dopo aver riprodotto il comportamento imprevisto del portale, selezionare **Esporta** e salvare il file.

    ![Screenshot di "Export"](media/capture-browser-trace/safari-network-export-har.png)

1. Arrestare la registrazione dello schermo e salvare il file.

1. Tornare al riquadro strumenti di sviluppo del browser, selezionare la scheda **console** ed espandere la finestra. Posizionare il cursore all'inizio dell'output della console, quindi trascinare e selezionare l'intero contenuto dell'output. Usare Command-C per copiare l'output e salvarlo in un file di testo.

    ![Screenshot dell'output della console](media/capture-browser-trace/safari-console-select.png)

1. Assemblare il file HAR, l'output della console e la registrazione dello schermo in un formato compresso come zip e condividerlo con il supporto tecnico Microsoft.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica del portale di Azure](azure-portal-overview.md)