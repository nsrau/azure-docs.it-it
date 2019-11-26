---
title: Configurare e configurare l'integrazione dei report relativi a costi e utilizzo di AWS con gestione costi di Azure
description: Questo articolo illustra come configurare e configurare l'integrazione dei report di utilizzo e costi di AWS con gestione costi di Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 66dbe45ff1a8ee773fdf7fcb0aa7cfe8e6ad6437
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219685"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Configurare e configurare l'integrazione dei report di utilizzo e costi di AWS

Con l'integrazione di Amazon Web Services (AWS) cost and Usage (CUR), è possibile monitorare e controllare la spesa AWS in gestione costi di Azure. L'integrazione consente un'unica posizione nel portale di Azure in cui è possibile monitorare e controllare la spesa per Azure e AWS. Questo articolo illustra come configurare l'integrazione e configurarla in modo da poter usare le funzionalità di gestione dei costi di Azure per analizzare i costi ed esaminare i budget.

Gestione costi elabora il report di utilizzo e costi di AWS archiviato in un bucket S3 usando le credenziali di accesso di AWS per ottenere le definizioni dei report e scaricare i file CSV del report GZIP.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Creare un report sui costi e sull'utilizzo in AWS

L'uso di un report costi e utilizzo è la modalità consigliata di AWS per raccogliere ed elaborare i costi AWS. Per ulteriori informazioni, vedere la documentazione relativa al [report sui costi e sull'utilizzo di AWS](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) .

Usare la pagina **report sui costi di utilizzo di &** della console di gestione della fatturazione e dei costi in AWS per creare un report di costo e utilizzo con i passaggi seguenti:

