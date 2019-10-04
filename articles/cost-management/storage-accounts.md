---
title: Configurare gli account di archiviazione per Cloudyn di Azure | Microsoft Docs
description: Questo articolo descrive come configurare gli account di archiviazione di Azure e i contenitori di archiviazione di AWS per Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: secdec18
ms.openlocfilehash: 91377c41699f01eaf57a085ea82e9d7289549990
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65969152"
---
# <a name="configure-storage-accounts-for-cloudyn"></a>Configurare gli account di archiviazione per Cloudyn

<!--- intent: As a Cloudyn user, I want to configure Cloudyn to use my cloud service provider storage account to store my reports. -->

È possibile salvare i report di Cloudyn nel portale di Cloudyn, nell'archiviazione di Azure o nei contenitori di archiviazione di AWS. Il salvataggio dei report nel portale di Cloudyn è gratuito. Il salvataggio dei report in una risorsa di archiviazione del provider di servizi cloud è tuttavia facoltativo e comporta un costo aggiuntivo. Questo articolo consente di configurare gli account di archiviazione di Azure e i bucket di archiviazione di Amazon Web Services (AWS) per l'archiviazione dei report.

## <a name="prerequisites"></a>Prerequisiti

È necessario un account di archiviazione di Azure o un bucket di archiviazione di Amazon.

Se non si ha un account di archiviazione di Azure, è necessario crearne uno. Per altre informazioni sulla creazione di un account di archiviazione di Azure, vedere [Creare un account di archiviazione](../storage/common/storage-quickstart-create-account.md).

Se non si ha un bucket di archiviazione di AWS Simple Storage Service (S3), è necessario crearne uno. Per altre informazioni sulla creazione di un bucket di S3, vedere [Create a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) (Creare un bucket).

## <a name="configure-your-azure-storage-account"></a>Configurare l'account di archiviazione di Azure

