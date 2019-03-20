---
title: Connettere un account di Amazon Web Services a Cloudyn in Azure | Microsoft Docs
description: Connettere un account di Amazon Web Services per visualizzare i dati relativi ai costi e all'utilizzo nei report di Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2018
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 411a3d606ac8ec2f262ec9a1aabac7b74ccd110a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58010941"
---
# <a name="connect-an-amazon-web-services-account"></a>Connettere un account di Amazon Web Services

Per connettere un account di Amazon Web Services (AWS) a Cloudyn sono disponibili due opzioni. È possibile stabilire la connessione con un ruolo IAM (Identity & Access Management, gestione delle identità e degli accessi) o con un account utente IAM di sola lettura. È consigliabile usare il ruolo IAM perché consente di delegare l'accesso con autorizzazioni definite a entità attendibili. Usando il ruolo IAM non è necessario condividere le chiavi di accesso a lungo termine. Dopo aver connesso un account AWS a Cloudyn, i costi e i dati di utilizzo sono disponibili nei report di Cloudyn. Questo documento include le istruzioni da seguire per entrambe le opzioni.

Per altre informazioni sulle identità IAM di AWS, vedere [Identities (Users, Groups, and Roles)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html) (Identità: utenti, gruppi e ruoli).

Occorre inoltre abilitare i report di fatturazione dettagliati di AWS e archiviare le informazioni in un bucket di Amazon Simple Storage Service (S3). I report di fatturazione dettagliati includono gli addebiti di fatturazione con informazioni su tag e risorse su base oraria. L'archiviazione dei report consente a Cloudyn di recuperarli dal bucket e di visualizzare le informazioni nei relativi report.


## <a name="aws-role-based-access"></a>Accesso in base al ruolo AWS

Le sezioni seguenti illustrano le procedure per creare un ruolo IAM di sola lettura per fornire l'accesso a Cloudyn.

### <a name="get-your-cloudyn-account-external-id"></a>Ottenere l'ID esterno dell'account Cloudyn

Per prima cosa, è necessario ottenere dal portale di Cloudyn la passphrase di connessione univoca che viene usata in AWS come **ID esterno**.

