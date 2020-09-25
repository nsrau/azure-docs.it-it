---
title: Connetti il tuo account AWS al centro sicurezza di Azure
description: Monitoraggio delle risorse di AWS dal centro sicurezza di Azure
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 33ff6748eba9d0349df244e70f07ad178f258373
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91250285"
---
#  <a name="connect-your-aws-accounts-to-azure-security-center"></a>Connettere gli account AWS al centro sicurezza di Azure

Con i carichi di lavoro cloud comunemente distribuiti su più piattaforme cloud, i servizi di sicurezza cloud devono eseguire la stessa operazione.

Il Centro sicurezza di Azure protegge i carichi di lavoro in Azure, Amazon Web Services (AWS) e Google Cloud Platform (GCP).

Onboarding dell'account AWS nel centro sicurezza, integra l'hub di sicurezza di AWS e il Centro sicurezza di Azure. Il Centro sicurezza offre quindi visibilità e protezione in entrambi gli ambienti cloud per offrire:

- Provisioning automatico degli agenti (il Centro sicurezza USA [Azure Arc](../azure-arc/servers/overview.md) per distribuire l'agente di log Analytics nelle istanze di AWS)
- Gestione dei criteri
- Gestione vulnerabilità
- Rilevamento e risposta dell'endpoint incorporato (EDR)
- Rilevamento di configurazioni non configurate per la sicurezza
- Una singola visualizzazione che mostra le raccomandazioni del Centro sicurezza e i risultati dell'hub di sicurezza AWS
- Integrazione delle risorse di AWS nei calcoli di Punteggio sicuro del Centro sicurezza
- Valutazioni della conformità alle normative delle risorse di AWS

Nella schermata seguente è possibile visualizzare gli account AWS visualizzati nel Dashboard panoramica del Centro sicurezza.

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="3 progetti GCP elencati nel dashboard di panoramica del Centro sicurezza" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato versione:|Anteprima|
|Prezzi|Richiede [Azure Defender per i server](defender-for-servers-introduction.md)|
|Ruoli e autorizzazioni necessari:|**Proprietario** o **collaboratore** nella sottoscrizione di Azure pertinente|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![No](./media/icons/no-icon.png) Nazionale/sovrano (US Gov, Cina gov, altri gov)|
|||



## <a name="connect-your-aws-account"></a>Connettere l'account AWS

### <a name="step-1-set-up-aws-security-hub"></a>Passaggio 1. Configurare l'hub di sicurezza di AWS:

1. Per visualizzare le raccomandazioni sulla sicurezza per più aree, ripetere i passaggi seguenti per ogni area pertinente.

    > [!IMPORTANT]
    > Se si usa un account master AWS, ripetere i tre passaggi seguenti per configurare l'account master e tutti gli account membri connessi in tutte le aree pertinenti

    1. Abilitare la [configurazione di AWS](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html).
    1. Abilitare l' [Hub di sicurezza AWS](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html).
    1. Verificare che sia presente un flusso di dati all'hub sicurezza.

        Quando si Abilita per la prima volta l'hub sicurezza, potrebbero essere necessarie diverse ore prima che i dati siano disponibili.

### <a name="step-2-set-up-authentication-for-security-center-in-aws"></a>Passaggio 2. Configurare l'autenticazione per il Centro sicurezza in AWS

Esistono due modi per consentire al centro sicurezza di eseguire l'autenticazione in AWS:

- **Creare un ruolo IAM per il Centro sicurezza** -questo è il metodo più sicuro ed è consigliato
- **Utente AWS per il Centro sicurezza** -un'opzione meno sicura se iam non è abilitato

#### <a name="create-an-iam-role-for-security-center"></a>Creare un ruolo IAM per il Centro sicurezza
1. Dalla console di Amazon Web Services, in **sicurezza, Identity & Compliance**, selezionare **IAM**.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="Servizi AWS":::

1. Selezionare **ruoli** e **Crea ruolo**.
1. Selezionare **un altro account AWS**.
1. Immettere i dettagli seguenti:

    - **ID account** : immettere l'ID dell'account Microsoft (**158177204117**), come illustrato nella pagina del connettore AWS del Centro sicurezza.
    - **Richiedi ID esterno** -selezionare
    - **ID esterno** : immettere l'ID sottoscrizione, come illustrato nella pagina del connettore AWS nel centro sicurezza 

1. Selezionare **Avanti**.
1. Nella sezione **associazione dei criteri di autorizzazione** selezionare i criteri seguenti:

    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess

1. Facoltativamente, aggiungere i tag. L'aggiunta di tag all'utente non influisce sulla connessione.
1. Selezionare **Avanti**.

1. Nell'elenco ruoli scegliere il ruolo creato

1. Salvare il nome della risorsa Amazon (ARN) per un momento successivo. 

#### <a name="create-an-aws-user-for-security-center"></a>Creare un utente di AWS per il Centro sicurezza 
1. Aprire la scheda **utenti** e selezionare **Aggiungi utente**.
1. Nel passaggio **Dettagli** immettere un nome utente per il Centro sicurezza e assicurarsi di selezionare **accesso a livello di codice** per il tipo di accesso AWS. 
1. Selezionare **Next Permissions** (Autorizzazioni successive).
1. Selezionare **Connetti i criteri esistenti direttamente** e applicare i criteri seguenti:
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. Selezionare **Avanti: Tag**. Facoltativamente, aggiungere i tag. L'aggiunta di tag all'utente non influisce sulla connessione.
1. Selezionare **Revisione**.
1. Salvare l' **ID chiave di accesso** generato automaticamente e il file CSV della **chiave di accesso privata** per un momento successivo.
1. Esaminare il riepilogo e fare clic su **Crea utente**.


### <a name="step-3-configure-the-ssm-agent"></a>Passaggio 3. Configurare l'agente SSM

AWS Systems Manager è necessario per automatizzare le attività nelle risorse di AWS. Se le istanze EC2 non hanno l'agente SSM, seguire le istruzioni pertinenti da Amazon:

- [Installazione e configurazione dell'agente SSM nelle istanze di Windows](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [Installazione e configurazione dell'agente SSM nelle istanze di Amazon EC2 Linux](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-create-a-service-principal-for-onboarding-at-scale"></a>Passaggio 4. Creare un'entità servizio per l'onboarding su larga scala

In qualità di **proprietario** della sottoscrizione da usare per l'onboarding, creare un'entità servizio per il caricamento di Azure Arc come descritto in [creare un'entità servizio per l'onboarding su larga scala](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)


### <a name="step-5-connect-aws-to-security-center"></a>Passaggio 5. Connetti AWS al centro sicurezza

1. Dal menu del Centro sicurezza selezionare connettori per più **cloud**.
1. Selezionare **Aggiungi account AWS**.
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Pulsante Aggiungi account AWS nella pagina dei connettori multicloud del Centro sicurezza":::
1. Configurare le opzioni nella scheda **autenticazione di AWS** :
    1. Immettere un **nome visualizzato** per il connettore.
    1. Verificare che la sottoscrizione sia corretta. Si tratta della sottoscrizione che includerà le raccomandazioni Connector e l'hub di sicurezza AWS.
    1. A seconda dell'opzione di autenticazione scelta nel [passaggio 2. Configurare l'autenticazione per il Centro sicurezza in AWS](#step-2-set-up-authentication-for-security-center-in-aws):
        - Selezionare  **presupporre Role** e incollare ARN da [creare un ruolo IAM per il Centro sicurezza](#create-an-iam-role-for-security-center) che :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="Incolla il file ARN nel campo pertinente della connessione guidata AWS nel portale di Azure":::

            OR

        - Selezionare le **credenziali** e incollare la **chiave di accesso** e la **chiave privata** dal file con estensione CSV salvato in [creare un utente di AWS per il Centro sicurezza](#create-an-aws-user-for-security-center).
1. Selezionare **Avanti**.
1. Configurare le opzioni nella scheda **configurazione di Azure Arc** :

    Il Centro sicurezza individua le istanze EC2 nell'account AWS connesso e USA SSM per caricarle in Azure Arc. 

    > [!TIP]
    > L'elenco dei sistemi operativi supportati è riportato nelle domande frequenti riportate di seguito.

    1. Selezionare il **gruppo di risorse** e l' **area di Azure** in cui verrà caricato il EC2s AWS individuato nella sottoscrizione selezionata.
    1. Immettere l' **ID dell'entità servizio** e il **segreto client dell'entità servizio** per Azure Arc, come descritto qui [creare un'entità servizio per l'onboarding su larga scala](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)
    1. Se il computer si connette a Internet tramite un server proxy, specificare l'indirizzo IP del server proxy o il nome e il numero di porta usati dal computer per comunicare con il server proxy. Immettere il valore nel formato ```http://<proxyURL>:<proxyport>```
    1. Selezionare **Rivedi e crea**.

        Esaminare le informazioni di riepilogo

        Le sezioni Tag elencano tutti i tag di Azure che verranno creati automaticamente per ogni EC2 caricato con i relativi dettagli pertinenti per riconoscerli facilmente in Azure. 

        Per altre informazioni sui tag di Azure [, vedere usare i tag per organizzare le risorse e la gerarchia di gestione di Azure](../azure-resource-manager/management/tag-resources.md).

### <a name="step-7-confirmation"></a>Passaggio 7. Conferma

Quando il connettore viene creato correttamente e l'hub di sicurezza AWS è stato configurato correttamente:

- Il Centro sicurezza analizza l'ambiente per le istanze EC2 di AWS, li carica in Azure Arc, consentendo di installare l'agente di Log Analytics e fornire consigli sulla protezione dalle minacce e sulla sicurezza. 
- Il servizio ASC analizza le nuove istanze di AWS EC2 ogni 6 ore e le carica in base alla configurazione.
- Lo standard di AWS CIS verrà visualizzato nel dashboard conformità normativa del Centro sicurezza.
- Se i criteri dell'hub di sicurezza sono abilitati, le indicazioni verranno visualizzate nel portale del Centro sicurezza e nel dashboard conformità normativa 5-10 minuti dopo il completamento dell'onboarding.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="Risorse e raccomandazioni di AWS nella pagina delle raccomandazioni del Centro sicurezza":::



## <a name="monitoring-your-aws-resources"></a>Monitoraggio delle risorse di AWS

Come illustrato in precedenza, la pagina raccomandazioni per la sicurezza del Centro sicurezza di Azure Visualizza le risorse di AWS insieme alle risorse di Azure e GCP per una vera e propria visualizzazione cloud.

Per visualizzare tutte le raccomandazioni attive per le risorse per tipo di risorsa, usare la pagina inventario asset del Centro sicurezza e filtrare per il tipo di risorsa AWS a cui si è interessati:

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="Filtro del tipo di risorsa della pagina inventario asset che mostra le opzioni AWS"::: 


## <a name="aws-in-security-center-faq"></a>Domande frequenti su AWS nel centro sicurezza

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>Quali sistemi operativi per le istanze EC2 sono supportati?

Sistema operativo supportato per l'onboarding automatico in Azure Arc per computer AWS

- Ubuntu 16,04-SSM Agent è preinstallato, per impostazione predefinita
- Ubuntu 18,04-SSM Agent è preinstallato, per impostazione predefinita
- Windows Server-SSM Agent è preinstallato, per impostazione predefinita
- CentOS Linux 7-SSM deve essere installato manualmente o onboarding separatamente
- SUSE Linux Enterprise Server (SLES) 15 (x64)-SSM deve essere installato manualmente o caricato separatamente
- Red Hat Enterprise Linux (RHEL) 7 (x64)-SSM deve essere installato manualmente o caricato separatamente


## <a name="next-steps"></a>Passaggi successivi

La connessione dell'account AWS fa parte dell'esperienza multicloud disponibile nel centro sicurezza di Azure. Per informazioni correlate, vedere la pagina seguente:

- [Connettere gli account GCP al centro sicurezza di Azure](quickstart-onboard-gcp.md)
