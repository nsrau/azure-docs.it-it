---
title: Come impedire configurazioni non configurate con il Centro sicurezza di Azure
description: Informazioni su come usare le opzioni ' enforce ' è Deny ' del Centro sicurezza nelle pagine dei dettagli delle raccomandazioni
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: ceafbe1fd1682fc5e92ab3048ed09866fb9ebbdf
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89570490"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Impedisci configurazioni errate con le raccomandazioni Imponi/nega

Le configurazioni non configurate per la sicurezza sono una delle principali cause degli incidenti di sicurezza. Il Centro sicurezza offre ora la possibilità di *evitare* configurazioni errate delle nuove risorse per quanto concerne raccomandazioni specifiche. 

Questa funzionalità può aiutare a proteggere i carichi di lavoro e stabilizzare il Punteggio sicuro.

L'applicazione di una configurazione sicura, basata su una raccomandazione specifica, viene offerta in due modalità:

- Usando l'effetto di **negazione** di criteri di Azure, è possibile arrestare la creazione di risorse non integre.

- Con l'opzione **Imponi** è possibile sfruttare l'effetto **DeployIfNotExist** di criteri di Azure e correggere automaticamente le risorse non conformi al momento della creazione
 
Questa opzione è disponibile per le raccomandazioni di sicurezza selezionate e si trova nella parte superiore della pagina dei dettagli delle risorse.

## <a name="prevent-resource-creation"></a>Impedisci la creazione di risorse

1. Aprire la raccomandazione che le nuove risorse devono soddisfare e selezionare il pulsante **Nega** nella parte superiore della pagina.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="Pagina di raccomandazione con pulsante nega evidenziato":::

    Viene visualizzato il riquadro configurazione che elenca le opzioni di ambito. 

1. Impostare l'ambito selezionando la sottoscrizione o il gruppo di gestione pertinente.

    > [!TIP]
    > È possibile utilizzare i tre puntini alla fine della riga per modificare una singola sottoscrizione oppure utilizzare le caselle di controllo per selezionare più sottoscrizioni o gruppi, quindi selezionare **modifica per nega**.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Impostazione dell'ambito per Deny di criteri di Azure":::


## <a name="enforce-a-secure-configuration"></a>Applicare una configurazione sicura

1. Aprire la raccomandazione per la distribuzione di un modello se le nuove risorse non soddisfano le esigenze e selezionare il pulsante **applica** nella parte superiore della pagina.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="Pagina di raccomandazione con il pulsante Applica evidenziato":::

    Viene visualizzato il riquadro Configurazione con tutte le opzioni di configurazione dei criteri. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="Imponi opzioni di configurazione":::

1. Impostare l'ambito, il nome dell'assegnazione e altre opzioni rilevanti.

1. Selezionare **Rivedi e crea**.

## <a name="recommendations-with-denyenforce-options"></a>Raccomandazioni con opzioni Deny/enforce

Questi consigli possono essere usati con l'opzione **Deny** :

- È consigliabile eseguire la migrazione delle macchine virtuali alle nuove risorse di Azure Resource Manager
- È consigliabile eseguire la migrazione degli account di archiviazione alle nuove risorse di Azure Resource Manager
- È consigliabile rimuovere tutte le regole di autorizzazione ad eccezione di RootManageSharedAccessKey dallo spazio dei nomi di Hub eventi
- È consigliabile rimuovere tutte le regole di autorizzazione ad eccezione di RootManageSharedAccessKey dallo spazio dei nomi del bus di servizio
- Il trasferimento sicuro negli account di archiviazione deve essere abilitato
- Devono essere abilitate solo connessioni sicure alla Cache Redis
- Le variabili dell'account di automazione devono essere crittografate
- I cluster di Service Fabric deve usare solo Azure Active Directory per l'autenticazione client
- La proprietà ClusterProtectionLevel dei cluster di Service Fabric dovrebbe essere impostata su EncryptAndSign
- Controlla l'accesso di rete senza restrizioni agli account di archiviazione


Questi consigli possono essere usati con l'opzione **Imponi** :

- È consigliabile abilitare i log di diagnostica in App per la logica
- È consigliabile abilitare i log di diagnostica Data Lake Analytics
- È consigliabile abilitare i log di diagnostica nell'hub IoT
- È consigliabile abilitare i log di diagnostica negli account Batch
- I log di diagnostica devono essere abilitati in Analisi di flusso di Azure
- È consigliabile abilitare i log di diagnostica nel bus di servizio
- È consigliabile abilitare i log di diagnostica nei servizi di ricerca
- È consigliabile abilitare i log di diagnostica in Hub eventi
- È consigliabile abilitare i log di diagnostica nei set di scalabilità di macchine virtuali
- I log di diagnostica in Key Vault devono essere abilitati
- È consigliabile abilitare il controllo in SQL Server
- Sicurezza dei dati avanzata deve essere abilitata nei server SQL



