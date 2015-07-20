<properties title="Creating an Oracle WebLogic Server 12c and Oracle Database 12c Virtual Machine in Azure" pageTitle="Creazione di una macchina virtuale Oracle WebLogic Server 12c e Oracle Database 12c in Azure" description="Seguire un esempio di creazione di un’immagine Oracle WebLogic Server 12c e Oracle Database 12c in esecuzione su Windows Server 2012 in Microsoft Azure." services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Creazione di una macchina virtuale Oracle WebLogic Server 11g in Azure
Nell'esempio seguente viene mostrato come creare una macchina virtuale basata su un'immagine Oracle WebLogic Server 11g fornita da Microsoft in esecuzione su Windows Server 2008 R2 in Azure.

##Creazione di una macchina virtuale Oracle WebLogic Server 11g in Azure

1. Accedere al [Portale di Azure](https://ms.portal.azure.com/).

2. Fare clic su **Marketplace**, su Calcolo, quindi digitare **Oracle** nella casella di ricerca.

3. Selezionare o esaminare le informazioni su questa immagine (come le dimensioni minime consigliate), quindi fare clic.

4. Specificare un **nome host** per la macchina virtuale.

5. Specificare un **nome utente** per la macchina virtuale. Questo utente può essere utilizzato per l’accesso in remoto alla macchina virtuale e non è il nome utente del database Oracle.

6. Specificare e confermare una password per la macchina virtuale o fornire una chiave pubblica SSH.

7. Per impostazione predefinita vengono visualizzati i livelli di prezzo consigliati. Per visualizzare tutte le opzioni di configurazione, fare clic su **Visualizza tutto** in alto a destra.

8. Impostare [Configurazione facoltativa](https://msdn.microsoft.com/library/azure/dn763935.aspx) in base alle esigenze, con queste considerazioni:

	1. Lasciare **Account di archiviazione** invariato per creare un nuovo account di archiviazione con il nome della macchina virtuale.

	2. Lasciare **Set di disponibilità** come «Non configurato».

	3. Al momento non aggiungere alcun **endpoint**.

9. Scegliere o creare un [gruppo di risorse](resource-group-portal.md)

10. Scegliere una **sottoscrizione**

11. Scegliere una **posizione**

##Configurazione di una macchina virtuale Oracle WebLogic Server 11g in Azure

1. Accedere al [Portale di Azure](https://ms.portal.azure.com/).

2. Fare clic su **Macchine virtuali**.

3. Fare clic sul nome della macchina virtuale a cui si desidera accedere.

4. Fare clic su **Connect**.

5. Rispondere ai prompt visualizzati per effettuare la connessione alla macchina virtuale. Quando vengono richiesti il nome e la password dell'amministratore, utilizzare i valori forniti durante la creazione della macchina virtuale.

6. Fare clic su **Start di Windows**, su **Tutti i programmi**, su **Oracle WebLogic**, su **WebLogic Server 11g R1**, su **Strumenti**, quindi su **Configurazione guidata**.

7. Nella finestra di dialogo di **benvenuto**, selezionare **Crea un nuovo dominio WebLogic**, quindi fare clic su **Avanti**.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image30.png)

8. Nella finestra di dialogo **Seleziona origine dominio**, accettare i valori predefiniti, quindi fare clic su **Avanti**.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image31.png)

9. Nella finestra di dialogo **Seleziona nome e posizione dominio**, accettare i valori predefiniti, quindi fare clic su **Avanti**.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image32.png)

10. Nella finestra di dialogo **Configura nome utente e password amministratore**:

	1. [Facoltativo] Modificare il nome utente da **weblogic** a un valore di propria scelta.

	2. Specificare e confermare una password per l'amministratore di WebLogic Server.

	3. Fare clic su **Next**.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image33.png)

11. Nella finestra di dialogo **Configura modalità di avvio e JDK del server**, selezionare **Modalità di produzione**, selezionare il JDK disponibile (o sfogliare per un JDK se necessario), quindi fare clic su **Avanti**.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image34.png)

12.	Nella finestra di dialogo **Seleziona configurazione facoltativa**, non selezionare alcuna opzione, quindi fare clic su **Avanti**.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image35.png)

13.	Nella finestra di dialogo **Riepilogo configurazione**, fare clic su **Crea**.
	
	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image35.png)

14.	Nella finestra di dialogo **Crea dominio**, selezionare **Avvia server di amministrazione**, quindi fare clic su **Fine**.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image37.png)

15.	Viene avviato un prompt dei comandi per **startWebLogic.cmd**. Quando richiesto, fornire il nome utente e la password WebLogic.

