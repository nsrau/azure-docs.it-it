---
title: Certificare il dispositivo di anteprima Plug and Play IoT | Microsoft Docs
description: Questa esercitazione descrive come aggiungere le informazioni sul prodotto al catalogo dei dispositivi Microsoft Azure Certified per IoT, connettere il dispositivo al servizio di certificazione di Azure IoT e quindi eseguire i test di certificazione di Plug and Play IoT.
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: tutorial
ms.author: koichih
author: konichi3
ms.date: 06/21/2019
ms.openlocfilehash: 1516a132372a81d06d82de2409c48220f27b8d87
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878732"
---
# <a name="tutorial-certify-your-iot-plug-and-play-preview-device"></a>Esercitazione: Certificare il dispositivo di anteprima Plug and Play IoT

Per pubblicare un dispositivo di anteprima Plug and Play IoT nel [catalogo dei dispositivi Microsoft Azure Certified per IoT](https://aka.ms/iotdevcat) occorre che superi un set di test di certificazione. Usare il portale di [Microsoft Azure Certified per IoT](https://aka.ms/ACFI) per sottoporre il dispositivo a certificazione. Il [servizio di certificazione di Azure IoT](https://aka.ms/azure-iot-aics) esegue i test di certificazione.

In questa esercitazione si apprenderà:

> [!div class="checklist"]
> * Quali sono i requisiti di certificazione di Plug and Play IoT.
> * Come aggiungere il nome e le informazioni sul prodotto al portale.
> * Come connettere e individuare le interfacce di Plug and Play IoT.
> * Come rivedere le interfacce di Plug and Play IoT ed eseguire i test di certificazione.
> * Come pubblicare il dispositivo Plug and Play IoT nel catalogo.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

* Un account del Centro per i partner Microsoft.
* L'ID di Microsoft Partner Network.

Per altre informazioni, vedere [How to onboard to the Azure Certified for IoT portal](howto-onboard-portal.md) (Come eseguire l'onboarding nel portale Microsoft Azure Certified per IoT).

## <a name="certification-requirements"></a>Requisiti di certificazione

Per certificare il dispositivo Plug and Play IoT, deve soddisfare i seguenti requisiti:

* Il codice del dispositivo Plug and Play IoT deve essere installato sul proprio dispositivo.
* Il codice del dispositivo Plug and Play IoT è incorporato in Azure IoT SDK.
* Il codice del dispositivo deve supportare il [servizio Device Provisioning in hub IoT](../iot-dps/about-iot-dps.md).
* Il codice del dispositivo deve implementare l'[interfaccia Informazioni dispositivo](concepts-common-interfaces.md).
* Il modello di funzionalità e il codice del dispositivo funzionano con IoT Central.

Tutti i dispositivi attualmente presenti nel catalogo vengono considerati come dispositivi Plug and Play IoT pre-certificati. Non si garantisce la qualità e la conformità del prodotto finale dei componenti software di Plug and Play IoT come SDK e Digital Twin Definition Language.

Tutti i dispositivi Plug and Play IoT pre-certificati devono ripetere la certificazione al momento della disponibilità a livello generale di Plug and Play IoT in base alla versione finale dei requisiti di certificazione e dei componenti software forniti da Microsoft.

## <a name="add-product-name"></a>Aggiungere il nome del prodotto

**Prodotto** è un nome descrittivo di un modello di prodotto che un cliente può acquistare. Per aggiungere un prodotto:

1. Accedere al portale [Microsoft Azure Certified per IoT](https://aka.ms/ACFI).
1. Passare alla pagina **Prodotti** nel portale dal menu a sinistra e selezionare **+ Nuovo**.
1. Immettere il nome descrittivo del prodotto e selezionare **Crea**. Il nome qui immesso è diverso dal nome visualizzato nel catalogo dei dispositivi.

## <a name="add-product-information"></a>Aggiungere informazioni sul prodotto

Dopo averlo correttamente creato nel portale, il prodotto viene visualizzato nella pagina **Prodotti**. Per aggiungere informazioni sul prodotto:

1. Selezionare il collegamento al prodotto creato presente nella colonna Prodotto della pagina **Prodotto**. Lo stato dovrebbe essere bozza.
1. Selezionare il collegamento **Modifica** accanto all'intestazione **Informazioni sui prodotti**. Immettere le informazioni sul prodotto nella pagina relativa e assicurarsi di completare tutti i campi obbligatori.
1. Selezionare **Salva**. Il pulsante **Salva** viene visualizzato solo quando si completano tutti i campi obbligatori. Se i campi sono incompleti, il pulsante cambia in **Save and finish later** (Salva e termina in seguito).
1. Rivedere il contenuto immesso. Completare tutti i campi obbligatori per pubblicare il dispositivo nel rispettivo catalogo. Sarà sempre possibile apportare modifiche alle informazioni sul prodotto nella pagina dei dettagli facendo clic sul collegamento **Modifica** accanto all'intestazione **Informazioni sui prodotti**.

## <a name="connect-and-discover-interfaces"></a>Connettere e individuare interfacce

Per eseguire i test di certificazione, connettere il dispositivo al [servizio di certificazione di Azure IoT](https://aka.ms/azure-iot-aics) (AICS) disponibile nel portale.

Questi passaggi vanno effettuati una tantum per eseguire i test di certificazione e non è necessario modificare il codice del dispositivo del prodotto. Per iniziare, seguire questi passaggi per connettersi ad AICS:

1. Accedere al portale usando il proprio account del Centro per i partner.
1. Fare clic su **Connect + test** per avviare il flusso di certificazione.
1. Scegliere il [metodo di autenticazione](../iot-dps/concepts-security.md#attestation-mechanism) per effettuare il provisioning del dispositivo con il [servizio Device Provisioning in hub IoT di Azure](../iot-dps/about-iot-dps.md).
   * Se si usa un [certificato X.509](../iot-hub/iot-hub-security-x509-get-started.md#prerequisites), caricare il certificato X.509 generato. Può essere opportuno rivedere il codice di esempio che mostra come usare i certificati X.509: [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/iothub_ll_client_x509_sample/iothub_ll_client_x509_sample.c), [C#](../iot-hub/iot-hub-security-x509-get-started.md).
   * Se si usa una [chiave simmetrica](../iot-dps/concepts-symmetric-key-attestation.md), copiare e incollare la chiave simmetrica nel codice del dispositivo.
   * Il metodo di autenticazione TPM non è attualmente supportato.
1. Copiare e incollare i seguenti ID generati nel codice del dispositivo.
   * [ID registrazione](../iot-dps/use-hsm-with-sdk.md)
   * [ID DPS](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
   * [Endpoint DPS](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
1. Quando il codice del dispositivo è incorporato e distribuito nel dispositivo, selezionare **Connetti** per individuare le interfacce di Plug and Play IoT.
1. Quando la connessione ad AICS è riuscita, selezionare **Avanti** per rivedere le interfacce di Plug and Play IoT individuate.

## <a name="run-tests-and-publish-the-device"></a>Eseguire i test e pubblicare il dispositivo

Nella pagina di revisione, è possibile rivedere le interfacce di Plug and Play IoT individuate. Usare questa pagina per controllare le primitive implementate correttamente nella visualizzazione dell'interfaccia. È anche possibile controllare la posizione dell'interfaccia.

1. Assicurarsi di immettere gli input del payload per i campi obbligatori. Questi campi includono le informazioni di payload per la primitiva di comando per l'interfaccia specificata.
1. Dopo aver immesso tutte le informazioni necessarie, selezionare **Avanti**.
1. Per eseguire i test per le interfacce di Plug and Play IoT implementate, selezionare **Esegui i test**.
1. Tutti i test vengono eseguiti automaticamente. Se un test non viene superato, selezionare **Visualizza log** per visualizzare i messaggi di errore di AICS e i dati di telemetria non elaborati inviati all'hub IoT.
1. Per completare i test di certificazione, selezionare **Fine**.
1. Pubblicare il dispositivo Plug and Play IoT nel catalogo. Per aggiungere il dispositivo certificato al catalogo, selezionare **Add to catalog** (Aggiungi al catalogo) sulla barra degli strumenti. Se l'opzione **Add to catalog** è disattivata, le informazioni sul prodotto sono incomplete oppure i test non sono stati superati. 
1. Selezionare il collegamento "CERTIFIED AND IN THE CATALOG" (Certificato e nel catalogo) per visualizzare il dispositivo pubblicato nel catalogo.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base sulla certificazione di un dispositivo Plug and Play IoT, è consigliabile acquisire maggiori informazioni sulla gestione dei modelli di funzionalità:

> [!div class="nextstepaction"]
> [Gestire i modelli](./howto-manage-models.md)
