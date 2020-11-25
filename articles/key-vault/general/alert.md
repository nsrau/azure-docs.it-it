---
title: Monitoraggio e avviso Azure Key Vault | Microsoft Docs
description: Creare un dashboard per monitorare l'integrità dell'insieme di credenziali delle chiavi e configurare gli avvisi.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: 9195bb59264731914740e1cca902707603e3502d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018121"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Monitoraggio e avvisi per Azure Key Vault

## <a name="overview"></a>Panoramica

Dopo aver iniziato a usare Key Vault per archiviare i segreti di produzione, è importante monitorare l'integrità dell'insieme di credenziali delle chiavi per assicurarsi che il servizio funzioni come previsto. Quando si inizia a ridimensionare il servizio, il numero di richieste inviate all'insieme di credenziali delle chiavi aumenta. Di conseguenza, può aumentare anche la latenza delle richieste e, in casi estremi, le richieste possono venire limitate, con un impatto sulle prestazioni del servizio. È anche necessario ricevere un avviso se l'insieme di credenziali delle chiavi Invia un numero insolito di codici di errore, in modo che sia possibile ricevere una notifica rapida di eventuali problemi di configurazione del firewall o dei criteri di accesso. In questo documento vengono trattati gli argomenti seguenti:

+ Metriche di base Key Vault da monitorare
+ Come configurare le metriche e creare un dashboard 
+ Come creare avvisi a soglie specificate 

## <a name="basic-key-vault-metrics-to-monitor"></a>Metriche di base Key Vault da monitorare

+ Disponibilità dell'insieme di credenziali  
+ Saturazione dell'insieme di credenziali 
+ Latenza API del servizio 
+ Totale riscontri API servizio (filtro per tipo di attività) 
+ Codici di errore (Filtra in base al codice di stato) 

**Disponibilità** dell'insieme di credenziali: questa metrica deve essere sempre al 100%. si tratta di una metrica importante da monitorare, perché può indicare rapidamente se l'insieme di credenziali delle chiavi ha riscontrato un'interruzione del servizio. 

**Saturazione** dell'insieme di credenziali: il numero di richieste al secondo che può essere utilizzato da un insieme di credenziali delle chiavi è basato sul tipo di operazione eseguita. Alcune operazioni dell'insieme di credenziali hanno una soglia di richieste al secondo inferiori. Questa metrica aggrega l'utilizzo totale dell'insieme di credenziali delle chiavi in tutti i tipi di operazione per trovare un valore percentuale che indica l'utilizzo corrente dell'insieme di credenziali delle chiavi. Per un elenco completo dei limiti del servizio Key Vault, vedere il documento seguente. [Limiti dei servizi Azure Key Vault ](service-limits.md)

**Latenza API del servizio** : questa metrica Mostra la latenza media di una chiamata a Key Vault. Anche se l'insieme di credenziali delle chiavi può essere entro i limiti del servizio, un utilizzo elevato dell'insieme di credenziali delle chiavi può comportare una latenza che impedisce la riuscita delle applicazioni 

**Totale riscontri API** : questa metrica Mostra tutte le chiamate effettuate all'insieme di credenziali delle chiavi. Ciò consentirà di identificare le applicazioni che chiamano l'insieme di credenziali delle chiavi. 

