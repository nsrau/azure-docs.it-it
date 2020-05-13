---
title: Monitorare Key Vault con monitoraggio di Azure per Key Vault (anteprima) | Microsoft Docs
description: Questo articolo descrive il monitoraggio di Azure per gli insiemi di credenziali delle chiavi.
services: azure-monitor
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/13/2019
ms.openlocfilehash: 2387f14a537c15c891bff968573bf4d698c01770
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211303"
---
# <a name="monitoring-your-key-vault-service-with-azure-monitor-for-key-vault-preview"></a>Monitoraggio del servizio Key Vault con monitoraggio di Azure per Key Vault (anteprima)
Monitoraggio di Azure per Key Vault (anteprima) offre un monitoraggio completo degli insiemi di credenziali delle chiavi grazie a una visualizzazione unificata delle richieste Key Vault, delle prestazioni, degli errori e della latenza.
Questo articolo consente di comprendere come caricare e personalizzare l'esperienza di monitoraggio di Azure per Key Vault (anteprima).

## <a name="introduction-to-azure-monitor-for-key-vault-preview"></a>Introduzione a monitoraggio di Azure per Key Vault (anteprima)

Prima di passare all'esperienza, è necessario comprenderne il modo in cui presenta e visualizza le informazioni.
-    **In una prospettiva di scalabilità** che mostra una visualizzazione snapshot delle prestazioni in base alle richieste, alla suddivisione degli errori e a una panoramica delle operazioni e della latenza.
-   Eseguire il **drill-down dell'analisi** di un particolare insieme di credenziali delle chiavi per eseguire analisi dettagliate.
-    **Personalizzabile** , in cui è possibile modificare la metrica che si desidera visualizzare, modificare o impostare le soglie che si allineano ai limiti e salvare la cartella di lavoro. I grafici nella cartella di lavoro possono essere aggiunti ai dashboard di Azure.

Monitoraggio di Azure per Key Vault combina i log e le metriche per offrire una soluzione di monitoraggio globale. Tutti gli utenti possono accedere ai dati di monitoraggio basati sulle metriche, tuttavia l'inclusione di visualizzazioni basate su log potrebbe richiedere agli utenti di [abilitare la registrazione del Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-logging).

## <a name="configuring-your-key-vaults-for-monitoring"></a>Configurazione degli insiemi di credenziali delle chiavi per il monitoraggio

> [!NOTE]
> L'abilitazione dei log è un servizio a pagamento che fornisce funzionalità di monitoraggio aggiuntive.

1. La scheda operazioni & latenza consente di determinare il numero e gli insiemi di credenziali delle chiavi abilitati. Per iniziare la raccolta, selezionare il pulsante **Enable (Abilita** ), che consente di visualizzare una cartella di lavoro distinta in cui sono elencati gli insiemi di credenziali delle chiavi che richiedono l'abilitazione dei log di diagnostica.

    ![Screenshot della scheda operazioni e latenza con il pulsante Abilita blu visualizzato](./media/key-vaults-insights-overview/enable-logging.png)

2. Per abilitare i log di diagnostica, fare clic sul collegamento **Abilita** sotto la colonna azioni e creare una nuova impostazione di diagnostica che invii i log a un'area di lavoro log Analytics. È consigliabile inviare tutti i log alla stessa area di lavoro.

3. Dopo aver salvato le impostazioni di diagnostica, sarà possibile visualizzare tutti i grafici e le visualizzazioni basati su log sotto la Key Vault Insights. Si noti che potrebbero essere necessari alcuni minuti per iniziare a popolare i log.

