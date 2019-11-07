---
title: Creare un'applicazione di analisi dei punti vendita in Azure IoT Central | Microsoft Docs
description: Questa esercitazione illustra come creare un'applicazione di analisi dei punti vendita al dettaglio in IoT Central. Oltre creare e personalizzare l'applicazione, verranno aggiunti i dispositivi dei sensori.
services: iot-central
ms.service: iot-central
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 10/03/2019
ms.openlocfilehash: 0d586aaab4fc87bb671e67152cf66e9fb1447504
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495176"
---
# <a name="tutorial-create-an-in-store-analytics-application-in-azure-iot-central"></a>Esercitazione: Creare un'applicazione di analisi dei punti vendita in Azure IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

L'esercitazione mostra agli autori di soluzioni come creare un'applicazione di analisi dei punti vendita in Azure IoT Central. L'applicazione di esempio è relativa a un punto vendita al dettaglio. Si tratta di una soluzione che tiene in considerazione le comuni esigenze aziendali in termini di monitoraggio e adattamento alle condizioni ambientali e di occupazione.

L'applicazione di esempio creata include tre dispositivi reali: un gateway Rigado Cascade 500 e due sensori RuuviTag. L'esercitazione spiega anche come usare il sensore di occupazione simulata incluso nel modello di applicazione a scopo di test. Il gateway Rigado C500 funge da hub di comunicazione nell'applicazione. Comunica con i sensori nel punto vendita e ne gestisce le connessioni al cloud. RuuviTag è un sensore ambientale che fornisce dati di telemetria, tra cui temperatura, umidità e pressione. Il sensore di occupazione simulata consente di tenere traccia del movimento e della presenza in prossimità delle casse di un punto vendita. 

Questa esercitazione include le istruzioni per connettere i dispositivi Rigado e RuuviTag all'applicazione. Se i gateway e i sensori disponibili sono diversi, è comunque possibile seguire la procedura per creare l'applicazione. L'esercitazione spiega anche come creare sensori RuuviTag simulati. I sensori simulati consentono di creare l'applicazione anche se non sono disponibili dispositivi reali. 

La soluzione di monitoraggio delle condizioni e del pagamento viene sviluppata in tre parti:

* Creare l'applicazione e connettere i dispositivi per il monitoraggio delle condizioni
* Personalizzare il dashboard per consentire gli operatori di monitorare e gestire i dispositivi
* Configurare l'esportazione dei dati per consentire ai responsabili del punto vendita di eseguire analisi e visualizzare informazioni dettagliate

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Usare il modello **Analisi punti vendita - Pagamento** di Azure IoT Central per creare un'applicazione per punti vendita al dettaglio
> * Personalizzare le impostazioni dell'applicazione
> * Creare e personalizzare i modelli di dispositivo IoT
> * Connettere i dispositivi all'applicazione
> * Aggiungere regole e azioni per il monitoraggio delle condizioni

## <a name="prerequisites"></a>Prerequisiti

