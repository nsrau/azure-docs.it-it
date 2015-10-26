<properties
   pageTitle="Configurazione delle credenziali per l'autenticazione denominate"
   description="Informazioni su come fornire le credenziali che Visual Studio può usare per l'autenticazione delle richieste inviate a Azure per pubblicare un'applicazione in Azure da Visual Studio o monitorare un servizio cloud esistente."
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/02/2015"
   ms.author="kempb" />

# Configurazione delle credenziali per l'autenticazione denominate

Per pubblicare un'applicazione in Azure da Visual Studio o monitorare un servizio cloud esistente, è necessario fornire le credenziali che Visual Studio può usare per l'autenticazione delle richieste inviate a Azure. In Visual Studio è possibile fornire queste credenziali accedendo a più posizioni. Ad esempio, da Esplora server è possibile aprire il menu di scelta rapida per il nodo di **Azure** e scegliere **Connetti ad Azure**. Quando si effettua l'accesso, le informazioni sulla sottoscrizione associate all'account di Azure sono disponibili in Visual Studio. A questo punto, non ci sono altre operazioni da eseguire.

Gli strumenti di Azure supportano un modo meno recente di fornire le credenziali, attraverso il file di sottoscrizione (file con estensione publishsettings). Questo argomento illustra tale metodo, che è ancora supportato in Azure SDK 2.2.

Per l'autenticazione in Azure sono richiesti i seguenti elementi.

- ID sottoscrizione

- Un certificato X.509 v3 valido

>[AZURE.NOTE]La lunghezza della chiave del certificato X.509 v3 deve essere di almeno 2048 bit. Azure rifiuterà qualsiasi certificato che non soddisfa questo requisito o che sia non valido.

Visual Studio usa l'ID sottoscrizione e i dati del certificato come credenziali. Nel file di sottoscrizione (file con estensione publishsettings), che contiene una chiave pubblica per il certificato, viene fatto riferimento alle credenziali appropriate. Il file di sottoscrizione può contenere le credenziali per più di una sottoscrizione.

È possibile modificare le informazioni di sottoscrizione dalla finestra di dialogo **Nuova/Modifica sottoscrizione**, come illustrato più avanti in questo argomento.

Se si vuole creare un certificato, vedere le istruzioni contenute in [Creare e caricare un certificato di gestione per Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx), quindi caricare manualmente il certificato nel portale di gestione.

>[AZURE.NOTE]Le credenziali richieste da Visual Studio per gestire i servizi cloud non sono le stesse necessarie per autenticare una richiesta per i servizi di archiviazione di Azure.

## Modificare o esportare le credenziali di autenticazione in Visual Studio

È anche possibile impostare, modificare o esportare le credenziali di autenticazione nella finestra di dialogo **Nuova sottoscrizione**, visualizzata se si esegue una delle azioni seguenti:

- In **Esplora server** aprire il menu di scelta rapida per il nodo di **Azure**, scegliere **Gestisci sottoscrizioni**, selezionare la scheda **Certificati** e infine fare clic su **Nuovo** o **Modifica**.

- Quando si pubblica un servizio cloud di Azure dalla procedura guidata **Pubblica l'applicazione Azure**, scegliere **Gestire** dall'elenco **Scegliere la propria sottoscrizione**, quindi selezionare la scheda Certificati e infine fare clic su **Nuovo** o **Modifica**.

La procedura riportata di seguito presuppone che sia visualizzata la finestra di dialogo **Nuova sottoscrizione**.

### Per configurare le credenziali di autenticazione in Visual Studio

1. Scegliere un certificato dall'elenco **Selezionare un certificato esistente per l'autenticazione**.

1. Fare clic su **Copia il percorso completo**. Il percorso del certificato (file con estensione cer) viene copiato negli Appunti.

    >[AZURE.IMPORTANT]Per pubblicare l'applicazione Azure da Visual Studio, è necessario caricare questo certificato nel portale di gestione.

1. Per caricare il certificato al Portale di gestione di Azure:

    1. Scegliere il collegamento Portale di Azure.

         Il [Portale di gestione di Azure](http://go.microsoft.com/fwlink/?LinkID=213885) si apre.

    1. Accedere al Portale di gestione di Azure utilizzando l'account Microsoft, e poi premere il tasto **Servizi Cloud**.

    1. Scegliere il servizio cloud desiderato.

        Viene visualizzata la pagina del servizio.

    1. Nella scheda **Certificati** fare clic su **Carica**.

    1. Incollare il percorso completo del file con estensione cer appena creato e digitare la password specificata.

<!---HONumber=Oct15_HO3-->