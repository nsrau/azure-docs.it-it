---
title: Usare il portale di Azure per creare un hub IoT | Documentazione Microsoft
description: Come creare, gestire ed eliminare hub IoT di Azure con il portale di Azure. Include informazioni su piani tariffari, ridimensionamento, sicurezza e configurazione della messaggistica.
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0909cd2b-4c1e-49e0-b68a-75532caf0a6a
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: d4eb942db51af9c8136e9e0f5f8683cc15679d08
ms.openlocfilehash: 5a0cd9ac88f30bb5f1ccba43260b86392dba6d7b


---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Creare un hub IoT usando il portale di Azure
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduzione
In questo articolo viene descritto come trovare il servizio hub IoT nel portale di Azure e come creare e gestire hub IoT.

## <a name="where-to-find-iot-hubs"></a>Dove trovare hub IoT
Gli hub IoT possono essere situati in diverse posizioni.

1. **+ Nuovo**: **hub IoT di Azure** è un servizio IoT e si trova nella categoria **Internet delle cose** in **+ Nuovo**, come altri servizi.
2. È possibile accedere agli hub IoT anche tramite il Marketplace come servizio in **Internet delle cose**.

## <a name="create-an-iot-hub"></a>Creare un hub IoT
È possibile creare un hub IoT usando i metodi seguenti:

* La creazione di un hub IoT con l'opzione **+ Nuovo** consente di accedere al pannello visualizzato nell'immagine seguente. I passaggi per la creazione di un hub IoT tramite questo metodo e tramite il Marketplace sono identici.
* Creazione di un hub IoT tramite il Marketplace: se si fa clic su **Crea** viene aperto un pannello identico a quello precedente per l'esperienza **+Nuovo**. Le sezioni seguenti elencano i numerosi passaggi previsti per la creazione di un hub IoT.

### <a name="choose-the-name-of-the-iot-hub"></a>Scegliere il nome dell'hub IoT
Per creare un hub IoT, è necessario assegnare un nome all'hub IoT. Questo nome deve essere univoco in tutti gli hub IoT. Non è consentita la duplicazione degli hub nel back-end della soluzione, quindi è consigliabile assegnare all'hub un nome quanto più possibile univoco.

### <a name="choose-the-pricing-tier"></a>Scegliere il piano tariffario
È possibile scegliere fra quattro piani: **Gratuito**, **Standard 1**, **Standard 2** e **Standard S3**. Il piano gratuito consente la connessione di solo 500 dispositivi all'hub IoT e di un massimo di 8.000 messaggi al giorno.

**Standard S1**: l'edizione Hub IoT S1 è progettata per le soluzioni IoT con un numero elevato di dispositivi che generano quantità relativamente limitate di dati per dispositivo. Ogni unità dell'edizione S1 consente fino a 400.000 messaggi al giorno tra tutti i dispositivi.

**Standard S2**: l'edizione Hub IoT S2 è progettata per le soluzioni IoT in cui i dispositivi generano grandi quantità di dati. Ogni unità dell'edizione S2 consente fino a 6 milioni di messaggi al giorno tra tutti i dispositivi connessi.

**Standard S3**: l'edizione Hub IoT S3 è progettata per soluzioni IoT che generano grandi quantità di dati. Ogni unità dell'edizione S3 consente fino a 300 milioni di messaggi al giorno tra tutti i dispositivi connessi.

![][4]

> [!NOTE]
> L'hub IoT consente solo un hub gratuito per sottoscrizione di Azure.
> 
> 

### <a name="iot-hub-units"></a>Unità hub IoT
Il numero di messaggi consentiti per unità al giorno dipende dal piano tariffario dell'hub. Ad esempio, se si desidera che l'hub IoT supporti 700.000 messaggi in entrata, selezionare due unità del piano S1.

### <a name="device-to-cloud-partitions-and-resource-group"></a>Partizioni da dispositivo a cloud e gruppo di risorse
È possibile modificare il numero di partizioni per un hub IoT. Le partizioni predefinite sono impostate su 4, ma è possibile scegliere un altro numero di partizioni da un elenco a discesa.

Per i gruppi di risorse non è necessario creare in modo esplicito un gruppo di risorse vuoto. Quando si crea una risorsa, è possibile scegliere di creare un nuovo gruppo di risorse o usarne uno esistente.

![][5]

### <a name="choose-subscriptions"></a>Scegliere le sottoscrizioni
L'hub IoT di Azure mostra automaticamente l'elenco di sottoscrizioni Azure alle quali sono collegati gli account utente. È possibile scegliere una delle opzioni disponibili per associare l'hub IoT a una sottoscrizione Azure.

