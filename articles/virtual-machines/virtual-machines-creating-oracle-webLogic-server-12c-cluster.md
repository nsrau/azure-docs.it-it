<properties 
	pageTitle="Creazione di un cluster Oracle WebLogic Server 12c in Azure" 
	description="Seguire un esempio di creazione di un cluster Oracle WebLogic Server 12c in Microsoft Azure." 
	services="virtual-machines" 
	authors="bbenz" 
	documentationCenter=""/>

<tags 
	ms.service="virtual-machines" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="infrastructure-services" 
	ms.date="06/22/2015" 
	ms.author="bbenz" />

#Creazione di un cluster Oracle WebLogic Server 12c in Azure
Nell'esempio seguente viene mostrato come creare un cluster Oracle WebLogic Server in Azure, basato su un'immagine Oracle WebLogic Server 12c fornita da Microsoft in esecuzione su Windows Server 2012.

Ciascuna istanza di un cluster WebLogic Server deve essere eseguita sulla stessa versione di Oracle WebLogic Server. In questo esempio viene utilizzato WebLogic Server 12c Enterprise Edition.

##Creazione di macchine virtuali da utilizzare nel cluster
Verranno create una macchina virtuale da utilizzare come server di amministrazione del cluster e altre macchine virtuali da usare come parte del cluster.

### Creazione di una macchina virtuale da utilizzare come server di amministrazione

Creare una macchina virtuale utilizzando l’immagine **Oracle WebLogic Server 12c Enterprise Edition su Windows Server 2012** disponibile in Azure. La procedura per creare questa macchina virtuale è reperibile in [Creazione di una macchina virtuale Oracle WebLogic Server 12c in Azure](#creating-an-oracle-weblogic-server-12c-virtual-machine-in-azure-new-article). Ai fini di questa esercitazione, denominare la macchina virtuale **MYVM1-ADMIN**. Prendere nota del nome della rete virtuale della macchina virtuale, poiché tale valore sarà utilizzato quando verranno create le macchine virtuali aggiuntive da aggiungere al cluster. (È possibile assegnare dei nomi qualsiasi alla macchina virtuale e alla rete virtuale, purché siano univoci in Azure.)

### Creazione di macchine virtuali gestite da utilizzare nel cluster

Creare macchine virtuali aggiuntive che verranno gestite dal server di amministrazione utilizzando l'immagine Oracle WebLogic Server 12c disponibile in Azure. Ai fini di questa esercitazione, denominare le macchine virtuali aggiuntive **MYVM2-MANAGED** e **MYVM3-MANAGED**. La procedura per creare queste macchine virtuali è reperibile in [Creazione di una macchina virtuale Oracle WebLogic Server 12c in Azure](#creating-an-oracle-weblogic-server-12c-virtual-machine-in-azure-new-article), *ad eccezione* della seguente modifica necessaria:

-  Quando vengono create le macchine virtuali, non creare una nuova rete virtuale. In particolare, nella finestra di dialogo **Configurazione facoltativa > Rete virtuale**, anziché il valore predefinito **Crea una nuova rete virtuale**, selezionare la rete virtuale creata per la macchina virtuale del server di amministrazione. Ad esempio, se durante la creazione del server di amministrazione è stata creata una rete virtuale denominata **ESEMPIO**, selezionare **ESEMPIO** quando viene creata la macchina virtuale del cluster gestito.

##Creazione di un dominio

1. Accedere al [Portale di Azure](https://ms.portal.azure.com/).

2. Fare clic su **Macchine virtuali**.

3. Fare clic sul nome della macchina virtuale creata come server di amministrazione del cluster (ad esempio, **MYVM1-ADMIN**).

4. Fare clic su **Connect**.

5. Rispondere ai prompt visualizzati per effettuare la connessione alla macchina virtuale. Quando vengono richiesti il nome e la password dell'amministratore, utilizzare i valori forniti durante la creazione della macchina virtuale.

6. All'interno di **Pagina 1** della finestra di dialogo **Configurazione guidata di Fusion Middleware**, fare clic su **Crea un nuovo dominio**, quindi su **Avanti**. (Se la finestra di dialogo **Configurazione guidata di Fusion Middleware** non viene aperta, aprirla facendo clic su **Start di Windows**, digitando **Configurazione guidata**, quindi facendo clic sull’icona di **Configurazione guidata**.)

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image19.png)

7. All'interno di **Pagina 2** della finestra di dialogo **Configurazione guidata di Fusion Middleware**, selezionare il modello **Dominio WebLogic Server di base**, quindi fare clic su **Avanti**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image20.png)

8. All'interno di **Pagina 3** della finestra di dialogo **Configurazione guidata di Fusion Middleware**:

	1. [Facoltativo] Modificare il nome utente da **weblogic** a un valore di propria scelta.
	
	2. Specificare e confermare una password per l'amministratore di WebLogic Server.
	
	3. Fare clic su **Next**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image21.png)

