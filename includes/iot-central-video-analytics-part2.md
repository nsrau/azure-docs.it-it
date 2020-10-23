---
title: includere file
description: includere file
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 383cd286f89bde13f5e557792e980f0455e00917
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876694"
---
## <a name="deploy-and-configure-azure-media-services"></a>Distribuire e configurare Servizi multimediali di Azure

La soluzione usa un account di Servizi multimediali di Azure per archiviare i clip video di rilevamento degli oggetti creati dal dispositivo gateway IoT Edge.

Quando si crea l'account di Servizi multimediali:

- È necessario specificare un nome di account, una sottoscrizione di Azure, una località, un gruppo di risorse e un account di archiviazione. Creare un nuovo account di archiviazione usando le impostazioni predefinite durante la creazione dell'account di Servizi multimediali.

- Scegliere l'area **Stati Uniti orientali** come località.

- Creare un nuovo gruppo di risorse denominato *lva-rg* nell'area **Stati Uniti orientali** per gli account di archiviazione e di Servizi multimediali. Al termine delle esercitazioni, è possibile rimuovere tutte le risorse in modo semplice eliminando il gruppo di risorse *lva-rg*.

Creare l'[account di Servizi multimediali nel portale di Azure](https://portal.azure.com/?r=1#create/Microsoft.MediaService).

> [!TIP]
> In queste esercitazioni viene usata l'area **Stati Uniti orientali** per tutti gli esempi. Se lo si preferisce, è possibile usare un'area più vicina.

Prendere nota del nome dell'account di **Servizi multimediali** nel file *scratchpad.txt*.

Al termine della distribuzione, passare alla pagina **Proprietà** per l'account di **Servizi multimediali**. Prendere nota dell'**ID risorsa** nel file *scratchpad.txt* perché questo valore verrà usato in seguito durante la configurazione del modulo IoT Edge.

Configurare quindi un'entità servizio Azure Active Directory per la risorsa di Servizi multimediali. Selezionare **Accesso all'API** e quindi **Autenticazione tramite entità servizio**. Creare una nuova app di Azure Active Directory con lo stesso nome della risorsa di Servizi multimediali e creare un segreto con una descrizione *IoT Edge Access*.

:::image type="content" source="./media/iot-central-video-analytics-part2/media-service-authentication.png" alt-text="Configurare un'app di Azure AD per Servizi multimediali di Azure":::

Quando il segreto viene generato, scorrere verso il basso fino alla sezione **Copiare le credenziali per connettere l'applicazione dell'entità servizio.** . Selezionare quindi **JSON**. Da questa posizione è possibile copiare tutte le informazioni sulle credenziali contemporaneamente. Prendere nota di queste informazioni nel file *scratchpad.txt* perché verranno usate in seguito durante la configurazione del dispositivo IoT Edge.

> [!WARNING]
> Questa è l'unica possibilità per visualizzare e salvare il segreto. Se lo si perde, sarà necessario generare un altro segreto.

## <a name="create-the-azure-iot-central-application"></a>Creare l'applicazione Azure IoT Central

In questa sezione viene creata una nuova applicazione Azure IoT Central da un modello. Questa applicazione verrà usata in tutta la serie di esercitazioni per creare una soluzione completa.

Per creare una nuova applicazione Azure IoT Central:

1. Passare al sito Web di [gestione applicazioni di Azure IoT Central](https://aka.ms/iotcentral).

1. Accedere con le credenziali usate per l'accesso alla sottoscrizione di Azure.

1. Per iniziare a creare una nuova applicazione Azure IoT Central, selezionare **Nuova applicazione** nella pagina **Compila**.

1. Selezionare **Vendita al dettaglio**. Nella pagina relativa alla vendita al dettaglio sono visualizzati diversi modelli di applicazione di vendita al dettaglio.

Per creare una nuova applicazione di analisi video:

1. Selezionare il modello di applicazione **Analisi video - rilevamento movimento e oggetti**. Questo modello include i modelli per i dispositivi usati nell'esercitazione. Il modello include i dashboard di esempio che gli operatori possono usare per eseguire attività quali il monitoraggio e la gestione delle fotocamere.

1. Facoltativamente, scegliere un **nome applicazione** descrittivo. Questa applicazione si basa su un punto vendita al dettaglio fittizio denominato Northwind Traders. Per **Nome applicazione** l'esercitazione usa *Northwind Traders video analytics*.

    > [!NOTE]
    > Se si usa un nome descrittivo per **Nome applicazione**, è comunque necessario usare un valore univoco per l'**URL** dell'applicazione.

1. Se si ha una sottoscrizione di Azure, selezionare **Directory**, **Sottoscrizione di Azure** e **Stati Uniti** per **Località**. Se non si ha una sottoscrizione, è possibile abilitare la **versione di valutazione gratuita di 7 giorni** e completare le informazioni di contatto richieste. Questa esercitazione usa tre dispositivi, ovvero due telecamere e un dispositivo IoT Edge, quindi se non si usa la versione di valutazione gratuita verranno addebitati i costi per l'utilizzo.

    Per altre informazioni su directory, sottoscrizioni e località, vedere l'[argomento di avvio rapido per creare un'applicazione](../articles/iot-central/core/quick-deploy-iot-central.md).

1. Selezionare **Crea**.

    :::image type="content" source="./media/iot-central-video-analytics-part2/new-application.png" alt-text="Configurare un'app di Azure AD per Servizi multimediali di Azure":::

### <a name="retrieve-the-configuration-data"></a>Recuperare i dati di configurazione

Più avanti in questa esercitazione quando si configura il gateway IoT Edge sono necessari alcuni dati di configurazione dell'applicazione IoT Central. Il dispositivo IoT Edge necessita di queste informazioni per la registrazione e la connessione all'applicazione.

Nella sezione **Amministrazione** selezionare **Applicazione** e prendere nota dei valori di **URL applicazione** e **ID applicazione** nel file *scratchpad.txt*:

:::image type="content" source="./media/iot-central-video-analytics-part2/administration.png" alt-text="Configurare un'app di Azure AD per Servizi multimediali di Azure":::

Selezionare **Token API** e generare un nuovo token denominato **LVAEdgeToken** per il ruolo **Operatore**:

:::image type="content" source="./media/iot-central-video-analytics-part2/token.png" alt-text="Configurare un'app di Azure AD per Servizi multimediali di Azure":::

Prendere nota del token nel file *scratchpad.txt* per usarlo in seguito. Dopo la chiusura della finestra di dialogo, non sarà più possibile visualizzare il token.

Nella sezione **Amministrazione** selezionare **Connessione del dispositivo** e quindi **SAS-IoT-Devices**.

Prendere nota del valore di **Chiave primaria** per i dispositivi nel file *scratchpad.txt*. Questo *token di firma di accesso condiviso del gruppo primario* verrà usato in seguito durante la configurazione del dispositivo IoT Edge.
