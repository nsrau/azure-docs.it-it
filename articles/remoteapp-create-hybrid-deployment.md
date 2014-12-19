<properties title="How to create a hybrid deployment of RemoteApp" pageTitle="Come creare una distribuzione ibrida di RemoteApp" description="Learn how to create a deployment of RemoteApp that connects to your internal network." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/12/2014" ms.author="elizapo" ms.manager="kathyw" />

#Come creare una distribuzione ibrida di RemoteApp

Sono disponibili due tipi di distribuzione di RemoteApp: 

- Cloud: risiede completamente in Azure e viene creata con l'opzione **Creazione rapida** nel portale di gestione di Azure.  
- Ambiente ibrido: include una rete virtuale per l'accesso locale e viene creata con l'opzione **Crea con VPN** nel portale di gestione.

Questa esercitazione illustra i sette passaggi del processo di creazione di una distribuzione ibrida. 

1.	Creare un'[immagine modello personalizzata per RemoteApp](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-create-custom-image/).
2.	Creare un servizio RemoteApp.
2.	Collegarsi a una rete virtuale.
3.	Collegarsi a un'immagine modello.
4.	Configurare la sincronizzazione della directory. RemoteApp richiede l'integrazione con Azure Active Directory 1) configurando DirSync con l'opzione Sincronizzazione password o 2) configurando DirSync senza l'opzione Sincronizzazione password ma usando un dominio federato ad ADFS.
5.	Pubblicare i programmi RemoteApp.
6.	Configurare l'accesso utente.

**Prima di iniziare**

Prima di creare il servizio, è necessario eseguire le seguenti operazioni:

