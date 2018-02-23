## <a name="create-a-ruby-application"></a>Creare un'applicazione Ruby
Per istruzioni, vedere [Creare un'applicazione Ruby in Azure](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Configurare l'applicazione per l'uso del bus di servizio
Per usare il bus di servizio, scaricare e usare il pacchetto Ruby di Azure, che comprende un set di pratiche librerie che comunicano con i servizi REST di archiviazione.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizzare RubyGems per ottenere il pacchetto
1. Usare un'interfaccia della riga di comando, ad esempio **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix).
2. Digitare "gem install azure" nella finestra di comando per installare la gemma e le dipendenze.

### <a name="import-the-package"></a>Importare il pacchetto
Usando l'editor di testo preferito aggiungere quanto segue alla parte superiore del file Ruby dove si intende usare l'archiviazione:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Configurare una stringa di connessione per il bus di servizio
Usare il codice seguente per impostare i valori dello spazio dei nomi, il nome della chiave, la chiave, il firmatario e l'host:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Impostare il valore dello spazio dei nomi sul valore creato invece che sull'intero URL. Ad esempio, usare **"yourexamplenamespace"** e non "yourexamplenamespace.servicebus.windows.net".
