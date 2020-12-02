---
title: Configurare l'integrazione di AWS con Gestione costi di Azure
description: Questo articolo illustra come impostare e configurare l'integrazione del report di costi e utilizzo di AWS con Gestione costi di Azure.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: matrive
ms.openlocfilehash: 2b8a008decc41a5686fb2c8d9fee271f95f0fef3
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122411"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Impostare e configurare l'integrazione del report di costi e utilizzo di AWS

Grazie all'integrazione del report di costi e utilizzo di Amazon Web Services (AWS) è possibile monitorare e controllare la spesa AWS in Gestione costi di Azure. L'integrazione consente di monitorare e controllare la spesa per Azure e AWS in un'unica posizione nel portale di Azure. Questo articolo illustra come impostare l'integrazione e configurarla in modo da poter usare le funzionalità di Gestione costi di Azure per analizzare i costi e rivedere i budget.

Gestione costi elabora il report di costi e utilizzo di AWS archiviato in un bucket S3 usando le credenziali di accesso di AWS per ottenere le definizioni del report e scaricare i file CSV GZIP del report.

Per informazioni su come configurare l'integrazione di report AWS, guardare il video su [come configurare i connettori per AWS in Gestione costi](https://www.youtube.com/watch?v=Jg5KC1cx5cA). Per guardare altri video, visitare il [canale YouTube di Gestione costi](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/Jg5KC1cx5cA]

## <a name="create-a-cost-and-usage-report-in-aws"></a>Creare un report di costi e utilizzo in AWS

L'uso di un report di costi e utilizzo è la modalità consigliata da AWS per raccogliere ed elaborare i costi di AWS. Per altre informazioni, vedere la documentazione del [Report di costi e utilizzo di AWS](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html).

Usare la pagina **Cost & Usage Reports** (Report di costi e utilizzo) della console Billing and Cost Management in AWS per creare un report di costi e utilizzo seguendo questa procedura:

