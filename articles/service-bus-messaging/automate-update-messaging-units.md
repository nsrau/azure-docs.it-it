---
title: Bus di servizio di Azure - Aggiornare automaticamente le unità di messaggistica
description: Questo articolo illustra come è possibile usare l'aggiornamento automatico delle unità di messaggistica di uno spazio dei nomi del bus di servizio.
ms.topic: how-to
ms.date: 09/15/2020
ms.openlocfilehash: 0a72cc991e768a7bed01762d984cc56238ae0ad0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984778"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Aggiornare automaticamente le unità di messaggistica di uno spazio dei nomi del bus di servizio di Azure 
Il ridimensionamento automatico offre la possibilità di avere la quantità corretta di risorse in esecuzione per gestire il carico dell'applicazione. Consente di aggiungere risorse per gestire gli incrementi di carico nonché di risparmiare denaro rimuovendo le risorse inattive. Per ulteriori informazioni sulla funzionalità di scalabilità automatica di monitoraggio di Azure, vedere [Panoramica della scalabilità automatica in Microsoft Azure](../azure-monitor/platform/autoscale-overview.md) . 

La messaggistica di livello Premium del bus di servizio garantisce l'isolamento delle risorse a livello di CPU e di memoria in modo che ogni carico di lavoro del cliente venga eseguito in isolamento. Questo contenitore di risorse è detto **unità di messaggistica**. Per altre informazioni sulle unità di messaggistica, vedere [messaggistica Premium del bus di servizio](service-bus-premium-messaging.md). 

Usando la funzionalità di scalabilità automatica per gli spazi dei nomi premium del bus di servizio, è possibile specificare un numero minimo e massimo di [unità di messaggistica](service-bus-premium-messaging.md) e aggiungere o rimuovere unità di messaggistica automaticamente in base a un set di regole. 

Ad esempio, è possibile implementare gli scenari di ridimensionamento seguenti per gli spazi dei nomi del bus di servizio usando la funzionalità di scalabilità automatica. 

- Aumentare le unità di messaggistica per uno spazio dei nomi del bus di servizio quando l'utilizzo della CPU dello spazio dei nomi supera il 75%. 
- Ridurre le unità di messaggistica per uno spazio dei nomi del bus di servizio quando l'utilizzo della CPU dello spazio dei nomi scende sotto il 25%. 
- Usare più unità di messaggistica durante l'orario di ufficio e meno durante le ore di inattività. 

Questo articolo illustra come è possibile ridimensionare automaticamente uno spazio dei nomi del bus di servizio (aggiornamento delle [unità di messaggistica](service-bus-premium-messaging.md)) nell'portale di Azure. 

> [!IMPORTANT]
> Questo articolo si applica solo al livello **Premium** del bus di servizio di Azure. 

## <a name="autoscale-setting-page"></a>Pagina impostazione di scalabilità automatica
Per prima cosa, seguire questa procedura per passare alla pagina **delle impostazioni di scalabilità** automatica per lo spazio dei nomi del bus di servizio.

