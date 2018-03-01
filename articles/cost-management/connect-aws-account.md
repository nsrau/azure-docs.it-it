---
title: Connettere un account di Amazon Web Services a Gestione costi di Azure | Microsoft Docs
description: Connettere un account di Amazon Web Services per visualizzare i dati relativi ai costi e all'utilizzo nei report di Gestione costi.
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 02/08/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: a82d803b51859f809ca5a39ce177697a1f66a008
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="connect-an-amazon-web-services-account"></a>Connettere un account di Amazon Web Services

Per connettere un account di Amazon Web Services (AWS) a Gestione costi di Azure sono disponibili due opzioni. È possibile stabilire la connessione con un ruolo IAM (Identity & Access Management, gestione delle identità e degli accessi) o con un account utente IAM di sola lettura. È consigliabile usare il ruolo IAM perché consente di delegare l'accesso con autorizzazioni definite a entità attendibili. Usando il ruolo IAM non è necessario condividere le chiavi di accesso a lungo termine. Dopo la connessione di un account di AWS a Gestione costi, i dati relativi ai costi e all'utilizzo sono disponibili nei report di Gestione costi. Questo documento include le istruzioni da seguire per entrambe le opzioni.

Per altre informazioni sulle identità IAM di AWS, vedere [Identities (Users, Groups, and Roles)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html) (Identità: utenti, gruppi e ruoli).

## <a name="aws-role-based-access"></a>Accesso in base al ruolo AWS

Le sezioni seguenti illustrano le procedure per creare un ruolo IAM di sola lettura per fornire l'accesso a Gestione costi.

### <a name="get-your-cost-management-account-external-id"></a>Ottenere l'ID esterno dell'account di Gestione costi

Per prima cosa, è necessario ottenere dal portale di Gestione costi di Azure la passphrase di connessione univoca che viene usata in AWS come **ID esterno**.

