<properties title="How to create a hybrid deployment of RemoteApp" pageTitle="How to create a hybrid deployment of RemoteApp" description="Learn how to create a deployment of RemoteApp that connects to your internal network." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="elizapo"></tags>

# Come creare una distribuzione cloud di RemoteApp

Sono disponibili due tipi di distribuzione di RemoteApp:

-   Cloud: risiede completamente in Azure e viene creata con l'opzione **Creazione rapida** nel portale di gestione di Azure.
-   Ambiente ibrido: include una rete virtuale per l'accesso locale e viene creata con l'opzione **Crea con VPN** nel portale di gestione.

In questa esercitazione vengono illustrati i quattro passaggi della creazione di una distribuzione cloud.

1.  Creare un servizio RemoteApp.
2.  Facoltativamente, configurare la sincronizzazione della directory. RemoteApp richiede questa configurazione per sincronizzare utenti, gruppi, contatti e password dall'ambiente Active Directory locale al tenant di Azure Active Directory.
3.  Pubblicare i programmi RemoteApp.
4.  Configurare l'accesso utente.

**Prima di iniziare**

Prima di creare il servizio, è necessario eseguire le seguenti operazioni:

-   Effettuare l'iscrizione per l'anteprima di RemoteApp all'indirizzo [][]<http://azure.microsoft.com/it-it/services/remoteapp/></a>.
-   Raccogliere informazioni sugli utenti e sui gruppi a cui concedere l'accesso. Possono essere informazioni sugli account Microsoft o sugli account aziendali Active Directory per utenti o gruppi.
-   Questa procedura presuppone che verrà usata l'immagine modello fornita nell'ambito della sottoscrizione oppure che sia stata già caricata l'immagine modello da usare. Se è necessario caricare un'immagine modello diversa, è possibile effettuare questa operazione nella pagina Immagini modello. Fare clic su **carica un'immagine modello** e seguire i passaggi della procedura guidata.

## **Passaggio 1: Creare un servizio RemoteApp**

1.  Nel [portale di gestione di Azure][portale di gestione di Azure] passare alla pagina RemoteApp.
2.  Fare clic su **Nuovo \> Creazione rapida**.

3.  Immettere un nome per il servizio e selezionare l'area.
4.  Selezionare la sottoscrizione da usare per creare il servizio.
5.  Scegliere il modello da usare per il servizio.

    **Suggerimento:** la sottoscrizione per RemoteApp include un'immagine modello che contiene le applicazioni Office 2013, alcune pubblicate (ad esempio Word) e altre pronte per la pubblicazione.

6.  Fare clic su **Crea servizio RemoteApp**.

    **Importante:** il provisioning del servizio può richiedere fino a 30 minuti.

Dopo la creazione del servizio RemoteApp passare alla pagina **Avvio rapido** per continuare con la procedura di configurazione.

## **Passaggio 2: Configurare la sincronizzazione della directory (facoltativo)**

Se si vuole usare Active Directory, RemoteApp richiede la sincronizzazione della directory tra Azure Active Directory e l'istanza locale di Active Directory in modo da sincronizzare utenti, gruppi, contatti e password nel tenant di Azure Active Directory. Per informazioni sulla pianificazione e le procedure dettagliate, vedere [Roadmap sulla sincronizzazione della directory][Roadmap sulla sincronizzazione della directory].

## **Passaggio 3: Pubblicare i programmi RemoteApp**

Un programma RemoteApp è l'app o il programma da fornire agli utenti e si trova nell'immagine modello caricata per il servizio. Quando un utente accede a un programma RemoteApp, questo sembra essere eseguito nell'ambiente locale, ma in realtà viene eseguito in Azure.

Prima che gli utenti possano accedere ai programmi RemoteApp, è necessario pubblicarli nel feed degli utenti finali, ovvero un elenco di programmi disponibili cui gli utenti accedono tramite il portale di Azure.

È possibile pubblicare più programmi nel servizio RemoteApp. Nella pagina dei programmi RemoteApp fare clic su **Pubblica** per aggiungere un programma. È possibile pubblicare dal menu Start dell'immagine modello oppure specificando il percorso nell'immagine modello per il programma. Se si sceglie di aggiungere il programma dal menu Start, scegliere quello da pubblicare. Se si sceglie di specificare il percorso del programma, fornire il nome del programma e il percorso in cui è installato il programma nell'immagine modello.

## **Passaggio 4: Configurare l'accesso utente**

A questo punto, dopo aver creato il servizio RemoteApp, è necessario aggiungere gli utenti e i gruppi che potranno usare le risorse remote. Se si usa Active Directory, gli utenti o i gruppi a cui viene fornito l'accesso devono esistere nel tenant di Active Directory associato alla sottoscrizione usata per creare il servizio RemoteApp.

1.  Nella pagina Avvio rapido fare clic su **Configura accesso utente**.
2.  Immettere l'account aziendale o il nome del gruppo di Active Directory oppure l'account Microsoft per cui concedere l'accesso.

    Per gli utenti assicurarsi di usare il formato “[utente@dominio.com][utente@dominio.com]”. Per i gruppi immettere il nome del gruppo.

3.  Dopo aver convalidato utenti o gruppi, fare clic su **Salva**.

## Passaggi successivi

La procedura è stata completata e la distribuzione cloud RemoteApp è stata creata e distribuita. Il passaggio successivo consiste nel chiedere agli utenti di scaricare e installare il client Desktop remoto. L'URL del client è indicato nella pagina Avvio rapido di RemoteApp. Chiedere quindi agli utenti di effettuare l'accesso ad Azure e di accedere ai programmi RemoteApp pubblicati.

  []: http://azure.microsoft.com/it-it/services/remoteapp/
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [Roadmap sulla sincronizzazione della directory]: http://msdn.microsoft.com/it-it/library/azure/hh967642.aspx
  [utente@dominio.com]: mailto:user@domain.com
