---
title: Configurare e monitorare i dispositivi IoT su larga scala con l'hub IoT di Azure | Microsoft Docs
description: Usare le configurazioni automatiche dei dispositivi dell'hub IoT di Azure per assegnare una configurazione a più dispositivi
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: fe5ce960663f39d4f2c87a7bbffa091d327e9559
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632449"
---
# <a name="configure-and-monitor-iot-devices-at-scale---preview"></a>Configurare e monitorare i dispositivi IoT su larga scala - anteprima

La gestione automatica dei dispositivi nell'hub IoT di Azure consente di automatizzare molte attività ripetitive e complesse di gestione di un numero elevato di dispositivi nel loro intero ciclo di vita. Con la gestione automatica dei dispositivi è possibile assegnare come destinazione un insieme di dispositivi in base alle proprietà, definire la configurazione desiderata e consentire all'hub IoT di aggiornare i dispositivi ogni volta che entrano nell'ambito.  Questo risultato viene ottenuto usando la configurazione automatica dei dispositivi, che consente anche di fornire un riepilogo del completamento e della conformità, di gestire l'unione e i conflitti e di distribuire le configurazioni in fasi.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Le configurazioni automatiche dei dispositivi funzionano aggiornando un insieme di dispositivi gemelli con le proprietà desiderate e segnalando un riepilogo in base alle proprietà segnalate dei dispositivi gemelli.  Introduce una nuova classe e un documento JSON denominato _Configuration_ che presenta tre parti:

* La **condizione di destinazione** definisce l'ambito dei dispositivi gemelli da aggiornare. La condizione di destinazione viene specificata come query sui tag e/o le proprietà segnalate dei dispositivi gemelli.

* Il **contenuto di destinazione** definisce le proprietà desiderate da aggiungere o aggiornare nei dispositivi gemelli di destinazione. Il contenuto include un percorso della sezione delle proprietà desiderate da modificare.

* Le **metriche** definiscono i conteggi di riepilogo dei vari stati di configurazione, ad esempio **Success**, **In progress** ed **Error**. Le metriche personalizzate vengono specificate come query sulle proprietà segnalate dei dispositivi gemelli.  Le metriche di sistema sono metriche predefinite che misurano lo stato di aggiornamento dei dispositivi gemelli, ad esempio il numero di dispositivi gemelli di destinazione e di quelli che sono stati aggiornati. 

> [!Note]
> Durante l'anteprima, questa funzionalità non è disponibile per gli hub IoT nelle aree Stati Uniti orientali, Stati Uniti occidentali, Europa settentrionale ed Europa occidentale.

## <a name="implement-device-twins-to-configure-devices"></a>Implementare i dispositivi gemelli per configurare i dispositivi