## Installazione di un’applicazione su una macchina virtuale Oracle WebLogic Server 11g in Azure

1. Dopo aver effettuato l'accesso alla macchina virtuale, copiare l'esempio shoppingcart.war disponibile all'indirizzo [http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war](http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war) in locale. Ad esempio, creare una cartella denominata **c:\\mywar** e salvare il file WAR disponibile all’indirizzo [http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war](http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war) in **c:\\mywar**.

2. Aprire la **Console di amministrazione di WebLogic Server**, [http://localhost:7001/console](http://localhost:7001/console). Quando richiesto, fornire il nome utente e la password WebLogic.

3. All'interno della **Console di amministrazione di WebLogic Server**, fare clic su **Blocca e modifica**, su **Distribuzioni**, quindi su **Installa**.

4. Per **Percorso**, digitare `c:\mywar\shoppingcart.war`.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image38.png)

	Fare clic su **Next**.

5. Selezionare **Installa questa distribuzione come applicazione**, quindi fare clic su **Avanti**.

6. Fare clic su **Finish**.

7. All’interno della **Console di amministrazione di WebLogic Server**, fare clic su **Attiva modifiche**.

8. Fare clic su **Distribuzioni**, selezionare **ShoppingCart**, fare clic su **Avvia**, quindi su **Gestisci tutte le richieste**. Quando viene richiesto di confermare, fare clic su **Sì**.

9. Per visualizzare l'applicazione ShoppingCart in esecuzione in locale, aprire il browser all’indirizzo [http://localhost:7001/shoppingcart](http://localhost:7001/shoppingcart)

10.  Consentire una connessione in ingresso tramite il firewall alla porta 7001.

	1. Dopo aver effettuato la connessione alla **macchina virtuale**, fare clic su **Start di Windows**, su **Pannello di controllo**, su **Sistema e sicurezza**, su **Windows Firewall**, quindi su **Impostazioni avanzate**. Viene aperta la console di gestione di **Windows Firewall con sicurezza avanzata**.

	2. All’interno della console di gestione del firewall, fare clic su **Regole connessioni in entrata** nel riquadro a sinistra (se non viene visualizzata l’opzione **Regole connessioni in entrata**, espandere il nodo principale nel riquadro a sinistra), quindi fare clic su **Nuova regola** nel riquadro a destra.

	3. Per** Tipo di regola**, selezionare **Porta** e fare clic su **Avanti**.

	4. Per **Protocollo e porte**, selezionare **TCP**, **Porte locali specifiche**, immettere **7001** per la porta, quindi fare clic su **Avanti**.

	5. Selezionare **Consenti la connessione** e fare clic su **Avanti**.

	6. Accettare le impostazioni predefinite per i profili per i quali viene applicata la regola e fare clic su **Avanti**.

	7. Specificare un nome per la regola e facoltativamente una descrizione, quindi fare clic su **Fine**.

11. Per creare un endpoint per la macchina virtuale:

	1. Accedere al [Portale di Azure](https://ms.portal.azure.com/).

    2. Fare clic su **Sfoglia**.

    3. Fare clic su **Macchine virtuali**.

    4. Selezionare la macchina virtuale.

	5. Fare clic su **Impostazioni**.

    6. Fare clic su **Endpoints**.

    7. Fare clic su **Aggiungi**.

    8. Specificare un nome per l'endpoint.

		1. Utilizzare **TCP** per il protocollo.

        2. Utilizzare **80** per la porta pubblica.

        3. Utilizzare **7001** per la porta privata.

    9. Lasciare le altre opzioni invariate.

	10. Fare clic su **OK**.

12. Per visualizzare l'applicazione ShoppingCart in esecuzione su Internet, aprire un browser all'URL nel formato `http://<<unique_domain_name>>/shoppingcart`. (È possibile determinare il valore per `<<unique_domain_name>>` all'interno del [portale di Azure](https://ms.portal.azure.com/) facendo clic su **Macchine virtuali**, quindi selezionando la macchina virtuale che si utilizza per eseguire Oracle WebLogic Server.)

## Risorse aggiuntive

Dopo aver configurato la macchina virtuale che esegue Oracle WebLogic Server, vedere gli argomenti seguenti per ulteriori informazioni.

- [Immagini di macchine virtuali Oracle - Considerazioni varie](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

- [Documentazione del prodotto Oracle WebLogic Server](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

- [Immagini di macchine virtuali Oracle per Azure](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

<!---HONumber=July15_HO2-->