Per completare questa serie di esercitazioni, sono necessari gli elementi seguenti:
* Una sottoscrizione di Azure (consigliata). Facoltativamente, è possibile usare una versione di valutazione gratuita valida per 7 giorni. Se non si ha una sottoscrizione di Azure, è possibile crearne una nella [pagina di iscrizione ad Azure](https://aka.ms/createazuresubscription).
* Accesso a un dispositivo gateway e due sensori ambientali (facoltativamente, è possibile usare dispositivi simulati come descritto nell'esercitazione)
* Modelli di dispositivo per i dispositivi usati (i modelli vengono forniti disponibili per tutti i dispositivi usati nell'esercitazione)

## <a name="create-an-application"></a>Creare un'applicazione
In questa sezione viene creata una nuova applicazione Azure IoT Central da un modello. Questa applicazione verrà usata in tutta la serie di esercitazioni per creare una soluzione completa.

Per creare una nuova applicazione Azure IoT Central:

1. Passare al sito Web di [gestione applicazioni di Azure IoT Central](https://aka.ms/iotcentral).

1. Se si ha una sottoscrizione di Azure, accedere con le credenziali usate per tale sottoscrizione, altrimenti accedere usando un account Microsoft:

    ![Immettere l'account dell'organizzazione](./media/tutorial-in-store-analytics-create-app-pnp/sign-in.png)

1. Per iniziare a creare una nuova applicazione Azure IoT Central, selezionare **Nuova applicazione**.

1. Selezionare **Vendita al dettaglio**.  Nella pagina relativa alla vendita al dettaglio sono visualizzati diversi modelli di applicazione di vendita al dettaglio.

Per creare una nuova applicazione Analisi punti vendita - Pagamento che usa le funzionalità di anteprima:  

1. Selezionare il modello di applicazione **Analisi punti vendita - Pagamento**. Questo modello include modelli di dispositivo per tutti i dispositivi usati nell'esercitazione, ad eccezione dei sensori RuuviTag. Il modello fornisce anche un dashboard operatore per monitorare le condizioni ambientali e di pagamento oltre allo stato dei dispositivi. 

1. Facoltativamente, scegliere un **nome applicazione** descrittivo. Questa applicazione si basa su un punto vendita al dettaglio fittizio denominato Contoso. Per l'esercitazione viene usato il **nome applicazione** *Contoso checkout*. Il modello di applicazione si basa sulla società fittizia Northwind. Per personalizzare l'applicazione, in questa esercitazione si usa Contoso.

    > [!NOTE]
    > Se si usa un **nome applicazione** descrittivo, è comunque necessario usare un valore univoco per l'**URL** dell'applicazione.

1. Se si ha una sottoscrizione di Azure, immettere i valori relativi a *directory, sottoscrizione di Azure e area*. Se non si ha una sottoscrizione, è possibile abilitare la **versione di valutazione gratuita di 7 giorni** e completare le informazioni di contatto richieste.  

    Per altre informazioni su directory e sottoscrizioni, vedere la [guida introduttiva per la creazione di un'applicazione](../core/quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

1. Selezionare **Create** (Crea).

    ![Pagina Crea applicazione di Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/preview-application-template.png)

## <a name="customize-application-settings"></a>Personalizzare le impostazioni dell'applicazione
In qualità di autore, è possibile modificare diverse impostazioni per personalizzare l'esperienza utente nell'applicazione. In questa sezione verrà selezionato un tema dell'applicazione predefinito. Facoltativamente, verrà illustrato come creare un tema personalizzato e aggiornare l'immagine dell'applicazione. Un tema personalizzato consente di impostare i colori del browser dell'applicazione, l'icona del browser e il logo dell'applicazione visualizzato nella testata.

Per selezionare un tema dell'applicazione predefinito:

1. Selezionare **Impostazioni** nella testata.

    ![Impostazioni dell'applicazione Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/settings-icon.png)

2. Selezionare un nuovo **tema**.

3. Selezionare **Salva**.

Invece di usare un tema predefinito, è possibile creare un tema personalizzato. Se si vuole usare un set di immagini di esempio per personalizzare l'applicazione e completare l'esercitazione, scaricare le [immagini dell'esempio Contoso](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail).

Per creare un tema personalizzato:

1. Espandere il riquadro sinistro, se non è già espanso.

    ![Riquadro sinistro di Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/dashboard-expand.png)

1. Selezionare **Amministrazione > Personalizzare l'applicazione**.

1. Usare il pulsante **Cambia** per scegliere un'immagine da caricare come **logo dell'applicazione**. Facoltativamente, specificare un valore per **Testo alternativo logo**. 

1. Usare il pulsante **Cambia** per scegliere un'immagine per l'**icona del browser** che verrà visualizzata nelle schede del browser.

1. Facoltativamente, sostituire i **colori del browser** predefiniti aggiungendo codici colore esadecimali HTML. Per l'**intestazione** aggiungere *#008575*.  Per l'**evidenziatore** aggiungere *#A1F3EA*. 

1. Selezionare **Salva**. 

    ![Logo personalizzato di Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/select-application-logo.png)

    Dopo il salvataggio, l'applicazione aggiorna i colori del browser, il logo nella testata e l'icona del browser. 

    ![Impostazioni aggiornate dell'applicazione Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/saved-application-settings.png)

Per aggiornare l'immagine dell'applicazione:

1. Selezionare **Amministrazione > Impostazioni applicazione**.

1. Usare il pulsante **Scegli un'immagine** per scegliere un'immagine da caricare come immagine dell'applicazione. Questa immagine viene visualizzata nel riquadro dell'applicazione nella pagina **App personali** del sito Web di gestione applicazioni di IoT Central.

1. Selezionare **Salva**.

1. Facoltativamente, passare alla visualizzazione **App personali** del sito Web di [gestione applicazioni di Azure IoT Central](https://aka.ms/iotcentral). Nel riquadro dell'applicazione viene visualizzata l'immagine aggiornata dell'applicazione.

    ![Personalizzazione dell'immagine dell'applicazione in Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/customize-application-image.png)

## <a name="create-device-templates"></a>Creare modelli di dispositivo
In qualità di autore, è possibile creare modelli di dispositivo che consentono all'utente e agli operatori delle applicazioni di configurare e gestire i dispositivi. Per creare un modello, è possibile crearne uno personalizzato, importare un file di modello esistente o importare un modello dal catalogo dei dispositivi Azure IoT. Dopo aver creato e personalizzato un modello di dispositivo, usarlo per connettere dispositivi reali all'applicazione. Facoltativamente, usare un modello di dispositivo per generare dispositivi simulati a scopo di test.

Il modello di applicazione **Analisi punti vendita - Pagamento** include modelli di dispositivo per diversi dispositivi.  Sono disponibili modelli di dispositivo per due dei tre dispositivi usati nell'applicazione. Il modello di dispositivo RuuviTag non è incluso nel modello di applicazione **Analisi punti vendita - Pagamento**. In questa sezione viene aggiunto un modello di dispositivo per i sensori RuuviTag all'applicazione.

Per aggiungere un modello di dispositivo RuuviTag all'applicazione:

1. Selezionare **Modelli di dispositivo** nel riquadro sinistro.

1. Selezionare **+ Nuovo** per creare un nuovo modello di dispositivo.

1. Trovare e selezionare il modello di dispositivo sensore **RuuviTag** nel catalogo di dispositivi di Azure IoT. 

1. Selezionare **Avanti: Personalizza**.

    ![Modello di dispositivo sensore RuuviTag di Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-template.png)

1. Selezionare **Create** (Crea). L'applicazione aggiunge il modello di dispositivo RuuviTag.

1. Selezionare **Modelli di dispositivo** nel riquadro sinistro. Nella pagina vengono visualizzati tutti i modelli di dispositivo inclusi nel modello di applicazione, oltre al modello di dispositivo RuuviTag appena aggiunto.

    ![Modello di dispositivo sensore RuuviTag di Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/device-templates-list.png)

## <a name="customize-device-templates"></a>Personalizzare i modelli di dispositivo
È possibile personalizzare i modelli di dispositivo nell'applicazione in tre modi. In primo luogo, è possibile personalizzare le interfacce predefinite native nei dispositivi modificando le funzionalità del dispositivo. Con un sensore di temperatura, ad esempio, è possibile modificare dettagli quali il nome visualizzato dell'interfaccia della temperatura, il tipo di dati, le unità di misura e gli intervalli operativi minimo e massimo. 

In secondo luogo, è possibile personalizzare i modelli di dispositivo aggiungendo proprietà cloud. Le proprietà cloud non fanno parte delle funzionalità predefinite del dispositivo. Sono infatti dati personalizzati che l'applicazione Azure IoT Central crea, archivia e associa ai dispositivi. Un esempio di proprietà cloud può essere costituito da un valore calcolato o da metadati, ad esempio un percorso che si vuole associare a un set di dispositivi. 

Infine, è possibile personalizzare i modelli di dispositivo creando visualizzazioni personalizzate. Le visualizzazioni consentono agli operatori di visualizzare i dati di telemetria e i metadati per i dispositivi, ad esempio le metriche e l'integrità dei dispositivi.

In questo caso si useranno i primi due metodi per personalizzare il modello di dispositivo per i sensori RuuviTag. Per informazioni sulla creazione di visualizzazioni per i sensori, vedere la guida di avvio rapido [Aggiungere un dispositivo simulato all'applicazione IoT Central](../core/quick-create-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

Per personalizzare le interfacce predefinite del modello di dispositivo RuuviTag:

1. Selezionare **Modelli di dispositivo** nel riquadro sinistro. 

1. Selezionare il modello per i sensori RuuviTag. 

1. Nascondere il riquadro sinistro. La visualizzazione riepilogo del modello include le funzionalità del dispositivo.

    ![Visualizzazione riepilogo del modello di dispositivo RuuviTag di Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-summary-view.png)

1. Selezionare **Personalizza** nel menu del modello di dispositivo RuuviTag. 

1. Scorrere l'elenco delle funzionalità e individuare il tipo di dati di telemetria `humidity`. Si tratta della riga con il valore di **Nome visualizzato** *humidity* modificabile.

Nei passaggi seguenti si personalizzerà il tipo di dati di telemetria `humidity` per i sensori RuuviTag. Facoltativamente, personalizzare alcuni degli altri tipi di dati di telemetria.

Per il tipo di dati di telemetria `humidity` apportare le modifiche seguenti:

1. Selezionare il controllo **Espandi** per espandere i dettagli dello schema per la riga.

1. Aggiornare il valore di **Nome visualizzato** da *humidity* a un valore personalizzato, ad esempio *Relative humidity*.

1. Modificare l'opzione **Tipo semantico** da *Nessuno* a *Humidity*.  Facoltativamente, impostare i valori dello schema per il tipo di dati di telemetria humidity nella visualizzazione schema espansa. Le impostazioni dello schema consentono di creare requisiti di convalida dettagliati per i dati tracciati dai sensori. Ad esempio, è possibile impostare i valori degli intervalli operativi minimo e massimo per una determinata interfaccia.

1. Fare clic su **Salva** per salvare le modifiche.

    ![Personalizzazione del modello di dispositivo RuuviTag di Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-template-customize.png)

Per aggiungere una proprietà cloud a un modello di dispositivo nell'applicazione:

1. Selezionare **Proprietà cloud** nel menu del modello di dispositivo RuuviTag.

1. Selezionare **Aggiungi proprietà cloud**. 

Specificare i valori seguenti per creare una proprietà personalizzata in cui archiviare il percorso di ogni dispositivo:

1. Immettere il valore *Location* per **Nome visualizzato**. Questo valore viene copiato automaticamente nel campo **Nome** e corrisponde a un nome descrittivo della proprietà. È possibile usare il valore copiato o modificarlo.

1. Selezionare *Stringa* nell'elenco a discesa **Schema**. Un tipo stringa consente di associare una stringa di nome di percorso a qualsiasi dispositivo basato sul modello. Ad esempio, è possibile associare un'area in un punto vendita a ogni dispositivo. Facoltativamente, è possibile impostare il **tipo semantico** della proprietà su *Location* in modo da impostare automaticamente lo **schema** su *Geopoint* e associare le coordinate GPS a un dispositivo. 

1. Impostare **Lunghezza minima** su *2*. 

1. Impostare **Taglia spazio vuoto** su **Attivato**.

1. Fare clic su **Salva** per salvare la proprietà cloud personalizzata.

    ![Personalizzazione del modello di dispositivo RuuviTag di Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-template-cloud-property.png)

1. Selezionare **Pubblica**. 

    La pubblicazione di un modello di dispositivo consente di renderlo visibile agli operatori dell'applicazione. Dopo aver pubblicato un modello, usarlo per generare dispositivi simulati per il test o per connettere dispositivi reali all'applicazione. Se sono già stati connessi dispositivi all'applicazione, la pubblicazione di un modello personalizzato consente di eseguire il push delle modifiche nei dispositivi.

## <a name="add-devices"></a>Aggiungere dispositivi
Dopo aver creato e personalizzato i modelli di dispositivo, è possibile aggiungere i dispositivi. 

Per questa esercitazione si userà il set seguente di dispositivi reali e simulati per creare l'applicazione:
- Un gateway Rigado C500 reale
- Due sensori RuuviTag reali
- Un sensore di **occupazione** simulato. Il sensore simulato è incluso nel modello di applicazione, di conseguenza non è necessario crearlo. 

> [!NOTE]
> Se non si hanno dispositivi reali, è comunque possibile completare questa esercitazione creando sensori RuuviTag simulati. Le istruzioni seguenti includono i passaggi per creare un sensore RuuviTag simulato. Non è necessario creare un gateway simulato.

Completare i passaggi nei due articoli seguenti per connettere un gateway Rigado reale e i sensori RuuviTag. Al termine, tornare a questa esercitazione. Poiché i modelli di dispositivo sono già stati creati in questa esercitazione, non è necessario crearli di nuovo nel set di istruzioni seguente.

- Per connettere un gateway Rigado, vedere [Connettere un gateway Rigado Cascade 500 all'applicazione Azure IoT Central](../core/howto-connect-rigado-cascade-500-pnp.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json).
- Per connettere sensori RuuviTag, vedere [Connettere un sensore RuuviTag all'applicazione Azure IoT Central](../core/howto-connect-ruuvi-pnp.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json). È anche possibile usare queste istruzioni per creare due sensori simulati, se necessario.

## <a name="add-rules-and-actions"></a>Aggiungere regole e azioni
Quando si usano sensori nell'applicazione Azure IoT Central per monitorare le condizioni, è possibile creare regole per eseguire azioni quando vengono soddisfatte determinate condizioni. Una regola viene associata a un modello di dispositivo e a uno o più dispositivi e contiene le condizioni che devono essere soddisfatte sulla base di dati di telemetria o eventi del dispositivo. Una regola prevede anche una o più azioni associate. Le azioni possono includere l'invio di notifiche di posta elettronica o l'attivazione di un'azione webhook per l'invio dei dati ad altri servizi. Il modello di applicazione **Analisi punti vendita - Pagamento** include alcune regole predefinite per i dispositivi nell'applicazione.

In questa sezione viene creata una nuova regola che controlla il livello massimo di umidità relativa in base ai dati di telemetria dei sensori RuuviTag. Alla regola viene aggiunta un'azione in modo che se l'umidità supera il limite massimo, l'applicazione invia un messaggio di posta elettronica. 

Per creare una regola: 

1. Espandere il riquadro sinistro.

1. Selezionare **Regole**.

1. Selezionare **+ Nuovo**.

1. Immettere *Humidity level* come nome della regola. 

1. Scegliere il modello di dispositivo RuuviTag in **Ambiti**. La regola definita verrà applicata a tutti i sensori basati su tale modello. Facoltativamente, è possibile creare un filtro che applica la regola solo a un subset definito di sensori. 

1. Scegliere `Relative humidity` come valore per **Telemetria**. Si tratta della funzionalità del dispositivo che è stata personalizzata in un passaggio precedente.

1. Scegliere `Is greater than` come valore di **Operatore**. 

1. Immettere un livello di umidità interna superiore tipo per il proprio ambiente in **Valore**. Ad esempio, immettere *65*. È stata impostata una condizione per la regola che si verifica quando l'umidità relativa in qualsiasi sensore RuuviTag reale o simulato supera questo valore. Può essere necessario incrementare o ridurre questo valore in base all'intervallo di umidità normale nell'ambiente.  

   ![Aggiunta delle condizioni per le regole in Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/rules-add-conditions.png)

Per aggiungere un'azione alla regola:

1. Selezionare **+ Posta elettronica**. 

1. Immettere *High humidity notification* come **nome visualizzato** descrittivo dell'azione. 

1. Immettere l'indirizzo di posta elettronica associato all'account in **A**. Se si usa un indirizzo di posta elettronica diverso, l'indirizzo usato deve essere quello di un utente che è stato aggiunto all'applicazione. L'utente deve inoltre eseguire almeno una volta l'accesso e la disconnessione.

1. Facoltativamente, immettere una nota da includere nel testo del messaggio di posta elettronica.

1. Selezionare **Fine** per completare l'azione.

   ![Aggiunta di azioni alla regole in Azure IoT Central](./media/tutorial-in-store-analytics-create-app-pnp/rules-add-action.png)

1. Selezionare **Salva** per salvare e attivare la nuova regola. 

    Entro pochi minuti l'account di posta elettronica specificato inizierà a ricevere messaggi di posta elettronica. L'applicazione invia un messaggio di posta elettronica ogni volta che un sensore indica che il livello di umidità supera il valore impostato nella condizione.

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come:

* Usare il modello **Analisi punti vendita - Pagamento** di Azure IoT Central per creare un'applicazione per punti vendita al dettaglio
* Personalizzare le impostazioni dell'applicazione
* Creare e personalizzare i modelli di dispositivo IoT
* Connettere i dispositivi all'applicazione
* Aggiungere regole e azioni per il monitoraggio delle condizioni

Ora che è stata creata un'applicazione Azure IoT Central per il monitoraggio delle condizioni, il passaggio successivo consigliato è il seguente:

> [!div class="nextstepaction"]
> [Personalizzare il dashboard dell'operatore](./tutorial-in-store-analytics-customize-dashboard-pnp.md)
