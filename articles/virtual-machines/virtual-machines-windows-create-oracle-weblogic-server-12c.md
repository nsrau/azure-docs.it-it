<properties
	pageTitle="Creare una macchina virtuale Oracle WebLogic Server 12c | Microsoft Azure"
	description="Creare una macchina virtuale Oracle WebLogic Server 12c in esecuzione su Windows Server 2012 in Microsoft Azure mediante il modello di distribuzione di Gestione risorse."
	services="virtual-machines-windows"
	authors="bbenz"
	documentationCenter=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz" />

#Creazione di una macchina virtuale Oracle WebLogic Server 12c in Azure
Nell'esempio seguente viene mostrato come creare una macchina virtuale basata su un'immagine Oracle WebLogic Server 12c fornita da Microsoft in esecuzione su Windows Server 2012 in Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.



##Creazione di una macchina virtuale Oracle WebLogic Server 12c in Azure

1. Accedere al [Portale di Azure](https://ms.portal.azure.com/).

2. Fare clic su **Marketplace**, su **Calcolo**, quindi digitare **Oracle** nella casella di ricerca.

3.	Selezionare l'immagine **Oracle WebLogic Server 12c Standard Edition su Windows Server 2012** o **Oracle WebLogic Server 12c Enterprise Edition su Windows Server 2012**. Esaminare le informazioni su questa immagine (come le dimensioni minime consigliate), quindi fare clic su **Avanti**.

4.	Specificare un **nome host** per la macchina virtuale.

5.	Specificare un **nome utente** per la macchina virtuale. Questo utente può essere usato per l'accesso in remoto alla macchina virtuale e non è il nome utente del database Oracle.

6.	Specificare e confermare una password per la macchina virtuale o fornire una chiave pubblica SSH.

7.	Scegliere un **piano tariffario**. Per impostazione predefinita, vengono visualizzati i livelli di prezzo consigliati. Per visualizzare tutte le opzioni di configurazione, fare clic su **Visualizza tutto** in alto a destra.

8.	Impostare la configurazione facoltativa in base alle esigenze, con queste considerazioni:
	1. Lasciare **Account di archiviazione** invariato per creare un nuovo account di archiviazione con il nome della macchina virtuale.
	2. Lasciare **Set di disponibilità** come «Non configurato».
	3. Al momento non aggiungere alcun **endpoint**.

9.	Scegliere o creare un [gruppo di risorse](../resource-group-portal.md)

10. Scegliere una **sottoscrizione**

11. Selezionare un **Percorso**


##Configurazione di una macchina virtuale Oracle WebLogic Server 12c in Azure

1. Accedere al [Portale di Azure](https://ms.portal.azure.com/).

2.	Fare clic su **Macchine virtuali**.

3.	Fare clic sul nome della macchina virtuale a cui si desidera accedere.

4.	Fare clic su **Connect**.

5.	Rispondere ai prompt visualizzati per effettuare la connessione alla macchina virtuale. Quando vengono richiesti il nome e la password dell'amministratore, usare i valori forniti durante la creazione della macchina virtuale.

6.	Nella finestra di dialogo **Guida introduttiva alla piattaforma WebLogic** fare clic su **Introduzione a WebLogic Server**. Se la finestra di dialogo **Guida introduttiva alla piattaforma WebLogic** non viene aperta, aprirla facendo clic su **Start di Windows**, digitando **Avvia server di amministrazione per il dominio WebLogic Server**, quindi facendo clic sull'icona **Avvia server di amministrazione per il dominio WebLogic Server**.

7.	Nella finestra di dialogo di **Benvenuto** selezionare **Crea un nuovo dominio WebLogic** e quindi fare clic su **Avanti**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image10.png)

8.	Nella finestra di dialogo **Seleziona origine dominio** accettare i valori predefiniti e quindi fare clic su **Avanti**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image11.png)

9.	Nella finestra di dialogo **Seleziona nome e posizione dominio** accettare i valori predefiniti e quindi fare clic su **Avanti**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image12.png)

10.	Nella finestra di dialogo **Configura nome utente e password amministratore**:

	1.	[Facoltativo] Modificare il nome utente da **weblogic** a un valore di propria scelta.

	2.	Specificare e confermare una password per l'amministratore di WebLogic Server.

	3.	Fare clic su **Avanti**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image13.png)

11.	Nella finestra di dialogo **Configura modalità di avvio e JDK del server** selezionare **Modalità di produzione**, selezionare il JDK disponibile (o sfogliare per un JDK se necessario) e quindi fare clic su **Avanti**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image14.png)

12.	Nella finestra di dialogo **Seleziona configurazione facoltativa** non selezionare alcuna opzione, quindi fare clic su **Avanti**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image15.png)

13.	Nella finestra di dialogo **Riepilogo configurazione**, fare clic su **Crea**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image16.png)