9. All'interno di **Pagina 4** della finestra di dialogo **Configurazione guidata di Fusion Middleware**, selezionare **Produzione** per la modalità di dominio, selezionare il JDK disponibile (o sfogliare per un JDK se necessario), quindi fare clic su **Avanti**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image22.png)

10.  All'interno di **Pagina 5** della finestra di dialogo **Configurazione guidata di Fusion Middleware**, non selezionare alcuna opzione, quindi fare clic su **Avanti**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image23.png)

11.  All'interno di **Pagina 6** della finestra di dialogo **Configurazione guidata di Fusion Middleware**, fare clic su **Crea**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image24.png)

12.  All'interno di **Pagina 7** della finestra di dialogo **Configurazione guidata di Fusion Middleware**, fare clic su **Avanti** al termine della creazione del dominio.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image25.png)

13.  All'interno di **Pagina 8** della finestra di dialogo **Configurazione guidata di Fusion Middleware**, selezionare Avvia server di amministrazione, quindi fare clic su **Fine**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image26.png)

14.  Viene avviato un prompt dei comandi per **startWebLogic.cmd**. Quando richiesto, fornire il nome utente e la password WebLogic.

##Configurazione del cluster

1. Dopo aver eseguito l'accesso alla macchina virtuale di amministrazione, eseguire la **Console di amministrazione di WebLogic Server**, <http://localhost:7001/console>. Quando richiesto, fornire il nome utente e la password WebLogic Server.

2. Nella **Console di amministrazione di WebLogic Server**, fare clic su **Blocca e modifica**.

3. Nel riquadro **Struttura di dominio**, espandere **Ambiente**, quindi fare clic su **Cluster**.

4. Nella finestra di dialogo **Riepilogo dei cluster**, fare clic su **Nuovo**, quindi su **Cluster**.

5. Nella finestra di dialogo **Proprietà cluster**:

	1. Immettere un nome per il cluster.

	2. Selezionare **Unicast** per la **modalità di messaggistica**.

		![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image001.png)

	
	3. Fare clic su **OK**.

6. Nel riquadro **Struttura di dominio**, espandere **Ambiente**, quindi fare clic su **Server**.

7. Aggiungere il primo server gestito al cluster.

	1. Fare clic su **New**.

	2. Nella finestra di dialogo **Crea un nuovo server**:

		1. Per **Nome server**, immettere il nome del primo server gestito. Ad esempio\*\*, MYVM2-MANAGED.\*\*

		2. Per **Indirizzo di ascolto server**, immettere nuovamente il nome.

		3. Per **Porta di ascolto**, digitare **7008**.

		4. Selezionare **Sì, configura questo server come membro di un cluster esistente.**

		5. Nell’elenco a discesa **Seleziona un cluster**, selezionare il cluster creato in precedenza.

			34d27e82-bb2e-4f9c-aaad-ca3e28c0f5fc

		6. Fare clic su **Next**.

		7. Fare clic su **Finish**.

8. Aggiungere il secondo server gestito al cluster utilizzando i passaggi precedenti. Per **Nome server** e **Indirizzo di ascolto server**, utilizzare il nome del secondo computer gestito. Per **Porta di ascolto**, utilizzare **7008**.

9. Sempre nella console di amministrazione di WebLogic Server, fare clic su **Attiva modifiche**.

