---
title: Risolvere i problemi relativi a Azure Synapse Studio (anteprima)Troubleshoot Azure Synapse Studio (preview)
description: Risolvere i problemi relativi a Azure Synapse StudioTroubleshoot Azure Synapse Studio
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: ede38317762e5aac5562c9bb3494713c3edacc69
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431319"
---
# <a name="azure-synapse-studio-preview-troubleshooting"></a>Risoluzione dei problemi di Azure Synapse Studio (anteprima)Azure Synapse Studio (preview) troubleshooting

Questa guida alla risoluzione dei problemi fornisce istruzioni sulle informazioni da fornire quando si apre un ticket di supporto in caso di problemi di connettività di rete. Con le informazioni appropriate, possiamo forse risolvere il problema più rapidamente.

## <a name="sql-on-demand-preview-service-connectivity-issue"></a>Problema di connettività del servizio SU richiesta SQL (anteprima)SQL on-demand (preview) service connectivity (preview) connectivity (preview) service connectivity (environment

### <a name="symptom-1"></a>Sintomo 1

L'opzione "SQL on-demand" è disattivata nell'elenco a discesa "Connetti a".

![sintomo1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>Sintomo 2

L'esecuzione della query con "SQL on-demand" viene visualizzato il messaggio di errore "Impossibile stabilire la connessione al server".

![sintomo2](media/troubleshooting-synapse-studio/symptom2.png)

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

> [!NOTE] 
>    I seguenti passaggi per la risoluzione dei problemi sono per Chromium Edge e Chrome. È possibile utilizzare altri browser (ad esempio FireFox) con gli stessi passaggi di risoluzione dei problemi, ma la finestra "Strumento di sviluppo" potrebbe avere un layout diverso dalle schermate di questo TSG. Se possibile, NON utilizzare Edge classico per la risoluzione dei problemi, in quanto potrebbe mostrare informazioni imprecise in determinate situazioni.

Apri il pannello "Informazioni di diagnostica", seleziona il pulsante "Scarica diagnostica". Conservare le informazioni scaricate per la segnalazione degli errori. È invece possibile copiare l'"ID sessione" e allegarlo all'apertura del ticket di supporto.

![diagnostic-info](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

Per iniziare la risoluzione dei problemi, ripetere l'operazione eseguita in Azure Synapse Studio.To begin troubleshooting, retry the operation you performed in Azure Synapse Studio.

- Per il sintomo 1, selezionare il pulsante "Aggiorna" a destra dell'elenco a discesa "Usa database" nella scheda "Script SQL" e verificare se è possibile visualizzare "SQL on-demand".
- Per il sintomo 2, provare a eseguire nuovamente la query per verificare se viene eseguita correttamente.

Se il problema persiste, premere F12 nel browser per aprire "Strumenti di sviluppo" (DevTools).

Nella finestra "Strumenti di sviluppo", passa al pannello "Rete". Se necessario, selezionare il pulsante "Cancella" sulla barra degli strumenti nel pannello "Rete".
Assicurati che l'opzione "Disabilita cache" nel pannello "Rete" sia selezionata.

Ripetere l'operazione eseguita in Azure Synapse Studio.Retry the operation you performed in Azure Synapse Studio. Potresti vedere i nuovi elementi visualizzati nell'elenco "Rete" in "Strumenti di sviluppo". Prendere nota dell'ora di sistema corrente da fornire nel ticket di supporto.

![pannello di rete](media/troubleshooting-synapse-studio/network-panel.png)

Individuare l'elemento la cui colonna Url corrisponde al modello seguente:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Dove [*A*] è il nome dell'area di lavoro e "-ondemand" potrebbe essere "-sqlod" e dove [*B*] deve essere un nome di database, ad esempio "master". Dovrebbero essere presenti al massimo due elementi con lo stesso valore URL ma valori di metodo diversi; OPTIONS e POST. Controllare se questi due elementi hanno "200" o "20x" nella colonna di stato, dove "x" potrebbe essere qualsiasi singola cifra.

Se uno di loro ha qualcosa di diverso da "20x" e:

- status inizia con "(failed)", allarga la colonna "Status" o posiziona il puntatore del mouse sul testo dello stato per visualizzare il testo completo. Includere il testo e/o lo screenshot all'apertura del ticket di supporto.

    ![status-text](media/troubleshooting-synapse-studio/status-text.png)

    - Se viene visualizzata ERR_NAME_NOT_RESOLVED e l'area di lavoro è stata creata entro 10 minuti, attendere 10 minuti e riprovare a verificare se il problema persiste.
    - Se vedi ERR_INTERNET_DISCONNECTED o ERR_NETWORK_CHANGED, è possibile che la connessione di rete del PC abbia problemi. Controllare la connessione di rete e ripetere l'operazione.
    - Se viene visualizzato ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR o altri codici di errore contenenti "SSL", è possibile che la configurazione SSL locale si verifichi nodi o che l'amministratore di rete abbia bloccato l'accesso al server SQL su richiesta. Aprire un ticket di supporto e allegare il codice di errore nella descrizione.
    - Se viene visualizzato ERR_NETWORK_ACCESS_DENIED, potrebbe essere necessario verificare con l'amministratore se il criterio firewall locale ha bloccato l'accesso al dominio con estensione database.windows.net o alla porta remota 1443.
    - Facoltativamente, provare immediatamente la stessa operazione su un computer e/o un ambiente di rete diverso per escludere un problema di configurazione di rete nel PC.

- stato è "40x", "50x" o altri numeri, selezionare sugli elementi per visualizzare i dettagli. Dovresti vedere i dettagli dell'elemento a destra. Trova la sezione "Intestazione risposta"; quindi verificare se esiste un elemento denominato "access-control-allow-origin". In tal caso, verificare se ha uno dei seguenti valori:

    - `*`(asterisco singolo)
    - https://web.azuresynapse.net/(o altro valore con cui inizia il testo nella barra degli indirizzi del browser)

Se l'intestazione della risposta contiene uno dei valori precedenti, significa che dovremmo aver già raccolto le informazioni sull'errore. Se necessario, è possibile aprire un ticket di supporto e, facoltativamente, allegare la schermata dei dettagli dell'elemento.

Se non riesci a vedere l'intestazione o l'intestazione non ha uno dei valori elencati sopra, allega una schermata dei dettagli dell'elemento quando apri il ticket.

![dettagli dell'elemento](media/troubleshooting-synapse-studio/item-details.png)

Se i passaggi precedenti non risolvono il problema, potrebbe essere necessario aprire un ticket di supporto. Quando invii il ticket di supporto, includi l'"ID sessione" o "Informazioni diagnostiche" scaricato all'inizio di questa guida.

Quando segnali il problema, puoi facoltativamente acquisire uno screenshot della scheda "Console" nella sezione "Strumenti di sviluppo" e allegarlo al ticket di supporto. Scorrere il contenuto e acquisire più di uno screenshot se necessario per acquisire l'intero messaggio.

![strumento-strumento per sviluppatori-console](media/troubleshooting-synapse-studio/developer-tool-console.png)

Se stai allegando screenshot, fornisci il tempo (o un intervallo di tempo stimato) di quando hai preso le schermate. Ci aiuterà quando si esamina il problema.

Alcuni browser supportano la visualizzazione di timestamp nella scheda "Console". Per Chromium Edge/Chrome, apri la finestra di dialogo "Impostazioni" in "Strumenti di sviluppo" e seleziona "Mostra timestamp" nella scheda "Preferenze".

![developer-tool-console-settings](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![show-time-stampo](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>Passaggi successivi
Se i passaggi precedenti aiutano a risolvere il problema [Creare un ticket](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) di supporto