14.	Nella finestra di dialogo **Crea dominio**, selezionare **Avvia server di amministrazione**, quindi fare clic su **Fine**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image17.png)

15.	Viene avviato un prompt dei comandi per **startWebLogic.cmd**. Quando richiesto, fornire il nome utente e la password WebLogic.

##Installazione di un'applicazione su una macchina virtuale Oracle WebLogic Server 12c in Azure
1.	Dopo aver effettuato l'accesso alla macchina virtuale, copiare l'esempio di shoppingcart.war disponibile all'indirizzo http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war in locale. Ad esempio, creare una cartella denominata **c:\\mywar** e salvare il file WAR in http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war su **c:\\mywar**.

2.	Aprile la **Console di amministrazione di WebLogic Server**, http://localhost:7001/console. Quando richiesto, fornire il nome utente e la password WebLogic.

3.	All'interno della **Console di amministrazione di WebLogic Server**, fare clic su **Blocca e modifica**, su **Distribuzioni**, quindi su **Installa**.

4.	Per **Percorso**, digitare **c:\\mywar\\shoppingcart.war**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image18.png)

	Fare clic su **Avanti**.

5.	Selezionare **Installa questa distribuzione come applicazione** e quindi fare clic su **Avanti**.

6.	Fare clic su **Fine**.

7.	All’interno della **Console di amministrazione di WebLogic Server**, fare clic su **Salva**, quindi su **Attiva modifiche**.

8.	Fare clic su **Distribuzioni**, selezionare **ShoppingCart**, fare clic su **Avvia**, quindi su **Gestisci tutte le richieste**. Quando viene richiesto di confermare, fare clic su **Sì**.

9.	Per visualizzare l'applicazione ShoppingCart in esecuzione in locale, aprire il browser all’indirizzo <http://localhost:7001/shoppingcart>

10.	Per creare un endpoint per la macchina virtuale:

	1. Accedere al [Portale di Azure](https://ms.portal.azure.com/).

	2.	Fare clic su **Sfoglia**

	3.	Fare clic su **Macchine virtuali**.

	4.	Selezionare la macchina virtuale.

	5.	Fare clic su **Impostazioni**.

	6.	Fare clic su **Endpoints**.

	7.	Fare clic su **Aggiungi**.

	8.	Specificare un nome per l'endpoint.

		1. Usare **TCP** per il protocollo

		2. Utilizzare **80** per la porta pubblica.

		3. Usare **7001** per la porta privata.

	9.	Lasciare le altre opzioni invariate.

	10. Fare clic su **OK**.

11.	Consentire una connessione in ingresso tramite il firewall alla porta 7001.

	1.	Accedere alla macchina virtuale.

	2.	Fare clic su **Start di Windows**, digitare **Windows Firewall con sicurezza avanzata**, quindi fare clic sull’icona di **Windows Firewall con sicurezza avanzata**. Viene aperta la console di gestione di **Windows Firewall con sicurezza avanzata**.

	3.	Nella console di gestione del firewall fare clic su **Regole connessioni in entrata** nel riquadro a sinistra (se non viene visualizzata l'opzione **Regole connessioni in entrata**, espandere il nodo principale nel riquadro a sinistra) e quindi fare clic su Nuova regola nel riquadro a destra.

	4.	Per **Tipo di regola**, selezionare **Porta** e fare clic su **Avanti**.

	5.	Per **Protocollo e porte**, selezionare **TCP**, quindi **Porte locali specifiche**, immettere **7001** per la porta e infine fare clic su **Avanti**.

	6.	Selezionare **Consenti la connessione** e fare clic su **Avanti**.

	7.	Accettare le impostazioni predefinite per i profili per i quali viene applicata la regola e fare clic su **Avanti**.

	8.	Specificare un nome per la regola e facoltativamente una descrizione, quindi fare clic su **Fine**.

12.	Per visualizzare l'applicazione ShoppingCart in esecuzione su Internet, aprire un browser all'URL nel formato `http://<<unique_domain_name>>/shoppingcart`. È possibile determinare il valore per <<*unique\_domain\_name*>> all'interno del [portale di Azure](https://ms.portal.azure.com/) facendo clic su **Macchine virtuali** e poi selezionando la macchina virtuale che si usa per eseguire Oracle WebLogic Server.


##Risorse aggiuntive
Dopo aver configurato la macchina virtuale che esegue Oracle WebLogic Server, vedere gli argomenti seguenti per altre informazioni.

-	[Immagini di macchine virtuali Oracle - Considerazioni varie](virtual-machines-windows-classic-oracle-considerations.md)

-	[Documentazione del prodotto Oracle WebLogic Server](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

-	[Oracle WebLogic Server 12c con Linux su Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

-	[Immagini di macchine virtuali Oracle per Azure](virtual-machines-linux-classic-oracle-images.md)

<!---HONumber=AcomDC_0323_2016-->