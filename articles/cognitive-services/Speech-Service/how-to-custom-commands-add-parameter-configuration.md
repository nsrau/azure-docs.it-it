---
title: 'Procedura: configurare un parametro come entità External Entities'
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come configurare un parametro di stringa per fare riferimento al catalogo esposto su un endpoint Web.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: sausin
ms.openlocfilehash: 45dba3b7f46ec558c46b8505da26fd3ef4de4cbc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284184"
---
# <a name="add-configurations-to-commands-parameters"></a>Aggiungere configurazioni ai parametri dei comandi

In questo articolo vengono fornite ulteriori informazioni sulla configurazione dei parametri avanzati, tra cui:

 - Come i valori dei parametri possono appartenere a un set definito esternamente all'applicazione di comandi personalizzati
 - Come aggiungere clausole di convalida sul valore dei parametri

## <a name="prerequisites"></a>Prerequisiti

È necessario avere completato i passaggi descritti negli articoli seguenti:

> [!div class="checklist"]
> * [Procedura: creare un'applicazione con semplici comandi](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Procedura: aggiungere parametri ai comandi](./how-to-custom-commands-add-parameters-to-commands.md)


## <a name="configure-parameter-as-external-catalog-entity"></a>Configurare il parametro come entità del catalogo esterno

In questa sezione vengono configurati i parametri di tipo stringa per fare riferimento a cataloghi esterni ospitati su un endpoint Web. Ciò consente di aggiornare il catalogo esterno in modo indipendente senza apportare modifiche all'applicazione comandi personalizzati. Questo approccio è utile nei casi in cui le voci del catalogo possono essere di grandi dimensioni.

Riutilizzare il parametro **SubjectDevice** del comando **TurnOnOff** . La configurazione corrente per questo parametro è **accettare gli input predefiniti dal catalogo interno**. Si riferisce all'elenco statico di dispositivi, come definito nella configurazione dei parametri. Si vuole spostare questo contenuto in un'origine dati esterna che può essere aggiornata in modo indipendente.

Per eseguire questa operazione, iniziare aggiungendo un nuovo endpoint Web. Passare alla sezione **endpoint Web** nel riquadro a sinistra e aggiungere un nuovo endpoint Web con la configurazione seguente.

| Impostazione | Valore consigliato |
|----|----|
| Nome | `getDevices` |
| URL | `https://aka.ms/speech/cc-sampledevices` |
| Metodo | GET |


Se il valore suggerito per l'URL non è corretto, è necessario configurare e ospitare un semplice endpoint Web che restituisce un JSON costituito dall'elenco dei dispositivi che possono essere controllati. L'endpoint Web deve restituire un formato JSON come segue:
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```


Passare quindi alla pagina delle impostazioni dei parametri **SubjectDevice** e modificare le proprietà nel modo seguente.

| Impostazione | Valore consigliato |
| ----| ---- |
| Configurazione | Accetta input predefiniti dal catalogo esterno |                               
| Endpoint Catalogo | getDevices |
| Metodo | GET |

Selezionare quindi **Salva**.

> [!IMPORTANT]
> Non verrà visualizzata un'opzione per configurare un parametro per accettare gli input da un catalogo esterno, a meno che non sia stato impostato l'endpoint Web nella sezione **endpoint Web** del riquadro sinistro.

### <a name="try-it-out"></a>Provare questa operazione

Selezionare **Train** e attendere il completamento del training. Al termine del training, selezionare **test** e provare alcune interazioni.

* Input: attiva
* Output: quale dispositivo si vuole controllare?
* Input: luci
* Output: OK, accensione delle luci

> [!NOTE]
> Si noti che è possibile controllare ora tutti i dispositivi ospitati nell'endpoint Web. È comunque necessario eseguire il training dell'applicazione per testare le nuove modifiche e ripubblicare l'applicazione.

## <a name="add-validation-to-parameters"></a>Aggiungere la convalida ai parametri

Le **convalide** sono costrutti applicabili a determinati tipi di parametro che consentono di configurare vincoli sul valore del parametro e di richiedere la correzione se i valori non rientrano nei vincoli. Per un elenco completo dei tipi di parametro che estendono il costrutto di convalida, vedere [riferimenti](./custom-commands-references.md)

Testare le convalide usando il comando **setemperature** . Usare la procedura seguente per aggiungere una convalida per il parametro **temperature** .

1. Selezionare il comando **setemperature** nel riquadro sinistro.
1. Selezionare **temperatura** nel riquadro centrale.
1. Selezionare **Aggiungi una convalida** presente nel riquadro di destra.
1. Nella finestra **nuova convalida** configurare la convalida come indicato di seguito e selezionare **Crea**.


    | Configurazione parametri | Valore consigliato | Descrizione |
    | ---- | ---- | ---- |
    | Min Value (Valore minimo) | `60` | Per i parametri number, il valore minimo che questo parametro può assumere |
    | Max Value (Valore massimo) | `80` | Per i parametri number, il valore massimo che questo parametro può assumere |
    | Risposta non riuscita |  Editor semplice > prima variante >`Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Richiedi un nuovo valore se la convalida ha esito negativo |

    > [!div class="mx-imgBorder"]
    > ![Aggiungere una convalida dell'intervallo](media/custom-commands/add-validations-temperature.png)

### <a name="try-it-out"></a>Provare questa operazione

1. Selezionare l'icona del **Training** presente nella parte superiore del riquadro destro.

1. Una volta completato il training, selezionare **test** e provare alcune interazioni:

    - Input: impostare la temperatura su 72 gradi
    - Output: OK, impostazione della temperatura su 72 gradi
    - Input: impostare la temperatura su 45 gradi
    - Output: è possibile impostare la temperatura solo tra 60 e 80 gradi
    - Input: renderlo 72 gradi
    - Output: OK, impostazione della temperatura su 72 gradi

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: aggiungere regole di interazione](./how-to-custom-commands-add-interaction-rules.md)
