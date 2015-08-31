1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto (non sulla soluzione), quindi scegliere **Pubblica**. 

	![Opzione di menu per la pubblicazione del progetto](./media/app-service-api-pub-web-create/20-publish-gesture-v3.png)

2. Scegliere la scheda **Profilo** e fare clic su **App per le API di Microsoft Azure (anteprima)**.

	![Finestra di dialogo Pubblica sul Web](./media/app-service-api-pub-web-create/21-select-api-apps-for-deployment-v2.png)

3. Fare clic su **Nuovo** per effettuare il provisioning di una nuova app per le API nella sottoscrizione di Azure.

	![Finestra di dialogo per la selezione di servizi API esistenti](./media/app-service-api-pub-web-create/23-publish-to-apiapps-v3.png)

4. Nella finestra di dialogo **Crea un'app per le API** immettere quanto segue:

	- Per **API App Name**, immettere il nome che si sta utilizzando per questa esercitazione. 
	- Se si dispone di più sottoscrizioni di Azure, selezionare quella da usare.
	- In **Piano di servizio app** selezionare uno dei piani di servizio app esistenti oppure selezionare **Crea nuovo piano di servizio app** e immettere il nome di un nuovo piano. 
	- In **Gruppo di risorse** selezionare i gruppi di risorse esistenti oppure selezionare **Crea nuovo gruppo di risorse** e immettere un nome. 
	- In **Livello di accesso** selezionare **Disponibile per chiunque**. È possibile in un secondo momento limitare l'accesso tramite il portale di anteprima di Azure.
	- In **Area** selezionare un'area nelle vicinanze.  

	![Finestra di dialogo per la configurazione di app Web di Microsoft Azure](./media/app-service-api-pub-web-create/24-new-api-app-dialog-v3.png)

5. Fare clic su **OK** per creare l'app per le API nella sottoscrizione.

	Poiché questo processo può richiedere alcuni minuti, Visual Studio visualizza una finestra di conferma.

6. Fare clic su **OK** nella finestra di conferma.
 
	Il processo di provisioning crea il gruppo di risorse e l'app per le API nella sottoscrizione di Azure. Visual Studio mostra l'avanzamento nella finestra **Attività del servizio app di Azure**.

	![Notifica sullo stato tramite la finestra Attività del servizio app di Azure](./media/app-service-api-pub-web-create/26-provisioning-success-v3.png)

<!---HONumber=August15_HO8-->