**Codici di errore** : questa metrica indicherà se l'insieme di credenziali delle chiavi sta riscontrando una quantità di errori insolita. Per un elenco completo dei codici di errore e delle linee guida per la risoluzione dei problemi, vedere il documento seguente. [Codici di errore dell'API REST di Azure Key Vault](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Come configurare le metriche e creare un dashboard

1. Accedere al portale di Azure
2. Passare alla Key Vault
3. Selezionare le **metriche** in **monitoraggio** 

> [!div class="mx-imgBorder"]
> ![Schermata che evidenzia l'opzione metrica nella sezione monitoraggio.](../media/alert-1.png)

4. Aggiornare il titolo del grafico a quello che si vuole visualizzare nel dashboard. 
5. Selezionare l'ambito. In questo esempio verrà selezionato un singolo insieme di credenziali delle chiavi. 
6. Selezionare la metrica di disponibilità **e aggregazione** dell'insieme di credenziali **generale** 
7. Aggiornare l'intervallo di tempo alle ultime 24 ore e aggiornare la granularità dell'ora a 1 minuto. 

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra la metrica globale dell'insieme di credenziali disponibilità.](../media/alert-2.png)

8. Ripetere i passaggi precedenti per la metrica dell'insieme di credenziali e la latenza dell'API del servizio. Selezionare **Aggiungi al dashboard** per salvare le metriche in un dashboard. 

> [!IMPORTANT]
> Selezionare "Aggiungi al dashboard" e salvare tutte le metriche configurate. Se si esce dalla pagina e si torna al suo esterno senza salvare, le modifiche apportate alla configurazione andranno perse. 

9. Per monitorare tutti i tipi di operazioni nell'insieme di credenziali delle chiavi, usare la metrica **Totale riscontri API del servizio** e selezionare **applica suddivisione in base al tipo di attività**

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra il pulsante Applica suddivisione.](../media/alert-3.png)

10. Per monitorare i codici di errore nell'insieme di credenziali delle chiavi, usare la metrica **Totale risultati dell'API del servizio** e selezionare **applica suddivisione in base al tipo di attività**

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra la metrica totale dei risultati dell'API del servizio selezionata.](../media/alert-4.png)

A questo punto si avrà un dashboard simile al seguente. È possibile fare clic sui tre puntini nella parte superiore destra di ogni riquadro ed è possibile ridisporre e ridimensionare i riquadri in base alle esigenze. 

Dopo aver salvato e pubblicato il dashboard, verrà creata una nuova risorsa nella sottoscrizione di Azure. Sarà possibile visualizzarlo in qualsiasi momento cercando "Dashboard condiviso". 

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra il dashboard pubblicato.](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Come configurare gli avvisi nel Key Vault 

Questa sezione illustra come configurare gli avvisi per l'insieme di credenziali delle chiavi in modo che sia possibile avvisare il team di agire immediatamente se l'insieme di credenziali delle chiavi è in uno stato non integro. È possibile configurare gli avvisi che inviano un messaggio di posta elettronica, preferibilmente a un team DL, attivare una notifica di griglia di eventi o chiamare un numero di telefono o un SMS. È anche possibile scegliere avvisi statici basati su un valore fisso o un avviso dinamico che avvisa se una metrica monitorata supera il limite medio dell'insieme di credenziali delle chiavi per un determinato numero di volte in un intervallo di tempo definito. 

> [!IMPORTANT]
> Si noti che possono essere necessari fino a 10 minuti per l'avvio dell'invio delle notifiche da parte degli avvisi appena configurati. 

### <a name="configure-an-action-group"></a>Configurare un gruppo di azione 

Un gruppo di azioni è un elenco configurabile di notifiche e proprietà.

1. Accedere al portale di Azure
2. Cerca **avvisi** nella casella di ricerca
3. Selezionare **Gestisci azioni**

> [!div class="mx-imgBorder"]
> ![Schermata che evidenzia il pulsante Gestisci azioni.](../media/alert-6.png)

4. Selezionare **+ Aggiungi gruppo di azioni**

> [!div class="mx-imgBorder"]
> ![Schermata che evidenzia il pulsante + Aggiungi gruppo di azioni.](../media/alert-7.png)

5. Scegliere il **tipo di azione** per il gruppo di azioni. In questo esempio verrà creato un avviso di posta elettronica.

> [!div class="mx-imgBorder"]
> ![Screenshot che evidenzia i campi necessari per aggiungere un gruppo di azione.](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Screenshot che Mostra gli elementi necessari per aggiungere un messaggio di posta elettronica o un avviso di SMS.](../media/alert-9.png)

6. Fare clic su **OK** nella parte inferiore della pagina. Creazione di un gruppo di azione completata. 

Ora che è stato configurato un gruppo di azione, verranno configurate le soglie di avviso di Key Vault. 

### <a name="configure-alert-thresholds"></a>Configurare le soglie degli avvisi 

1. Selezionare la risorsa dell'insieme di credenziali delle chiavi nella portale di Azure e selezionare gli **avvisi** sotto il **monitoraggio**

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra l'opzione di menu avvisi nella sezione monitoraggio.](../media/alert-10.png)

2. Seleziona **nuova regola di avviso**

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra il pulsante + nuova regola di avviso.](../media/alert-11.png)

3. Consente di selezionare l'ambito della regola di avviso. È possibile selezionare un singolo insieme di credenziali o più di un insieme di credenziali. 

> [!IMPORTANT]
> Si noti che quando si selezionano più insiemi di credenziali per l'ambito degli avvisi, tutti gli insiemi di credenziali selezionati devono trovarsi nella stessa area. Sarà necessario configurare regole di avviso separate per gli insiemi di credenziali in aree diverse. 

