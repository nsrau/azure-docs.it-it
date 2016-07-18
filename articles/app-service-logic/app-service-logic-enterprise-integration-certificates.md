<properties 
	pageTitle="Uso dei certificati con Enterprise Integration Pack | Servizio app di Microsoft Azure" 
	description="Informazioni su come usare i certificati con Enterprise Integration Pack e le app per la logica" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2016" 
	ms.author="deonhe"/>

# Informazioni sui certificati ed Enterprise Integration Pack

## Panoramica
Enterprise Integration impiega i certificati per proteggere le comunicazioni B2B. È possibile usare due tipi di certificati nelle app di Enterprise Integration:
- Certificati pubblici, che devono essere acquistati da un'autorità di certificazione (CA)
- Certificati privati, che è possibile emettere personalmente; questi sono talvolta chiamati certificati autofirmati.

## Che cosa sono i certificati?
I certificati sono documenti digitali che vengono usati per verificare l'identità dei partecipanti nelle comunicazioni elettroniche e anche per proteggere le comunicazioni elettroniche stesse.

## Perché usare i certificati?
In alcuni casi è necessario garantire la riservatezza delle comunicazioni B2B. Enterprise Integration usa i certificati per proteggere queste comunicazioni in due modi:
- Crittografando il contenuto dei messaggi
- Apponendo una firma digitale ai messaggi

## Come caricare i certificati?

### Certificato pubblico
Per usare un **certificato pubblico** nelle app per la logica con funzionalità B2B, è innanzitutto necessario caricarlo nell'account di integrazione. Per usare invece un **certificato autofirmato**, bisogna prima caricarlo nell'[insieme di credenziali delle chiavi](../key-vault/key-vault-get-started.md "Informazioni sull'insieme di credenziali delle chiavi").

Dopo che è stato caricato, il certificato può essere usato per proteggere i messaggi B2B quando si definiscono le loro proprietà nei [contratti](./app-service-logic-enterprise-integration-agreements.md).

Ecco i passaggi dettagliati per caricare i certificati pubblici nell'account di integrazione dopo l'accesso al portale di Azure:
1. Selezionare **Esplora** ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Immettere **integrazione** nella casella di ricerca del filtro e selezionare **Integration Accounts** (Account di integrazione) dall'elenco dei risultati ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Selezionare l'**account di integrazione** a cui aggiungere il certificato ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Selezionare il riquadro **Certificati** ![](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)
5. Selezionare il pulsante **Aggiungi** nel pannello Certificati che si apre ![](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)
6. Immettere un **nome** per il certificato, selezionare il tipo di certificato (in questo esempio si usa il tipo di certificato pubblico) quindi selezionare l'icona della cartella sul lato destro della casella di testo **Certificato**. Verrà visualizzato il selettore file che consente di individuare e selezionare il file del certificato da caricare nell'account di integrazione. Dopo aver selezionato il certificato, selezionare **OK** nel selettore file. Questa operazione convalida e carica il certificato nell'account di integrazione. Infine, di nuovo nel pannello **Aggiungi certificato**, selezionare il pulsante **OK**. ![](./media/app-service-logic-enterprise-integration-certificates/certificate-3.png)
7. Entro un minuto si vedrà una notifica con l'avviso che il caricamento del certificato è stato completato.
8. Selezionare il riquadro **Certificati**. La pagina viene aggiornata e si dovrebbe vedere il certificato appena aggiunto: ![](./media/app-service-logic-enterprise-integration-certificates/certificate-4.png)

### Certificato privato
Nell'account di integrazione è possibile caricare anche certificati privati. Per farlo seguire questa procedura:
1. [Caricare la propria chiave privata nell'insieme di credenziali delle chiavi](../key-vault/key-vault-get-started.md "Informazioni sull'insieme di credenziali delle chiavi")
2. Creare un certificato privato Caricare il certificato privato nell'account di integrazione

Dopo aver eseguito i passaggi precedenti, è possibile usare il certificato privato per creare i contratti.

Ecco i passaggi dettagliati per caricare i certificati privati nell'account di integrazione dopo l'accesso al portale di Azure:
1. Selezionare **Esplora** ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Immettere **integrazione** nella casella di ricerca del filtro e selezionare **Integration Accounts** (Account di integrazione) dall'elenco dei risultati ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Selezionare l'**account di integrazione** a cui aggiungere il certificato ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Selezionare il riquadro **Certificati** ![](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)
5. Selezionare il pulsante **Aggiungi** nel pannello Certificati che si apre ![](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)
6. Immettere un **nome** per il certificato, selezionare il tipo di certificato (in questo esempio si usa il tipo di certificato pubblico) quindi selezionare l'icona della cartella sul lato destro della casella di testo **Certificato**. Verrà visualizzato il selettore file che consente di individuare e selezionare il file del certificato da caricare nell'account di integrazione. Dopo aver selezionato il certificato, selezionare **OK** nel selettore file. Questa operazione convalida e carica il certificato nell'account di integrazione. Infine, di nuovo nel pannello **Aggiungi certificato**, selezionare il pulsante **OK**. ![](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-1.png)
7. Entro un minuto si vedrà una notifica con l'avviso che il caricamento del certificato è stato completato.
8. Selezionare il riquadro **Certificati**. La pagina viene aggiornata e si dovrebbe vedere il certificato appena aggiunto: ![](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-2.png)

Dopo che è stato caricato, il certificato può essere usato per proteggere i messaggi B2B quando si definiscono le loro proprietà nei [contratti](./app-service-logic-enterprise-integration-agreements.md).


## Passaggi successivi
- [Creare un contratto B2B](./app-service-logic-enterprise-integration-agreements.md)
- [Creare un'app per la logica con funzionalità B2B](./app-service-logic-enterprise-integration-b2b.md)
- [Altre informazioni sull'insieme di credenziali delle chiavi](../key-vault/key-vault-get-started.md "Informazioni sull'insieme di credenziali delle chiavi")

<!---HONumber=AcomDC_0706_2016-->