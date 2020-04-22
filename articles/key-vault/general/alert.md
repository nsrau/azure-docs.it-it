---
title: Monitoraggio e avvisi dell'insieme di chiavi di Azure Documenti Microsoft
description: Creare un dashboard per monitorare l'integrità dell'insieme di credenziali delle chiavi e configurare gli avvisi.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: cc0d969ff6eb76732768dfed2826762920ae9e62
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726943"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Monitoraggio e avvisi per l'insieme di credenziali delle chiavi di AzureMonitoring and alerting for Azure Key Vault

## <a name="overview"></a>Panoramica

Una volta che hai iniziato a utilizzare l'insieme di credenziali delle chiavi per archiviare i segreti di produzione, è importante monitorare l'integrità dell'insieme di credenziali delle chiavi per assicurarsi che il servizio funzioni come previsto. Quando si inizia a scalare il servizio, il numero di richieste inviate all'insieme di credenziali delle chiavi aumenterà. Ciò ha un potenziale per aumentare la latenza delle richieste e, in casi estremi, causare la limitazione delle richieste che avranno un impatto sulle prestazioni del servizio. È inoltre necessario ricevere un avviso se l'insieme di credenziali delle chiavi invia un numero insolito di codici di errore, in modo da poter ricevere rapidamente una notifica di eventuali problemi di configurazione del firewall o dei criteri di accesso. In questo documento verranno trattati i seguenti argomenti:

+ Metriche dell'insieme di chiavi di base da monitorareBasic Key Vault metrics to monitor
+ Come configurare le metriche e creare un dashboard 
+ Come creare avvisi a soglie specifiche 

## <a name="basic-key-vault-metrics-to-monitor"></a>Metriche dell'insieme di chiavi di base da monitorareBasic Key Vault metrics to monitor

+ Disponibilità del Vault  
+ Saturazione del Vault 
+ Latenza API servizioService API Latency 
+ Totale oggetti API del servizio (filtro per tipo di attività)Total Service API Hits (Filter by Activity Type) 
+ Codici di errore (filtro in base al codice di stato) 

**Disponibilità dell'insieme** di credenziali: questa metrica deve essere sempre al 100%, questa è una metrica importante da monitorare, poiché può mostrare rapidamente se l'insieme di credenziali delle chiavi ha subito un'interruzione. 

**Saturazione vault:** il numero di richieste al secondo che un insieme di credenziali delle chiavi può servire si basa sul tipo di operazione eseguita. Alcune operazioni dell'insieme di credenziali hanno una soglia di richieste al secondo inferiore. Questa metrica aggrega l'utilizzo totale dell'insieme di credenziali delle chiavi in tutti i tipi di operazione per ottenere un valore percentuale che indica l'utilizzo corrente dell'insieme di credenziali delle chiavi. Per un elenco completo dei limiti del servizio dell'insieme di credenziali delle chiavi, vedere il documento seguente. [Limiti dei servizi Azure Key Vault ](service-limits.md)

**Latenza API servizio:** questa metrica indica la latenza media di una chiamata all'insieme di credenziali delle chiavi. Anche se l'insieme di credenziali delle chiavi potrebbe essere entro i limiti del servizio, un utilizzo elevato dell'insieme di credenziali delle chiavi potrebbe introdurre una latenza che causa l'esito negativo delle applicazioni a valle. 

**Totale riscontri API:** questa metrica mostra tutte le chiamate effettuate all'insieme di credenziali delle chiavi. In questo modo sarà possibile identificare le applicazioni che chiamano l'insieme di credenziali delle chiavi. 

**Codici di errore:** questa metrica indica se nell'insieme di credenziali delle chiavi si verifica una quantità insolita di errori. Per un elenco completo dei codici di errore e indicazioni per la risoluzione dei problemi, vedere il documento seguente. [Azure Key Vault REST API Error Codes](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Come configurare le metriche e creare un dashboard

1. Accedere al portale di Azure
2. Passare all'insieme di credenziali delle chiavi
3. Selezionare **Le metriche** in **Monitoraggio** 

> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure](../media/alert-1.png)