4. Per ulteriore assistenza su come abilitare i log di diagnostica per il servizio Key Vault, vedere la [Guida completa](https://docs.microsoft.com/azure/key-vault/key-vault-logging).

## <a name="view-from-azure-monitor"></a>Visualizza da monitoraggio di Azure

Da monitoraggio di Azure è possibile visualizzare i dettagli relativi a richieste, latenza e errori da più insiemi di credenziali delle chiavi nella sottoscrizione e identificare i problemi di prestazioni e gli scenari di limitazione.

Per visualizzare l'utilizzo e le operazioni degli account di archiviazione in tutte le sottoscrizioni, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/)

2. Selezionare **Monitor** dal riquadro a sinistra nel portale di Azure e nella sezione Insights selezionare **Key Vaults (anteprima)**.

![Screenshot dell'esperienza panoramica con più grafici](./media/key-vaults-insights-overview/overview.png)

## <a name="overview-workbook"></a>Cartella di lavoro Panoramica

Nella cartella di lavoro Panoramica per la sottoscrizione selezionata la tabella Visualizza le metriche di Key Vault interattive per gli insiemi di credenziali delle chiavi raggruppati nella sottoscrizione. È possibile filtrare i risultati in base alle opzioni selezionate dagli elenchi a discesa seguenti:

* Sottoscrizioni: sono elencate solo le sottoscrizioni con Key Vault.

* Insiemi di credenziali delle chiavi: per impostazione predefinita, vengono preselezionati solo fino a 5 insiemi di credenziali delle chiavi. Se si selezionano tutti o più insiemi di credenziali delle chiavi nel selettore di ambito, verranno restituiti fino a 200 insiemi di credenziali delle chiavi. Se, ad esempio, si dispone di un totale di 573 insiemi di credenziali delle chiavi in tre sottoscrizioni selezionate, verranno visualizzati solo gli insiemi di credenziali 200.

* Intervallo di tempo: per impostazione predefinita, Visualizza le ultime 24 ore di informazioni in base alle selezioni corrispondenti effettuate.

Il riquadro contatore, sotto l'elenco a discesa, esegue il rollup del numero totale di insiemi di credenziali delle chiavi nelle sottoscrizioni selezionate e riflette il numero di insiemi di credenziali selezionato. Sono disponibili Heatmaps condizionali con codifica a colori per le colonne della cartella di lavoro che segnalano le metriche di richiesta, errori e latenza. Il colore più profondo ha il valore più alto e un colore più chiaro è basato sui valori più bassi.

## <a name="failures-workbook"></a>Cartella di lavoro errori

Selezionare **errori** nella parte superiore della pagina e verrà visualizzata la scheda errori. Mostra i riscontri dell'API, la frequenza nel tempo, oltre alla quantità di determinati codici di risposta.

![Screenshot della cartella di lavoro degli errori](./media/key-vaults-insights-overview/failures.png)

Per le colonne nella cartella di lavoro è presente una codifica dei colori condizionale o Heatmaps, in cui l'API di report raggiunge le metriche con un valore blu. Il colore più profondo ha il valore più alto e un colore più chiaro è basato sui valori più bassi.

La cartella di lavoro Visualizza le operazioni riuscite (codici di stato 2xx), errori di autenticazione (codici di stato 401/403), limitazione (codici di stato 429) e altri errori (codici di stato 4xx).

Per comprendere meglio cosa rappresentano i codici di stato, è consigliabile leggere la documentazione relativa ai [codici di stato e risposta Azure Key Vault](https://docs.microsoft.com/azure/key-vault/authentication-requests-and-responses).

## <a name="operations--latency-workbook"></a>Cartella di lavoro & latenza Operations

Selezionare **operazioni & latenza** nella parte superiore della pagina e verrà visualizzata la scheda **operazioni & latenza** . Questa scheda consente di caricare gli insiemi di credenziali delle chiavi per il monitoraggio. Per i passaggi più dettagliati, vedere la sezione configurazione degli insiemi [di credenziali delle chiavi per il monitoraggio](#configuring-your-key-vaults-for-monitoring) .

È possibile visualizzare il numero di insiemi di credenziali delle chiavi abilitati per la registrazione. Se almeno un insieme di credenziali è stato configurato correttamente, sarà possibile visualizzare le tabelle che visualizzano le operazioni e i codici di stato per ogni insieme di credenziali delle chiavi. È possibile fare clic nella sezione dei dettagli di una riga per ottenere informazioni aggiuntive sulla singola operazione.

![Screenshot dei grafici di operazioni e latenza](./media/key-vaults-insights-overview/logs.png)

Se non vengono visualizzati dati per questa sezione, fare riferimento alla sezione superiore su come abilitare i log per Azure Key Vault o consultare la sezione relativa alla risoluzione dei problemi riportata di seguito.

## <a name="view-from-a-key-vault-resource"></a>Visualizzare da una risorsa Key Vault

Per accedere a monitoraggio di Azure per Key Vault direttamente da un insieme di credenziali delle chiavi:

1. Nella portale di Azure selezionare Key Vault.

2. Dall'elenco scegliere un insieme di credenziali delle chiavi. Nella sezione monitoraggio scegliere Insights (anteprima).

Queste visualizzazioni sono accessibili anche selezionando il nome della risorsa di un insieme di credenziali delle chiavi dalla cartella di lavoro livello di monitoraggio di Azure.

![Screenshot della visualizzazione da una risorsa di Key Vault](./media/key-vaults-insights-overview/key-vault-resource-view.png)

Nella cartella di lavoro Panoramica per l' **insieme** di credenziali delle chiavi vengono visualizzate diverse metriche delle prestazioni che consentono di valutare rapidamente:

- Grafici delle prestazioni interattive con i dettagli più essenziali correlati a transazioni, latenza e disponibilità di Key Vault.

- Le metriche e i riquadri di stato evidenziano la disponibilità del servizio, il numero totale di transazioni per la risorsa di Key Vault e la latenza complessiva.

Selezionando una delle altre schede per gli **errori** o le **operazioni** vengono aperte le rispettive cartelle di lavoro.

![Screenshot della visualizzazione degli errori](./media/key-vaults-insights-overview/resource-failures.png)

La cartella di lavoro errori suddivide i risultati di tutte le richieste dell'insieme di credenziali delle chiavi nell'intervallo di tempo selezionato e fornisce la categorizzazione in successi (2xx), errori di autenticazione (401/403), limitazione (429) e altri errori.

![Screenshot della visualizzazione operazioni](./media/key-vaults-insights-overview/operations.png)

La cartella di lavoro operazioni consente agli utenti di approfondire tutti i dettagli di tutte le transazioni, che possono essere filtrate in base allo stato dei risultati tramite i riquadri di primo livello.

![Screenshot della visualizzazione operazioni](./media/key-vaults-insights-overview/info.png)

Gli utenti possono anche definire l'ambito delle visualizzazioni in base a tipi di transazioni specifici nella tabella superiore, che aggiorna in modo dinamico la tabella inferiore, in cui gli utenti possono visualizzare i dettagli completi dell'operazione in un riquadro del contesto popup.

>[!NOTE]
> Si noti che gli utenti devono avere le impostazioni di diagnostica abilitate per visualizzare questa cartella di lavoro. Per ulteriori informazioni sull'abilitazione dell'impostazione di diagnostica, vedere la pagina relativa alla [registrazione Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/logging).

## <a name="pin-and-export"></a>Pin ed export

È possibile aggiungere una delle sezioni della metrica a un dashboard di Azure selezionando l'icona a puntina da disegno in alto a destra nella sezione.

Le cartelle di lavoro Panoramica per più sottoscrizioni e insiemi di credenziali delle chiavi o errori supportano l'esportazione dei risultati in formato Excel selezionando l'icona di download a sinistra dell'icona a forma di puntina da disegno.

![Screenshot dell'icona del pin selezionata](./media/key-vaults-insights-overview/pin.png)

## <a name="customize-azure-monitor-for-key-vault"></a>Personalizzare monitoraggio di Azure per Key Vault

Questa sezione illustra gli scenari comuni per la modifica della cartella di lavoro per personalizzare il supporto delle esigenze di analisi dei dati:
*  Definire l'ambito della cartella di lavoro per selezionare sempre una sottoscrizione o un insieme di credenziali delle chiavi specifico
* Modificare le metriche nella griglia
* Modificare la soglia delle richieste
* Modificare il rendering del colore

È possibile iniziare le personalizzazioni abilitando la modalità di modifica selezionando il pulsante **Personalizza** sulla barra degli strumenti superiore.

![Screenshot del pulsante Personalizza](./media/key-vaults-insights-overview/customize.png)

Le personalizzazioni vengono salvate in una cartella di lavoro personalizzata per impedire la sovrascrittura della configurazione predefinita nella cartella di lavoro pubblicata. Le cartelle di lavoro vengono salvate in un gruppo di risorse, nella sezione Report personali privata o nella sezione report condivisi accessibile a tutti gli utenti con accesso al gruppo di risorse. Dopo avere salvato la cartella di lavoro personalizzata, è necessario passare alla raccolta di cartelle di lavoro per avviarla.

![Screenshot della raccolta di cartelle di lavoro](./media/key-vaults-insights-overview/gallery.png)

### <a name="specifying-a-subscription-or-key-vault"></a>Specifica di una sottoscrizione o di un insieme di credenziali delle chiavi

È possibile configurare la panoramica su più sottoscrizioni e insiemi di credenziali delle chiavi o le cartelle di lavoro degli errori per l'ambito di una o più sottoscrizioni o Key Vault a ogni esecuzione, eseguendo i passaggi seguenti:

1. Selezionare **monitoraggio** dal portale e quindi selezionare **Key Vault (anteprima)** nel riquadro a sinistra.
2. Nella cartella di lavoro **Panoramica** , dalla barra dei comandi, selezionare **modifica**.
3. Selezionare dall'elenco a discesa **sottoscrizioni** una o più sottoscrizioni che si desidera utilizzare come predefinite. Tenere presente che la cartella di lavoro supporta la selezione di un totale di 10 sottoscrizioni.
4. Selezionare dall'elenco a discesa **Key Vaults** uno o più account da usare come predefinito. Tenere presente che la cartella di lavoro supporta la selezione di un totale di 200 account di archiviazione.
5. Selezionare **Salva con nome** dalla barra dei comandi per salvare una copia della cartella di lavoro con le personalizzazioni, quindi fare clic su **modifica eseguita** per tornare alla modalità di lettura.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Questa sezione illustra la diagnosi e la risoluzione dei problemi comuni che possono verificarsi quando si usa monitoraggio di Azure per Key Vault (anteprima). Usare l'elenco che segue per individuare le informazioni pertinenti a un problema specifico.

### <a name="resolving-performance-issues-or-failures"></a>Risoluzione dei problemi di prestazioni o degli errori

Per risolvere i problemi relativi all'insieme di credenziali delle chiavi identificato con monitoraggio di Azure per Key Vault (anteprima), vedere la [documentazione di Azure Key Vault](https://docs.microsoft.com/azure/key-vault/).

### <a name="why-can-i-only-see-200-key-vaults"></a>Perché è possibile visualizzare solo gli insiemi di credenziali delle chiavi 200?

È previsto un limite di 200 insiemi di credenziali delle chiavi che possono essere selezionati e visualizzati. Indipendentemente dal numero di sottoscrizioni selezionate, il numero di insiemi di credenziali delle chiavi selezionato ha un limite di 200.

### <a name="what-will-happen-when-a-pinned-item-is-clicked"></a>Cosa accade quando si fa clic su un elemento bloccato?

Quando si fa clic su un elemento aggiunto nel dashboard, viene aperto uno dei due elementi seguenti:
* Se le informazioni sono state salvate, verrà visualizzata l'istanza di Insights da cui è stato salvato il PIN.
* Se le informazioni sono state salvate, verrà aperta una nuova istanza di Insights predefinita.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Perché non vengono visualizzate tutte le sottoscrizioni in selezione sottoscrizione?

Vengono mostrate solo le sottoscrizioni che contengono insiemi di credenziali delle chiavi, scelti dal filtro della sottoscrizione selezionato, selezionati nella "directory + sottoscrizione" nell'intestazione del portale di Azure.

![Screenshot del filtro di sottoscrizione](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-am-getting-an-error-message-that-the-query-exceeds-the-maximum-number-of-workspacesregions-allowed-what-to-do-now"></a>Viene ricevuto un messaggio di errore che segnala che la "query supera il numero massimo di aree di lavoro/aree consentite", cosa fare ora?

Attualmente, per visualizzare i dati è previsto un limite di 25 aree e di aree di lavoro di 200, sarà necessario ridurre il numero di sottoscrizioni e/o gruppi di risorse.

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>Si vuole apportare modifiche o aggiungere altre visualizzazioni a Key Vault Insights, come procedere?

Per apportare modifiche, selezionare la "modalità di modifica" per modificare la cartella di lavoro, quindi è possibile salvare il lavoro come una nuova cartella di lavoro associata a una sottoscrizione e a un gruppo di risorse designati.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Qual è il tempo di granularità dopo che è stata aggiunta una parte delle cartelle di lavoro?

Viene utilizzato il tempo di "auto", pertanto dipende dall'intervallo di tempo selezionato.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Qual è l'intervallo di tempo in cui una parte della cartella di lavoro è bloccata?

L'intervallo di tempo dipende dalle impostazioni del dashboard.

### <a name="why-do-i-not-see-any-data-for-my-key-vault-under-the-operations--latency-sections"></a>Perché non vengono visualizzati dati per la Key Vault nelle sezioni operazioni & latenza?

Per visualizzare i dati basati su log, è necessario abilitare i log per ogni insieme di credenziali delle chiavi che si vuole monitorare. Questa operazione può essere eseguita con le impostazioni di diagnostica per ogni insieme di credenziali delle chiavi. Sarà necessario inviare i dati a un'area di lavoro Log Analytics designata.

### <a name="i-have-already-enabled-logs-for-my-key-vault-why-am-i-still-unable-to-see-my-data-under-operations--latency"></a>Sono già stati abilitati i log per la Key Vault, perché non è ancora possibile visualizzare i dati in Operations & latenza?

Attualmente, i log di diagnostica non funzionano in modo retroattivo, quindi i dati verranno visualizzati solo dopo che sono state intraprese le azioni per gli insiemi di credenziali delle chiavi. Pertanto, l'intervallo di tempo può richiedere un po' di tempo, a seconda della modalità di attivazione dell'insieme di credenziali delle chiavi.

Inoltre, se è stato selezionato un numero elevato di insiemi di credenziali delle chiavi e sottoscrizioni, potrebbe non essere possibile visualizzare i dati a causa di limitazioni delle query. Per visualizzare i dati, potrebbe essere necessario ridurre il numero di sottoscrizioni o insiemi di credenziali delle chiavi selezionati. 

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>Cosa accade se si vogliono visualizzare altri dati o creare visualizzazioni personali? Come è possibile apportare modifiche a Key Vault Insights?

È possibile modificare la cartella di lavoro esistente, tramite l'uso della modalità di modifica, quindi salvare il lavoro come una nuova cartella di lavoro che avrà tutte le nuove modifiche.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle cartelle di lavoro degli scenari sono progettate per supportare, come creare nuovi report esistenti e personalizzarli e altro ancora esaminando [creare report interattivi con le cartelle di lavoro di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).
