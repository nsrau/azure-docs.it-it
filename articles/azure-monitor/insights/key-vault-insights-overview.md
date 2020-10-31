---
title: Monitorare Key Vault con monitoraggio di Azure per Key Vault | Microsoft Docs
description: Questo articolo descrive Monitoraggio di Azure per Key Vault.
services: azure-monitor
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/10/2020
ms.openlocfilehash: d28138b251cf159b7cbc5cc6942bb287e57a95d7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93088098"
---
# <a name="monitoring-your-key-vault-service-with-azure-monitor-for-key-vault"></a>Monitoraggio del servizio Key Vault con monitoraggio di Azure per Key Vault
Monitoraggio di Azure per Key Vault offre un monitoraggio completo degli insiemi di credenziali delle chiavi grazie a una visualizzazione unificata delle richieste Key Vault, delle prestazioni, degli errori e della latenza.
Questo articolo consente di comprendere come eseguire l'onboarding e personalizzare l'esperienza di monitoraggio di Azure per Key Vault.

## <a name="introduction-to-azure-monitor-for-key-vault"></a>Introduzione a monitoraggio di Azure per Key Vault

Prima di approfondire l'esperienza è necessario comprendere il modo in cui le informazioni vengono presentate e visualizzate.
-    **Prospettiva su larga scala** , che mostra una visualizzazione snapshot delle prestazioni in base alle richieste, una suddivisione degli errori e a una panoramica di operazioni e latenza.
-   **Analisi drill-down** di un determinato insieme di credenziali delle chiavi per eseguire analisi dettagliate.
-    **Personalizzabile** perché è possibile cambiare le metriche da visualizzare, modificare o su cui impostare soglie in linea con i limiti previsti e salvare la propria cartella di lavoro. I grafici della cartella di lavoro possono essere aggiunti ai dashboard di Azure.

Monitoraggio di Azure per Key Vault combina log e metriche per offrire una soluzione di monitoraggio globale. Tutti gli utenti possono accedere ai dati di monitoraggio basati su metriche, tuttavia l'inclusione di visualizzazioni basate su log potrebbe richiedere di [abilitare la registrazione dell'istanza di Azure Key Vault](../../key-vault/general/logging.md).

## <a name="view-from-azure-monitor"></a>Visualizzazione in Monitoraggio di Azure

In Monitoraggio di Azure è possibile visualizzare i dettagli su richieste, latenza ed errori di più insiemi di credenziali delle chiavi disponibili nella sottoscrizione, oltre a identificare problemi di prestazioni e scenari di limitazione delle richieste.

Per visualizzare l'utilizzo e le operazioni degli insiemi di credenziali delle chiavi in tutte le sottoscrizioni, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/)

2. Selezionare **monitoraggio** dal riquadro a sinistra nel portale di Azure e nella sezione Insights selezionare **Key Vaults** (insiemi di credenziali delle chiavi).