4. Aggiornare il titolo del grafico in base a ciò che si desidera visualizzare nel dashboard. 
5. Selezionare l'ambito. In questo esempio verrà selezionato un singolo insieme di credenziali delle chiavi. 
6. Selezionare la media di disponibilità e **aggregazione** **del vault complessivo** della metrica 
7. Aggiornare l'intervallo di tempo per le ultime 24 ore e aggiornare la granularità del tempo a 1 minuto. 

> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure](../media/alert-2.png)

8. Ripetere i passaggi precedenti per le metriche Di saturazione del Vault e Latenza API servizio. Seleziona **Aggiungi al dashboard** per salvare le metriche in un dashboard. 

> [!IMPORTANT]
> Seleziona "Aggiungi al dashboard" e salva ogni metrica configurata. Se si esce dalla pagina e si torna ad essa senza salvare, le modifiche alla configurazione andranno perse. 

9. Per monitorare tutti i tipi di operazioni sull'insieme di credenziali delle chiavi, utilizzare **l'API Total Service Hits** Metric e selezionare Applica **divisione per tipo di attività**

> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure](../media/alert-3.png)

10. Per monitorare i codici di errore nell'insieme di credenziali delle chiavi, utilizzare la metrica **Risultati totali api del servizio** e selezionare Applica **divisione per tipo di attività**

> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure](../media/alert-4.png)

A questo punto si avrà un dashboard simile al seguente. È possibile fare clic sui 3 puntini in alto a destra di ogni riquadro e si può riorganizzare e ridimensionare i riquadri di cui hai bisogno. 

Dopo aver salvato e pubblicato il dashboard, verrà creata una nuova risorsa nella sottoscrizione di Azure.Once you save and publish the dashboard will create a new resource in your Azure subscription. Sarete in grado di vederlo in qualsiasi momento cercando "dashboard condiviso". 

> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Come configurare gli avvisi nell'insieme di credenziali delle chiaviHow to configure alerts on your Key Vault 

In questa sezione verrà illustrato come configurare gli avvisi nell'insieme di credenziali delle chiavi in modo da avvisare il team di intervenire immediatamente se l'insieme di credenziali delle chiavi si trova in uno stato non integro. È possibile configurare avvisi che inviano un messaggio di posta elettronica, preferibilmente a una DL del team, che inviano una notifica alla griglia degli eventi o chiamano o inviano un SMS a un numero di telefono. È inoltre possibile scegliere avvisi statici in base a un valore fisso o un avviso dinamico che avviserà l'utente se una metrica monitorata supera il limite medio dell'insieme di credenziali delle chiavi un determinato numero di volte in un intervallo di tempo definito. 

> [!IMPORTANT]
> Tieni presente che l'invio delle notifiche può richiedere fino a 10 minuti. 

### <a name="configure-an-action-group"></a>Configurare un gruppo di azioni 

Un gruppo di azioni è un elenco configurabile di notifiche e proprietà.

1. Accedere al portale di Azure
2. Cercare **avvisi** nella casella di ricerca
3. Selezionare **Gestisci azioni**

> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure](../media/alert-6.png)

4. Selezionare **: Aggiungi gruppo di azioni**

> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure](../media/alert-7.png)

5. Scegliere il **tipo di azione** per il gruppo di azioni. In questo esempio verrà creato un avviso di posta elettronica.

> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure](../media/alert-9.png)

6. Fare clic su **OK** nella parte inferiore della pagina. È stato creato correttamente un gruppo di azioni. 

Dopo aver configurato un gruppo di azioni, verranno configurate le soglie di avviso dell'insieme di credenziali delle chiavi. 

### <a name="configure-alert-thresholds"></a>Configurare le soglie di avviso 

1. Selezionare la risorsa dell'insieme di credenziali delle chiavi nel portale di Azure e selezionare Avvisi in **MonitoraggioSelect** your key vault resource in the Azure portal and select **Alerts** under Monitoring

> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure](../media/alert-10.png)

2. Seleziona **nuova regola di avviso**

> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure](../media/alert-11.png)

3. Selezionare l'ambito della regola di avviso. È possibile selezionare un singolo vault o più. 

