## <a name="create-a-self-hosted-ir"></a>Creare un runtime di integrazione self-hosted

In questa sezione si crea un runtime di integrazione self-hosted e lo si associa a un computer locale con il database di SQL Server. Il runtime di integrazione self-hosted è il componente che copia i dati da SQL Server sul computer all'archivio BLOB di Azure. 

1. Creare una variabile per il nome del runtime di integrazione. Usare un nome univoco e annotarlo. perché sarà usato più avanti in questa esercitazione. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
1. Creare un runtime di integrazione self-hosted. 

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
 

2. Eseguire questo comando per recuperare lo stato del runtime di integrazione creato. Verificare che il valore della proprietà **State** sia impostato su **NeedRegistration**. 

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

3. Eseguire questo comando per recuperare le **chiavi di autenticazione** per la registrazione del runtime di integrazione self-hosted nel servizio Data Factory nel cloud. 

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
4. Copiare una delle chiavi (escludere le virgolette doppie) per la registrazione del runtime di integrazione self-hosted che si installerà nel computer nel passaggio successivo.  

## <a name="install-integration-runtime"></a>Installare il runtime di integrazione
1. Se **Microsoft Integration Runtime** è già installato nel computer, disinstallarlo tramite **Installazione applicazioni**. 
2. [Scaricare](https://www.microsoft.com/download/details.aspx?id=39717) il runtime di integrazione self-hosted in un computer Windows locale, quindi eseguire l'installazione. 
3. Nella pagina iniziale dell'**installazione guidata di Microsoft Integration Runtime** fare clic su **Avanti**.  
4. Nella pagina **Contratto di licenza con l'utente finale** accettare le condizioni e fare clic su **Avanti**. 
5. Nella pagina **Cartella di destinazione** fare clic su **Avanti**. 
6. Nella pagina **Pronto per l'installazione** fare clic su **Installa**. 
7. Se viene visualizzato un messaggio di avviso relativo al computer configurato per passare alla modalità di sospensione o di ibernazione quando non è in uso, fare clic su **OK**. 
8. Se viene visualizzata la finestra **Opzioni risparmio energia**, chiuderla e passare alla finestra di configurazione. 
9. Nella pagina **Installazione di Microsoft Integration Runtime completata** fare clic su **Fine**.
10. Nella pagina **Registra Runtime di integrazione (self-hosted)** incollare la chiave salvata nella sezione precedente e fare clic su **Registra**. 

   ![Registrare il runtime di integrazione](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)
2. Al termine della registrazione del runtime di integrazione self-hosted viene visualizzato il messaggio seguente:

   ![Registrazione completata](media/data-factory-create-install-integration-runtime/registered-successfully.png)

3. Nella pagina **Nuovo nodo di Runtime di integrazione (self-hosted)** fare clic su **Avanti**. 

    ![Pagina Nuovo nodo di Runtime di integrazione](media/data-factory-create-install-integration-runtime/new-integration-runtime-node-page.png)
4. Nella pagina **Canale di comunicazione Intranet** fare clic su **Ignora**. È possibile selezionare un certificato TLS/SSL per proteggere la comunicazione tra nodi in un ambiente di runtime di integrazione di più nodi. 

    ![Pagina Canale di comunicazione Intranet](media/data-factory-create-install-integration-runtime/intranet-communication-channel-page.png)
5. Nella pagina **Registra Runtime di integrazione (self-hosted)** fare clic su **Avvia Configuration Manager**. 
6. Quando il nodo viene connesso al servizio cloud, viene visualizzata la pagina seguente:

   ![Connessione del nodo](media/data-factory-create-install-integration-runtime/node-is-connected.png)
7. Testare ora la connettività al database di SQL Server.

    ![Scheda Diagnostica](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    - Nella finestra **Gestione configurazione** passare alla scheda **Diagnostica**.
    - Selezionare **SqlServer** in **Tipo di origine dati**.
    - Immettere il nome del **server**.
    - Immettere il nome del **database**. 
    - Selezionare la modalità di **autenticazione**. 
    - Immettere il nome **utente**. 
    - Immettere la **password** per il nome utente.
    - Fare clic su **Test** per verificare che il runtime di integrazione possa connettersi a SQL Server. Se la connessione ha esito positivo, viene visualizzato un segno di spunta verde. In caso contrario, viene visualizzato un messaggio associato all'errore. Risolvere eventuali problemi e assicurarsi che il runtime di integrazione possa connettesi a SQL Server.    

    > [!NOTE]
    > Annotare questi valori, ovvero tipo di autenticazione, server, database, utente, password, perché saranno usati più avanti in questa esercitazione. 
    
