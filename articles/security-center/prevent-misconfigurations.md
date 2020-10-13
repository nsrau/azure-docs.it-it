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
ms.openlocfilehash: 19e9a33350b6a1a67986dc35a372f737e45ab39a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906383"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Impedisci configurazioni errate con le raccomandazioni Imponi/nega

Gli errori di configurazione della sicurezza sono una delle cause principali degli eventi imprevisti di sicurezza. Il Centro sicurezza consente ora di contribuire a *evitare* gli errori di configurazione nelle nuove risorse per quanto riguarda raccomandazioni specifiche. 

Questa funzionalità può contribuire alla protezione dei carichi di lavoro e alla stabilizzazione del punteggio di sicurezza.

È possibile imporre una configurazione di sicurezza, basata su una raccomandazione specifica, in due modi:

- L'effetto **Deny** di Criteri di Azure consente di interrompere la creazione di risorse non integre
- L'opzione **Imponi** consente di sfruttare i vantaggi dell'effetto **DeployIfNotExist** di Criteri di Azure e di correggere automaticamente eventuali risorse non conformi al momento della creazione

Si trova nella parte superiore della pagina dei dettagli della risorsa per le raccomandazioni di sicurezza selezionate (vedere le [raccomandazioni con le opzioni di negazione/applicazione](#recommendations-with-denyenforce-options)).

## <a name="prevent-resource-creation"></a>Impedisci la creazione di risorse

1. Aprire la raccomandazione che le nuove risorse devono soddisfare e selezionare il pulsante **Nega** nella parte superiore della pagina.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="Pagina di raccomandazione con pulsante nega evidenziato":::

    Viene visualizzato il riquadro configurazione che elenca le opzioni di ambito. 

1. Impostare l'ambito selezionando la sottoscrizione o il gruppo di gestione pertinente.

    > [!TIP]
    > È possibile utilizzare i tre puntini alla fine della riga per modificare una singola sottoscrizione oppure utilizzare le caselle di controllo per selezionare più sottoscrizioni o gruppi, quindi selezionare **modifica per nega**.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Pagina di raccomandazione con pulsante nega evidenziato":::


## <a name="enforce-a-secure-configuration"></a>Applicare una configurazione sicura

1. Aprire la raccomandazione per la distribuzione di un modello se le nuove risorse non soddisfano le esigenze e selezionare il pulsante **applica** nella parte superiore della pagina.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="Pagina di raccomandazione con pulsante nega evidenziato":::

    Viene visualizzato il riquadro Configurazione con tutte le opzioni di configurazione dei criteri. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="Pagina di raccomandazione con pulsante nega evidenziato":::

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



