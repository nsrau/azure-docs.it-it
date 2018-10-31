---
title: Certificazione di un modulo IoT Edge | Microsoft Docs
description: Come certificare un modulo IoT Edge per il Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: c37ed908b61ca54957affed3f81526353bc3f53b
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389565"
---
# <a name="the-iot-edge-module-certification-process"></a>Processo di certificazione del modulo IoT Edge

Questo articolo descrive i passaggi e i requisiti necessari per certificare un modulo IoT Edge per la pubblicazione in Azure Marketplace. 

>[!Note]
>Questo documento è temporaneo. Il marketplace del modulo IoT Edge è in fase di creazione in parallelo e questo articolo verrà sostituito con la documentazione pubblica.

## <a name="understanding-an-iot-edge-module"></a>Informazioni sui moduli IoT Edge

Terminologia dei moduli:

-   Un'**immagine del modulo** è un pacchetto che contiene il software che definisce il modulo.

-   Un'**istanza del modulo** è l'unità di calcolo specifica che esegue l'immagine del modulo in un dispositivo di IoT Edge. L'istanza del modulo viene avviata dal runtime di IoT Edge.

I moduli possono includere anche l'SDK dei moduli IoT, che usa la terminologia seguente:

-   Un'**identità del modulo** è un'informazione (che include le credenziali di sicurezza) archiviata nell'hub IoT e associata a ciascuna istanza del modulo.

-   Un **modulo gemello** è un documento JSON archiviato nell'hub IoT, che contiene informazioni sullo stato di un'istanza del modulo, inclusi i metadati, le configurazioni e le condizioni.

-   Gli **SDK** vengono usati per sviluppare moduli personalizzati in più linguaggi, come C\#, C, Python, Java e Node.JS.

## <a name="the-onboarding-process-for-an-iot-edge-module"></a>Processo di onboarding per un modulo IoT Edge

La cattura di schermata seguente mostra il processo tramite il quale un modulo IoT Edge diventa pubblico in Azure Marketplace.

![Processo di certificazione](./media/cloud-partner-portal-iot-edge-module-certification-process/onboarding-process.png)

### <a name="onboarding-step-details"></a>Dettagli dei passaggi di onboarding

