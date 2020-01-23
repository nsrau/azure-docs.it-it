---
title: Come creare una richiesta di supporto di Azure | Documentazione Microsoft
description: I clienti che necessitano di assistenza possono usare i portale di Azure per trovare soluzioni self-service e per creare e gestire richieste di supporto.
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 01/23/2020
ms.author: kfollis
ms.openlocfilehash: 6218a1089352ce9ee3e2d1d10f3282e64eae8c51
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547660"
---
# <a name="how-to-create-an-azure-support-request"></a>Come creare una richiesta di supporto di Azure

## <a name="overview"></a>Overview

I clienti di Azure possono creare e gestire le richieste di supporto nel [portale di Azure](https://portal.azure.com).

> [!NOTE]
> L'URL del portale di Azure è specifico per il cloud di Azure in cui viene distribuita l'organizzazione.
>
>* Portale di Azure per uso commerciale è: [https://portal.azure.com](https://portal.azure.com)
>* Portale di Azure per la Germania è: [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Portale di Azure per il Stati Uniti Government è: [https://portal.azure.us](https://portal.azure.us)
>
>

In base ai suggerimenti dei clienti, è stata aggiornata l'esperienza di richiesta di supporto per concentrarsi su tre obiettivi principali:

* **Semplificazione**: semplificare la ricerca e la risoluzione dei problemi di invio di una richiesta di supporto.
* **Integrato**: è possibile aprire facilmente una richiesta di supporto quando si sta risolvendo un problema con una risorsa di Azure, senza cambiare contesto.
* **Efficienza**: raccogliere le informazioni chiave necessarie all'agente di supporto per risolvere il problema in modo efficiente.

## <a name="getting-started"></a>Inizia ora

È possibile ottenere **supporto e supporto** nella portale di Azure. È disponibile dal menu portale di Azure, dall'intestazione globale o dal menu delle risorse per un servizio. Prima di poter archiviare una richiesta di supporto, è necessario disporre delle autorizzazioni appropriate.

### <a name="role-based-access-control"></a>Controllo degli accessi basato sul ruolo

Per creare una richiesta di supporto, è necessario essere un amministratore o essere assegnati al ruolo di [collaboratore della richiesta di supporto](../../role-based-access-control/built-in-roles.md#support-request-contributor) .

### <a name="go-to-help--support-from-the-global-header"></a>Passare a guida e supporto tecnico dall'intestazione globale

Per avviare una richiesta di supporto da qualsiasi punto del portale di Azure:

1. Selezionare l’icona **?** nell'intestazione globale. Selezionare quindi **Guida e supporto**.

   ![Guida e supporto](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. Selezionare **Nuova richiesta di supporto**. Seguire le istruzioni per fornire informazioni sul problema. Verranno suggerite alcune possibili soluzioni, verranno fornite informazioni dettagliate sul problema e verranno fornite informazioni su come inviare e tenere traccia della richiesta di supporto.

   ![Nuova richiesta di supporto](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Passare a guida e supporto da un menu delle risorse

Per avviare una richiesta di supporto nel contesto della risorsa, è attualmente in uso:

1. Nel menu delle risorse, nella sezione **supporto e risoluzione dei problemi** , selezionare **nuova richiesta di supporto**.

   ![Nel contesto](./media/how-to-create-azure-support-request/incontext2lower.png)

2. Seguire le istruzioni per fornire informazioni sul problema che si sta verificando. Quando si avvia il processo di richiesta di supporto dalla risorsa, alcune opzioni sono pre-selezionate.

## <a name="create-a-support-request"></a>Creare una richiesta di supporto

Verranno illustrati alcuni passaggi per raccogliere informazioni sul problema e risolverlo. Ogni passaggio è descritto nelle sezioni riportate di seguito.

### <a name="basics"></a>Nozioni di base

Il primo passaggio del processo di richiesta di supporto consiste nella raccolta di informazioni di base sul problema e sul piano di supporto.

Nella scheda informazioni di **base** della **nuova richiesta di supporto**, usare i selettori per iniziare a indicare il problema. Per prima cosa, è necessario identificare alcune categorie generali per il tipo di problema e scegliere la sottoscrizione correlata. Selezionare il servizio, ad esempio **macchina virtuale che esegue Windows**. Selezionare la risorsa, ad esempio il nome della macchina virtuale. Descrivere il problema con le proprie parole, quindi **selezionare il tipo di problema** per ottenere informazioni più specifiche.

![Pannello Nozioni di base](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> Azure offre un supporto illimitato per la gestione delle sottoscrizioni, che include fatturazione, rettifiche delle quote e trasferimenti di account. Per rivolgersi al supporto tecnico è necessario un piano di supporto. [Altre informazioni sui piani di supporto](https://azure.microsoft.com/support/plans).
>
>

### <a name="solutions"></a>Soluzioni

Dopo aver raccolto le informazioni di base, si mostreranno le soluzioni da provare autonomamente. In alcuni casi, è anche possibile eseguire una diagnostica rapida. Le soluzioni vengono scritte da tecnici di Azure e risolvono i problemi più comuni.

### <a name="details"></a>Dettagli

Successivamente, vengono raccolti ulteriori dettagli sul problema. Fornire informazioni dettagliate e dettagliate in questo passaggio consente di instradare la richiesta di supporto all'agente corretto.

Se possibile, indicare quando il problema è stato avviato ed eventuali passaggi per riprodurlo. È possibile caricare un file, ad esempio un file di log o un output di diagnostica.

Una volta fornite tutte le informazioni sul problema, scegliere come ottenere supporto. Nella sezione **metodo di supporto** di **Dettagli**Selezionare la gravità dell'effetto. Fornire il metodo di contatto preferito, un momento opportuno per contattare l'utente e la lingua di supporto.

Successivamente, completare la sezione **Contact info (informazioni di contatto** ) per scoprire come contattare l'utente.

### <a name="review--create"></a>Rivedi e crea

Completare tutte le informazioni necessarie in ogni scheda, quindi selezionare **Verifica + crea**. Verificare i dettagli da inviare per il supporto. Tornare a qualsiasi scheda per apportare una modifica, se necessario. Al termine della richiesta di supporto, selezionare **Crea**.

Un agente di supporto contatterà l'utente utilizzando il metodo indicato. Per informazioni sul tempo di risposta iniziale, vedere [ambito del supporto e](https://azure.microsoft.com/support/plans/response/)velocità di risposta.

## <a name="all-support-requests"></a>Tutte le richieste di supporto

È possibile visualizzare i dettagli e lo stato delle richieste di supporto passando a **Guida e supporto** >  **tutte le richieste di supporto**.

![Tutte le richieste di supporto](./media/how-to-create-azure-support-request/allrequestslower.png)

In questa pagina è possibile filtrare le richieste di supporto in base alla **sottoscrizione**, alla data di **creazione** (UTC) e **allo stato**. Inoltre, in questa pagina si possono ordinare e cercare le richieste di supporto.

Consente di selezionare una richiesta di supporto per visualizzare i dettagli, inclusa la gravità e il tempo previsto per la risposta di un agente di supporto.

Se si desidera modificare il livello di gravità della richiesta, selezionare **effetto business**. Consente di scegliere da un elenco di livelli di gravità da assegnare.

> [!NOTE]
> Il livello di gravità massimo dipende dal piano di supporto. [Altre informazioni sui piani di supporto](https://azure.microsoft.com/support/plans).
>
>
Per ulteriori informazioni sulle opzioni di supporto self-help in Azure, guardare questo video:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>Passaggi successivi

* [Inviare commenti e suggerimenti](https://feedback.azure.com/forums/266794-support-feedback)
* Seguici su [Twitter](https://twitter.com/azuresupport)
* Ottenere assistenza dai peer nei [Forum MSDN](https://social.msdn.microsoft.com/Forums/azure)
* Per altre informazioni, vedere [domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq)