1. Aprire il portale di Cloudyn dal portale di Azure oppure passare a [https://azure.cloudyn.com](https://azure.cloudyn.com) ed eseguire l'accesso.
2. Fare clic sul simbolo della ruota dentata e quindi selezionare **Cloud Accounts** (Account cloud).
3. In Accounts Management (Gestione account) selezionare la scheda **AWS Accounts** (Account AWS) e quindi fare clic su **Add new +** (Aggiungi nuovo +).
4. Nella finestra di dialogo **Add AWS Account** (Aggiungi account AWS) copiare l'**ID esterno** e salvare il valore per usarlo nella procedura di creazione del ruolo AWS nella sezione successiva. L'ID esterno è univoco dell'account. Nell'immagine seguente l'ID esterno di esempio è _Contoso_ seguito da un numero. ma l'ID effettivamente usato sarà diverso.  
    ![ID esterno visualizzato nella finestra Add AWS Account (Aggiungi account AWS)](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Aggiungere l'accesso di sola lettura in base al ruolo AWS

1. Accedere alla console di AWS all'indirizzo https://console.aws.amazon.com/iam/home e selezionare **Roles** (Ruoli).
2. Fare clic su **Create Role** (Crea ruolo) e quindi selezionare **AWS account** (Account AWS).
3. Nella casella **Account ID** (ID account) incollare `432263259397`. Questo è l'ID dell'account dell'agente di raccolta dati di Cloudyn assegnato da AWS al servizio Cloudyn. Usare l'ID account esattamente com'è visualizzato.
4. Accanto a **Options** (Opzioni) selezionare **Require external ID** (Richiedi ID esterno). Incollare il valore univoco copiato in precedenza dal campo **External ID** (ID esterno) in Cloudyn. Fare quindi clic su **Next: Permissions** (Avanti: Autorizzazioni).  
    ![Incollare l'ID esterno da Cloudyn nella pagina Create role (Crea ruolo)](./media/connect-aws-account/create-role01.png)
5. In **Attach permissions policies** (Collega i criteri di autorizzazione), nella casella del filtro di ricerca **Policy type** (Tipo di criteri), digitare `ReadOnlyAccess`, selezionare **ReadOnlyAccess** e quindi fare clic su **Next: Review** (Avanti: Revisione).  
    ![Selezionare ReadOnlyAccess nell'elenco di nomi di criteri](./media/connect-aws-account/readonlyaccess.png)
6. Nella pagina Review (Revisione) verificare che le selezioni siano corrette e digitare un valore in **Role name** (Nome ruolo). Ad esempio, *Azure-Cost-Mgt*. Immettere una descrizione in **Role description** (Descrizione ruolo). Ad esempio, _Assegnazione di ruolo per Cloudyn_ e quindi fare clic su **Create role** (Crea ruolo).
7. Nell'elenco **Roles** (Ruoli) fare clic sul ruolo creato e copiare il valore di **Role ARN** (ARN ruolo) dalla pagina Summary (Riepilogo). L'ARN (Amazon Resource Name) del ruolo sarà utile in un secondo momento, quando si registrerà la configurazione in Cloudyn.  
    ![Copiare il valore di Role ARN (ARN del ruolo) dalla pagina Summary (Riepilogo)](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>Configurare l'accesso in base al ruolo IAM di AWS in Cloudyn

1. Aprire il portale di Cloudyn dal portale di Azure oppure passare a https://azure.cloudyn.com/ ed eseguire l'accesso.
2. Fare clic sul simbolo della ruota dentata e quindi selezionare **Cloud Accounts** (Account cloud).
3. In Accounts Management (Gestione account) selezionare la scheda **AWS Accounts** (Account AWS) e quindi fare clic su **Add new +** (Aggiungi nuovo +).
4. In **Account Name** (Nome account) digitare un nome per l'account.
5. Accanto a **Access Type** (Tipo di accesso) selezionare **IAM Role** (Ruolo IAM).
6. Nel campo **Role ARN** (ARN ruolo) incollare il valore copiato in precedenza e quindi fare clic su **Save** (Salva).  
    ![Incollare il valore di Role ARN (ARN del ruolo) nella casella Add AWS Account (Aggiungi account AWS)](./media/connect-aws-account/add-aws-account-box.png)


L'account di AWS verrà visualizzato nell'elenco degli account. Il valore di **Owner ID** (ID proprietario) riportato nell'elenco corrisponde all'ARN del ruolo. Nel campo **Account Status** (Stato account) deve essere visualizzato un simbolo di segno di spunta verde che indica che Cloudyn può accedere all'account AWS. Finché non si abilita la fatturazione AWS dettagliata, lo stato di consolidamento appare come **Standalone** (Autonomo).

![Stato dell'account AWS visualizzato nella pagina Accounts Management (Gestione degli account)](./media/connect-aws-account/aws-account-status01.png)

Cloudyn inizia a raccogliere i dati e a compilare i report. Quindi, [abilitare la fatturazione AWS dettagliata](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>Accesso in base all'utente di AWS

Le sezioni seguenti illustrano le procedure per creare un utente di sola lettura per fornire l'accesso a Cloudyn.

### <a name="add-aws-read-only-user-based-access"></a>Aggiungere l'accesso di sola lettura in base all'utente di AWS

1. Accedere alla console di AWS all'indirizzo https://console.aws.amazon.com/iam/home e selezionare **Users** (Utenti).
2. Fare clic su **Add User**.
3. Nel campo **User name** (Nome utente) digitare un nome per l'utente.
4. Per **Access type** (Tipo di accesso) selezionare **Programmatic access** (Accesso a livello di codice) e fare clic su **Next: Permissions** (Avanti: Autorizzazioni).  
    ![Immettere un nome utente nella pagina Add user (Aggiungi utente)](./media/connect-aws-account/add-user01.png)
5. Per le autorizzazioni, selezionare **Attach existing policies directly** (Collega direttamente i criteri esistenti).
6. In **Attach permissions policies** (Collega i criteri di autorizzazione), nella casella del filtro di ricerca **Policy type** (Tipo di criteri), digitare `ReadOnlyAccess`, selezionare **ReadOnlyAccess** e quindi fare clic su **Next: Review** (Avanti: Revisione).  
    ![Selezionare ReadOnlyAccess per impostare le autorizzazioni per l'utente](./media/connect-aws-account/set-permission-for-user.png)
7. Nella pagina Review (Revisione) verificare che le selezioni siano corrette e quindi fare clic su **Create user** (Crea utente).
8. Nella pagina Complete (Procedura completata) vengono visualizzati l'ID della chiave di accesso e la chiave di accesso privata. Queste informazioni saranno utili per configurare la registrazione in Cloudyn.
9. Fare clic su **Download .csv** (Scarica csv) e salvare il file credentials.csv in una posizione sicura.  
    ![Fare clic su Download .csv (Scarica csv) per salvare le credenziali](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>Configurare l'accesso in base all'utente IAM di AWS in Cloudyn

1. Aprire il portale di Cloudyn dal portale di Azure oppure passare a https://azure.cloudyn.com/ ed eseguire l'accesso.
2. Fare clic sul simbolo della ruota dentata e quindi selezionare **Cloud Accounts** (Account cloud).
3. In Accounts Management (Gestione account) selezionare la scheda **AWS Accounts** (Account AWS) e quindi fare clic su **Add new +** (Aggiungi nuovo +).
4. In **Account Name** (Nome account) digitare un nome per l'account.
5. Accanto a **Access Type** (Tipo di accesso) selezionare **IAM User** (Utente IAM).
6. In **Access Key** (Chiave di accesso) incollare il valore di **Access key ID** (ID chiave di accesso) riportato nel file credentials.csv.
7. In **Secret Key** (Chiave privata) incollare il valore di **Secret access key** (Chiave di accesso privata) riportato nel file credentials.csv e quindi fare clic su **Save** (Salva).  

L'account di AWS verrà visualizzato nell'elenco degli account. In **Account Status** (Stato account) dovrebbe essere visualizzato un segno di spunta verde.

Cloudyn inizia a raccogliere i dati e a compilare i report. Quindi, [abilitare la fatturazione AWS dettagliata](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Abilitare la fatturazione AWS dettagliata

Usare la procedura seguente per ottenere l'ARN del ruolo AWS. L'ARN del ruolo viene usato per concedere le autorizzazioni di lettura a un bucket di fatturazione.

1. Accedere alla console di AWS all'indirizzo https://console.aws.amazon.com e selezionare **Services** (Servizi).
2. Nella casella Service Search (Ricerca servizio) digitare *IAM* e selezionare l'opzione corrispondente.
3. Selezionare **Roles** (Ruoli) dal menu a sinistra.
4. Nell'elenco di ruoli selezionare il ruolo creato per l'accesso a Cloudyn.
5. Nella pagina Roles Summary (Riepilogo ruoli) fare clic per copiare il valore di **Role ARN** (ARN del ruolo). Tenere a portata di mano l'ARN del ruolo per i passaggi successivi.

### <a name="create-an-s3-bucket"></a>Creare un bucket S3

Occorre creare un bucket S3 in cui archiviare le informazioni di fatturazione dettagliate.

1. Accedere alla console di AWS all'indirizzo https://console.aws.amazon.com e selezionare **Services** (Servizi).
2. Nella casella Service Search (Ricerca servizio) digitare *S3* e selezionare **S3**.
3. Nella pagina Amazon S3 fare clic su **Create bucket** (Crea bucket).
4. Nella procedura guidata Create bucket (Crea bucket) scegliere un nome e un'area per il bucket e quindi fare clic su **Next** (Avanti).  
    ![Informazioni di esempio per la pagina Create bucket (Crea bucket)](./media/connect-aws-account/create-bucket.png)
5. Nella pagina **Set properties** (Imposta proprietà) mantenere i valori predefiniti e fare click **Next** (Avanti).
6. Nella pagina Review (Verifica) fare clic su **Create bucket** (Crea bucket). Viene visualizzato l'elenco dei bucket.
7. Fare clic sul bucket creato e selezionare la scheda **Permissions** (Autorizzazioni), quindi selezionare **Bucket Policy** (Criteri bucket). Si apre l'editor dei criteri di bucket.
8. Copiare l'esempio JSON seguente e incollarlo nell'editor dei criteri di bucket.
   - Sostituire `<BillingBucketName>` con il nome del bucket S3.
   - Sostituire `<ReadOnlyUserOrRole>` con l'ARN del ruolo o dell'utente copiato in precedenza.

   ```json
   {
    "Version": "2012-10-17",
    "Id": "Policy1426774604000",
    "Statement": [
        {
            "Sid": "Stmt1426774604000",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": [
                "s3:GetBucketAcl",
                "s3:GetBucketPolicy"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>"
        },
        {
            "Sid": "Stmt1426774604001",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        },
        {
            "Sid": "Stmt1426774604002",
            "Effect": "Allow",
            "Principal": {
                "AWS": "<ReadOnlyUserOrRole>"
            },
            "Action": [
                "s3:List*",
                "s3:Get*"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        }
    ]
   }
   ```

9. Fare clic su **Save**.  
    ![In Bucket policy editor (Editor criteri bucket) fare clic su Save (Salva)](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Abilitare i report di fatturazione AWS

Dopo aver creato e configurato il bucket S3, passare a [Billing Preferences](https://console.aws.amazon.com/billing/home?#/preference) (Preferenze di fatturazione) nella console di AWS.

1. Nella pagina Preferences (Preferenze) selezionare **Receive Billing Reports** (Ricevi report di fatturazione).
2. In **Receive Billing Reports** (Ricevi report di fatturazione) immettere il nome del bucket creato e fare clic su **Verify** (Verifica).  
3. Selezionare tutte e quattro le opzioni di granularità dei report e quindi fare clic su **Save preferences** (Salva preferenze).  
    ![Selezionare la granularità per abilitare i report](./media/connect-aws-account/enable-reports.png)

Cloudyn recupera le informazioni di fatturazione dettagliate dal bucket S3 e popola i report dopo che la fatturazione dettagliata è stata abilitata. Possono trascorrere fino a 24 ore prima che i dati sulla fatturazione dettagliata vengano visualizzati nella console Cloudyn. Una volta disponibili questi dati, lo stato di consolidamento dell'account appare come **Consolidated** (Consolidato). Lo stato dell'account risulta invece **Completed** (Completato).

![Stato di consolidamento visualizzato nella scheda AWS Accounts (Account AWS)](./media/connect-aws-account/consolidated-status.png)

Per alcuni report di ottimizzazione può essere necessario raccogliere i dati di alcuni giorni per ottenere una quantità di dati di esempio adeguata per produrre suggerimenti accurati.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su Cloudyn, passare all'esercitazione [Esaminare uso e costi](tutorial-review-usage.md) per Cloudyn.
