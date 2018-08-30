---
title: Usare il portale di Azure per creare un hub IoT | Microsoft Docs
description: Come creare, gestire ed eliminare hub IoT di Azure con il portale di Azure. Include informazioni su piani tariffari, ridimensionamento, sicurezza e configurazione della messaggistica.
author: dominicbetts
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: dobett
ms.openlocfilehash: 0b03ae434e93dbab45235fe67c499497e1257064
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2018
ms.locfileid: "42141799"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Creare un hub IoT usando il portale di Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

L'articolo illustra:

* Come trovare il servizio hub IoT nel portale di Azure.
* Come creare e gestire hub IoT.

## <a name="where-to-find-the-iot-hub-service"></a>Dove trovare il servizio hub IoT

È possibile trovare il servizio hub IoT nelle posizioni seguenti nel portale:

* Scegliere **+ Nuovo** e quindi scegliere **Internet delle cose**.
* Nel marketplace scegliere **Internet delle cose**.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

È possibile creare un hub IoT usando i metodi seguenti:

* L'opzione **+ Nuovo** apre il pannello illustrato nello screenshot seguente. I passaggi per la creazione di un hub IoT tramite questo metodo e tramite il Marketplace sono identici.

* Nel marketplace scegliere **Crea** per aprire il pannello visualizzato nello screenshot seguente.

Le sezioni seguenti descrivono i diversi passaggi per creare un hub IoT.

### <a name="choose-the-name-of-the-iot-hub"></a>Scegliere il nome dell'hub IoT

Per creare un hub IoT, è necessario assegnare un nome all'hub IoT. Questo nome deve essere univoco in tutti gli hub IoT.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

### <a name="choose-the-pricing-tier"></a>Scegliere il piano tariffario

È possibile scegliere tra livelli diversi a seconda del numero di funzionalità che si desidera e di quanti messaggi si inviano al giorno tramite la soluzione. Il livello gratuito è utilizzabile a scopo di test e valutazione. Consente la connessione di 500 dispositivi all'hub IoT e un massimo di 8.000 messaggi al giorno. Ogni sottoscrizione Azure può creare hub IoT nel livello gratuito. 

Per informazioni dettagliate sulle altre opzioni relative al livello, vedere [Scegliere il livello più adatto di hub IoT](iot-hub-scaling.md).

### <a name="iot-hub-units"></a>Unità hub IoT

Il numero di messaggi consentiti per unità al giorno dipende dal piano tariffario dell'hub. Ad esempio, se si desidera che l'hub IoT supporti 700.000 messaggi in entrata, selezionare due unità del piano S1.

### <a name="device-to-cloud-partitions-and-resource-group"></a>Partizioni da dispositivo a cloud e gruppo di risorse

È possibile modificare il numero di partizioni per un hub IoT. Il numero predefinito di partizioni è 4 ed è possibile scegliere un altro numero nell'elenco a discesa.

Non è necessario creare in modo esplicito un gruppo di risorse vuoto. Quando si crea una risorsa, è possibile scegliere di creare un nuovo gruppo di risorse o usarne uno esistente.

![Screenshot che illustra come creare un hub nel portale di Azure](./media/iot-hub-create-through-portal/location1.png)

### <a name="choose-subscription"></a>Scegliere la sottoscrizione

L'hub IoT di Azure mostra automaticamente l'elenco di sottoscrizioni di Azure alle quali è collegato l'account utente. È possibile scegliere la sottoscrizione di Azure a cui associare l'hub IoT.

### <a name="choose-the-location"></a>Scegliere la località

L'opzione relativa alla posizione offre un elenco delle aree in cui è disponibile l'hub IoT.

### <a name="create-the-iot-hub"></a>Creare l'hub IoT

Dopo aver completato tutti i passaggi precedenti, è possibile creare l'hub IoT. Fare clic su **Crea** per avviare il processo di back-end per creare e distribuire l'hub IoT con le opzioni scelte.

Possono essere necessari alcuni minuti per creare l'hub IoT, perché l'esecuzione della distribuzione del back-end nei server delle località appropriate richiede tempo.

## <a name="change-the-settings-of-the-iot-hub"></a>Modificare le impostazioni dell'hub IoT
<!--robinsh these screenshots are out of date -->

È possibile modificare le impostazioni di un hub IoT esistente dopo averlo creato dal pannello Hub IoT.

