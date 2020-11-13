---
title: "Procedura: esportare un'applicazione di comandi personalizzati come servizio di riconoscimento vocale remoto"
titleSuffix: Azure Cognitive Services
description: In questo articolo si apprenderà come esportare un'applicazione di comandi personalizzati come abilità
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: sausin
ms.openlocfilehash: 8c7cb1e9f39b1de7897da29467a607953b42bb24
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565739"
---
# <a name="export-custom-commands-application-as-a-remote-skill"></a>Esporta l'applicazione comandi personalizzati come abilità remota

In questo articolo si apprenderà come esportare un'applicazione Custom commands come esperienza remota.

## <a name="prerequisites"></a>Prerequisiti
> [!div class="checklist"]
> * [Informazioni sulle competenze di bot Framework](https://aka.ms/speech/cc-skill-overview)
> * [Informazioni sul manifesto delle competenze](https://aka.ms/speech/cc-skill-manifest)
> * [Come richiamare un'abilità da un bot di bot Framework](https://aka.ms/speech/cc-skill-consumer)
> * Applicazione comandi personalizzati esistente. Se non si dispone di un'applicazione comandi personalizzata, provare con- [avvio rapido: creare un assistente vocale usando i comandi personalizzati](quickstart-custom-commands-application.md)

## <a name="custom-commands-as-remote-skills"></a>Comandi personalizzati come competenze remote
* Le competenze di bot Framework sono blocchi per la creazione di competenze di conversazione riutilizzabili per i casi di utilizzo colloquiale che consentono di aggiungere a un bot funzionalità estese in pochi minuti. Per ulteriori informazioni, vedere la pagina relativa alla [capacità di bot Framework](https://microsoft.github.io/botframework-solutions/overview/skills/).
* Un'applicazione di comandi personalizzata può essere esportata come capacità. Questa competenza può quindi essere richiamata sul protocollo Remote Skills da un bot Framework bot.

## <a name="configure-an-application-to-be-exposed-as-a-remote-skill"></a>Configurare un'applicazione da esporre come abilità remota

### <a name="application-level-settings"></a>Impostazioni a livello di applicazione
1. Nel riquadro sinistro selezionare **Impostazioni**  >  **competenze remote**.
1. Impostare le **competenze remote abilitate su attivato** .

### <a name="authentication-to-skills"></a>Autenticazione alle competenze
1. Per abilitare l'autenticazione, aggiungere gli ID applicazione Microsoft dei bot per bot Framework che si vuole configurare per chiamare l'applicazione dei comandi personalizzati.
      > [!div class="mx-imgBorder"]
      > ![Aggiungere un ID MSA a skill](media/custom-commands/skill-add-msa-id.png)

1. Se è stata aggiunta almeno una voce all'elenco, l'autenticazione verrà abilitata per l'applicazione e solo i bot consentiti saranno in grado di chiamare l'applicazione.
> [!TIP]
>  Per disabilitare l'autenticazione, eliminare tutti gli ID applicazione Microsoft dall'elenco dei consentiti. 

 ### <a name="enabledisable-commands-to-be-exposed-as-skills"></a>Abilita/Disabilita i comandi da esporre come competenze

È possibile scegliere i comandi che si desidera esportare sulle competenze remote.

1. Per esporre un comando sulle competenze, selezionare **Abilita un nuovo comando** sotto i **comandi Abilita per le competenze**.
1. Nell'elenco a discesa selezionare il comando che si desidera aggiungere.
1. Selezionare **Salva**.

### <a name="configure-triggering-utterances-for-commands"></a>Configurare espressioni di attivazione per i comandi
I comandi personalizzati usano le frasi di esempio configurate per i comandi in modo da generare le competenze che attivano le espressioni. Queste **espressioni di attivazione** verranno usate per generare il [**manifesto delle competenze**](https://microsoft.github.io/botframework-solutions/skills/handbook/manifest/)della sezione del **Dispatcher** .

In qualità di autore, potrebbe essere necessario controllare quali frasi di **esempio** vengono usate per generare le espressioni di attivazione per le competenze.
1. Per impostazione predefinita, tutti gli **esempi di attivazione** di un comando verranno inclusi nel file manifesto.
1. Se si desidera eliminare in modo esplicito qualsiasi esempio, selezionare **modifica** icona nella sezione comando da **comandi abilitati per le competenze** .
    > [!div class="mx-imgBorder"]
    > ![Modificare un comando abilitato per Skill](media/custom-commands/skill-edit-enabled-command.png)

1. Quindi, nelle frasi di esempio che si desidera omettere, **fare clic con il pulsante destro del mouse** su  >  **Disattiva frase di esempio**.
    > [!div class="mx-imgBorder"]
    > ![Disabilitare esempi](media/custom-commands/skill-disable-example-sentences.png)

1. Selezionare **Salva**.
1. Si noterà che non è possibile aggiungere un nuovo esempio in questa finestra. Se è necessario eseguire questa operazione, passare all'uscita dalla sezione Impostazioni e selezionare il comando pertinente da **comandi** Accordion. A questo punto, è possibile aggiungere la nuova voce nella sezione **frasi di esempio** . Questa modifica viene riflessa automaticamente nel valore di impostazioni competenze remote per il comando.

> [!IMPORTANT]
> Nel caso in cui le frasi di esempio esistenti includano riferimenti a **String >** tipo di dati Catalog, tali frasi verranno automaticamente omesse dall'elenco Skills triggering enunciations. 

## <a name="download-skill-manifest"></a>Scarica il manifesto delle competenze
1. Dopo aver **pubblicato** l'applicazione, è possibile scaricare il file manifesto delle competenze.
1. Usare il manifesto delle competenze per configurare il bot del consumer di bot Framework per chiamare la skill dei comandi personalizzati.
> [!IMPORTANT]
> È necessario **pubblicare** l'applicazione Commands personalizzata per scaricare il manifesto delle competenze. </br>
> Inoltre, se sono state apportate **modifiche** all'applicazione, è necessario pubblicare nuovamente l'applicazione affinché le ultime modifiche vengano riflesse nel file manifesto.

> [!NOTE]
> Se si riscontrano problemi con la pubblicazione dell'applicazione e l'errore indirizza alle competenze che attivano le espressioni, verificare di nuovo la configurazione per i **comandi abilitati per le competenze**. Ogni comando esposto deve avere almeno un enunciato di attivazione valido.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiornare un comando dal client](./how-to-custom-commands-update-command-from-client.md)
