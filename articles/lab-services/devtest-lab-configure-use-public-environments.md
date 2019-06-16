---
title: Configurare e usare ambienti pubblici in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come configurare e usare ambienti pubblici in Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2018
ms.author: spelluru
ms.openlocfilehash: 2cd6998c7ac11638ead67fde384bdf4599692781
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706034"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Configurare e usare ambienti pubblici in Azure DevTest Labs
Azure DevTest Labs include un [repository pubblico di modelli di Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) che consente di creare ambienti senza connettersi a un'origine GitHub esterna. Questo repository include i modelli usati di frequente, ad esempio App Web di Azure, Cluster di Service Fabric e l'ambiente di sviluppo della Farm di SharePoint. Questa funzionalità è simile al repository pubblico di artefatti incluso in ogni lab creato. Il repository di ambienti consente di iniziare rapidamente usando modelli di ambiente predefiniti con parametri di input minimi per garantire un'agevole esperienza iniziale con le risorse PaaS all'interno dei lab. 

## <a name="configuring-public-environments"></a>Configurazione di ambienti pubblici
Come proprietario di lab, è possibile abilitare il repository di ambienti pubblici durante la creazione del lab. Per abilitare ambienti pubblici per il lab, selezionare **Abilita** per il campo **Ambienti pubblici** durante la creazione del lab. 

![Abilitare ambienti pubblici per un nuovo lab](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


Per i lab esistenti, il repository di ambienti pubblici non è abilitato. Per usare i modelli nel repository, abilitarlo manualmente. Anche per i lab creati usando i modelli di Resource Manager, il repository è disabilitato per impostazione predefinita.

È possibile abilitare o disabilitare gli ambienti pubblici per il lab e rendere disponibili agli utenti del lab solo specifici ambienti tramite i passaggi seguenti: 

1. Selezionare **Configurazione e criteri** per il lab. 
2. Nella sezione **BASI DELLE MACCHINE VIRTUALI** selezionare **Ambienti pubblici**.
3. Per abilitare gli ambienti pubblici per il lab, selezionare **Sì**. In caso contrario, selezionare **No**. 
4. Se gli ambienti pubblici sono stati abilitati, tutti gli ambienti nel repository sono abilitati per impostazione predefinita. È possibile deselezionare un ambiente per renderlo non disponibile per gli utenti del lab. 

![Pagina degli ambienti pubblici](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Usare i modelli di ambiente come utente di lab
Come utente di lab, è possibile creare un nuovo ambiente dall'elenco abilitato di modelli di ambiente semplicemente selezionando **Aggiungi** dalla barra degli strumenti nella pagina del lab. L'elenco delle basi include i modelli di ambienti pubblici abilitati dall'amministratore del lab all'inizio dell'elenco.

![Modelli di ambienti pubblici](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>Passaggi successivi
Questo è un repository open source nel quale è possibile aggiungere i modelli di Resource Manager più utili e usati di frequente. Per contribuirvi, è sufficiente inviare una richiesta pull al repository.  
