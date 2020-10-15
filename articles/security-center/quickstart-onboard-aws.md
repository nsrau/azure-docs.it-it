---
title: Connettere l'account AWS al Centro sicurezza di Azure
description: Monitoraggio delle risorse di AWS dal Centro sicurezza di Azure
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: cddae0a7115fc2999b52eaba7df2b49db509981b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449033"
---
#  <a name="connect-your-aws-accounts-to-azure-security-center"></a>Connettere gli account AWS a Centro sicurezza di Azure

I carichi di lavoro cloud si estendono in genere su più piattaforme cloud, quindi anche i servizi di sicurezza cloud devono adottare lo stesso approccio.

Il Centro sicurezza di Azure protegge i carichi di lavoro in Azure, Amazon Web Services (AWS) e Google Cloud Platform (GCP).

L'onboarding dell'account di AWS nel Centro sicurezza consente di integrare AWS Security Hub e il Centro sicurezza di Azure. Il Centro sicurezza offre quindi la visibilità e la protezione in entrambi gli ambienti cloud per offrire:

- Provisioning automatico dell'agente (il Centro sicurezza usa [Azure Arc](../azure-arc/servers/overview.md) per distribuire l'agente di Log Analytics nelle istanze di AWS)
- Gestione dei criteri
- Gestione vulnerabilità
- Rilevamento di endpoint e risposta incorporato
- Rilevamento degli errori di configurazione per la sicurezza
- Una singola visualizzazione che mostra le raccomandazioni del Centro sicurezza e i risultati di AWS Security Hub
- Integrazione delle risorse di AWS nei calcoli del punteggio di sicurezza del Centro sicurezza
- Valutazioni della conformità alle normative delle risorse di AWS

Lo screenshot seguente mostra gli account AWS visualizzati nel dashboard di panoramica del Centro sicurezza.

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="3 progetti GCP elencati nel dashboard di panoramica del Centro sicurezza" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Anteprima|
|Prezzi:|Richiede [Azure Defender per server](defender-for-servers-introduction.md)|
|Autorizzazioni e ruoli obbligatori:|**Proprietario** o **Collaboratore** nella sottoscrizione di Azure rilevante|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![No](./media/icons/no-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)|
|||



## <a name="connect-your-aws-account"></a>Connettere l'account AWS

### <a name="step-1-set-up-aws-security-hub"></a>Passaggio 1. Configurare AWS Security Hub:

1. Per visualizzare le raccomandazioni sulla sicurezza per più aree, ripetere la procedura seguente per ogni area rilevante.

    > [!IMPORTANT]
    > Se si usa un account AWS principale, ripetere i tre passaggi seguenti per configurare l'account principale e tutti gli account membri connessi in tutte le aree rilevanti

    1. Abilitare [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html).
    1. Abilitare [AWS Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html).
    1. Verificare che sia presente un flusso di dati verso l'istanza di Security Hub.

        Quando si abilita Security Hub per la prima volta, per la disponibilità dei dati potrebbe essere necessario attendere alcune ore.

### <a name="step-2-set-up-authentication-for-security-center-in-aws"></a>Passaggio 2. Configurare l'autenticazione per il Centro sicurezza in AWS

È possibile consentire in due modi al Centro sicurezza di eseguire l'autenticazione in AWS:

- **Creare un ruolo di gestione delle identità e degli accessi per il Centro sicurezza**: questo è il metodo più sicuro ed è il metodo consigliato
- **Utente di AWS per il Centro sicurezza**: opzione meno sicura nel caso in cui la gestione delle identità e degli accessi non sia abilitata

#### <a name="create-an-iam-role-for-security-center"></a>Creare un ruolo di gestione delle identità e degli accessi per il Centro sicurezza
1. In **Security, Identity & Compliance** (Sicurezza, identità e conformità) nella console di Amazon Web Services selezionare **IAM**.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="3 progetti GCP elencati nel dashboard di panoramica del Centro sicurezza":::