1. Accedere alla Console di gestione AWS e aprire la [console Billing and Cost Management](https://console.aws.amazon.com/billing).
2. Nel riquadro di spostamento selezionare **Cost & Usage Reports** (Report di costi e utilizzo).
3. Selezionare **Create report** (Crea report).
4. In **Report name** (Nome report) immettere un nome per il report.
5. In **Additional report details** (Ulteriori dettagli del report) selezionare **Include resource IDs** (Includi ID risorse).
6. In **Data refresh settings** (Impostazioni aggiornamento dati) selezionare se si vuole aggiornare il report di costi e utilizzo di AWS quando quest'ultimo applica all'account rimborsi, crediti o costi di supporto dopo la finalizzazione della fattura. Quando un report viene aggiornato, un nuovo report viene caricato in Amazon S3. È consigliabile lasciare selezionata l'impostazione.
7. Selezionare **Avanti**.
8. Per **S3 bucket** (Bucket S3), scegliere **Configure** (Configura).
9. Nella finestra di dialogo Configure S3 Bucket (Configura bucket S3) immettere un nome per il bucket e l'area in cui si vuole creare un nuovo bucket, quindi scegliere **Next** (Avanti).
10. Selezionare **I have confirmed that this policy is correct** (Ho confermato che il criterio è corretto) e scegliere **Save** (Salva).
11. (Facoltativo) In Report path prefix (Prefisso percorso report) immettere il prefisso del percorso del report che si vuole anteporre al nome del report.
Se non si specifica un prefisso, quello predefinito è il nome specificato per il report. L'intervallo di date ha il formato `/report-name/date-range/`.
12. In **Time unit** (Unità di tempo) scegliere  **Hourly** (Ogni ora).
13. In **Report versioning** (Controllo delle versioni del report) scegliere se si vuole che ogni versione del report sovrascriva quella precedente o se si vuole creare nuovi report aggiuntivi.
14. In **Enable data integration for** (Abilita integrazione dei dati per) non è necessaria alcuna selezione.
15. In **Compression** (Compressione) selezionare **GZIP**.
16. Selezionare **Avanti**.
17. Dopo aver esaminato le impostazioni per il report, selezionare **Review and Complete** (Verifica e completa).

    Prendere nota del nome del report. Verrà usato nei passaggi successivi.

Possono essere necessarie fino a 24 ore prima che AWS inizi a recapitare i report nel bucket Amazon S3. Dopo l'avvio del recapito, AWS aggiorna i file del report di costi e utilizzo di AWS almeno una volta al giorno. È possibile continuare a configurare l'ambiente AWS senza attendere l'avvio del recapito.

## <a name="create-a-role-and-policy-in-aws"></a>Creare un ruolo e un criterio in AWS

Gestione costi di Azure accede più volte al giorno al bucket S3 in cui si trova il report di costi e utilizzo. Il servizio necessita dell'accesso alle credenziali per verificare la presenza di nuovi dati. Per consentire a Gestione costi di accedere, creare un ruolo e un criterio in AWS.

Per abilitare l'accesso in base al ruolo a un account AWS in Gestione costi, il ruolo viene creato nella console di AWS. È necessario ottenere il _ruolo ARN_ e l'_ID esterno_ dalla console di AWS. Verranno usati più avanti nella pagina **Crea un connettore AWS** in Gestione costi.

Usare la procedura guidata Create a New Role (Crea un nuovo ruolo):

1. Accedere alla console di AWS e selezionare **Services** (Servizi).
2. Nell'elenco dei servizi selezionare **IAM**.
3. Selezionare **Roles** (Ruoli) e quindi **Create Role** (Crea ruolo).
4. Nella pagina successiva selezionare **Another AWS account** (Un altro account AWS).
5. In **Account ID** (ID account) immettere **432263259397**.
6. In **Options** (Opzioni) selezionare **Require external ID** (Richiedi ID esterno). Procedura consigliata quando una terza parte assumerà questo ruolo.
7. In **External ID** (ID esterno) immettere l'ID esterno che è un passcode condiviso tra il ruolo AWS e Gestione costi di Azure. Lo stesso ID esterno viene usato anche nella pagina **Nuovo connettore** in Gestione costi. Microsoft consiglia di usare un criterio per l'uso di passcode complessi quando si immette l'ID esterno.
    > [!NOTE]
    > Non modificare la selezione per **Require MFA** (Richiedi MFA). L'opzione deve rimanere deselezionata.
8. Selezionare **Avanti: Permissions** (Avanti: Autorizzazioni).
9. Selezionare **Create policy** (Crea criterio). Si apre una nuova scheda del browser. È qui che verrà creato un criterio.
10. Selezionare **Choose a service** (Scegli un servizio).

Configurare l'autorizzazione per il report di costi e utilizzo:

1. Immettere **Report di costi e utilizzo**.
2. Selezionare **Access level** (Livello di accesso) > **Read** (Lettura) > **DescribeReportDefinitions**. Questo passaggio consente a Gestione costi di leggere i report di costi e utilizzo definiti e determinare se corrispondono al prerequisito di definizione del report.
3. Selezionare **Add additional permissions** (Aggiungi autorizzazioni aggiuntive).

Configurare l'autorizzazione per il bucket e gli oggetti S3:

1. Selezionare **Choose a service** (Scegli un servizio).
2. Immettere **S3**.
3. Selezionare **Access level** (Livello di accesso) > **List** (Elenco) > **ListBucket**. Questa azione ottiene l'elenco di oggetti presenti nel bucket S3.
4. Selezionare **Access level** (Livello di accesso) > **Read** (Lettura) > **GetObject**. Questa azione consente il download dei file di fatturazione.
5. Selezionare **Resources** (Risorse).
6. Selezionare **bucket – Add ARN** (bucket - Aggiungi ARN).
7. In **Bucket name** (Nome bucket) immettere il bucket usato per archiviare i file del report di costi e utilizzo.
8. Selezionare **object – Add ARN** (oggetto - Aggiungi ARN).
9. In **Bucket name** (Nome bucket) immettere il bucket usato per archiviare i file del report di costi e utilizzo.
10. In **Object name** (Nome oggetto) selezionare **Any** (Qualsiasi).
11. Selezionare **Add additional permissions** (Aggiungi autorizzazioni aggiuntive).

Configurare l'autorizzazione per Cost Explorer:

1. Selezionare **Choose a service** (Scegli un servizio).
2. Immettere **Servizio Cost Explorer**.
3. Selezionare **All Cost Explorer Service actions (ce:\*)** (Tutte le azioni del servizio Cost Explorer). Questa azione convalida la correttezza della raccolta.
4. Selezionare **Add additional permissions** (Aggiungi autorizzazioni aggiuntive).

Aggiungere l'autorizzazione per AWS Organizations:

1. Immettere **Organizations**.
2. Selezionare **Access level** (Livello di accesso) > **List** (Elenco) > **ListAccounts**. Questa azione ottiene i nomi degli account.
3. In **Review Policy** (Rivedi criterio) immettere un nome per il nuovo criterio. Verificare di aver immesso le informazioni corrette, quindi selezionare **Create Policy** (Crea criterio).
4. Tornare alla scheda precedente e aggiornare la pagina Web del browser. Nella barra di ricerca cercare il nuovo criterio.
5. Selezionare **Avanti: Review** (Avanti: Rivedi).
6. Immettere un nome per il nuovo ruolo. Verificare di aver immesso le informazioni corrette e quindi selezionare **Create Role** (Crea ruolo).

    Prendere nota del ruolo ARN e dell'ID esterno usati nei passaggi precedenti quando è stato creato il ruolo. Verranno usati più avanti durante la configurazione del connettore di Gestione costi di Azure.

Il codice JSON del criterio deve essere simile all'esempio riportato di seguito. Sostituire _bucketname_ con il nome del bucket S3.

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

## <a name="set-up-a-new-connector-for-aws-in-azure"></a>Configurare un nuovo connettore per AWS in Azure

Usare le informazioni seguenti per creare un connettore AWS e iniziare a monitorare i costi di AWS:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare alla schermata iniziale di Azure facendo clic su **Home** nel menu a sinistra (icona del menu "hamburger" con tre linee).
3. Passare a **Strumenti** > **Gestione costi** in fondo alla pagina.
3. In **Impostazioni** selezionare **Connettori per AWS**.  
4. Selezionare **+ Aggiungi** nella parte superiore della pagina per creare un connettore.  
    :::image type="content" source="./media/aws-integration-setup-configure/aws-connector.png" alt-text="Esempio che mostra l'impostazione Connettori per AWS" :::
1. Nella pagina **Crea connettore** immettere un nome per il connettore in **Nome visualizzato**.  
    :::image type="content" source="./media/aws-integration-setup-configure/create-aws-connector01.png" alt-text="Esempio della pagina per la creazione di un connettore AWS" :::
1. Selezionare facoltativamente il gruppo di gestione predefinito. In esso verranno archiviati tutti gli account collegati individuati. È possibile configurarlo in seguito.
1. Nella sezione **Fatturazione** selezionare **Rinnovo automatico** su **On** se si vuole assicurare il funzionamento continuo. Se si seleziona l'opzione di addebito automatico, è necessario selezionare una sottoscrizione di fatturazione.
1. In **Ruolo ARN** immettere il valore usato durante la configurazione del ruolo in AWS.
1. In **ID esterno** immettere il valore usato durante la configurazione del ruolo in AWS.
1. In **Nome del report** immettere il nome creato in AWS.
1. Selezionare **Avanti** e quindi **Crea**.

La visualizzazione dei nuovi ambiti AWS, dell'account AWS consolidato, degli account AWS collegati e dei relativi dati sui costi può richiedere alcune ore.

Dopo aver creato il connettore, è consigliabile assegnargli il controllo di accesso. Agli utenti vengono assegnate le autorizzazioni per gli ambiti appena individuati: account AWS consolidato e account AWS collegati. L'utente che crea il connettore è il proprietario del connettore, dell'account consolidato e di tutti gli account collegati.

L'assegnazione delle autorizzazioni per il connettore agli utenti dopo l'individuazione non determina l'assegnazione delle autorizzazioni per gli ambiti AWS esistenti. Le autorizzazioni vengono infatti assegnate solo ai nuovi account collegati.

## <a name="take-additional-steps"></a>Passaggi aggiuntivi

- [Configurare i gruppi di gestione](../../governance/management-groups/overview.md#initial-setup-of-management-groups), se non è già stato fatto.
- Verificare che i nuovi ambiti siano stati aggiunti alla selezione ambiti. Selezionare **Aggiorna** per visualizzare i dati più recenti.
- Nella pagina **Connettori cloud** selezionare il connettore e quindi **Vai all'account di fatturazione** per assegnare l'account collegato ai gruppi di gestione.

> [!NOTE]
> I gruppi di gestione non sono attualmente supportati per i clienti con Contratto del cliente Microsoft. I clienti con Contratto del cliente Microsoft possono creare il connettore e visualizzare i rispettivi dati di AWS. I clienti con Contratto del cliente Microsoft, tuttavia, non possono visualizzare insieme i costi di Azure e di AWS in un gruppo di gestione.

## <a name="manage-aws-connectors"></a>Gestire i connettori AWS

Quando si seleziona un connettore nella pagina **Connettori per AWS**, è possibile:

- Selezionare **Vai all'account di fatturazione** per visualizzare le informazioni relative all'account AWS consolidato.
- Selezionare **Controllo di accesso** per gestire l'assegnazione di ruolo per il connettore.
- Selezionare **Modifica** per aggiornare il connettore. Non è possibile modificare il numero di account AWS perché è visualizzato nel ruolo ARN. Ma è possibile creare un nuovo connettore.
- Selezionare **Verifica** per eseguire di nuovo il test di verifica per assicurarsi che Gestione costi possa raccogliere i dati usando le impostazioni del connettore.

:::image type="content" source="./media/aws-integration-setup-configure/aws-connector-details.png" alt-text="Esempio di dettagli del connettore AWS" :::

## <a name="set-up-azure-management-groups"></a>Configurare gruppi di gestione di Azure

Inserire le sottoscrizioni di Azure e gli account AWS collegati nello stesso gruppo di gestione per creare un'unica posizione in cui vedere le informazioni dei diversi provider di servizi cloud. Se l'ambiente di Azure non è stato ancora configurato con i gruppi di gestione, vedere [Configurazione iniziale dei gruppi di gestione](../../governance/management-groups/overview.md#initial-setup-of-management-groups).

Se si vuole separare i costi, è possibile creare un gruppo di gestione contenente solo account AWS collegati.

## <a name="set-up-an-aws-consolidated-account"></a>Configurare un account AWS consolidato

L'account AWS consolidato combina fatturazione e pagamenti per più account AWS. Funge anche da account AWS collegato. È possibile visualizzare i dettagli per l'account consolidato di AWS usando il collegamento nella pagina del connettore AWS. 

:::image type="content" source="./media/aws-integration-setup-configure/aws-consolidated-account01.png" alt-text="Dettagli di esempio per un account AWS consolidato" :::

Nella pagina è possibile effettuare le operazioni seguenti:

- Selezionare **Aggiornamento** per eseguire l'aggiornamento in blocco dell'associazione degli account AWS collegati a un gruppo di gestione.
- Selezionare **Controllo di accesso** per impostare l'assegnazione di ruolo per l'ambito.

### <a name="permissions-for-an-aws-consolidated-account"></a>Autorizzazioni per un account AWS consolidato

Per impostazione predefinita, le autorizzazioni per un account AWS consolidato vengono impostate al momento della creazione dell'account, in base alle autorizzazioni del connettore AWS. Il creatore del connettore è il proprietario.

Per gestire il livello di accesso usare la pagina **Livello di accesso** dell'account AWS consolidato. Gli account AWS collegati, tuttavia, non ereditano le autorizzazioni concesse all'account AWS consolidato.

## <a name="set-up-an-aws-linked-account"></a>Configurare un account AWS collegato

L'account AWS collegato è quello in cui vengono create e gestite le risorse AWS. Un account collegato funge anche da limite di sicurezza.

In questa pagina è possibile effettuare le operazioni seguenti:

- Selezionare **Aggiornamento** per aggiornare l'associazione dell'account AWS collegato a un gruppo di gestione.
- Selezionare **Controllo di accesso** per impostare un'assegnazione di ruolo per l'ambito.

:::image type="content" source="./media/aws-integration-setup-configure/aws-linked-account01.png" alt-text="Esempio della pagina Account AWS collegato" :::

### <a name="permissions-for-an-aws-linked-account"></a>Autorizzazioni per un account AWS collegato

Per impostazione predefinita, le autorizzazioni per un account AWS collegato vengono impostate al momento della creazione, in base alle autorizzazioni del connettore AWS. Il creatore del connettore è il proprietario. Per gestire il livello di accesso usare la pagina **Livello di accesso** dell'account AWS collegato. Gli account AWS collegati non ereditano le autorizzazioni da un account AWS consolidato.

Gli account AWS collegati ereditano sempre le autorizzazioni dal gruppo di gestione a cui appartengono.

## <a name="next-steps"></a>Passaggi successivi

- Ora che è stata impostata e configurata l'integrazione del report di costi e utilizzo di AWS, passare a [Gestire i costi e l'utilizzo di AWS](aws-integration-manage.md).
- Se non si ha familiarità con l'analisi dei costi, vedere la guida di avvio rapido [Esplorare e analizzare i costi con l'analisi dei costi](quick-acm-cost-analysis.md).
- Se non si ha familiarità con i budget in Azure, vedere [Creare e gestire budget di Azure](tutorial-acm-create-budgets.md).
