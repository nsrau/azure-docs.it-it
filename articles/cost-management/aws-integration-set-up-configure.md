---
title: Impostare e configurare l'integrazione di report di utilizzo e costi AWS con gestione costi di Azure
description: Questo articolo illustra come impostare e configurare l'integrazione di report di utilizzo e costi AWS con gestione costi di Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 951178a82e0975f5f2af71bd48cf0f931246ae37
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002132"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Impostare e configurare l'integrazione di report di utilizzo e costi di AWS

Con Amazon Web Services (AWS) utilizzo e costi integrazione di report (CUR), è possibile monitorare e controllare la spesa di AWS in Gestione costi di Azure. L'integrazione consente a un'unica posizione nel portale di Azure in cui è possibile monitorare e controllo di spesa di Azure e AWS. Questo articolo illustra come configurare l'integrazione e configurarlo in modo che usi le funzionalità di gestione costi di Azure per analizzare i costi e rivedere i budget.

I processi di gestione dei costi il report di utilizzo e costi AWS archiviato in un bucket S3 usando le credenziali di accesso AWS per ottenere le definizioni dei report e scaricare i file CSV GZIP report.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Creare un report di utilizzo e costi in AWS

Utilizzo di un report di utilizzo e costi è il modo consigliato di AWS per raccogliere ed elaborare ai costi AWS. Per altre informazioni, vedere la [Report sull'utilizzo e costi AWS](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) documentazione.

Usare la **report sull'utilizzo e costi** della console di gestione dei costi e fatturazione in AWS per creare un report di utilizzo e costi con i passaggi seguenti:

1. Accedere alla Console di gestione di AWS e aprire il [console di gestione dei costi e fatturazione](https://console.aws.amazon.com/billing).
2. Nel riquadro di spostamento, selezionare **report sull'utilizzo e costi**.
3. Selezionare **creazione di report**.
4. Per la **Nome Report**, immettere un nome per il report.
5. Per la **dettagli aggiuntivi della segnalazione** , per includere gli ID di ogni risorsa nel report, quindi scegliere **includono gli ID delle risorse**.
6. Per la **impostazioni di aggiornamento dati**, specificare se si vuole che il report di utilizzo e costi di AWS per aggiornare se AWS applica rimborsi, i crediti, o supportano tariffe per l'account dopo avere completato la fattura. Quando un report viene aggiornato, un nuovo report viene caricato in Amazon S3. È consigliabile lasciare questa impostazione su.
7. Selezionare **Avanti**.
8. Per la **bucket di S3**, scegliere **configura**.
9. Nella finestra di dialogo configurare Bucket di S3, effettuare una delle operazioni seguenti:
    1. Selezionare un bucket esistente dall'elenco a discesa e scegliere **successivo**.
    2. Immettere un nome del bucket e l'area in cui si desidera creare un bucket di nuovo e scegliere **successivo**.
10. Selezionare che ho verificato che questo criterio è correggere e scegliere Salva.
11. (Facoltativo) Prefisso del percorso del Report, immettere il prefisso di percorso di report che si desidera antecedente al nome del report.
Se non si specifica un prefisso, il prefisso predefinito è il nome specificato per il report nel passaggio 4 e l'intervallo di date per il report, nel formato seguente: `/report-name/date-range/`
12. Per la **unità di tempo**, scegliere **oraria**.
13. Per la **controllo delle versioni dei Report**, scegliere se si desidera che ogni versione del report per sovrascrivere la versione precedente del report o da recapitare oltre alle versioni precedenti.
14. Per la **abilitare l'integrazione di dati per**, è necessaria alcuna selezione.
15. Per la **compressione**, selezionare **GZIP**.
16. Selezionare **Avanti**.
17. Dopo aver esaminato le impostazioni per il report, selezionare **verifica e completa**.

    Si noti il nome del report. È possibile usarlo nei passaggi successivi.

Può richiedere fino a 24 ore per AWS per iniziare a recapitare i report nel bucket Amazon S3. Dopo l'avvio di recapito, AWS Aggiorna i file di report di utilizzo e costi AWS almeno una volta al giorno. È possibile continuare a configurare l'ambiente di AWS senza tempi di attesa per il recapito iniziare.

## <a name="create-a-role-and-policy-in-aws"></a>Creare un ruolo e i criteri in AWS

Gestione costi di Azure consente di accedere il bucket S3 in cui il report di utilizzo e costi si trova più volte al giorno. Il servizio deve accedere alle credenziali per verificare la presenza di nuovi dati. Creare un ruolo e i criteri in AWS per consentire la gestione dei costi per accedervi.

Per abilitare l'accesso basato sui ruoli a un account AWS in Gestione costi, il ruolo viene creato nella console di AWS. È necessario avere il _ARN del ruolo_ e _ID esterno_ dalla console di AWS. In un secondo momento, usarli nel **creare un connettore di AWS** pagina in Gestione costi.

Usare la creazione guidata nuovo ruolo:

1. Accedere alla console di AWS e selezionare **Services**.
2. Nell'elenco dei servizi, selezionare **IAM**.
3. Selezionare **ruoli** e quindi selezionare **Create Role**.
4. Nella pagina successiva, selezionare **account AWS un'altra**.
5. Nelle **ID dell'Account**, immettere **432263259397**.
6. Nelle **le opzioni**, selezionare **Richiedi ID esterno (procedura consigliata quando una terza parte assumerà il ruolo)**.
7. Nelle **ID esterno**, immettere l'ID esterno. L'ID esterno è un codice di accesso condiviso tra il ruolo AWS e gestione costi di Azure. Lo stesso ID esterno viene usato anche nella **nuovo connettore** pagina in Gestione costi. Ad esempio, un ID esterno è simile _Companyname1234567890123_.

    > [!NOTE]
    > Non modificare la selezione per **richiedere il MFA**. Deve rimanere selezionata.
8. Selezionare **Avanti: Permissions** (Avanti: Autorizzazioni).
9. Selezionare **creare criterio**. Si apre una nuova scheda del browser. Ovvero in cui si crea un criterio.
10. Selezionare **scegliere un servizio**.

Configurare le autorizzazioni per il report di utilizzo e costi:

1. Immettere **Report sull'utilizzo e costi**.
2. Selezionare **livello di accesso** > **lettura** > **DescribeReportDefinitions**. Questo passaggio consente la gestione dei costi leggere quali report CUR sono definiti e determinare se il prerequisito di definizione del report corrispondono.
3. Selezionare **aggiungere le autorizzazioni aggiuntive**.

Configurare le autorizzazioni per il bucket S3 e oggetti:

1. Selezionare **scegliere un servizio**.
2. Immettere **S3**.
3. Selezionare **livello di accesso** > **elenco** > **ListBucket**. Questa operazione Ottiene l'elenco di oggetti nel Bucket di S3.
4. Selezionare **livello di accesso** > **lettura** > **GetObject**. Questa azione consente il download dei file di fatturazione.
5. Selezionare **risorse**.
6. Selezionare **bucket – aggiungere ARN**.
7. Nelle **nome del Bucket**, immettere il bucket utilizzato per archiviare i file CUR.
8. Selezionare **oggetto – aggiungere ARN**.
9. Nelle **nome del Bucket**, immettere il bucket utilizzato per archiviare i file CUR.
10. Nelle **nome dell'oggetto**, selezionare **qualsiasi**.
11. Selezionare **aggiungere le autorizzazioni aggiuntive**.

Configurare le autorizzazioni per Esplora risorse di costo:

1. Selezionare **scegliere un servizio**.
2. Immettere **costo Service Explorer**.
3. Selezionare **tutti i costi Explorer Service actions (ce:\*)**. Questa azione verifica che la raccolta sia corretta.
4. Selezionare **aggiungere le autorizzazioni aggiuntive**.

Aggiungere le autorizzazioni per le organizzazioni di AWS:

1. Immettere **organizzazioni**.
2. Selezionare **livello di accesso** > **elenco** > **ListAccounts**. Questa operazione Ottiene i nomi degli account.
3. Nelle **criteri di revisione**, immettere un nome per il nuovo criterio. Verificare di aver digitato le informazioni corrette e quindi selezionare **Crea criteri**.
4. Tornare alla scheda precedente e aggiornare pagina Web del browser. Nella barra di ricerca, cercare i nuovi criteri.
5. Selezionare **Avanti: revisione**.
6. Immettere un nome per il nuovo ruolo. Verificare di aver digitato le informazioni corrette e quindi selezionare **Create Role**.

    Nota: il ruolo ARN e l'ID esterno usata nei passaggi precedenti quando è stato creato il ruolo. Si userà loro in un secondo momento quando si configura il connettore di gestione costi di Azure.

Il codice JSON del criterio dovrebbe essere simile al seguente. Sostituire _bucketname_ con il nome del bucket S3.

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
            "ce:*",
            "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-aws-connector-in-azure"></a>Configurare un nuovo connettore AWS in Azure

Usare le informazioni seguenti per creare un connettore di AWS e iniziare a monitorare i costi AWS:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare a **gestione e fatturazione dei costi** > **gestione costi**.
3. Sotto **le impostazioni**, selezionare **Cloud connettori (anteprima)**.  
    ![Esempio che illustra i connettori di Cloud (anteprima) impostazione)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Selezionare **+ Aggiungi** nella parte superiore della pagina per creare un connettore.
5. Nel **creare un connettore di AWS** nella pagina **nome visualizzato**, immettere un nome per il connettore.  
    ![Esempio della pagina per la creazione di un connettore AWS](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Facoltativamente, selezionare il gruppo di gestione predefinito. Archivierà individuati tutti gli account collegati. È possibile configurarlo in un secondo momento.
7. Nel **fatturazione** sezione, selezionare **addebitato automaticamente il % 1 al momento della disponibilità generale** se si vuole garantire il funzionamento continuo quando scade l'anteprima. Se si seleziona l'opzione automatica, è necessario selezionare una sottoscrizione di fatturazione.
8. Per la **ARN del ruolo**, immettere il valore usato quando si configura il ruolo in AWS.
9. Per la **ID esterno**, immettere il valore usato quando si configura il ruolo in AWS.
10. Per la **Nome Report**, immettere il nome che è stato creato in AWS.
11. Selezionare **successivo** e quindi selezionare **crea**.

Potrebbe richiedere alcune ore per i nuovi ambiti AWS, di AWS consolidati account account AWS collegati e i dati relativi ai costi venga visualizzato.

Dopo aver creato il connettore, è consigliabile assegnare il controllo di accesso a esso. Gli utenti vengono assegnati autorizzazioni per gli ambiti appena individuati: AWS consolidati account e degli account AWS collegato. L'utente che crea il connettore è il proprietario del connettore, l'account consolidato e tutti gli account collegati.

Assegnazione di autorizzazioni per i connettori agli utenti dopo l'individuazione viene eseguita non assegnare autorizzazioni per gli ambiti di AWS esistenti. In alternativa, solo i nuovi account collegati vengono assegnate autorizzazioni.

## <a name="take-additional-steps"></a>Eseguire passaggi aggiuntivi

- [Configurare i gruppi di gestione](../governance/management-groups/index.md#initial-setup-of-management-groups), se già stato fatto.
- Verificare che i nuovi ambiti vengono aggiunti per il selettore di ambito. Selezionare **Aggiorna** per visualizzare i dati più recenti.
- Nel **Cloud connettori** pagina, selezionare il connettore e selezionare **passare all'account di fatturazione** per assegnare l'account collegato ai gruppi di gestione.

## <a name="manage-cloud-connectors"></a>Gestire i connettori di cloud

Quando si seleziona un connettore nella **Cloud connettori** pagina, è possibile:

- Selezionare **passare all'Account di fatturazione** per visualizzare le informazioni per il AWS consolidati account.
- Selezionare **controllo di accesso** per gestire l'assegnazione di ruolo per il connettore.
- Selezionare **modifica** per aggiornare il connettore. È possibile modificare il numero di account AWS, perché viene visualizzato nell'ARN del ruolo. Ma è possibile creare un nuovo connettore.
- Selezionare **Verify** per rieseguire il test di verifica per assicurarsi che Gestione costi può raccogliere i dati usando le impostazioni del connettore.

![Esempio di elenco di connettori AWS creati](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>Configurare i gruppi di gestione di Azure

Per creare un'unica posizione per visualizzare le informazioni sul provider tra cloud, è necessario inserire le sottoscrizioni di Azure e account AWS collegato nello stesso gruppo di gestione. Se è già stato configurato l'ambiente di Azure con gruppi di gestione, vedere [configurazione dei gruppi di gestione iniziale](../governance/management-groups/index.md#initial-setup-of-management-groups).

Se si vuole separare i costi, è possibile creare un gruppo di gestione che contiene solo gli account AWS collegato.

## <a name="set-up-an-aws-consolidated-account"></a>Conto consolidato configurare AWS

L'account AWS consolidati combina la fatturazione e pagamento per più account AWS. Viene anche usato come un account di AWS collegato.

![Informazioni dettagliate di esempio per AWS consolidati account](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

Dalla pagina, è possibile:

- Selezionare **aggiornare** per aggiornamento in blocco l'associazione di AWS account con un gruppo di gestione collegati.
- Selezionare **controllo di accesso** per impostare l'assegnazione di ruolo per l'ambito.

### <a name="permissions-for-an-aws-consolidated-account"></a>Le autorizzazioni per AWS consolidati account

Per impostazione predefinita, le autorizzazioni per un account AWS consolidati vengono impostate al momento della creazione dell'account, in base alle autorizzazioni per i connettore AWS. Il creatore del connettore è il proprietario.

Gestire il livello di accesso usando il **a livello di accesso** pagina della finestra di AWS consolidati account. Tuttavia, AWS collegati gli account non ereditano le autorizzazioni per l'account AWS consolidati.

## <a name="set-up-an-aws-linked-account"></a>Configurare un account AWS collegato

L'account AWS collegata è in cui vengono create e gestite risorse AWS. Un account collegato funge anche da limite di sicurezza.

In questa pagina è possibile:

- Selezionare **aggiornare** per aggiornare l'associazione di AWS account con un gruppo di gestione collegato.
- Selezionare **controllo di accesso** per impostare un'assegnazione di ruolo per l'ambito.

![Esempio della pagina di AWS Account collegato](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>Le autorizzazioni per AWS account collegato

Per impostazione predefinita, le autorizzazioni per un account AWS collegato vengono impostate al momento della creazione, in base alle autorizzazioni per i connettore AWS. Il creatore del connettore è il proprietario. Gestire il livello di accesso usando il **livello di accesso** pagina della finestra di AWS collegato l'account. AWS collegati gli account non ereditano autorizzazioni da un account AWS consolidati.

AWS account collegati ereditano sempre le autorizzazioni dal gruppo di gestione a cui appartengono.

## <a name="next-steps"></a>Passaggi successivi

- Dopo aver impostato e configurato l'integrazione di report di utilizzo e costi di AWS, procedere al [utilizzo e costi AWS gestire](aws-integration-manage.md).
- Se non si ha familiarità con analisi dei costi, vedere [esplorare e analizzare i costi con l'analisi del costo](quick-acm-cost-analysis.md) Guida introduttiva.
- Se non si ha familiarità con i budget di Azure, vedere [crea e Gestisci i budget di Azure](tutorial-acm-create-budgets.md).