- Effettuare l'iscrizione per l'anteprima di RemoteApp all'indirizzo [http://azure.microsoft.com/it-it/services/remoteapp/](http://azure.microsoft.com/it-it/services/remoteapp/).
- Creare in Active Directory un account utente da usare come account del servizio RemoteApp. Limitare le autorizzazioni per l'account in modo che possa solo aggiungere computer al dominio.
- Raccogliere informazioni sulla rete locale, ovvero indirizzi IP e dettagli sui dispositivi VPN.
- Installare il modulo [Azure PowerShell](http://azure.microsoft.com/it-it/documentation/articles/install-configure-powershell/).
- Raccogliere informazioni sugli utenti e sui gruppi a cui concedere l'accesso. Le informazioni possono essere relative all'account Microsoft o all'account di lavoro Active Directory per utenti o gruppi.
- Creare un'immagine modello. Un'immagine modello di RemoteApp contiene le app e i programmi da pubblicare per gli utenti. Per i passaggi dettagliati, vedere [Come creare un'immagine modello personalizzata per RemoteApp](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-create-custom-image/). 






## **Passaggio 1: Creare un servizio RemoteApp** ##



1. Nel [portale di gestione di Azure](http://manage.windowsazure.com) passare alla pagina RemoteApp.
2. Fare clic su **Nuovo > Crea con VPN**.
3. Immettere un nome per il servizio e fare clic su **Crea servizio RemoteApp**.

Dopo la creazione del servizio RemoteApp passare alla pagina **Avvio rapido** di RemoteApp per continuare con la procedura di configurazione.

## **Passaggio 2: Collegarsi a una rete virtuale** ##

Una rete virtuale consente agli utenti di accedere ai dati nella rete locale mediante le risorse remote di RemoteApp. Sono quattro i passaggi per configurare il collegamento alla rete virtuale:

1. Nella pagina Avvio rapido fare clic su **Collega una rete virtuale RemoteApp**. 
2. Scegliere se creare una nuova rete virtuale o usarne una esistente. Per questa esercitazione verrà creata una nuova rete.
3. Specificare le informazioni seguenti per la nuova rete:  
      - Nome
      - Spazio degli indirizzi della rete virtuale
      - Spazio degli indirizzi locale
      - Indirizzo IP del server DNS
      - Indirizzo IP VPN

	Per altre informazioni, vedere [Configurare una VPN da sito a sito nel portale di gestione](http://msdn.microsoft.com/library/azure/dn133795.aspx).

4. Tornare quindi alla pagina Avvio rapido e fare clic su **Ottieni script** per scaricare uno script per configurare il dispositivo VPN per la connessione alla rete virtuale appena creata. È necessario disporre delle seguenti informazioni relative al dispositivo VPN: 
	- Fornitore
	- Piattaforma
	- Sistema operativo

	Salvare lo script ed eseguirlo nel dispositivo VPN. 

	**Nota:** dopo aver eseguito lo script, la rete virtuale passerà allo stato Pronto. L'operazione può richiedere 5-7 minuti. Finché la rete non è pronta, non sarà possibile usarla.

5. Infine, sempre nella pagina Avvio rapido, fare clic su **Aggiungi dominio locale**. Aggiungere l'account del servizio RemoteApp al dominio di Active Directory locale. È necessario disporre di nome di dominio, unità organizzativa, nome utente e password dell'account del servizio.


## **Passaggio 3: Collegarsi a un'immagine modello di RemoteApp** ##

Un'immagine modello di RemoteApp contiene i programmi da condividere con gli utenti. È possibile caricare la nuova immagine modello creata (dalle istruzioni in [Come creare un'immagine modello personalizzata per RemoteApp](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-create-custom-image/)) creare un collegamento a un'immagine esistente già caricata in Azure.

Se si carica la nuova immagine, è necessario immettere il nome e scegliere il percorso dell'immagine. Nella pagina successiva della procedura guidata verrà visualizzato un insieme di cmdlet di PowerShell. Copiarli ed eseguirli da un prompt di Windows PowerShell con privilegi elevati per caricare l'immagine specificata.

Se si crea un collegamento a un'immagine modello esistente, è sufficiente specificare il nome, il percorso e la sottoscrizione di Azure associata all'immagine.



## **Passaggio 4: Configurare la sincronizzazione della directory di Active Directory** ##

RemoteApp richiede l'integrazione con Azure Active Directory 1) configurando DirSync con l'opzione Sincronizzazione password o 2) configurando DirSync senza l'opzione Sincronizzazione password ma usando un dominio federato ad ADFS. Per informazioni sulla pianificazione e le procedure dettagliate, vedere [Roadmap sulla sincronizzazione della directory](http://msdn.microsoft.com//library/azure/hh967642.aspx).

## **Passaggio 5: Pubblicare i programmi RemoteApp** ##

Un programma RemoteApp è l'app o il programma da fornire agli utenti. Si trova nell'immagine modello caricata per il servizio. Quando un utente accede a un programma RemoteApp, questo sembra essere eseguito nell'ambiente locale, ma in realtà viene eseguito in Azure. 

Prima che gli utenti possano accedere ai programmi RemoteApp, è necessario pubblicarli nel feed degli utenti finali, ovvero un elenco di programmi disponibili cui gli utenti accedono tramite il portale di Azure.
 
Nel servizio di RemoteApp è possibile pubblicare più programmi. Nella pagina dei programmi RemoteApp fare clic su **Pubblica** per aggiungere un programma. È possibile pubblicare dal menu Start dell'immagine modello oppure specificando il percorso nell'immagine modello per il programma. Se si sceglie di aggiungere il programma dal menu Start, scegliere quello da pubblicare. Se si sceglie di fornire il percorso del programma, specificare il nome del programma e il percorso in cui è installato nell'immagine modello.

## **Passaggio 6: Configurare l'accesso utente** ##

A questo punto, dopo aver creato il servizio RemoteApp, è necessario aggiungere gli utenti e i gruppi che potranno usare le risorse remote. Gli utenti o i gruppi a cui viene fornito l'accesso devono esistere nel tenant di Active Directory associato alla sottoscrizione usata per creare il servizio RemoteApp.

1.	Nella pagina Avvio rapido fare clic su **Configura accesso utente**. 
2.	Immettere l'account di lavoro o il nome del gruppo (da Active Directory) o l'account Microsoft al quale concedere l'accesso.

	Per gli utenti assicurarsi di usare il formato "utente@domain.com". Per i gruppi immettere il nome del gruppo.

3.	Dopo aver convalidato utenti o gruppi, fare clic su **Salva**.


## Passaggi successivi ##
La procedura è stata completata e la distribuzione ibrida RemoteApp è stata creata e distribuita. Il passaggio successivo consiste nel chiedere agli utenti di scaricare e installare il client Desktop remoto. L'URL del client è indicato nella pagina Avvio rapido di RemoteApp. Quindi, chiedere agli utenti di connettersi al client e di accedere ai programmi RemoteApp pubblicati.