1. Accedere alla console di gestione di AWS e aprire la [console di gestione costi e fatturazione](https://console.aws.amazon.com/billing).
2. Nel riquadro di spostamento selezionare **Cost & usage reports**.
3. Selezionare **Crea report**.
4. Per **nome report**immettere un nome per il report.
5. In **Dettagli report aggiuntivi**selezionare **Includi ID risorsa**.
6. Per **le impostazioni di aggiornamento dati**, scegliere se si vuole che il report di utilizzo e costi di AWS venga aggiornato se AWS applica i rimborsi, i crediti o i costi di supporto all'account dopo aver completato la fatturazione. Quando un report viene aggiornato, viene caricato un nuovo report in Amazon S3. È consigliabile lasciare selezionata l'opzione.
7. Selezionare **Avanti**.
8. Per il **bucket S3**, scegliere **Configura**.
9. Nella finestra di dialogo Configura bucket S3 eseguire una delle attività seguenti:
    1. Selezionare un bucket esistente dall'elenco a discesa e fare clic su **Avanti**.
    2. Immettere un nome di bucket e l'area in cui si vuole creare un nuovo bucket, quindi scegliere **Avanti**.
10. Selezionare **conferma che questo criterio è corretto**, quindi fare clic su **Salva**.
11. Opzionale Per prefisso percorso report immettere il prefisso del percorso del report che si desidera anteporre al nome del report.
Se non si specifica un prefisso, il prefisso predefinito è il nome specificato per il report. L'intervallo di date ha il formato `/report-name/date-range/`.
12. Per **unità di tempo**scegliere **orario**.
13. Per il **controllo delle versioni dei report**, scegliere se si desidera che in ogni versione del report venga sovrascritta la versione precedente o se si desiderano nuovi report aggiuntivi.
14. Per **abilitare l'integrazione dei dati per**, non è necessaria alcuna selezione.
15. Per la **compressione**selezionare **gzip**.
16. Selezionare **Avanti**.
17. Dopo aver esaminato le impostazioni per il report, selezionare **Verifica e completa**.

    Prendere nota del nome del report. Verrà usato nei passaggi successivi.

Potrebbero essere necessarie fino a 24 ore prima che AWS avvii il recapito dei report nel bucket Amazon S3. Dopo l'avvio del recapito, AWS aggiorna i file di report relativi a costi e utilizzo di AWS almeno una volta al giorno. È possibile continuare a configurare l'ambiente AWS senza attendere l'avvio del recapito.

## <a name="create-a-role-and-policy-in-aws"></a>Creare un ruolo e un criterio in AWS

Gestione costi di Azure accede al bucket S3 in cui il report di costo e utilizzo si trova più volte al giorno. Il servizio deve accedere alle credenziali per verificare la presenza di nuovi dati. È possibile creare un ruolo e un criterio in AWS per consentire a gestione costi di accedervi.

Per abilitare l'accesso in base al ruolo a un account AWS in gestione costi, il ruolo viene creato nella console di AWS. È necessario avere il _ruolo ARN_ e l' _ID esterno_ dalla console di AWS. Successivamente, è possibile usarli nella pagina **creare un connettore AWS** in gestione costi.

Utilizzare la procedura guidata Crea nuovo ruolo:

1. Accedere alla console di AWS e selezionare **Servizi**.
2. Nell'elenco dei servizi selezionare **IAM**.
3. Selezionare **ruoli** e quindi fare clic su **Crea ruolo**.
4. Nella pagina successiva selezionare **un altro account AWS**.
5. In **ID account**immettere **432263259397**.
6. In **Opzioni**selezionare **Richiedi ID esterno (procedura consigliata quando il ruolo verrà assunto da terze parti)** .
7. In **External ID (ID esterno**) immettere l'ID esterno. L'ID esterno è un codice condiviso tra il ruolo AWS e gestione costi di Azure. Lo stesso ID esterno viene usato anche nella pagina **nuovo connettore** in gestione costi. Ad esempio, un ID esterno è simile a _Companyname1234567890123_.

    > [!NOTE]
    > Non modificare la selezione per **Richiedi**autenticazione a più fattori. Deve rimanere cancellata.
8. Selezionare **Next: Permissions**.
9. Selezionare **Create policy** (Crea criterio). Si apre una nuova scheda del browser. Qui è possibile creare un criterio.
10. Selezionare **Scegli un servizio**.

Configurare l'autorizzazione per il report costi e utilizzo:

1. Immettere il **report sui costi e sull'utilizzo**.
2. Selezionare **livello di accesso** > **leggere** > **DescribeReportDefinitions**. Questo passaggio consente a gestione costi di leggere i report CUR definiti e determinare se corrispondono al prerequisito per la definizione del report.
3. Selezionare **Aggiungi autorizzazioni aggiuntive**.

Configurare l'autorizzazione per il bucket S3 e gli oggetti:

1. Selezionare **Scegli un servizio**.
2. Immettere **S3**.
3. Selezionare > **livello di accesso** **elenco** > **ListBucket**. Questa azione ottiene l'elenco di oggetti nel bucket S3.
4. Selezionare il **livello di accesso** > **leggere** > **GetObject**. Questa azione consente il download dei file di fatturazione.
5. Selezionare **risorse**.
6. Selezionare **bucket: aggiungere ARN**.
7. In **nome bucket**immettere il bucket usato per archiviare i file cur.
8. Selezionare **oggetto: aggiungere ARN**.
9. In **nome bucket**immettere il bucket usato per archiviare i file cur.
10. In **nome oggetto**selezionare **any**.
11. Selezionare **Aggiungi autorizzazioni aggiuntive**.

Configurare l'autorizzazione per Esplora costi:

1. Selezionare **Scegli un servizio**.
2. Immettere il **servizio cost Explorer**.
3. Selezionare **tutte le azioni del servizio cost Explorer (CE:\*)** . Questa azione convalida la correttezza della raccolta.
4. Selezionare **Aggiungi autorizzazioni aggiuntive**.

Aggiungere l'autorizzazione per le organizzazioni AWS:

1. Immettere le **organizzazioni**.
2. Selezionare > **livello di accesso** **elenco** > **ListAccounts**. Questa azione ottiene i nomi degli account.
3. In **Verifica criterio**immettere un nome per il nuovo criterio. Verificare di aver immesso le informazioni corrette e quindi selezionare **Crea criterio**.
4. Tornare alla scheda precedente e aggiornare la pagina Web del browser. Nella barra di ricerca cercare il nuovo criterio.
5. Selezionare **Successivo: Revisione**.
6. Immettere un nome per il nuovo ruolo. Verificare di aver immesso le informazioni corrette, quindi selezionare **Crea ruolo**.

    Si noti il ruolo ARN e l'ID esterno usati nei passaggi precedenti quando è stato creato il ruolo. Verranno usati in un secondo momento quando si configura il connettore di gestione costi di Azure.

Il codice JSON dei criteri dovrebbe essere simile all'esempio seguente. Sostituire _bucketname_ con il nome del bucket S3.

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

Usare le informazioni seguenti per creare un connettore AWS e iniziare a monitorare i costi di AWS:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare a **Gestione costi + fatturazione** > **Gestione costi**.
3. In **Impostazioni**selezionare **connettori cloud (anteprima)** .  
    ![esempio che illustra l'impostazione dei connettori cloud (anteprima)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Selezionare **+ Aggiungi** nella parte superiore della pagina per creare un connettore.
5. Nella pagina **Crea un connettore AWS** , in **nome visualizzato**, immettere un nome per il connettore.  
    ![esempio della pagina per la creazione di un connettore AWS](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Facoltativamente, selezionare il gruppo di gestione predefinito. Tutti gli account collegati individuati vengono archiviati. È possibile configurarlo in un secondo momento.
7. Nella sezione **fatturazione** selezionare **addebita automaticamente l'1% in fase di disponibilità generale** se si desidera garantire un'operazione continua alla scadenza dell'anteprima. Se si seleziona l'opzione automatica, è necessario selezionare una sottoscrizione per la fatturazione.
8. Per **Role ARN**, immettere il valore usato durante la configurazione del ruolo in AWS.
9. Per **ID esterno**, immettere il valore usato durante la configurazione del ruolo in AWS.
10. Per **nome report**immettere il nome creato in AWS.
11. Selezionare **Avanti** e quindi fare clic su **Crea**.

Potrebbero essere necessarie alcune ore per visualizzare i nuovi ambiti AWS, l'account consolidato AWS, gli account collegati AWS e i relativi dati relativi ai costi.

Dopo aver creato il connettore, è consigliabile assegnarvi il controllo di accesso. Agli utenti vengono assegnate le autorizzazioni per gli ambiti appena individuati: account consolidato AWS e account collegati AWS. L'utente che crea il connettore è il proprietario del connettore, l'account consolidato e tutti gli account collegati.

L'assegnazione delle autorizzazioni del connettore agli utenti dopo l'individuazione non consente di assegnare le autorizzazioni agli ambiti AWS esistenti. Al contrario, vengono assegnate le autorizzazioni solo per i nuovi account collegati.

## <a name="take-additional-steps"></a>Eseguire passaggi aggiuntivi

- [Configurare i gruppi di gestione](../governance/management-groups/overview.md#initial-setup-of-management-groups), se non è già stato fatto.
- Verificare che i nuovi ambiti vengano aggiunti alla selezione ambito. Selezionare **Aggiorna** per visualizzare i dati più recenti.
- Nella pagina **connettori cloud** selezionare il connettore e selezionare **Vai a account di fatturazione** per assegnare l'account collegato ai gruppi di gestione.

## <a name="manage-cloud-connectors"></a>Gestire i connettori cloud

Quando si seleziona un connettore nella pagina **connettori cloud** , è possibile:

- Selezionare **Vai a account di fatturazione** per visualizzare le informazioni relative all'account consolidato AWS.
- Selezionare **controllo di accesso** per gestire l'assegnazione di ruolo per il connettore.
- Selezionare **modifica** per aggiornare il connettore. Non è possibile modificare il numero di account AWS perché viene visualizzato nel ruolo ARN. È tuttavia possibile creare un nuovo connettore.
- Selezionare **Verifica** per eseguire nuovamente il test di verifica per assicurarsi che gestione costi possa raccogliere dati usando le impostazioni del connettore.

![Elenco di esempio di connettori AWS creati](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>Configurare i gruppi di gestione di Azure

Inserire le sottoscrizioni di Azure e gli account collegati AWS nello stesso gruppo di gestione per creare un'unica posizione in cui è possibile visualizzare le informazioni sul provider tra cloud. Se l'ambiente Azure non è già stato configurato con i gruppi di gestione, vedere [configurazione iniziale dei gruppi di gestione](../governance/management-groups/overview.md#initial-setup-of-management-groups).

Se si desidera separare i costi, è possibile creare un gruppo di gestione che contenga solo account collegati AWS.

## <a name="set-up-an-aws-consolidated-account"></a>Configurare un account consolidato di AWS

L'account consolidato AWS combina fatturazione e pagamenti per più account AWS. Funge anche da account collegato di AWS.

![Dettagli di esempio per un account consolidato AWS](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

Dalla pagina è possibile:

- Selezionare **aggiornamento** per eseguire l'aggiornamento in blocco dell'associazione degli account collegati AWS a un gruppo di gestione.
- Selezionare **controllo di accesso** per impostare l'assegnazione di ruolo per l'ambito.

### <a name="permissions-for-an-aws-consolidated-account"></a>Autorizzazioni per un account consolidato AWS

Per impostazione predefinita, le autorizzazioni per un account consolidato AWS vengono impostate alla creazione dell'account, in base alle autorizzazioni del connettore AWS. Il creatore del connettore è il proprietario.

Per gestire il livello di accesso, usare la pagina **livello di accesso** dell'account consolidato AWS. Tuttavia, gli account collegati AWS non ereditano le autorizzazioni per l'account consolidato AWS.

## <a name="set-up-an-aws-linked-account"></a>Configurare un account collegato di AWS

L'account collegato di AWS è il punto in cui vengono create e gestite le risorse di AWS. Un account collegato funge anche da limite di sicurezza.

Da questa pagina è possibile:

- Selezionare **Aggiorna** per aggiornare l'associazione di un account collegato di AWS a un gruppo di gestione.
- Selezionare **controllo di accesso** per impostare un'assegnazione di ruolo per l'ambito.

![Esempio della pagina dell'account collegato di AWS](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>Autorizzazioni per un account collegato di AWS

Per impostazione predefinita, le autorizzazioni per un account collegato di AWS vengono impostate al momento della creazione, in base alle autorizzazioni del connettore AWS. Il creatore del connettore è il proprietario. Per gestire il livello di accesso, usare la pagina **livello di accesso** dell'account collegato di AWS. Gli account collegati AWS non ereditano le autorizzazioni da un account consolidato AWS.

Gli account collegati di AWS ereditano sempre le autorizzazioni dal gruppo di gestione a cui appartengono.

## <a name="next-steps"></a>Passaggi successivi

- Ora che è stata impostata e configurata l'integrazione dei report di utilizzo e costi di AWS, continuare a [gestire i costi e l'utilizzo di AWS](aws-integration-manage.md).
- Se non si ha familiarità con l'analisi dei costi, vedere [esplorare e analizzare i costi con la Guida introduttiva all'analisi dei costi](quick-acm-cost-analysis.md) .
- Se non si ha familiarità con i budget in Azure, vedere [creare e gestire i budget di Azure](tutorial-acm-create-budgets.md).
