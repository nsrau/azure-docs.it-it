---
title: Risolvere i problemi di Synapse Studio
description: Risolvere i problemi di Azure sinapsi Studio
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: a96709c7538bda6d6b338c29dc512902e9ca2c87
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445840"
---
# <a name="azure-synapse-studio-troubleshooting"></a>Risoluzione dei problemi di Azure sinapsi Studio

Questa guida alla risoluzione dei problemi fornisce istruzioni sulle informazioni da fornire quando si apre un ticket di supporto in caso di problemi di connettività di rete. Con le informazioni corrette, il problema potrebbe essere risolto più rapidamente.

## <a name="serverless-sql-pool-service-connectivity-issue"></a>Problema di connettività del servizio pool SQL senza server

### <a name="symptom-1"></a>Sintomo 1

L'opzione "pool SQL senza server" è disabilitata nell'elenco a discesa "Connetti a".

![symptom1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>Sintomo 2

L'esecuzione della query con "pool SQL senza server" fornisce il messaggio di errore "Impossibile stabilire la connessione al server".

![sintomo 2](media/troubleshooting-synapse-studio/symptom2.png)
 

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

> [!NOTE] 
>    I passaggi per la risoluzione dei problemi seguenti sono per Chrome Edge e Chrome. È possibile utilizzare altri browser (ad esempio, FireFox) con le stesse procedure di risoluzione dei problemi, ma la finestra "Developer Tool" potrebbe avere un layout diverso dalle schermate di questo STG. Se possibile, non usare il bordo classico per la risoluzione dei problemi, in quanto potrebbe visualizzare informazioni non accurate in determinate situazioni.

Aprire il pannello "informazioni di diagnostica" e selezionare il pulsante "Scarica diagnostica". Mantieni le informazioni scaricate per la segnalazione errori. È invece possibile copiare il "ID sessione" e collegarlo quando si apre il ticket di supporto.

![informazioni di diagnostica](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

Per iniziare la risoluzione dei problemi, ripetere l'operazione eseguita in Azure sinapsi Studio.

- Per sintomo 1, selezionare il pulsante "Aggiorna" a destra dell'elenco a discesa "Usa database" nella scheda "script SQL" e verificare se è possibile visualizzare "pool SQL senza server".
- Per il sintomo 2, provare a eseguire nuovamente la query per verificare se viene eseguita correttamente.

Se il problema persiste, premere F12 nel browser per aprire "Strumenti di sviluppo" (DevTools).

Nella finestra "Strumenti di sviluppo" passare al pannello "rete". Se necessario, selezionare il pulsante "Cancella" sulla barra degli strumenti nel pannello "rete".
Assicurarsi che l'opzione "Disabilita cache" nel pannello "rete" sia selezionata.

Ripetere l'operazione eseguita in Azure sinapsi Studio. È possibile che vengano visualizzati nuovi elementi nell'elenco "rete" in "Strumenti di sviluppo". Prendere nota dell'ora di sistema corrente da fornire nel ticket di supporto.

![Pannello di rete 1](media/troubleshooting-synapse-studio/network-panel.png)

Trovare l'elemento la cui colonna URL corrisponde al modello seguente:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Dove [*A*] è il nome dell'area di lavoro e "-ondemand" può essere "-sqlod" e dove [*B*] deve essere un nome di database, ad esempio "Master". Dovrebbero essere presenti al massimo due elementi con lo stesso valore di URL ma con valori di metodo diversi. OPZIONI e POST. Verificare che questi due elementi includano "200" o "20x" nella colonna stato, dove "x" può essere una singola cifra.

Se uno dei due ha un valore diverso da "20x" e:

- Lo stato inizia con "(operazione non riuscita)", ampliando la colonna "stato" o posizionando il puntatore sul testo di stato per visualizzare il testo completo. Includere il testo e/o lo screenshot quando si apre il ticket di supporto.

    ![testo stato](media/troubleshooting-synapse-studio/status-text.png)

    - Se viene visualizzato ERR_NAME_NOT_RESOLVED e l'area di lavoro è stata creata entro 10 minuti, attendere 10 minuti e riprovare a verificare se il problema persiste.
    - Se viene visualizzato ERR_INTERNET_DISCONNECTED o ERR_NETWORK_CHANGED, è possibile che si verifichino problemi di connessione alla rete del computer. Controllare la connessione di rete e ripetere l'operazione.
    - Se vengono visualizzati ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR o altri codici di errore contenenti "SSL", è possibile che si verifichino problemi nella configurazione SSL locale oppure che l'amministratore di rete abbia bloccato l'accesso al server del pool SQL senza server. Aprire un ticket di supporto e alleghire il codice di errore nella descrizione.
    - Se viene visualizzato ERR_NETWORK_ACCESS_DENIED, potrebbe essere necessario verificare con l'amministratore se il criterio del firewall locale ha bloccato l'accesso a un dominio *. database.windows.net o a una porta remota 1443.
    - Facoltativamente, provare immediatamente a eseguire la stessa operazione in un altro computer e/o in un ambiente di rete per escludere un problema di configurazione di rete nel PC.

- Status è "40x", "50x" o altri numeri, selezionare gli elementi per visualizzare i dettagli. Verranno visualizzati i dettagli dell'elemento a destra. Trovare la sezione "intestazione risposta"; Controllare quindi se esiste un elemento denominato "Access-Control-Allow-Origin". In caso affermativo, controllare se ha uno dei valori seguenti:

    - `*` (asterisco singolo)
    - https://web.azuresynapse.net/ (o un altro valore che inizia con il testo nella barra degli indirizzi del browser)

Se l'intestazione della risposta contiene uno dei valori precedenti, significa che è necessario avere già raccolto le informazioni sull'errore. Se necessario, è possibile aprire un ticket di supporto e, facoltativamente, aggiungere la schermata dei dettagli dell'elemento.

Se l'intestazione non è visibile oppure l'intestazione non ha uno dei valori elencati in precedenza, alleghi una schermata dei dettagli dell'elemento quando si apre il ticket.

 
![Dettagli elemento](media/troubleshooting-synapse-studio/item-details.png)
 
Se i passaggi precedenti non consentono di risolvere il problema, potrebbe essere necessario aprire un ticket di supporto. Quando si invia il ticket di supporto, includere "ID sessione" o "informazioni di diagnostica" scaricati all'inizio di questa guida.

Quando si segnala il problema, è possibile creare una schermata della scheda "console" nel "Strumenti di sviluppo" e collegarla al ticket di supporto. Scorrere il contenuto e prendere più di uno screenshot, se necessario, per acquisire l'intero messaggio.

![console degli strumenti per sviluppatori](media/troubleshooting-synapse-studio/developer-tool-console.png)

Se si allineano schermate, specificare l'ora (o un intervallo di tempo stimato) di quando si sono prese le schermate. Che ci consentirà di esaminare il problema.

Alcuni browser supportano la visualizzazione dei timestamp nella scheda "console". Per cromo Edge/Chrome, aprire la finestra di dialogo "Impostazioni" in "Strumenti di sviluppo" e selezionare "Mostra timestamp" nella scheda "Preferenze".

![impostazioni della console dello strumento di sviluppo](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![Mostra timestamp](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>Passaggi successivi
Se la procedura precedente non consente di risolvere il problema, [creare un ticket di supporto](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
