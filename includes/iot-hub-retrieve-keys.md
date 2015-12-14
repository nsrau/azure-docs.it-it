## Recuperare le chiavi dell’hub IoT

Visualizzare i codici di autenticazione per il nuovo hub IoT.

1. Aggiungere il metodo seguente a Program.cs:

    ```
    static void ShowIoTHubKeys(ResourceManagementClient client, string token)
    {
    
    }
    ```

2. Aggiungere il codice seguente al metodo **ShowIoTHubKeys** per stampare le chiavi di autenticazione nella console:

    ```
    client.HttpClient.DefaultRequestHeaders.Authorization = 
        new AuthenticationHeaderValue("Bearer", token);
    var httpsRepsonse = client.HttpClient.PostAsync(
        string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}/listKeys?api-version=2015-08-15-preview", 
        subscriptionId, rgName, iotHubName),
        null).Result;
    
    Console.WriteLine("Keys: {0}, 
        httpsRepsonse.Content.ReadAsStringAsync().Result);
    ```

<!---HONumber=AcomDC_1203_2015-->