10. Nella macchina virtuale di amministrazione, creare una variabile di ambiente denominata **SERVER\_HOME** con il valore impostato su **C:\\Oracle\\Middleware\\Oracle\_Home\\wlserver**. È possibile creare una variabile di ambiente utilizzando la procedura seguente:

	1. Fare clic su **Start di Windows**, digitare **Pannello di controllo**, fare clic sull’icona di **Pannello di controllo**, su **Sistema e sicurezza**, su **Sistema**, quindi su **Impostazioni di sistema avanzate**.

	2. Fare clic sulla scheda **Avanzate**, quindi su **Variabili d’ambiente**.

	3. Sotto la sezione **Variabili di sistema**, fare clic su **Nuova** per creare la variabile.

	4. Nella finestra di dialogo **Nuova variabile di sistema**, immettere **SERVER\_HOME** per il nome della variabile, quindi **C:\\Oracle\\Middleware\\Oracle\_Home\\wlserver** per il valore.

	5. Fare clic su **OK** per salvare la nuova variabile di ambiente e chiudere la finestra di dialogo **Nuova variabile di sistema**.

	6. Chiudere le altre finestre di dialogo che sono state aperte da Pannello di controllo.

11. Aprire un nuovo prompt dei comandi (in modo che la variabile di ambiente **SERVER\_HOME** sia attiva).

	>[AZURE.NOTE]Alcuni dei passaggi rimanenti richiedono l'utilizzo di un prompt dei comandi dopo che è stato effettuato l'accesso alle macchine virtuali. Per comprendere facilmente a quale macchina è stato effettuato l’accesso, dopo aver aperto il prompt dei comandi, eseguire **title %NOMECOMPUTER%**.
	>
	>**( %NOMECOMPUTER%** è una variabile di ambiente definita dal sistema che viene impostata automaticamente con il nome del computer. L’esecuzione del comando **title** **%NOMECOMPUTER%** farà in modo che nella barra del titolo del prompt dei comandi verrà visualizzato il nome del computer.)

12. Eseguire il comando seguente:

		%SERVER\_HOME%\\common\\bin\\pack.cmd -managed=true -domain=C:\\Oracle\\Middleware\\Oracle\_Home\\user\_projects\\domains\\base\_domain -template=c:\\mytestdomain.jar -template\_name="mytestdomain" 

	Con questo comando viene creato un file jar denominato **c:\\mytestdomain.jar**. Questo file jar verrà in seguito copiato sulle macchine virtuali gestite nel cluster.

13. Consentire una connessione in ingresso tramite il firewall alla porta 7001.

	1. Dopo aver effettuato l’accesso alla macchina virtuale, fare clic su **Start di Windows**, digitare **Windows Firewall con sicurezza avanzata**, quindi fare clic sull’icona di **Windows Firewall con sicurezza avanzata**. Viene aperta la console di gestione di **Windows Firewall con sicurezza avanzata**.

	2. All’interno della console di gestione del firewall, fare clic su **Regole connessioni in entrata** nel riquadro a sinistra (se non viene visualizzata l’opzione **Regole connessioni in entrata**, espandere il nodo principale nel riquadro a sinistra), quindi fare clic su **Nuova regola** nel riquadro a destra.

	3. Per **Tipo di regola**, selezionare **Porta** e fare clic su **Avanti**.

	4. Per **Protocollo e porte**, selezionare **TCP**, **Porte locali specifiche**, immettere **7001** per la porta, quindi fare clic su **Avanti**.

	5. Selezionare **Consenti la connessione** e fare clic su **Avanti**.

	6. Accettare le impostazioni predefinite per i profili per i quali viene applicata la regola e fare clic su **Avanti**.

	7. Specificare un nome per la regola e facoltativamente una descrizione, quindi fare clic su **Fine**.

