## <a name="create-a-self-hosted-integration-runtime"></a>Creare un runtime di integrazione self-hosted

In questa sezione si crea un runtime di integrazione self-hosted e lo si associa a un computer locale con il database di SQL Server. Il runtime di integrazione self-hosted è il componente che copia i dati dall'istanza di SQL Server nel computer all'archivio BLOB di Azure. 

1. Creare una variabile per il nome del runtime di integrazione. Usare un nome univoco e prenderne nota. perché sarà usato più avanti in questa esercitazione. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
2. Creare un runtime di integrazione self-hosted. 

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Di seguito è riportato l'output di esempio:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 
3. Eseguire questo comando per recuperare lo stato del runtime di integrazione creato. Verificare che il valore della proprietà **State** sia impostato su **NeedRegistration**. 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Di seguito è riportato l'output di esempio:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

4. Eseguire questo comando per recuperare le chiavi di autenticazione per la registrazione del runtime di integrazione self-hosted nel servizio Azure Data Factory nel cloud: 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Di seguito è riportato l'output di esempio:

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    

5. Copiare una delle chiavi (escluse le virgolette) usate per la registrazione del runtime di integrazione self-hosted che si installerà nel computer nei passaggi successivi.  

## <a name="install-the-integration-runtime"></a>Installare il runtime di integrazione
1. Se il runtime di integrazione è già installato nel computer, disinstallarlo tramite **Installazione applicazioni**. 

2. [Scaricare](https://www.microsoft.com/download/details.aspx?id=39717) il runtime di integrazione self-hosted in un computer Windows locale. Eseguire l'installazione.

3. Nella pagina dell'**installazione guidata di Microsoft Integration Runtime** fare clic su **Avanti**.

4. Nella pagina **Contratto di licenza con l'utente finale** accettare le condizioni e fare clic su **Avanti**.

5. Nella pagina **Cartella di destinazione** fare clic su **Avanti**.

6. Nella pagina **Pronto per l'installazione** fare clic su **Installa**.

7. Se viene visualizzato un messaggio di avviso relativo alla configurazione del computer per passare alla modalità di sospensione o di ibernazione quando non è in uso, fare clic su **OK**.

8. Se viene visualizzata la pagina **Opzioni risparmio energia**, chiuderla e passare alla pagina di configurazione.

9. Nella pagina **Installazione di Microsoft Integration Runtime completata** fare clic su **Fine**.

10. Nella pagina **Registra Runtime di integrazione (self-hosted)** incollare la chiave salvata nella sezione precedente e fare clic su **Registra**. 

    ![Registrare il runtime di integrazione](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)

11. Al termine della registrazione del runtime di integrazione self-hosted viene visualizzato il messaggio seguente:

    ![Registrazione completata](media/data-factory-create-install-integration-runtime/registered-successfully.png)

12. Nella pagina **Nuovo nodo di Runtime di integrazione (self-hosted)** fare clic su **Avanti**. 

    ![Pagina Nuovo nodo di Runtime di integrazione](media/data-factory-create-install-integration-runtime/new-integration-runtime-node-page.png)

13. Nella pagina **Canale di comunicazione Intranet** fare clic su **Ignora**. Selezionare un certificato TLS/SSL per proteggere la comunicazione tra nodi in un ambiente di runtime di integrazione di più nodi. 

    ![Pagina Canale di comunicazione Intranet](media/data-factory-create-install-integration-runtime/intranet-communication-channel-page.png)

14. Nella pagina **Registra Runtime di integrazione (self-hosted)** fare clic su **Avvia Configuration Manager**.

15. Quando il nodo viene connesso al servizio cloud, viene visualizzata la pagina seguente:

    ![Pagina di connessione del nodo](media/data-factory-create-install-integration-runtime/node-is-connected.png)

16. Testare ora la connettività al database di SQL Server.

    ![Scheda Diagnostica](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    a. Nella pagina **Configuration Manager** passare alla scheda **Diagnostica**.

    b. Selezionare **SqlServer** per il tipo di origine dati.

    c. Immettere il nome del server.

    d. Immettere il nome del database.

    e. Selezionare la modalità di autenticazione.

    f. Immettere il nome utente.

    g. Immettere il nome utente e la password

    h. Selezionare **Test** per verificare che il runtime di integrazione possa connettersi a SQL Server. Se la connessione ha esito positivo, viene visualizzato un segno di spunta verde. Se la connessione non ha esito positivo, viene visualizzato un messaggio di errore. Risolvere eventuali problemi e assicurarsi che il runtime di integrazione possa connettesi a SQL Server.    

    > [!NOTE]
    > Prendere nota di questi valori per tipo di autenticazione, server, database, utente e password, perché saranno usati più avanti in questa esercitazione. 
    
