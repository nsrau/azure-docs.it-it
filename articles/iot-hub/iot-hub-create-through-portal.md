---
title: Usare il portale di Azure per creare un hub IoT | Microsoft Docs
description: Come creare, gestire ed eliminare hub IoT di Azure con il portale di Azure. Include informazioni su piani tariffari, ridimensionamento, sicurezza e configurazione della messaggistica.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.openlocfilehash: 21758d112cd7eaf45b8b279cf2a6f6060c44c9c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742036"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Creare un hub IoT usando il portale di Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Questo articolo descrive come creare e gestire hub IoT con il [portale di Azure](https://portal.azure.com).

Per eseguire i passaggi in questa esercitazione, è necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>Modificare le impostazioni dell'hub IoT

È possibile modificare le impostazioni di un hub IoT esistente dopo averlo creato dal riquadro Hub IoT.

![Screenshot che mostra le impostazioni per l'hub IoT](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

Di seguito sono riportate alcune delle proprietà che possono essere impostate per un hub IoT:

**Prezzi e scalabilità**: È possibile usare questa proprietà per eseguire la migrazione a un livello diverso o impostare il numero dell'IoT Hub di unità. 

**Monitoraggio delle operazioni**: Attivare o disattivare, le diverse categorie di monitoraggio, ad esempio la registrazione per gli eventi correlati ai messaggi da dispositivo a cloud o i messaggi da cloud a dispositivo.

**Filtro IP**: Specificare un intervallo di indirizzi IP che verrà accettati o rifiutati dall'hub IoT.

**Proprietà**: Fornisce l'elenco delle proprietà che è possibile copiare e usare in altre posizioni, ad esempio l'ID di risorsa, gruppo di risorse, posizione e così via.

### <a name="shared-access-policies"></a>Criteri di accesso condivisi

È anche possibile visualizzare o modificare l'elenco dei criteri di accesso condiviso facendo clic su **Criteri di accesso condiviso** nella sezione **Impostazioni**. Questi criteri definiscono le autorizzazioni per la connessione di dispositivi e servizi all'hub IoT. 

Fare clic su **Aggiungi** per aprire il pannello **Aggiungi un criterio di accesso condiviso**.  È possibile immettere il nome dei nuovi criteri e le autorizzazioni da associare a questi criteri, come illustrato nella figura seguente:

![Screenshot che illustra l'aggiunta di un criterio di accesso condiviso](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* I criteri **Lettura registro** e **Scrittura registro** consentono di concedere diritti di accesso in lettura e scrittura per il registro delle identità. La scelta dell'opzione di scrittura include automaticamente l'opzione di lettura.

* I criteri **Connessione servizio** concedono le autorizzazioni per accedere agli endpoint del servizio, ad esempio per la **ricezione di messaggi da dispositivo a cloud**. 

* I criteri **Connessione dispositivo** concedono le autorizzazioni per l'invio e la ricezione di messaggi tramite gli endpoint sul lato dispositivo dell'hub IoT.

Fare clic su **Crea** per aggiungere i criteri appena creati all'elenco esistente.

## <a name="message-routing-for-an-iot-hub"></a>Routing dei messaggi per un hub IoT

Fare clic su **Routing messaggi** in **Messaggistica** per visualizzare il riquadro Routing messaggi, in cui si definiscono le route e gli endpoint personalizzati per l'hub. [Routing messaggi](iot-hub-devguide-messages-d2c.md) consente di gestire la modalità di invio dei dati dai dispositivi agli endpoint. Il primo passaggio consiste nell'aggiungere una nuova route. È quindi possibile aggiungere un endpoint esistente alla route o crearne uno nuovo tra i tipi supportati, ad esempio un archivio BLOB. 

![Riquadro Routing messaggi](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>Route

Route è la prima scheda nel riquadro Routing messaggi. Per aggiungere una nuova route, fare clic su **+Aggiungi**. Viene visualizzata la schermata seguente. 

![Screenshot che illustra l'aggiunta di una nuova route](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

Assegnare un nome all'hub. Il nome deve essere univoco all'interno dell'elenco di route per tale hub. 

Per **Endpoint**, è possibile selezionare un endpoint dall'elenco a discesa o aggiungerne uno nuovo. In questo esempio sono già disponibili un account di archiviazione e un contenitore. Per aggiungerli come endpoint, fare clic su **+Aggiungi** accanto all'elenco a discesa Endpoint e selezionare **Archivio BLOB**. La schermata seguente mostra dove vengono specificati l'account di archiviazione e il contenitore.

![Screenshot che mostra l'aggiunta di un endpoint di archiviazione per la regola di routing](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

Fare clic su **Selezionare un contenitore** per selezionare l'account di archiviazione e il contenitore. Dopo che questi campi sono stati selezionati, si tornerà al riquadro Endpoint. Usare le impostazioni predefinite per il resto dei campi e **Crea** per creare l'endpoint per l'account di archiviazione e aggiungerlo alle regole di routing.

Per **Origine dati** selezionare Device Telemetry Messages (Messaggi telemetria dispositivo). 

Aggiungere quindi una query di routing. In questo esempio i messaggi che hanno una proprietà dell'applicazione denominata `level` con un valore uguale a `critical` vengono indirizzati all'account di archiviazione.

![Screenshot che illustra il salvataggio di una nuova regola di routing](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

Fare clic su **Salva** per salvare la regola di routing. Si tornerà al riquadro Routing messaggi e verrà visualizzata la nuova regola di routing.

### <a name="custom-endpoints"></a>Endpoint personalizzati

Fare clic sulla scheda **Endpoint personalizzati**. Verranno visualizzati tutti gli endpoint personalizzati già creati. A questo punto, è possibile aggiungere nuovi endpoint o eliminare quelli esistenti. 

> [!NOTE]
> Se si elimina una route, non vengono eliminati gli endpoint assegnati a tale route. Per eliminare un endpoint, fare clic sulla scheda Endpoint personalizzati, selezionare l'endpoint che si vuole eliminare e fare clic su Elimina.
>

Altre informazioni sugli endpoint personalizzati sono disponibili in [Reference - IoT hub endpoints](iot-hub-devguide-endpoints.md) (Riferimenti: endpoint di hub IoT).

È possibile definire fino a 10 endpoint personalizzati per un hub IoT. 

Per un esempio completo di come usare gli endpoint personalizzati con il routing, vedere [Routing dei messaggi con l'hub IoT](tutorial-routing.md).

## <a name="find-a-specific-iot-hub"></a>Trovare un hub IoT specifico

Di seguito sono riepilogati due modi per trovare un hub IoT specifico nella sottoscrizione:

1. Se si conosce il gruppo di risorse a cui appartiene l'hub IoT, fare clic su **Gruppi di risorse** e quindi selezionare il gruppo di risorse dall'elenco. La schermata del gruppo di risorse mostra tutte le risorse in tale gruppo, inclusi gli hub IoT. Fare clic sull'hub che si sta cercando.

2. Fare clic su **Tutte le risorse**. Nel riquadro **Tutte le risorse** è disponibile un elenco a discesa che ha come impostazione predefinita `All types`. Fare clic sull'elenco a discesa e deselezionare `Select all`. Trovare `IoT Hub` e selezionarlo. Fare clic sulla casella dell'elenco a discesa per chiuderlo e le voci verranno filtrate, mostrando solo gli hub IoT.

## <a name="delete-the-iot-hub"></a>Eliminare l'hub IoT

Per eliminare un hub IoT, trovare l'hub IoT che si vuole eliminare e quindi scegliere il pulsante **Elimina** sotto il nome dell'hub IoT.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla gestione dell'hub IoT di Azure, consultare questi collegamenti:

* [Routing dei messaggi con l'hub IoT](tutorial-routing.md)
* [Metriche di hub IoT](iot-hub-metrics.md)
* [Monitoraggio delle operazioni](iot-hub-operations-monitoring.md)