-   **Passaggio 1** - I partner inviano le proprie offerte con il tipo di offerta **Modulo IoT Edge** nello strumento del portale Cloud Partner offerto dal team di Azure Marketplace. Per accedere allo strumento del portale Cloud Partner è necessario essere un partner Microsoft ufficiale. Per altre informazioni, consultare la [guida alla pubblicazione](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

-   **Passaggio 2** - Il Marketplace esegue automaticamente i controlli antivirus e antimalware. Durante questa fase il team IoT può eseguire test personalizzati di inserimento automatizzato.

-   **Passaggio 3** - Il modulo viene reso pubblico. Viene presentato nel marketplace e il contenitore può essere recuperato in modo anonimo da un URL. Ad esempio, *marketplace.azurecr.io/module-identifier*.

## <a name="iot-edge-module-certification-criteria"></a>Criteri di certificazione dei moduli IoT Edge

Di seguito sono illustrati i requisiti principali che devono essere soddisfatti affinché un modulo IoT Edge possa essere certificato e pubblicato in Azure Marketplace.

>[!Note]
>Questi requisiti sono soggetti a cambiamenti. In tal caso si riceverà una notifica con un certo anticipo, in modo da avere il tempo di aggiornare i propri contenitori in base ai requisiti aggiornati.

### <a name="technical-requirements"></a>Requisiti tecnici

**Modulo IoT Edge**

-   Solo i contenitori compatibili con Docker sono attualmente supportati come moduli IoT Edge. Il modulo deve essere in esecuzione su [Moby](https://mobyproject.org/). 

    >[!Note]
    >I contenitori Docker dovrebbero funzionare con Moby in quanto Moby è il progetto open source sottostante per Docker.

-   Il partner deve fornire le impostazioni predefinite seguenti: 

    -   Un valore **tag** predefinito (che non può essere vuoto).

    -   Un valore **createOptions** predefinito (che può essere vuoto).

    -   Se si usa l'SDK dei moduli IoT, un valore **twin** predefinito (che può essere vuoto).

    -   Se si usa l'SDK dei moduli IoT, un valore **routes** predefinito (che può essere vuoto).

**Supporto delle piattaforme**

-   Devono essere supportate solo le piattaforme **disponibili a livello generale** in IoT Edge Livello 1 (come indicato in [Supporto di Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)). Ad esempio, attualmente devono essere supportate le combinazioni di sistema operativo e architettura seguenti:

    -   Ubuntu Server 18.04 per x64

    -   Ubuntu Server 16.04 per x64

    -   Raspbian-stretch per arm32 (armhf)

**Dimensionamento dei dispositivi**

-   Qualsiasi dispositivo con le dimensioni equivalenti (CPU/RAM/Archiviazione/GPU/ e così via) di un Raspberry Pi o superiore può essere un dispositivo IoT Edge. Se un modulo funziona solo con specifici vincoli di dimensioni, tali vincoli devono essere specificati nella descrizione del modulo.

**Impostazioni predefinite**

-   Un modulo deve iniziare con i parametri predefiniti specifici per ogni piattaforma supportata (sistema operativo + architettura).

-   Le impostazioni di configurazione devono essere documentate chiaramente (tag, variabili di ambiente, twin, routes). Nell'ambito della presentazione dell'offerta, i partner possono definire una descrizione per il proprio modulo che supporti il markup HTML di base o punti a una pagina Web esterna.

**Controllo delle versioni**

-   I clienti devono essere in grado di scegliere se vogliono aggiornare automaticamente un modulo che proviene dal marketplace oppure usare una versione esatta che hanno testato. I moduli del Marketplace devono seguire lo stesso criterio di controllo delle versioni del runtime IoT Edge. Ad esempio: 

    -   I tag di versioni come "1.0" possono essere aggiornati.

    -   I tag di versioni secondarie, come "1.3.24", non possono essere aggiornati.

**Telemetria**

-   I moduli che usano l'SDK dei moduli IoT devono impostare l'identificatore di modulo univoco assegnato dal marketplace ai fini della telemetria. Questo consente ad Azure Marketplace di identificare il numero di istanze di modulo in esecuzione. Questo identificatore univoco è il nome del contenitore assegnato dal marketplace al momento dell'inserimento. Per impostarlo, usare i metodi degli SDK seguenti:
    - [C\#](https://hub.docker.com/_/mysql/)
    - [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._product_info?view=azure-java-stable)

-   Per i moduli che non usano l'SDK dei moduli IoT sono disponibili informazioni dettagliate meno precise nel portale Cloud Partner, ad esempio il numero di download.

**Sicurezza**

-   I contenitori devono avere l'accesso con meno privilegi possibile all'host. I contenitori "con privilegi" dovrebbero essere estremamente rari.

-   I partner devono garantire la sicurezza del proprio modulo nell'ambito del supporto che forniscono.

**Aggiornamenti**

-   I partner devono mantenere il modulo aggiornato e funzionale. In caso di modifiche di rilievo pianificate per il runtime IoT Edge, riceveranno una notifica in anticipo.

**SDK dei moduli IoT**

-   L'inclusione dell'SDK dei moduli IoT non è un prerequisito per la certificazione,
    tuttavia può offrire un'esperienza utente migliore, ad esempio per il supporto del routing, l'invio di messaggi al cloud e così via. L'SDK dei moduli IoT è necessario per ottenere la telemetria sul numero di istanze di modulo in esecuzione.

**Requisito soggettivo**

-   È necessario soddisfare almeno un caso d'uso di IoT Edge reale. Ad esempio, l'onboarding di un contenitore WordPress dovrebbe essere eseguito solo se un cliente è disposto a usarlo da IoT Edge.

**Requisiti legali (dai criteri AMP)**

-   Il modulo deve essere conforme ai contratti e ai criteri di Microsoft Azure Marketplace. Per altre informazioni, vedere il Contratto per gli editori associato e [Criteri di Partecipazione a Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).

-   Oltre ai contratti e ai criteri di Azure Marketplace, potrebbero essere applicati altri requisiti legali specifici di un tipo di offerta di modulo IoT Edge. Questi requisiti sono attualmente in fase di revisione.

-   Per il modulo devono essere definite le *condizioni per l'utilizzo* e l'*informativa sulla privacy*.

-   Se il modulo usa terze parti, devono essere definite anche le comunicazioni relative alle terze parti.

**Requisiti di supporto (dai criteri AMP)**

-   I partner sono responsabili del supporto dei propri moduli IoT Edge. Devono garantire che le opzioni di supporto fornite nella descrizione del modulo IoT Edge rimangano disponibili e che almeno una delle opzioni di supporto sia sempre disponibile. Per altre informazioni, vedere la sezione 4 del contratto per editori AMP.

**Categorizzazione**

-   Tutti i moduli IoT Edge saranno visualizzati nella categoria **Internet delle cose \> Modulo IoT Edge**. Spetta al partner scegliere la sottocategoria più adatta per il proprio prodotto.