### <a name="choose-the-location"></a>Scegliere la località
L'opzione relativa alla posizione offre un elenco delle aree in cui viene offerto l'hub IoT. Hub IoT è disponibile per la distribuzione nelle località seguenti Australia orientale, Australia sud-orientale, Asia orientale, Asia sudorientale, Europa settentrionale, Europa occidentale, Giappone orientale, Giappone occidentale, Stati Uniti orientali, Stati Uniti occidentali.

### <a name="create-the-iot-hub"></a>Creare l'hub IoT
Dopo aver completato tutti i passaggi precedenti, l'hub IoT è pronto per essere creato. Fare clic su **Crea** per avviare il processo di back-end per la creazione di questo hub IoT con le opzioni specifiche e distribuirlo nella posizione specificata.

Possono essere necessari alcuni minuti per la creazione dell'hub IoT, perché l'esecuzione della distribuzione del back-end nei server delle località appropriate richiede tempo.

## <a name="change-the-settings-of-the-iot-hub"></a>Modificare le impostazioni dell'hub IoT
È possibile modificare le impostazioni di un hub IoT esistente dopo averlo creato dal pannello Hub IoT.

![][8]

**Criteri di accesso condiviso**: questi criteri definiscono le autorizzazioni per la connessione di dispositivi e servizi all'hub IoT. È possibile accedere a questi criteri facendo clic su **Criteri di accesso condiviso** in **Generale**. In questo pannello è possibile modificare i criteri esistenti o aggiungerne di nuovi.

### <a name="create-a-policy"></a>Creare un criterio
* Fare clic su **Aggiungi** per aprire un pannello. Qui è possibile immettere il nome dei nuovi criteri e le autorizzazioni da associare a questi criteri, come illustrato nella figura seguente:
  
    Sono disponibili numerose autorizzazioni che possono essere associate a questi criteri condivisi. I primi due criteri di **lettura del registro di configurazione** e di **scrittura del registro di configurazione** consentono di concedere diritti di accesso in lettura e scrittura per l'archivio identità del dispositivo o per il registro delle identità. Scegliendo l'opzione di scrittura, verrà scelta automaticamente anche l'opzione di lettura.
  
     I criteri di **Connessione servizio** concedono le autorizzazioni per accedere agli endpoint lato cloud, ad esempio il gruppo di consumatori dei servizi collegati all'hub IoT. I criteri di **Connessione dispositivo** concedono le autorizzazioni per l'invio e la ricezione di messaggi sugli endpoint lato dispositivo dell'hub IoT.
* Fare clic su **Crea** per aggiungere i criteri appena creati all'elenco esistente.

![][10]

## <a name="endpoints"></a>Endpoint
Fare clic su **Endpoint** per visualizzare un elenco di endpoint per l'hub IoT che si sta modificando. Esistono due principali tipi di endpoint: endpoint predefinti nell'hub IoT ed endpoint aggiunti all'hub IoT in seguito alla sua creazione.

### <a name="built-in-endpoints"></a>Endpoint predefiniti
Esistono due principali tipi di endpoint predefiniti: **Cloud to device feedback** (Commenti da cloud a dispositivi) ed **Eventi**.

* Impostazioni **Cloud to device feedback** (Commenti da cloud a dispositiv): questa impostazione include due impostazioni secondarie **Cloud to Device TTL** (Durata (TTL) da cloud a dispositivo) e **Tempo di conservazione** per i messaggi. Quando si crea l'hub IoT inizialmente, entrambe queste impostazioni vengono create con un valore predefinito di un'ora. Per modificare questi valori usare i dispositivi di scorrimento o digitare i valori.
* **Eventi**: questa impostazione include diverse impostazioni secondarie, alcune delle quali sono denominate/assegnate quando si crea l'hub IoT e possono essere copiate solo in altre impostazioni secondarie personalizzabili. Queste impostazioni sono elencate nella sezione successiva.

**Partizioni**: questo valore viene impostato quando si crea l'hub IoT e può essere modificato tramite questa impostazione.

**Event Hub-compatible name and endpoint** (Nomi ed endpoint compatibili con Hub eventi): quando viene creato l'hub IoT, viene creato internamente un Hub eventi a cui potrebbe essere necessario accedere in determinate circostanze. Il nome e l'endpoint compatibili con Hub eventi non possono essere personalizzati, ma sono disponibili per l'uso tramite il pulsante **Copia**.