> [!IMPORTANT]
> Si noti che quando si selezionano più vault per l'ambito degli avvisi, tutti i vault selezionati devono trovarsi nella stessa regione. Sarà necessario configurare regole di avviso separate per gli insiemi di credenziali in aree diverse. 

> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure](../media/alert-12.png)

4. Selezionare le condizioni per gli avvisi. È possibile scegliere uno dei seguenti segnali e definire la logica per gli avvisi. Il team dell'insieme di credenziali delle chiavi consiglia di configurare le soglie di avviso seguenti. 

    + La disponibilità dell'archivio chiavi scende al di sotto del 100% (soglia statica)
    + La latenza dell'insieme di credenziali delle chiavi è maggiore di 500 ms (soglia statica) 
    + La saturazione complessiva del Vault è superiore al 75% (soglia statica) 
    + La saturazione complessiva del Vault supera la media (soglia dinamica)
    + Codici di errore totali superiori alla media (soglia dinamica) 

> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Esempio 1: Configurazione di una soglia di avviso statica per la latenzaExample 1: Configuring a static alert threshold for latency

Selezionare Latenza API servizio globale come nome del segnaleSelect **Overall Service API Latency** as the signal name
> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure](../media/alert-14.png)

Vedere i seguenti parametri di configurazione.

+ Impostare la soglia su **Static** 
+ Impostare l'operatore su **Maggiore di**
+ Impostare il tipo di aggregazione su **Media**
+ Impostare il valore di soglia su **500**
+ Impostare il periodo di aggregazione su **5 minuti**
+ Impostare la frequenza di valutazione su **1 minuto**
+ Selezionare **Operazione completata**  

> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Esempio 2: Configurazione di una soglia di avviso dinamico per la saturazione del vaultExample 2: Configuring a dynamic alert threshold for vault saturation 

Quando si utilizza un avviso dinamico, sarà possibile visualizzare i dati cronologici dell'insieme di credenziali delle chiavi selezionato. L'area blu rappresenta l'utilizzo medio dell'insieme di credenziali delle chiavi. L'area rossa mostra i picchi che avrebbero attivato un avviso a condizione che vengano soddisfatti altri criteri nella configurazione degli avvisi. I punti rossi mostrano le istanze di violazioni in cui i criteri per l'avviso sono stati soddisfatti durante l'intervallo di tempo aggregato. È possibile impostare l'errore di un avviso dopo un determinato numero di violazioni entro un determinato periodo di tempo. Se non si desidera includere i dati passati, è disponibile un'opzione per escludere i vecchi dati nelle impostazioni avanzate. 

> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure](../media/alert-16.png)

Vedere i seguenti parametri di configurazione.

+ Impostare la soglia su **Dinamico** 
+ Impostare l'operatore su **Maggiore di**
+ Impostare il tipo di aggregazione su **Media**
+ Impostare la sensibilità di soglia su **Media**
+ Impostare il periodo di aggregazione su **5 minuti**
+ Impostare la frequenza di valutazione su **1 minuto**
+ **Facoltativo** Configurare le impostazioni avanzate 
+ Selezionare **Operazione completata**

> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure](../media/alert-17.png)

5. Aggiungere il gruppo di azioni configurato

> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure](../media/alert-18.png)

6. Abilitare l'avviso e assegnare un livello di gravitàEnable the alert and assign a severity

> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure](../media/alert-19.png)

7. Creare l'avvisoCreate the alert 


## <a name="next-steps"></a>Passaggi successivi

Congratulazioni, ora hai creato un dashboard di monitoraggio e configurato gli avvisi per il tuo insieme di credenziali delle chiavi! Dopo aver seguito tutti i passaggi precedenti, è necessario ricevere avvisi tramite posta elettronica quando l'insieme di credenziali delle chiavi soddisfa i criteri di avviso configurati. Di seguito è illustrato un esempio. Utilizzare gli strumenti impostati in questo articolo per monitorare attivamente l'integrità dell'insieme di credenziali delle chiavi. 

### <a name="example-email-alert"></a>Esempio di avviso e-mail 

> [!div class="mx-imgBorder"]
> ![Screenshot del portale di Azure](../media/alert-20.png)