> [!div class="mx-imgBorder"]
> ![Screenshot che illustra come è possibile selezionare un insieme di credenziali.](../media/alert-12.png)

4. Selezionare le condizioni per gli avvisi. È possibile scegliere uno dei segnali seguenti e definire la logica per l'invio di avvisi. Il team di Key Vault consiglia di configurare le soglie di avviso seguenti. 

    + Key Vault disponibilità scende sotto il 100% (soglia statica)
    + La latenza Key Vault è maggiore di 500 ms (soglia statica) 
    + Saturazione complessiva dell'insieme di credenziali superiore al 75% (soglia statica) 
    + Saturazione globale dell'insieme di credenziali supera la media (soglia dinamica)
    + Codici di errore totali superiori alla media (soglia dinamica) 

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra dove è possibile selezionare le condizioni per gli avvisi.](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Esempio 1: configurazione di una soglia di avviso statica per la latenza

Selezionare la **latenza dell'API del servizio complessiva** come nome del segnale
> [!div class="mx-imgBorder"]
> ![Screenshot che mostra il nome generale del segnale di latenza dell'API del servizio.](../media/alert-14.png)

Vedere i seguenti parametri di configurazione.

+ Imposta la soglia su **statica** 
+ Impostare l'operatore su un **valore maggiore di**
+ Imposta il tipo di aggregazione su **media**
+ Impostare il valore soglia su **500**
+ Impostare il periodo di aggregazione su **5 minuti**
+ Imposta la frequenza di valutazione su **1 minuto**
+ Selezionare **Operazione completata**  

> [!div class="mx-imgBorder"]
> ![Screenshot che evidenzia la logica di avviso configurata.](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Esempio 2: configurazione di una soglia di avviso dinamica per la saturazione dell'insieme di credenziali 

Quando si usa un avviso dinamico, sarà possibile visualizzare i dati cronologici dell'insieme di credenziali delle chiavi selezionato. L'area blu rappresenta l'utilizzo medio dell'insieme di credenziali delle chiavi. L'area rossa mostra picchi che avrebbero attivato un avviso purché siano soddisfatti altri criteri nella configurazione degli avvisi. I punti rossi mostrano le istanze di violazioni in cui i criteri per l'avviso sono stati soddisfatti durante l'intervallo di tempo aggregato. È possibile impostare un avviso in modo che venga attivato dopo un determinato numero di violazioni entro un determinato periodo di tempo. Se non si desidera includere dati precedenti, è disponibile un'opzione che consente di escludere i dati obsoleti nelle impostazioni avanzate. 

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra un grafico della saturazione complessiva dell'insieme di credenziali.](../media/alert-16.png)

Vedere i seguenti parametri di configurazione.

+ Impostare la soglia su **dinamica** 
+ Impostare l'operatore su un **valore maggiore di**
+ Imposta il tipo di aggregazione su **media**
+ Imposta la sensibilità della soglia su **media**
+ Impostare il periodo di aggregazione su **5 minuti**
+ Imposta la frequenza di valutazione su **1 minuto**
+ **Facoltativo** Configurare le impostazioni avanzate 
+ Selezionare **Operazione completata**

> [!div class="mx-imgBorder"]
> ![Screenshot di portale di Azure](../media/alert-17.png)

5. Aggiungere il gruppo di azioni configurato

> [!div class="mx-imgBorder"]
> ![Screenshot che illustra come aggiungere un gruppo di azione.](../media/alert-18.png)

6. Abilitare l'avviso e assegnare un livello di gravità

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra dove abilitare l'avviso e assegnare una gravità.](../media/alert-19.png)

7. Creare l'avviso 


## <a name="next-steps"></a>Passaggi successivi

A questo punto, è stato creato un dashboard di monitoraggio e sono stati configurati gli avvisi per l'insieme di credenziali delle chiavi. Dopo aver seguito tutti i passaggi precedenti, è necessario ricevere avvisi di posta elettronica quando l'insieme di credenziali delle chiavi soddisfa i criteri di avviso configurati. Di seguito è illustrato un esempio. Usare gli strumenti configurati in questo articolo per monitorare attivamente l'integrità dell'insieme di credenziali delle chiavi. 

### <a name="example-email-alert"></a>Avviso di posta elettronica di esempio 

> [!div class="mx-imgBorder"]
> ![Screenshot che evidenzia le informazioni necessarie per configurare un avviso di posta elettronica.](../media/alert-20.png)
