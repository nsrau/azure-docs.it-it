---
title: Avvio rapido - Gestire gli eventi SMS
titleSuffix: An Azure Communication Services quickstart
description: Informazioni su come gestire gli eventi SMS con i Servizi di comunicazione di Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e8decbe22f869573bf7a2221099d2bc30c00fc8a
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888845"
---
# <a name="quickstart-handle-sms-events"></a>Avvio rapido: Gestire gli eventi SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Introduzione all'uso dei Servizi di comunicazione di Azure con Griglia di eventi di Azure per gestire gli eventi SMS dei servizi di comunicazione. 

## <a name="about-azure-event-grid"></a>Informazioni su Griglia di eventi di Azure

[Griglia di eventi di Azure](../../../event-grid/overview.md) è un servizio di gestione degli eventi basato sul cloud. In questo articolo si apprenderà come eseguire la sottoscrizione agli [eventi del servizio di comunicazione](../../concepts/event-handling.md) e attivare un evento per visualizzare il risultato. In genere, si inviano eventi a un endpoint che elabora i dati dell'evento e intraprende azioni. In questo articolo gli eventi vengono inviati a un'app Web che raccoglie e visualizza i messaggi.

## <a name="prerequisites"></a>Prerequisiti
- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Una risorsa del Servizio di comunicazione di Azure. Altre informazioni sono disponibili nell'avvio rapido [Creare una risorsa di comunicazione di Azure](../create-communication-resource.md).
- Un numero di telefono abilitato per gli SMS. [Ottenere un numero di telefono](./get-phone-number.md).

## <a name="setting-up"></a>Configurazione

### <a name="enable-event-grid-resource-provider"></a>Abilitare il provider di risorse di Griglia di eventi

Se è la prima volta che si usa Griglia di eventi nella sottoscrizione di Azure, è necessario registrare il provider di risorse di Griglia di eventi seguendo la procedura indicata di seguito:

Nel portale di Azure:

1. Scegliere **Sottoscrizioni** dal menu sinistro.
2. Selezionare la sottoscrizione in uso per Griglia di eventi.
3. Nel menu sinistro, in **Impostazioni**, scegliere **Provider di risorse**.
4. Cercare **Microsoft.EventGrid**.
5. Se non è registrato, selezionare **Registra**. 

La registrazione può richiedere qualche minuto. Selezionare **Aggiorna** per aggiornare lo stato. Quando lo **Stato** è **Registrato**, è possibile continuare.

### <a name="event-grid-viewer-deployment"></a>Distribuzione del Visualizzatore Griglia di eventi

In questo avvio rapido verrà usato il [Visualizzatore di Griglia di eventi di Azure di esempio](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) per visualizzare gli eventi quasi in tempo reale. In questo modo, verrà fornita all'utente l'esperienza di un feed in tempo reale. Inoltre, il payload di ogni evento dovrebbe essere disponibile anche per l'ispezione.  

## <a name="subscribe-to-the-sms-events-using-web-hooks"></a>Effettuare la sottoscrizione agli eventi SMS tramite i webhook

Nel portale, passare alla risorsa dei Servizi di comunicazione di Azure creata. All'interno della risorsa del Servizio di comunicazione selezionare **Eventi** dal menu a sinistra della pagina **Servizi di comunicazione**.

:::image type="content" source="./media/handle-sms-events/select-events.png" alt-text="Screenshot che mostra la selezione del pulsante di sottoscrizione all'evento all'interno della pagina degli eventi di una risorsa.":::

Premere **Aggiungi sottoscrizione evento** per avviare la creazione guidata.

Nella pagina **Crea sottoscrizione evento** immettere un **nome**  per la sottoscrizione all'evento.

È possibile effettuare la sottoscrizione a eventi specifici per indicare a Griglia di eventi quali eventi SMS si desidera rilevare e dove inviarli. Selezionare gli eventi a cui si desidera effettuare la sottoscrizione dal menu a discesa. Per gli eventi SMS è possibile scegliere tra `SMS Received` e `SMS Delivery Report Received`. 