**Tempo di conservazione**: per impostazione predefinita è impostato su un giorno, ma può essere personalizzato con altri valori tramite l'elenco a discesa. Questo valore è espresso in giorni per Device to Cloud (Da dispositivo a cloud) e non in ore, come lo è l'impostazione simile per Cloud to device (Da cloud a dispositivo).

**Gruppi di consumer**: è un'impostazione simile ad altri sistemi di messaggistica che possono essere usati per eseguire il pull dei dati in modo specifico per connettere altre applicazioni o servizi all'hub IoT. Ogni hub IoT viene creato con un gruppo di consumer predefinito. Tuttavia, è possibile aggiungere o eliminare gruppi di consumer negli hub IoT.

> [!NOTE]
> Il gruppo di consumer predefinito non può essere modificato o eliminato.
> 
> 

![][11]

### <a name="custom-endpoints"></a>Endpoint personalizzati
È possibile aggiungere endpoint personalizzati all'hub IoT tramite il portale. Nel pannello degli endpoint, fare clic su **Aggiungi** nella parte superiore del pannello per aprire il pannello **Aggiungi endpoint**. Immettere le informazioni necessarie nel pannello, quindi fare clic su **OK**. L'endpoint personalizzato viene quindi visualizzato nel pannello principale degli endpoint.

![][13]

Altre informazioni sugli endpoint personalizzati sono disponibili in [Reference - IoT hub endpoints][lnk-devguide-endpoints] (Riferimenti: endpoint di hub IoT).

## <a name="routes"></a>Route
Fare clic su **Route** per gestire la modalità di invio dei messaggi da dispositivo a cloud dell'hub IoT.

![][14]

È possibile aggiungere altri route all'hub IoT facendo clic su **Aggiungi** nella parte superiore del pannello, inserendo le informazioni necessarie nel pannello e facendo clic su **OK**. Il route viene quindi visualizzato nel pannello principale degli endpoint. È possibile modificare un route selezionandolo nell'elenco di route e quindi modificandolo. Per abilitare un route, selezionarlo nell'elenco di route e impostare l'interruttore abilita/disabilita su **Off**. Fare clic su **OK** nella parte inferiore del pannello per salvare le modifiche.

![][15]

## <a name="pricing-and-scale"></a>Prezzi e scalabilità
I prezzi di un hub IoT esistente possono essere modificati tramite le impostazioni disponibili in **Prezzi** , con le eccezioni seguenti:

* Nell'implementazione corrente un hub IoT con uno SKU gratuito non può cambiare i piani con quelli di uno degli SKU a pagamento o viceversa.
* Nella sottoscrizione Azure può essere presente un solo livello gratuito per l'hub IoT.

![][12]

Il passaggio da un piano più elevato (S2 o S3) a un piano inferiore (S1 o S2) è consentito solo quando il numero di messaggi inviati per un dato giorno non sono in conflitto. Ad esempio, se il numero di messaggi al giorno supera 400.000, il livello per l'hub IoT può essere cambiato. Tuttavia, se si modifica il piano S1, l'hub IoT è limitato per il giorno in questione.

## <a name="delete-the-iot-hub"></a>Eliminare l'hub IoT
È possibile passare all'hub IoT che si vuole eliminare facendo clic su **Sfoglia**e quindi scegliendo l'hub appropriato da eliminare. Fare clic sul pulsante **Elimina** sotto il nome dell'hub IoT per eliminarlo.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla gestione dell'hub IoT di Azure, consultare questi collegamenti:

* [Gestire in blocco i dispositivi IoT][lnk-bulk]
* [Metriche di Hub IoT][lnk-metrics]
* [Monitoraggio delle operazioni][lnk-monitor]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per gli sviluppatori dell'hub IoT][lnk-devguide]
* [Simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]
* [Proteggere la soluzione IoT sin dall'inizio][lnk-securing]

[4]: ./media/iot-hub-create-through-portal/create-iothub.png
[5]: ./media/iot-hub-create-through-portal/location1.png
[8]: ./media/iot-hub-create-through-portal/portal-settings.png
[10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
[11]: ./media/iot-hub-create-through-portal/messaging-settings.png
[12]: ./media/iot-hub-create-through-portal/pricing-error.png
[13]: ./media/iot-hub-create-through-portal/endpoint-creation.png
[14]: ./media/iot-hub-create-through-portal/routes-list.png
[15]: ./media/iot-hub-create-through-portal/route-edit.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md



<!--HONumber=Jan17_HO1-->


