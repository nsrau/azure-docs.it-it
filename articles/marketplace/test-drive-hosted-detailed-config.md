---
title: Configurazione dettagliata per una test drive ospitata in Azure Marketplace
description: Spiegazione dei passaggi di configurazione per un test drive ospitato nel Marketplace commerciale
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 11/06/2020
ms.openlocfilehash: 88779f67a2fa9b18f0177a1459b32c672343bb57
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462942"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>Configurazione dettagliata per le unità di test ospitate

Questo articolo descrive come configurare un test drive ospitato per Dynamics 365 per Customer Engagement o Dynamics 365 per le operazioni.

## <a name="configure-for-dynamics-365-customer-engagement"></a>Configurare per Dynamics 365 Customer Engagement

1. Accedere al [Centro per i partner](https://partner.microsoft.com/).
2. Se non è possibile accedere al collegamento precedente, è necessario inviare una richiesta [qui](https://appsource.microsoft.com/partners/list-an-app) per pubblicare l'applicazione. Una volta esaminata la richiesta, viene concesso l'accesso per avviare il processo di pubblicazione.
3. Trova un'offerta **dinamica 365 per il coinvolgimento dei clienti** o crea una nuova offerta **di Dynamics 365 per i clienti** .
4. Selezionare la casella di controllo **Abilita una test drive** e selezionare un **tipo di test drive** (vedere il punto elenco sotto), quindi selezionare **Salva**.

    [![Selezione della casella di controllo ' Abilita un test drive '.](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - **Tipo di test drive** : scegliere **Microsoft Hosted (Dynamics 365 per Customer Engagement & PowerApps)**. Ciò indica che Microsoft ospiterà e gestirà il servizio che esegue il test drive provisioning e deprovisioning dell'utente.

5. Concedere Microsoft AppSource autorizzazione per il provisioning e il deprovisioning di test drive utenti nel tenant seguendo [queste istruzioni](./test-drive-azure-subscription-setup.md). In questo passaggio si genereranno i valori **app Azure ad ID** e **app Azure ad chiave** indicati di seguito.
6. Completare questi campi nella pagina di **configurazione tecnica test drive** .

    [![Pagina di configurazione tecnica test drive.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - Numero **massimo di unità di test simultanee** : numero di utenti simultanei che possono avere un test drive attivo in esecuzione nello stesso momento. Ogni utente utilizzerà una licenza Dynamics mentre la relativa test drive è attiva, quindi assicurarsi di disporre di almeno questo numero di licenze Dynamics disponibili per gli utenti test drive. Si consiglia da 3 a 5.
    - **Durata test drive** : il numero di ore per cui il test drive dell'utente sarà attivo. Al termine dell'intervallo di tempo, verrà eseguito il deprovisioning dell'utente dal tenant. Sono consigliate 2-24 ore a seconda della complessità dell'app. L'utente può sempre richiedere un altro test drive se esaurisce il tempo e desidera accedere nuovamente al test drive.
    - **URL istanza** : l'URL a cui verrà inviato l'utente test drive all'avvio del test drive. Si tratta in genere dell'URL dell'istanza di Dynamics 365 in cui è installata l'app e i dati di esempio. Valore di esempio: `https://testdrive.crm.dynamics.com` .
    - **URL dell'API Web dell'istanza** : URL dell'API Web per l'istanza di Dynamics 365. Recuperare questo valore accedendo all'istanza di Microsoft Dynamics 365 e passando a **impostazione**  >  **personalizzazione**  >  **risorse per sviluppatori**  >  **istanza Web API** e copiare l'indirizzo (URL). Valore di esempio

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="Esempio di API Web dell'istanza.":::

    - **Nome ruolo** : il nome del ruolo di sicurezza Dynamics 365 personalizzato creato per test drive oppure è possibile usare un ruolo esistente. Un nuovo ruolo deve avere i privilegi minimi necessari aggiunti al ruolo per accedere a un'istanza di engagement del cliente. Vedere i [privilegi minimi necessari per accedere a Microsoft Dynamics 365](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365). Si tratta del ruolo che verrà assegnato agli utenti durante la loro test drive. Valore di esempio: `testdriverole` .
    
        > [!IMPORTANT]
        > Verificare che il controllo del gruppo di sicurezza non sia stato aggiunto. Ciò consente all'utente di essere sincronizzato con l'istanza di engagement del cliente.

    - **Azure Active Directory ID tenant** : ID del tenant di Azure per l'istanza di Dynamics 365. Per recuperare questo valore, accedere a portale di Azure e passare a **Azure Active Directory**  >  **proprietà** e copiare l'ID directory. Valore di esempio: 172f988bf-86f1-41AF-91ab-2d7cd01112341.
    - **Azure Active Directory nome del tenant** : il nome del tenant di Azure per l'istanza di Dynamics 365. Usare il formato `<tenantname>.onmicrosoft.com`. Valore di esempio: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory ID applicazione** : l'ID dell'app Azure Active Directory (ad) creata nel passaggio 5. Valore di esempio: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory segreto client dell'applicazione** : segreto per l'app Azure ad creata nel passaggio 5. Valore di esempio: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .

7. Specificare i dettagli dell'inserzione nel Marketplace. Selezionare **lingua** per visualizzare altri campi obbligatori.

    [![Pagina dei dettagli dell'inserzione nel Marketplace.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Descrizione** : panoramica del test drive. Questo testo verrà visualizzato all'utente durante il provisioning del test drive. Questo campo supporta l'HTML se si desidera fornire il contenuto formattato.
    - **Manuale dell'utente** : un manuale dell'utente PDF che aiuta test drive utenti a comprendere come usare l'app.
    - **Video dimostrativo di test drive** : video che mostra l'app (facoltativo).

## <a name="configure-for-dynamics-365-operations"></a>Configurare per le operazioni di Dynamics 365

2. Se non è possibile accedere al collegamento precedente, è necessario inviare una richiesta [qui](https://appsource.microsoft.com/partners/list-an-app) per pubblicare l'applicazione. Una volta esaminata la richiesta, viene concesso l'accesso per avviare il processo di pubblicazione.
3. Trovare un'offerta **di dynamics 365 per le operazioni** esistente o creare una nuova offerta **di Dynamics 365 per le operazioni** .
4. Selezionare la casella di controllo **Abilita una test drive** e selezionare un **tipo di test drive** (vedere il punto elenco sotto), quindi selezionare **Salva**.

    [![Selezionare la casella di controllo "Abilita un test drive".](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **Tipo di test drive** : scegliere l'opzione **Dynamics 365 for Operations** . Ciò significa che Microsoft ospiterà e gestirà il servizio che esegue il test drive provisioning e deprovisioning dell'utente.

5. Concedere Microsoft AppSource autorizzazione per il provisioning e il deprovisioning di test drive utenti nel tenant seguendo [queste istruzioni](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md). In questo passaggio si genereranno i valori **app Azure ad ID** e **app Azure ad chiave** indicati di seguito.
6. Completare questi campi nella pagina di **configurazione tecnica test drive** .

    [![Pagina di configurazione tecnica del Marketplace.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - Numero **massimo di unità di test simultanee** : numero di utenti simultanei che possono avere un test drive attivo in esecuzione nello stesso momento. Ogni utente utilizzerà una licenza Dynamics mentre la relativa test drive è attiva, quindi assicurarsi di disporre di almeno questo numero di licenze Dynamics disponibili per gli utenti test drive. Si consiglia da 3 a 5.
    - **Durata test drive** : il numero di ore per cui il test drive dell'utente sarà attivo. Al termine dell'intervallo di tempo, verrà eseguito il deprovisioning dell'utente dal tenant. Sono consigliate 2-24 ore a seconda della complessità dell'app. L'utente può sempre richiedere un altro test drive se esaurisce il tempo e desidera accedere nuovamente al test drive.
    - **URL istanza** : l'URL a cui verrà inviato l'utente test drive all'avvio del test drive. Si tratta in genere dell'URL dell'istanza di Dynamics 365 in cui è installata l'app e i dati di esempio. Valore di esempio: `https://testdrive.crm.dynamics.com` .
    - **Azure Active Directory ID tenant** : ID del tenant di Azure per l'istanza di Dynamics 365. Per recuperare questo valore, accedere a portale di Azure e passare a **Azure Active Directory**  >  **proprietà** e copiare l'ID directory. Valore di esempio: 172f988bf-86f1-41AF-91ab-2d7cd01112341.
    - **Azure Active Directory nome del tenant** : il nome del tenant di Azure per l'istanza di Dynamics 365. Usare il formato `<tenantname>.onmicrosoft.com`. Valore di esempio: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory ID applicazione** : l'ID dell'app Azure Active Directory (ad) creata nel passaggio 5. Valore di esempio: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory segreto client dell'applicazione** : segreto per l'app Azure ad creata nel passaggio 5. Valore di esempio: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .
    - **Entità legale di valutazione** : fornire un'entità legale per assegnare un utente della versione di valutazione. È possibile crearne uno nuovo in [creare o modificare un'entità legale](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity).
    - **Nome ruolo** : il nome AOT (albero degli oggetti applicazione) del ruolo di sicurezza di Dynamics 365 personalizzato creato per test drive. Si tratta del ruolo che verrà assegnato agli utenti durante la loro test drive.

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="Pagina di configurazione della sicurezza.":::

7. Specificare i dettagli dell'inserzione nel Marketplace. Selezionare **lingua** per visualizzare altri campi obbligatori.

    [![Pagina dei dettagli dell'inserzione nel Marketplace.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Descrizione** : panoramica del test drive. Questo testo verrà visualizzato all'utente durante il provisioning del test drive. Questo campo supporta l'HTML se si desidera fornire il contenuto formattato.
    - **Manuale dell'utente** : un manuale dell'utente PDF che aiuta test drive utenti a comprendere come usare l'app.
    - **Video dimostrativo di test drive** : video che mostra l'app (facoltativo).

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->