14. Per ciascuna delle macchine virtuali gestite:

	1. Accedere alla macchina virtuale.

	2. Creare una variabile di ambiente denominata **SERVER\_HOME** con il valore impostato su **C:\\Oracle\\Middleware\\Oracle\_Home\\wlserver**.

	3. Copiare c:\\mytestdomain.jar dalla macchina virtuale di amministrazione a c:\\mytestdomain.jar sulla macchina virtuale gestita.

	4. Aprire un prompt dei comandi (e ricordare di eseguire **title %NOMECOMPUTER%** nel prompt dei comandi, in modo da determinare a quale computer viene effettuato l’accesso).

	5. Eseguire il comando seguente:

			%SERVER\_HOME%\\common\\bin\\unpack.cmd -domain=C:\\Oracle\\Middleware\\Oracle\_Home\\user\_projects\\domains\\base\_domain -template=c:\\mytestdomain.jar

	6. Modificare la directory corrente del prompt dei comandi in **C:\\Oracle\\Middleware\\Oracle\_Home\\user\_projects\\domains\\base\_domain\\bin**.

	7. Eseguire start<<\*NOMEMACCHINA\*>>.cmd, dove <<\*NOMEMACCHINA\*>> è il nome del computer gestito. Ad esempio, **startMYVM2-MANAGED**.

	8. Quando richiesto, fornire il nome utente e la password WebLogic Server.

	9. Consentire una connessione in ingresso tramite il firewall alla porta 7008. (Seguire i passaggi utilizzati per aprire la porta 7001 sul server di amministrazione, ma usare 7008 per i server gestiti.)

15. Sulla macchina virtuale di amministrazione, aprire la **Console di amministrazione di WebLogic Server**, <http://localhost:7001/console>, e visualizzare i server in esecuzione.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image003.png)

16. Creare un endpoint con carico bilanciato impostato per le macchine virtuali gestite:

	1. All'interno del [portale di Azure](https://ms.portal.azure.com/), nella sezione **Macchine virtuali **, selezionare la prima macchina virtuale gestita (come **MYVM2-MANAGED)**. 
	2. Fare clic su **Impostazioni**, su **Endpoint**, quindi su **Aggiungi**.

	3. Specificare un nome per l'endpoint, **TCP** per il protocollo, la porta pubblica **80** e la porta privata **7008**. Lasciare le altre opzioni invariate.

	4. Selezionare **Crea un set con carico bilanciato**, quindi fare clic su **Operazione completata**.

	5. Specificare un nome per il set con carico bilanciato, accettare le impostazioni predefinite per gli altri parametri, quindi fare clic su **Operazione completata. \* \*

17. Per creare un endpoint per la macchina virtuale:

	1. Accedere al [Portale di Azure](https://ms.portal.azure.com/).

	2. Fare clic su **Sfoglia**.

	3. Fare clic su **Macchine virtuali**.

	4. Selezionare la macchina virtuale.

	5. Fare clic su **Impostazioni**.

	6. Fare clic su **Set con carico bilanciato**.

	7. Fare clic su **Accedi**.

	8. Impostare il tipo di set con carico bilanciato come **Interno**.

	9. Specificare un nome per l'endpoint.

		1. Utilizzare **TCP** per il protocollo.

		2. Utilizzare **80** per la porta pubblica.

		3. Utilizzare **7008** per la porta probe.

	10. Lasciare le altre opzioni invariate.

	11. Fare clic su **OK**.

	12. Attendere che la macchina virtuale effettui l’accesso al set con carico bilanciato prima di procedere con il passaggio successivo.

18. All'interno del [portale di Azure](https://ms.portal.azure.com/), nella sezione **Macchine virtuali**, selezionare la seconda macchina virtuale gestita (come **MYVM3-MANAGED**). Seguire i passaggi riportati in precedenza per effettuare l’accesso al set con carico bilanciato creato per la prima macchina virtuale gestita.

##Distribuzione di un'applicazione nel cluster

A questo punto, è possibile distribuire l'applicazione utilizzando la procedura seguente. Si supponga che si distribuisce l'applicazione ShoppingCart di Oracle, disponibile per il download all'indirizzo <http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war>.

1. Accedere alla macchina virtuale che funge da amministratore per il cluster di WebLogic Server (ad esempio, **MYVM1-ADMIN**). 

2. Copiare il file shoppingcart.war in locale. Ad esempio, creare una cartella denominata **c:\\mywar** e salvare il file WAR in <http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war> su **c:\\mywar**.

3. Aprile la **Console di amministrazione di WebLogic Server**, <http://localhost:7001/console>. Quando richiesto, fornire il nome utente e la password WebLogic.

4. All'interno della **Console di amministrazione di WebLogic Server**, fare clic su **Blocca e modifica**, su **Distribuzioni**, quindi su **Installa**.

5. Per **Percorso**, digitare **c:\\myway\\shoppingcart.war**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image004.png)

	Fare clic su **Next**.