1. Aprire il portale Cloudyn dal portale di Azure o passare a [https://azure.cloudyn.com](https://azure.cloudyn.com) e accedere.
2. Fare clic su **Settings** (Impostazioni) (icona con ruota dentata) e quindi selezionare **Cloud Accounts** (Account cloud).
3. In Accounts Management (Gestione account) selezionare la scheda **AWS Accounts** (Account AWS) e quindi fare clic su **Add new +** (Aggiungi nuovo +).
4. Nella finestra di dialogo **Add AWS Account** (Aggiungi account AWS) copiare l'**ID esterno** e salvare questo valore per usarlo nella procedura di creazione del ruolo AWS nella sezione successiva. Nella figura seguente è illustrato l'ID di esempio _Cloudyn_, ma l'ID effettivamente usato sarà diverso.  
    ![ID esterno](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Aggiungere l'accesso di sola lettura in base al ruolo AWS

1. Accedere alla console di AWS all'indirizzo https://console.aws.amazon.com/iam/home e selezionare **Roles** (Ruoli).
2. Fare clic su **Create Role** (Crea ruolo) e quindi selezionare **AWS account** (Account AWS).
3. Incollare l'identità `432263259397` nel campo **Account ID** (ID account). Questo è l'ID dell'account dell'agente di raccolta dati di Gestione costi che è necessario usare.
4. Accanto a **Options** (Opzioni) selezionare **Require external ID** (Richiedi ID esterno), incollare il valore copiato in precedenza nel campo **External ID** (ID esterno) e quindi fare clic su **Next: Permissions** (Avanti: Autorizzazioni).  
    ![Creare il ruolo](./media/connect-aws-account/create-role01.png)
5. In **Attach permissions policies** (Collega i criteri di autorizzazione), nella casella del filtro di ricerca **Policy type** (Tipo di criteri), digitare `ReadOnlyAccess`, selezionare **ReadOnlyAccess** e quindi fare clic su **Next: Review** (Avanti: Revisione).  
    ![Accesso in sola lettura](./media/connect-aws-account/readonlyaccess.png)
6. Nella pagina Review (Revisione) verificare che le selezioni siano corrette e digitare un valore in **Role name** (Nome ruolo). Ad esempio, *Azure-Cost-Mgt*. Immettere una descrizione in **Role description** (Descrizione ruolo). Ad esempio, _Assegnazione di ruolo per Gestione costi di Azure_ e quindi fare clic su **Create role** (Crea ruolo).
7. Nell'elenco **Roles** (Ruoli) fare clic sul ruolo creato e copiare il valore di **Role ARN** (ARN ruolo) dalla pagina Summary (Riepilogo). L'ARN (Amazon Resource Name) del ruolo sarà utile in un secondo momento, quando si registrerà la configurazione in Gestione costi di Azure.  
    ![ARN del ruolo](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cost-management"></a>Configurare l'accesso in base al ruolo IAM di AWS in Gestione costi

1. Aprire il portale Cloudyn dal portale di Azure o passare a https://azure.cloudyn.com/ e accedere.
2. Fare clic su **Settings** (Impostazioni) (icona con ruota dentata) e quindi selezionare **Cloud Accounts** (Account cloud).
3. In Accounts Management (Gestione account) selezionare la scheda **AWS Accounts** (Account AWS) e quindi fare clic su **Add new +** (Aggiungi nuovo +).
4. In **Account Name** (Nome account) digitare un nome per l'account.
5. Accanto a **Access Type** (Tipo di accesso) selezionare **IAM Role** (Ruolo IAM).
6. Nel campo **Role ARN** (ARN ruolo) incollare il valore copiato in precedenza e quindi fare clic su **Save** (Salva).  
    ![Stato dell'account AWS](./media/connect-aws-account/aws-account-status01.png)

L'account di AWS verrà visualizzato nell'elenco degli account. Il valore di **Owner ID** (ID proprietario) riportato nell'elenco corrisponde all'ARN del ruolo. In **Account Status** (Stato account) dovrebbe essere visualizzato un segno di spunta verde.

Gestione costi inizia a raccogliere i dati e a compilare i report. Alcuni report di ottimizzazione, tuttavia, potrebbero richiedere dati relativi ad alcuni giorni prima della generazione di indicazioni precise.

## <a name="aws-user-based-access"></a>Accesso in base all'utente di AWS

Le sezioni seguenti illustrano le procedure per creare un utente di sola lettura per fornire l'accesso a Gestione costi.

### <a name="add-aws-read-only-user-based-access"></a>Aggiungere l'accesso di sola lettura in base all'utente di AWS

1. Accedere alla console di AWS all'indirizzo https://console.aws.amazon.com/iam/home e selezionare **Users** (Utenti).
2. Fare clic su **Add User** (Aggiungi utente).
3. Nel campo **User name** (Nome utente) digitare un nome per l'utente.
4. Per **Access type** (Tipo di accesso) selezionare **Programmatic access** (Accesso a livello di codice) e fare clic su **Next: Permissions** (Avanti: Autorizzazioni).  
    ![Aggiungere l'utente](./media/connect-aws-account/add-user01.png)
5. Per le autorizzazioni, selezionare **Attach existing policies directly** (Collega direttamente i criteri esistenti).
6. In **Attach permissions policies** (Collega i criteri di autorizzazione), nella casella del filtro di ricerca **Policy type** (Tipo di criteri), digitare `ReadOnlyAccess`, selezionare **ReadOnlyAccess** e quindi fare clic su **Next: Review** (Avanti: Revisione).  
    ![Impostare le autorizzazioni per l'utente](./media/connect-aws-account/set-permission-for-user.png)
7. Nella pagina Review (Revisione) verificare che le selezioni siano corrette e quindi fare clic su **Create user** (Crea utente).
8. Nella pagina Complete (Procedura completata) vengono visualizzati l'ID della chiave di accesso e la chiave di accesso privata. Queste informazioni saranno utili per configurare la registrazione in Gestione costi.
9. Fare clic su **Download .csv** (Scarica csv) e salvare il file credentials.csv in una posizione sicura.  
    ![Scaricare le credenziali](./media/connect-aws-account/download-csv.png)


### <a name="configure-aws-iam-user-based-access-in-cost-management"></a>Configurare l'accesso in base all'utente IAM di AWS in Gestione costi

1. Aprire il portale Cloudyn dal portale di Azure o passare a https://azure.cloudyn.com/ e accedere.
2. Fare clic su **Settings** (Impostazioni) (icona con ruota dentata) e quindi selezionare **Cloud Accounts** (Account cloud).
3. In Accounts Management (Gestione account) selezionare la scheda **AWS Accounts** (Account AWS) e quindi fare clic su **Add new +** (Aggiungi nuovo +).
4. In **Account Name** (Nome account) digitare un nome per l'account.
5. Accanto a **Access Type** (Tipo di accesso) selezionare **IAM User** (Utente IAM).
6. In **Access Key** (Chiave di accesso) incollare il valore di **Access key ID** (ID chiave di accesso) riportato nel file credentials.csv.
7. In **Secret Key** (Chiave privata) incollare il valore di **Secret access key** (Chiave di accesso privata) riportato nel file credentials.csv e quindi fare clic su **Save** (Salva).  
    ![Stato dell'account utente di AWS](./media/connect-aws-account/aws-user-account-status.png)

L'account di AWS verrà visualizzato nell'elenco degli account. In **Account Status** (Stato account) dovrebbe essere visualizzato un segno di spunta verde.

Gestione costi inizia a raccogliere i dati e a compilare i report. Alcuni report di ottimizzazione, tuttavia, potrebbero richiedere dati relativi ad alcuni giorni prima della generazione di indicazioni precise.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su Gestione costi di Azure di Cloudyn, passare all'esercitazione [Esaminare l'utilizzo e i costi](tutorial-review-usage.md) per Gestione costi.
