
<properties
	pageTitle="Uso dei certificati con Enterprise Integration Pack | Microsoft Azure"
	description="Informazioni su come usare i certificati con Enterprise Integration Pack e le App per la logica"
	services="logic-apps"
	documentationCenter=".net,nodejs,java"
	authors="msftman"
	manager="erikre"
	editor="cgronlun"/>  

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="deonhe"/>  

# Informazioni sui certificati ed Enterprise Integration Pack

## Overview
Enterprise Integration impiega i certificati per proteggere le comunicazioni B2B. È possibile usare due tipi di certificati nelle app di Enterprise Integration:

- Certificati pubblici, che devono essere acquistati da un'autorità di certificazione (CA).
- Certificati privati, che si possono rilasciare personalmente. Sono talvolta definiti certificati autofirmati.


## Che cosa sono i certificati?
I certificati sono documenti digitali che verificano l'identità dei partecipanti nelle comunicazioni elettroniche e proteggono le comunicazioni elettroniche stesse.

## Perché usare i certificati?
In alcuni casi è necessario garantire la riservatezza delle comunicazioni B2B. Enterprise Integration usa i certificati per proteggere queste comunicazioni in due modi:

- Crittografando il contenuto dei messaggi
- Apponendo una firma digitale ai messaggi

## Come caricare i certificati?

### Certificati pubblici
Per usare un *certificato pubblico* nelle app per la logica con funzionalità B2B, è necessario prima caricare il certificato nell'account di integrazione. Per usare invece un *certificato autofirmato*, bisogna prima caricarlo nell'[insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-get-started.md "Informazioni sull'insieme di credenziali delle chiavi").

Dopo che è stato caricato, un certificato diventa disponibile per proteggere i messaggi B2B quando se ne definiscono le proprietà nei [contratti](./app-service-logic-enterprise-integration-agreements.md) creati.

Ecco i passaggi dettagliati per caricare i certificati pubblici nell'account di integrazione dopo l'accesso al portale di Azure:

1. Selezionare **Esplora**. ![Selezionare Esplora](./media/app-service-logic-enterprise-integration-overview/overview-1.png)

2. Immettere **integrazione** nella casella di ricerca del filtro e quindi selezionare **Account di integrazione** nell'elenco dei risultati ![Selezione gli account di integrazione](./media/app-service-logic-enterprise-integration-overview/overview-2.png)

3. Selezionare l'account di integrazione a cui aggiungere il certificato. ![Selezionare l'account di integrazione a cui aggiungere il certificato](./media/app-service-logic-enterprise-integration-overview/overview-3.png)

4.  Selezionare il riquadro **Certificati**. ![Selezionare il riquadro Certificati](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)

5. Selezionare il pulsante **Aggiungi** nel pannello **Certificati** che si apre. ![Selezionare il pulsante Aggiungi](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)

6. Immettere un **nome** per il certificato e quindi selezionare il tipo di certificato. In questo esempio viene usato il tipo di certificato pubblico. Selezionare quindi l'icona della cartella sul lato destro della casella di testo **Certificato**.

7. Quando viene visualizzato il selettore file, individuare e selezionare il file del certificato da caricare nell'account di integrazione.

8. Selezionare il certificato e poi **OK** nel selettore file. Questa operazione convalida e carica il certificato nell'account di integrazione.

8. Infine, di nuovo nel pannello **Aggiungi certificato** selezionare il pulsante **OK**. ![Selezionare il pulsante OK](./media/app-service-logic-enterprise-integration-certificates/certificate-3.png)

9. In circa un minuto si riceverà una notifica con l'avviso che il caricamento del certificato è stato completato.

10. Selezionare il riquadro **Certificati**. Viene visualizzato il certificato appena aggiunto. ![Vedere il nuovo certificato](./media/app-service-logic-enterprise-integration-certificates/certificate-4.png)

### Certificati privati
Nell'account di integrazione è possibile caricare anche certificati privati seguendo questa procedura:

1. [Caricare la propria chiave privata nell'insieme di credenziali delle chiavi](../key-vault/key-vault-get-started.md "Informazioni sull'insieme di credenziali delle chiavi").

	> [AZURE.TIP] È necessario autorizzare la funzione App per la logica nel Servizio app di Azure per eseguire operazioni sull'insieme di credenziali delle chiavi. È possibile concedere l'accesso all'entità servizio App per la logica usando questo comando di PowerShell: `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`

2. Creare un certificato privato.

3. Caricare il certificato privato nell'account di integrazione.

Dopo avere eseguito i passaggi precedenti, è possibile usare il certificato privato per creare i contratti.

Ecco i passaggi dettagliati per caricare i certificati privati nell'account di integrazione dopo l'accesso al portale di Azure:

1. Selezionare **Esplora**. ![Caricare i certificati privati nell'account di integrazione](./media/app-service-logic-enterprise-integration-overview/overview-1.png)

2. Immettere **integrazione** nella casella di ricerca del filtro e quindi selezionare **Account di integrazione** nell'elenco dei risultati ![Selezione gli account di integrazione](./media/app-service-logic-enterprise-integration-overview/overview-2.png)

3. Selezionare l'account di integrazione a cui aggiungere il certificato. ![Selezionare l'account di integrazione a cui aggiungere il certificato](./media/app-service-logic-enterprise-integration-overview/overview-3.png)

4. Selezionare il riquadro **Certificati**. ![Selezionare il riquadro Certificati](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)

5. Selezionare il pulsante **Aggiungi** nel pannello **Certificati** che si apre. ![Selezionare il pulsante Aggiungi](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)

6. Immettere un **nome** per il certificato e quindi selezionare il tipo di certificato. In questo esempio viene usato il tipo di certificato pubblico. Selezionare quindi l'icona della cartella sul lato destro della casella di testo **Certificato**.

7. Quando viene visualizzato il selettore file, individuare e selezionare il file del certificato da caricare nell'account di integrazione.

8. Dopo aver selezionato il certificato, selezionare **OK** nel selettore file. Questa azione convalida il certificato e lo carica nell'account di integrazione.

9. Infine, di nuovo nel pannello **Aggiungi certificato** selezionare il pulsante **OK**. ![Add certificate](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-1.png)

10. In circa un minuto si riceverà una notifica con l'avviso che il caricamento del certificato è stato completato.

11. Selezionare il riquadro **Certificati**. Viene visualizzato il certificato appena aggiunto. ![Vedere il nuovo certificato](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-2.png)

Dopo che è stato caricato, un certificato è disponibile per proteggere i messaggi B2B quando se ne definiscono le proprietà nei [contratti](./app-service-logic-enterprise-integration-agreements.md).

## Passaggi successivi
- [Creare un'app per la logica che usa funzionalità B2B](./app-service-logic-enterprise-integration-b2b.md)
- [Creare un contratto B2B](./app-service-logic-enterprise-integration-agreements.md)
- [Altre informazioni sull'insieme di credenziali delle chiavi](../key-vault/key-vault-get-started.md "Informazioni sull'insieme di credenziali delle chiavi")

<!---HONumber=AcomDC_0928_2016-->