1. Selezionare **Roles** (Ruoli) e **Create role** (Crea ruolo).
1. Selezionare **Another AWS account** (Un altro account AWS).
1. Immettere i dettagli seguenti:

    - **Account ID**  (ID account): immettere l'ID account Microsoft (**158177204117**) indicato nella pagina del connettore AWS nel Centro sicurezza.
    - **Require External ID** (Richiedi ID esterno): questa opzione deve essere selezionata.
    - **External ID** (ID esterno): immettere l'ID della sottoscrizione indicato nella pagina del connettore AWS nel Centro sicurezza. 

1. Selezionare **Avanti**.
1. Nella sezione **Attach permission policies** (Allega criteri di autorizzazione) selezionare i criteri seguenti:

    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess

1. Aggiungere tag, se necessario. L'aggiunta di tag all'utente non influisce sulla connessione.
1. Selezionare **Avanti**.

1. Nell'elenco di ruoli scegliere il ruolo creato

1. Salvare il valore ARN (Amazon Resource Name) per un momento successivo. 

#### <a name="create-an-aws-user-for-security-center"></a>Creare un utente AWS per il Centro sicurezza 
1. Aprire la scheda **Users** (Utenti) e selezionare **Add user** (Aggiungi utente).
1. Nel passaggio **Details** (Dettagli) immettere un nome utente per il Centro sicurezza e assicurarsi di selezionare **Programmatic access** (Accesso programmatico) per il tipo di accesso AWS. 
1. Selezionare **Next Permissions** (Autorizzazioni successive).
1. Selezionare **Attach existing policies directly** (Collega direttamente i criteri esistenti) e applicare i criteri seguenti:
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. Selezionare **Avanti: Tag**. Aggiungere tag, se necessario. L'aggiunta di tag all'utente non influisce sulla connessione.
1. Selezionare **Review** (Verifica).
1. Salvare il file CSV generato con **Access key ID** (ID chiave di accesso) e **Secret access key** (Chiave di accesso segreta) per un momento successivo.
1. Controllare il riepilogo e fare clic su **Create user** (Crea utente).


### <a name="step-3-configure-the-ssm-agent"></a>Passaggio 3. Configurare SSM Agent

AWS Systems Manager è necessario per l'automazione di attività nelle risorse di AWS. Se le istanze di EC2 non includono SSM Agent, seguire le istruzioni rilevanti fornite da Amazon:

- [Installazione e configurazione di SSM Agent in istanze di Windows](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [Installazione e configurazione di SSM Agent in istanze Linux di Amazon EC2](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-create-a-service-principal-for-onboarding-at-scale"></a>Passaggio 4. Creare un'entità servizio per l'onboarding su larga scala

Come **Proprietario** della sottoscrizione da usare per l'onboarding, creare un'entità servizio per l'onboarding di Azure Arc, come illustrato in [Creare un'entità servizio per l'onboarding su larga scala](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)


### <a name="step-5-connect-aws-to-security-center"></a>Passaggio 5. Connettere AWS al Centro sicurezza

1. Dal menu del Centro sicurezza selezionare **Connettori per più cloud**.
1. Selezionare **Aggiungi un account AWS**.
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="3 progetti GCP elencati nel dashboard di panoramica del Centro sicurezza":::
1. Configurare le opzioni disponibili nella scheda **Autenticazione AWS**:
    1. Immettere un **Nome visualizzato** per il connettore.
    1. Verificare che la sottoscrizione sia corretta. Si tratta della sottoscrizione che includerà il connettore e le raccomandazioni di AWS Security Hub.
    1. In base all'opzione di autenticazione scelta in [Passaggio 2. Configurare l'autenticazione per il Centro sicurezza in AWS](#step-2-set-up-authentication-for-security-center-in-aws):
        - Selezionare **Assumi il ruolo** e incollare il valore ARN da [Creare un ruolo di gestione delle identità e degli accessi per il Centro sicurezza](#create-an-iam-role-for-security-center) :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="3 progetti GCP elencati nel dashboard di panoramica del Centro sicurezza":::

            OR

        - Selezionare **Credenziali** e incollare la **chiave di accesso** e la **chiave privata** dal file CSV salvato in [Creare un utente AWS per il Centro sicurezza](#create-an-aws-user-for-security-center).
1. Selezionare **Avanti**.
1. Configurare le opzioni disponibili nella scheda **Configurazione di Azure Arc**:

    Il Centro sicurezza individua le istanze di EC2 nell'account AWS connesso e usa SSM per eseguirne l'onboarding in Azure Arc. 

    > [!TIP]
    > Per un elenco di sistemi operativi supportati, vedere le domande frequenti disponibili più avanti.

    1. Selezionare il **Gruppo di risorse** e l'**Area di Azure** in cui verrà eseguito l'onboarding delle istanze individuate di AWS EC2 nella sottoscrizione selezionata.
    1. Immettere l'**ID entità servizio** e il **Segreto client entità servizio** per Azure Arc come illustrato in [Creare un'entità servizio per l'onboarding su larga scala](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)
    1. Se il computer si connette a Internet tramite un server proxy, specificare l'indirizzo IP del server proxy o il nome e il numero di porta usati dal computer per comunicare con il server proxy. Immettere il valore nel formato ```http://<proxyURL>:<proxyport>```
    1. Selezionare **Rivedi e crea**.

        Verificare le informazioni di riepilogo

        Nella sezione Tag saranno elencati tutti i tag di Azure creati automaticamente per ogni istanza di EC2 sottoposta a onboarding, con i rispettivi dettagli rilevanti per facilitarne il riconoscimento in Azure. 

        Per altre informazioni sui tag di Azure, vedere [Usare i tag per organizzare le risorse di Azure e la gerarchia di gestione](../azure-resource-manager/management/tag-resources.md).

### <a name="step-7-confirmation"></a>Passaggio 7. Conferma

Quando il connettore è stato creato e la configurazione di AWS Security Hub è stata definita correttamente:

- Il Centro sicurezza analizza l'ambiente per individuare istanze di AWS EC2, eseguirne l'onboarding in Azure Arc, installare l'agente di Log Analytics e fornire protezione dalle minacce e raccomandazioni sulla sicurezza. 
- Il servizio Centro sicurezza di Azure esegue ogni 6 ore l'analisi per individuare nuove istanze di AWS EC2 e ne esegue l'onboarding in base alla configurazione.
- Gli standard CIS per AWS verranno visualizzati nel dashboard di conformità alle normative del Centro sicurezza.
- Se è abilitato il criterio di Security Hub, le raccomandazioni verranno visualizzate nel portale del Centro sicurezza e nel dashboard di conformità alle normative 5-10 minuti dopo il completamento dell'onboarding.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="3 progetti GCP elencati nel dashboard di panoramica del Centro sicurezza":::



## <a name="monitoring-your-aws-resources"></a>Monitoraggio delle risorse di AWS

Come illustrato sopra, la pagina delle raccomandazioni sulla sicurezza del Centro sicurezza di Azure mostra le risorse di AWS insieme alle risorse di Azure e GCP per offrire una visualizzazione effettiva per più cloud.

Per visualizzare tutte le raccomandazioni attive per le risorse in base al tipo di risorsa, usare la pagina di inventario delle risorse del Centro sicurezza per applicare un filtro basato sul tipo di risorsa di AWS a cui si è interessati:

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="3 progetti GCP elencati nel dashboard di panoramica del Centro sicurezza"::: 


## <a name="aws-in-security-center-faq"></a>Domande frequenti su AWS nel Centro sicurezza

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>Quali sistemi operativi sono supportati per le istanze di EC2?

Sistemi operativi supportati per l'onboarding automatico in Azure Arc per computer AWS

- Ubuntu 16.04 - SSM Agent preinstallato per impostazione predefinita
- Ubuntu 18.04 -SSM Agent preinstallato per impostazione predefinita
- Windows Server - SSM Agent preinstallato per impostazione predefinita
- CentOS Linux 7 – SSM da installare manualmente o sottoporre a onboarding separatamente
- SUSE Linux Enterprise Server (SLES) 15 (x64) -SSM da installare manualmente o sottoporre a onboarding separatamente
- Red Hat Enterprise Linux (RHEL) 7 (x64) - SSM da installare manualmente o sottoporre a onboarding separatamente


## <a name="next-steps"></a>Passaggi successivi

La connessione dell'account AWS è inclusa nell'esperienza per più cloud disponibile nel Centro sicurezza di Azure. Per informazioni correlate, vedere la pagina seguente:

- [Connettere gli account GCP a Centro sicurezza di Azure](quickstart-onboard-gcp.md)