6. Selezionare **Installa questa distribuzione come applicazione**, quindi fare clic su **Avanti**.

7. Fare clic su **Finish**.

8. Per **Destinazioni disponibili**, selezionare il cluster creato in precedenza, quindi assicurarsi che l’opzione **Tutti i server nel cluster** sia selezionata, quindi fare clic su **Avanti**.

9. Sotto **Accessibilità origine**, selezionare **Copia questa applicazione su tutte le destinazione per me**, quindi fare clic su **Fine**.

10.  All’interno della **Console di amministrazione di WebLogic Server**, fare clic su **Salva**, quindi su **Attiva modifiche**.

11.  Fare clic su **Distribuzioni**, selezionare **ShoppingCart**, fare clic su **Avvia**, quindi su **Gestisci tutte le richieste**. Quando viene richiesto di confermare, fare clic su **Sì**.

12.  Per visualizzare l'applicazione ShoppingCart in esecuzione su Internet, aprire un browser all'URL nel formato `http://<<unique_domain_name>>/shoppingcart`. (È possibile determinare il valore per `<<unique_domain_name>>` all'interno del [portale di Azure](https://ms.portal.azure.com/) facendo clic su Macchine virtuali, quindi selezionando la macchina virtuale che si utilizza per eseguire Oracle WebLogic Server.)

## Passaggi successivi

Per assicurarsi ulteriormente che il cluster funzioni come previsto, è possibile modificare il progetto shoppingcart.war per visualizzare il nome della macchina che gestisce la sessione del browser, avviare una sessione del browser, arrestare la macchina che ha gestito la sessione del browser e aggiornare quest’ultima per scoprire se un’altra macchina continua a gestirla.

Ad esempio:

1. Modificare il file **DWRHeader1.jspf** in modo che contenga il codice seguente all'inizio:

		<table>
		
		<tr><td><CENTER><b><h3><% out.println("Your request is served from " + System.getenv("computername") ); %></h3></b></CENTER></td></tr>
		
		</table>             

2. Modificare il file **weblogic.xm** in modo che contenga il codice seguente dopo la riga di commento `Insert session descriptor element here`.

		<session-descriptor>
			<persistent-store-type>replicated_if_clustered</persistent-store-type>
		</session-descriptor>


3. Ricompilare e ridistribuire il file shoppingcart.war aggiornato.

4. Aprire una sessione del browser ed eseguire l'applicazione ShoppingCart. Aggiungere alcuni elementi al carrello e osservare quale macchina sta gestendo la sessione del browser.

5. All'interno del portale di Azure, nell’interfaccia utente di **Macchine virtuali**, selezionare la macchina virtuale che ha gestito la sessione del browser e fare clic su **Arresta**. Attendere che lo stato della macchina virtuale corrisponda a **Arrestato (deallocato)** prima di procedere.

6. Aggiornare la sessione del browser che esegue l'applicazione ShoppingCart e verificare se un’altra macchina sta gestendo la sessione del browser.

7. Fare clic sul collegamento del carrello e verificare se gli elementi aggiunti in precedenza sono ancora presenti nel carrello.

## Risorse aggiuntive

Dopo aver configurato il cluster che esegue Oracle WebLogic Server, vedere gli argomenti seguenti per ulteriori informazioni.

- [Immagini di macchine virtuali Oracle - Considerazioni varie](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

- [Documentazione del prodotto Oracle WebLogic Server](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

- [Oracle WebLogic Server 12c con Linux su Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

<!---HONumber=July15_HO5-->