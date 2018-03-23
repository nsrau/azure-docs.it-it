La tabella seguente mostra le associazioni supportate nelle due principali versioni del runtime di Funzioni di Azure.

| type | 1.x | 2.x | Trigger | Input | Output |  
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Archiviazione BLOB](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔<sup>1</sup>|✔|✔|✔|  
| [Cosmos DB](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|  
| [Griglia di eventi](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |  
| [Hub eventi](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|  
| [File esterno](../articles/azure-functions/functions-bindings-external-file.md)<sup>2</sup>    |✔|| |✔|✔|  
| [Tabella esterna](../articles/azure-functions/functions-bindings-external-table.md)<sup>2</sup>  |✔|| |✔|✔|  
| [HTTP](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔<sup>1</sup>|✔| |✔|
| [Microsoft Graph<br/>Tabelle di Excel](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔| |✔|✔|
| [Microsoft Graph<br/>File di OneDrive](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔| |✔|✔|
| [Microsoft Graph<br/>Indirizzo e-mail Outlook](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔| | |✔|
| [Microsoft Graph<br/>Eventi](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔|✔|✔|✔|
| [Microsoft Graph<br/>Token di autenticazione](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔| |✔| |
| [App per dispositivi mobili](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔|✔| |✔|✔|  
| [Hub di notifica di Azure](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Archiviazione code](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔<sup>1</sup>|✔| |✔|  
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [Bus di servizio](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|  
| [Archiviazione tabelle](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔<sup>1</sup>| |✔|✔|  
| [Timer](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|
| [Webhook](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔||✔| |✔|
  
<sup>1</sup> In 2.x tutte le associazioni tranne HTTP, Timer e Archiviazione di Azure devono essere registrate. Vedere [Registrare le estensioni delle associazioni](../articles/azure-functions/functions-triggers-bindings.md#register-binding-extensions).

<sup>2</sup> Sperimentale &mdash; Non supportato e potrebbe venire abbandonato in futuro.
