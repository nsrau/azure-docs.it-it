---
title: Come creare una richiesta di supporto di Azure | Documentazione Microsoft
description: I clienti che necessitano di assistenza possono usare il portale di Azure per trovare soluzioni self-service e creare e gestire le richieste di supporto.
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 01/23/2020
ms.author: kfollis
ms.openlocfilehash: 28a29bbb94f63657a69e873bb8f969e96ee92c76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248489"
---
# <a name="how-to-create-an-azure-support-request"></a>Come creare una richiesta di supporto in Azure

## <a name="overview"></a>Panoramica

Azure consente di creare e gestire le richieste di supporto, note anche come ticket di supporto. È possibile creare e gestire le richieste nel portale di [Azure](https://portal.azure.com), descritto in questo articolo. È anche possibile creare e gestire le richieste a livello di codice, usando l'API REST del ticket di supporto di Azure.You can also create and manage requests programmatically, using [the Azure support ticket REST API](/rest/api/support).

> [!NOTE]
> L'URL del portale di Azure è specifico del cloud di Azure in cui viene distribuita l'organizzazione.
>
>* Il portale di Azure per uso commerciale è:Azure portal for commercial use is:[https://portal.azure.com](https://portal.azure.com)
>* Il portale di Azure per la Germania è:Azure portal for Germany is:[https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Il portale di Azure per enti pubblici degli Stati Uniti è:Azure portal for the United States government is:[https://portal.azure.us](https://portal.azure.us)
>
>

In base al feedback dei clienti, abbiamo aggiornato l'esperienza di richiesta di supporto per concentrarci su tre obiettivi principali:

* **Semplificata**: Semplifica l'individuazione e la risoluzione dei problemi di supporto e semplifica il modo in cui invii una richiesta di supporto.
* **Integrato**: È possibile aprire facilmente una richiesta di supporto quando si risolve un problema con una risorsa di Azure, senza cambiare contesto.
* **Efficiente**: Raccogliere le informazioni chiave necessarie all'agente di supporto per risolvere in modo efficiente il problema.

## <a name="getting-started"></a>Introduzione

È possibile accedere alla **Guida e al supporto** nel portale di Azure.You can get to Help - support in the Azure portal. È disponibile dal menu del portale di Azure, dall'intestazione globale o dal menu delle risorse per un servizio. Prima di poter presentare una richiesta di supporto, è necessario disporre delle autorizzazioni appropriate.

### <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Per creare una richiesta di supporto, è necessario essere un amministratore o essere assegnati al ruolo [Contributore richiesta](../../role-based-access-control/built-in-roles.md#support-request-contributor) di supporto a livello di sottoscrizione.

### <a name="go-to-help--support-from-the-global-header"></a>Vai alla Guida e al supporto dell'intestazione globale

Per avviare una richiesta di supporto da qualsiasi punto del portale di Azure:To start a support request from anywhere in the Azure portal:

1. Selezionare il **?** nell'intestazione globale. Quindi selezionare **Guida e supporto**.

   ![Guida e supporto](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. Selezionare **Nuova richiesta di supporto**. Segui le istruzioni per fornirci informazioni sul tuo problema. Ti suggeriamo alcune possibili soluzioni, raccogliere dettagli sul problema e aiutarti a inviare e tenere traccia della richiesta di supporto.

   ![Nuova richiesta di supporto](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Vai alla Guida e al supporto da un menu delle risorse

Per avviare una richiesta di supporto nel contesto della risorsa, si sta attualmente lavorando con:

1. Nella sezione **Supporto e risoluzione dei problemi** del menu delle risorse selezionare Nuova richiesta di **supporto.**

   ![Nel contesto](./media/how-to-create-azure-support-request/incontext2lower.png)

2. Segui le istruzioni per fornirci informazioni sul problema che stai riscontrando. Quando si avvia il processo di richiesta di supporto dalla risorsa, alcune opzioni sono preselezionate dall'utente.

## <a name="create-a-support-request"></a>Creare una richiesta di supporto

Ti guideremo attraverso alcuni passaggi per raccogliere informazioni sul tuo problema e aiutarti a risolverlo. Ogni passaggio è descritto nelle sezioni seguenti.

### <a name="basics"></a>Nozioni di base

Il primo passaggio del processo di richiesta di supporto consiste nella raccolta di informazioni di base sul problema e sul piano di supporto.

Nella scheda **Nozioni di base** della richiesta di **supporto**, utilizzare i selettori per iniziare a comunicare il problema. In primo luogo, si identificheranno alcune categorie generali per il tipo di problema e si sceglie la sottoscrizione correlata. Selezionare il servizio, ad esempio **Macchina virtuale che esegue Windows**. Selezionare la risorsa, ad esempio il nome della macchina virtuale. Descrivere il problema con le proprie parole, quindi **selezionare il tipo di problema** per ottenere più specifico.

![Pannello Nozioni di base](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> Azure offre un supporto illimitato per la gestione delle sottoscrizioni, che include fatturazione, aggiustamenti delle quote e trasferimenti di account. Per rivolgersi al supporto tecnico è necessario un piano di supporto. [Altre informazioni sui piani di supporto](https://azure.microsoft.com/support/plans).
>
>

### <a name="solutions"></a>Soluzioni

Dopo aver raccolto le informazioni di base, vi mostriamo le soluzioni da provare da soli. In alcuni casi, potremmo anche eseguire una diagnosi rapida. Le soluzioni sono scritte dai tecnici di Azure e risolvono i problemi più comuni.

### <a name="details"></a>Dettagli

Successivamente, raccogliamo ulteriori dettagli sul problema. Fornire informazioni dettagliate e dettagliate in questo passaggio ci aiuta a indirizzare la tua richiesta di supporto all'agente giusto.

Se possibile, comunicaci quando è iniziato il problema e i passaggi per riprodurlo. È possibile caricare un file, ad esempio un file di log o un output dalla diagnostica.

Dopo aver ottenuto tutte le informazioni sul problema, scegliere come ottenere assistenza. Nella sezione Metodo di **supporto** di **Dettagli**selezionare la gravità dell'impatto. Fornisci il tuo metodo di contatto preferito, un buon momento per contattarti e il tuo linguaggio di supporto.

Successivamente, completa la sezione Informazioni di **contatto** in modo che sappiamo come contattarti.

### <a name="review--create"></a>Rivedi e crea

Completare tutte le informazioni richieste in ogni scheda, quindi selezionare **Rivedi .** Controlla i dettagli che invierai al supporto. Tornare a qualsiasi scheda per apportare una modifica, se necessario. Quando si è certi che la richiesta di supporto sia stata completata, selezionare **Crea**.

Un agente di supporto ti contatterà utilizzando il metodo indicato. Per informazioni sul tempo di risposta iniziale, vedere [Ambito e velocità di risposta](https://azure.microsoft.com/support/plans/response/)del supporto .

## <a name="all-support-requests"></a>Tutte le richieste di supporto

È possibile visualizzare i dettagli e lo stato delle richieste di supporto accedendo a **Guida e supporto** >  **Tutte le richieste**di supporto .

![Tutte le richieste di supporto](./media/how-to-create-azure-support-request/allrequestslower.png)

In questa pagina è possibile filtrare le richieste di supporto in **base a Sottoscrizione**, Data **creazione** (UTC) e **Stato**. Inoltre, in questa pagina si possono ordinare e cercare le richieste di supporto.

Selezionare una richiesta di supporto per visualizzare i dettagli, inclusa la gravità e il tempo previsto necessario per la risposta di un agente di supporto.

Se si desidera modificare la gravità della richiesta, selezionare **Impatto aziendale**. Scegliere da un elenco di livelli di gravità da assegnare.

> [!NOTE]
> Il livello di gravità massimo dipende dal piano di supporto. [Altre informazioni sui piani di supporto](https://azure.microsoft.com/support/plans).
>
>
Per altre informazioni sulle opzioni di supporto self-help in Azure, guardare questo video:To learn more about self-help support options in Azure, watch this video:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>Passaggi successivi

* [Inviaci il tuo feedback e suggerimenti](https://feedback.azure.com/forums/266794-support-feedback)
* Interagisci con noi su [Twitter](https://twitter.com/azuresupport)
* Ottenere assistenza dai colleghi nei [forum MSDN](https://social.msdn.microsoft.com/Forums/azure)
* Altre informazioni nelle [domande frequenti sul supporto di AzureLearn](https://azure.microsoft.com/support/faq) more in Azure Support FAQ