Se viene chiesto di specificare un valore per **Nome dell'argomento del sistema**, è possibile fornire una stringa univoca. Questo campo non ha effetto sull'esperienza utente e viene usato solo a scopi di telemetria interna.

Consultare l'elenco completo degli [eventi supportati dai Servizi di comunicazione di Azure](../../concepts/event-handling.md).

:::image type="content" source="./media/handle-sms-events/select-events-create-eventsub.png" alt-text="Screenshot che mostra i tipi di evento SMS ricevuto e Rapporto di recapito SMS selezionati.":::

Selezionare **Webhook** per **Tipo di endpoint**. 

:::image type="content" source="./media/handle-sms-events/select-events-create-linkwebhook.png" alt-text="Screenshot che mostra il campo Tipo di endpoint impostato su Webhook.":::

Per **Endpoint** fare clic su **Seleziona un endpoint** e immettere l'URL dell'app Web.

In questo caso, verrà usato l'URL del [Visualizzatore di Griglia di eventi di Azure di esempio](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) configurato in precedenza nell'avvio rapido. L'URL dell'esempio sarà nel formato: `https://{{site-name}}.azurewebsites.net/api/updates`

Selezionare **Confermare la selezione**.

:::image type="content" source="./media/handle-sms-events/select-events-create-selectwebhook-epadd.png" alt-text="Screenshot che mostra la finestra di conferma dell'endpoint webhook.":::

## <a name="viewing-sms-events"></a>Visualizzazione degli eventi SMS

### <a name="triggering-sms-events"></a>Attivazione degli eventi SMS

Per visualizzare i trigger di evento, è necessario generare innanzitutto gli eventi.

- Gli eventi `SMS Received` vengono generati quando il numero di telefono dei Servizi di comunicazione riceve un SMS. Per attivare un evento è sufficiente inviare un messaggio dal proprio telefono al numero di telefono collegato alla risorsa dei Servizi di comunicazione.
- Gli eventi `SMS Delivery Report Received` vengono generati quando si invia un SMS a un utente tramite un numero di telefono dei Servizi di comunicazione. Per attivare un evento è necessario abilitare `Delivery Report` nelle opzioni dell'[SMS inviato](../telephony-sms/send.md). Provare a inviare un messaggio al proprio telefono con `Delivery Report`. Il completamento di queste operazioni comporta l'addebito di alcuni centesimi (EUR) sull'account Azure.

Consultare l'elenco completo degli [eventi supportati dai Servizi di comunicazione di Azure](../../concepts/event-handling.md).

### <a name="receiving-sms-events"></a>Ricezione di eventi SMS

Dopo aver completato una delle azioni descritte sopra, si noterà che gli eventi `SMS Received` e `SMS Delivery Report Received` vengono inviati all'endpoint. Questi eventi sono visualizzati nel [Visualizzatore di Griglia di eventi di Azure di esempio](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) configurato all'inizio. È possibile premere l'icona a forma di occhio accanto all'evento per visualizzare l'intero payload. Gli eventi avranno l'aspetto seguente:

:::image type="content" source="./media/handle-sms-events/sms-received.png" alt-text="Screenshot che mostra lo schema di Griglia di eventi di un evento SMS ricevuto.":::

:::image type="content" source="./media/handle-sms-events/sms-delivery-report-received.png" alt-text="Screenshot che mostra lo schema di Griglia di eventi di un evento Rapporto di recapito SMS.":::

Altre informazioni sugli [schemi di eventi e altri concetti relativi agli eventi](../../concepts/event-handling.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire e rimuovere una sottoscrizione di Servizi di comunicazione, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate. Vedere altre informazioni sulla [pulizia delle risorse](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Passaggi successivi

In questo avvio rapido si è appreso come utilizzare gli eventi SMS. È possibile ricevere messaggi SMS creando una sottoscrizione di Griglia di eventi.

> [!div class="nextstepaction"] 
> [Invia SMS](../telephony-sms/send.md)

Può essere utile vedere anche gli articoli seguenti:

 - [Altre informazioni sui concetti relativi alla gestione degli eventi](../../concepts/event-handling.md)
 - [Altre informazioni su Griglia di eventi](../../../event-grid/overview.md)