Le configurazioni automatiche dei dispositivi richiedono l'uso di dispositivi gemelli per la sincronizzazione dello stato tra il cloud e i dispositivi.  Per indicazioni sull'uso di dispositivi gemelli, vedere [Comprendere e usare dispositivi gemelli nell'hub IoT][lnk-device-twin].

## <a name="identify-devices-using-tags"></a>Identificare i dispositivi tramite tag

Prima di poter creare una configurazione, è necessario specificare i dispositivi a cui si vuole applicare. L'hub IoT di Azure identifica i dispositivi usando i tag nel dispositivo gemello. Ogni dispositivo può avere più tag ed è possibile definirli in qualsiasi modo risulti appropriato per una soluzione specifica. Ad esempio, se si gestiscono i dispositivi in posizioni diverse, è possibile aggiungere i tag seguenti a un dispositivo gemello:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="create-a-configuration"></a>Creare una configurazione

1. Nel [portale di Azure][lnk-portal] passare all'hub IoT. 
1. Selezionare **IoT device configuration (preview)** (Configurazione del dispositivo IoT - anteprima).
1. Selezionare **Add Configuration** (Aggiungi configurazione).

La creazione di una configurazione prevede cinque passaggi, illustrati nelle sezioni seguenti. 

### <a name="step-1-name-and-label"></a>Passaggio 1: Nome ed etichetta

1. Assegnare alla configurazione un nome univoco con un massimo di 128 lettere in minuscolo. Evitare gli spazi e i seguenti caratteri non validi: `& ^ [ ] { } \ | " < > /`.
1. Aggiungere etichette per tenere traccia delle configurazioni. Le etichette sono coppie di **Nome** e **Valore** che descrivono la configurazione. Ad esempio, `HostPlatform, Linux` o `Version, 3.0.1`.
1. Selezionare **Avanti** per procedere al passaggio due. 

### <a name="step-2-specify-settings"></a>Passaggio 2: Specificare le impostazioni

Questa sezione specifica il contenuto di destinazione da impostare nei dispositivi gemelli di destinazione. Esistono due input per ogni set di impostazioni. Il primo è il percorso del dispositivo gemello, ovvero il percorso della sezione JSON all'interno delle proprietà desiderate del dispositivo gemello che verranno impostate.  Il secondo è il contenuto JSON da inserire in questa sezione. Ad esempio, impostare il percorso e il contenuto dei dispositivi gemelli nel modo seguente:

![Impostare il percorso e il contenuto dei dispositivi gemelli](./media/iot-hub-auto-device-config/create-configuration-full-browser.png)

È possibile anche definire singole impostazioni specificando il percorso completo in Device Twin Path (Percorso dispositivo gemello) e il valore in Content (Contenuto) senza parentesi quadre. Ad esempio, impostare il percorso del dispositivo gemello su `properties.desired.chiller-water.temperature` e il contenuto su: `66`

Se due o più configurazioni assegnano come destinazione lo stesso percorso del dispositivo gemello, verrà applicato il contenuto dalla configurazione con priorità più alta (la priorità viene definita nel passaggio 4).

Se si vuole rimuovere una proprietà, specificare il valore della proprietà su `null`.

È possibile aggiungere altre impostazioni selezionando **Add Device Twin Setting** (Aggiungi impostazione dispositivo gemello)

### <a name="step-3-specify-metrics-optional"></a>Passaggio 3: Specificare le metriche (facoltativo)

Le metriche forniscono i conteggi di riepilogo dei diversi stati che un dispositivo può segnalare dopo l'applicazione del contenuto della configurazione. Ad esempio è possibile creare una metrica per le modifiche alle impostazioni in sospeso, una metrica per gli errori e una metrica per le modifiche alle impostazioni con esito positivo.

1. Immettere un nome per **Metric Name** (Nome metrica)
1. Immettere una query per **Metric Criteria** (Criteri metrica).  La query è basata sulle proprietà segnalate dei dispositivi gemelli.  La metrica rappresenta il numero di righe restituite dalla query.

Ad esempio: `SELECT deviceId FROM devices WHERE properties.reported.chillerWaterSettings.status='pending'`

È possibile includere una clausola per indicare che la configurazione è stata applicata, ad esempio: `SELECT deviceId FROM devices WHERE configurations.[[yourconfigname]].status='Applied'` incluse le doppie parentesi.


### <a name="step-4-target-devices"></a>Passaggio 4: Assegnare i dispositivi di destinazione

Usare la proprietà tags dai dispositivi gemelli per selezionare i dispositivi specifici che devono ricevere la configurazione.  È possibile anche assegnare i dispositivi di destinazione in base alle proprietà segnalate dei dispositivi gemelli.

Dato che più configurazioni possono avere come destinazione lo stesso dispositivo, è necessario assegnare a ogni configurazione un numero di priorità. In caso di conflitto, prevale la configurazione con la priorità più alta. 

1. Immettere un numero intero positivo in **Priority** (Priorità) per la configurazione. Il valore numerico più alto indica la priorità più alta. Se due configurazioni hanno lo stesso numero di priorità, prevale quella creata più di recente. 
1. Specificare una condizione in **Target condition** (Condizione di destinazione) per determinare i dispositivi di destinazione di questa configurazione. La condizione è basata sui tag o sulle proprietà segnalate dei dispositivi gemelli e deve corrispondere al formato di espressione. Ad esempio, `tags.environment='test'` o `properties.reported.chillerProperties.model='4000x'`. 
1. Selezionare **Avanti** per procedere al passaggio finale.

### <a name="step-5-review-configuration"></a>Passaggio 5: Rivedere la configurazione

Rivedere le informazioni sulla configurazione e quindi selezionare **Submit** (Invia).

## <a name="monitor-a-configuration"></a>Monitorare una configurazione

Per visualizzare i dettagli di una configurazione e monitorare i dispositivi che la eseguono, seguire questa procedura:

1. Nel [portale di Azure][lnk-portal] passare all'hub IoT. 
1. Selezionare **IoT device configuration (preview)** (Configurazione del dispositivo IoT - anteprima).
1. Esaminare l'elenco delle configurazioni. Per ogni configurazione è possibile visualizzare i dettagli seguenti:
   * **ID**: il nome della configurazione.
   * **Target condition** (Condizione di destinazione): la query usata per definire i dispositivi di destinazione.
   * **Priority** (Priorità): il numero di priorità assegnato alla configurazione.
   * **Creation time** (Data/ora di creazione): il timestamp di creazione della configurazione. Questo timestamp viene usato per stabilire la prevalenza quando due configurazioni hanno la stessa priorità. 
   * **System metrics** (Metriche sistema): le metriche che vengono calcolate dall'hub IoT e che non possono essere personalizzate dagli sviluppatori. Assegnato: specifica il numero di dispositivi gemelli che soddisfano la condizione di destinazione. Si applica: il numero di dispositivi gemelli che sono stati modificati dalla configurazione, che può includere modifiche parziali nel caso in cui anche una configurazione separata con priorità più alta abbia apportato modifiche. 
   * **Metriche personalizzate**: metriche specificate dallo sviluppatore come query sulle proprietà segnalate dei dispositivi gemelli.  È possibile definire fino a cinque metriche personalizzate per ogni configurazione. 
   
1. Selezionare la configurazione che si vuole monitorare.  
1. Esaminare i dettagli della configurazione. È possibile usare le schede per visualizzare dettagli specifici sui dispositivi che hanno ricevuto la configurazione: 
   * **Condizione di destinazione**: dispositivi che soddisfano la condizione di destinazione. 
   * **Metriche**: elenco delle metriche di sistema e personalizzate.  È possibile visualizzare un elenco di dispositivi che vengono conteggiati per ogni metrica selezionando la metrica nell'elenco a discesa e quindi selezionando **View Devices** (Visualizza dispositivi).
   * **Device Twin Settings** (Impostazioni dispositivi gemelli): le impostazioni dei dispositivi gemelli specificate dalla configurazione. 
   * **Configuration Labels** (Etichette configurazione): coppie chiave-valore usate per descrivere una configurazione.  Le etichette non impattano sulla funzionalità. 

## <a name="modify-a-configuration"></a>Modificare una configurazione

Quando si modifica una configurazione, le modifiche vengono replicate immediatamente in tutti i dispositivi di destinazione. 

Se si aggiorna la condizione di destinazione, vengono eseguiti gli aggiornamenti seguenti:
* Se un dispositivo gemello non soddisfa la condizione di destinazione precedente, ma soddisfa la nuova condizione di destinazione e questa configurazione ha la priorità più alta per il dispositivo gemello, questa configurazione viene applicata al dispositivo. 
* Se un dispositivo gemello non soddisfa più la condizione di destinazione, verranno rimosse le impostazioni dalla configurazione e il dispositivo gemello verrà modificato dalla configurazione successiva con la priorità più alta. 
* Se un dispositivo gemello che attualmente esegue questa configurazione non soddisfa più la condizione di destinazione e non soddisfa la condizione di destinazione delle altre configurazioni, le impostazioni vengono rimosse dalla configurazione e non verranno apportate altre modifiche al dispositivo gemello. 

Per modificare una configurazione, seguire questa procedura: 

1. Nel [portale di Azure][lnk-portal] passare all'hub IoT. 
1. Selezionare **IoT device configuration (preview)** (Configurazione del dispositivo IoT - anteprima). 
1. Selezionare la configurazione che si vuole modificare. 
1. Apportare modifiche nei campi seguenti: 
   * Condizione di destinazione 
   * Etichette 
   * Priorità 
   * Metriche
1. Selezionare **Salva**.
1. Seguire i passaggi in [Monitorare una configurazione][anchor-monitor] per controllare la distribuzione delle modifiche. 

## <a name="delete-a-configuration"></a>Eliminare una configurazione

Quando si elimina una configurazione, gli eventuali dispositivi gemelli ricevono la configurazione successiva con la priorità più alta. Se i dispositivi gemelli non soddisfano la condizione di destinazione di qualsiasi altra configurazione, non vengono applicate altre impostazioni. 

1. Nel [portale di Azure][lnk-portal] passare all'hub IoT. 
1. Selezionare **IoT device configuration (preview)** (Configurazione del dispositivo IoT - anteprima). 
1. Usare la casella di controllo per selezionare la configurazione che si vuole eliminare. 
1. Selezionare **Elimina**.
1. Verrà visualizzato un messaggio che richiede la conferma.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato appreso come configurare e monitorare i dispositivi IoT su larga scala. Per ulteriori informazioni sulla gestione dell'hub IoT di Azure, consultare questi collegamenti:

* [Gestire in blocco le identità dei dispositivi dell'hub IoT][lnk-bulkIDs]
* [Metriche di Hub IoT][lnk-metrics]
* [Monitoraggio delle operazioni][lnk-monitor]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per gli sviluppatori dell'hub IoT][lnk-devguide]
* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge][lnk-iotedge]

Per analizzare l'uso del servizio Device Provisioning dell'hub IoT per abilitare il provisioning automatico senza intervento umano, vedere: 

* [Servizio Device Provisioning in hub IoT di Azure][lnk-dps]

[lnk-device-twin]: iot-hub-devguide-device-twins.md
[lnk-bulkIDs]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
[lnk-portal]: https://portal.azure.com
