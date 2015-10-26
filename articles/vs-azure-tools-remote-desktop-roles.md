<properties 
   pageTitle="Utilizzo di Desktop remoto con i ruoli Azure"
   description="Utilizzo di Desktop remoto con i ruoli Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2015"
   ms.author="kempb" />

# Utilizzo di Desktop remoto con i ruoli Azure

Usando Azure SDK e Servizi Desktop remoto, è possibile accedere ai ruoli di Azure e alle macchine virtuali ospitate da Azure. In Visual Studio è possibile configurare Servizi Desktop remoto da un progetto Azure. Per abilitare Servizi Desktop remoto, è necessario creare un progetto di lavoro contenente uno o più ruoli e quindi pubblicarlo in Azure.

>[AZURE.IMPORTANT]Accedere a un ruolo di Azure solo per la risoluzione dei problemi o lo sviluppo. Lo scopo di ogni macchina virtuale è eseguire un ruolo specifico nell'applicazione di Azure e non eseguire altre applicazioni client. Se si vuole usare Azure per ospitare una macchina virtuale che è possibile usare per qualsiasi scopo, vedere Accesso alle macchine virtuali di Azure da Esplora server.

## Per abilitare e usare Desktop remoto per un ruolo di Azure

1. In Esplora soluzioni aprire il menu di scelta rapida per il progetto e quindi scegliere **Pubblica**.

    Verrà visualizzata la procedura guidata **Pubblica l'applicazione Azure**.

    ![Comando per la pubblicazione di un progetto di servizio cloud](./media/vs-azure-tools-remote-desktop-roles/IC799161.png)

1. Nella parte inferiore della pagina **Impostazioni di pubblicazione di Microsoft Azure** della procedura guidata selezionare la casella di controllo **Abilita Desktop remoto per tutti i ruoli**.

    Verrà visualizzata la finestra di dialogo **Configurazione Desktop remoto**.

1. Nella parte inferiore della finestra di dialogo **Configurazione Desktop remoto** scegliere il pulsante **Altre opzioni**.
 
    Verrà visualizzato un elenco a discesa che consente di creare o scegliere un certificato in modo che sia possibile crittografare le informazioni sulle credenziali per la connessione tramite Desktop remoto.

1. Nell'elenco a discesa scegliere **<Create>** o scegliere un certificato esistente dall'elenco.

    Se si sceglie un certificato esistente, ignorare i passaggi seguenti.

    >[AZURE.NOTE]I certificati necessari per una connessione Desktop remoto sono diversi da quelli usati per altre operazioni di Azure. Il certificato di accesso remoto deve avere una chiave privata.

    Verrà visualizzata la finestra di dialogo **Crea certificato**.

    1. Specificare un nome descrittivo per il nuovo certificato e quindi scegliere il pulsante **OK**. Il nuovo certificato verrà visualizzato nell'elenco a discesa.

    1. Nella finestra di dialogo **Configurazione Desktop remoto** specificare un nome utente e una password.
    
        Non è possibile usare un account esistente. Non specificare l'amministratore come nome utente per il nuovo account.

        >[AZURE.NOTE]Se la password non soddisfa i requisiti di complessità necessari, accanto alla casella di testo della password viene visualizzata un'icona rossa. La password deve includere lettere maiuscole e minuscole, numeri o simboli.

    1. Scegliere una data di scadenza dell'account, dopo la quale le connessioni Desktop remoto verranno bloccate.

    1. Dopo aver fornito tutte le informazioni necessarie, scegliere il pulsante **OK**.
    
        Verranno aggiunte diverse impostazioni che abilitano i servizi di accesso remoto ai file con estensione cscfg e csdef.

1. Nella procedura guidata **Impostazioni di pubblicazione Microsoft Azure** scegliere il pulsante **OK** quando si è pronti a pubblicare il servizio cloud.

    Se non si è ancora pronti, scegliere il pulsante **Annulla**. Le impostazioni di configurazione vengono salvate ed è possibile pubblicare il servizio cloud in un secondo momento.

## Connettersi a un ruolo di Azure tramite Desktop remoto

Dopo aver pubblicato il servizio cloud in Azure, è possibile usare Esplora Server per accedere alle macchine virtuali ospitate da Azure.

1. In Esplora server espandere il nodo **Azure** e quindi espandere il nodo di un servizio cloud e uno dei relativi ruoli per visualizzare un elenco di istanze.

1. Aprire il menu di scelta rapida di un nodo dell'istanza e quindi scegliere **Connessione tramite desktop remoto**.

    ![Connessione tramite Desktop remoto](./media/vs-azure-tools-remote-desktop-roles/IC799162.png)

1. Immettere il nome utente e la password creati in precedenza. L'accesso alla sessione remota è stato completato.

<!---HONumber=Oct15_HO3-->