La configurazione dell'archiviazione di Azure per l'uso di Cloudyn è semplice. Raccogliere informazioni dettagliate sull'account di archiviazione e copiarle nel portale di Cloudyn.

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
2. Fare clic su **Tutti i servizi**, selezionare **Account di archiviazione**, scorrere fino all'account di archiviazione da usare e quindi selezionarlo.
3. Nella pagina dell'account di archiviazione in **Impostazioni** fare clic su **Chiavi di accesso**.
4. Copiare il **Nome account di archiviazione** e la **Stringa di connessione** in key1.  
   ![Copiare il nome dell'account di archiviazione e la stringa di connessione](./media/storage-accounts/azure-storage-access-keys.png)  
5. Aprire il portale di Cloudyn dal portale di Azure oppure passare a https://azure.cloudyn.com ed eseguire l'accesso.
6. Fare clic sul simbolo con ruota dentata e quindi selezionare **Reports Storage Management** (Gestione archiviazione rapporti).
7. Fare clic su **Add new +** (Aggiungi nuovo +) e verificare che sia selezionato Microsoft Azure. Incollare il nome dell'account di archiviazione di Azure nell'area **Nome**. Incollare la **stringa di connessione** nell'area corrispondente. Immettere un nome di contenitore e quindi fare clic su **Salva**.  
   ![Incollare il nome dell'account di archiviazione di Azure e la stringa di connessione nella casella per aggiungere una nuova archiviazione per i report](./media/storage-accounts/azure-cloudyn-storage.png)

   La nuova voce di archiviazione dei report di Azure viene visualizzata nell'elenco degli account di archiviazione.  
    ![Nuova voce di archiviazione dei report di Azure nell'elenco](./media/storage-accounts/azure-storage-entry.png)


È ora possibile salvare i report nell'archiviazione di Azure. In qualsiasi report fare clic su **Azioni** e quindi selezionare **Schedule report** (Pianifica report). Assegnare un nome al report e quindi aggiungere il proprio URL o usare quello creato automaticamente. Selezionare **Save to storage** (Salva in risorsa di archiviazione) e quindi selezionare l'account di archiviazione. Immettere un prefisso che viene aggiunto al nome del file di report. Selezionare un formato di file CSV o JSON e quindi salvare il report.

## <a name="configure-an-aws-storage-bucket"></a>Configurare un bucket di archiviazione di AWS

Cloudyn usa le credenziali AWS esistenti, Utente o Ruolo, per salvare i report nel bucket. Per verificare l'accesso, Cloudyn tenta di salvare un file di testo di piccole dimensioni nel bucket con il nome file _check-bucket-permission.txt_.

Specificare il ruolo o l'utente di Cloudyn con l'autorizzazione PutObject per il bucket. Per salvare i report, usare quindi un bucket esistente o crearne uno nuovo. Decidere infine come gestire la classe di archiviazione, impostare le regole del ciclo di vita o rimuovere i file non necessari.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Assegnare autorizzazioni al ruolo o all'utente di AWS

Quando si crea un nuovo criterio, specificare le autorizzazioni esatte necessarie per salvare un report in un bucket di S3.

1. Accedere alla console di AWS e selezionare **Services** (Servizi).
2. Dall'elenco dei servizi selezionare **IAM**.
3. Sul lato sinistro della console selezionare **Policies** (Criteri) e quindi fare clic su **Create Policy** (Crea criterio).
4. Fare clic sulla scheda **JSON**.
5. Il criterio seguente consente di salvare un report in un bucket di S3. Copiare e incollare l'esempio di criterio seguente nella scheda **JSON**. Sostituire &lt;bucketname&gt; con il nome del bucket.

   ```json
   {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Sid":  "CloudynSaveReport2S3",
        "Effect":      "Allow",
        "Action": [
          "s3:PutObject"
        ],
        "Resource": [
          "arn:aws:s3:::<bucketname>/*"
        ]
      }
    ]
   }
   ```

6. Fare clic su **Review policy** (Rivedi criterio).  
    ![Criterio JSON AWS con informazioni di esempio](./media/storage-accounts/aws-policy.png)  
7. Nella pagina per la revisione del criterio digitare un nome per il criterio. Ad esempio _CloudynSaveReport2S3_.
8. Fare clic su **Create policy** (Crea criterio).

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>Collegare il criterio a un ruolo o a un utente di Cloudyn nell'account

Per collegare il nuovo criterio, aprire la console di AWS e modificare il ruolo o l'utente di Cloudyn.

1. Accedere alla console di AWS, selezionare **Services** (Servizi) e quindi dall'elenco di servizi scegliere **IAM**.
2. Nel lato sinistro della console selezionare **Roles** (Ruoli) or **Users** (Utenti).

**Per i ruoli:**

  1. Fare clic sul nome del ruolo di Cloudyn.
  2. Nella scheda **Permissions** (Autorizzazioni) fare clic su **Attach Policy** (Collega criterio).
  3. Cercare il criterio creato, selezionarlo e quindi fare clic su **Attach Policy** (Collega criterio).
    ![Criterio di esempio collegato al ruolo di Cloudyn](./media/storage-accounts/aws-attach-policy-role.png)

**Per gli utenti:**

1. Selezionare l'utente di Cloudyn.
2. Nella scheda **Permissions** (Autorizzazioni) fare clic su **Add permissions** (Aggiungi autorizzazioni).
3. Nella sezione **Grant Permission** (Concedi autorizzazione) selezionare **Attach existing policies directly** (Collega direttamente i criteri esistenti).
4. Cercare il criterio creato, selezionarlo e quindi fare clic su **Next: Review** (Avanti: Rivedi).
5. Nella pagina per l'aggiunta delle autorizzazioni al nome del ruolo fare clic su **Add permissions** (Aggiungi autorizzazioni).  
    ![Criterio di esempio collegato all'utente di Cloudyn](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Facoltativo: impostare le autorizzazioni con un criterio di bucket

È anche possibile impostare le autorizzazioni per creare report nel bucket di S3 usando un criterio di bucket. Nella visualizzazione classica di S3:

1. Creare un bucket o selezionarne uno esistente.
2. Selezionare la scheda **Permissions** (Autorizzazioni) e quindi fare clic su **Bucket policy** (Criterio di bucket).
3. Copiare e incollare l'esempio di criterio seguente. Sostituire &lt;bucket\_name&gt; e &lt;Cloudyn\_principle&gt; con l'ARN del bucket. Sostituire l'ARN del ruolo o dell'utente usato da Cloudyn.

   ```
   {
   "Id": "Policy1485775646248",
   "Version": "2012-10-17",
   "Statement": [
    {
      "Sid": "SaveReport2S3",
      "Action": [
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": "<bucket_name>/*",
      "Principal": {
        "AWS": [
          "<Cloudyn_principle>"
        ]
      }
    }
   ]
   }
   ```

4. Nell'editor del criterio di bucket fare clic su **Save** (Salva).

### <a name="add-aws-report-storage-to-cloudyn"></a>Aggiungere una risorsa di archiviazione dei report di AWS in Cloudyn

1. Aprire il portale di Cloudyn dal portale di Azure oppure passare a https://azure.cloudyn.com ed eseguire l'accesso.
2. Fare clic sul simbolo con ruota dentata e quindi selezionare **Reports Storage Management** (Gestione archiviazione rapporti).
3. Fare clic su **Add new +** (Aggiungi nuovo +) e verificare che sia selezionato AWS.
4. Selezionare un account e un bucket di archiviazione. Il nome del bucket di archiviazione di AWS viene inserito automaticamente.  
    ![Informazioni di esempio nella finestra per aggiungere una nuova archiviazione dei report](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Fare clic su **Save** (Salva) e quindi su **OK**.

    La nuova voce di archiviazione dei report di AWS viene visualizzata nell'elenco degli account di archiviazione.  
    ![Nuova voce di archiviazione dei report di AWS visualizzata nell'elenco degli account di archiviazione](./media/storage-accounts/aws-storage-entry.png)


È ora possibile salvare i report nell'archiviazione di Azure. In qualsiasi report fare clic su **Azioni** e quindi selezionare **Schedule report** (Pianifica report). Assegnare un nome al report e quindi aggiungere il proprio URL o usare quello creato automaticamente. Selezionare **Save to storage** (Salva in risorsa di archiviazione) e quindi selezionare l'account di archiviazione. Immettere un prefisso che viene aggiunto al nome del file di report. Selezionare un formato di file CSV o JSON e quindi salvare il report.

## <a name="next-steps"></a>Passaggi successivi

- Per comprendere le funzioni e la struttura di base dei report di Cloudyn, vedere [Informazioni sui report di Cloudyn](understanding-cost-reports.md).