![Screenshot dell'esperienza di panoramica con più grafici](./media/key-vaults-insights-overview/overview.png)

## <a name="overview-workbook"></a>Cartella di lavoro Panoramica

Nella cartella di lavoro Panoramica per la sottoscrizione selezionata la tabella visualizza metriche interattive per gli insiemi di credenziali delle chiavi raggruppati all'interno della sottoscrizione. È possibile filtrare i risultati in base alle opzioni selezionate negli elenchi a discesa seguenti:

* Sottoscrizioni: sono elencate solo le sottoscrizioni con insiemi di credenziali delle chiavi.

* Insiemi di credenziali delle chiavi: per impostazione predefinita, vengono preselezionati solo 5 insiemi di credenziali delle chiavi in totale. Se si selezionano più insiemi di credenziali delle chiavi nel selettore di ambito, ne verranno restituiti al massimo 200. Se ad esempio sono presenti 573 insiemi di credenziali delle chiavi in totale tra tre sottoscrizioni selezionate, ne verranno visualizzati solo 200.

* Intervallo di tempo: per impostazione predefinita, vengono visualizzate le ultime 24 ore di informazioni in base alle selezioni corrispondenti effettuate.

Il riquadro del contatore sotto gli elenchi a discesa riepiloga il numero totale di insiemi di credenziali delle chiavi nella sottoscrizione e indica quanti ne sono selezionati. Sono disponibili mappe termiche condizionali con codifica a colori per le colonne della cartella di lavoro che segnalano le metriche di richieste, errori e latenza. Il colore più profondo presenta il valore più elevato, mentre un colore più chiaro deriva da valori più bassi.

## <a name="failures-workbook"></a>Cartella di lavoro Errori

Selezionare **Errori** nella parte superiore della pagina per aprire la scheda corrispondente. Visualizza gli accessi alle API, la frequenza nel corso del tempo, oltre alla quantità di specifici codici di risposta.

![Screenshot della cartella di lavoro Errori](./media/key-vaults-insights-overview/failures.png)

Per le colonne della cartella di lavoro vengono usate una codifica a colori o mappe termiche condizionali per indicare le metriche degli accessi alle API con un valore blu. Il colore più profondo presenta il valore più elevato, mentre un colore più chiaro deriva da valori più bassi.

La cartella di lavoro visualizza le operazioni riuscite (codici di stato 2xx), gli errori di autenticazione (codici di stato 401/403), la limitazione delle richieste (codici di stato 429) e altri errori (codici di stato 4xx).

Per capire meglio cosa rappresenta ogni codice di stato, è consigliabile leggere la documentazione su [codici di risposta e di stato di Azure Key Vault](../../key-vault/general/authentication-requests-and-responses.md).

## <a name="view-from-a-key-vault-resource"></a>Visualizzazione in una risorsa Key Vault

Per accedere a Monitoraggio di Azure per Key Vault direttamente da un insieme di credenziali delle chiavi:

1. Nella portale di Azure selezionare Insiemi di credenziali delle chiavi.

2. Scegliere un insieme di credenziali delle chiavi nell'elenco. Nella sezione monitoraggio scegliere Insights.

Queste visualizzazioni sono accessibili anche selezionando il nome della risorsa di un insieme di credenziali delle chiavi nella cartella di lavoro a livello di Monitoraggio di Azure.

![Screenshot di una visualizzazione di una risorsa insieme di credenziali delle chiavi](./media/key-vaults-insights-overview/key-vault-resource-view.png)

Nella cartella di lavoro **Panoramica** per l'account di archiviazione vengono visualizzate diverse metriche delle prestazioni per una valutazione rapida di:

- Grafici interattivi delle prestazioni, che mostrano i dettagli più importanti relativi a transazioni, latenza e disponibilità degli insiemi di credenziali delle chiavi.

- Riquadri di metriche e stato, che evidenziano la disponibilità del servizio, il numero totale di transazioni per la risorsa insieme di credenziali delle chiavi e la latenza complessiva.

Selezionando una delle altre schede per **Errori** o **Operazioni** vengono aperte le rispettive cartelle di lavoro.

![Screenshot della visualizzazione errori](./media/key-vaults-insights-overview/resource-failures.png)

La cartella di lavoro Errori suddivide i risultati di tutte le richieste effettuate agli insiemi di credenziali delle chiavi nell'intervallo di tempo selezionato e fornisce la categorizzazione in base a operazioni completate (2xx), errori di autenticazione (401/403), limitazione delle richieste (429) e altri errori.

![Screenshot della visualizzazione operazioni](./media/key-vaults-insights-overview/operations.png)

La cartella di lavoro Operazioni consente di approfondire i dettagli completi di tutte le transazioni, che possono essere filtrati per stato del risultato tramite i riquadri superiori.

![Screenshot che mostra la cartella di lavoro delle operazioni che contiene i dettagli completi di tutte le transazioni.](./media/key-vaults-insights-overview/info.png)

Gli utenti possono anche definire l'ambito delle visualizzazioni in base a specifici tipi di transazioni nella tabella superiore, che aggiorna in modo dinamico la tabella inferiore, in cui è possibile visualizzare i dettagli completi delle operazioni nel riquadro contestuale popup.

>[!NOTE]
> Si noti che per visualizzare questa cartella di lavoro, è necessario aver abilitato le impostazioni di diagnostica. Per altre informazioni sull'abilitazione dell'impostazione di diagnostica, vedere [Registrazione di Azure Key Vault](../../key-vault/general/logging.md).

## <a name="pin-and-export"></a>Aggiunta ed esportazione

È possibile aggiungere una qualsiasi delle sezioni di metriche a un dashboard di Azure selezionando l'icona a forma di puntina da disegno in alto a destra nella sezione.

La panoramica relativa a più sottoscrizioni e insiemi di credenziali delle chiavi o le cartelle di lavoro degli errori supportano l'esportazione dei risultati in formato Excel selezionando l'icona del download a sinistra dell'icona della puntina da disegno.

![Screenshot dell'icona della puntina da disegno selezionata](./media/key-vaults-insights-overview/pin.png)

## <a name="customize-azure-monitor-for-key-vault"></a>Personalizzare Monitoraggio di Azure per Key Vault

Questa sezione illustra gli scenari comuni in cui è utile modificare la cartella di lavoro per personalizzarla a supporto delle proprie esigenze di analisi dei dati:
*  Definire l'ambito della cartella di lavoro in modo da selezionare sempre una particolare sottoscrizione o uno o più insiemi di credenziali delle chiavi specifici
* Cambiare le metriche nella griglia
* Cambiare la soglia delle richieste
* Cambiare il rendering dei colori

Per iniziare le personalizzazioni, abilitare la modalità di modifica selezionando il pulsante **Personalizza** sulla barra degli strumenti superiore.

![Screenshot del pulsante personalizza](./media/key-vaults-insights-overview/customize.png)

Le personalizzazioni vengono salvate in una cartella di lavoro personalizzata per evitare la sovrascrittura della configurazione predefinita nella cartella di lavoro pubblicata. Le cartelle di lavoro vengono salvate all'interno di un gruppo di risorse, ovvero nella sezione Report personali privata o nella sezione Report condivisi accessibile a tutti gli utenti che hanno accesso al gruppo di risorse. Dopo aver salvato la cartella di lavoro personalizzata, per avviarla è necessario passare alla raccolta di cartelle di lavoro.

![Screenshot della raccolta di cartelle di lavoro](./media/key-vaults-insights-overview/gallery.png)

### <a name="specifying-a-subscription-or-key-vault"></a>Specifica di una sottoscrizione o di un insieme di credenziali delle chiavi

È possibile configurare la panoramica relativa a più sottoscrizioni e insiemi di credenziali delle chiavi o le cartelle di lavoro degli errori in modo da includere nell'ambito una o più sottoscrizioni o uno o più insiemi di credenziali delle chiavi per ogni esecuzione, seguendo questa procedura:

1. Selezionare **monitoraggio** dal portale e quindi selezionare insiemi di credenziali delle **chiavi** nel riquadro a sinistra.
2. Nella cartella di lavoro **Panoramica** selezionare **Modifica** sulla barra dei comandi.
3. Nell'elenco a discesa **Sottoscrizioni** selezionare una o più sottoscrizioni da usare come predefinite. Tenere presente che la cartella di lavoro supporta la selezione di un totale di 10 sottoscrizioni.
4. Nell'elenco a discesa **Insiemi di credenziali delle chiavi** selezionare uno o più account da usare come predefiniti. Tenere presente che la cartella di lavoro supporta la selezione di un totale di 200 account di archiviazione.
5. Selezionare **Salva con nome** sulla barra dei comandi per salvare una copia della cartella di lavoro con le personalizzazioni, quindi fare clic su **Modifica completata** per tornare alla modalità di lettura.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Per indicazioni generali sulla risoluzione dei problemi, vedere l'articolo dedicato alla [risoluzione dei](troubleshoot-workbooks.md)problemi di Insights basato sulla cartella di lavoro.

Questa sezione illustra la diagnosi e la risoluzione dei problemi comuni che possono verificarsi quando si usa monitoraggio di Azure per Key Vault. Usare l'elenco che segue per individuare le informazioni pertinenti a un problema specifico.

### <a name="resolving-performance-issues-or-failures"></a>Risoluzione dei problemi di prestazioni o degli errori

Per risolvere i problemi relativi all'insieme di credenziali delle chiavi identificato con monitoraggio di Azure per Key Vault, vedere la [documentazione di Azure Key Vault](../../key-vault/index.yml).

### <a name="why-can-i-only-see-200-key-vaults"></a>Perché è possibile visualizzare solo gli insiemi di credenziali delle chiavi 200

È previsto un limite di 200 insiemi di credenziali delle chiavi che è possibile selezionare e visualizzare. A prescindere dal numero di sottoscrizioni selezionate, il numero di insiemi di credenziali delle chiavi selezionati prevede un limite di 200.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Perché non vengono visualizzate tutte le sottoscrizioni nella selezione della sottoscrizione

Vengono mostrate solo le sottoscrizioni che contengono insiemi di credenziali delle chiavi, scelte tramite il filtro di sottoscrizioni selezionato, che sono selezionate nella sezione "Directory e sottoscrizione" nell'intestazione del portale di Azure.

![Screenshot del filtro di sottoscrizioni](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>Desidero apportare modifiche o aggiungere altre visualizzazioni a Key Vault Insights, come faccio

Per apportare modifiche, selezionare "Modalità di modifica" per modificare la cartella di lavoro, quindi è possibile salvare il lavoro come una nuova cartella di lavoro associata a una sottoscrizione e a un gruppo di risorse designati.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Qual è il tempo di granularità dopo che è stata aggiunta una parte delle cartelle di lavoro

La cadenza temporale è automatica, quindi dipende dall'intervallo di tempo selezionato.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Qual è l'intervallo di tempo in cui una parte della cartella di lavoro è bloccata

L'intervallo di tempo dipende dalle impostazioni del dashboard.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>È possibile vedere altri dati o creare visualizzazioni personali? Come è possibile apportare modifiche a Key Vault Insights

È possibile modificare la cartella di lavoro esistente, tramite la modalità di modifica, quindi salvare il lavoro come una nuova cartella di lavoro che conterrà tutte le nuove modifiche.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sugli scenari che le cartelle di lavoro sono progettate per supportare, su come creare nuovi report e personalizzare report esistenti e altro ancora sono disponibili nell'articolo [Creare report interattivi con le cartelle di lavoro di Monitoraggio di Azure](../platform/workbooks-overview.md).