![Screenshot che mostra le impostazioni per l'hub IoT](./media/iot-hub-create-through-portal/portal-settings.png)

**Criteri di accesso condiviso**: questi criteri definiscono le autorizzazioni per la connessione di dispositivi e servizi all'hub IoT. È possibile accedere a questi criteri facendo clic su **Criteri di accesso condiviso** in **Generale**. In questo pannello è possibile modificare i criteri esistenti o aggiungerne di nuovi.

### <a name="create-a-policy"></a>Creare un criterio

* Fare clic su **Aggiungi** per aprire un pannello. Qui è possibile immettere il nome dei nuovi criteri e le autorizzazioni da associare a questi criteri, come illustrato nella figura seguente:

    Sono disponibili numerose autorizzazioni che possono essere associate a questi criteri condivisi. I criteri **Lettura registro** e **Scrittura registro** consentono di concedere diritti di accesso in lettura e scrittura per il registro delle identità. La scelta dell'opzione di scrittura include automaticamente l'opzione di lettura.

    I criteri **Connessione servizio** concedono le autorizzazioni per accedere agli endpoint del servizio, ad esempio per la **ricezione di messaggi da dispositivo a cloud**. I criteri **Connessione dispositivo** concedono le autorizzazioni per l'invio e la ricezione di messaggi tramite gli endpoint sul lato dispositivo dell'hub IoT.

* Fare clic su **Crea** per aggiungere i criteri appena creati all'elenco esistente.

   ![Screenshot che illustra l'aggiunta di un criterio di accesso condiviso](./media/iot-hub-create-through-portal/shared-access-policies.png)

## <a name="endpoints"></a>Endpoint

Fare clic su **Endpoint** per visualizzare un elenco di endpoint per l'hub IoT che si sta modificando. Esistono due principali tipi di endpoint: endpoint predefinti nell'hub IoT ed endpoint aggiunti all'hub IoT in seguito alla sua creazione.

![Screenshot che illustra l'aggiunta di un endpoint](./media/iot-hub-create-through-portal/messaging-settings.png)

### <a name="built-in-endpoints"></a>Endpoint predefiniti

Esistono due tipi di endpoint predefiniti: **Cloud to device feedback** (Commenti da cloud a dispositivi) ed **Eventi**.

* Impostazioni **Cloud to device feedback** (Commenti da cloud a dispositiv): questa impostazione include due impostazioni secondarie **Cloud to Device TTL** (Durata (TTL) da cloud a dispositivo) e **Tempo di conservazione** (in ore) per i messaggi. Quando si crea per la prima volta un hub IoT, entrambe queste impostazioni hanno il valore predefinito di un'ora. Per modificare queste impostazioni usare i dispositivi di scorrimento o digitare i valori.

* Impostazioni **Eventi**: questa impostazione presenta diverse impostazioni secondarie, alcune delle quali sono di sola lettura. L'elenco seguente descrive le singole impostazioni:

  * **Partizioni**: viene impostato un valore predefinito quando viene creato l'hub IoT. Grazie a questa impostazione è possibile modificare il numero di partizioni.

  * **Event Hub-compatible name and endpoint** (Nomi ed endpoint compatibili con Hub eventi): quando viene creato l'hub IoT, viene creato internamente un Hub eventi a cui potrebbe essere necessario accedere in determinate circostanze. Non è possibile personalizzare i valori del nome e dell'endpoint compatibile con l'hub eventi, ma è possibile copiarli facendo clic su **Copia**.

  * **Tempo di conservazione**: per impostazione predefinita è impostato su un giorno ma è possibile modificarlo tramite l'elenco a discesa. Questo valore è espresso in giorni per l'impostazione da dispositivo a cloud.

  * **Gruppi di consumer**: i gruppi di consumer consentono a più lettori di leggere i messaggi in modo indipendente dall'hub IoT. Ogni hub IoT viene creato con un gruppo di consumer predefinito. Tuttavia, con questa impostazione è possibile aggiungere o eliminare gruppi di consumer negli hub IoT.

  > [!NOTE]
  > Il gruppo di consumer predefinito non può essere modificato o eliminato.

### <a name="custom-endpoints"></a>Endpoint personalizzati

È possibile aggiungere endpoint personalizzati all'hub IoT tramite il portale. Nel pannello **Endpoint**, fare clic su **Aggiungi** nella parte superiore del pannello per aprire il pannello **Aggiungi endpoint**. Immettere le informazioni necessarie, quindi fare clic su **OK**. L'endpoint personalizzato viene ora elencato nel pannello principale **Endpoint**.

![Screenshot che illustra l'aggiunta di un endpoint personalizzato](./media/iot-hub-create-through-portal/endpoint-creation.png)

Altre informazioni sugli endpoint personalizzati sono disponibili in [Reference - IoT hub endpoints]( iot-hub-devguide-endpoints.md) (Riferimenti: endpoint di hub IoT).

## <a name="routes"></a>Route

Fare clic su **Route** per gestire la modalità di invio dei messaggi da dispositivo a cloud dell'hub IoT.

![Screenshot che illustra l'aggiunta di una nuova route](./media/iot-hub-create-through-portal/routes-list.png)

È possibile aggiungere i route all'hub IoT facendo clic su **Aggiungi** nella parte superiore del pannello **Route*** inserendo le informazioni necessarie e facendo clic su **OK**. Il route viene quindi elencato nel pannello principale **Route**. È possibile modificare un route selezionandolo nell'elenco di route. Per abilitare un route, selezionarlo nell'elenco di route e impostare l'interruttore **Enabled** su **Off**. Per salvare le modifiche, fare clic su **OK** nella parte inferiore del pannello.

![Screenshot che illustra la modifica di una nuova regola di routing](./media/iot-hub-create-through-portal/route-edit.png)

## <a name="delete-the-iot-hub"></a>Eliminare l'hub IoT

È possibile passare all'hub IoT che si vuole eliminare facendo clic su **Sfoglia** e quindi scegliendo l'hub appropriato da eliminare. Fare clic sul pulsante **Elimina** sotto il nome dell'hub IoT per eliminarlo.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla gestione dell'hub IoT di Azure, consultare questi collegamenti:

* [Gestire in blocco i dispositivi IoT](iot-hub-bulk-identity-mgmt.md)
* [Metriche di hub IoT](iot-hub-metrics.md)
* [Monitoraggio delle operazioni](iot-hub-operations-monitoring.md)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md)
* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Proteggere la soluzione IoT sin dall'inizio](../iot-fundamentals/iot-security-ground-up.md)
