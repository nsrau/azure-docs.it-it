---
title: Connetti il tuo account GCP al centro sicurezza di Azure
description: Monitoraggio delle risorse di GCP dal centro sicurezza di Azure
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: c4020bc5e96ff35fa2d04a872216e43abbba5323
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328310"
---
#  <a name="connect-your-gcp-accounts-to-azure-security-center"></a>Connettere gli account GCP al centro sicurezza di Azure

Con i carichi di lavoro cloud comunemente distribuiti su più piattaforme cloud, i servizi di sicurezza cloud devono eseguire la stessa operazione.

Il Centro sicurezza di Azure protegge i carichi di lavoro in Azure, Amazon Web Services (AWS) e Google Cloud Platform (GCP).

Onboarding dell'account GCP nel centro sicurezza, integra il comando di sicurezza di GCP e il Centro sicurezza di Azure. Il Centro sicurezza offre quindi visibilità e protezione in entrambi gli ambienti cloud per offrire:

- Rilevamento di configurazioni non configurate per la sicurezza
- Una singola visualizzazione che mostra le raccomandazioni del Centro sicurezza e i risultati di GCP Security Command Center
- Integrazione delle risorse GCP nei calcoli di Punteggio sicuro del Centro sicurezza
- Integrazione di GCP Security Command Center raccomandazioni basate sullo standard CIS nel dashboard conformità normativa del Centro sicurezza

Nella schermata seguente è possibile visualizzare i progetti GCP visualizzati nel Dashboard panoramica del Centro sicurezza.

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="3 progetti GCP elencati nel dashboard di panoramica del Centro sicurezza" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::


## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato versione:|Anteprima|
|Prezzi|Richiede [Azure Defender per i server](defender-for-servers-introduction.md)|
|Ruoli e autorizzazioni necessari:|**Proprietario** o **collaboratore** nella sottoscrizione di Azure pertinente|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![No](./media/icons/no-icon.png) Nazionale/sovrano (US Gov, Cina gov, altri gov)|
|||

## <a name="connect-your-gcp-account"></a>Connettere l'account GCP

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>Passaggio 1. Configurare GCP Security Command Center con analisi dell'integrità della sicurezza

Per tutti i progetti GCP nell'organizzazione, è necessario anche:

1. Configurare **GCP Security Command Center** seguendo [queste istruzioni dalla documentazione di GCP](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup).
1. Abilitare l' **analisi dell'integrità della sicurezza** seguendo [queste istruzioni della documentazione di GCP](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics).
1. Verificare che sia presente un flusso di dati al centro comandi di sicurezza.

Le istruzioni per la connessione dell'ambiente GCP per la configurazione della sicurezza seguono le raccomandazioni di Google per l'utilizzo delle raccomandazioni relative alla configurazione della sicurezza. L'integrazione si avvale di Google Security Command Center e utilizzerà risorse aggiuntive che potrebbero influito sulla fatturazione.

Quando si Abilita per la prima volta l'analisi dell'integrità della sicurezza, potrebbero essere necessarie diverse ore affinché i dati siano disponibili.


### <a name="step-2-enable-gcp-security-command-center-api"></a>Passaggio 2. Abilita API del centro comandi per la sicurezza GCP

1. Dalla **libreria API della console Cloud**di Google, selezionare il progetto che si vuole connettere al centro sicurezza di Azure.
1. Nella libreria API trovare e selezionare l' **API del centro comandi di sicurezza**.
1. Nella pagina dell'API selezionare **Abilita**.

Altre informazioni sull' [API del centro comandi di sicurezza](https://cloud.google.com/security-command-center/docs/reference/rest/).


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>Passaggio 3. Creazione di un account di servizio dedicato per l'integrazione della configurazione di sicurezza

