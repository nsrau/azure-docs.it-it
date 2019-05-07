---
title: Impostare e configurare l'integrazione di report di utilizzo e costi AWS con gestione costi di Azure
description: Questo articolo illustra in modo dettagliato impostazione e configurazione dell'integrazione di report di utilizzo e costi AWS con gestione costi di Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: a7a020284f44eda0da62f307866c74b0a8df493d
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205708"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Impostare e configurare l'integrazione di report di utilizzo e costi di AWS

Con l'integrazione di report di utilizzo e costi di Amazon Web Services, è possibile monitorare e controllare la spesa di AWS in Gestione costi di Azure. L'integrazione consente a un'unica posizione nel portale di Azure in cui è possibile monitorare e controllo di spesa di Azure e AWS. Questo articolo illustra come configurare l'integrazione e configurarlo in modo che usi le funzionalità di gestione dei costi per analizzare i costi e rivedere i budget.

I processi di gestione dei costi il report di utilizzo e costi AWS archiviato in un bucket S3 usando le credenziali di accesso AWS per ottenere le definizioni dei report e scaricare i file CSV GZIP report.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Creare un report di utilizzo e costi in AWS

Uso di un report di utilizzo e costi è AWS soluzione consigliata per raccogliere ed elaborare ai costi AWS. Per altre informazioni, vedere la [Report sull'utilizzo e costi AWS](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) articolo della documentazione.

Usare la **report** della console di gestione dei costi e fatturazione in AWS per creare un report di utilizzo e costi con i passaggi seguenti.

1. Accedere alla Console di gestione di AWS e aprire la console di gestione dei costi e fatturazione in https://console.aws.amazon.com/billing.
2. Nel riquadro di spostamento, fare clic su **report**.
3. Fare clic su **creazione di report**.
4. Per la **Nome Report**, digitare un nome per il report.
5. Per la **unità di tempo**, scegliere **oraria**.
6. Per la **inclusione**, aggiungere gli ID di ogni risorsa nel report e selezionare **degli ID di risorsa**.
7. Per la **abilitare il supporto per**, è necessaria alcuna selezione.
8. Per la **impostazioni di aggiornamento dati**, selezionare **Aggiorna automaticamente i costi &amp; Report di utilizzo quando vengono rilevati gli addebiti per i mesi precedenti chiusi fatture**.
9. Fare clic su **Avanti**.
10. Per la **bucket Amazon S3**, digitare il nome del bucket Amazon S3 in cui si desidera consegna dei report e fare clic su **Verify**. Il bucket è necessario disporre delle autorizzazioni appropriate per essere valido. Per altre informazioni sull'aggiunta di autorizzazioni per il bucket, vedere [Bucket di impostazione e le autorizzazioni di accesso oggetto](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/set-permissions.html).
11. Per la **prefisso percorso Report**, digitare il prefisso di percorso di report che si desidera aggiungere al nome del report.
12. Per la **compressione**, selezionare **GZIP**.
13. Fare clic su **Avanti**.
14. Dopo aver esaminato le impostazioni per il report, fare clic su **verifica e completa**.
    Si noti il **Nome Report**. Si userà lo nei passaggi successivi.

Può richiedere fino a 24 ore per AWS per iniziare a recapitare i report nel bucket Amazon S3. Dopo l'avvio di recapito, AWS Aggiorna i file di report di utilizzo e costi AWS almeno una volta al giorno. È possibile continuare a configurare l'ambiente di AWS senza tempi di attesa per il recapito iniziare.

## <a name="create-a-role-and-policy-in-aws"></a>Creare un ruolo e i criteri in AWS

Gestione costi di Azure consente di accedere il bucket S3 in cui il report di utilizzo e costi si trova più volte al giorno. Gestione costi necessita di accedere alle credenziali per verificare la presenza di nuovi dati. Creare un ruolo e i criteri in AWS per consentire l'accesso da Gestione costi.

Per abilitare l'accesso basato sui ruoli a un account AWS in Gestione costi di Azure, il ruolo viene creato nella console di AWS. È necessario avere il _ARN del ruolo_ e _ID esterno_ dalla console di AWS. In un secondo momento, serviranno in Crea una pagina di connettore AWS in Gestione costi di Azure.

Usare la creazione guidata nuovo ruolo:

1. Accedi alla console di AWS e selezionare **Services**.
2. Nell'elenco dei servizi, selezionare **IAM**.
3. Selezionare **ruoli** e quindi fare clic su **Create Role**.
4. Nella pagina successiva, selezionare **account AWS un'altra**.
5. Nelle **ID dell'Account** , immettere _432263259397_.
6. Nelle **le opzioni**, selezionare **Richiedi ID esterno (procedura consigliata quando una terza parte assumerà il ruolo)**.
7. Nelle **ID esterno**, immettere l'ID esterno. L'ID esterno è un codice di accesso condiviso tra il ruolo AWS e gestione costi di Azure. Lo stesso ID esterno viene usato anche nella pagina del nuovo connettore in Gestione costi. Ad esempio, un ID esterno è simile _Companyname1234567890123_.
    Non modificare la selezione per **richiedere il MFA**. Deve rimanere selezionata.
8. Fare clic su **Avanti: Permissions** (Avanti: Autorizzazioni).
9. Fare clic su **Create policy** (Crea criterio). Viene aperta una nuova scheda del browser in cui si crea un nuovo criterio.
10. Fare clic su **scegliere un servizio**.

Configurare le autorizzazioni dei costi e Report di utilizzo:

1. Tipo di **Report sull'utilizzo e costi**.
2. Selezionare **livello di accesso**, **lettura** > **DescribeReportDefinitions**. In questo modo che Gestione costi di leggere ciò che CUR i report vengono definiti e determinare se il prerequisito di definizione del report corrispondono.
3. Fare clic su **aggiungere le autorizzazioni aggiuntive**.

Configurare l'autorizzazione di oggetti e i bucket di S3:

1. Fare clic su **scegliere un servizio**.
2. Tipo di _S3_.
3. Selezionare **livello di accesso**, **elenco** > **ListBucket**. Questa operazione Ottiene l'elenco di oggetti nel Bucket di S3.
4. Selezionare **livello di accesso**, **lettura** > **GetObject**. Questa azione consente di scaricare i file di fatturazione.
5. Selezionare **risorse**.
6. Selezionare **bucket – aggiungere ARN**.
7. Nelle **nome del Bucket**, immettere il bucket utilizzato per archiviare i file CUR.
8. Selezionare **oggetto – aggiungere ARN**.
9. Nelle **nome del Bucket**, immettere il bucket utilizzato per archiviare i file CUR.
10. Nelle **nome dell'oggetto**, selezionare **qualsiasi**.
11. Fare clic su **aggiungere le autorizzazioni aggiuntive**.

Configurare le autorizzazioni Esplora costi:

1. Fare clic su **scegliere un servizio**.
2. Tipo di _costo Service Explorer_.
3. Selezionare **tutti i costi Explorer Service actions (ce:\*)**. Questa azione verifica che la raccolta sia corretta.
4. Fare clic su **aggiungere le autorizzazioni aggiuntive**.

Aggiungere l'autorizzazione di organizzazioni:

1. Tipo di **organizzazioni**.
2. Selezionare **livello di accesso, elenco** > **ListAccounts**. Questa operazione Ottiene i nomi degli account.
3. Nelle **criteri di revisione**, immettere un nome per il nuovo criterio. Controllo per assicurarsi che immesso le informazioni corrette e quindi fare clic su **Crea criteri**.
4. Tornare alla scheda precedente e aggiornare la page del browser web. Nella barra di ricerca, cercare i nuovi criteri.
5. Selezionare **Avanti: revisione**.
6. Immettere un nome per il nuovo ruolo. Controllo per assicurarsi che immesso le informazioni corrette e quindi fare clic su **Create Role**.
    Si noti il **ARN del ruolo** e il **ID esterno** usati nei passaggi precedenti durante la creazione del ruolo. Si verranno utilizzati in un secondo momento quando si configura il connettore di gestione costi di Azure.

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

Usare le informazioni seguenti per creare un nuovo connettore AWS e iniziare a monitorare i costi AWS.

1. Accedi al portale di Azure al https://portal.azure.com.
2. Passare a **gestione e fatturazione dei costi** > **gestione costi**.
3. Sotto **le impostazioni**, fare clic su **Cloud connettori (anteprima)**.  
    ![Esempio che illustra i connettori di Cloud (impostazione di anteprima)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Fare clic su **+ Aggiungi** nella parte superiore della pagina per creare un nuovo connettore.
5. La creazione di una pagina di connettore AWS, digitare un **nome visualizzato** per assegnare un nome del connettore.  
    ![Esempio di creare una pagina di connettore di AWS](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Facoltativamente, selezionare il gruppo di gestione predefinito. Archivierà individuati tutti gli account collegati. È possibile configurarlo in un secondo momento.
7. Sotto il **fatturazione** sezione, selezionare **addebitato automaticamente il % 1 al momento della disponibilità generale** se si vuole garantire il funzionamento continuo quando scade l'anteprima. Se si seleziona l'opzione automatica, è necessario selezionare una fatturazione **sottoscrizione**.
8. Immettere il **ARN del ruolo**. È il valore usato quando si configura il ruolo in AWS.
9. Immettere il **ID esterno**. È il valore usato quando si configura il ruolo in AWS.
10. Immettere il **Nome Report** creato in AWS.
11. Fare clic su **successivo** e quindi fare clic su **crea**.

Potrebbe richiedere alcune ore per i nuovi ambiti AWS, l'Account AWS consolidati e account collegati AWS e i dati relativi ai costi venga visualizzato.

Dopo aver creato il connettore, è consigliabile assegnare il controllo di accesso al connettore. Gli utenti vengono assegnati autorizzazioni per gli ambiti appena individuati: AWS consolidati Account e AWS account collegati. L'utente che crea il connettore è il proprietario del connettore, account consolidati e account tutti collegati.

Assegnazione di autorizzazioni per i connettori agli utenti dopo l'individuazione viene eseguita non assegnare autorizzazioni per gli ambiti di AWS esistenti. In alternativa, solo i nuovi account collegati vengono assegnate autorizzazioni.

## <a name="additional-steps"></a>Passaggi aggiuntivi

- [Configurare i gruppi di gestione](../governance/management-groups/index.md#initial-setup-of-management-groups), se già stato fatto.
- Verificare che i nuovi ambiti vengono aggiunti per il selettore di ambito. Non dimenticare di fare clic su **Aggiorna** per visualizzare i dati più recenti.
- Nella pagina del connettore Cloud, selezionare il connettore e fare clic su **passare all'account di fatturazione** per assegnare l'account collegato ai gruppi di gestione.

## <a name="manage-cloud-connectors"></a>Gestire i connettori di cloud

Quando si seleziona un connettore nella pagina connettori Cloud, è possibile:

- Fare clic su **passare all'Account di fatturazione** per visualizzare le informazioni Account AWS consolidati.
- Fare clic su **controllo di accesso** per gestire l'assegnazione di ruolo per il connettore.
- Fare clic su **modifica** per aggiornare il connettore. È possibile modificare il numero di Account di AWS, così come appare l'ARN del ruolo. Tuttavia, è possibile creare un nuovo connettore.
- Fare clic su **Verify** per rieseguire il test di verifica per assicurarsi che Gestione costi può raccogliere i dati usando le impostazioni del connettore.

![Esempio che mostra l'elenco dei connettori AWS creati](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="role-of-azure-management-groups"></a>Ruolo dei gruppi di gestione di Azure

Per creare un'unica posizione per visualizzare le informazioni sul provider tra cloud, è necessario inserire le sottoscrizioni di Azure e account AWS collegato nello stesso gruppo di gestione. Se è già stato configurato l'ambiente di Azure con gruppi di gestione, vedere [configurazione dei gruppi di gestione iniziale](../governance/management-groups/index.md#initial-setup-of-management-groups).

Se si vuole separare i costi, è possibile creare un gruppo di gestione che contiene solo gli account AWS collegato.

## <a name="aws-consolidated-account"></a>Account AWS consolidati

L'account AWS consolidati viene usato per consolidare la fatturazione e pagamento per più account AWS. L'account AWS consolidati funge anche da un account di AWS collegato.

![Esempio di dettagli dell'Account di AWS consolidati](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

La pagina è possibile:

- Fare clic su **aggiornare** per l'aggiornamento in blocco l'associazione degli account AWS collegato a un gruppo di gestione.
- Fare clic su **controllo di accesso** per impostare l'assegnazione di ruolo per l'ambito.

### <a name="aws-consolidated-account-permissions"></a>Autorizzazioni dell'account AWS consolidati

Per impostazione predefinita, il AWS consolidati account le autorizzazioni vengono impostate al momento della creazione, in base alle autorizzazioni per i connettore AWS. Il creatore del connettore è il proprietario.

Il livello di accesso è gestire utilizzando la pagina di livello di accesso dell'account AWS consolidati. Tuttavia, le autorizzazioni per il AWS consolidati Account non vengono ereditati dagli account AWS collegato.

## <a name="aws-linked-account"></a>Account AWS collegato

L'account AWS collegata è in cui vengono create e gestite risorse AWS. Un account collegato funge anche da limite di sicurezza.

In questa pagina è possibile:

- Fare clic su **aggiornare** per aggiornare l'associazione dell'account AWS collegato con un gruppo di gestione.
- Fare clic su **controllo di accesso** per impostare un'assegnazione di ruolo per l'ambito.

![Esempio della pagina di AWS Account collegato](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="aws-linked-account-permissions"></a>Autorizzazioni dell'account AWS collegato

Per impostazione predefinita, le autorizzazioni dell'account AWS collegato sono impostate al momento della creazione, in base alle autorizzazioni per i connettore AWS. Il creatore del connettore è il proprietario. Gestire il livello di accesso utilizzando la pagina a livello di accesso dell'account AWS collegato. Autorizzazioni per l'account AWS consolidato non vengono ereditate dagli account AWS collegato.

Account AWS collegati ereditano sempre le autorizzazioni dal gruppo di gestione che a cui appartengono.

## <a name="next-steps"></a>Passaggi successivi

- Dopo aver impostato e configurato l'integrazione di report di utilizzo e costi di AWS, procedere al [utilizzo e costi AWS gestire](aws-integration-manage.md).
- Se non si ha familiarità con analisi dei costi, vedere [esplorare e analizzare i costi con l'analisi del costo](quick-acm-cost-analysis.md) Guida introduttiva.
- Se non si ha familiarità con i budget di Azure, vedere [crea e Gestisci i budget di Azure](tutorial-acm-create-budgets.md) esercitazione.