1. Accedere [portale di Azure](https://portal.azure.com). 
2. Nella barra di ricerca digitare **bus**di servizio, selezionare **bus di servizio** nell'elenco a discesa e premere **invio**. 
1. Selezionare lo **spazio dei nomi Premium** dall'elenco degli spazi dei nomi. 
1. Passa alla pagina **scala** . 

    :::image type="content" source="./media/automate-update-messaging-units/scale-page.png" alt-text="Spazio dei nomi del bus di servizio-pagina scala":::

## <a name="manual-scale"></a>Scalabilità manuale 
Questa impostazione consente di impostare un numero fisso di unità di messaggistica per lo spazio dei nomi. 

1. Nella pagina **impostazione di scalabilità** automatica selezionare **Scala manuale** se non è già selezionata. 
1. Per impostazione **unità di messaggistica** selezionare il numero di unità di messaggistica nell'elenco a discesa.
1. Selezionare **Salva** sulla barra degli strumenti per salvare l'impostazione. 

    :::image type="content" source="./media/automate-update-messaging-units/manual-scale.png" alt-text="Spazio dei nomi del bus di servizio-pagina scala":::       


## <a name="custom-autoscale---default-condition"></a>Scalabilità automatica personalizzata-condizione predefinita
È possibile configurare il ridimensionamento automatico delle unità di messaggistica usando le condizioni. Questa condizione di ridimensionamento viene eseguita quando nessuna delle altre condizioni di scala corrisponde. È possibile impostare la condizione predefinita in uno dei modi seguenti:

- Ridimensionare in base a una metrica (ad esempio utilizzo della CPU o della memoria)
- Scalabilità a un numero specifico di unità di messaggistica

Non è possibile impostare una pianificazione per la scalabilità automatica in base a giorni specifici o a un intervallo di date per una condizione predefinita. Questa condizione di ridimensionamento viene eseguita quando nessuna delle altre condizioni di ridimensionamento con pianificazioni corrisponde. 

### <a name="scale-based-on-a-metric"></a>Ridimensionare in base a una metrica
La procedura seguente illustra come aggiungere una condizione per aumentare automaticamente le unità di messaggistica (scalabilità orizzontale) quando l'utilizzo della CPU è superiore al 75% e ridurre le unità di messaggistica (scalabilità orizzontale) quando l'utilizzo della CPU è inferiore al 25%. Gli incrementi vengono eseguiti da 1 a 2, da 2 a 4 e da 4 a 8. Analogamente, i decrementi vengono eseguiti da 8 a 4, da 4 a 2 e da 2 a 1. 

1. Nella pagina **impostazione di scalabilità** automatica selezionare **scalabilità automatica personalizzata** per l'opzione **scegliere la modalità di ridimensionamento della risorsa** . 
1. Nella sezione **predefinita** della pagina specificare un **nome** per la condizione predefinita. Selezionare l'icona a **matita** per modificare il testo. 
1. Selezionare **Ridimensiona in base a una metrica per la** **modalità di ridimensionamento**. 
1. Selezionare **+ Aggiungi una regola**. 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-add-rule-link.png" alt-text="Spazio dei nomi del bus di servizio-pagina scala":::    
1. Nella pagina **regola di scalabilità** , attenersi alla seguente procedura:
    1. Selezionare una metrica dall'elenco a discesa **nome metrica** . In questo esempio, si tratta di una **CPU**. 
    1. Selezionare un operatore e i valori di soglia. In questo esempio sono **maggiori di** e **75** per la **soglia della metrica per attivare un'azione di ridimensionamento**. 
    1. Selezionare un' **operazione** nella sezione **azione** . In questo esempio è impostato su **increase**. 
    1. Selezionare quindi **Aggiungi**
    
        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-75.png" alt-text="Spazio dei nomi del bus di servizio-pagina scala":::       

        > [!NOTE]
        > La funzionalità di scalabilità automatica aumenta le unità di messaggistica per lo spazio dei nomi se l'utilizzo complessivo della CPU supera il 75% in questo esempio. Gli incrementi vengono eseguiti da 1 a 2, da 2 a 4 e da 4 a 8. 
1. Selezionare **+ Aggiungi nuovamente una regola** e attenersi alla seguente procedura nella pagina **regola di scalabilità** :
    1. Selezionare una metrica dall'elenco a discesa **nome metrica** . In questo esempio, si tratta di una **CPU**. 
    1. Selezionare un operatore e i valori di soglia. In questo esempio sono **minori di** e **25** per la **soglia della metrica per attivare un'azione di ridimensionamento**. 
    1. Selezionare un' **operazione** nella sezione **azione** . In questo esempio è impostato su **Diminuisci**. 
    1. Selezionare quindi **Aggiungi** 

        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-25.png" alt-text="Spazio dei nomi del bus di servizio-pagina scala":::       

        > [!NOTE]
        > La funzionalità di scalabilità automatica riduce le unità di messaggistica per lo spazio dei nomi se l'utilizzo complessivo della CPU scende al di sotto del 25% in questo esempio. I decrementi vengono eseguiti da 8 a 4, da 4 a 2 e da 2 a 1. 
1. Impostare il numero **minimo** e **massimo** e il numero **predefinito** di unità di messaggistica.

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-based.png" alt-text="Spazio dei nomi del bus di servizio-pagina scala":::
1. Selezionare **Salva** sulla barra degli strumenti per salvare l'impostazione di scalabilità automatica. 
        
### <a name="scale-to-specific-number-of-messaging-units"></a>Scalabilità a un numero specifico di unità di messaggistica
Attenersi alla procedura seguente per configurare la regola per la scalabilità dello spazio dei nomi per l'utilizzo di un numero specifico di unità di messaggistica. Anche in questo caso, la condizione predefinita viene applicata quando nessuna delle altre condizioni di scala corrisponde. 

1. Nella pagina **impostazione di scalabilità** automatica selezionare **scalabilità automatica personalizzata** per l'opzione **scegliere la modalità di ridimensionamento della risorsa** . 
1. Nella sezione **predefinita** della pagina specificare un **nome** per la condizione predefinita. 
1. Selezionare **adatta a unità di messaggistica specifiche** per la **modalità di ridimensionamento**. 
1. Per **unità di messaggistica**selezionare il numero di unità di messaggistica predefinite. 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-messaging-units.png" alt-text="Spazio dei nomi del bus di servizio-pagina scala":::       

## <a name="custom-autoscale---additional-conditions"></a>Ridimensionamento automatico personalizzato-condizioni aggiuntive
La sezione precedente illustra come aggiungere una condizione predefinita per l'impostazione di scalabilità automatica. Questa sezione illustra come aggiungere altre condizioni all'impostazione di scalabilità automatica. Per queste condizioni aggiuntive non predefinite, è possibile impostare una pianificazione basata su giorni specifici di una settimana o un intervallo di date. 

### <a name="scale-based-on-a-metric"></a>Ridimensionare in base a una metrica
1. Nella pagina **impostazione di scalabilità** automatica selezionare **scalabilità automatica personalizzata** per l'opzione **scegliere la modalità di ridimensionamento della risorsa** . 
1. Selezionare **Aggiungi una condizione di scala** nel blocco **predefinito** . 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="Spazio dei nomi del bus di servizio-pagina scala":::    
1. Specificare un **nome** per la condizione. 
1. Verificare che sia selezionata l'opzione **Ridimensiona in base a una metrica** . 
1. Selezionare **+ Aggiungi una regola** per aggiungere una regola per aumentare le unità di messaggistica quando l'utilizzo complessivo della CPU supera il 75%. Seguire i passaggi della sezione [condizione predefinita](#custom-autoscale---default-condition) . 
5. Impostare il numero **minimo** e **massimo** e il numero **predefinito** di unità di messaggistica.
6. È anche possibile impostare una **pianificazione** su una condizione personalizzata, ma non sulla condizione predefinita. È possibile specificare le date di inizio e di fine per la condizione (o) selezionare giorni specifici (lunedì, martedì e così via) di una settimana. 
    1. Se si seleziona **specifica le date di inizio/fine**, selezionare il **fuso orario**, la data e l' **ora di inizio** e la **data e l'ora di fine** (come illustrato nella figura seguente) per applicare la condizione. 

       :::image type="content" source="./media/automate-update-messaging-units/custom-min-max-default.png" alt-text="Spazio dei nomi del bus di servizio-pagina scala":::
    1. Se si seleziona **Ripeti giorni specifici**, selezionare i giorni della settimana, il fuso orario, l'ora di inizio e l'ora di fine in cui applicare la condizione. 

        :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days.png" alt-text="Spazio dei nomi del bus di servizio-pagina scala":::
  
### <a name="scale-to-specific-number-of-messaging-units"></a>Scalabilità a un numero specifico di unità di messaggistica
1. Nella pagina **impostazione di scalabilità** automatica selezionare **scalabilità automatica personalizzata** per l'opzione **scegliere la modalità di ridimensionamento della risorsa** . 
1. Selezionare **Aggiungi una condizione di scala** nel blocco **predefinito** . 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="Spazio dei nomi del bus di servizio-pagina scala":::    
1. Specificare un **nome** per la condizione. 
2. Selezionare l'opzione **adatta a unità di messaggistica specifiche** per la **modalità di ridimensionamento**. 
1. Selezionare il numero di **unità di messaggistica** nell'elenco a discesa. 
6. Per la **pianificazione**, specificare le date di inizio e di fine per la condizione (o) selezionare giorni specifici (lunedì, martedì e così via) di una settimana e degli orari. 
    1. Se si seleziona **specifica le date di inizio/fine**, selezionare il **fuso orario**, la data e l' **ora di inizio** e la **data e l'ora di fine** per rendere effettiva la condizione. 
    
    :::image type="content" source="./media/automate-update-messaging-units/scale-specific-messaging-units-start-end-dates.png" alt-text="Spazio dei nomi del bus di servizio-pagina scala":::        
    1. Se si seleziona **Ripeti giorni specifici**, selezionare i giorni della settimana, il fuso orario, l'ora di inizio e l'ora di fine in cui applicare la condizione.
    
    :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days-2.png" alt-text="Spazio dei nomi del bus di servizio-pagina scala":::

> [!IMPORTANT]
> Per altre informazioni sul funzionamento delle impostazioni di scalabilità automatica, in particolare su come seleziona un profilo o una condizione e valuta più regole, vedere [informazioni sulle impostazioni di scalabilità](../azure-monitor/platform/autoscale-understanding-settings.md)automatica.          

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle unità di messaggistica, vedere [Messaggistica Premium](service-bus-premium-messaging.md)