1. Nella **console di GCP**selezionare il progetto che si desidera connettere al centro sicurezza.
1. Nel **menu di navigazione**, in **IAM &** opzioni di amministrazione, selezionare **account del servizio**.
1. Selezionare **Crea account del servizio**.
1. Immettere un nome account e selezionare **Crea**.
1. Specificare il **ruolo** come **Visualizzatore di amministrazione del Centro sicurezza**e selezionare **continua**.
1. La sezione **concedere agli utenti l'accesso a questo account del servizio** è facoltativa. Selezionare **Fine**.
1. Copiare il **valore di posta elettronica** dell'account del servizio creato e salvarlo per usarlo in seguito.
1. Nel **menu di navigazione**, in **IAM &** opzioni di amministrazione, selezionare **IAM**
    1. Passa a livello di organizzazione.
    1. Selezionare **Aggiungi**.
    1. Nel campo **nuovi membri** incollare il valore di **posta elettronica** copiato in precedenza.
    1. Specificare il ruolo come **Visualizzatore di amministrazione del Centro sicurezza** e quindi selezionare Salva.
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="Impostazione delle autorizzazioni di GCP rilevanti":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>Passaggio 4. Creare una chiave privata per l'account del servizio dedicato
1. Passare al livello del progetto.
1. Nel **menu di navigazione**, in **IAM &** opzioni di amministrazione, selezionare **account del servizio**.
1. Aprire l'account di servizio dedicato e scegliere modifica.
1. Nella sezione **chiavi** selezionare **Aggiungi chiave** e quindi **Crea nuova chiave**.
1. Nella schermata Crea chiave privata selezionare **JSON**e quindi fare clic su **Crea**.
1. Salvare il file JSON per un uso successivo.


### <a name="step-5-connect-gcp-to-security-center"></a>Passaggio 5. Connetti GCP al centro sicurezza 
1. Dal menu del Centro sicurezza selezionare **connettori cloud**.
1. Selezionare Aggiungi account GCP.
1. Nella pagina onboarding eseguire le operazioni seguenti e quindi fare clic su **Next (avanti**).
    1. Convalidare la sottoscrizione scelta.
    1. Nel campo **nome visualizzato** immettere un nome visualizzato per il connettore.
    1. Nel campo **ID organizzazione** immettere l'ID dell'organizzazione. Se non lo si conosce, vedere [creazione e gestione di organizzazioni](https://cloud.google.com/resource-manager/docs/creating-managing-organization).
    1. Nella casella file di **chiave privata** passare al file JSON scaricato nel [passaggio 4. Creare una chiave privata per l'account del servizio dedicato](#step-4-create-a-private-key-for-the-dedicated-service-account).


### <a name="step-6-confirmation"></a>Passaggio 6. Conferma

Quando il connettore viene creato correttamente e il centro comandi di GCP Security è stato configurato correttamente:

- Lo standard GCP CIS verrà visualizzato nel dashboard conformità normativa del Centro sicurezza.
- Le raccomandazioni sulla sicurezza per le risorse di GCP verranno visualizzate nel portale del Centro sicurezza e nel dashboard di conformità normativa 5-10 minuti dopo il completamento dell'onboarding:   :::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="risorse e consigli per GCP nella pagina raccomandazioni del Centro sicurezza":::


## <a name="monitoring-your-gcp-resources"></a>Monitoraggio delle risorse di GCP

Come illustrato in precedenza, la pagina raccomandazioni per la sicurezza del Centro sicurezza di Azure Visualizza le risorse di GCP insieme alle risorse di Azure e AWS per una vera e propria visualizzazione cloud.

Per visualizzare tutte le raccomandazioni attive per le risorse per tipo di risorsa, usare la pagina inventario asset del Centro sicurezza e filtrare per il tipo di risorsa GCP a cui si è interessati:

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="Filtro del tipo di risorsa della pagina inventario asset che mostra le opzioni di GCP"::: 


## <a name="next-steps"></a>Passaggi successivi

La connessione dell'account GCP fa parte dell'esperienza multicloud disponibile nel centro sicurezza di Azure. Per informazioni correlate, vedere la pagina seguente:

- [Connettere gli account AWS al centro sicurezza di Azure](quickstart-onboard-aws.md)
