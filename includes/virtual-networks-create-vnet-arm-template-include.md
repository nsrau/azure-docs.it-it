## <a name="download-and-understand-the-arm-template"></a>Scaricare e comprendere il modello ARM
È possibile scaricare il modello ARM esistente per la creazione di una rete virtuale e di due subnet da GitHub, apportare le modifiche desiderate e riutilizzarlo. A tale scopo, seguire questa procedura:

1. Passare alla [pagina del modello di esempio](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Fare clic su **azuredeploy.json**, quindi fare clic su **RAW**.
3. Salvare il file in una cartella locale nel computer in uso.
4. Se si ha familiarità con i modelli ARM, procedere al passaggio 7.
5. Aprire il file appena salvato e visualizzare il contenuto in **parameters** nella riga 5. I parametri del modello ARM costituiscono un segnaposto per i valori che possono essere compilati durante la distribuzione.
   
   | Parametro | Descrizione |
   | --- | --- |
   | **Località** |Area di Azure in cui verrà creata la rete virtuale |
   | **vnetName** |Nome per la nuova rete virtuale |
   | **addressPrefix** |Spazio di indirizzi per la rete virtuale, nel formato CIDR |
   | **subnet1Name** |Nome per la prima rete virtuale |
   | **subnet1Prefix** |Blocco CIDR per la prima subnet |
   | **subnet2Name** |Nome per la seconda rete virtuale |
   | **subnet2Prefix** |Blocco CIDR per la seconda subnet |
   
   > [!IMPORTANT]
   > I modelli ARM conservati in github possono cambiare nel tempo. Assicurarsi di aver controllato il modello prima di utilizzarlo.
   > 
   > 
6. Controllare il contenuto in **resources** e prendere nota di quanto segue:
   
   * **type**. Tipo di risorsa che sarà creato dal modello. In questo caso, **Microsoft.Network/virtualNetworks**, che rappresenta una rete virtuale.
   * **name**. Nome della risorsa. Notare l'utilizzo di **[parameters('vnetName')]**con cui si indica che il nome verrà specificato come input dall'utente o tramite un file di parametri durante la distribuzione.
   * **properties**. Elenco di proprietà per la risorsa. Questo modello utilizza le proprietà relative allo spazio di indirizzi e alla subnet durante la creazione della rete virtuale.
7. Tornare alla [pagina del modello di esempio](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Fare clic su **azuredeploy-parameters.json** e quindi su **RAW**.
9. Salvare il file in una cartella locale nel computer in uso.
10. Aprire il file appena salvato e modificare i valori per i parametri. Utilizzare i valori riportati di seguito per la distribuzione della rete virtuale descritta in questo scenario.
    
        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }
11. Salvare il file.



<!--HONumber=Nov16_HO2-->


